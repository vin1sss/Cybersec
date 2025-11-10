---
title: Fundamentos da Forense Digital
tags:
  - forense
  - fundamentos
  - introducao
  - metodologia
  - cadeia-de-custodia
status: concluido
---

# Fundamentos da Forense Digital

A forense digital é o ramo da ciência forense que aplica métodos técnicos e científicos para identificar, adquirir, preservar, analisar e apresentar evidências digitais de forma legalmente admissível e tecnicamente confiável. Ela atua tanto em contextos de segurança da informação (resposta a incidentes) quanto em investigações criminais conduzidas por órgãos públicos ou por equipes corporativas.

A crescente digitalização de processos e serviços fez com que a maioria dos incidentes - fraudes, vazamentos, espionagem, invasões, uso indevido de recursos - deixasse rastros em dispositivos e mídias digitais. A forense digital é o conjunto de práticas que permite localizar, preservar e interpretar esses rastros.

---

## 1. Conceitos Básicos

**Forense digital:** aplicação de ciência e tecnologia para adquirir, analisar e preservar indícios digitais de forma que possam ser apresentados e sustentados em tribunal.

**Prova digital:** qualquer dado ou informação de origem digital cuja obtenção foi legítima e cuja integridade pode ser demonstrada (logs, documentos, e-mails, bases de dados, tráfego de rede, arquivos de sistema, artefatos de navegação).

**Informação digital:** todo dado armazenado ou transmitido em formato digital, incluindo registros de sistemas operacionais, metadados de arquivos, registros de aplicativos, artefatos de nuvem e dados de redes.

A informática/forense digital está diretamente relacionada à resposta a incidentes, pois permite reconstruir o que ocorreu, identificar o vetor de ataque, apontar usuários e sistemas envolvidos e coletar material que pode ser usado para responsabilização.

---

## 2. Objetivos da Forense Digital

- Identificar, recolher e preservar evidências de um incidente ou cibercrime;
- Interpretar, documentar e apresentar as provas de modo a serem admissíveis em tribunal;
- Compreender técnicas e métodos usados pelos ofensores;
- Reduzir perdas financeiras, de reputação e de propriedade intelectual;
- Conhecer e respeitar a legislação aplicável;
- Conhecer plataformas digitais, tipos de dados e sistemas operacionais;
- Selecionar ferramentas apropriadas para aquisição e análise;
- Recuperar arquivos excluídos, ocultos ou temporários que possam servir como evidência;
- Apoiar o processo de acusação/instrução;
- Gerar lições aprendidas para prevenir incidentes semelhantes.

---

## 3. Metodologia de Forense Digital (Visão NIST)

Embora diferentes casos exijam ferramentas e técnicas específicas, há um processo de referência amplamente aceito - como o descrito pelo NIST - que organiza a investigação em quatro fases principais.

### 3.1 Coleta (Collection)

- Identificação de todos os dispositivos e mídias potencialmente relevantes (computadores, notebooks, mídias removíveis, câmeras, smartphones, dispositivos IoT).
- Garantia de que o dado original não seja alterado durante a coleta.
- Registro detalhado do que foi coletado (origem, data, responsável).

### 3.2 Exame (Examination)

- Redução e filtragem do grande volume de dados coletados.
- Extração apenas dos dados de interesse (por período, por usuário, por tipo de arquivo, por artefato).
- Organização dos dados para posterior análise.

### 3.3 Análise (Analysis)

- Correlação dos dados examinados com o cenário do incidente.
- Construção de linha do tempo dos eventos.
- Extração de relações entre artefatos (arquivos, usuários, sistemas).
- Formulação de conclusões técnicas que respondam às perguntas da investigação.

### 3.4 Relato (Reporting)

- Documentação metodológica da investigação: o que foi feito, com quais ferramentas e por quê.
- Apresentação dos achados técnicos de forma clara e rastreável.
- Inclusão de sumário executivo para públicos não técnicos.
- Registro de recomendações.

Essa abordagem garante rastreabilidade, reprodutibilidade e aderência a boas práticas reconhecidas.

---

## 4. Tipos de Forense Digital

A natureza da evidência digital varia conforme o ambiente e o meio onde o dado foi gerado. Entre as especializações mais comuns:

- **Computer Forensics:** investigação de computadores pessoais e estações de trabalho; análise de sistemas de arquivos, usuários, softwares instalados e artefatos do sistema operacional.
- **Mobile Forensics:** coleta e análise de dispositivos móveis, com foco em chamadas, mensagens, aplicativos, localização e artefatos de uso.
- **Network Forensics:** investigação de eventos em redes, especialmente por meio de logs e capturas de tráfego, para identificar comunicações, origem/destino e possíveis exfiltrações.
- **Database Forensics:** análise de bancos de dados para detectar acessos indevidos, alterações, deleções ou exfiltração.
- **Cloud Forensics:** investigação de dados e eventos em ambientes de nuvem, onde parte da evidência está sob controle do provedor.
- **Email Forensics:** estudo de cabeçalhos, rotas e conteúdos de e-mails para apurar fraudes, phishing e comunicações ligadas ao incidente.

Cada tipo possui procedimentos de aquisição e análise próprios, mas todos devem preservar integridade e cadeia de custódia.

---

## 5. Aquisição de Evidências

A aquisição é uma das etapas mais sensíveis, pois erros aqui comprometem toda a investigação.

### 5.1 Autorização

A coleta deve ser precedida de autorização das autoridades competentes ou de instrumento jurídico/administrativo equivalente. Evidência coletada sem autorização pode ser recusada.

### 5.2 Cadeia de Custódia

A cadeia de custódia é o documento que descreve todo o ciclo de vida da evidência, incluindo:

- Descrição do item (nome, tipo, número de série);
- Identificação de quem coletou;
- Data e hora da coleta;
- Local e forma de armazenamento;
- Registros de acesso (quem, quando, para quê).

Ela garante atribuição de responsabilidade, preservação e autenticidade da prova. É o principal instrumento para demonstrar que a evidência apresentada é a mesma que foi coletada.

### 5.3 Uso de Write Blockers

Ao coletar dados de mídias como HDDs e SSDs, é recomendável utilizar write blockers (bloqueadores de escrita).

Motivo: ao conectar o disco a uma estação de trabalho comum, processos de sistema podem alterar metadados (timestamps, registros de acesso), comprometendo a integridade. O write blocker impede qualquer tentativa de escrita na mídia original durante a aquisição.

---

## 6. Processos Operacionais Recomendados

Um fluxo operacional maduro costuma incluir:

1. Busca e apreensão autorizadas (proper search authority);
2. Registro imediato em cadeia de custódia;
3. Transporte e armazenamento seguros (inclusive proteção contra acesso remoto em dispositivos móveis);
4. Criação de cópia forense (“forensic image”) com ferramenta validada;
5. Trabalho somente sobre a cópia, mantendo o original intacto;
6. Validação por hash para demonstrar que a cópia é idêntica ao original;
7. Documentação contínua de passos, parâmetros e ferramentas.

O uso de ferramentas validadas, a possibilidade de repetição dos resultados e o relato técnico claro são condições para que a prova seja aceita.

---

## 7. Forense em Sistemas Windows

Em muitos incidentes, o ativo mais comum apreendido é um computador ou notebook com Windows. Nesses casos, é prática consolidada obter dois tipos de imagem:

| Tipo de imagem | Conteúdo | Observação |
| -------------- | -------- | ---------- |
| Imagem de Disco (Disk Image) | Cópia bit a bit de todo o dispositivo de armazenamento (HDD/SSD). Inclui arquivos, espaço não alocado, MFT, histórico de navegação, documentos. | Dado não volátil — permanece após desligar. |
| Imagem de Memória (Memory Image) | Cópia do conteúdo da RAM no momento da coleta. Inclui processos em execução, conexões, chaves descriptografadas, arquivos abertos. | Dado volátil — deve ser coletado primeiro. |

### 7.1 Ferramentas Comuns

- **FTK Imager**: criação de imagens de disco com interface gráfica; suporta formatos diferentes e permite pré-visualização.
- **Autopsy**: plataforma forense open source para análise de imagens; oferece busca por palavra-chave, recuperação de arquivos deletados, análise de metadados e detecção de inconsistências.
- **DumpIt**: utilitário simples para captura de memória de sistemas Windows.
- **Volatility**: framework para análise de memória (processos, conexões, DLLs, artefatos de malware) e que suporta múltiplos sistemas operacionais.

O uso combinado de imagem de disco e de memória permite reconstruir tanto o estado persistente quanto o estado momentâneo do sistema.

---

## 8. Metadados em Arquivos e Mídias

Quase todo artefato digital contém metadados que podem ser explorados em investigação.

### 8.1 Metadados de Documentos (PDF, DOCX)

Ferramentas como `pdfinfo` permitem extrair:

- título;
- assunto;
- autor;
- aplicação usada para criação;
- datas de criação e modificação;
- número de páginas;
- presença de criptografia.

Exemplo:

```bash
pdfinfo arquivo.pdf
````

Esses dados permitem identificar o software utilizado, o autor registrado e montar a linha do tempo de criação e alteração do documento.

### 8.2 Metadados EXIF em Imagens

O padrão EXIF (Exchangeable Image File Format) embute nas imagens informações como:

* modelo da câmera ou smartphone;
* data e hora da captura;
* parâmetros de disparo;
* coordenadas GPS (quando o dispositivo possui GPS).

Ferramentas como `exiftool` permitem extrair todos os campos:

```bash
exiftool imagem.jpg
```

Quando há coordenadas, é possível convertê-las para endereço aproximado via serviços de mapas. Isso auxilia na validação de procedência da foto, na localização do cenário e na correlação temporal com outras evidências.

---

## 9. Conclusão

A forense digital moderna combina:

1. **Procedimento** (autorização, cadeia de custódia, coleta correta);
2. **Técnica** (imagens forenses, ferramentas validadas, extração de metadados, análise de memória e disco);
3. **Relato** (documentação clara, reprodutível e adequada ao público).

Sem cadeia de custódia e sem integridade garantida, a melhor descoberta técnica pode ser desconsiderada. Sem metodologia e ferramentas adequadas, não há como percorrer grandes volumes de dados de forma eficiente. E sem relato, o trabalho técnico não chega ao tomador de decisão.

Este documento estabelece a base conceitual para as demais notas do repositório.