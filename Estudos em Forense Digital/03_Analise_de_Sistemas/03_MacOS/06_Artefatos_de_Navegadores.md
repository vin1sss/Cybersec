---
title: Artefatos de navegadores no macOS
tags:
  - forense
  - macos
  - navegadores
  - safari
  - chrome
  - firefox
status: concluido
---

# Artefatos de navegadores no macOS

Navegadores são uma das melhores fontes de evidência em sistemas macOS porque guardam histórico, downloads, cookies, logins e às vezes até formulários. A vantagem é que quase tudo fica em pastas previsíveis dentro de `~/Library/`. Esta nota lista os navegadores mais comuns (Safari, Chrome/Chromium/Edge e Firefox) e mostra onde ficam os arquivos principais.

---

## 1. Objetivo

- Apontar os caminhos padrão dos navegadores no macOS.
- Indicar quais arquivos/bancos valem extração.
- Lembrar que muitos deles são SQLite e podem ser abertos diretamente.
- Montar um checklist de coleta por navegador.

---

## 2. Safari

Safari é o navegador nativo. Os artefatos ficam dentro da pasta do usuário:

- `~/Library/Safari/`
- `~/Library/Caches/com.apple.Safari/`
- `~/Library/WebKit/com.apple.Safari/`

Arquivos importantes (podem variar conforme versão):

- `History.db` - histórico de navegação.
- `Downloads.plist` ou registros similares - lista de downloads.
- Pastas de ícones e previews que podem ajudar a mostrar que a página foi aberta.

Em forense:
- copiar a pasta `~/Library/Safari/` inteira do usuário;
- anotar a data de acesso/alteração;
- cruzar com horário do incidente.

---

## 3. Google Chrome e navegadores Chromium (Brave, Edge novo)

No macOS, o Chrome guarda dados em:

- `~/Library/Application Support/Google/Chrome/`

Dentro dessa pasta você encontra perfis:

- `Default/`
- `Profile 1/`
- `Profile 2/`
- etc.

Cada perfil tem seus próprios bancos. Arquivos importantes (dentro de `Default/` ou do perfil correspondente):

- `History` - histórico de navegação (SQLite).
- `Cookies` - cookies (SQLite, pode estar criptografado).
- `Login Data` - logins salvos (exige decriptar).
- `Bookmarks` - favoritos.
- `Downloads` ou na própria tabela dentro do History.

Para Edge baseado em Chromium o caminho é parecido:

- `~/Library/Application Support/Microsoft Edge/`

Para Brave:

- `~/Library/Application Support/BraveSoftware/Brave-Browser/`

A lógica é sempre a mesma:
- entrar na pasta Application Support do navegador;
- localizar o perfil;
- coletar os bancos SQLite de histórico, cookies e downloads.

---

## 4. Firefox

Firefox costuma ficar em:

- `~/Library/Application Support/Firefox/Profiles/`

Dentro de `Profiles/` você verá uma pasta com sufixo `.default-release` ou parecido. É dentro dela que ficam:

- `places.sqlite` - histórico e favoritos.
- `cookies.sqlite` - cookies.
- `formhistory.sqlite` - histórico de formulários.
- `logins.json` + `key4.db` - logins (necessário decriptar com chave).

Se houver mais de um perfil, coletar todos. Em CTF/lab às vezes usam o Firefox porque é mais fácil trocar de perfil e esconder o rastro em outro.

---

## 5. O que extrair primeiro

Ordem sugerida para análise rápida:

1. Histórico de navegação:
   - Safari: `History.db`
   - Chrome: `History`
   - Firefox: `places.sqlite`
2. Downloads:
   - Safari: arquivos/plists em `~/Library/Safari/`
   - Chrome: tabela de downloads dentro do `History`
   - Firefox: `places.sqlite` e/ou JSON de downloads conforme versão
3. Cookies (para comprovar sessão ativa)
4. Logins (se o caso permitir e se houver ferramenta para decriptar)

Com isso você já responde perguntas típicas:
- que site foi acessado?
- quando foi acessado?
- houve download de arquivo malicioso?
- havia sessão aberta no serviço X?

---

## 6. Correlação com outras notas do macOS

Depois de coletar os artefatos de navegador:
- correlacionar horários com os logs do sistema (nota de logs);
- ver se houve download de algo que depois apareceu como persistência (nota de LaunchAgents/Daemons);
- procurar no histórico por termos ligados ao incidente.

Isso ajuda a mostrar a sequência: usuário acessou página maliciosa, fez download, executou binário, binário criou LaunchAgent.

---

## 7. Cuidados na coleta

- muitos desses arquivos ficam bloqueados quando o navegador está aberto; em imagem isso não é problema;
- produzir cópia em modo somente leitura;
- se possível, anotar a versão do navegador encontrada na pasta (alguns armazenam em arquivos de config);
- não alterar timestamps.

---

## 8. Checklist de coleta de artefatos de navegador

1. Para cada usuário em `/Users/<usuario>/`:
   - [ ] Coletar `~/Library/Safari/` (se existir)
   - [ ] Coletar `~/Library/Application Support/Google/Chrome/` (se existir)
   - [ ] Coletar `~/Library/Application Support/Microsoft Edge/` (se existir)
   - [ ] Coletar `~/Library/Application Support/BraveSoftware/Brave-Browser/` (se existir)
   - [ ] Coletar `~/Library/Application Support/Firefox/Profiles/` (se existir)
1. Identificar perfis e copiar todos os bancos SQLite.
2. Registrar datas de modificação dos arquivos de histórico.
3. Anotar qual navegador parece ser o principal.
4. Cruzar histórico com horário do incidente.

---

## 9. Conclusão

Artefatos de navegador em macOS seguem o mesmo raciocínio de outros sistemas: ficam quase sempre dentro da pasta do usuário, em `~/Library/...`, e costumam ser bancos SQLite fáceis de consultar. Se você coletar Safari, Chromium e Firefox para cada usuário relevante, dificilmente vai perder uma evidência de acesso ou download.

