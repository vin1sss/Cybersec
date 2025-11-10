---
title: "Imagem de Disco"
tags: [coleta, aquisicao, imagem-forense, disco, hash, integridade]
status: rascunho
---

# Imagem de Disco

Este documento descreve como **adquirir uma imagem forense de mídia não volátil** (HD, SSD, pendrive, cartão SD ou volume de VM) de forma íntegra, validável e aceitável em investigação. Ele também registra os **formatos de imagem mais usados**, o **fluxo de validação por hash** e os cuidados específicos com mídias modernas (SSD/TRIM). A ordem geral de um caso é:

1. preservar o que é volátil (memória);
2. em seguida adquirir a mídia não volátil (disco);
3. validar e documentar.

---

## 1. Conceito

Uma **imagem forense** é uma cópia bit a bit (sector-by-sector) de toda a mídia original, incluindo:

- espaço não alocado;
- slack space;
- partições ocultas;
- setores marcados como apagados.

O objetivo é permitir que toda a análise futura seja feita **na cópia**, mantendo o original guardado e imutável.

---

## 2. Formatos de arquivo de imagem forense

Ao criar a imagem, você pode escolher entre diferentes formatos. Cada laboratório costuma padronizar 1 ou 2.

1. **RAW (dd)**  
   - Cópia exata sem compressão.  
   - Suportado por praticamente todas as ferramentas.  
   - Produz arquivos grandes.  
   - Ideal quando se quer máxima compatibilidade.

2. **EnCase (E01)**  
   - Formato muito usado em ambientes periciais.  
   - Suporta compressão e divisão em partes (útil para discos grandes).  
   - Armazena metadados (hashes, notas).  
   - Bom para cadeia de custódia porque guarda mais contexto.

3. **AFF (Advanced Forensic Format)**  
   - Aberto.  
   - Suporta compressão, metadados e verificação de integridade.  
   - Boa opção quando não se quer depender de formato proprietário.

4. **Formatos proprietários de ferramentas**  
   - Algumas suítes (Cellebrite, Magnet, etc.) geram contêineres próprios que misturam dados brutos e relatórios.  
   - Use quando a análise posterior for obrigatoriamente nessa mesma suíte.

**Critério de escolha**: compatibilidade com as ferramentas que você (ou o laboratório) vai usar na fase de análise e exigências legais/contratuais do caso.

---

## 3. Fluxo padrão de aquisição de disco

1. **Preparar o ambiente de coleta**  
   - Estação forense limpa, atualizada e com ferramentas validadas;  
   - Mídia de destino formatada e vazia;  
   - Hora correta (NTP) para registro nos relatórios.

2. **Conectar o disco via bloqueador de escrita**  
   - Evita alterações de timestamps e de sistema de arquivos;  
   - Se não houver write blocker físico, usar montagem read-only no SO (menos ideal).

3. **Identificar a mídia**  
   - `fdisk -l`, `lsblk` (Linux) ou “Disk Management” (Windows) para ter certeza de qual disco será lido.

4. **Criar a imagem**  
   - Ferramentas típicas:
     - **Linux (raw)**:
       ```bash
       dd if=/dev/sdX of=/evidencias/caso01.img bs=4M conv=sync,noerror
       ```
       - `if=` é o disco de origem (NUNCA confundir com o de destino);
       - `of=` é o arquivo de imagem;
       - `conv=noerror` continua mesmo com erro de leitura;
       - `bs=4M` acelera o processo.
     - **dc3dd** (derivado do dd, com cálculo de hash embutido).
     - **Guymager** (GUI para Linux, gera E01/AFF, mostra progresso).
     - **FTK Imager** (Windows e Linux)  permite selecionar disco físico, escolher formato e já gerar hash.

5. **Calcular e registrar o hash**  
   - Pode ser feito durante (dc3dd) ou logo após:
     ```bash
     sha256sum /evidencias/caso01.img > caso01.img.sha256
     ```
   - Registrar também o hash do disco original se a ferramenta permitir.

6. **Documentar**  
   - Data/hora;
   - Operador;
   - Ferramenta e versão;
   - Número de série do disco;
   - Local de armazenamento da imagem.

---

## 4. Validação da imagem

Depois de criar a imagem, é obrigatório demonstrar que **ela é idêntica ao original**.

1. Escolha o algoritmo (MD5, SHA-1 ou, preferencialmente, **SHA-256**).
2. Calcule o hash do **disco original** (se o ambiente permitir) e o hash da **imagem criada**.
3. Compare os dois valores.
4. Registre no relatório:

   - algoritmo usado;
   - valor de hash da mídia original;
   - valor de hash da imagem;
   - data/hora;
   - ferramenta/host onde o cálculo foi feito.

Se os valores coincidirem, a integridade está preservada. Se não coincidirem, a imagem deve ser refeita ou o fato precisa ser explicado.

---

## 5. Considerações sobre SSD e TRIM

Em mídias SSD/NVMe:

- o comando **TRIM** faz o controlador apagar blocos que o sistema operacional marcou como livres;
- isso quer dizer que **evidências podem desaparecer com o tempo** mesmo sem uso ativo;
- portanto, **a aquisição deve ser feita o mais cedo possível**;
- sempre que der, desconectar o SSD e adquirir em estação própria, via write blocker que suporte o tipo de interface (SATA, NVMe).

Mesmo assim, faça o hash do que foi possível obter e registre a condição da mídia.

---

## 6. Montagem e análise da imagem

A aquisição termina quando o arquivo de imagem está criado, validado e documentado. A partir daí:

1. **Montar a imagem em modo somente leitura**  
   - Linux:
     ```bash
     mkdir /mnt/caso01
     mount -o ro,loop,show_sys_files caso01.img /mnt/caso01
     ```
   - ou usar Autopsy/The Sleuth Kit para “ingestar” a imagem.

2. **Explorar sistema de arquivos e artefatos**  
   - NTFS: MFT, $LogFile, USN Journal;  
   - ext4: inodes, journals, superblocos.

3. **Recuperar arquivos apagados**  
   - file carving (PhotoRec, Scalpel).

4. **Correlacionar timestamps**  
   - criação, modificação, acesso e alteração de metadados;
   - lembrar que formatos de timestamp podem precisar de conversão.

> Observação: a análise em si costuma ficar em outra pasta do cofre (ex.: `03_Analise_de_Sistemas`). Aqui documentamos **como chegar à imagem** pronta para ser analisada.

---

## 7. Relação com a coleta de memória

O material de referência ressalta que **a RAM deve ser coletada primeiro** porque é volátil (processos, conexões, chaves de criptografia e até dados de disco descriptografado podem estar só na memória).  
O fluxo recomendado é:

1. isolar o host;
2. capturar **memória volátil** (Magnet RAM Capturer, Belkasoft, FTK Imager, winpmem);
3. calcular hash do dump;
4. só depois partir para a **imagem de disco** descrita neste arquivo.

Registrar isso aqui evita que, no laboratório, alguém faça o inverso e perca evidência de RAM.

---

## 8. Documentação mínima (checklist)

- [ ] Identificação da mídia (marca/modelo/serial)
- [ ] Data e hora do início e término da aquisição
- [ ] Ferramenta usada e versão
- [ ] Caminho/nome do arquivo de imagem
- [ ] Formato gerado (RAW, E01, AFF…)
- [ ] Hash da mídia original (se aplicável)
- [ ] Hash do arquivo de imagem
- [ ] Nome e assinatura do operador
- [ ] Observações sobre erros de leitura, setores defeituosos ou TRIM ativo
- [ ] Local/etiqueta de armazenamento da imagem