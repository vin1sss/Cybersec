---
title: Usuários, chaves e segurança no macOS
tags:
  - forense
  - macos
  - usuarios
  - seguranca
  - filevault
status: concluido
---

# Usuários, chaves e segurança no macOS

Para análise forense, identificar corretamente quais usuários existem no sistema e quais deles têm privilégios é tão importante quanto achar os artefatos. No macOS isso passa por olhar a pasta `/Users/`, mas também por observar se o disco está criptografado (FileVault), se há chaves SSH do usuário e se o usuário tem permissão de administrador.

---

## 1. Objetivo

- Mapear todos os usuários locais do macOS.
- Identificar quais usuários são relevantes para a investigação.
- Verificar presença de criptografia (FileVault).
- Localizar chaves e credenciais que possam ter sido usadas.
- Registrar privilégios e possibilidade de escalonamento.

---

## 2. Identificando usuários

O ponto de partida é sempre `/Users/`:

- `/Users/<usuario>/` - home de cada usuário real.
- `/Users/Shared/` - pasta compartilhada.
- `/Users/Guest/` ou similares podem existir dependendo da configuração.

Em aquisição/imagem:
- listar todas as pastas dentro de `/Users`;
- comparar datas de criação/modificação das homes com a data do incidente;
- priorizar usuários que tenham atividade recente.

Anotar no laudo algo como:
- usuários encontrados;
- qual deles parece ser o "principal" da máquina;
- quais foram analisados.

---

## 3. Estrutura da home do usuário

Cada home de usuário tem a estrutura padrão:

- `Desktop`
- `Documents`
- `Downloads`
- `Library` - principal para forense
- `Pictures`, `Movies`, etc.

Para segurança e chaves, olhar principalmente:

- `~/Library/`
- `~/.ssh/` (se existir)

---

## 4. Verificando privilégios (admin)

No macOS, usuários podem pertencer ao grupo de administradores. Em máquina viva, isso é fácil de ver. Em imagem, você pode inferir por:

- presença de apps instalados só para aquele usuário;
- arquivos de configuração em `/etc/` modificados em horários compatíveis com o uso desse usuário;
- itens de persistência em nível de sistema criados na mesma época (o que sugere que alguém com privilégio fez isso).

Se estiver documentando para alunos, basta escrever que "o usuário X aparenta ser o principal/administrador" e justificar com a atividade na home dele.

---

## 5. FileVault e criptografia

O macOS pode usar o FileVault para criptografar o disco. Para forense isso significa:

- se o volume estava montado e descriptografado no momento da coleta, você consegue acessar tudo normalmente;
- se o volume está criptografado e você não tem a chave/senha, o acesso será limitado.

É importante registrar no relatório:
- se o volume APFS estava criptografado;
- se foi necessário descriptografar com credenciais;
- se alguma área ficou inacessível.

Isso impacta diretamente a conclusão: "não foi possível analisar diretório X devido à criptografia".

---

## 6. Chaves SSH e acesso remoto

Assim como em Linux, usuários de macOS podem ter chaves SSH em:

- `~/.ssh/id_rsa`
- `~/.ssh/id_ed25519`
- `~/.ssh/authorized_keys`
- `~/.ssh/known_hosts`

Em investigação, isso responde perguntas como:
- este usuário acessava outros servidores?
- esta máquina aceitava acesso remoto de outro host?

Se você encontrar um malware/persistência apontando para um script que abre túnel ou usa SSH, vale olhar essa pasta.

---

## 7. Keychains (chaves e senhas do sistema)

O macOS usa o Keychain para guardar senhas, certificados e chaves de forma segura. Em análise forense offline, extrair conteúdo de Keychain pode exigir ferramentas e, em alguns casos, a própria senha do usuário.

O importante para a nota é registrar:
- que o macOS usa Keychain para armazenar segredos;
- que parte do conteúdo pode estar protegida;
- que a existência de Keychain não significa que as senhas estejam em claro.

Assim você mostra ao aluno/leitora do vault que há um ponto de segurança ali, mas que não é tão trivial quanto pegar um arquivo de texto.

---

## 8. Itens de segurança ligados ao usuário

Ao analisar a pasta do usuário, verificar também:

- aplicativos de segurança instalados só para aquele usuário (em `~/Applications`);
- scripts de login do usuário;
- itens de persistência no próprio usuário: `~/Library/LaunchAgents/`;
- arquivos de configuração de VPN ou de e-mail em `~/Library/Application Support/` e `~/Library/Preferences/`.

Tudo isso pode conter credenciais, endereços de servidores e outros dados sensíveis.

---

## 9. Checklist rápido

1. Listar todas as pastas em `/Users/`.
2. Identificar o(s) usuário(s) principal(is) pelo volume de arquivos.
3. Coletar `~/Library/` de cada usuário relevante.
4. Verificar se existe `~/.ssh/` e copiar o conteúdo.
5. Registrar se o volume está criptografado (FileVault).
6. Verificar `~/Library/LaunchAgents/` para persistências por usuário.
7. Procurar por arquivos de configuração sensíveis (Mail, VPN, browsers).
8. Anotar quais usuários têm atividade recente na época do incidente.

---

## 10. Conclusão

Mapear usuários no macOS não é só ver a pasta `/Users/`: é entender qual deles realmente usou a máquina, se tinha privilégios e se o disco estava protegido por FileVault. Quando você junta isso com as notas de persistência e de logs, consegue dizer "foi o usuário X, nesta data, nesta máquina" com bem mais confiança.