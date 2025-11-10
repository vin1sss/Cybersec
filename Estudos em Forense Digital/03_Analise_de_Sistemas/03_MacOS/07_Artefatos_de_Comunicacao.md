---
title: Artefatos de comunicação no macOS
tags:
  - forense
  - macos
  - comunicacao
  - imessage
  - mail
  - mensagens
status: concluido
---

# Artefatos de comunicação no macOS

O macOS costuma centralizar comunicação em pastas dentro de `~/Library/`, principalmente para Mensagens (iMessage) e Mail. Isso é ótimo para forense porque você consegue encontrar bancos SQLite, anexos e pastas inteiras de correio eletrônico dentro da home do usuário, sem precisar varrer o sistema todo.

---

## 1. Objetivo

- Listar os principais artefatos de comunicação que ficam no macOS por usuário.
- Indicar os caminhos mais usados por iMessage/Messages e Mail.
- Lembrar que muitos dados ficam em bancos SQLite.
- Montar um checklist de coleta.

---

## 2. Mensagens (iMessage/Messages)

O aplicativo Mensagens do macOS costuma guardar dados em:

- `~/Library/Messages/`

Dentro dessa pasta você pode encontrar:

- `chat.db` - banco principal de conversas (SQLite).
- `Attachments/` - anexos trocados nas conversas.
- outros arquivos auxiliares (.plist, índices, caches).

Pontos importantes:
- o `chat.db` costuma ter dados de remetente, destinatário, timestamps e conteúdo das mensagens;
- anexos podem ser imagens, PDFs, áudios;
- se o usuário tem integração com iPhone/Apple ID, boa parte das conversas acaba vindo para o Mac.

Para análise, sempre copiar a pasta inteira `~/Library/Messages/` e trabalhar na cópia.

---

## 3. Mail (cliente de e-mail nativo)

O Mail do macOS guarda dados em:

- `~/Library/Mail/`

Dependendo da versão do macOS, você verá subpastas do tipo `V8`, `V9` etc., que representam o formato daquela versão do Mail. Dentro delas ficam:

- caixas de entrada e pastas por conta/e-mail;
- mensagens em formato .emlx;
- anexos.

Também vale olhar:

- `~/Library/Preferences/com.apple.mail.plist`
  - pode conter configurações de contas;
- `~/Library/Mail/V*/MailData/`
  - índices, envelopes de mensagens, metadados.

O importante é registrar:
- que o usuário tinha conta(s) configurada(s) no Mail;
- que havia mensagens locais no momento da coleta;
- que anexos foram armazenados.

---

## 4. FaceTime e chamadas

FaceTime e histórico de chamadas podem aparecer em:

- `~/Library/Preferences/com.apple.facetime.plist`
- `~/Library/Containers/com.apple.FaceTime/` (dependendo da versão)

Esses artefatos ajudam a mostrar que o usuário se comunicou por áudio/vídeo em determinada data. Em CTF/lab às vezes basta provar que houve uso do FaceTime, não necessariamente extrair o conteúdo.

---

## 5. Aplicativos de terceiros

Muitos apps de comunicação instalados pelo usuário vão cair em:

- `~/Library/Application Support/<NomeDoApp>/`
- `~/Library/Containers/<bundle-id>/`

Exemplos comuns:
- Zoom
- Microsoft Teams
- Slack
- Telegram Desktop
- Discord

Cada um tem seu formato, mas a regra é a mesma:
- olhar primeiro `Application Support`;
- copiar o diretório inteiro para análise;
- procurar por bancos SQLite, JSON, logs e pastas de cache.

Como é ambiente de ensino, vale deixar anotado que "artefatos de comunicação de terceiros ficam geralmente em Application Support".

---

## 6. Correlação com usuário e horário

Como as pastas ficam dentro de `~/Library/`, você consegue ligar facilmente:
- quem se comunicou (qual usuário da máquina);
- com que aplicativo;
- em que horário.

Depois é só cruzar com:
- logs do sistema (nota de logs);
- persistência (se o malware se espalhava por mensagem);
- navegador (se o link recebido foi aberto).

---

## 7. Cuidados e limitações

- alguns bancos podem estar bloqueados se o app estiver aberto na hora da coleta ao vivo;
- versões mais novas do macOS podem mudar levemente o caminho de Mail e Messages;
- integração com iCloud pode fazer com que nem tudo esteja local;
- para conteúdo criptografado ou sincronizado pode ser necessário credencial do usuário.

Registrar isso no laudo evita cobrar da análise algo que não estava na máquina.

---

## 8. Checklist de coleta de artefatos de comunicação

1. Para cada usuário:
   - [ ] Coletar `~/Library/Messages/` completo (chat.db + Attachments).
   - [ ] Coletar `~/Library/Mail/` completo.
   - [ ] Coletar `~/Library/Preferences/com.apple.mail.plist`.
   - [ ] Coletar `~/Library/Preferences/com.apple.facetime.plist` (se existir).
   - [ ] Coletar `~/Library/Application Support/<apps de chat>` que estiverem instalados.
2. Identificar bancos SQLite e copiar em separado para análise.
3. Anotar datas de modificação de `chat.db` e de pastas de Mail.
4. Cruzar nomes de contatos/conversas com o contexto do incidente.
5. Registrar se havia anexos e onde estavam.

---

## 9. Conclusão

Os artefatos de comunicação no macOS são bem previsíveis: quase tudo fica na pasta do usuário, em `~/Library/`, e em formatos conhecidos (SQLite, .plist, pastas de Mail). Se você coletar Messages, Mail e Application Support dos apps de chat, já cobre praticamente todos os cenários didáticos e de CTF que envolvem "mostrar que o usuário falou com alguém" ou "comprovar que um arquivo chegou por mensagem".

