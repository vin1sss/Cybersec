---
title: "SQLite Browser (análise de bases SQLite)"
tags: [ferramenta, sqlite, navegador, artefatos, utilitario, dfir]
status: rascunho
---

# SQLite Browser (análise de bases SQLite)

Este documento descreve o uso de um **visualizador/gerenciador de bancos SQLite** (como o “DB Browser for SQLite”) para analisar **artefatos forenses que são armazenados em SQLite**. Muitos aplicativos modernos usam SQLite como banco local: navegadores (Chrome/Edge/Firefox), mensageiros, apps de sincronização, histórico de downloads, cache de aplicativos, até alguns componentes do Windows. Por isso, em forense é comum extrair um `.db` / `.sqlite` / `.sqlite3` e precisar **ver o que tem dentro**.

O SQLite Browser é a forma mais simples de:
1. abrir o arquivo;
2. listar tabelas;
3. ver registros;
4. rodar consultas SQL;
5. exportar para CSV.

---

## 1. Por que isso é importante em forense

Porque muitos artefatos saem exatamente assim:

- histórico do Chrome: `History` (SQLite)
- downloads do Chrome: `History` / `Downloads`
- cookies: `Cookies`
- formulários: `Web Data`
- alguns apps de comunicação mantêm conversas em bases SQLite
- apps de sincronização (ex.: clientes de cloud) às vezes registram metadados em SQLite

Então o fluxo típico é:
- você extrai a pasta de perfil de usuário da imagem;
- encontra vários `.db`;
- abre no SQLite Browser e vê tabelas como `urls`, `visits`, `downloads`, etc.

---

## 2. Abrindo o arquivo

1. abrir o DB Browser for SQLite;
2. **Open Database**;
3. selecionar o arquivo `.db`/`.sqlite`/`.sqlite3`;
4. o programa vai mostrar as **tabelas** e **views** existentes.

A partir daí você pode navegar só clicando, sem precisar escrever SQL – mas saber o mínimo de SQL ajuda bastante.

---

## 3. Explorando tabelas comuns de navegador (exemplo)

Se você abriu o `History` do Chrome/Edge:

- tabela `urls` → lista de URLs visitadas (url, title, visit_count, last_visit_time)
- tabela `visits` → acessos a essas URLs, com timestamp
- tabela `downloads` → arquivos baixados
- tabela `keyword_search_terms` → pesquisas feitas

Muitas dessas colunas de data estão em **formato de tempo do Chrome** (WebKit time), então você converte depois (CyberChef, script, psort, etc.).

---

## 4. Executando consultas SQL

A aba **Execute SQL** (ou similar) permite rodar SELECTs. Por exemplo, para listar as últimas 50 URLs:

```sql
SELECT url, title, visit_count, last_visit_time
FROM urls
ORDER BY last_visit_time DESC
LIMIT 50;
````

Para um banco de downloads:

```sql
SELECT target_path, tab_url, start_time, received_bytes
FROM downloads
ORDER BY start_time DESC;
```

Assim você pode montar exatamente a visão que precisa para o caso.

---

## 5. Exportando para CSV

Quase sempre você vai querer colocar esses dados em outro lugar (timeline, Excel, relatório). O SQLite Browser permite:

* exportar uma tabela inteira;
* exportar o resultado de uma query.

Exemplo: depois de rodar o SELECT acima, clicar em **Export → CSV** e salvar como `downloads.csv`.

Depois você pode:

* importar no plaso/psort;
* cruzar com hashes;
* entregar para quem está montando a linha do tempo.

---

## 6. Cuidados com evidência

Lembrar que o SQLite **é editável**. Então:

* trabalhar sempre na **cópia** do arquivo `.db`;
* não salvar alterações no banco original;
* se o programa perguntar para “vacuum” ou “save”, cancelar;
* registrar o caminho original dentro da imagem de onde o `.db` veio.

Assim você garante que a evidência permanece íntegra.

---

## 7. Integração com outras ferramentas

* extração (Autopsy, TSK, KAPE) → você encontra os `.db`;
* **SQLite Browser** → você lê/consulta;
* **CyberChef** → converte timestamps que vieram esquisitos;
* **hashes** → você calcula e registra para garantir que o `.db` não mudou;
* timeline → você pega as datas e joga para a linha do tempo.

Ou seja, o SQLite Browser é o “visualizador” no meio do fluxo.

---

## 8. Blocos operacionais

```text
passo: abrir e listar tabelas

1. Open Database -> selecionar history (Chrome)
2. Ir em "Browse Data"
3. Selecionar tabela "urls"
4. Visualizar registros
```

```text
consulta: últimas 100 páginas visitadas

SELECT url, title, visit_count, last_visit_time
FROM urls
ORDER BY last_visit_time DESC
LIMIT 100;
```

```text
consulta: downloads recentes

SELECT target_path, tab_url, start_time, received_bytes
FROM downloads
ORDER BY start_time DESC;
```

```text
exportar:
1. Rodar a query
2. Export -> CSV
3. Salvar como downloads.csv
```

---

## 9. Boas práticas

* sempre trabalhar na cópia;
* registrar o caminho original dentro da imagem;
* exportar para CSV e anexar ao caso;
* converter timestamps antes de concluir qualquer coisa;
* se o app tiver várias versões de banco (ex.: `History`, `History-journal`), guardar todas.

---

## 10. Comparação rápida

* **SQLite Browser (DB Browser for SQLite)** – GUI simples, ótima para navegar e exportar.
* **sqlite3 (CLI)** – bom para automatizar e rodar scripts.
* **Autopsy** – já parseia alguns bancos para você, mas nem sempre mostra tudo; abrir no SQLite Browser dá visão completa.
* **CyberChef** – ajuda a converter campos, não a consultar.

Resumindo: o SQLite Browser é o “abridor de bancos de artefatos” – ideal para ver o que o aplicativo registrou e tirar exatamente as linhas que interessam para o seu caso.