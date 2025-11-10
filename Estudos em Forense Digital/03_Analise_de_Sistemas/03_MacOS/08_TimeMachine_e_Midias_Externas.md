---
title: Time Machine e mídias externas no macOS
tags:
  - forense
  - macos
  - backup
  - time-machine
  - midias-externas
status: concluido
---

# Time Machine e mídias externas no macOS

O macOS incentiva muito o uso do Time Machine e de discos externos para backup. Para forense isso é ótimo, porque pode existir uma cópia mais antiga dos mesmos arquivos que você está investigando - e às vezes o arquivo que foi apagado do volume principal ainda está no backup. Além disso, mídias externas (pendrives, HDs) deixam rastros de montagem que ajudam a provar que o usuário conectou um dispositivo.

---

## 1. Objetivo

- Registrar o papel do Time Machine em análise forense.
- Apontar onde procurar backups e listas de backups.
- Lembrar que discos/pendrives externos podem conter versões antigas de arquivos.
- Incluir no procedimento o passo "procurar mídias externas".

---

## 2. Time Machine - visão geral

O Time Machine cria backups incrementais do sistema, normalmente em:

- um disco externo dedicado;
- um compartilhamento de rede;
- ou outro volume configurado para isso.

Cada backup é organizado por data/hora. Em uma investigação isso significa que você pode navegar para "como o sistema estava" em determinada data.

Em ambiente de ensino/CTF às vezes não há o backup de fato, mas é importante documentar que o macOS tem esse recurso e que ele deve ser verificado.

---

## 3. Onde procurar evidências de Time Machine

Dependendo da configuração, você pode encontrar:

- preferências do Time Machine em `~/Library/Preferences/` e `/Library/Preferences/` (por exemplo, `com.apple.TimeMachine.plist`);
- referências a último backup, disco utilizado ou caminho de rede;
- pastas de backup no disco externo (se estiver disponível na imagem).

Se o disco de backup não estiver junto com a imagem principal, vale anotar no laudo: "Sistema configurado para Time Machine - mídia de backup não fornecida".

---

## 4. Por que o Time Machine é útil na forense

- pode conter arquivo antes de ser alterado ou criptografado;
- pode conter o binário malicioso que foi removido;
- pode mostrar que determinada pasta existia;
- pode ajudar a montar linha do tempo: última execução de backup, intervalo entre backups.

Ou seja, ele é fonte de artefatos antigos.

---

## 5. Mídias externas (pendrive, HD, cartão)

Usuários de macOS costumam plugar mídias externas que são montadas em `/Volumes/`. Em análise:

- verifique se há pastas residuais em `/Volumes/` na imagem;
- veja se existem logs apontando montagem de volumes externos;
- procure por nomes de volume típicos de pendrive (ex.: `KINGSTON`, `UNTITLED`, `NO NAME`) ou de HD externo.

Isso ajuda a responder: "o usuário tinha acesso a outra mídia?" ou "o arquivo pode ter sido copiado para fora?".

---

## 6. O que coletar quando o disco externo está presente

Se a imagem incluir o disco de backup ou o pendrive, coletar:

1. estrutura de diretórios inteira (para ver se há pasta de backup do Time Machine);
2. datas de criação/modificação de arquivos que batem com o incidente;
3. arquivos com mesmo nome dos encontrados no Mac, mas com data anterior;
4. eventuais binários ou scripts usados para persistência que foram removidos do sistema principal.

Em CTF é comum colocarem a versão "antes" do arquivo malicioso no disco externo.

---

## 7. Correlacionando com logs e persistência

- se um LaunchAgent apontava para `/Users/<usuario>/Downloads/evil`, mas o arquivo sumiu, veja se o Time Machine ou o disco externo guardam cópia;
- se os logs mostram que um volume foi montado pouco antes do incidente, isso pode indicar exfiltração ou execução de malware a partir do pendrive;
- registre o nome do volume e o horário de montagem.

---

## 8. Checklist rápido

1. Verificar se há arquivos/plists de configuração do Time Machine.
2. Procurar referências a discos de backup ou caminhos de rede.
3. Se o disco de backup estiver disponível, montar em modo somente leitura.
4. Navegar pelos backups por data e procurar:
   - binários removidos
   - versões antigas de documentos
   - pastas de LaunchAgents/LaunchDaemons antigas
5. Verificar `/Volumes/` na imagem principal.
6. Registrar nomes de volumes externos encontrados.
7. Cruzar horários de montagem com horário do incidente.
8. Anotar no laudo se o backup não foi fornecido.

---

## 9. Conclusão

Time Machine e mídias externas são extensões naturais da análise de macOS: se você ignorar, pode perder a versão do artefato que prova o caso. Mesmo que o backup não esteja presente na imagem, só o fato de o sistema estar configurado para usar Time Machine já é uma informação relevante que precisa aparecer no relatório.

