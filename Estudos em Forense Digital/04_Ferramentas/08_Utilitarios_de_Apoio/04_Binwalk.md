---
title: "Binwalk"
tags: [ferramenta, utilitario, binario, firmware, extracao, carving, dfir]
status: rascunho
---

# Binwalk

Este documento apresenta o **Binwalk** como utilitário de apoio para **inspeção e extração de conteúdo escondido/embutido em arquivos binários**. Ele é muito conhecido por análise de **firmware** (roteadores, IoT, appliances), mas também é útil em forense quando você desconfia que:

- um arquivo tem **vários formatos colados** (zip dentro de binário, imagem dentro de outro arquivo);
- alguém **embutiu dados** dentro de um executável;
- você recebeu um “dump” de dispositivo e quer ver **o que tem lá dentro**;
- precisa fazer **carving automático** de trechos reconhecidos por assinatura.

O Binwalk procura **assinaturas** de formatos conhecidos ao longo do arquivo e pode **extrair** automaticamente o que encontrar.

---

## 1. Conceito

Arquivos binários grandes (firmware, dumps, imagens de equipamento) muitas vezes são, na verdade, **pacotes de várias coisas**: sistema de arquivos, kernel, imagens, configurações, compressões. O Binwalk lê o binário e tenta identificar:

- cabeçalhos de arquivos comprimidos (gzip, lzma, xz);
- sistemas de arquivos embutidos (SquashFS, JFFS2, cramfs);
- imagens (PNG, JPEG);
- scripts;
- assinaturas comuns.

Depois ele pode **extrair** esses pedaços para você analisar separadamente.

---

## 2. Uso básico: identificar o que existe

Para apenas **analisar** o conteúdo de um binário:

```bash
binwalk firmware.bin
````

Saída típica (exemplo):

```text
DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             uImage header, header size: 64 bytes, Linux ARM
64            0x40            gzip compressed data, from Unix, last modified...
1052672       0x101000        Squashfs filesystem, little endian, version 4.0...
```

Isso já te diz que:

* o arquivo tem um header;
* tem uma parte gzip;
* tem um sistema de arquivos SquashFS lá dentro.

---

## 3. Extraindo automaticamente

Se você quer **tirar para fora** tudo que o Binwalk encontrar:

```bash
binwalk -e firmware.bin
```

O `-e` (ou `--extract`) cria uma pasta (geralmente `_<nome do arquivo>.extracted/`) com os arquivos extraídos.

Depois você entra nessa pasta e analisa com outras ferramentas (por exemplo, montar o SquashFS).

Você também pode pedir para usar o “modo inteligente” de extração:

```bash
binwalk -Me firmware.bin
```

* `-M` → recursivo (se dentro tinha outro binário, ele tenta extrair também)
* `-e` → extrair

Cuidado: isso pode gerar MUITOS arquivos, mas é ótimo para varrer tudo.

---

## 4. Quando isso é útil em forense

* **Análise de firmware de roteador/equipamento**: você recebeu o firmware que estava em uso e quer ver scripts, configurações padrão, credenciais hardcoded.
* **Arquivos suspeitos com dados embutidos**: recebeu um `.jpg` grande demais → roda o Binwalk para ver se tem um zip dentro.
* **Capturas de dispositivos IoT**: às vezes o dump vem todo misturado → Binwalk ajuda a separar.
* **Engenharia reversa básica**: descobrir qual sistema de arquivos está dentro de uma imagem binária.

---

## 5. Inspeção de entropia

O Binwalk pode mostrar **entropia** do arquivo, o que ajuda a ver se há partes comprimidas ou criptografadas:

```bash
binwalk --entropy firmware.bin
```

Isso desenha um gráfico (em texto/ASCII ou gerando imagem) mostrando onde o arquivo tem mais “bagunça”. Trechos muito “lisos” podem ser compressão ou criptografia.

Útil para:

* saber onde focar a extração;
* perceber que parte do arquivo está protegida e talvez não seja extraível de forma simples.

---

## 6. Integração com outras ferramentas

Depois que o Binwalk extrair:

* se for **SquashFS/JFFS2** → montar em Linux (`unsquashfs`, etc.) e explorar o sistema de arquivos;
* se forem **imagens** → rodar **ExifTool** para metadados;
* se forem **scripts/configs** → abrir no editor e procurar credenciais, IPs, domínios;
* se encontrar **binários** → calcular hash (sha256sum) e enviar para análise.

Ou seja, o Binwalk faz o “desembrulho”, e outras ferramentas fazem a análise.

---

## 7. Blocos operacionais

```text
comando: identificar componentes de um binário

binwalk arquivo.bin
```

```text
comando: extrair tudo que reconhecer

binwalk -e arquivo.bin
```

```text
comando: extrair recursivamente (profundo)

binwalk -Me arquivo.bin
```

```text
comando: analisar entropia

binwalk --entropy arquivo.bin
```

---

## 8. Boas práticas

* trabalhar sempre em **cópia** do arquivo original;
* rodar o Binwalk em pasta separada (ele cria subpastas com muitos arquivos);
* registrar quais opções foram usadas (`-e`, `-M`, `--entropy`);
* calcular hash dos artefatos relevantes extraídos;
* se o arquivo for muito grande, avaliar se vale extrair recursivamente (pode demorar e gerar muito output);
* documentar o que foi encontrado (ex.: “imagem continha SquashFS versão X, com diretório /etc contendo credenciais”).

---

## 9. Comparação rápida

* **Binwalk** – detecta e extrai partes de arquivos binários com base em assinaturas; ótimo para firmware e arquivos “misturados”.
* **foremost/scalpel** – carving mais genérico baseado em cabeçalho/rodapé de arquivos.
* **7zip/unzip/tar** – extraem só o que já é claramente um formato de arquivo compactado.
* **ExifTool** – extrai metadados, não faz carving.
* **CyberChef** – pode ajudar a decodificar partes, mas não substitui a varredura por assinaturas do Binwalk.

O Binwalk é o que você usa quando desconfia que “tem mais coisa dentro desse arquivo do que ele está mostrando”.