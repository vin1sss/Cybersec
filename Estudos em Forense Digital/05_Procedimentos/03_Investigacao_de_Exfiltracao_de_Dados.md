---
title: "Investigação de Exfiltração de Dados"
tags: [procedimento, exfiltracao, rede, navegador, timeline, dfir]
status: rascunho
---

# Investigação de Exfiltração de Dados

Este procedimento descreve como conduzir uma investigação quando há suspeita de que **dados saíram da máquina** (upload para serviço externo, cópia via SMB, sincronização não autorizada, envio por navegador, ferramenta de backup/sync). Ele combina análise de disco/imagem, artefatos de navegador, captura/PCAP e timeline.

Objetivo: responder **o quê saiu**, **quando saiu**, **por qual canal** e **com qual usuário/processo**.

---

## 1. Fontes que mais ajudam em exfiltração

- **Navegador / perfil do usuário** (Chrome, Edge, Firefox)  
  → histórico, downloads (reversa), extensões, sessões de Google Drive/OneDrive/Dropbox;
- **Logs de sistema e segurança**  
  → conexões de rede, acessos a compartilhamentos, logon/remoto;
- **PCAP / artefatos de rede**  
  → Wireshark, NetworkMiner, tcpdump;
- **Pastas de sincronização**  
  → OneDrive, Google Drive, Dropbox (metadados locais);
- **Timeline**  
  → para ver se o acesso ao arquivo e o envio ocorreram próximos;
- **Artefatos de execução**  
  → se o usuário usou um cliente de sync ou script para enviar.

---

## 2. Ordem geral (resumo)

1. **Contextualizar o arquivo/dado suspeito**
2. **Levantar evidências locais (histórico, sync, logs)**
3. **Correlacionar com rede/PCAP**
4. **Montar timeline do período**
5. **Consolidar responsáveis e canais de saída**

---

## 3. Contextualizar o arquivo/dado suspeito

Se você sabe **qual dado** foi exfiltrado (ex.: `clientes_2025.xlsx`):

- procurar na imagem onde esse arquivo estava;
- ver quem acessou/modificou (timestamps);
- anotar o horário aproximado.

Se você **não sabe qual arquivo**, mas sabe o **período** (ex.: “ontem às 14h”), a timeline vai ser mais importante.

---

## 4. Levantar evidências locais

### 4.1 Navegador

Extrair do perfil do usuário (ver `04_Ferramentas/08_Utilitarios_de_Apoio/05_SQLite_Browser.md`):

- `history` / `urls` → acessos a serviços de arquivo (drive, dropbox, onedrive, wetransfer…)
- `downloads` → às vezes mostra uploads iniciados via web
- cookies/sessions → mostram login em conta pessoal

Procure por domínios de storage:  
`drive.google.com`, `docs.google.com`, `dropbox.com`, `onedrive.live.com`, `box.com`, `mega.nz`, etc.

### 4.2 Pastas de sincronização

Verificar se há:
- `C:\Users\<user>\OneDrive\`
- `C:\Users\<user>\Dropbox\`
- `C:\Users\<user>\Google Drive\` ou similar

Muitos clientes guardam **logs ou bases SQLite** com “arquivo sincronizado em tal hora”. Abrir esses `.db` no SQLite Browser.

### 4.3 Logs do Windows

Exportar e analisar (`04_Ferramentas/03_Registro_e_Artefatos_Windows/04_Analise_de_Logs_Windows_Chainsaw_EvtxECmd.md`) para ver:

- logons (quem estava logado no horário);
- eventos de rede ou erros de acesso a compartilhamento;
- execução de ferramentas que podem ter sido usadas para enviar (PowerShell com `Invoke-WebRequest`, por exemplo).

---

## 5. Correlacionar com rede/PCAP

Se você tem captura de rede do período:

1. abrir no **Wireshark** (`04_Ferramentas/06_Rede/01_Wireshark_para_Forense.md`);
2. filtrar por IP da máquina suspeita:
```text
   ip.addr == 192.168.1.50
````

3. procurar conexões para serviços de storage (HTTPs vai estar criptografado, mas o **SNI/host** aparece);
4. usar **NetworkMiner** para ver se houve **upload** de arquivo identificado (aba *Files*);
5. se o canal for SMB interno, filtrar por porta 445 e ver host de destino.

Mesmo com HTTPS você consegue mostrar: “às 14:07 o host X estabeleceu conexão com dropbox.com e transferiu N bytes”.

---

## 6. Montar timeline do período

Para provar sequência (“acessou arquivo → conectou no serviço → fez upload”), é útil montar timeline:

1. montar a imagem e rodar:

   ```bash
   log2timeline.py timeline.plaso /mnt/evidencia
   ```
2. exportar só o período do incidente:

   ```bash
   psort.py -o L2tcsv -w exfil_14h.csv --slice "2025-11-07T14:00:00 2025-11-07T14:20:00" timeline.plaso
   ```
3. analisar o CSV ou importar no Timesketch.

Você procura por:

* acesso/leitura do arquivo de interesse;
* atividade de navegador;
* atividade de cliente de sync;
* artefatos de execução.

Se tudo cair na mesma janela de minutos, fortalece a evidência.

---

## 7. Canais comuns de exfiltração

* **Navegador → serviço de nuvem**: quase sempre vai aparecer no histórico e nos PCAPs.
* **Cliente de sincronização instalado**: vai deixar artefatos locais de sync.
* **SMB para outro host interno**: vai aparecer em PCAP e às vezes em logs.
* **Ferramenta/CLI (curl, PowerShell, script)**: procurar nos artefatos de execução e no Volatility (se tiver dump) por comandos.
* **E-mail/webmail**: olhar histórico e, se disponível, logs de proxy.

Ter esses canais em mente ajuda a saber onde olhar primeiro.

---

## 8. Registro dos achados

No final, o relatório mínimo deve ter:

* host de origem (IP, hostname, usuário logado);
* data/hora da exfiltração (ou janela de tempo);
* destino (domínio ou IP);
* volume aproximado (se visível na captura);
* artefato local que prova a ação (histórico, log, base SQLite, timeline);
* hash do(s) arquivo(s) de evidência usados.

---

## 9. Checklist rápido

* [ ] Identificado arquivo/dado ou período suspeito
* [ ] Perfil de navegador analisado (histórico, downloads, cookies)
* [ ] Pastas/apps de sincronização verificados
* [ ] Logs Windows exportados e analisados
* [ ] PCAP analisado (Wireshark/NetworkMiner) ou descartado se não disponível
* [ ] Timeline gerada para o período
* [ ] Canais de exfiltração identificados e documentados

---

## 10. Relação com outras pastas

* coleta de evidências → `02_Coleta_de_Evidencias/*`
* análise de sistema Windows → `03_Analise_de_Sistemas/01_Windows/*`
* ferramentas de rede → `04_Ferramentas/06_Rede/*`
* timeline e correlação → `04_Ferramentas/05_Timeline_e_Correlacao/*`
* utilitários (SQLite, CyberChef) → `04_Ferramentas/08_Utilitarios_de_Apoio/*`

Este procedimento amarra tudo isso num caminho único focado em “descobrir se saiu dado e como”.