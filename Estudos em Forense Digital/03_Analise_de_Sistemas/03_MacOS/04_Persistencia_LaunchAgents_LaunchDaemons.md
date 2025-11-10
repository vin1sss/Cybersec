---
title: Persistência em macOS - LaunchAgents e LaunchDaemons
tags:
  - forense
  - macos
  - persistencia
  - launchd
  - analise-de-sistemas
status: concluido
---

# Persistência em macOS - LaunchAgents e LaunchDaemons

No macOS, a forma mais comum e "nativa" de fazer algo iniciar automaticamente não é colocar no "Iniciar" como no Windows, mas registrar um item para o `launchd`. Esse registro é feito por meio de arquivos `.plist` colocados em diretórios específicos. Por isso, em forense macOS, sempre que você suspeitar de persistência, precisa olhar essas pastas e ler esses `.plist`.

---

## 1. Objetivo

- Identificar todos os diretórios de persistência baseados em launchd.
- Explicar a diferença entre LaunchAgents e LaunchDaemons.
- Mostrar quais campos do `.plist` são importantes.
- Montar um procedimento de análise e validação.

---

## 2. launchd e o papel dos .plist

O `launchd` é o gerenciador de serviços do macOS. Ele lê arquivos de configuração no formato `.plist` (Property List) que dizem o que carregar, quando carregar e com quais argumentos.

Características importantes para forense:

- o `.plist` aponta para o binário/script real;
- o nome do `.plist` pode ser enganoso (ex.: `com.apple.update.plist` apontando para `/Users/.../evil`);
- a data de criação/modificação do `.plist` ajuda a montar a linha do tempo.

---

## 3. Locais de persistência

Existem três caminhos que você deve sempre verificar:

1. **Persistência para todos os usuários (nível de sistema)**  
   - `/Library/LaunchAgents/`
   - `/Library/LaunchDaemons/`

2. **Persistência para um usuário específico**  
   - `~/Library/LaunchAgents/`

3. **Persistência de sistema/Apple**  
   - `/System/Library/LaunchAgents/`
   - `/System/Library/LaunchDaemons/`
   - normalmente são itens legítimos do sistema, mas vale comparar por data.

Em laboratório/CTF o mais comum é o atacante criar algo em:
- `~/Library/LaunchAgents/`
- ou `/Library/LaunchAgents/`

porque não precisa mexer tanto em permissões.

---

## 4. LaunchAgents x LaunchDaemons

- **LaunchAgents**
  - rodam no contexto de um usuário logado;
  - podem interagir com a sessão gráfica;
  - caminhos:
    - `/Library/LaunchAgents/`
    - `~/Library/LaunchAgents/`

- **LaunchDaemons**
  - rodam em nível de sistema;
  - iniciam antes do login do usuário;
  - precisam de mais privilégios para serem criados;
  - caminho:
    - `/Library/LaunchDaemons/`

Para forense isso significa: se o atacante queria garantir execução mesmo sem login, ele tende a usar Daemon. Se queria algo mais discreto, preso ao usuário, Agent.

---

## 5. Estrutura básica de um .plist de launchd

Um `.plist` típico tem chaves como:

- `Label` - identificador do serviço.
- `Program` ou `ProgramArguments` - o que será executado.
- `RunAtLoad` - se deve rodar ao carregar.
- `KeepAlive` - se deve ser mantido vivo.
- `StartInterval` ou `StartCalendarInterval` - execução agendada.

Exemplo ilustrativo (não é do sistema):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>Label</key>
    <string>com.user.backupagent</string>
    <key>ProgramArguments</key>
    <array>
      <string>/Users/alvo/.local/bin/.hidden</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
    <key>KeepAlive</key>
    <true/>
  </dict>
</plist>
````

Para análise:

* se o caminho do `ProgramArguments` for fora de `/Applications` ou de `/usr/...`, atenção;
* se estiver apontando para dentro de `Downloads`, `Documents`, pasta oculta ou tmp, quase certo que é malicioso.

---

## 6. Indicadores de suspeita

Marque como suspeito quando:

* nome do `.plist` tenta imitar Apple: `com.apple.update.helper.plist`;
* data de criação/modificação não bate com a instalação do macOS ou com as atualizações;
* aponta para binário em pasta de usuário;
* aponta para script `.sh`, `.py` ou binário sem extensão em pasta oculta;
* tem `KeepAlive` e `RunAtLoad` ao mesmo tempo, garantindo que o processo permaneça;
* possui `StartInterval` muito curto (ex.: 30 segundos).

Esses pontos são úteis para ensinar alunos a diferenciar item legítimo de item injetado.

---

## 7. Procedimento de análise

1. Listar todos os arquivos em:

   * `/Library/LaunchAgents/`
   * `/Library/LaunchDaemons/`
   * `~/Library/LaunchAgents/`
2. Ordenar por data de modificação/criação.
3. Para cada `.plist`, anotar:

   * nome do arquivo;
   * caminho do `Program` ou `ProgramArguments`;
   * flags (`RunAtLoad`, `KeepAlive`, `StartInterval`);
   * proprietário/permissões (se estiver disponível).
4. Localizar no disco o binário/script apontado.
5. Se o binário não existir mais, procurar erros nos logs (ver nota de logs) para comprovar a tentativa de execução.
6. Registrar tudo no laudo.

---

## 8. Correlação com logs

Depois de identificar um `.plist` suspeito, volte na nota de logs e procure:

* mensagens de erro dizendo que não foi possível carregar o LaunchAgent/Daemon;
* mensagens com o label do serviço;
* tentativas repetidas de execução (quando `KeepAlive` está ativo).

Isso ajuda a demonstrar persistência mesmo que o arquivo já tenha sido removido.

---

## 9. Checklist rápido

* [ ] Verificar `/Library/LaunchAgents/`
* [ ] Verificar `/Library/LaunchDaemons/`
* [ ] Verificar `~/Library/LaunchAgents/` para cada usuário
* [ ] Ordenar itens por data e comparar com data do incidente
* [ ] Ler `.plist` e identificar caminho do executável
* [ ] Validar se o arquivo apontado existe e é legítimo
* [ ] Procurar o nome/label do `.plist` nos logs
* [ ] Registrar todos os itens suspeitos

---

## 10. Conclusão

LaunchAgents e LaunchDaemons são o ponto número 1 de persistência em macOS e, por isso, não podem faltar em um procedimento de análise. Só de listar e ordenar esses diretórios você já consegue achar a maioria das persistências usadas em laboratórios, CTFs e cenários didáticos. Em conjunto com os logs, dá para montar a história completa: quando foi criado, o que ele tentava executar e se de fato executou.