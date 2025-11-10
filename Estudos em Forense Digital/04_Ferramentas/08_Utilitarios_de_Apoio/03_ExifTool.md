---
title: "ExifTool"
tags: [ferramenta, metadados, exif, imagem, pdf, utilitario, dfir]
status: rascunho
---

# ExifTool

Este documento apresenta o **ExifTool** como utilitário de apoio para **extração e análise de metadados** de arquivos. Ele é capaz de ler (e, fora de contexto forense, escrever) metadados de uma quantidade enorme de formatos: fotos (JPEG, PNG), vídeos, áudio, PDFs, documentos do Office, arquivos de celular, entre outros.

Em forense digital, o ExifTool é muito útil para responder coisas como:

- quando este arquivo foi criado/capturado?
- com qual dispositivo/modelo?
- havia coordenadas de GPS?
- qual software gerou/alterou o arquivo?
- há vestígios de edição (ex.: “Adobe Photoshop”)?

E principalmente: **os metadados batem com a história que está sendo contada?**

---

## 1. Conceito

Arquivos de mídia e documentos geralmente carregam metadados em padrões como EXIF, XMP e IPTC. Esses metadados podem conter:

- data/hora de criação e modificação;
- timezone;
- câmera / celular usado;
- coordenadas de GPS;
- nome do autor;
- software usado para editar;
- miniaturas internas.

O **ExifTool** lê tudo isso e imprime em texto, de forma padronizada.

---

## 2. Uso básico

Para ver todos os metadados que o ExifTool conseguir achar:

```bash
exiftool arquivo.jpg
````

Ele vai listar dezenas de campos, por exemplo:

```text
ExifTool Version Number         : 12.00
File Name                       : arquivo.jpg
File Modification Date/Time     : 2025:11:07 14:11:22-03:00
Make                            : Apple
Model                           : iPhone 13
Create Date                     : 2025:11:07 14:10:55
GPS Latitude                    : -23.5505
GPS Longitude                   : -46.6333
Software                        : Adobe Photoshop 25.0
...
```

Isso já dá MUITA informação.

---

## 3. Listando campos específicos

Se você quer só um campo (para automatizar ou para relatório), use `-<campo>`:

```bash
exiftool -DateTimeOriginal -CreateDate -ModifyDate foto.png
```

ou, para GPS:

```bash
exiftool -GPSLatitude -GPSLongitude -GPSCoordinate arquivo.jpg
```

Isso é ótimo para montar tabela de várias fotos.

---

## 4. Vários arquivos de uma vez

Você pode rodar o ExifTool sobre uma pasta inteira:

```bash
exiftool *.jpg
```

ou recursivo:

```bash
exiftool -r /caminho/para/midias
```

E pode pedir para gerar CSV:

```bash
exiftool -r -csv -DateTimeOriginal -Model -GPSLatitude -GPSLongitude ./midias > metadados.csv
```

Assim você abre no Excel e analisa.

---

## 5. Usos forenses comuns

1. **Confirmar data/hora de captura de foto**

   * comparar com a timeline (plaso/psort);
   * ver se alguém alterou só o nome do arquivo, mas não o EXIF.

2. **Identificar dispositivo**

   * modelo de câmera/celular;
   * às vezes número de série.

3. **Localização (GPS)**

   * ver se a foto foi tirada em determinado local;
   * confrontar com alegação de presença/ausência.

4. **Detectar edição**

   * campo `Software` mostrando “Photoshop”, “GIMP”, “Snapseed”;
   * isso ajuda a mostrar que a imagem não saiu direto da câmera.

5. **Metadados em PDFs e documentos**

   * autor, empresa, software de criação;
   * datas internas que não aparecem no sistema de arquivos.

---

## 6. Integração com o restante do exame

O ExifTool entra bem quando você já extraiu arquivos de:

* um PCAP (NetworkMiner → salvou imagem/doc);
* uma imagem de disco (Autopsy/TSK → exportou fotos);
* um celular/dispositivo (coleta e você quer ver GPS);
* um e-mail (anexo).

Você roda o ExifTool sobre esses itens e anexa o resultado ao caso. Se a data do EXIF divergir da data do sistema de arquivos, registre a discrepância.

---

## 7. Blocos operacionais

```text
comando: mostrar todos os metadados de uma imagem

exiftool foto.jpg
```

```text
comando: extrair só datas e dispositivo

exiftool -DateTimeOriginal -CreateDate -ModifyDate -Make -Model foto.jpg
```

```text
comando: gerar CSV de uma pasta de mídias

exiftool -r -csv -DateTimeOriginal -Model -GPSLatitude -GPSLongitude ./midias > midias.csv
```

```text
comando: ver metadados de um PDF

exiftool relatorio.pdf
```

---

## 8. Boas práticas

* trabalhar sempre na **cópia** do arquivo de mídia (ExifTool consegue escrever metadados, então não rode modos de escrita em evidência);
* registrar o comando usado e a versão do ExifTool;
* guardar a saída original (em `.txt` ou `.csv`) junto do caso;
* comparar datas EXIF com o fuso horário correto (às vezes a câmera estava com fuso antigo);
* quando encontrar GPS, converter para endereço apenas no relatório (não gravar no arquivo).

---

## 9. Comparação rápida

* **ExifTool** – leitura profunda e padronizada de metadados, via CLI, multiformato.
* **Ferramentas gráficas de preview** – mostram poucos campos.
* **CyberChef** – ajuda a transformar dados, mas não substitui leitura de EXIF.
* **Binwalk** (próximo arquivo) – mais para análise de binários/firmware.

Resumindo: o ExifTool é o que você usa para “tirar tudo que o arquivo sabe sobre si mesmo” e colocar no seu relatório.