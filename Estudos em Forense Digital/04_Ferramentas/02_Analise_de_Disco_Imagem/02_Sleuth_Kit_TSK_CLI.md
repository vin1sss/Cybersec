---
title: "The Sleuth Kit (TSK) - CLI"
tags: [ferramenta, analise, imagem-forense, tsk, linha-de-comando, filesystem, artefatos]
status: rascunho
---

# The Sleuth Kit (TSK) - CLI

Este documento apresenta o uso do **The Sleuth Kit (TSK)** pela linha de comando para analisar imagens forenses de disco. O TSK é o conjunto de ferramentas sobre o qual o Autopsy se apoia. Aqui você faz manualmente, comando por comando, o mesmo tipo de exame que o Autopsy faz de forma gráfica.

A vantagem do TSK é que ele:
- funciona bem em ambientes sem interface gráfica;
- permite automação e scripts;
- dá controle detalhado sobre volumes, partições e arquivos;
- é excelente para laboratórios que querem registrar exatamente quais comandos foram executados.

Fluxo recomendado:
1. adquirir e validar a imagem (RAW, E01 convertido);
2. identificar partições e sistemas de arquivos;
3. explorar o sistema de arquivos;
4. extrair arquivos ou metadados de interesse;
5. registrar comandos e saídas.

---

## 1. Conceito

O TSK é uma coleção de utilitários que operam sobre imagens de disco em modo somente leitura. Cada utilitário tem uma função bem específica:
- um lista arquivos;
- outro imprime metadados;
- outro extrai o conteúdo de um arquivo;
- outro mostra a tabela de partições;
- e assim por diante.

A lógica é sempre:
1. apontar para a imagem;
2. informar o offset (se houver partições);
3. executar o comando certo para aquele tipo de informação.

---

## 2. Principais comandos do TSK

Vamos ver os mais usados em análise de disco:

- `mmls` - lista partições e seus offsets
- `fsstat` - mostra informações do sistema de arquivos
- `fls` - lista arquivos e diretórios
- `istat` - mostra metadados de um arquivo (por inodo)
- `icat` - extrai o conteúdo de um arquivo (por inodo)
- `img_stat` - exibe informações gerais da imagem
- `tsk_recover` - recupera arquivos apagados para um diretório

Esses comandos, combinados, já resolvem boa parte das análises de sistemas de arquivos.

---

## 3. Inspeção inicial da imagem

Antes de sair listando arquivos, descubra o que a imagem tem dentro. Para isso:

```bash
img_stat caso01.img
````

Esse comando mostra:

* tamanho da imagem
* setores
* tipo de imagem
* se há tabela de partições

Se a imagem foi o disco inteiro, o próximo passo é identificar partições.

---

## 4. Descobrindo partições com mmls

```bash
mmls caso01.img
```

Saída típica:

```text
DOS Partition Table
Offset Sector: 0
Units are in 512-byte sectors

     Slot    Start        End          Length       Description
00:  -----   0000000000   0000002047   0000002048   Primary Table (#0)
01:  000:    0000002048   0002068479   0002066432   NTFS / exFAT (0x07)
...
```

O que importa aqui:

* a partição útil (ex.: NTFS) tem um setor de início (Start);
* você precisa desse valor para dizer aos outros comandos onde o sistema de arquivos começa;
* esse valor é o offset em setores.

Se o sistema de arquivos começa no setor 2048, todos os comandos que acessam o FS precisam desse offset.

---

## 5. Obtendo informações do sistema de arquivos com fsstat

```bash
fsstat -o 2048 caso01.img
```

Explicação:

* `-o 2048` - offset em setores onde o FS começa (vindo do `mmls`);
* `fsstat` mostra tipo de FS, tamanhos, times, estrutura de diretórios, etc.

Isso confirma se o FS é mesmo NTFS, FAT, ext, e se os timestamps fazem sentido para o caso.

---

## 6. Listando arquivos e diretórios com fls

O comando `fls` é o equivalente em CLI ao "navegar pelo disco". Ele lista entradas de diretórios, inclusive apagadas.

```bash
fls -o 2048 -r caso01.img
```

Opções úteis:

* `-o 2048` - offset do FS
* `-r` - recursivo (desce subpastas)
* `-m /` - monta um prefixo de caminho para facilitar leitura
* `-d` - lista apenas diretórios
* `-f ntfs` - força o tipo de FS

Exemplo mais legível:

```bash
fls -o 2048 -r -m / caso01.img
```

A saída vai mostrar algo como:

```text
d/d 5: /Users
r/r 128-128-3: /Users/vinicius/desktop.txt
```

* `d/d` - diretório
* `r/r` - arquivo regular
* número - inodo/ID que você usa depois no `istat` e `icat`

---

## 7. Vendo metadados de um arquivo com istat

Se em `fls` você viu um arquivo interessante com inodo 128, pode pedir os metadados:

```bash
istat -o 2048 caso01.img 128
```

Isso mostra:

* tamanho
* timestamps (criação, modificação, acesso)
* atributos do arquivo
* onde os dados estão no disco

É útil para montar linha do tempo manualmente ou confirmar se o arquivo foi alterado perto do horário do incidente.

---

## 8. Extraindo o conteúdo de um arquivo com icat

Para pegar o arquivo inteiro:

```bash
icat -o 2048 caso01.img 128 > desktop.txt
```

Explicação:

* `icat` lê o conteúdo do arquivo referenciado pelo inodo;
* redirecionamos para um arquivo real (`> desktop.txt`);
* isso não altera a imagem original.

É assim que você "tira" um arquivo de dentro de uma imagem sem precisar montar o FS.

---

## 9. Recuperando arquivos apagados

O TSK também pode recuperar o que estiver marcado como apagado (dependendo do FS e da condição dos blocos):

```bash
tsk_recover -o 2048 caso01.img ./recuperados
```

* `./recuperados` é o diretório onde os arquivos serão salvos
* o comando varre o FS e extrai o que for possível

Isso é útil quando o caso envolve exclusão de arquivos e você quer um dump rápido do que ainda está recuperável.

---

## 10. Trabalhando com imagens E01

Alguns ambientes usam imagem EnCase (`.E01`). O TSK consegue trabalhar com elas se o suporte estiver compilado, mas uma abordagem comum é converter antes para RAW com outras ferramentas e então usar o TSK. A lógica do TSK não muda: você sempre aponta para a imagem e usa offset.

---

## 11. Exemplo de fluxo completo

1. verificar a imagem:

   ```bash
   img_stat caso01.img
   ```
2. encontrar partições:

   ```bash
   mmls caso01.img
   ```

   * anotar o setor de início, por exemplo 2048
3. ver detalhes do FS:

   ```bash
   fsstat -o 2048 caso01.img
   ```
4. listar arquivos recursivamente:

   ```bash
   fls -o 2048 -r -m / caso01.img > arvore.txt
   ```
5. procurar, no `arvore.txt`, o arquivo de interesse e anotar o inodo
6. ver metadados:

   ```bash
   istat -o 2048 caso01.img <inodo>
   ```
7. extrair:

   ```bash
   icat -o 2048 caso01.img <inodo> > arquivo_extraido.bin
   ```

---

## 12. Blocos operacionais

```text
comando: identificar partições na imagem

mmls caminho/para/imagem.img
# anotar o setor (Start) da partição de interesse
```

```text
comando: listar arquivos de forma recursiva

fls -o <offset> -r -m / caminho/para/imagem.img
```

```text
comando: extrair arquivo por inodo

icat -o <offset> caminho/para/imagem.img <inodo> > nome-do-arquivo
```

```text
comando: recuperar arquivos apagados

tsk_recover -o <offset> caminho/para/imagem.img ./saída
```

---

## 13. Boas práticas

* sempre trabalhar sobre uma cópia da imagem;
* salvar a saída dos comandos (redirecionar para `.txt`) para documentar o exame;
* anotar o offset usado - muitos erros vêm de usar o offset errado;
* combinar TSK com outras ferramentas de artefatos (RegRipper, Chainsaw) quando o caso for Windows;
* quando o FS for NTFS e houver timestamps suspeitos, guardar a saída do `istat` como evidência.

---

## 14. Comparação rápida

* Autopsy: faz tudo isso de forma gráfica, com ingest e relatório.
* TSK (CLI): faz o mesmo, porém manual, ideal para automação, ambientes mínimos e documentação passo a passo.
* Ferramentas comerciais: podem ter parseadores adicionais, mas seguem o mesmo princípio de apontar para a imagem e reconstruir o FS.