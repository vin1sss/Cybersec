---
title: "plaso / log2timeline"
tags: [ferramenta, timeline, correlacao, plaso, log2timeline, dfir]
status: rascunho
---

# plaso / log2timeline

Este documento apresenta o **plaso** (ferramenta) e o **log2timeline.py** (script principal) como solução para gerar **timelines forenses unificadas** a partir de várias fontes de evidência: sistema de arquivos, registros de navegador, logs do sistema, artefatos de aplicativos, etc. A ideia do plaso é juntar tudo em um único arquivo de eventos e, depois, você filtra e analisa no tempo.

Ele é muito útil quando:
1. você tem muitos artefatos diferentes de uma mesma máquina;
2. precisa saber “o que aconteceu entre 14:00 e 14:10?”;
3. precisa correlacionar acesso a arquivo + execução + navegação + logs.

---

## 1. Conceito

O plaso (às vezes chamado de “log2timeline” porque esse era o nome original) é um **processador de artefatos temporais**. Ele:

- lê uma fonte de dados (diretório, imagem de disco, arquivo único);
- aplica uma grande coleção de parsers (para browsers, logs, artefatos de SO);
- produz um arquivo de saída no formato **.plaso** (um banco de eventos);
- esse arquivo depois é “imprimível” com outra ferramenta (`psort.py`) em CSV, JSON, etc.

Diferente de ferramentas que mostram só os timestamps do sistema de arquivos, o plaso tenta **extrair tudo que tem tempo**.

---

## 2. Fluxo básico de uso

O fluxo do plaso tem duas etapas:

1. **Coleta/parse** (log2timeline.py)
   - lê a fonte e cria o arquivo `.plaso`
2. **Formatação/filtro** (psort.py)
   - lê o `.plaso` e gera um CSV/JSON já ordenado por tempo

Visualmente:

```text
[imagem/diretório] --(log2timeline.py)--> timeline.plaso --(psort.py)--> timeline.csv
````

Isso é bom porque você pode fazer uma coleta pesada uma vez e depois gerar quantas saídas quiser.

---

## 3. Fontes de dados

O plaso consegue trabalhar com:

* imagens de disco (montadas ou não);
* diretórios de evidência;
* arquivos específicos (event logs, browser history, $MFT);
* contêineres forenses que ele saiba abrir.

Com imagem de disco, muita gente faz assim:

1. monta a imagem em `/mnt/evidencia`
2. roda o log2timeline apontando para `/mnt/evidencia`
3. deixa o plaso “varrer” tudo

---

## 4. Criando o arquivo .plaso (log2timeline.py)

Exemplo comum:

```bash
log2timeline.py timeline.plaso /mnt/evidencia
```

Explicando:

* `timeline.plaso` → nome do arquivo de saída (banco de eventos)
* `/mnt/evidencia` → caminho onde estão os arquivos que você quer que ele analise

Dependendo do tamanho da evidência, isso pode demorar porque ele vai tentar extrair **todo evento possível**.

Você também pode apontar para um único arquivo:

```bash
log2timeline.py chrome.plaso /mnt/evidencia/Users/vinicius/AppData/Local/Google/Chrome/User\ Data/Default
```

---

## 5. Filtrando e exportando (psort.py)

Depois que você tem o `.plaso`, usa o `psort.py` para transformar isso em algo que dá pra ler:

```bash
psort.py -o L2tcsv -w timeline.csv timeline.plaso
```

Explicando:

* `-o L2tcsv` → formato de saída (CSV próprio do plaso)
* `-w timeline.csv` → arquivo final que você vai abrir no Excel/LibreOffice
* `timeline.plaso` → o banco de eventos gerado antes

Você pode usar filtros de tempo:

```bash
psort.py -o L2tcsv -w timeline_14h.csv --slice "2025-11-07T14:00:00 2025-11-07T14:10:00" timeline.plaso
```

Assim você pega só o intervalo de interesse.

---

## 6. Por que usar plaso?

* porque ele **normaliza** várias fontes em uma linha do tempo única;
* porque ele já conhece muitos tipos de artefatos (não precisa reescrever parser);
* porque você pode dividir o trabalho: um analisa, outro só filtra;
* porque timeline é a forma mais fácil de contar a história de um incidente.

Em investigações de exfiltração, malware e “o que o usuário fez”, timeline é quase sempre a vista mais útil.

---

## 7. Cuidados e boas práticas

* rodar o log2timeline **sobre cópia** da evidência;
* guardar o `.plaso` junto do caso – ele é o “projeto” da timeline;
* registrar timezone da máquina analisada e do analista (diferença de fuso bagunça a timeline);
* se a imagem for muito grande, pode valer a pena rodar o log2timeline em partes;
* documentar a versão do plaso usada (parsers mudam com o tempo).

---

## 8. Integração com outros artefatos

Depois de ter o `timeline.csv`, você pode:

* cruzar com eventos Windows que você exportou (EvtxECmd);
* cruzar com horários que achou na memória (Volatility);
* procurar por nomes de arquivos que descobriu no Autopsy;
* procurar por horas de execução que viu no Registro.

A timeline vira o “eixo” da investigação.

---

## 9. Blocos operacionais

```text
comando: gerar timeline.plaso a partir de diretório montado

log2timeline.py timeline.plaso /mnt/evidencia
```

```text
comando: exportar para CSV

psort.py -o L2tcsv -w timeline.csv timeline.plaso
```

```text
comando: exportar só um intervalo de tempo

psort.py -o L2tcsv -w timeline_intervalo.csv --slice "2025-11-07T10:00:00 2025-11-07T12:00:00" timeline.plaso
```

---

## 10. Comparação rápida

* **plaso/log2timeline** – timeline unificada, muitos parsers, ótimo para exame completo.
* **Ferramentas de timeline do próprio Autopsy** – boas, mas focadas no caso dentro do Autopsy.
* **Timesketch** – pensado para análise colaborativa de timelines (veremos depois).
* **Scripts próprios** – úteis, mas dão mais trabalho porque você precisa parsear tudo manualmente.

O plaso é o que dá o “volume” de eventos. As outras ferramentas costumam consumir esse volume e mostrar de forma mais confortável.