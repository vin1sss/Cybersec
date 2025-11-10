---
title: "Timesketch - Visão Geral"
tags: [ferramenta, timeline, correlacao, timesketch, plaso, dfir]
status: rascunho
---

# Timesketch – visão geral

Este documento apresenta o **Timesketch** como solução para **analisar timelines forenses de forma interativa e colaborativa**. Ele não substitui o plaso: a ideia é que você gere a timeline com `log2timeline.py` → exporte com `psort.py` → e então **importe no Timesketch** para pesquisar, filtrar, marcar eventos e contar a história do incidente com mais conforto.

Use quando:
- a timeline ficou grande demais para usar só no CSV;
- mais de uma pessoa vai analisar o mesmo caso;
- você quer fazer buscas rápidas por termos, IPs, usuários, caminhos;
- você quer marcar eventos importantes e classificá-los.

---

## 1. Conceito

O Timesketch é uma aplicação web pensada para DFIR que:

- recebe uma ou mais timelines (geralmente geradas pelo plaso/psort);
- indexa esses eventos em um backend de busca (ex.: Elasticsearch);
- fornece uma interface para filtrar por tempo, termo, atributo;
- permite marcar (taggear) eventos importantes;
- permite escrever “stories” explicando o que aconteceu.

Assim, em vez de brigar com um CSV de centenas de milhares de linhas, você pesquisa, vê o evento, marca e segue.

---

## 2. Fluxo de trabalho com Timesketch

1. **Processar a evidência**  
   - usar `log2timeline.py` para gerar `timeline.plaso`.

2. **Exportar para formato aceito**  
   - usar `psort.py` para gerar um CSV/JSON:
     ```bash
     psort.py -o L2tcsv -w timeline.csv timeline.plaso
     ```

3. **Importar no Timesketch**  
   - via interface ou via linha de comando (timesketch-importer), apontando para o CSV gerado;
   - dar um nome para o sketch (o caso).

4. **Analisar na interface**  
   - filtrar por tempo;
   - buscar por “powershell.exe”, “.exe” em pasta incomum, IPs, domínios;
   - marcar eventos relevantes.

5. **Documentar**  
   - usar a área de “stories” ou um relatório externo, mas sempre referindo os IDs dos eventos marcados.

---

## 3. O que ele resolve

- **Volume**: timelines grandes ficam mais manuseáveis.
- **Velocidade**: buscas textuais rápidas (IP, usuário, caminho).
- **Colaboração**: vários analistas podem ver e marcar os mesmos eventos.
- **Contexto**: você vê o evento dentro da linha do tempo geral, não isolado.

Em incidentes de dias ou semanas, isso ajuda muito a não se perder.

---

## 4. Estrutura básica no Timesketch

Depois de importar, você tem:

- **Sketch**: é o “caso” – agrupa timelines e suas buscas.
- **Timelines**: cada arquivo que você importou (pode ter mais de um por caso).
- **Search / Filter**: onde você faz queries (por texto, por campo, por tempo).
- **Saved searches**: buscas que você salva para reutilizar.
- **Stories**: lugar para escrever a narrativa do que você achou.
- **Tags / labels**: marcadores que você coloca em eventos importantes (ex.: `execucao-suspeita`, `lateral-movement`, `exfil`).

---

## 5. Tipos de buscas comuns

Depois que a timeline está no Timesketch, costuma-se fazer buscas como:

- por processo/comando:
  - `"powershell.exe"`
  - `"cmd.exe /c"`
- por extensões:
  - `".ps1"`
  - `".exe"`
- por caminho:
  - `"C:\\Users\\"`
  - `"AppData\\Roaming"`
- por IP/domínio:
  - `"41.168.5.140"`
  - `"dropbox.com"`
- por nome de usuário:
  - `"vinicius"`

E sempre combinando com **intervalo de tempo** para focar no período do incidente.

---

## 6. Integração com o que já temos

- `04_Ferramentas/05_Timeline_e_Correlacao/01_plaso_log2timeline.md` → gera o `.plaso`.
- `04_Ferramentas/05_Timeline_e_Correlacao/02_psort.md` → gera o CSV.
- **Timesketch (este)** → dá uma interface boa para trabalhar esse CSV.

Ou seja, este arquivo fecha o ciclo de “timeline e correlação” que você colocou na árvore.

---

## 7. Blocos operacionais

```text
comando: gerar CSV para importar no Timesketch

psort.py -o L2tcsv -w timeline.csv timeline.plaso
````

```text
comando: importar (exemplo genérico do importer do timesketch)

timesketch_importer.py --sketch_id 1 --file timeline.csv --timeline_name "Timeline caso 01"
```

```text
passo: analisar na interface

1. Abrir o sketch
2. Definir intervalo de tempo
3. Buscar por termo (ex.: powershell.exe)
4. Taggear os eventos relevantes
5. Salvar a busca
```

---

## 8. Boas práticas

* dar nomes claros às timelines importadas (“disco-caso01”, “ram-caso01”);
* sempre importar com timezone correto (para não deslocar o incidente);
* salvar as buscas úteis (ex.: “execuções de powershell”, “downloads suspeitos”);
* usar tags padronizadas se mais de uma pessoa estiver analisando;
* documentar no relatório final a query que levou ao evento (isso mostra reprodutibilidade).

---

## 9. Comparação rápida

* **plaso/log2timeline** – coleta e parsing, gera o volume bruto.
* **psort** – transforma o bruto em CSV/JSON.
* **Timesketch** – interface para explorar, filtrar e colaborar.
* **Autopsy timeline** – boa para um caso único dentro do Autopsy, mas menos flexível para colaboração e para múltiplas timelines.

O Timesketch entra quando você quer ir além do CSV e transformar a timeline em uma ferramenta de investigação contínua.