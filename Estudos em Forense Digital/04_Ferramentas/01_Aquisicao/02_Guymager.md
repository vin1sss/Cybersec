---
title: "Guymager"
tags: [ferramenta, aquisicao, imagem-forense, guymager, linux, hash, integridade]
status: rascunho
---

# Guymager

Este documento descreve o uso do **Guymager** como ferramenta de aquisição forense em sistemas Linux. Ele é uma opção gráfica, rápida e estável para criar imagens de discos e mídias removíveis, com geração automática de hash e registro de informações da evidência. Segue o mesmo princípio dos demais métodos: copiar bit a bit, verificar e documentar.

A sequência geral de um caso permanece:

1. coletar o que é volátil (memória);
2. adquirir o disco/mídia não volátil;
3. validar por hash e registrar.

---

## 1. Conceito

O Guymager é um utilitário livre de aquisição forense que:

- realiza cópias forenses completas (RAW, EWF/E01);
- mostra progresso em tempo real (tamanho, velocidade, tempo estimado);
- gera hashes durante o processo (MD5, SHA-1);
- pode criar múltiplas cópias ao mesmo tempo;
- armazena metadados sobre a aquisição.

Por ser gráfico, é muito usado em laboratórios que padronizam Linux, mas querem evitar erros de linha de comando durante a coleta.

---

## 2. Cenários de uso

O Guymager é indicado quando:

- o ambiente de coleta é Linux;
- a equipe prefere interface gráfica em vez de `dd` ou `dc3dd`;
- é necessário gerar imagem em **E01** diretamente no Linux;
- o analista quer acompanhar taxa de leitura e possíveis erros;
- há necessidade de produzir relatório com dados da mídia.

Em ambientes Windows, costuma-se usar FTK Imager; em ambientes Linux sem interface, usa-se `dd`/`dc3dd`.

---

## 3. Pré-requisitos e boas práticas

- executar o Guymager com privilégios administrativos (root), para enxergar discos físicos;
- conectar a evidência por **write blocker** sempre que possível;
- garantir espaço livre suficiente no disco de destino;
- verificar o nome/dispositivo correto antes de iniciar (`/dev/sda`, `/dev/sdb`, etc.);
- manter o relógio e o timezone corretos para registro de data/hora.

---

## 4. Fluxo de aquisição no Guymager

1. **Iniciar o Guymager**
   - normalmente pela interface do sistema ou via terminal com `sudo guymager`.

2. **Identificar o dispositivo de origem**
   - a tela principal lista os discos detectados (modelo, tamanho);
   - conferir cuidadosamente qual é a evidência.

3. **Iniciar aquisição**
   - clicar com o botão direito sobre o disco desejado;
   - selecionar **Acquire image**.

4. **Configurar a aquisição**
   - escolher o **formato de saída**:
     - `EWF/E01` (EnCase) - comum e compacto;
     - `RAW` - compatível com praticamente tudo;
   - definir o **diretório de destino**;
   - definir o **nome do arquivo**;
   - selecionar os **hashes** que serão calculados (MD5, SHA-1);
   - preencher campos de caso/descrição se o laboratório exigir.

5. **Executar e acompanhar**
   - iniciar a aquisição;
   - observar progresso, velocidade e possíveis setores com erro;
   - ao final, o Guymager apresenta o resumo e os hashes.

6. **Salvar o relatório**
   - guardar o arquivo de log/relatório gerado junto da imagem.

---

## 5. Validação e hashes

O Guymager já calcula hashes durante a aquisição. Mesmo assim, é recomendável registrar explicitamente:

- algoritmo usado;
- valor do hash da imagem;
- data e hora do término;
- tamanho final do arquivo.

Se for necessário calcular novamente no shell:

```bash
sha256sum caso01-disco01.E01 > caso01-disco01.E01.sha256
````

Isso permite demonstrar que o arquivo de imagem permanece íntegro ao longo do tempo.

---

## 6. Observações sobre formatos

O Guymager trabalha bem com:

1. **RAW (.img/.dd)**

   * cópia direta, maior compatibilidade;
   * ocupa mais espaço.

2. **EWF/E01**

   * permite compressão;
   * armazena metadados;
   * muito aceito em ferramentas de análise.

A escolha deve considerar o fluxo posterior de análise e o espaço disponível.

---

## 7. Bloco operacional

```text
comando: aquisição com Guymager (Linux)

1. Conectar a mídia de evidência (preferir write blocker)
2. Abrir terminal: sudo guymager
3. Na lista, localizar o disco de origem
4. Botão direito -> Acquire image
5. Escolher formato (E01 ou RAW)
6. Definir diretório e nome do arquivo
7. Selecionar hashes (MD5/SHA1)
8. Iniciar aquisição e aguardar
9. Salvar o relatório ao final
```

---

## 8. Comparação rápida com outros métodos

* **Guymager** - GUI, Linux, mostra progresso, gera E01 e hashes automaticamente.
* **FTK Imager** - GUI, muito usado em Windows.
* **dd/dc3dd** - CLI, altamente flexível, ideal para automação e ambientes sem GUI.

Todos seguem o mesmo princípio forense: copiar o conteúdo inteiro, calcular hash e documentar.