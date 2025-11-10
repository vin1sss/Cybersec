---
title: "Imaging no Linux com dc3dd"
tags: [diversos, imaging, linux, dc3dd, hash, montagem]
status: rascunho
---

# Imaging no Linux com dc3dd

Este documento mostra um fluxo **prático** de como criar uma imagem forense de um disco/dispositivo em Linux usando o `dc3dd` (que é um `dd` melhorado para forense). Ele complementa o arquivo de “Imagem de Disco” e serve quando você está num host Linux, anexou um disco e quer:

1. identificar o dispositivo certo;
2. imagear com log;
3. calcular hash;
4. montar a imagem para conferir.

---

## 1. Por que dc3dd?

- tem barra de progresso;
- tem logging nativo;
- consegue calcular hash durante a aquisição;
- sintaxe parecida com `dd` (fácil de lembrar).

Se não tiver `dc3dd`, dá pra usar `dd`, mas aí você perde algumas comodidades.

---

## 2. Identificar o dispositivo

Primeiro, descobrir **qual** é o disco que você vai copiar. Em Linux:

```bash
lsblk -a
````

Isso lista todos os block devices (discos, partições, loops). Procure o que você acabou de anexar (pendrive, disco externo, loop de 1 GB, etc.).

Se for imagem anexada via loop:

```bash
sudo losetup -l
```

pra ver qual `/dev/loopX` está apontando para o arquivo.

Se quiser mais detalhes:

```bash
sudo blkid /dev/loop11
```

(ajusta o número) — isso te dá tipo de filesystem e UUID.

**Importante:** confirme 2 vezes o nome do dispositivo. Errar `if=` e `of=` é como formatar o disco errado.

---

## 3. Preparar o destino

Escolha uma pasta onde a imagem vai ficar. Ex.:

```bash
mkdir -p /evidencias/caso01
cd /evidencias/caso01
```

O destino **não** deve ser o mesmo disco de onde você está copiando, se puder evitar. Em laboratório, o ideal é outro disco, maior.

---

## 4. Rodar o dc3dd

Exemplo básico:

```bash
sudo dc3dd if=/dev/loop11 of=example1.img log=imaging_loop11.txt
```

Explicando:

* `if=` → *input file*, o dispositivo de origem (disco de evidência);
* `of=` → *output file*, a imagem que você está criando;
* `log=` → arquivo de log com tudo que aconteceu (guarda!).

Quando terminar, o `dc3dd` mostra algo como:

```text
device size: ...
1153433600 bytes ( 1.1 G ) copied ( 100% )
```

Guarde esse log junto da evidência (ex.: `01_brutos/`).

### 4.1. Com hash embutido

Se quiser já sair com hash:

```bash
sudo dc3dd if=/dev/loop11 of=example1.img hash=sha256 hlog=example1.hash.log log=imaging_loop11.txt
```

* `hash=sha256` → calcula SHA-256 durante a cópia
* `hlog=...` → salva o resultado do hash em arquivo separado

Isso facilita a documentação.

---

## 5. Conferir tamanho

Depois que terminar:

```bash
ls -alh example1.img
```

O tamanho tem que ser igual ao do dispositivo de origem. Se o disco tinha 1,1 GB, a imagem deve ter ~1,1 GB.

---

## 6. Verificar integridade (hash)

Mesmo tendo usado hash no dc3dd, dá pra (e é bom) recalcular:

```bash
sudo sha256sum example1.img > example1.img.sha256
```

Se o dispositivo ainda estiver anexado, você pode comparar direto com o device:

```bash
sudo sha256sum /dev/loop11
```

Os valores precisam ser iguais. Isso vira sua prova de integridade.

Se sua organização ainda usa MD5/MD5+SHA-1, dá pra fazer:

```bash
sudo md5sum example1.img > example1.img.md5
```

---

## 7. Montar a imagem (validação)

Montar é só pra conferir que a imagem está boa:

```bash
sudo mkdir -p /mnt/example1
sudo mount -o loop,ro example1.img /mnt/example1
ls /mnt/example1
```

* `-o loop` → monta arquivo como se fosse disco
* `ro` → read-only, pra não alterar nada dentro da imagem

Se listar diretórios/arquivos, a imagem ficou ok.

Quando terminar:

```bash
sudo umount /mnt/example1
```

---

## 8. Onde guardar o que foi gerado

Organize assim:

```text
caso_2025-11-07_incidente/
  01_brutos/
    example1.img
    example1.img.sha256
    imaging_loop11.txt
    example1.hash.log
```

O log de aquisição (`imaging_loop11.txt`) é tão importante quanto a imagem, porque mostra *como* você fez.

---

## 9. Boas práticas rápidas

* usar **sudo** sempre que for ler o dispositivo;
* nunca rodar `dc3dd` sem ter certeza do `if=`;
* sempre ter **mais espaço** do que o tamanho do disco;
* guardar o log junto da evidência;
* registrar data/hora, operador e versão do `dc3dd`.

---

## 10. Relacionados

* visão geral de imagem → `02_Coleta_de_Evidencias/01_Imagem_de_Disco.md`
* cold forensics e ordem de coleta → `06_Diversos/02_Cold_Forensics_e_Ordem_de_Volatilidade.md`
* documentação e cadeia → `05_Procedimentos/06_Documentacao_e_Cadeia_de_Custodia.md`