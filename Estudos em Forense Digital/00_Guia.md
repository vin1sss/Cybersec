# Guia Geral - Repositório de Estudos em Forense Digital

Bem-vindo ao repositório de estudos em Forense Digital!
Este guia é o ponto de entrada para entender tudo o que existe aqui dentro e onde cada coisa fica. 

* Se você está começando: vá para Introdução.
* Se precisa aprender a coletar evidências: vá para Coleta de Evidências.
* Se vai analisar Windows, Linux ou macOS: vá para Análise de Sistemas.
* Se quer saber qual ferramenta usar: vá para Ferramentas.
* Se precisa de passo a passo pronto: vá para Procedimentos.
* Se quer exercícios e laboratórios: vá para Práticas.

Este repositório representa um conteúdo completo de estudo em forense digital, organizado por tema e por tipo de atividade.

---

## 01. Introdução

Fundamentos e conceitos base para qualquer analista forense. Comece por aqui.

Arquivos:

* 01_Introducao/01_Fundamentos.pdf
* 01_Introducao/02_Cadeia_de_Custodia.pdf
* 01_Introducao/03_Logs.pdf

---

## 02. Coleta de Evidências

Material focado em aquisição correta e preservação de evidências.

Arquivos:

* 02_Coleta_de_Evidencias/01_Imagem_de_Disco.pdf
* 02_Coleta_de_Evidencias/02_Memoria_Volatil.pdf
* 02_Coleta_de_Evidencias/03_Logs_de_Sistema.pdf
* 02_Coleta_de_Evidencias/04_Coleta_de_Dispositivos_Moveis.pdf

---

## 03. Análise de Sistemas

Aqui ficam os materiais organizados por sistema operacional. Útil para consultar artefatos, caminhos e o que analisar em cada ambiente.

### 03.1 Windows

Arquivos:

* 03_Analise_de_Sistemas/01_Windows/01_Visao_Geral_Windows.pdf
* 03_Analise_de_Sistemas/01_Windows/02_Registro_Hives.pdf
* 03_Analise_de_Sistemas/01_Windows/03_EventLogs.pdf
* 03_Analise_de_Sistemas/01_Windows/04_Artefatos_de_Execucao.pdf
* 03_Analise_de_Sistemas/01_Windows/05_Usuarios_e_Perfis.pdf

### 03.2 Linux

Arquivos:

* 03_Analise_de_Sistemas/02_Linux/01_Visao_Geral_Linux.pdf
* 03_Analise_de_Sistemas/02_Linux/02_Usuarios_e_Pastas_Home.pdf
* 03_Analise_de_Sistemas/02_Linux/03_Logs_do_Sistema.pdf
* 03_Analise_de_Sistemas/02_Linux/04_Servicos_e_Persistencia.pdf
* 03_Analise_de_Sistemas/02_Linux/05_Historico_e_Execucao.pdf

### 03.3 macOS

Arquivos:

* 03_Analise_de_Sistemas/03_MacOS/01_Visao_Geral_macOS.pdf
* 03_Analise_de_Sistemas/03_MacOS/02_Sistemas_de_Arquivos_APFS_HFS+.pdf
* 03_Analise_de_Sistemas/03_MacOS/03_Logs_e_Eventos_macOS.pdf
* 03_Analise_de_Sistemas/03_MacOS/04_Persistencia_LaunchAgents_LaunchDaemons.pdf
* 03_Analise_de_Sistemas/03_MacOS/05_Usuarios_Chaves_e_Seguranca.pdf
* 03_Analise_de_Sistemas/03_MacOS/06_Artefatos_de_Navegadores.pdf
* 03_Analise_de_Sistemas/03_MacOS/07_Artefatos_de_Comunicacao.pdf
* 03_Analise_de_Sistemas/03_MacOS/08_TimeMachine_e_Midias_Externas.pdf
* 03_Analise_de_Sistemas/03_MacOS/09_Checklist_Forense_macOS.pdf

---

## 04. Ferramentas

Seção para consulta de ferramentas forenses. Está dividida por função (aquisição, análise de disco, memória, rede etc.).

Arquivo geral:

* 04_Ferramentas/00_Visao_Geral_de_Ferramentas_Guia.pdf

### 04.1 Aquisição

* 04_Ferramentas/01_Aquisicao/01_FTK_Imager.pdf
* 04_Ferramentas/01_Aquisicao/02_Guymager.pdf
* 04_Ferramentas/01_Aquisicao/03_dd_dc3dd.md.pdf

### 04.2 Análise de Disco/Imagem

* 04_Ferramentas/02_Analise_de_Disco_Imagem/01_Autopsy.pdf
* 04_Ferramentas/02_Analise_de_Disco_Imagem/02_Sleuth_Kit_TSK_CLI.pdf
* 04_Ferramentas/02_Analise_de_Disco_Imagem/03_Solucoes_Comerciais_XWays_Magnet.pdf

### 04.3 Registro e Artefatos Windows

* 04_Ferramentas/03_Registro_e_Artefatos_Windows/01_RegRipper.pdf
* 04_Ferramentas/03_Registro_e_Artefatos_Windows/02_regipy_regrippy.pdf
* 04_Ferramentas/03_Registro_e_Artefatos_Windows/03_Registro_Offline_Registry_Explorer_chntpw_reged.pdf
* 04_Ferramentas/03_Registro_e_Artefatos_Windows/04_Analise_de_Logs_Windows_Chainsaw_EvtxECmd.pdf

### 04.4 Memória

* 04_Ferramentas/04_Memoria/01_Volatility.pdf
* 04_Ferramentas/04_Memoria/02_Rekall.pdf

### 04.5 Timeline e Correlação

* 04_Ferramentas/05_Timeline_e_Correlacao/01_plaso_log2timeline.pdf
* 04_Ferramentas/05_Timeline_e_Correlacao/02_psort.pdf
* 04_Ferramentas/05_Timeline_e_Correlacao/03_Timesketch_Visao_Geral.pdf

### 04.6 Rede

* 04_Ferramentas/06_Rede/01_Wireshark_para_Forense.pdf
* 04_Ferramentas/06_Rede/02_tcpdump.pdf
* 04_Ferramentas/06_Rede/03_NetworkMiner.pdf

### 04.7 Coleta rápida Windows

* 04_Ferramentas/07_Coleta_Rapida_Windows/01_KAPE.pdf

### 04.8 Utilitários de apoio

* 04_Ferramentas/08_Utilitarios_de_Apoio/01_Hashes_hashdeep_sha256sum_md5sum.pdf
* 04_Ferramentas/08_Utilitarios_de_Apoio/02_CyberChef.pdf
* 04_Ferramentas/08_Utilitarios_de_Apoio/03_ExifTool.pdf
* 04_Ferramentas/08_Utilitarios_de_Apoio/04_Binwalk.pdf
* 04_Ferramentas/08_Utilitarios_de_Apoio/05_SQLite_Browser.pdf

---

## 05. Procedimentos

Procedimentos operacionais padrão (SOP) para cenários comuns de forense e resposta.

Arquivos:

* 05_Procedimentos/01_Aquisicao_Padrao_Host_Windows.pdf
* 05_Procedimentos/02_Analise_Inicial_de_Imagem_Windows.pdf
* 05_Procedimentos/03_Investigacao_de_Exfiltracao_de_Dados.pdf
* 05_Procedimentos/04_Resposta_a_Incidente_de_Malware.pdf
* 05_Procedimentos/05_Coleta_Rapida_em_Campo_com_KAPE.pdf
* 05_Procedimentos/06_Documentacao_e_Cadeia_de_Custodia.pdf

---

## 06. Diversos

Materiais de apoio, modelos e temas complementares.

Arquivos:

* 06_Diversos/01_Glossario_Forense.pdf
* 06_Diversos/02_Cold_Forensics_e_Ordem_de_Volatilidade.pdf
* 06_Diversos/03_Imaging_no_Linux_com_dc3dd.pdf
* 06_Diversos/04_Uso_de_IA_em_DFIR.pdf
* 06_Diversos/05_Modelo_de_Relatorio_Simples.pdf

---

## 07. Práticas

Laboratórios e estudos de caso para treino.

Arquivos:

* 07_Praticas/01_THM_CaseB4DM755.pdf
* 07_Praticas/02_THM_MBrandGPTAnalysis.pdf
* 07_Praticas/03_THM_ExtAnalysis.pdf

---

## Nota final

* Este guia foi escrito para ser convertido em PDF, por isso os caminhos estão em texto puro e não em links específicos de Obsidian.

---

## Árvore Geral do Repositório

```
└───Estudos em Forense Digital
    │   00_Guia.pdf
    │
    ├───01_Introducao
    │       01_Fundamentos.pdf
    │       02_Cadeia_de_Custodia.pdf
    │       03_Logs.pdf
    │
    ├───02_Coleta_de_Evidencias
    │       01_Imagem_de_Disco.pdf
    │       02_Memoria_Volatil.pdf
    │       03_Logs_de_Sistema.pdf
    │       04_Coleta_de_Dispositivos_Moveis.pdf
    │
    ├───03_Analise_de_Sistemas
    │   ├───01_Windows
    │   │       01_Visao_Geral_Windows.pdf
    │   │       02_Registro_Hives.pdf
    │   │       03_EventLogs.pdf
    │   │       04_Artefatos_de_Execucao.pdf
    │   │       05_Usuarios_e_Perfis.pdf
    │   │
    │   ├───02_Linux
    │   │       01_Visao_Geral_Linux.pdf
    │   │       02_Usuarios_e_Pastas_Home.pdf
    │   │       03_Logs_do_Sistema.pdf
    │   │       04_Servicos_e_Persistencia.pdf
    │   │       05_Historico_e_Execucao.pdf
    │   │
    │   └───03_MacOS
    │           01_Visao_Geral_macOS.pdf
    │           02_Sistemas_de_Arquivos_APFS_HFS+.pdf
    │           03_Logs_e_Eventos_macOS.pdf
    │           04_Persistencia_LaunchAgents_LaunchDaemons.pdf
    │           05_Usuarios_Chaves_e_Seguranca.pdf
    │           06_Artefatos_de_Navegadores.pdf
    │           07_Artefatos_de_Comunicacao.pdf
    │           08_TimeMachine_e_Midias_Externas.pdf
    │           09_Checklist_Forense_macOS.pdf
    │
    ├───04_Ferramentas
    │   │   00_Visao_Geral_de_Ferramentas_Guia.pdf
    │   │
    │   ├───01_Aquisicao
    │   │       01_FTK_Imager.pdf
    │   │       02_Guymager.pdf
    │   │       03_dd_dc3dd.md.pdf
    │   │
    │   ├───02_Analise_de_Disco_Imagem
    │   │       01_Autopsy.pdf
    │   │       02_Sleuth_Kit_TSK_CLI.pdf
    │   │       03_Solucoes_Comerciais_XWays_Magnet.pdf
    │   │
    │   ├───03_Registro_e_Artefatos_Windows
    │   │       01_RegRipper.pdf
    │   │       02_regipy_regrippy.pdf
    │   │       03_Registro_Offline_Registry_Explorer_chntpw_reged.pdf
    │   │       04_Analise_de_Logs_Windows_Chainsaw_EvtxECmd.pdf
    │   │
    │   ├───04_Memoria
    │   │       01_Volatility.pdf
    │   │       02_Rekall.pdf
    │   │
    │   ├───05_Timeline_e_Correlacao
    │   │       01_plaso_log2timeline.pdf
    │   │       02_psort.pdf
    │   │       03_Timesketch_Visao_Geral.pdf
    │   │
    │   ├───06_Rede
    │   │       01_Wireshark_para_Forense.pdf
    │   │       02_tcpdump.pdf
    │   │       03_NetworkMiner.pdf
    │   │
    │   ├───07_Coleta_Rapida_Windows
    │   │       01_KAPE.pdf
    │   │
    │   └───08_Utilitarios_de_Apoio
    │           01_Hashes_hashdeep_sha256sum_md5sum.pdf
    │           02_CyberChef.pdf
    │           03_ExifTool.pdf
    │           04_Binwalk.pdf
    │           05_SQLite_Browser.pdf
    │
    ├───05_Procedimentos
    │       01_Aquisicao_Padrao_Host_Windows.pdf
    │       02_Analise_Inicial_de_Imagem_Windows.pdf
    │       03_Investigacao_de_Exfiltracao_de_Dados.pdf
    │       04_Resposta_a_Incidente_de_Malware.pdf
    │       05_Coleta_Rapida_em_Campo_com_KAPE.pdf
    │       06_Documentacao_e_Cadeia_de_Custodia.pdf
    │
    ├───06_Diversos
    │       01_Glossario_Forense.pdf
    │       02_Cold_Forensics_e_Ordem_de_Volatilidade.pdf
    │       03_Imaging_no_Linux_com_dc3dd.pdf
    │       04_Uso_de_IA_em_DFIR.pdf
    │       05_Modelo_de_Relatorio_Simples.pdf
    │
    └───07_Praticas
            01_THM_CaseB4DM755.pdf
            02_THM_MBrandGPTAnalysis.pdf
            03_THM_ExtAnalysis.pdf
```
