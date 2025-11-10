---
title: "FTK Imager"
tags: [ferramenta, aquisicao, imagem-forense, ftk-imager, hash, integridade]
status: rascunho
---

# FTK Imager

Este documento apresenta o uso do **FTK Imager** como ferramenta de aquisição forense para criar imagens de discos, partições ou mídias removíveis, com geração de hash e relatório. Ele complementa o estudo sobre imagem de disco e mostra como fazer o mesmo processo em uma interface gráfica amplamente utilizada em laboratórios.

A sequência recomendada em um caso é:

1. preservar memória (volátil);
2. adquirir o disco ou mídia não volátil;
3. validar com hash e documentar.

---

## 1. Conceito

O FTK Imager é uma ferramenta de aquisição e visualização que permite:

- criar cópias bit a bit em formatos forenses (RAW, E01);
- registrar informações da mídia e do processo;
- calcular hashes para comprovar integridade;
- montar uma imagem já existente e extrair arquivos.

Ele é focado na etapa de preservação. A análise detalhada costuma ser feita depois, em ferramentas como Autopsy, The Sleuth Kit ou soluções comerciais.

---

## 2. Cenários de uso

Use o FTK Imager quando for necessário:

- adquirir uma mídia em ambiente Windows de forma rápida e visual;
- gerar relatório automático do processo;
- preservar um pendrive ou HD recebido como evidência;
- montar uma imagem já existente para consultar o conteúdo;
- trabalhar com formatos amplamente aceitos (E01 e RAW).

Quando o ambiente for predominantemente Linux, é comum usar ferramentas como Guymager ou dd/dc3dd, que seguem o mesmo princípio de cópia bit a bit.

---

## 3. Cuidados antes da aquisição

- sempre que possível, conectar a mídia por meio de bloqueio de escrita (write blocker);
- trabalhar em estação dedicada à forense, atualizada e com hora correta;
- identificar claramente qual disco será adquirido para evitar imagem do disco errado;
- garantir que há espaço suficiente na mídia de destino para receber a imagem;
- registrar data, hora e identificação da evidência antes de iniciar.

---

## 4. Fluxo de aquisição no FTK Imager

1. Abrir o FTK Imager.
2. Acessar o menu `File` - `Create Disk Image`.
3. Escolher o tipo de origem:
   - `Physical Drive` para adquirir o disco inteiro;
   - `Logical Drive` para adquirir apenas uma partição.
4. Selecionar a mídia correta na lista.
5. Escolher o formato de saída:
   - **E01** - formato forense comum, com suporte a compressão e metadados;
   - **RAW (dd)** - cópia direta e compatível com diversas ferramentas.
6. Definir o diretório de destino e o nome do arquivo de imagem.
7. Marcar a opção de cálculo de hash (MD5 e/ou SHA1).
8. Confirmar e iniciar a aquisição.
9. Ao término, salvar o relatório gerado pelo FTK Imager.

Esse relatório contém as informações necessárias para comprovar o procedimento realizado.

---

## 5. Validação da imagem

Mesmo que o FTK Imager calcule hash durante o processo, é boa prática registrar isso de forma explícita:

1. observar qual algoritmo foi usado (MD5, SHA-1);
2. anotar o valor de hash da imagem gerada;
3. registrar data e hora do término;
4. guardar o relatório junto da imagem.

Se houver exigência de SHA-256, o valor pode ser calculado depois, no sistema operacional:

```bash
sha256sum nome-da-imagem.E01 > nome-da-imagem.E01.sha256
````

O objetivo é sempre mostrar que a imagem não foi alterada após a aquisição.

---

## 6. Montagem e extração

O FTK Imager também pode ser usado para inspecionar rapidamente o conteúdo de uma imagem:

1. abrir o programa;
2. ir em `File` - `Add Evidence Item`;
3. selecionar `Image File`;
4. apontar para o arquivo `.E01` ou `.dd`;
5. navegar pela árvore de diretórios exibida;
6. exportar arquivos ou pastas específicas.

Esse recurso é útil para visualizar o conteúdo sem alterar a imagem original.

---

## 7. Bloco operacional

```text
comando: aquisição com FTK Imager

1. Abrir FTK Imager
2. File -> Create Disk Image
3. Selecionar Physical Drive (ou Logical Drive)
4. Escolher formato E01
5. Definir caminho e nome do arquivo
6. Marcar cálculo de hash
7. Iniciar aquisição
8. Salvar o relatório ao final
```

---

## 8. Relação com outros métodos de aquisição

* FTK Imager - indicado para ambientes Windows e para quem precisa de relatório automático.
* Guymager - indicado para ambientes Linux com interface gráfica.
* dd/dc3dd - indicado para aquisição por linha de comando, scripts e ambientes de resposta.
* Ferramentas comerciais - usadas quando o laboratório já possui suíte integrada.

A escolha da ferramenta não altera o princípio forense: cópia bit a bit, hash e documentação.