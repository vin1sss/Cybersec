---
title: "psort (análise e exportação de timelines plaso)"
tags: [ferramenta, timeline, correlacao, plaso, psort]
status: rascunho
---

# psort (análise e exportação de timelines plaso)

Este documento descreve o uso do **psort.py**, o componente do plaso responsável por **ler o arquivo .plaso já processado** e **exportar** os eventos em formatos úteis (CSV, JSON, bodyfile, etc.), permitindo também aplicar filtros de tempo, de fonte e de tipo de evento. Ele é a segunda etapa natural depois do `log2timeline.py`.

Fluxo completo de timeline com plaso:

1. `log2timeline.py` → lê a evidência e gera `timeline.plaso`;
2. `psort.py` → lê `timeline.plaso` e gera `timeline.csv` (ou outro formato);
3. analista → abre o CSV, filtra, correlaciona com outras evidências.

---

## 1. Pré-requisito

Para usar o `psort.py` você **já precisa ter** um arquivo `.plaso` gerado. Exemplo:

```bash
log2timeline.py timeline.plaso /mnt/evidencia
````

Depois disso é que você entra com o `psort.py`.

---

## 2. Uso básico

O uso mais comum é:

```bash
psort.py -o L2tcsv -w timeline.csv timeline.plaso
```

Explicando:

* `-o L2tcsv` → formato de saída (CSV no padrão do plaso);
* `-w timeline.csv` → arquivo a ser escrito;
* `timeline.plaso` → arquivo de entrada (o “banco” de eventos).

Esse comando cria um CSV com **todos** os eventos que o plaso encontrou, ordenados no tempo.

---

## 3. Filtrando por intervalo de tempo

Timelines costumam ficar enormes. O psort permite recortar só o pedaço que interessa, por exemplo, o intervalo do incidente:

```bash
psort.py -o L2tcsv \
  -w timeline_incidente.csv \
  --slice "2025-11-07T14:00:00 2025-11-07T14:20:00" \
  timeline.plaso
```

* `--slice` recebe “início fim” em formato ISO.
* Isso reduz muito o volume e facilita a leitura.

---

## 4. Filtrando por tipo/fonte de evento

Dependendo da versão, o psort aceita filtros mais avançados (expressões de filtro do plaso). Um uso típico é separar só eventos de navegador, ou só de log do Windows. Exemplo ilustrativo:

```bash
psort.py -o L2tcsv \
  -w timeline_web.csv \
  --filter "data_type contains 'webhist'" \
  timeline.plaso
```

A ideia é: o plaso já classificou o evento; o psort só vai mostrar os que batem com o filtro.

---

## 5. Formatos de saída

Além de `L2tcsv`, é comum usar:

* `jsonl` → bom para importar em outras ferramentas;
* `dynamic` → saída mais genérica;
* `body` → bodyfile, caso você queira usar em ferramentas de timeline que entendem esse formato.

Exemplo em JSON:

```bash
psort.py -o jsonl -w timeline.jsonl timeline.plaso
```

---

## 6. Integração com o restante do exame

Depois de ter o CSV/JSON do psort, você pode:

* abrir no Excel/LibreOffice e filtrar por hora, usuário, caminho;
* juntar com eventos do Windows exportados por EvtxECmd;
* cruzar com horário de execução de malware visto no Volatility;
* usar como base para montar o relatório narrativo (“às 14:03 o usuário abriu...”).

O psort é, portanto, a ponte entre o processamento pesado do plaso e o analista humano.

---

## 7. Blocos operacionais

```text
comando: exportar toda a timeline para CSV

psort.py -o L2tcsv -w timeline.csv timeline.plaso
```

```text
comando: exportar só o intervalo do incidente

psort.py -o L2tcsv -w incidente.csv --slice "2025-11-07T10:00:00 2025-11-07T10:30:00" timeline.plaso
```

```text
comando: exportar em JSON

psort.py -o jsonl -w timeline.jsonl timeline.plaso
```

---

## 8. Boas práticas

* sempre manter o `.plaso` original guardado – o psort pode ser rodado de novo com outros filtros;
* anotar o timezone usado para não cometer erro de hora;
* nomear os CSVs pelo intervalo ou foco (ex.: `timeline_14-15.csv`, `timeline_web.csv`);
* documentar a versão do plaso/psort usada.

---

## 9. Comparação rápida

* **psort.py** – leitura e exportação do que o plaso já processou; ideal para o analista.
* **log2timeline.py** – coleta e parsing; mais pesado.
* **Timesketch** – consome timelines (inclusive as geradas com psort) e dá interface colaborativa e de busca.

O psort fica no meio: não é o parser pesado e não é a interface final, é o “organizador” dos eventos.