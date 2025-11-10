---
title: "Visão Geral de Ferramentas"
tags: [ferramentas, dfir, aquisicao, analise, windows, rede, memoria, timeline]
status: rascunho
---

# Visão Geral de Ferramentas - Guia

Esta pasta reúne **ferramentas práticas** usadas ao longo do fluxo de forense digital e resposta a incidentes. A ideia não é só listar softwares, mas organizar **por fase de uso** e manter um padrão de documentação (conceito → quando usar → fluxo → comandos → boas práticas).

O objetivo é: se alguém abrir apenas esta pasta, consiga saber **qual ferramenta usar em cada momento** e onde estão os detalhes.

---

## 1. Organização da pasta

A pasta `04_Ferramentas` está dividida em:

1. **01_Aquisicao** – ferramentas para criar **cópia forense** ou obter mídia/memória:
   - `01_FTK_Imager.md`
   - `02_Guymager.md`
   - `03_dd_dc3dd.md`

2. **02_Analise_de_Disco_Imagem** – ferramentas para **abrir e examinar** imagens coletadas:
   - `01_Autopsy.md`
   - `02_Sleuth_Kit_TSK_CLI.md`
   - `03_Solucoes_Comerciais_XWays_Magnet.md`

3. **03_Registro_e_Artefatos_Windows** – ferramentas específicas para artefatos de **Windows** (Registro, logs, hives offline):
   - `01_RegRipper.md`
   - `02_regipy_regrippy.md`
   - `03_Registro_Offline_Registry_Explorer_chntpw_reged.md`
   - `04_Analise_de_Logs_Windows_Chainsaw_EvtxECmd.md`

4. **04_Memoria** – ferramentas para **análise de RAM** já coletada:
   - `01_Volatility.md`
   - `02_Rekall.md`

5. **05_Timeline_e_Correlacao** – para juntar tudo em **linha do tempo**:
   - `01_plaso_log2timeline.md`
   - `02_psort.md`
   - `03_Timesketch_Visao_Geral.md`

6. **06_Rede** – análise de **tráfego e pacotes**:
   - `01_Wireshark_para_Forense.md`
   - `02_tcpdump.md`
   - `03_NetworkMiner.md`

7. **07_Coleta_Rapida_Windows** – triagem rápida em host vivo:
   - `01_KAPE.md`

8. **08_Utilitarios_de_Apoio** – ferramentas que não fazem “forense sozinhas”, mas ajudam em quase tudo:
   - `01_Hashes_hashdeep_sha256sum_md5sum.md`
   - `02_CyberChef.md`
   - `03_ExifTool.md`
   - `04_Binwalk.md`
   - `05_SQLite_Browser.md`

---

## 2. Relação com o fluxo de um caso

Um caso típico pode seguir esta ordem:

1. **Coletar**  
   - disco/imagem → (FTK Imager, Guymager, dd/dc3dd)  
   - memória → (coleta anterior; análise depois com Volatility/Rekall)  
   - garantir integridade → (hashes)

2. **Montar e analisar a imagem**  
   - Autopsy / Sleuth Kit / solução comercial

3. **Extrair artefatos de Windows**  
   - Registro e hives → RegRipper, regipy, Registry Explorer  
   - Logs → Chainsaw, EvtxECmd (dentro do arquivo de logs)

4. **Analisar memória (se houver)**  
   - Volatility / Rekall

5. **Criar timeline unificada**  
   - plaso/log2timeline → psort → (opcional) Timesketch

6. **Correlacionar rede**  
   - Wireshark, tcpdump, NetworkMiner

7. **Triagem rápida em host vivo**  
   - KAPE

8. **Apoio e transformação**  
   - hashes, CyberChef, ExifTool, Binwalk, SQLite Browser

Este 00_Visao_Geral serve justamente para lembrar “em que momento uso o quê”.

---

## 3. Padrão dos arquivos desta pasta

Cada arquivo de ferramenta segue o mesmo modelo:

1. **frontmatter** com `title`, `tags`, `status`;
2. **descrição do objetivo** da ferramenta;
3. **quando usar** (cenários típicos);
4. **fluxo/passo a passo**;
5. **blocos operacionais** (comandos ou sequência de cliques);
6. **boas práticas**;
7. **comparação rápida** com ferramentas da mesma categoria.

Isso mantém o repositório consistente e facilita estudo e impressão.

---

## 4. Ferramentas por tipo de evidência

- **Disco / imagem**: FTK Imager, Guymager, dd/dc3dd, Autopsy, Sleuth Kit.
- **Windows (artefatos)**: RegRipper, regipy/regrippy, Registry Explorer, Chainsaw/EvtxECmd.
- **Memória**: Volatility, Rekall.
- **Rede / PCAP**: Wireshark, tcpdump, NetworkMiner.
- **Timeline**: plaso/log2timeline, psort, Timesketch.
- **Host vivo**: KAPE.
- **Apoio**: hashdeep/sha256sum/md5sum, CyberChef, ExifTool, Binwalk, SQLite Browser.

Se a dúvida for “tenho X, o que uso?”, basta achar a categoria.

---

## 5. Boas práticas gerais para uso de ferramentas

- **trabalhar sempre na cópia** da evidência;
- **registrar versão** da ferramenta usada (muda parser, muda saída);
- **salvar saída em arquivo** (CSV, TXT, JSON) e não depender só da tela;
- **calcular e registrar hash** dos arquivos criados (imagens, dumps, PCAPs, artefatos exportados);
- **manter timezone claro** ao gerar timeline ou extrair datas;
- **documentar o comando** que foi executado (ajuda a repetir e auditar).

---

## 6. Como estudar esta pasta

1. ler este `00_Visao_Geral_de_Ferramentas.md`;
2. ir para a área de interesse (ex.: “quero aprender aquisição” → `01_Aquisicao/...`);
3. seguir na ordem dos arquivos dentro da subpasta;
4. usar os blocos operacionais como “cola” na hora de fazer laboratório.

Assim o material fica navegável e qualquer pessoa consegue seguir o mesmo padrão.