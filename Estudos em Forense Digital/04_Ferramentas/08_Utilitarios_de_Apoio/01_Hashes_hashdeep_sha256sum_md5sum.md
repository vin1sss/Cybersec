---
title: "Hashes: hashdeep, sha256sum, md5sum"
tags: [ferramenta, integridade, hash, cadeia-de-custodia, validacao, forense]
status: rascunho
---

# Hashes: hashdeep, sha256sum, md5sum

Este documento descreve o uso de ferramentas de **cálculo e verificação de hash** em contexto forense, com foco em `sha256sum`, `md5sum` e `hashdeep`. O objetivo é garantir que **o arquivo que você está analisando é exatamente o mesmo que foi coletado**, mantendo a cadeia de custódia e permitindo reanálises futuras.

Você vai usar hash em quase tudo:
- imagem de disco adquirida;
- dump de memória;
- PCAP capturado;
- artefatos extraídos (ex.: um .exe do NetworkMiner);
- pastas inteiras de coleta (ex.: saída do KAPE).

---

## 1. Conceito

Um **hash criptográfico** é um valor calculado a partir do conteúdo de um arquivo. Se o arquivo mudar 1 byte, o hash muda. Em forense, a ideia é:

1. calcular o hash **logo após a aquisição**;
2. anotar o valor no relatório;
3. sempre que copiar/mover o arquivo, recalcular e comparar;
4. se os valores baterem, integridade preservada.

Algoritmos comuns:
- **SHA-256** (recomendado/atual)
- SHA-1 (ainda visto em materiais antigos)
- MD5 (muito comum em forense, mas mais fraco criptograficamente)

Para estudo e repositório, dá para mostrar os três, mas priorizar **SHA-256**.

---

## 2. Ferramentas básicas (Linux, macOS, WSL)

### 2.1 sha256sum

Calcula SHA-256 de um arquivo:

```bash
sha256sum caso01.img
````

Saída típica:

```text
d2b2b0b4210a0f5c8e9cf1b0f1f4f401f0a6b40c5be2f5...  caso01.img
```

Guarde essa linha. É ela que prova que o arquivo não mudou.

### 2.2 md5sum

Muito usada porque aparece em várias suítes forenses:

```bash
md5sum caso01.img
```

Você pode guardar SHA-256 **e** MD5 (duplo registro).

---

## 3. hashdeep (vários arquivos e verificação)

O **hashdeep** é útil quando você tem **muitos arquivos** (ex.: saída do KAPE, artefatos extraídos) e quer:

1. gerar uma lista de hashes;
2. depois verificar se todos continuam iguais.

### 3.1 Gerar a lista

```bash
hashdeep -r -l -o f -c sha256 -v ./coleta > hashes.txt
```

Explicando:

* `-r` → recursivo (desce pastas)
* `-l` → usar caminhos relativos
* `-o f` → “match on filename” na verificação
* `-c sha256` → usar SHA-256
* `./coleta` → pasta que você quer hashear
* `> hashes.txt` → salva a tabela de hashes

O `hashes.txt` vira o “manifesto” daquela coleta.

### 3.2 Verificar depois

Se você copiar essa pasta para outro lugar e quiser confirmar:

```bash
hashdeep -r -l -a -k hashes.txt ./coleta
```

* `-a` → modo audit
* `-k hashes.txt` → arquivo de referência

Se estiver tudo ok, ele confirma. Se algo mudou, ele mostra.

---

## 4. Quando calcular hash

* **Logo após aquisição de disco** (imagem RAW, E01)
  → anotar no relatório.
* **Logo após dump de memória**
  → para ter certeza de que o dump não se corrompeu.
* **Depois de extrair um arquivo de dentro de uma imagem/PCAP**
  → para identificar esse arquivo de forma única.
* **Antes de mandar arquivo para outro analista**
  → manda o hash junto.
* **Quando receber arquivo de outro analista**
  → compara com o hash que ele mandou.

Esse hábito garante rastreabilidade.

---

## 5. Formato de registro (sugestão)

Ao registrar no seu material/caso, use algo assim:

```text
Arquivo: caso01.img
Tamanho: 120,034,123,776 bytes
Hash (SHA-256): d2b2b0b4210a0f5c8e9cf1b0f1f4f401f0a6b40c5be2f5...
Hash (MD5):     6e6bc4e49dd477ebc98ef4046c067b5f
Data/hora: 2025-11-07 14:32:10 UTC-3
Ferramenta: sha256sum (Linux)
Operador: __________________
```

Assim qualquer pessoa consegue revalidar.

---

## 6. Blocos operacionais

```text
comando: calcular SHA-256 de uma imagem

sha256sum caso01.img > caso01.img.sha256
```

```text
comando: calcular MD5 e SHA-256 de um arquivo extraído

md5sum malware.exe > malware.hashes
sha256sum malware.exe >> malware.hashes
```

```text
comando: gerar manifesto de uma pasta inteira

hashdeep -r -l -c sha256 ./coleta > coleta_hashes.txt
```

```text
comando: verificar pasta contra manifesto

hashdeep -r -a -k coleta_hashes.txt ./coleta
```

---

## 7. Boas práticas

* **preferir SHA-256** para relatório principal;
* guardar o arquivo de hash (`.sha256`, `.md5`) na mesma pasta da evidência;
* registrar **data/hora e ferramenta** usada;
* se o hash não bater, **não seguir analisando** sem registrar o fato;
* ao enviar evidência para alguém, mandar o hash em canal separado (ex.: e-mail com o hash, evidência no storage).

---

## 8. Comparação rápida

* **sha256sum / md5sum** – simples, ótimo para 1 arquivo.
* **hashdeep** – ótimo para muitos arquivos e para verificação posterior.
* **suites forenses (FTK, EnCase, Autopsy)** – muitas já calculam o hash na aquisição; você pode registrar o valor que ela mostrou.
* **hashes inseguros (MD5)** – ainda usados por compatibilidade, mas não devem ser o único valor em laudos modernos.

O importante não é a ferramenta em si, e sim **que o valor calcule a partir do arquivo que você está guardando** e que esse valor seja registrado junto com a evidência.