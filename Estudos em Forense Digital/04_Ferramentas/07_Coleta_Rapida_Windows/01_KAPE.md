---
title: "KAPE (Kroll Artifact Parser and Extractor)"
tags: [ferramenta, windows, coleta-rapida, kape, artefatos, triagem, dfir]
status: rascunho
---

# KAPE (Kroll Artifact Parser and Extractor)

O **KAPE** é uma ferramenta portátil para **coleta e processamento rápido de artefatos forenses em Windows**. Ele foi pensado para o cenário clássico de resposta a incidentes: você chega em uma máquina Windows, não tem tempo (ou permissão) para fazer uma imagem de disco completa, mas precisa tirar logo **os artefatos de maior valor forense** (logs, Registro, prefetch, LNK, navegador, MFT...) e, se possível, já sair com isso **parseado em CSV**.

A sacada do KAPE é separar o trabalho em duas fases:

1. **Targets (T)** → o que coletar;
2. **Modules (M)** → o que fazer com o que foi coletado.

Assim você pode usar só a fase T (coleta) ou T+M (coleta + processamento automático).

---

## 1. Quando usar o KAPE

Use o KAPE quando:

- precisa de **triagem rápida** em máquina Windows (IR, SOC, perito em campo);
- não quer abrir GUI, navegar por pastas, lembrar caminhos longos de artefatos;
- quer ter certeza de que pegou **o conjunto mínimo que sempre interessa** (event logs, hives, prefetch, recent files, artefatos de usuário);
- precisa padronizar a coleta entre vários analistas (todos usam o mesmo target);
- quer sair da máquina já com CSVs legíveis (módulos executados).

Ele **não substitui** uma imagem forense completa, mas muitas vezes é o que salva quando o tempo é curto.

---

## 2. Conceito de Targets e Modules

### 2.1 Targets (T)
Targets são arquivos de configuração (`.tkape`) que dizem ao KAPE:
- “vá até tal pasta”
- “traga este tipo de arquivo”
- “com esta nomenclatura”

Exemplos de targets:
- coletar todos os **Windows Event Logs**;
- coletar **hives de Registro**;
- coletar **Prefetch**;
- coletar **artefatos de navegador de todos os usuários**;
- coletar o “triagem padrão” (`!SANS_Triage`, `KAPE Triage`, etc.).

Ou seja: TARGET = *o quê*.

### 2.2 Modules (M)
Modules são scripts/configurações que rodam **depois** que você já tem os arquivos. Eles pegam o que foi coletado e passam em ferramentas de parsing (muitas delas do Eric Zimmermann).

Exemplos de modules:
- rodar **EvtxECmd** nos `.evtx` coletados;
- rodar **MFTECmd** na MFT coletada;
- rodar **RECmd** nos hives coletados;
- rodar o **!EZParser** (um “combo” que processa um monte de artefatos de uma vez).

Ou seja: MODULE = *o que fazer com o que foi coletado*.

---

## 3. Execução básica (linha de comando)

Forma clássica:

```text
kape.exe --tsource C: --tdest C:\coleta --target !SANS_Triage
````

Explicação:

* `--tsource C:` → a origem dos artefatos é o disco C: da máquina
* `--tdest C:\coleta` → tudo que for coletado vai ser salvo em `C:\coleta`
* `--target !SANS_Triage` → é o conjunto de artefatos que queremos (um “pacote padrão”)

Se você quiser **coletar e já processar**:

```text
kape.exe --tsource C: --tdest C:\coleta --target !SANS_Triage ^
--msource C:\coleta --mdest C:\coleta\parsed --module !EZParser
```

Aqui:

* `--msource C:\coleta` → modules vão ler da pasta que você acabou de encher
* `--mdest C:\coleta\parsed` → saída dos modules (CSV, txt, etc.)
* `--module !EZParser` → roda um módulo “grandão” que chama vários parsers

---

## 4. O que o KAPE costuma coletar

Depende do target, mas, em geral, ele consegue pegar:

* **Logs Windows (.evtx)**:

  * `Security.evtx`
  * `System.evtx`
  * `Application.evtx`
  * e logs de aplicações/sources específicos
* **Registro / hives**:

  * `SYSTEM`, `SOFTWARE`, `SAM`, `SECURITY`
  * `NTUSER.DAT` e `USRCLASS.DAT` de cada usuário
* **Artefatos de execução**:

  * Prefetch
  * ShimCache/Amcache (dependendo de como você configurar o parse depois)
* **Artefatos de usuário**:

  * Recent files
  * Jump Lists
  * LNKs
  * Browser history
* **MFT** e outros artefatos de sistema de arquivos (para usar depois com MFTECmd)

Com um único comando você sai com quase tudo aquilo que um analista de Windows sempre pede primeiro.

---

## 5. GUI (gkape.exe)

Além da CLI, o KAPE tem uma interface gráfica (`gkape.exe`) que é ótima para quem ainda está aprendendo:

* mostra a lista de **Targets** disponíveis e você marca os que quer;
* mostra a lista de **Modules** e você marca os que quer;
* mostra a linha de comando equivalente (boa para copiar e automatizar depois);
* ajuda a não errar em `--tsource`, `--tdest`, `--msource`, `--mdest`.

Em laboratório/curso, é legal mostrar a GUI primeiro e depois partir para CLI.

---

## 6. Saída e organização de pastas

Depois de rodar o KAPE, a pasta de destino costuma ficar algo assim:

```text
C:\coleta\
  ├── KAPE-Targets\
  │     ├── WindowsEventLogs\
  │     ├── RegistryHives\
  │     ├── Prefetch\
  │     └── ...
  └── KAPE-Modules\
        ├── EvtxECmd\
        ├── MFTECmd\
        └── ...
```

* tudo que está em **KAPE-Targets** são os artefatos **brutos** copiados;
* tudo que está em **KAPE-Modules** são os **resultados** (muitas vezes CSV) dos parsers.

Isso facilita comprimir e mandar para outro analista, ou arquivar no repositório do caso.

---

## 7. Usos avançados e cenários comuns

### 7.1 Triagem em várias máquinas

Você pode colocar o KAPE em um pendrive, plugar em 3 máquinas diferentes e rodar exatamente o mesmo comando. No final, terá três pastas `C:\coleta` (ou o nome que você der), todas com a mesma estrutura. Fica fácil comparar.

### 7.2 Execução remota

Em ambientes de IR, dá para usar o KAPE remotamente (ex.: com PSExec, ou via EDR que roda comando remoto). A lógica não muda: você aponta `--tsource` para a unidade do host e `--tdest` para uma pasta onde você tenha permissão.

### 7.3 Coletar agora, processar depois

Você pode rodar **apenas** o target na máquina vítima (mais rápido), copiar a pasta de targets para sua workstation e aí rodar **só os modules** lá:

```text
kape.exe --msource .\KAPE-Targets --mdest .\Parsed --module !EZParser
```

Assim você não sobrecarrega o host.

---

## 8. Blocos operacionais

```text
comando: triagem rápida padrão (coleta)

kape.exe --tsource C: --tdest C:\coleta --target !SANS_Triage
```

```text
comando: coleta + parse automático

kape.exe --tsource C: --tdest C:\coleta --target !SANS_Triage ^
         --msource C:\coleta --mdest C:\coleta\parsed --module !EZParser
```

```text
comando: rodar módulos depois, em outra máquina

kape.exe --msource .\KAPE-Targets --mdest .\Parsed --module !EZParser
```

```text
comando: ver ajuda e lista de artefatos

kape.exe --help
# ou abrir gkape.exe e olhar os targets/modules disponíveis
```

---

## 9. Boas práticas

* **rodar como administrador** para garantir acesso a todos os artefatos;
* usar **destino em disco diferente** quando possível (menos bagunça no C:);
* **registrar data/hora** do início e término da coleta (vai para o relatório);
* **não editar** os arquivos dentro de `KAPE-Targets` – analisar sempre em cópia;
* **comprimir** a pasta de saída (zip/7z) antes de transferir;
* guardar junto o **comando executado** (ou screenshot do gkape) para reprodutibilidade;
* manter o KAPE atualizado (os artefatos de Windows mudam com versões novas).

---

## 10. Comparação rápida

* **KAPE** → coleta e parse rápido de Windows, portátil, focado em artefatos de alto valor.
* **Imagem de disco** → tudo, bit a bit, mas pesado e demorado.
* **Ferramentas do próprio Eric Zimmermann (EvtxECmd, MFTECmd, RECmd)** → KAPE pode chamar todas elas pra você.
* **EDR/coleta corporativa** → bom para muitas máquinas, mas nem sempre tem todos os artefatos; você pode complementar com KAPE.

O KAPE é, resumindo, o “kit de emergência” para Windows: chega, coleta o que é mais importante, processa se der tempo e leva embora.