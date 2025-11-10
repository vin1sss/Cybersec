---
title: "Análise de logs Windows com Chainsaw e EvtxECmd"
tags: [ferramenta, windows, logs, evtx, dfir, chainsaw, evtxecmd]
status: rascunho
---

# Análise de logs Windows com Chainsaw e EvtxECmd

Este documento descreve como analisar **logs de eventos do Windows (.evtx)** usando duas ferramentas muito usadas em DFIR: **Chainsaw** e **EvtxECmd** (da série de ferramentas do Eric Zimmermann). O objetivo é conseguir extrair rapidamente eventos relevantes (execução de programas, criação de contas, logons, PowerShell, etc.) a partir dos arquivos `.evtx` coletados de uma imagem ou de um host comprometido.

Ele complementa os outros arquivos de Registro/artefatos Windows e mostra o caminho para tratar o outro grande conjunto de evidências do Windows: os **Event Logs**.

---

## 1. Conceito

- O Windows registra muita coisa em arquivos de log no formato **.evtx**, normalmente em:
  - `C:\Windows\System32\winevt\Logs\`
- Em uma aquisição de disco, esses arquivos vêm junto e podem ser copiados para análise offline.
- Ler esses logs dentro do Windows é possível (Event Viewer), mas, para forense, é mais interessante **processar offline**, filtrar e exportar.

Ferramentas como Chainsaw e EvtxECmd fazem justamente isso: leem `.evtx` fora do Windows e transformam em saídas pesquisáveis (CSV, JSON, texto).

---

## 2. Onde estão os logs no disco

Em uma imagem Windows, os principais logs ficam em:

```text
C:\Windows\System32\winevt\Logs\
````

Exemplos comuns:

* `Security.evtx`
* `System.evtx`
* `Application.evtx`
* `Microsoft-Windows-PowerShell%4Operational.evtx`
* `Windows PowerShell.evtx`

Ao montar a imagem em Linux, isso vira algo como:

```text
/mnt/img/Windows/System32/winevt/Logs/Security.evtx
```

O fluxo padrão é: copiar esses `.evtx` para uma pasta de trabalho e rodar as ferramentas sobre essa pasta.

---

## 3. EvtxECmd (visão geral)

**EvtxECmd** é uma ferramenta que:

* lê arquivos .evtx;
* aplica “maps” conhecidos para extrair campos importantes de cada provedor de log;
* gera saída em CSV (muito conveniente para filtrar depois);
* é muito usada em DFIR por ser rápida e previsível.

Características:

* focada em extrair de forma estruturada;
* boa para processar um ou muitos logs;
* pode ser usada em linha de comando;
* ótima para aulas (resultado claro).

### Uso típico

```text
EvtxECmd.exe -f C:\caso01\logs\Security.evtx --csv C:\caso01\saida\ --csvname security.csv
```

Explicação:

* `-f` aponta para o arquivo .evtx
* `--csv` define a pasta de saída
* `--csvname` define o nome do CSV

Você pode apontar para uma pasta inteira:

```text
EvtxECmd.exe -d C:\caso01\logs\ --csv C:\caso01\saida\
```

Assim, todos os .evtx da pasta são processados.

---

## 4. Chainsaw (visão geral)

O **Chainsaw** é uma ferramenta mais voltada para **detecção rápida** e **caça a ameaças** em cima de logs Windows. Ele:

* lê .evtx;
* aplica Sigma rules ou consultas pré-definidas;
* destaca eventos suspeitos (execuções anômalas, PowerShell estranho, uso de scripts, etc.);
* serve muito bem para “primeira passada” em um conjunto grande de logs.

É excelente quando você tem pouco tempo e quer ver “o que salta aos olhos”.

### Uso típico (exemplo genérico)

```text
chainsaw.exe hunt C:\caso01\logs\ --rules .\rules\ --mapping .\mappings\winlogbeat-json.yml --output C:\caso01\chainsaw-out.csv
```

A sintaxe pode mudar conforme a versão, mas a ideia é:

* dizer onde estão os logs;
* dizer quais regras usar;
* gerar uma saída que você possa ver depois.

Muitas distribuições do Chainsaw já vêm com regras básicas que apontam para eventos suspeitos comuns.

---

## 5. Quando usar cada um

* **EvtxECmd**:

  * quando você quer uma extração limpa, em CSV, de um ou vários logs;
  * quando vai fazer análise posterior no Excel, LibreOffice ou em outra ferramenta;
  * quando está ensinando como o evento é estruturado.

* **Chainsaw**:

  * quando você quer “achados rápidos”;
  * quando quer aplicar regra (Sigma) para identificar TTPs;
  * quando quer priorizar eventos suspeitos antes de olhar todo o resto.

Nada impede de usar os dois: primeiro EvtxECmd para extrair tudo, depois Chainsaw para marcar o que é suspeito.

---

## 6. Eventos Windows que costumam interessar em forense

Alguns IDs de evento que valem a pena olhar (especialmente em `Security.evtx` e `System.evtx`):

* 4624 - logon bem-sucedido
* 4625 - logon falho
* 4672 - logon com privilégios especiais
* 4688 - criação de processo
* 4720 - criação de conta
* 4722 - habilitação de conta
* 1102 - limpeza de log
* eventos do PowerShell (operational) - execução de scripts/comandos
* eventos de serviço sendo criado/modificado

As ferramentas facilitam porque já destacam ou pelo menos colocam esses campos em colunas separadas.

---

## 7. Fluxo de trabalho offline

1. **Montar a imagem** (somente leitura).
2. **Copiar os .evtx** para uma pasta de trabalho, por exemplo:

   * `C:\caso01\logs\` ou `./logs/`
3. **Processar com EvtxECmd** para ter uma visão tabular:

   ```text
   EvtxECmd.exe -d ./logs --csv ./saida/
   ```
4. **Processar com Chainsaw** para caça rápida:

   ```text
   chainsaw.exe hunt ./logs --rules ./rules --output ./saida/chainsaw.csv
   ```
5. **Correlacionar** os eventos suspeitos com:

   * horários do incidente;
   * contas de usuário;
   * artefatos do Registro;
   * timeline de disco.

---

## 8. Blocos operacionais

```text
comando: processar um log específico com EvtxECmd

EvtxECmd.exe -f .\logs\Security.evtx --csv .\saida\ --csvname security.csv
```

```text
comando: processar todos os logs de uma pasta com EvtxECmd

EvtxECmd.exe -d .\logs\ --csv .\saida\
```

```text
comando: caça rápida com Chainsaw

chainsaw.exe hunt .\logs\ --rules .\rules\ --output .\saida\chainsaw.csv
```

```text
comando: listar ajuda do Chainsaw

chainsaw.exe --help
```

---

## 9. Boas práticas

* trabalhar sobre cópia dos .evtx, não sobre o arquivo dentro da imagem montada;
* manter os nomes originais dos logs (`Security.evtx`, `System.evtx`) para não se perder na hora de correlacionar;
* registrar o timezone e a origem da máquina - os eventos são carimbados com horário e isso impacta na timeline;
* guardar as saídas CSV junto do caso;
* quando possível, rodar as ferramentas na versão mais recente (parsers e regras melhoram).

---

## 10. Comparação com outras abordagens

* **Event Viewer (GUI do Windows)** - bom para olhar um evento, ruim para milhares de eventos.
* **EvtxECmd** - ótimo para exportação e estudo.
* **Chainsaw** - ótimo para detecção e priorização.
* **Ferramentas SIEM** - ótimas para ambiente corporativo, mas dependem de pré-coleta.
