---
title: "dd e dc3dd"
tags: [ferramenta, aquisicao, imagem-forense, dd, dc3dd, linux, hash]
status: rascunho
---

# dd e dc3dd

Este documento descreve o uso de **dd** e **dc3dd** para aquisição forense em ambientes Unix/Linux. São ferramentas de linha de comando que permitem criar cópias bit a bit de discos e partições. O `dc3dd` é uma versão estendida, criada pelo DoD Cyber Crime Center, que adiciona recursos úteis para forense, como cálculo de hash durante a cópia e melhor registro.

A lógica permanece a mesma dos outros métodos:

1. identificar corretamente a mídia de origem;
2. copiar setor a setor para um arquivo de imagem;
3. calcular e registrar hash;
4. documentar.

---

## 1. Conceito

- **dd**: utilitário clássico do Unix que copia dados de uma origem para um destino, podendo trabalhar em nível de dispositivo (ex.: `/dev/sda`).
- **dc3dd**: baseado em dd, mas com aprimoramentos para forense (hash embutido, logs, progressos).

Ambas as ferramentas trabalham em modo texto e, por isso, exigem atenção aos parâmetros para não sobrescrever a mídia errada.

---

## 2. Cenários de uso

Use dd ou dc3dd quando:

- estiver em ambiente Linux/Unix sem interface gráfica;
- estiver em live forense mínima (poucas ferramentas instaladas);
- precisar de script/automação de aquisição;
- quiser controle total sobre blocos, erros e buffer;
- o laboratório padroniza evidências em RAW.

Se desejar interface gráfica ou geração automática de E01, veja o documento do Guymager.

---

## 3. Identificando a mídia correta

Antes de rodar o comando, é essencial descobrir qual dispositivo é a **origem** (evidência) e qual é o **destino** (onde ficará o arquivo):

```bash
lsblk
# ou
sudo fdisk -l
````

Exemplo de saída onde o disco da evidência é `/dev/sdb`. Essa informação deve ser conferida com muito cuidado, pois inverter `if=` e `of=` destrói dados.

---

## 4. Aquisição com dd (básico)

Exemplo de comando padrão:

```bash
sudo dd if=/dev/sdb of=/evidencias/caso01.img bs=4M conv=sync,noerror status=progress
```

Explicação dos parâmetros:

* `if=` - input file - é a origem (disco ou partição) que será copiada;
* `of=` - output file - é o arquivo de imagem que será criado;
* `bs=4M` - tamanho do bloco de leitura/escrita (4 megabytes) para acelerar;
* `conv=sync,noerror` - continua mesmo se houver erro de leitura, preenchendo com zeros;
* `status=progress` - mostra o andamento (em versões mais novas do dd).

Ao término, terá sido criada uma imagem RAW (`.img` ou `.dd`) pronta para validação.

---

## 5. Aquisição com dc3dd (forense)

O `dc3dd` facilita o cálculo de hash durante o processo e gera log. Exemplo:

```bash
sudo dc3dd if=/dev/sdb of=/evidencias/caso01.img hash=sha256 log=/evidencias/caso01.log
```

Possibilidades úteis:

* `hash=md5` ou `hash=sha1` ou `hash=sha256`
* `hashlog=/caminho/arquivo.log` para registrar o hash
* `status=on` para exibir progresso

Isso reduz etapas, porque você já sai da aquisição com o hash calculado e registrado.

---

## 6. Validação da imagem

Se a aquisição foi feita com dd (sem hash embutido), calcule depois:

```bash
sha256sum /evidencias/caso01.img > /evidencias/caso01.img.sha256
```

Registrar:

* algoritmo usado;
* valor do hash;
* data/hora;
* tamanho do arquivo de imagem.

Se a mídia original puder ser lida sem riscos, também é possível calcular o hash dela e comparar com o da imagem.

---

## 7. Boas práticas e segurança

* sempre usar `sudo` ou rodar como root para ter acesso ao dispositivo inteiro;
* nunca montar a evidência em modo leitura-escrita;
* preferir destino em disco diferente do de origem;
* manter nomes claros: `2025-caso01-disco01.img`;
* guardar o arquivo de log (`dc3dd`) junto da imagem.

---

## 8. Montagem posterior da imagem

Depois de criada a imagem RAW, ela pode ser montada em modo somente leitura para análise:

```bash
sudo mkdir -p /mnt/caso01
sudo mount -o ro,loop,show_sys_files /evidencias/caso01.img /mnt/caso01
```

Assim é possível navegar pelo sistema de arquivos como se fosse o disco original.

---

## 9. Bloco operacional

```text
comando: aquisição com dd

1. Identificar o disco de origem: lsblk ou fdisk -l
2. Confirmar que o destino tem espaço suficiente
3. Executar:
   sudo dd if=/dev/sdX of=/evidencias/caso01.img bs=4M conv=sync,noerror status=progress
4. Aguardar término
5. Calcular hash da imagem:
   sha256sum /evidencias/caso01.img > /evidencias/caso01.img.sha256
6. Registrar data/hora e tamanho do arquivo
```

```text
comando: aquisição com dc3dd (com hash embutido)

1. Identificar o disco de origem
2. Executar:
   sudo dc3dd if=/dev/sdX of=/evidencias/caso01.img hash=sha256 log=/evidencias/caso01.log
3. Aguardar término
4. Conferir o log e guardar com a imagem
```

---

## 10. Comparação rápida

* **dd** - disponível em quase todo sistema Unix, simples, exige validação depois.
* **dc3dd** - focado em forense, já calcula hash e gera log.
* **Guymager** - GUI em Linux, bom para quem quer acompanhamento visual.
* **FTK Imager** - GUI em Windows, muito usado em laboratórios.

O princípio forense é o mesmo em todos: copiar tudo, validar, documentar.