---
title: "Glossário Forense"
tags: [diversos, definicoes, fundamentos, dfir]
status: rascunho
---

# Glossário Forense

Pequeno dicionário para quem for usar este repositório. A ideia é nivelar termos que aparecem em **coleta**, **análise** e **procedimentos**, sem virar um livro. Sempre que um termo tiver um arquivo próprio em outra pasta, dá pra apontar pra lá.

---

## A

**Aquisição forense**  
Ato de copiar dados de uma mídia/dispositivo de forma íntegra e validável, normalmente por meio de imagem bit a bit. Objetivo: preservar o original e trabalhar sempre na cópia. (ver `02_Coleta_de_Evidencias/`)

**Artefato**  
Qualquer dado do sistema operacional/aplicativo que revele atividade do usuário ou do sistema (prefetch, Event Logs, Registro, histórico de navegador, LNK, Jump Lists, bash_history etc.).

---

## C

**Cadeia de custódia**  
Registro contínuo de quem coletou, quem transportou, quem analisou e onde a evidência ficou armazenada. Serve para mostrar que o dado não foi alterado e que pode ser aceito em contexto legal. (ver `05_Procedimentos/06_Documentacao_e_Cadeia_de_Custodia.md`)

**Carving (file carving)**  
Técnica de recuperar arquivos procurando por assinaturas (cabeçalho/rodapé) direto no espaço não alocado, sem depender do sistema de arquivos. Usado quando o arquivo foi apagado ou o sistema de arquivos está danificado.

**Coleta rápida**  
Aquisição limitada aos artefatos de maior valor (logs, hives, prefetch, usuários) quando não há tempo para imagem completa. Ex.: KAPE. (ver `05_Procedimentos/05_Coleta_Rapida_em_Campo_com_KAPE.md`)

**Cold forensics**  
Exame feito com o sistema desligado ou com a imagem já criada, priorizando integridade e cadeia em vez de volatilidade. Muito usado quando o caso tem exigência legal alta. (ver `06_Diversos/02_Cold_Forensics_e_Ordem_de_Volatilidade.md`)

---

## D

**Disco / imagem de disco**  
Arquivo que representa bit a bit uma mídia original (HD, SSD, pendrive, VMDK). Pode estar em formatos RAW, E01, AFF etc. A análise deve ser feita **na imagem**, não no original.

**Dump de memória**  
Cópia do conteúdo da RAM naquele momento. Usado para achar processos, conexões, código injetado, chaves de descriptografia e artefatos que não foram para disco. (ver `04_Ferramentas/04_Memoria/01_Volatility.md`)

**DFIR**  
Digital Forensics and Incident Response. Conjunto de práticas para **descobrir o que aconteceu** e **conter** o incidente, mantendo evidência usável.

---

## E

**Evidência bruta**  
Aquilo que saiu direto do alvo (imagem, dump, pasta KAPE). Não deve ser editado. Deve ter hash e ficar em pasta só de brutos (`01_brutos/`).

**Evidência processada**  
Resultados de ferramentas (CSV, relatório do Autopsy, timeline, parse de EVTX). Pode ser recriado, então não exige o mesmo nível de proteção, mas precisa ser documentado.

---

## F

**Forensic soundness (caráter forense)**  
Qualidade do procedimento que permite demonstrar que a coleta/análise não alterou o original ou, se alterou, isso foi documentado e justificado.

---

## H

**Hash (MD5, SHA-1, SHA-256)**  
“Impressão digital” do arquivo/mídia. Se mudar 1 byte, o hash muda. Usado para provar integridade e para identificar arquivos conhecidos. Neste repositório, preferir **SHA-256**. (ver `04_Ferramentas/08_Utilitarios_de_Apoio/01_Hashes_hashdeep_sha256sum_md5sum.md`)

---

## I

**Imagem forense**  
Mesma ideia de “imagem de disco”, mas criada seguindo boas práticas (write blocker, hash, log de aquisição). Nem toda cópia de disco é imagem forense; depende do procedimento.

**IOC (Indicator of Compromise)**  
Elemento que ajuda a identificar presença de ameaça (hash de malware, domínio C2, IP, caminho incomum, chave de persistência). Útil para hunting e para notificar outros times. (ver `05_Procedimentos/04_Resposta_a_Incidente_de_Malware.md`)

---

## L

**Live forensics**  
Coleta feita com o sistema ligado (RAM, conexões, processos, chaves ainda montadas). Vantagem: pega o que é volátil. Desvantagem: pode alterar estado. (comparar com “cold forensics”)

**Logs**  
Registros de eventos de sistema/aplicativo. No Windows, EVTX; no Linux, `/var/log`. São fundamentais para timeline e para provar ações. (ver `01_Introducao/03_Logs.md`)

---

## M

**Montagem (mount) de imagem**  
Ato de montar uma imagem de disco como se fosse um disco real, geralmente em modo somente leitura, para navegar e extrair arquivos. Útil para validação rápida pós-aquisição.

**Malware forense / incidente de malware**  
Investigação focada em achar o executável malicioso, entender vetores, persistência e impacto. Usa muito memória + artefatos de execução. (ver `05_Procedimentos/04_Resposta_a_Incidente_de_Malware.md`)

---

## O

**Ordem de volatilidade**  
Prioridade de coleta do que muda mais rápido (CPU/cache → RAM → conexões → disco → logs remotos → backups). Ajuda a decidir o que coletar primeiro quando o tempo é curto. (ver `06_Diversos/02_Cold_Forensics_e_Ordem_de_Volatilidade.md`)

---

## P

**Prefetch**  
Artefato do Windows que registra execução de programas para acelerar carregamento. Útil para provar que algo rodou e quantas vezes. Pode estar em targets do KAPE.

**Procedimento**  
Documento que descreve passo a passo como atuar em determinado cenário (aquisição, exfiltração, malware). Tudo que está em `05_Procedimentos/` segue esse espírito.

---

## R

**Relatório / laudo**  
Documento final que amarra: o que foi coletado, o que foi feito, o que foi achado, o que se conclui. Deve listar evidências e hashes. (ver `06_Diversos/05_Modelo_de_Relatorio_Simples.md`)

**Registro (Windows Registry)**  
Base de dados do Windows que guarda configurações de sistema e de usuário. Tem muitos artefatos de interesse forense (MRU, Run, USB, etc.). (ver `03_Analise_de_Sistemas/01_Windows/02_Registro_Hives.md`)

---

## T

**Timeline**  
Linha do tempo que reúne eventos de fontes diferentes (logs, MFT, Registry, browser) pra mostrar o que aconteceu em ordem. Ferramentas: plaso/log2timeline, psort, Timesketch. (ver `04_Ferramentas/05_Timeline_e_Correlacao/`)

**Triagem**  
Análise rápida pra saber se vale aprofundar. Não substitui aquisição completa. Pode ser só abrir a imagem no Autopsy e rodar alguns módulos, ou rodar KAPE e olhar os CSVs principais.

---

## W

**Write blocker**  
Dispositivo (ou configuração) que impede escrita no disco de evidência durante a aquisição. Serve para garantir integridade. Sempre que possível usar hardware; software é plano B.

---

## Observações finais

- Se surgir um termo muito específico de uma ferramenta (ex.: “ingest module” do Autopsy), documentar na própria ferramenta em `04_Ferramentas/`.
- Este glossário é para **uso dentro deste repositório**: os termos refletem os nomes que estamos usando nos arquivos e não necessariamente todas as definições acadêmicas possíveis.