---
title: "Análise Inicial de Imagem Windows"
tags: [procedimento, windows, imagem-forense, autopsy, artefatos, timeline, dfir]
status: rascunho
---

# Análise Inicial de Imagem Windows

Este procedimento descreve **o que fazer depois que você já tem a imagem de disco de um host Windows** (RAW, E01, AFF) e levou para o laboratório. Ele organiza a análise em camadas: montar/ingestar a imagem, identificar o sistema, puxar artefatos de alto valor (usuários, execução, logs), e preparar a timeline. A ideia é chegar rápido a um **quadro inicial do que aconteceu**.

Ele se apoia principalmente em:
- `03_Analise_de_Sistemas/01_Windows/*`
- `04_Ferramentas/02_Analise_de_Disco_Imagem/*`
- `04_Ferramentas/03_Registro_e_Artefatos_Windows/*`
- `04_Ferramentas/05_Timeline_e_Correlacao/*`

---

## 1. Pré-requisitos

- imagem válida e com hash registrado;
- estação de análise com Autopsy **ou** Sleuth Kit **ou** ferramenta comercial;
- espaço em disco para ingestão e extração;
- data/hora do incidente (se conhecida) para orientar a timeline.

---

## 2. Ordem geral (resumo)

1. **Validar e montar/ingestar a imagem**
2. **Identificar o sistema e os usuários**
3. **Coletar artefatos de execução e de uso recente**
4. **Examinar logs de eventos Windows**
5. **Ver navegação/downloads (se pertinente ao caso)**
6. **Preparar timeline (opcional, mas recomendado)**
7. **Registrar achados iniciais**

---

## 3. Validar e montar/ingestar

1. **Validar hash** da imagem que chegou ao laboratório com o hash coletado em campo.
2. **Montar a imagem** (Linux: `mount -o ro,loop,show_sys_files ...` ou usar Sleuth Kit) **ou** abrir direto no **Autopsy**:
   - criar novo caso;
   - adicionar a imagem como data source;
   - deixar o Autopsy indexar.

Objetivo: poder navegar pelo sistema de arquivos e pelos artefatos sem alterar o original.

---

## 4. Identificar o sistema e os usuários

Abrir a estrutura de pastas e anotar:

- versão do Windows (pode vir de `Windows/System32/config/SOFTWARE` ou do próprio Autopsy);
- nomes de contas em `C:\Users\...`;
- datas de criação/modificação das pastas de usuário;
- presença de perfis incomuns (ex.: contas de serviço, contas recém-criadas).

Relacionar isso com o que está em `03_Analise_de_Sistemas/01_Windows/01_Visao_Geral_Windows.md` e `05_Usuarios_e_Perfis.md`.

Isso ajuda a saber **onde procurar histórico, downloads, artefatos de navegador**.

---

## 5. Artefatos de execução e uso recente

Entrar nos artefatos que mais mostram “o que o usuário rodou”:

- **Prefetch** (`C:\Windows\Prefetch`) – mostra programas executados e quando;
- **LNK / Jump Lists** – atalhos e documentos abertos;
- **Amcache / Shimcache** – histórico de execução (pode usar ferramentas específicas depois);
- **Recent** dos usuários.

Se estiver usando Autopsy, muitos desses artefatos já aparecem categorizados. Se estiver no modo manual, extraia os arquivos e rode as ferramentas descritas em:
- `04_Ferramentas/03_Registro_e_Artefatos_Windows/01_RegRipper.md`
- `04_Ferramentas/03_Registro_e_Artefatos_Windows/02_regipy_regrippy.md`

Objetivo: montar uma lista inicial de **programas executados + datas**.

---

## 6. Logs de eventos Windows

Os `.evtx` ficam geralmente em `C:\Windows\System32\winevt\Logs\`.

Passos:

1. exportar os arquivos de log;
2. rodar ferramenta própria (Chainsaw, EvtxECmd) conforme `04_Ferramentas/03_Registro_e_Artefatos_Windows/04_Analise_de_Logs_Windows_Chainsaw_EvtxECmd.md`;
3. buscar por:
   - logons
   - execução de processos (eventos 4688, dependendo da auditoria)
   - falhas de logon
   - instalação de software
   - eventos de rede/remoto

Isso normalmente já mostra se houve acesso remoto, se o usuário logou fora do horário, se houve criação de conta, etc.

---

## 7. Navegação e downloads

Se o caso envolver **exfiltração, vazamento ou download de malware**, olhar o perfil do usuário em:

- `C:\Users\<user>\AppData\Local\Google\Chrome\User Data\Default\` (ou equivalente de Edge/Firefox)
- arquivos de histórico e downloads (muitos são SQLite) → abrir com **SQLite Browser** (ver `04_Ferramentas/08_Utilitarios_de_Apoio/05_SQLite_Browser.md`)
- pasta `Downloads` do usuário

Isso ajuda a responder: “o usuário baixou o quê, de onde, e quando?”.

---

## 8. Preparar timeline (opcional, mas recomendado)

Se a imagem tiver muitos artefatos e o caso depender de horários, já dá pra partir para timeline:

1. montar a imagem em `/mnt/evidencia`
2. rodar:

   ```bash
   log2timeline.py timeline.plaso /mnt/evidencia
	````

3. exportar:

   ```bash
   psort.py -o L2tcsv -w timeline.csv timeline.plaso
   ```
4. (opcional) importar no Timesketch

Isso dá a visão consolidada de tudo que aconteceu perto do incidente (ver `04_Ferramentas/05_Timeline_e_Correlacao/*`).

---

## 9. Registro de achados iniciais

Ao final da análise inicial, registrar:

* SO e versão;
* usuários encontrados;
* programas vistos nos artefatos de execução (com datas);
* eventos relevantes dos logs (logons, acessos remotos, erros);
* downloads/URLs interessantes;
* intervalos de tempo que merecem aprofundamento.

Esse documento vai guiar análises posteriores (ex.: foco em PowerShell, foco em RDP, foco em navegador).

---

## 10. Checklist rápido

* [ ] Hash da imagem validado
* [ ] Imagem montada/ingestada
* [ ] Sistemas e usuários identificados
* [ ] Artefatos de execução coletados (prefetch, recent, amcache/shimcache)
* [ ] Logs Windows exportados e analisados
* [ ] Histórico/downloads verificados (se aplicável)
* [ ] (Opcional) Timeline gerada
* [ ] Achados iniciais registrados

---

## 11. Relação com outras pastas

* Coleta de evidências → `02_Coleta_de_Evidencias/*`
* Análise Windows (artefatos e estrutura) → `03_Analise_de_Sistemas/01_Windows/*`
* Ferramentas específicas → `04_Ferramentas/*`
* Procedimentos específicos (malware, exfiltração) → demais arquivos de `05_Procedimentos` (a preencher)

Este procedimento é a “porta de entrada” para qualquer imagem Windows nova que chegar no laboratório.