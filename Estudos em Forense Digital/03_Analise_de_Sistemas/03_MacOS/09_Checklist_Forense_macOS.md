---
title: Checklist forense macOS
tags:
  - forense
  - macos
  - checklist
  - analise-de-sistemas
status: concluido
---

# Checklist forense macOS

Este checklist reúne os pontos das notas anteriores de macOS para você aplicar sempre que receber uma imagem de Mac. A ideia é ter uma sequência única para alunos ou para você mesmo, sem precisar lembrar o nome de cada pasta toda vez.

---

## 1. Identificação inicial

- [ ] Confirmar que a imagem/sistema é macOS.
- [ ] Identificar versão aproximada do macOS (impacta APFS, logs e Mail).
- [ ] Verificar se o volume está em APFS ou HFS+.
- [ ] Registrar se há criptografia/FileVault ativa.

---

## 2. Estrutura do sistema de arquivos

- [ ] Listar contêiner APFS e volumes (sistema, dados, preboot, recovery).
- [ ] Montar o volume de dados em modo somente leitura.
- [ ] Registrar caminho para:
  - `/System`
  - `/Library`
  - `/Users`
  - `/private` (incluindo `/private/var/log`)

---

## 3. Usuários

- [ ] Listar todas as pastas em `/Users/`.
- [ ] Identificar o(s) usuário(s) principal(is) pelo volume e datas de arquivos.
- [ ] Para cada usuário relevante, copiar:
  - [ ] `~/Library/`
  - [ ] `~/Library/Logs/`
  - [ ] `~/Library/Messages/`
  - [ ] `~/Library/Mail/`
  - [ ] `~/.ssh/` (se existir)
- [ ] Anotar se há usuário com perfil de administrador.

---

## 4. Logs e eventos

- [ ] Coletar `/var/log/` completo (inclusive rotacionados).
- [ ] Coletar `/Library/Logs/`.
- [ ] Coletar `~/Library/Logs/` dos usuários analisados.
- [ ] Se máquina viva: salvar saída de `log show ...` (unified logging).
- [ ] Procurar erros de carregamento de LaunchAgents/LaunchDaemons.

---

## 5. Persistência

- [ ] Verificar `/Library/LaunchAgents/`
- [ ] Verificar `/Library/LaunchDaemons/`
- [ ] Verificar `~/Library/LaunchAgents/` para cada usuário
- [ ] Ordenar por data e comparar com data do incidente.
- [ ] Abrir `.plist` suspeitos e anotar:
  - Label
  - Program/ProgramArguments
  - RunAtLoad/KeepAlive
- [ ] Verificar se o caminho apontado pelo `.plist` existe.
- [ ] Correlacionar nomes dos `.plist` com logs coletados.

---

## 6. Navegadores

- [ ] Safari: coletar `~/Library/Safari/`
- [ ] Chrome: coletar `~/Library/Application Support/Google/Chrome/`
- [ ] Edge/Brave (Chromium): coletar pastas equivalentes em `~/Library/Application Support/`
- [ ] Firefox: coletar `~/Library/Application Support/Firefox/Profiles/`
- [ ] Identificar e copiar bancos SQLite de histórico, downloads, cookies.
- [ ] Anotar qual navegador parece ser o principal.

---

## 7. Comunicação

- [ ] Coletar `~/Library/Messages/` (chat.db + Attachments).
- [ ] Coletar `~/Library/Mail/` e `~/Library/Preferences/com.apple.mail.plist`.
- [ ] Procurar artefatos de FaceTime (`com.apple.facetime.plist`).
- [ ] Coletar `~/Library/Application Support/<apps de chat>` (Slack, Teams, Discord, Telegram Desktop, etc.).
- [ ] Registrar se havia anexos de mensagens.

---

## 8. Time Machine e mídias externas

- [ ] Procurar configuração do Time Machine (plists de preferência).
- [ ] Registrar se o backup não foi fornecido.
- [ ] Se o disco de backup estiver na evidência: montar e procurar versões antigas de arquivos.
- [ ] Verificar `/Volumes/` para nomes de pendrives/HDs externos.
- [ ] Correlacionar horário de montagem de volume externo com o incidente.

---

## 9. Segurança e chaves

- [ ] Verificar `~/.ssh/` para chaves, authorized_keys e known_hosts.
- [ ] Registrar presença de Keychain (observação sobre possível proteção).
- [ ] Anotar se FileVault estava ativo.
- [ ] Anotar contas de e-mail ou VPN configuradas no usuário.

---

## 10. Linha do tempo mínima

- [ ] Extrair datas de:
  - criação/modificação dos `.plist` suspeitos
  - logs relevantes
  - histórico de navegador
  - mensagens/e-mails
  - arquivos em `/Users/<usuario>/Downloads/`
- [ ] Montar sequência: acesso/download → execução → persistência → erro (se houver) → possível cópia para mídia externa.

---

## 11. Conclusão

- [ ] Descrever quais usuários foram analisados.
- [ ] Descrever quais artefatos estavam presentes.
- [ ] Descrever quais artefatos estavam ausentes (ex.: Time Machine não fornecido).
- [ ] Relacionar artefatos suspeitos encontrados (plist, mensagem, download) com os horários dos logs.
- [ ] Indicar se a análise ficou limitada por criptografia ou falta de mídia.

