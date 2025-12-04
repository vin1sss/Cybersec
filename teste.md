# **Apostila Acadêmica de Aprofundamento em SIEM Open Source**

### *Uma Análise Arquitetônica, Metodológica e Epistemológica dos Sistemas de Gerenciamento de Informações e Eventos de Segurança*

---

## **Lista de Abreviaturas e Siglas**

| Sigla | Significado |
|-------|-------------|
| API | Application Programming Interface |
| ATT&CK | Adversarial Tactics, Techniques, and Common Knowledge |
| BOH | Behavior-Oriented Hunting |
| CIM | Common Information Model |
| CIS | Center for Internet Security |
| DCIM | Data Center Infrastructure Management |
| DSL | Domain-Specific Language |
| ECS | Elastic Common Schema |
| EDR | Endpoint Detection and Response |
| ELK | Elasticsearch, Logstash, Kibana |
| EPS | Events Per Second |
| FIM | File Integrity Monitoring |
| HELK | Hunting ELK |
| HIDS | Host-based Intrusion Detection System |
| HOH | Hypothesis-Oriented Hunting |
| IDS | Intrusion Detection System |
| ILM | Information Lifecycle Management |
| IOC | Indicator of Compromise |
| KQL | Kibana Query Language |
| MFA | Multi-Factor Authentication |
| MITRE | Massachusetts Institute of Technology Research and Engineering |
| NDR | Network Detection and Response |
| NIDS | Network-based Intrusion Detection System |
| OSSEC | Open Source HIDS Security |
| OSSIM | Open Source Security Information Management |
| PCI-DSS | Payment Card Industry Data Security Standard |
| POC | Proof of Concept |
| RBAC | Role-Based Access Control |
| SEM | Security Event Management |
| SIEM | Security Information and Event Management |
| SIM | Security Information Management |
| SNMP | Simple Network Management Protocol |
| SOC | Security Operations Center |
| SOAR | Security Orchestration, Automation and Response |
| TTP | Tactics, Techniques, and Procedures |
| UEBA | User and Entity Behavior Analytics |
| WMI | Windows Management Instrumentation |
| WinRM | Windows Remote Management |
| XDR | Extended Detection and Response |

---

## **Sumário**

**1. INTRODUÇÃO** .......................................................................... XX
   1.1 Contextualização Histórica e Relevância Contemporânea ................ XX
   1.2 Justificativa e Delimitação do Escopo ........................................ XX
   1.3 Metodologia e Estrutura da Apostila ......................................... XX

**2. REVISÃO E CONSOLIDAÇÃO DOS CONCEITOS FUNDAMENTAIS DE SIEM** .. XX
   2.1 O SIEM como evolução epistemológica do SIM e do SEM .............. XX
   2.2 Arquitetura Geral de um SIEM ................................................. XX
   2.3 Distinção conceitual entre SIEM, NDR, EDR e XDR ..................... XX
   2.4 O papel do SIEM no SOC ........................................................ XX

**3. PROCESSOS INTERNOS DE UM SIEM** .......................................... XX
   3.1 Ingestão de Logs: A Porta de Entrada para a Inteligência Operacional ... XX
   3.2 Parsing e Normalização: A Conversão Epistemológica do Dado Bruto ... XX
   3.3 Análise e Correlação: O Núcleo Cognitivo do SIEM ....................... XX
   3.4 Retenção e Information Lifecycle Management (ILM) ..................... XX

**4. BOAS PRÁTICAS AVANÇADAS NA IMPLEMENTAÇÃO E GESTÃO DE SIEM** .. XX
   4.1 O Dimensionamento como Fundamento Estrutural da Operação ......... XX
   4.2 Seleção Estratégica de Fontes de Log e a Construção da Visibilidade .... XX
   4.3 Autenticação, Controle de Acesso e a Segurança do Próprio SIEM ..... XX
   4.4 A Maturidade Operacional como Manifestação de Boas Práticas ....... XX

**5. APROFUNDAMENTO POR FERRAMENTA** ..................................... XX
   5.1 Wazuh ................................................................................ XX
   5.2 Elastic Stack (ELK) ................................................................ XX
   5.3 OpenSearch ......................................................................... XX
   5.4 Security Onion ..................................................................... XX
   5.5 OSSIM ................................................................................ XX
   5.6 Graylog ............................................................................... XX
   5.7 HELK ................................................................................. XX
   5.8 MozDef ............................................................................... XX

**6. COMPARATIVO TÉCNICO APROFUNDADO** .................................. XX
   6.1 O Foco Conceitual de Cada Solução ......................................... XX
   6.2 Licenciamento e Suas Implicações Epistemológicas ....................... XX
   6.3 Backend de Armazenamento: A Ontologia do Dado Persistente ........ XX
   6.4 Linguagens de Regra: A Gramática da Detecção ........................... XX
   6.5 Métodos de Ingestão: A Fenomenologia do Ato de Observar ........... XX

**7. ARQUITETURAS SIEM: BÁSICA, INTERMEDIÁRIA E AVANÇADA** ........ XX
   7.1 Arquitetura Básica ................................................................ XX
   7.2 Arquitetura Intermediária ....................................................... XX
   7.3 Arquitetura Avançada ............................................................ XX

**8. REGRAS DE CORRELAÇÃO E HUNTING** ...................................... XX
   8.1 Fundamentos Epistemológicos da Correlação .............................. XX
   8.2 MITRE ATT&CK como Ontologia da Ameaça ............................... XX
   8.3 Criação de Regras de Correlação: Metodologia Científica da Detecção .. XX
   8.4 Threat Hunting: A Ciência Ativa da Busca pela Ameaça ................. XX

**9. CONCLUSÃO GERAL** .............................................................. XX
   9.1 O SIEM como dispositivo de interpretação da realidade digital ......... XX
   9.2 A evolução arquitetônica do SIEM como evolução da própria segurança .. XX
   9.3 As ferramentas Open Source como expressões de epistemologias distintas .. XX
   9.4 Correlação, Hunting e MITRE ATT&CK como tripé da detecção moderna .. XX
   9.5 O papel do SIEM na governança, compliance e investigação ............ XX
   9.6 A maturidade do SOC como maturidade do próprio SIEM ............... XX
   9.7 A síntese final: o SIEM como fundamento da defesa moderna .......... XX

**REFERÊNCIAS BIBLIOGRÁFICAS** ................................................. XX

**APÊNDICES** ........................................................................... XX
   Apêndice A: Checklist de Implementação de SIEM ............................ XX
   Apêndice B: Glossário Expandido .................................................. XX
   Apêndice C: Recursos Adicionais ................................................... XX

---

## **Resumo**

O presente trabalho propõe uma análise ampla, aprofundada e rigorosamente fundamentada dos Sistemas de Gerenciamento de Informações e Eventos de Segurança (Security Information and Event Management – SIEM), com ênfase especial em soluções Open Source amplamente difundidas no ecossistema de segurança contemporâneo. Reconstruindo e expandindo a estrutura conceitual presente no material base fornecido, esta apostila aprofunda aspectos estruturais, operacionais e metodológicos referentes à arquitetura geral de um SIEM, seus fluxos internos de dados, mecanismos de ingestão, normalização, correlação, retenção e análise, bem como sua relação epistemológica com paradigmas modernos de detecção e resposta, tais como NDR, EDR e XDR. 

**Objetivos Específicos:**
- Analisar criticamente as arquiteturas SIEM em níveis básico, intermediário e avançado
- Examinar comparativamente oito soluções Open Source: Wazuh, Elastic Stack, OpenSearch, Security Onion, OSSIM, Graylog, HELK e MozDef
- Estabelecer fundamentos metodológicos para construção de regras de correlação
- Contextualizar o papel do MITRE ATT&CK como framework epistemológico de detecção
- Desenvolver diretrizes práticas para implementação e maturação de ambientes SIEM

A partir da estrutura detalhada no documento de referência principal e complementada pelo esquema de tópicos presente no documento adicional, desenvolve-se uma abordagem teórica de caráter crítico-analítico, compondo uma obra de caráter autosuficiente, adequada tanto para pesquisadores quanto para profissionais especializados.

---

## **Abstract**

This work proposes a comprehensive, in-depth and rigorously grounded analysis of Security Information and Event Management (SIEM) systems, with special emphasis on Open Source solutions widely disseminated in the contemporary security ecosystem. Reconstructing and expanding the conceptual framework present in the base material provided, this academic guide deepens structural, operational and methodological aspects regarding the general architecture of a SIEM, its internal data flows, ingestion, normalization, correlation, retention and analysis mechanisms, as well as its epistemological relationship with modern detection and response paradigms such as NDR, EDR and XDR.

**Specific Objectives:**
- Critically analyze SIEM architectures at basic, intermediate and advanced levels
- Comparatively examine eight Open Source solutions: Wazuh, Elastic Stack, OpenSearch, Security Onion, OSSIM, Graylog, HELK and MozDef
- Establish methodological foundations for building correlation rules
- Contextualize the role of MITRE ATT&CK as an epistemological detection framework
- Develop practical guidelines for implementation and maturation of SIEM environments

Based on the detailed structure in the main reference document and complemented by the topic outline in the additional document, a theoretical approach of a critical-analytical nature is developed, composing a self-sufficient work, suitable for both researchers and specialized professionals.

**Keywords:** SIEM; Information Security; SOC; Event Correlation; Threat Hunting; Security Architecture; Elastic Stack; Wazuh; OpenSearch; Security Onion; MITRE ATT&CK; Log Management; Detection and Response; Cybersecurity.

---

# **1. Introdução**

## **1.1 Contextualização Histórica e Relevância Contemporânea**

Os Sistemas de Gerenciamento de Informações e Eventos de Segurança (SIEM) emergem, no final da década de 1990 e início dos anos 2000, como resposta organizacional a um problema epistêmico fundamental: a impossibilidade de compreender, em tempo hábil, a natureza e extensão de atividades maliciosas em infraestruturas computacionais crescentemente complexas. O termo SIEM, cunhado por analistas do Gartner Group em 2005, consolidou-se como categoria que sintetizava duas tradições tecnológicas anteriores — o Security Information Management (SIM) e o Security Event Management (SEM) — unificando-as em uma plataforma capaz de combinar análise histórica, correlação em tempo real e produção de inteligência acionável.

Nas últimas duas décadas, o cenário de ameaças transformou-se radicalmente. Ataques tornaram-se mais sofisticados, persistentes e direcionados; superfícies de ataque expandiram-se exponencialmente com a adoção de cloud computing, IoT, work-from-anywhere e DevOps; e os requisitos regulatórios intensificaram-se globalmente (GDPR, LGPD, HIPAA, PCI-DSS, SOX). Nesse contexto, o SIEM deixou de ser opcional e tornou-se componente estrutural da arquitetura de segurança empresarial.

## **1.2 Justificativa e Delimitação do Escopo**

A escolha pelo tema justifica-se pela centralidade dos SIEMs na segurança da informação contemporânea e pela crescente adoção de soluções Open Source, que democratizam o acesso a tecnologias antes restritas a grandes corporações. Este trabalho visa não apenas descrever tecnicamente tais sistemas, mas oferecer uma análise crítica e fundamentada que subsidie a escolha, implementação e gestão de um SIEM, alinhada às melhores práticas e às necessidades específicas de cada organização.

Delimita-se, assim, o escopo da pesquisa à análise de soluções SIEM Open Source, com ênfase nas aspectos arquitetônicos, metodológicos e epistemológicos, sem desconsiderar as implicações práticas e operacionais de sua adoção em ambientes corporativos.

## **1.3 Metodologia e Estrutura da Apostila**

Este trabalho adota metodologia descritivo-analítica, fundamentada em revisão bibliográfica, análise documental de especificações técnicas, frameworks de segurança (NIST, ISO 27001, MITRE ATT&CK) e documentação oficial das ferramentas estudadas. A estrutura da apostila organiza-se em nove seções principais:

1. **Introdução** - contextualização e objetivos
2. **Revisão e Consolidação dos Conceitos Fundamentais** - epistemologia do SIEM
3. **Processos Internos de um SIEM** - ingestão, parsing, correlação, retenção
4. **Boas Práticas Avançadas** - dimensionamento, fontes de log, segurança
5. **Aprofundamento por Ferramenta** - análise de 8 soluções Open Source
6. **Comparativo Técnico Aprofundado** - ontologias e diferenças estruturais
7. **Arquiteturas SIEM** - básica, intermediária e avançada
8. **Regras de Correlação e Hunting** - metodologia de detecção
9. **Conclusão Geral** - síntese e perspectivas futuras

---

# **2. Revisão e Consolidação dos Conceitos Fundamentais de SIEM**

## **2.1 O SIEM como evolução epistemológica do SIM e do SEM**

O SIEM surge como uma síntese histórica e tecnológica entre duas categorias de ferramentas que marcaram a segurança ofensiva e defensiva entre o final dos anos 1990 e início dos 2000: o Security Information Management (SIM), voltado à centralização, agregação, auditoria e retenção de logs para fins de conformidade e análise retrospectiva, e o Security Event Management (SEM), orientado à detecção em tempo real, visualização instantânea de eventos e operação dinâmica de alertas. O que caracteriza epistemologicamente o SIEM não é meramente a união destas funcionalidades, mas a constituição de um mecanismo integrador capaz de estabelecer relações semânticas entre eventos heterogêneos, oriundos de fontes díspares e produzidos em temporalidades distintas.

A arquitetura conceitual do SIEM descrita no documento-base enfatiza esse duplo caráter: simultaneamente histórico-retrospectivo e analítico-operacional. Em termos epistemológicos, o SIEM representa uma tentativa de converter dados operacionais em conhecimento estruturado, operável e contextualizado. Esse caráter cognitivo evidencia-se nos mecanismos internos de correlação, na normalização que permite abstração semântica de logs heterogêneos, na agregação que reduz ruído analítico e na consolidação de painéis síntese que funcionam como instrumentos hermenêuticos para analistas.

**Linha do Tempo Evolutiva:**
- **1998-2002**: Emergência do SIM (foco em compliance e auditoria)
- **2000-2004**: Desenvolvimento do SEM (detecção em tempo real)
- **2005**: Gartner consolida o termo SIEM
- **2008-2012**: Primeira onda de adoção corporativa massiva
- **2013-2017**: Integração com Big Data e UEBA
- **2018-presente**: Convergência com SOAR, cloud-native e XDR

## **2.2 Arquitetura Geral de um SIEM**

A arquitetura interna de um SIEM, conforme delineada no material de referência, está organizada em uma série de camadas funcionais cuja interação define a capacidade do sistema de sustentar análises de alta complexidade. A primeira dessas camadas é a aquisição de dados, na qual agentes locais, coletores remotos ou mecanismos agentless convertem o fluxo bruto de registros em entradas processáveis. Nesta etapa, a prioridade é garantir não somente a captação integral dos eventos relevantes, mas também sua integridade temporal e semântica.

A seguir, tem-se a camada de agregação e normalização, em que o sistema unifica estruturas, modelos e taxonomias, eliminando duplicidades e estabelecendo uma gramática comum capaz de abstrair as especificidades de cada dispositivo ou aplicação. A normalização é um requisito epistemológico fundamental: sem ela, a heterogeneidade dos formatos inviabilizaria qualquer tentativa de escrita de regras universais de correlação ou de análise transversal.

A terceira camada, a de análise e correlação, constitui o “núcleo cognitivo” do SIEM. Trata-se de um mecanismo que confronta o fluxo normalizado de dados com um conjunto estruturado de regras, heurísticas, assinaturas, estatísticas e modelos de comportamento, visando deduzir relações causais e detectar padrões que não se manifestariam em uma observação isolada. A correlação é, portanto, o ato de agregar sentido ao fluxo de dados, reconstruindo narrativas de ataque, incidentes ou anomalias.

Por fim, a arquitetura incorpora mecanismos de visualização e armazenamento. A visualização não é apenas um elemento de interface, mas instrumento epistemológico: por meio de dashboards, gráficos e sistemas de consulta, os analistas constroem interpretações e inferências. O armazenamento, por sua vez, constitui o arquivo histórico sem o qual não se pode realizar auditorias, análises forenses ou hunting retrospectivo.

**Diagrama Conceitual (representação textual):**

```
┌─────────────────────────────────────────────────────────────┐
│                    CAMADA DE VISUALIZAÇÃO                    │
│              (Dashboards, Alertas, Relatórios)               │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│               CAMADA DE ANÁLISE E CORRELAÇÃO                 │
│        (Motor de Regras, UEBA, Machine Learning)             │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│           CAMADA DE NORMALIZAÇÃO E ENRIQUECIMENTO            │
│         (Parsing, Taxonomia, Threat Intelligence)            │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│                 CAMADA DE AGREGAÇÃO                          │
│            (Deduplicação, Pré-processamento)                 │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│                   CAMADA DE INGESTÃO                         │
│       (Agentes, Syslog, APIs, Coletores de Rede)             │
└──────────────────────┬──────────────────────────────────────┘
                       │
┌──────────────────────▼──────────────────────────────────────┐
│                    FONTES DE DADOS                           │
│    (Endpoints, Firewalls, Servidores, Cloud, Aplicações)     │
└─────────────────────────────────────────────────────────────┘
```

## **2.3 Distinção conceitual entre SIEM, NDR, EDR e XDR**

Embora frequentemente tratados como ferramentas complementares, SIEM, NDR, EDR e XDR representam paradigmas distintos de observação e interação com ambientes computacionais. O material analisado apresenta suas diferenças estruturais, as quais aqui desenvolvemos em maior densidade teórica.

O SIEM é, por natureza, **log-centric**: opera sobre a reconstrução semântica da atividade sistêmica a partir de registros discretos. Já o NDR (Network Detection and Response) é **network-centric**, fundamentando-se na captura e análise contínua de pacotes, fluxos e metadados de rede. O EDR (Endpoint Detection and Response), por sua vez, é **host-centric**, observando a fenomenologia interna do endpoint, como processos, chamadas de sistema e acessos a arquivos. O XDR (Extended Detection and Response) tenta unificar esses paradigmas, produzindo um modelo **threat-centric**, capaz de correlacionar camadas diversas.

Esta apostila investiga tais distinções não apenas como diferenças funcionais, mas como expressões de modelos epistemológicos distintos: o SIEM interpreta, o NDR observa, o EDR monitora e o XDR integra. Daí decorre sua complementaridade.

## **2.4 O papel do SIEM no SOC**

O SIEM é descrito no documento-base como a “espinha dorsal” do SOC, formulação que ampliamos aqui de forma epistemológica. Ele não apenas conecta fontes e destinos de dados, mas estrutura a própria prática investigativa do analista. Sem o SIEM, o SOC opera em fragmentação informacional; com ele, opera em síntese cognitiva.

A centralidade do SIEM aparece de três formas:

1. **Na detecção**, ao gerar sinais iniciais de que uma atividade pode constituir ameaça.
2. **Na investigação**, ao fornecer corpus histórico necessário para reconstituir a cronologia de um incidente.
3. **Na conformidade**, ao sustentar registros auditáveis essenciais a normas regulatórias.

# **3. Processos Internos de um SIEM**

Os processos internos de um SIEM constituem o alicerce técnico e epistemológico que confere significado, coesão e potencial analítico ao conjunto de dados provenientes da infraestrutura de TI. Embora frequentemente apresentados como simples etapas operacionais, tais processos representam, na verdade, mecanismos fundamentais de construção de conhecimento dentro de um ecossistema de segurança. Cada fase – da ingestão à retenção – é um componente indispensável na transformação de dados brutos em artefatos interpretáveis, correlacionáveis e úteis para análises investigativas ou para a tomada de decisão estratégica em um SOC. Assim, nesta seção, busca-se reconstituir tais mecanismos sob uma perspectiva profundamente analítica, contemplando não apenas sua função técnica, mas sua relevância cognitiva para o funcionamento pleno de uma plataforma SIEM.

---

## **3.1 Ingestão de Logs: A Porta de Entrada para a Inteligência Operacional**

A ingestão de logs representa o primeiro contato entre o ecossistema operacional e o SIEM. É nela que ocorre a transição de um universo caótico de registros dispersos para uma linha de entrada minimamente estruturada. No documento-base, a ingestão é descrita como o processo responsável por trazer dados de eventos ao sistema, seja por agentes instalados nos hosts, seja por mecanismos agentless baseados em protocolos padrão.

A ingestão, contudo, cumpre uma função epistemológica ainda mais profunda: ela determina o que *pode* ser conhecido. Um SIEM só é capaz de construir narrativas corretas acerca de ameaças ou comportamentos anômalos se a camada de ingestão for abrangente, fidedigna e temporalmente coerente. Logo, a qualidade da ingestão condiciona a qualidade de todo o aparato analítico subsequente.

### **3.1.1 Ingestão baseada em agente**

Na ingestão via agente, um software leve (como Wazuh Agent, Beats, NXLog, OSSEC Agent, entre outros) é instalado no endpoint ou servidor. De acordo com o documento analisado, o agente pode realizar pré-processamento, filtragem, enriquecimento e até compressão antes de enviar dados ao SIEM. Esse pré-processamento interno é epistemologicamente relevante, pois desloca parte da construção semântica para a extremidade da rede, permitindo maior resiliência em ambientes de alto volume e maior controle sobre a granularidade dos eventos coletados.

Além disso, a utilização de agentes permite detectar eventos que não seriam visíveis por métodos agentless, como monitoramento detalhado de processos, variações de integridade de arquivos, eventos de kernel, entradas profundas do registro do Windows e ações internas de aplicações.

### **3.1.2 Ingestão agentless**

A ingestão sem agente, por sua vez, opera por meio de protocolos como Syslog, SNMP, WMI, WinRM, API endpoints e webhooks. De acordo com a apostila base, esse método reduz a sobrecarga nos hosts, mas sacrifica o pré-processamento e a filtragem local. Em ambientes amplos, a ingestão agentless possibilita coleta simplificada de dispositivos de rede, appliances e serviços de nuvem, onde a instalação de agentes é inviável ou indesejada.

No plano epistemológico, porém, esta metodologia delega a totalidade da semântica do evento ao SIEM, que passa a operar diretamente sobre dados brutos e potencialmente ruidosos.

### **3.1.3 A ingestão como fenômeno temporal**

Independentemente do método, a ingestão deve preservar a coerência temporal dos eventos. Em sistemas distribuídos, diferenças de fuso horário, clock drift e latências podem comprometer a capacidade de correlação. Assim, a ingestão não é apenas um processo técnico, mas um ato de reconstrução cronológica, no qual o SIEM busca estabelecer uma ordem estável dentro do fluxo heterogêneo de eventos.

**Matriz de Decisão: Agente vs Agentless**

| Critério | Baseado em Agente | Agentless |
|----------|-------------------|-----------|
| **Granularidade** | Alta (acesso profundo) | Média (depende do protocolo) |
| **Overhead no host** | Médio (consumo de recursos) | Baixo |
| **Pré-processamento** | Sim (local) | Não (centralizado) |
| **Latência** | Baixa | Variável |
| **Manutenção** | Complexa (vários agentes) | Simples |
| **Visibilidade interna** | Completa (processos, arquivos) | Limitada |
| **Ideal para** | Endpoints críticos, servidores | Dispositivos de rede, appliances |

---

## **3.2 Parsing e Normalização: A Conversão Epistemológica do Dado Bruto**

Após a ingestão, o SIEM depara-se com um conjunto massivo de registros potencialmente heterogêneos, produzidos em formatos diversos, com campos, taxonomias e granularidades distintas. O parsing e a normalização constituem, então, a etapa inaugural da construção cognitiva propriamente dita, pois convertem dados em linguagem analisável.

### **3.2.1 Parsing como extração semântica**

O parsing é a ação de extrair campos relevantes dos logs. A apostila base destaca que essa etapa identifica elementos como IP de origem, usuário, ação, protocolo, status e outros atributos indispensáveis para interpretações posteriores. No entanto, o parsing é mais do que extração; é tradução. Ele transforma fluxos textuais ou binários em entidades conceituais manipuláveis.

Assim, parsing não é um fim em si, mas um meio para garantir que o SIEM compreenda aquilo que, de outra forma, seria apenas ruído textual.

### **3.2.2 Normalização como criação de um léxico universal**

Embora o parsing extraia campos, a normalização os unifica. Sem normalização, o SIEM estaria condenado a operar sobre fragmentações semânticas:

* “src_ip” para um firewall Cisco,
* “sourceAddress” para um appliance Palo Alto,
* “client_ip” para um serviço web.

O documento-base menciona explicitamente que a normalização converte tais variações em um único campo universal, como “source_ip”. Essa unificação é essencial para a escrita de regras de correlação portáveis, convertendo a heterogeneidade dos logs em uma ontologia coerente.

**Exemplo Prático de Normalização:**

**Antes da normalização:**
- Cisco ASA: `src=192.168.1.10`
- Palo Alto: `sourceAddress=192.168.1.10`
- Windows: `IpAddress="192.168.1.10"`
- Linux syslog: `from 192.168.1.10`

**Após normalização (ECS):**
- Campo unificado: `source.ip: 192.168.1.10`

Esta unificação permite regras universais:
```kql
source.ip: 192.168.1.10 AND destination.port: 445 AND event.outcome: failure
```

### **3.2.3 Normalização como fundamento epistemológico da correlação**

A normalização não é apenas técnica; é epistemológica. Ela determina o nível de abstração no qual o SIEM opera. Se a normalização falha, a correlação falha. Se a normalização é pobre, as regras tornam-se frágeis. Se a normalização é robusta, surgem possibilidades analíticas sofisticadas, como:

* detecção baseada em comportamento global,
* correlação entre múltiplos domínios de evidência,
* hunting contextualizado baseado em ontologias (ECS, ECS-like, CIM, etc.).

---

## **3.3 Análise e Correlação: O Núcleo Cognitivo do SIEM**

A correlação constitui o cerne interpretativo do SIEM. É através dela que eventos dispersos se tornam narrativas coerentes. Como afirmado pelo documento principal, a correlação pode assumir formas determinísticas (regras if–then) ou probabilísticas (modelos comportamentais), mas seu objetivo é sempre o mesmo: atribuir significado ao que, isoladamente, parece ordinário.

### **3.3.1 Correlação baseada em regras**

As regras tradicionais de correlação são mecanismos lógico-dedutivos. Elas identificam padrões explícitos, como múltiplas tentativas de autenticação falha seguidas de um login bem-sucedido, ou o acesso simultâneo a recursos anômalos por um mesmo usuário.

Essa forma de correlação oferece alta precisão quando bem definida, mas sofre limitações epistemológicas:

* não detecta eventos inéditos,
* depende da antecipação humana,
* é vulnerável a manipulações que evitem gatilhos conhecidos.

Contudo, permanece indispensável para detecções de alta fidelidade.

### **3.3.2 Correlação comportamental (UEBA)**

Modelos comportamentais (User and Entity Behavior Analytics – UEBA) utilizam estatística e machine learning para identificar desvios de padrões históricos. A apostila base menciona essa abordagem como parte da análise avançada de correlação.

Aqui, a correlação deixa de depender da antecipação humana e passa a se fundamentar em anomalias, como:

* usuário que nunca acessou um servidor e repentinamente o faz às 3h da manhã,
* volume anormal de leitura de arquivos por um processo incomum,
* latência inesperada em tráfego lateral.

Essa forma de correlação eleva o SIEM ao campo da inferência probabilística e da detecção de ameaças desconhecidas.

### **3.3.3 A correlação como reconstrução narrativa**

Epistemologicamente, a correlação funciona como o ato de reconstruir histórias. O SIEM opera como um historiador digital, analisando fragmentos para inferir intenções, sequências e causalidades. É essa dimensão narrativa que permite identificar campaigns completas, desde reconhecimento até exfiltração.

---

## **3.4 Retenção e Information Lifecycle Management (ILM)**

A retenção é frequentemente tratada como um requisito regulatório, mas a apostila base destaca sua importância forense e histórica. Sob uma perspectiva acadêmica, a retenção é o mecanismo que preserva a memória institucional do ambiente tecnológico.

### **3.4.1 Dados quentes (hot)**

Os dados recentes, mantidos em armazenamento de alta performance, são essenciais para buscas imediatas, correlações em tempo real e resposta a incidentes. O SIEM depende da capacidade de consultar rapidamente esse conjunto para identificar sequências de eventos a curto prazo.

### **3.4.2 Dados frios (cold)**

Já os dados de longo prazo, mantidos em camadas menos custosas de armazenamento, atendem a necessidades de conformidade, auditoria e investigações retrospectivas. A divisão hot/warm/cold se consolidou como prática central em arquiteturas modernas de SIEM, descrita tanto no PDF principal quanto no documento adicional de tópicos.

### **3.4.3 ILM como formalização da memória organizacional**

O Information Lifecycle Management formaliza essa passagem entre camadas de retenção, automatizando:

* transição de hot para warm,
* posterior migração para cold,
* eventual descarte conforme políticas regulamentares.

Sem ILM, o SIEM colapsa sob o peso do próprio volume.

# **4. Boas Práticas Avançadas na Implementação e Gestão de SIEM**

A implementação eficaz de um SIEM não se limita à adoção de tecnologias ou ferramentas específicas, mas implica a construção de um ecossistema conceitual, arquitetônico e metodológico capaz de sustentar análises continuadas em ambientes de alta complexidade. A apostila-base destaca a importância de princípios como dimensionamento adequado, seleção criteriosa de fontes de logs, políticas robustas de autenticação e controle de acesso, e segregação arquitetônica do sistema. Nesta seção, desenvolve-se tais elementos de maneira aprofundada, buscando compreender não apenas como devem ser aplicados, mas por que são epistemologicamente necessários.

## **4.1 O Dimensionamento como Fundamento Estrutural da Operação**

O dimensionamento (sizing) constitui um dos pilares centrais da engenharia de um SIEM. Ele define a viabilidade operacional do sistema ao longo do tempo, determinando se o ambiente será capaz de suportar volumes crescentes de eventos, aumentos das matrizes de telemetria e escalonamentos imprevistos provocados por incidentes. A apostila original observa que erros de dimensionamento são causa comum de falhas graves em implementações de SIEM, frequentemente associadas a quedas de performance, perdas de eventos, latência na ingestão, incapacidade de correlação em tempo real e colapso dos pipelines de análise.

O dimensionamento deve, antes de tudo, reconhecer a ambiguidade do conceito de volume. Não basta contabilizar apenas a taxa média de eventos por segundo (Events Per Second – EPS). Deve-se considerar a variabilidade temporal, picos atribuídos a eventos extraordinários, janelas de maior atividade operacional, explosões de logs durante incidentes, além de fatores externos como integrações com novas ferramentas, mudanças na topologia de rede ou ampliação de domínios de identidade.

Sob uma perspectiva epistemológica, o dimensionamento traduz-se no ato de prever a capacidade cognitiva do SIEM: quanta informação ele poderá absorver, processar, normalizar, correlacionar e armazenar sem deterioração. Quanto mais vasto o ecossistema organizacional, mais complexa essa previsão. Assim, boas práticas recomendam a adoção de margens de segurança substanciais, frequentemente entre 20% e 30% acima do volume máximo projetado, não apenas como medida técnica, mas como reconhecimento da impossibilidade de antecipar plenamente a dinâmica das infraestruturas contemporâneas.

Além disso, o dimensionamento é intrinsecamente ligado à arquitetura subjacente. Sistemas baseados em Elasticsearch ou OpenSearch, populares em soluções Open Source, dependem fortemente de RAM, throughput de disco e topologia de cluster. Sistemas como Graylog ou Wazuh interagem com motores de armazenamento que impõem restrições próprias. Assim, dimensionar é compreender a fundo o comportamento do backend e antever transformações futuras do ambiente.

## **4.2 Seleção Estratégica de Fontes de Log e a Construção da Visibilidade**

A apostila base enfatiza que não é possível ou desejável ingerir todos os logs de um ambiente, sendo necessária uma priorização baseada no valor de segurança proporcionado por diferentes fontes. Expandindo essa perspectiva, é possível afirmar que a coleta de logs constitui um ato de curadoria epistemológica: decide-se o que será visível e o que permanecerá oculto, o que será passível de correlação e o que será irremediavelmente opaco.

Fontes de identidade representam a espinha dorsal desse processo. Controladores de domínio, servidores de autenticação e mecanismos de gestão de identidade formam o núcleo que sustenta a narrativa de qualquer incidente, pois permitem compreender quem fez o quê, quando e onde. Um SIEM que não coleta logs de identidade é incapaz de distinguir o banal do crítico, o legítimo do suspeito, o ruído do ataque. Assim, priorizá-los não é apenas uma recomendação técnica, mas uma exigência para a produção de conhecimento acionável.

Fontes de perímetro, como firewalls, proxies e VPNs, oferecem o olhar para a fronteira entre o ambiente interno e o externo. Elas revelam padrões de movimentação, tentativas de intrusão, fluxos inesperados e atividades que desafiam políticas preestabelecidas. Já os endpoints críticos — servidores de aplicação, bases de dados e sistemas de armazenamento — constituem a camada da consequência: se o ataque se materializa, é neles que seus efeitos se tornam mais evidentes.

Finalmente, serviços em nuvem, cada vez mais centrais na infraestrutura moderna, introduzem uma nova dimensão epistemológica: a do ambiente fragmentado e distribuído, em que identidades, dados e aplicações não residem mais em contornos geográficos ou arquitetônicos definidos. As fontes de auditoria de provedores de nuvem oferecem não apenas visibilidade, mas continuidade narrativa entre o on-premises e o ambiente cloud, permitindo que o SIEM construa narrativas completas apesar da fragmentação.

## **4.3 Autenticação, Controle de Acesso e a Segurança do Próprio SIEM**

O SIEM, por armazenar dados sensíveis e operar como centro cognitivo do SOC, deve ser protegido com rigor equivalente ou superior ao dos sistemas que monitora. A apostila-base destaca a importância de autenticação multifator (MFA) e controle de acesso baseado em função (Role-Based Access Control – RBAC) como mecanismos centrais para preservar a integridade e confidencialidade da plataforma. Aqui, estas práticas são compreendidas como elementos de uma epistemologia da confiança.

A MFA fortalece o mecanismo de autenticação ao exigir múltiplas formas de verificação, impedindo que credenciais comprometidas resultem em acesso indevido. No contexto de um SIEM, esse risco é particularmente crítico: um atacante com acesso à plataforma obtém não apenas telemetria completa do ambiente — que pode ser usada para evitar detecção — como também controle sobre os próprios mecanismos de análise. Trata-se, portanto, de impedir que o observador seja comprometido, corrompendo todo o processo investigativo.

O RBAC, por sua vez, estabelece camadas hierárquicas de permissões, assegurando que cada analista tenha acesso apenas ao que é estritamente necessário. Analistas de nível 1 não devem manipular pipelines, engenheiros não devem apagar alertas, hunters não precisam modificar configurações estruturais. Essa separação não é apenas organizacional, mas metodológica: preserva a integridade epistêmica do ambiente ao evitar interferências indevidas na produção e interpretação dos dados.

A segregação física ou lógica da rede do SIEM também é um princípio fundamental. Colocar o SIEM em uma zona de gerenciamento isolada garante que ele não se torne vetor de movimentação lateral para agentes maliciosos. Na prática, cria-se um espaço hermético onde dados sensíveis podem ser manipulados com menor risco.

## **4.4 A Maturidade Operacional como Manifestação de Boas Práticas**

Embora muitas vezes não descrita formalmente, a maturidade operacional de um SOC é profundamente dependente da gestão continuada de boas práticas no SIEM. Uma plataforma SIEM não é estática; ela vive, evolui e transforma-se em sintonia com o ambiente que monitora. Assim, boas práticas devem ser compreendidas como um processo longitudinal.

Isso implica revisitar periodicamente o dimensionamento, reavaliar as fontes de log, atualizar pipelines, revisar regras de correlação, aprimorar dashboards e testar procedimentos forenses. Significa, sobretudo, desenvolver uma mentalidade investigativa crítica, na qual o SIEM é constantemente reinterpretado e aperfeiçoado à luz de novas ameaças, novas tecnologias e novos paradigmas.

Em síntese, as boas práticas descritas nos materiais fornecidos constituem não apenas orientações operacionais, mas elementos que sustentam a integridade cognitiva do SIEM. Sem elas, o SIEM deteriora-se, tornando-se incapaz de cumprir sua função primordial: produzir conhecimento confiável a partir de dados dispersos.

---

# **5. Aprofundamento por Ferramenta (Wazuh, Elastic, OpenSearch, Security Onion, OSSIM, Graylog, HELK, MozDef)**

### *Uma análise epistemológica, arquitetônica e operacional das principais soluções SIEM Open Source*

A compreensão aprofundada das ferramentas que compõem o ecossistema SIEM Open Source exige mais do que uma simples descrição funcional. Implica reconhecer nelas expressões distintas de paradigmas arquitetônicos, epistemologias próprias de coleta e interpretação de dados, abordagens heterogêneas de normalização, correlação e visualização, bem como tensões internas entre escalabilidade, custo, complexidade e precisão analítica.

Os documentos fornecidos apresentam uma sistematização das características essenciais de cada solução, que aqui expandimos de forma crítica, conectando-as com modelos teóricos de segurança, práticas de SOC e perspectivas contemporâneas do campo.

Cada ferramenta analisada representa uma construção conceitual particular sobre como deve ser um SIEM e sobre o que significa produzir conhecimento de segurança a partir de grandes volumes de dados.

## **Tabela Resumo Comparativa**

| Ferramenta | Foco Principal | Complexidade | Escalabilidade | Ideal Para |
|------------|----------------|--------------|----------------|------------|
| **Wazuh** | Endpoint/HIDS | Média | Boa | Compliance, FIM, monitoramento de hosts |
| **Elastic Stack** | Observabilidade Universal | Alta | Excelente | Ambientes grandes, análise flexível |
| **OpenSearch** | SIEM 100% Open Source | Alta | Excelente | Organizações que exigem total abertura |
| **Security Onion** | NDR + SIEM Integrado | Muito Alta | Boa | Análise de tráfego e forense de rede |
| **OSSIM** | SIEM Clássico Integrado | Média | Média | PMEs, visão unificada tradicional |
| **Graylog** | Log Management Rápido | Baixa-Média | Boa | Consultas ágeis, investigações ad hoc |
| **HELK** | Hunting + Data Science | Alta | Média | Hunting avançado, pesquisa, Windows |
| **MozDef** | SIEM + SOAR | Média-Alta | Boa | Automação de resposta, orquestração |

---

# **5.1 Wazuh**

### *A epistemologia do endpoint como centro analítico*

O Wazuh emerge como uma plataforma que ultrapassa os limites tradicionais de um SIEM e aproxima-se de um modelo híbrido entre SIEM, EDR, HIDS e mecanismo de conformidade. Como descrito na apostila-base, o Wazuh evolui do OSSEC e integra nativamente monitoração de integridade, detecção baseada em host, análise de vulnerabilidades e um motor de regras XML robusto, articulando-se à Elastic Stack ou ao OpenSearch como backend de armazenamento e visualização.

## **5.1.1 Arquitetura**

Sua arquitetura centrada no modelo server-agent revela uma epistemologia particular: o endpoint não é apenas produtor de telemetria, mas coautor do processo de análise, realizando pré-processamentos, verificações de integridade, detecções locais e envio seletivo de informações. O agente transporta parte da inteligibilidade para a borda da rede, aproximando a lógica de coleta daquela típica dos EDRs.

Ao se integrar com Elastic ou OpenSearch, o Wazuh herda suas capacidades de clusterização, escalabilidade e modelagem de dados. Entretanto, o processamento semântico das detecções permanece concentrado no Manager, destacando uma separação clara entre armazenamento e inferência.

## **5.1.2 Métodos de ingestão**

Seu foco predominante em ingestão por agente permite acesso granular a arquivos, processos, chaves de registro, eventos de segurança e variações de integridade. É precisamente essa granularidade que confere ao Wazuh seu caráter de HIDS ampliado. A ingestão agentless existe, mas não constitui o centro epistemológico do sistema.

## **5.1.3 Vantagens e limitações**

O Wazuh oferece força singular em conformidade (CIS, PCI-DSS), detecção de rootkits, análise detalhada de processos e monitoramento de integridade. Sua limitação mais evidente, conforme exposto no material fornecido, reside na complexidade de escalabilidade de seu Manager e na relativa fragilidade para correlação de eventos puramente de rede.

## **5.1.4 Casos de uso e contribuições analíticas**

O Wazuh ocupa posição privilegiada em ambientes que priorizam visibilidade profunda em endpoints. Sua epistemologia está orientada pela crença de que compreender o comportamento do host é compreender a ameaça em sua fase mais concreta e material.

---

# **5.2 Elastic Stack (ELK)**

### *O paradigma da modularidade e da análise distribuída*

Elastic Stack não nasce como SIEM, mas se torna um pela extensibilidade e flexibilidade de sua arquitetura. Elasticsearch, Logstash, Kibana e Beats formam um ecossistema que permite ingerir, transformar, normalizar, indexar e visualizar praticamente qualquer dado. Como destaca a apostila-base, trata-se de uma solução escalável, amplamente utilizada e capaz de comportar sistemas analíticos em larga escala.

## **5.2.1 Arquitetura**

Sua arquitetura distribuída e modular sustenta o que pode ser descrito como uma epistemologia de “infraestrutura aberta”: não existe uma ontologia rígida de segurança pré-definida; tudo pode ser indexado, desde que um schema seja estabelecido. Elasticsearch funciona como o motor cognitivo no qual dados são modelados e consultas se tornam inferências.

## **5.2.2 Métodos de ingestão**

Elastic Stack oferece múltiplos caminhos de ingestão:

* Beats (Filebeat, Winlogbeat, Metricbeat), agentes leves especializados em formatos específicos;
* Logstash, mecanismo versátil de parsing e enriquecimento;
* Ingest Node, pipelines embutidos no próprio Elasticsearch.

Essa diversidade revela um modelo epistemológico baseado na pluralidade: qualquer dado é potencialmente útil, desde que adequadamente estruturado.

## **5.2.3 Vantagens e limitações**

Entre as vantagens, destacam-se escalabilidade massiva, flexibilidade extraordinária e um ecossistema amadurecido. As limitações concentram-se na complexidade operacional, na necessidade de expertise avançada e na redução de funcionalidades na versão estritamente open source, fenômeno apontado no documento analisado.

## **5.2.4 Epistemologia analítica do Elastic SIEM**

Ao estruturar dados via Elastic Common Schema (ECS), o Elastic cria uma ontologia universal de segurança. Sua lógica de detecção, baseada em KQL e machine learning, confere-lhe o status de ferramenta híbrida entre SIEM tradicional e plataforma de análise comportamental.

---

# **5.3 OpenSearch**

### *O SIEM como ecossistema totalmente aberto*

OpenSearch representa um movimento político e epistemológico: recusar as restrições de licenciamento impostas pelo Elastic Stack e construir um sistema 100% open source. O documento analisado enfatiza que OpenSearch herda a estrutura distribuída, o modelo de ingestão e a arquitetura geral do Elasticsearch, mas adiciona o módulo Security Analytics como seu núcleo SIEM.

## **5.3.1 Arquitetura**

OpenSearch replica a lógica distribuída de indexação e análise, oferecendo alta disponibilidade e escalabilidade nativa. Sua arquitetura possibilita clusters amplos, adequados para grandes volumes de EPS.

## **5.3.2 Métodos de ingestão**

Idênticos ao Elastic Stack, com suporte a Beats, Logstash e ingestão direta via API.

## **5.3.3 Vantagens e limitações**

A principal vantagem é a natureza open source irrestrita. A limitação mais clara é a juventude relativa do ecossistema e a menor maturidade de algumas funcionalidades SIEM em comparação a soluções mais antigas.

## **5.3.4 O Security Analytics como linguagem universal**

OpenSearch adota Sigma como linguagem de regra, oferecendo portabilidade epistemológica entre diferentes SIEMs. Isso representa um avanço no campo, permitindo que conhecimentos de detecção sejam traduzidos entre plataformas distintas.

---

# **5.4 Security Onion**

### *A integração epistemológica entre SIEM, NDR e análise forense*

Security Onion é mais que um SIEM; é um ambiente completo de monitoramento de segurança empresarial. Integrando Zeek, Suricata, Elastic/OpenSearch, Fleet e outras ferramentas, ele constitui um ecossistema centrado na observação profunda do tráfego de rede. O documento fornecido o descreve como solução all-in-one, capaz de monitorar logs e pacotes de forma simultânea com alto nível de fidelidade.

## **5.4.1 Arquitetura**

Security Onion apresenta arquitetura híbrida, unindo SIEM, NDR, IDS, FIM, análise forense e hunting. Seu modelo pode operar em estruturas stand-alone ou distribuídas, com sensores espalhados pela rede capturando tráfego e telemetria avançada.

## **5.4.2 Ingestão como observação fenomenológica da rede**

A ingestão de logs ocorre por meio de agentes, mas seu ponto forte é a ingestão de pacotes e metadados capturados por Zeek e Suricata. Esse modelo revela uma epistemologia distinta daquela dos SIEMs tradicionais: compreender a ameaça pela fenomenologia do tráfego, e não apenas pela narrativa dos logs.

## **5.4.3 Vantagens e limitações**

Entre as vantagens estão profundidade de visibilidade, integração entre múltiplas camadas analíticas e vocação natural para forense de rede. A limitação principal é a necessidade de hardware robusto e expertise elevada, dada a complexidade do ecossistema.

---

# **5.5 OSSIM**

### *A ontologia do SIEM clássico em sua forma mais integrada*

OSSIM permanece como uma das expressões mais tradicionais do conceito original de SIEM. Integrando coleta de logs, análise de vulnerabilidades, HIDS e NIDS em um único pacote, ele representa uma abordagem consolidada e historicamente significativa do campo.

## **5.5.1 Arquitetura**

Sua arquitetura integrada facilita a adoção por pequenas e médias empresas. O motor de correlação proprietário cria um modelo interpretativo inferencial baseado em combinação de eventos, vulnerabilidades e informações de ativos.

## **5.5.2 Limitações contemporâneas**

Conforme exposto no documento-base, o desenvolvimento da versão open source tornou-se mais lento, especialmente após a evolução comercial do USM Anywhere. Ainda assim, OSSIM permanece relevante por sua visão unificada e acessível.

---

# **5.6 Graylog**

### *A epistemologia da simplicidade e da velocidade de consulta*

Graylog representa uma abordagem orientada à usabilidade e velocidade. Embora não seja um SIEM completo por definição, expandiu-se para incorporar elementos de alerta e correlação, aproximando-se dessa categoria.

## **5.6.1 Arquitetura**

Baseado em Graylog Server, MongoDB e Elasticsearch/OpenSearch, oferece modelo simples de instalação e operação. Sua interface veloz e intuitiva serve como instrumento heurístico poderoso para analistas.

## **5.6.2 Epistemologia da busca**

O foco de Graylog na consulta rápida e eficiente de dados transforma-o numa ferramenta de análise exploratória, útil para investigações ad hoc. Seu mecanismo de extractors fornece parsing flexível sem complexidade excessiva.

---

# **5.7 HELK**

### *Uma plataforma analítica orientada ao hunting e à ciência de dados*

HELK (Hunting ELK) representa uma ruptura intelectual significativa no campo. Ele combina Elastic Stack, Kafka, Spark e Jupyter Notebooks, aproximando o SIEM da ciência de dados e do raciocínio exploratório.

## **5.7.1 Arquitetura**

Sua arquitetura de múltiplos componentes revela uma epistemologia aberta: dados são ingeridos e modelados para permitir análise interativa, modelagem estatística e experimentação.

## **5.7.2 Foco em Sysmon e logs de alta fidelidade**

HELK prioriza logs detalhados de Windows (Sysmon), permitindo detecções robustas e análises comportamentais complexas. Seu uso é particularmente valioso para hunters em ambientes Windows-centric.

---

# **5.8 MozDef**

### *A automação como paradigma de resposta*

MozDef, criado pela Mozilla, incorpora o paradigma SOAR dentro da lógica SIEM. Sua arquitetura baseada em eventos e automação representa um deslocamento da epistemologia do observador para a epistemologia do ator: não apenas interpreta-se a ameaça, responde-se a ela.

## **5.8.1 Arquitetura**

A integração entre Elasticsearch, RabbitMQ e microserviços permite que o MozDef opere como orquestrador de incidentes, automatizando respostas em escala.

## **5.8.2 Contribuições epistemológicas**

MozDef demonstra que a fronteira entre SIEM e SOAR é permeável e que a evolução natural dos sistemas de detecção aponta para a integração de interpretação e ação.

---

## **6. Comparativo Técnico Aprofundado**

### *Um exame ontológico, metodológico e operacional das diferenças entre os SIEMs Open Source contemporâneos*

A comparação técnica entre soluções SIEM não deve ser tratada como mero inventário funcional, mas como um exercício epistemológico e metodológico. Cada ferramenta expressa uma concepção particular sobre como a segurança deve ser monitorada, como o conhecimento deve ser produzido, como a correlação deve ser implementada e qual papel o SIEM desempenha dentro da macroestrutura do SOC.

O que se busca, nesta seção, não é apenas cotejar características, mas compreender como cada sistema articula sua própria ontologia — ou seja, sua definição implícita sobre o que constitui um evento relevante, como ele deve ser interpretado e qual lógica deve presidir sua agregação e análise.

A seguir, aprofundamos os eixos comparativos mais relevantes: foco conceitual, licenciamento, backend de armazenamento, linguagens de regra e mecanismos de ingestão.

## **6.1 O Foco Conceitual de Cada Solução: Diferentes Ontologias de Segurança**

O documento-base apresenta uma síntese dos focos de cada ferramenta, que aqui reconstruímos de maneira analítica, compreendendo que cada SIEM internaliza um “modelo de mundo” distinto.

O **Wazuh** opera sob a ontologia do **endpoint como locus primário da ameaça**. Para ele, compreender segurança é compreender processos, arquivos, integridade e comportamentos internos de sistemas. Sua epistemologia é microestrutural: a ameaça se revela nos detalhes do host.

O **Elastic Stack**, ao contrário, adota a ontologia da **observabilidade total**. Seu foco não é apenas segurança, mas qualquer forma de dado operacional. Assim, o SIEM sobre Elastic privilegia a plasticidade: tudo pode ser monitorado, desde que bem modelado. A ameaça, aqui, é compreendida no macrofluxo.

O **OpenSearch** herda esse mesmo paradigma, mas o radicaliza ao insistir na total abertura. Sua ontologia central é a da **transparência estrutural**, na qual nenhuma funcionalidade deve ser limitada por licenças proprietárias.

O **Security Onion** possui ontologia própria: a da **fenomenologia da rede**. Ele entende que todo ataque se manifesta no tráfego, nos metadados, nas sequências de pacotes. Sua epistemologia é essencialmente observacional: conhecer segurança é observar a rede como um campo fenomenológico.

O **OSSIM** ancora-se na ontologia clássica do SIEM: a integração de ativos, vulnerabilidades, logs e eventos, formando um modelo de risco consolidado. Ele entende a ameaça como síntese de múltiplos mecanismos.

O **Graylog** vê segurança pela ótica da **consulta rápida e da clareza analítica**. Sua epistemologia é quase hermenêutica: compreender é interpretar logs de forma ágil e flexível.

O **HELK** desloca o foco para uma ontologia da **ciência de dados aplicada à segurança**. Para ele, a ameaça é um fenômeno estatístico, comportamental e explorável, que demanda ferramentas de modelagem e notebooks interativos.

O **MozDef** assume a ontologia da **ação automatizada**. A segurança é um processo ativo: não basta interpretar; é preciso responder.

Essas ontologias influenciam profundamente a forma como cada ferramenta coleta, estrutura, interpreta e responde a eventos.

---

# **6.2 Licenciamento e Suas Implicações Epistemológicas**

O licenciamento não é apenas um aspecto jurídico; ele determina quem pode modificar, auditar, expandir e replicar o conhecimento embutido no SIEM.

Ferramentas como **Wazuh** e **OSSIM**, licenciadas sob GPLv2, mantêm a liberdade de acesso ao código, porém sob uma lógica de copyleft que exige que modificações permaneçam abertas. Elas promovem uma epistemologia comunitária, baseada no compartilhamento.

O **Elastic Stack**, após alterações de licenciamento da Elastic NV, opera sob modelo misto: partes open source e partes sob Elastic License. Isso introduz limites epistemológicos — certas capacidades analíticas tornam-se proprietárias, deslocando o conhecimento para um espaço parcialmente fechado.

O **OpenSearch**, sob Apache 2.0, radicaliza o oposto: permite livre modificação, redistribuição e integração. Sua epistemologia é expansiva, permitindo que comunidades reconstruam o sistema.

Ferramentas como **Graylog** e **MozDef** mantêm licenças igualmente abertas, mas com diferentes nuances. O modelo de licenciamento determina, assim, a elasticidade cognitiva da ferramenta: sua capacidade de evoluir, ser auditada e ser compreendida profundamente.

---

# **6.3 Backend de Armazenamento: A Ontologia do Dado Persistente**

O backend de armazenamento é o repositório ontológico do SIEM: é nele que os dados assumem forma estável, adquirindo permanência temporal e permitindo análise retrospectiva.

As soluções baseadas em **Elasticsearch/OpenSearch** (Elastic SIEM, Wazuh, Graylog, Security Onion, HELK, MozDef) compartilham uma epistemologia orientada a documentos indexados, consultas distribuídas e event sourcing. A natureza distribuída desses backends concede poder analítico significativo, permitindo reconstrução histórica eficiente.

O **OSSIM**, utilizando PostgreSQL e seu motor de correlação próprio, adota um modelo relacional tradicional, adequado a estruturas mais compactas, mas limitado diante de grandes volumes de EPS.

A escolha do backend não é apenas questão de desempenho, mas define o próprio modo como o SIEM conceitua dados, tempo e correlação.

---

# **6.4 Linguagens de Regra: A Gramática da Detecção**

As linguagens de regra são gramáticas epistemológicas: são nelas que se codifica a interpretação da ameaça.

O material-base apresenta uma comparação direta entre linguagens como Sigma, KQL, XML (Wazuh) e DSLs proprietárias. Expandimos essa comparação analisando sua natureza conceitual.

### **Sigma como linguagem universal**

Sigma funciona como uma metalinguagem: ela descreve detecções de maneira abstrata, permitindo traduções para SIEMs distintos. É expressão clara da epistemologia compartilhada: um conhecimento de detecção pode ser portado entre plataformas.

### **KQL como linguagem orientada a contexto**

KQL é declarativa e expressiva. Privilegia consultas relacionais sobre campos normalizados. Sua epistemologia é a da observação direta: detectar é consultar.

### **XML do Wazuh como linguagem hierárquica**

As regras em XML estruturam a detecção em camadas, permitindo condições, exceções, agrupamentos e enriquecimentos. Sua estrutura evidencia uma epistemologia de árvore lógica, adequada a ambientes host-centric.

### **Regras de Suricata/Snort como gramática de fenômenos de rede**

Essas regras descrevem a ameaça na forma de padrões sobre pacotes e fluxos. Revelam uma epistemologia fisicalista: a ameaça se manifesta como um fenômeno físico observável nos sinais de rede.

A escolha da linguagem determina como o SIEM pensa.

---

# **6.5 Métodos de Ingestão: A Fenomenologia do Ato de Observar**

Os SIEMs open source implementam diferentes concepções de ingestão, e os documentos fornecidos descrevem características centrais desses mecanismos. Analisamos aqui os métodos sob perspectiva fenomenológica.

### **Wazuh: a ingestão como sensorialidade profunda**

A coleta via agente transforma o endpoint em um órgão sensorial complexo. Ele detecta sutilezas impossíveis de observar via Syslog: alterações de hash, criação de processos suspeitos, modificações em registros.

### **Elastic/OpenSearch: a ingestão como pluralidade**

Beats, Logstash e ingest nodes constituem um ecossistema polifônico: múltiplos fluxos convergem para um mesmo pipeline analítico.

### **Graylog: a ingestão como interpretação leve**

Seus extractors privilegiam parsing simples e ágil, alinhado à ontologia da rapidez investigativa.

### **Security Onion: a ingestão como observação empírica da rede**

Zeek e Suricata transformam pacotes em metadados ricos, articulando uma visão fenomenológica da ameaça.

### **MozDef: a ingestão como precursor da ação**

Cada evento recebido é potencialmente um gatilho de automação.

---

# **7. Arquiteturas SIEM: Básica, Intermediária e Avançada**

### *Uma análise estrutural, epistemológica e operacional da evolução arquitetônica dos sistemas de monitoramento de segurança*

A arquitetura de um SIEM é a materialização física, lógica e epistemológica da concepção que uma organização possui sobre segurança, visibilidade, governança e resposta a incidentes. Ela não se resume a diagramas estáticos, mas constitui uma **infraestrutura cognitiva**, na qual fluxos de dados, pipelines de análise, camadas de armazenamento e mecanismos de correlação convergem para formar uma narrativa coerente e contínua sobre a atividade do ambiente tecnológico.

Os documentos fornecidos descrevem três níveis principais de arquitetura (básica, intermediária e avançada). Aqui, porém, tais modelos são reconstruídos sob perspectiva profundamente analítica, destacando não apenas suas características técnicas, mas os **paradigmas epistemológicos** que sustentam cada nível de maturidade.

## **7.1 Arquitetura Básica**

### *O SIEM como monólito cognitivo centralizado*

A arquitetura básica representa o estágio inicial de adoção de SIEM dentro de uma organização. É caracterizada por um modelo monolítico no qual todos os componentes — ingestão, processamento, armazenamento, correlação e visualização — coexistem em um único servidor ou em uma estrutura minimamente distribuída.

Conforme apresentado no material-base, este modelo é típico de pequenas empresas, provas de conceito (POCs) ou ambientes com baixo volume de EPS, em que a centralização reduz custos e simplifica a administração técnica.

**Diagrama 7.1 - Arquitetura Básica de SIEM**

```
                    ┌─────────────────────┐
                    │   SIEM All-in-One   │
                    │  ┌───────────────┐  │
                    │  │ Visualização  │  │
                    │  ├───────────────┤  │
                    │  │  Correlação   │  │
                    │  ├───────────────┤  │
                    │  │ Normalização  │  │
                    │  ├───────────────┤  │
                    │  │   Ingestão    │  │
                    │  ├───────────────┤  │
                    │  │ Armazenamento │  │
                    │  └───────────────┘  │
                    └──────────┬──────────┘
                               │
              ┌────────────────┼────────────────┐
              │                │                │
         ┌────▼───┐       ┌────▼───┐      ┌────▼───┐
         │ Host 1 │       │ Host 2 │      │ FW/Net │
         └────────┘       └────────┘      └────────┘
```

*Fonte: Elaborado pelo autor (2024)*

## **7.1.1 Ontologia da arquitetura básica**

A arquitetura básica opera sob a ontologia da **unidade**: todos os processos são interpretados como partes de um único mecanismo cognitivo centralizado. Isso gera um ambiente epistêmico estreito, no qual a totalidade da interpretação de eventos está confinada a uma máquina.

A fragilidade desse modelo emerge de maneira clara: um único ponto de falha implica colapso total da visibilidade. Além disso, o SIEM, nesse estágio, não possui meios de modular sua capacidade cognitiva; ele pensa com o poder computacional que possui. Não há elasticidade.

## **7.1.2 Limitações epistemológicas**

Embora útil para ambientes pequenos, a arquitetura básica possui limitações profundas:

* Não suporta crescimento orgânico do volume de eventos.
* Não admite expansão cognitiva: não há como distribuir processos de correlação, ingestão ou busca.
* Impede análises comportamentais complexas, que exigem estruturas distribuídas.

A arquitetura básica é, assim, um precursor, não um fim.

---

## **7.2 Arquitetura Intermediária**

### *O SIEM como ecossistema parcialmente distribuído*

A arquitetura intermediária representa o momento em que a organização passa a compreender que o SIEM não é apenas uma ferramenta, mas uma infraestrutura de conhecimento. Aqui, ocorre a fragmentação deliberada de funções: ingestão, processamento e armazenamento deixam de coexistir em um mesmo nó, dando lugar a uma estrutura distribuída com diferentes servidores especializados.

O documento-base descreve este modelo como composto por **servidores de coleta/processamento**, um **cluster de armazenamento** e um **servidor de gestão/visualização**. Expandimos esse conceito examinando os aspectos teóricos da distribuição.

**Diagrama 7.2 - Arquitetura Intermediária de SIEM**

```
┌──────────────────────────────────────────────────────────┐
│                  Camada de Visualização                   │
│              ┌─────────────────────────┐                  │
│              │  Kibana / Dashboards    │                  │
│              └────────────┬────────────┘                  │
└───────────────────────────┼───────────────────────────────┘
                            │
┌───────────────────────────┼───────────────────────────────┐
│              Cluster Elasticsearch/OpenSearch             │
│   ┌──────────┐     ┌──────────┐     ┌──────────┐         │
│   │  Node 1  │─────│  Node 2  │─────│  Node 3  │         │
│   │ (Master) │     │  (Data)  │     │  (Data)  │         │
│   └──────────┘     └──────────┘     └──────────┘         │
└───────────────────────────┬───────────────────────────────┘
                            │
┌───────────────────────────┼───────────────────────────────┐
│            Camada de Processamento/Ingestão               │
│   ┌────────────┐            ┌────────────┐                │
│   │ Logstash 1 │            │ Logstash 2 │                │
│   └─────┬──────┘            └─────┬──────┘                │
└─────────┼─────────────────────────┼────────────────────────┘
          │                         │
    ┌─────▼──────┐           ┌──────▼─────┐
    │   Beats    │           │   Syslog   │
    │  (Agents)  │           │  Servers   │
    └────────────┘           └────────────┘
```

*Fonte: Elaborado pelo autor (2024)*

## **7.2.1 Ontologia da arquitetura intermediária**

A ontologia central deste nível é a da **diferenciação funcional**. Começa a emergir a compreensão de que:

* ingestão é um ato fenomenológico,
* parsing e normalização são atos interpretativos,
* correlação é ato cognitivo,
* armazenamento é ato memorial.

Cada ato exige recursos distintos. A arquitetura intermediária corporifica essa separação epistemológica em estruturas técnicas.

## **7.2.2 O cluster de armazenamento como memória distribuída**

O uso de Elastic/OpenSearch como backend transforma o SIEM em um organismo distribuído, com múltiplos nós compartilhando responsabilidade pela persistência dos dados. A memória deixa de ser monolítica e passa a operar como **memória coletiva**, replicada, resiliente e escalável.

Esse armazenamento distribuído é também epistemologicamente relevante: permite consultas transversais complexas, análise de padrões longos e interpretação de eventos dispersos no tempo.

## **7.2.3 Benefícios epistemológicos e operacionais**

Este modelo:

* amplia a capacidade cognitiva do SIEM,
* reduz latência de busca,
* aumenta resiliência,
* permite ingestion pipelines paralelos,
* suporta correlações de maior complexidade.

Trata-se de uma arquitetura suficiente para a maior parte das organizações de médio porte e para SOCs em processo de maturação.

## **7.2.4 Limitações estruturais**

Sua limitação principal reside na ausência de desacoplamento completo entre ingestão e processamento. Em casos de picos massivos de EPS, o SIEM intermediário ainda pode sofrer saturação dos pipelines.

---

## **7.3 Arquitetura Avançada**

### *O SIEM como sistema cognitivo distribuído e escalável*

A arquitetura avançada representa o ápice da maturidade de um SIEM. É caracterizada por **distribuição modular extrema**, **camadas de desacoplamento** e **uso de message brokers** para absorver picos de ingestão. O material-base descreve essa estrutura em termos de múltiplas camadas — coleta, enfileiramento, processamento, armazenamento e análise — cada qual com componentes especializados operando de forma orquestrada.

Aqui, reinterpretamos essa arquitetura sob perspectiva de sistemas complexos.

**Diagrama 7.3 - Arquitetura Avançada de SIEM com Hot-Warm-Cold**

```
┌─────────────────────────────────────────────────────────────┐
│                    Camada de Análise                         │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐    │
│  │ Kibana   │  │  HELK    │  │  Jupyter │  │  Custom  │    │
│  │Dashboard │  │ Notebooks│  │ Notebooks│  │   Apps   │    │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘    │
└───────────────────────────┬─────────────────────────────────┘
                            │
┌───────────────────────────┼─────────────────────────────────┐
│              Cluster Elasticsearch (Hot-Warm-Cold)           │
│                                                              │
│  ┌─────────────────┐  ┌─────────────────┐  ┌────────────┐  │
│  │   HOT Tier      │  │   WARM Tier     │  │ COLD Tier  │  │
│  │  (SSD, 7 dias)  │→ │ (SSD, 30 dias)  │→ │(HDD, 1ano) │  │
│  │  ┌───┐ ┌───┐    │  │  ┌───┐ ┌───┐    │  │  ┌───┐     │  │
│  │  │ N1│ │ N2│    │  │  │ N3│ │ N4│    │  │  │ N5│     │  │
│  │  └───┘ └───┘    │  │  └───┘ └───┘    │  │  └───┘     │  │
│  └─────────────────┘  └─────────────────┘  └────────────┘  │
└───────────────────────────┬─────────────────────────────────┘
                            │
┌───────────────────────────┼─────────────────────────────────┐
│                Camada de Processamento                       │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐    │
│  │Logstash 1│  │Logstash 2│  │Logstash 3│  │Logstash 4│    │
│  │(Enrich)  │  │ (Parse)  │  │(Correlate)│  │(Forward) │    │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘    │
└───────┼─────────────┼─────────────┼─────────────┼───────────┘
        └──────┬──────┴──────┬──────┴──────┬──────┘
               │             │             │
┌──────────────┼─────────────┼─────────────┼───────────────────┐
│                  Message Broker (Kafka/RabbitMQ)             │
│  ┌──────────────────────────────────────────────────────┐    │
│  │  Topic: logs.firewall  │  Topic: logs.windows        │    │
│  │  Topic: logs.linux     │  Topic: logs.cloud          │    │
│  └──────────────────────────────────────────────────────┘    │
└──────────────┬─────────────┬─────────────┬───────────────────┘
               │             │             │
        ┌──────▼──────┐ ┌────▼────┐ ┌──────▼──────┐
        │  Beats/     │ │ Syslog  │ │   Custom    │
        │  Agents     │ │ Servers │ │  Collectors │
        └─────────────┘ └─────────┘ └─────────────┘
                 │            │            │
        ┌────────▼────────────▼────────────▼────────┐
        │          Fontes de Dados                   │
        │  Endpoints │ Network │ Cloud │ Apps        │
        └────────────────────────────────────────────┘
```

*Fonte: Elaborado pelo autor (2024)*

## **7.3.1 Ontologia da arquitetura avançada**

Se a arquitetura básica possui a ontologia da unidade, e a intermediária a da diferenciação, a arquitetura avançada possui a ontologia da **complexidade distribuída**.
Ela concebe o SIEM como uma **ecologia cognitiva**, na qual múltiplos mecanismos autônomos contribuem para a produção final de conhecimento.

O SIEM, nesse nível, não é uma ferramenta: é um **sistema vivo**, adaptável, resiliente, escalável e sujeito a dinâmicas emergentes.

## **7.3.2 A camada de coleta como rede sensorial distribuída**

Sensores de rede (Zeek, Suricata) e agentes (Wazuh, Beats) constituem uma camada fenomenológica altamente distribuída. Cada nó opera como um órgão perceptivo especializado. Em conjunto, criam uma percepção abrangente sobre processos, comportamentos, fluxos e interações.

## **7.3.3 A camada de enfileiramento como mediadora da entropia**

Message brokers como Kafka ou RabbitMQ desempenham função epistemológica crítica: **regulam o fluxo do conhecimento**, desacoplando ingestão e processamento.
Dessa forma, impedem que picos de eventos comprometam a integridade cognitiva do sistema.

Essa camada não é mero buffer: é um **estabilizador epistêmico**.

## **7.3.4 A camada de processamento como laboratório de interpretação**

Múltiplos processadores operando em paralelo — Logstash, Fluentd, OpenSearch Ingest Pipelines, Graylog pipelines — constituem um laboratório interpretativo no qual:

* logs são enriquecidos com dados de threat intelligence,
* correlações simples são aplicadas,
* campos são normalizados,
* ruído é removido.

Essa etapa prepara o dado para análise profunda. O SIEM começa a interpretar antes mesmo de armazenar.

## **7.3.5 A camada de armazenamento como sistema mnésico de camadas**

A arquitetura hot–warm–cold, citada no documento-base e expandida no documento adicional, é aqui reinterpretada como estratificação da memória.
Hot = memória de curto prazo
Warm = memória de médio prazo
Cold = memória de longo prazo

Esse modelo permite acesso rápido ao presente, contextualização pelo passado recente e investigações profundas no passado remoto.

## **7.3.6 A camada de análise como consciência distribuída**

Ferramentas como HELK, Kibana, OpenSearch Dashboards, notebooks de hunting e mecanismos de UEBA compõem a camada de análise.
É nela que o SIEM deixa de ser uma máquina de observação para se tornar uma máquina de interpretação e descoberta.

Aqui reside a “consciência operacional” do SOC.

## **7.3.7 Vantagens e limitações da arquitetura avançada**

Vantagens:

* escalabilidade praticamente ilimitada,
* resiliência elevada,
* adaptabilidade,
* suporte a big data,
* capacidade analítica profunda,
* preparação para automação (SOAR).

Limitações:

* complexidade extrema,
* alto custo operacional,
* necessidade de engenheiros especializados,
* risco de configuração inadequada comprometer o sistema como um todo.

A arquitetura avançada é, portanto, a expressão mais completa da epistemologia SIEM moderna, mas também a mais exigente.

---

# **8. Regras de Correlação e Hunting**

### *A hermenêutica dos eventos de segurança e a ciência da detecção moderna*

As regras de correlação e as práticas de threat hunting constituem o ápice cognitivo de um SIEM. Depois que a ingestão organiza, o parsing interpreta, a normalização unifica, o armazenamento preserva e os dashboards tornam visível, resta à correlação e ao hunting o papel de transformar dados em **compreensão**, indícios em **hipóteses**, padrões em **explicações**, eventos desconexos em **narrativas de ameaça**.

Os documentos fornecidos apresentam os componentes fundamentais desse processo — contexto, baseline, kill chain, MITRE ATT&CK, lógica de construção de regras, mecanismos de validação, e abordagens para otimização de falsos positivos e negativos. Aqui, tais conceitos são ampliados e reinterpretados de maneira profundamente epistemológica.

## **8.1 Fundamentos Epistemológicos da Correlação**

A correlação é, em sua essência, um processo hermenêutico: ela busca sentido em fragmentos. Assim como um historiador reconstrói fatos a partir de registros parciais, o SIEM reconstrói possíveis ameaças a partir de eventos discretos. Cada log é um vestígio; cada correlação, uma hipótese narrativa.

Do ponto de vista computacional, correlação é a associação de múltiplos eventos ao longo do tempo, espaço e contexto. Do ponto de vista epistemológico, é a **interseção entre o que é observado e o que é inferido**.
Essa dualidade faz da correlação o centro interpretativo do SIEM.

### **8.1.1 O papel do contexto**

A apostila-base enfatiza que a correlação eficaz depende de contexto — identidade, criticidade do ativo, localização, horário, função organizacional, padrões de acesso, vulnerabilidades pré-existentes, tags de risco.
Sem contexto, a correlação é cega; com contexto, ela é interpretativa.

A mesma ação — login fora do horário — pode ser banal ou crítica, dependendo de:

* quem executou,
* em que sistema,
* a partir de onde,
* com qual histórico comportamental,
* em qual momento tático de uma possível campanha adversária.

A correlação, assim, não é binária; é inferencial.

### **8.1.2 Linha de base (baseline) como epistemologia do normal**

Uma das formas mais profundas de correlação envolve a definição de uma baseline do que é considerado comportamento normal.
Essa baseline é um artefato epistemológico: ela constitui uma teoria sobre o “cotidiano” da infraestrutura.

Comportamentos anômalos emergem quando:

* um usuário ultrapassa padrões típicos de volume,
* um host realiza conexões inusuais,
* um processo acessa arquivos atípicos,
* um endpoint executa comandos anômalos,
* um tráfego se desvia do perfil histórico.

A baseline não é apenas estatística; ela é cultural.
Ela define a identidade funcional do ambiente.

### **8.1.3 A cadeia de ataque (Kill Chain) como narrativa sequencial**

A correlação também se fundamenta na Kill Chain — Reconhecimento, Exploração, Elevação de Privilégio, Persistência, Desenvolvimento Lateral, Comando e Controle, Exfiltração.
A apostila-base menciona a importância de entender esta sequência para correlacionar eventos que isoladamente são inofensivos.

Aqui, interpretamos a Kill Chain como **uma teoria narrativa do ataque**: cada fase é um capítulo, e a correlação busca reconstruir a história inteira.

---

## **8.2 MITRE ATT&CK como Ontologia da Ameaça**

O MITRE ATT&CK, conforme explicitado nos documentos fornecidos, é uma base de conhecimento global que estrutura táticas e técnicas adversárias.
Mas aqui, analisamos o MITRE sob um prisma epistemológico: trata-se de uma **ontologia padronizada da malícia**.

### **8.2.1 MITRE como metanarrativa**

O ATT&CK descreve não apenas o que os adversários fazem, mas **como pensam**, **como planejam**, **como executam**. É uma reconstrução conceitual do raciocínio ofensivo.

Ao mapear táticas (objetivo) e técnicas/subtécnicas (método), o MITRE fornece:

* um vocabulário universal para detecção,
* uma estrutura para construir regras de correlação,
* uma métrica de maturidade para SOCs,
* uma base epistemológica para hunting,
* um eixo para avaliação de cobertura defensiva.

O ATT&CK não é apenas uma tabela: é uma teoria operacional do ataque moderno.

### **8.2.2 Mapeamento de regras para técnicas**

Toda regra bem construída deve responder a três perguntas fundamentais:

1. **Qual técnica ATT&CK ela observa?**
2. **Qual evidência empírica permite inferir essa técnica?**
3. **Quais outras técnicas são possivelmente encadeadas?**

O mapeamento permite que a correlação seja orientada por uma teoria do comportamento adversário, e não apenas por assinaturas isoladas.

---

## **8.3 Criação de Regras de Correlação: Metodologia Científica da Detecção**

A construção de regras de correlação é, em essência, uma atividade científica.
O documento-base descreve cinco fases: definição da ameaça, identificação da fonte de dados, desenvolvimento lógico, validação e otimização. Aqui, reinterpretamos essas fases como etapas de um método investigativo.

### **8.3.1 Definição da ameaça: formulação da hipótese**

Antes de escrever uma regra, deve-se formular uma hipótese clara sobre o comportamento malicioso que se deseja detectar.
Essa hipótese deve ser:

* fundamentada no MITRE,
* sustentada por observações históricas,
* coerente com o modelo de ameaça da organização.

Assim como em qualquer ciência, a detecção começa pela formulação de uma tese.

### **8.3.2 Identificação da fonte de dados: seleção empírica**

Uma regra só existe se há evidência empírica.
A fonte de dados (Windows Event Log, Sysmon, Zeek, firewall, EDR, proxy, identidade etc.) determina:

* o nível de observação,
* a granularidade das evidências,
* a sensibilidade da detecção.

Escolher a fonte errada invalida toda a regra.

### **8.3.3 Construção lógica: inferência formal**

A lógica da regra deve refletir:

* temporalidade (janela de tempo),
* frequência (contagem de eventos),
* causalidade (sequencialidade),
* condicionalidade (se... então),
* granularidade (campos específicos).

A criação de regras é uma atividade formal, semelhante à construção de modelos teóricos.

### **8.3.4 Teste e validação: experimentação científica**

A regra deve ser testada contra:

* dados históricos,
* simulações como Atomic Red Team,
* ambientes controlados de laboratório.

Esse processo corresponde ao momento experimental da ciência.

### **8.3.5 Otimização: redução de ruído cognitivo**

O documento-base menciona a necessidade de reduzir falsos positivos e falsos negativos.
Na prática, isso implica:

* ajustar parâmetros,
* refinar condições,
* incorporar contexto,
* observar padrões reais de operação.

A detecção é sempre aperfeiçoada iterativamente.

### **Exemplo Prático 1: Detecção de Brute Force**

**Hipótese:** Múltiplas tentativas de autenticação falhadas seguidas de sucesso indicam possível brute force.

**Técnica MITRE:** T1110 - Brute Force

**Regra (pseudo-código):**
```
IF count(event.category: "authentication" AND event.outcome: "failure" 
   AND user.name: * AND source.ip: *)
   OVER 5 minutes >= 10
AND followed by
   event.category: "authentication" AND event.outcome: "success"
   AND same(user.name) AND same(source.ip)
   WITHIN 2 minutes
THEN
   ALERT "Possível Brute Force Bem-Sucedido"
   SEVERITY: High
   MITRE: T1110
```

*Figura 8.1 - Lógica de detecção de brute force bem-sucedido*

### **Exemplo Prático 2: Detecção de Lateral Movement**

**Hipótese:** Autenticação bem-sucedida em múltiplos hosts em curto período sugere movimento lateral.

**Técnica MITRE:** T1021 - Remote Services

**Regra (pseudo-código):**
```
IF count_distinct(destination.ip 
   WHERE event.category: "authentication" 
   AND event.outcome: "success"
   AND same(user.name)
   AND source.ip: internal_network)
   OVER 10 minutes >= 5
THEN
   ALERT "Possível Movimento Lateral"
   SEVERITY: Critical
   MITRE: T1021
```

*Figura 8.2 - Lógica de detecção de movimento lateral*

---

## **8.4 Threat Hunting: A Ciência Ativa da Busca pela Ameaça**

Se a correlação é reativa — interpretando aquilo que já aconteceu —, o hunting é proativo: ele busca aquilo que não foi explicitamente sinalizado, aquilo que não acionou regra alguma, aquilo que está nas entrelinhas do ambiente.

A apostila-base menciona hunting como prática essencial, mas aqui expandimos para sua dimensão científica.

### **8.4.1 Hunting como epistemologia da suspeita**

O hunter opera sob a hipótese de que algo já deu errado — mesmo que o SIEM ainda não o tenha detectado. Ele trabalha por:

* suposições fundamentadas,
* reconstruções de comportamento,
* exploração de anomalias,
* análise estatística,
* inferência contextual.

É uma atividade investigativa que combina ciência, arte e heurística.

### **8.4.2 Hunting orientado por hipóteses (HOH)**

Esta abordagem consiste em formular hipóteses baseadas em MITRE ATT&CK:

> “Se a técnica T1059.001 (PowerShell malicioso) é comum em campanhas adversárias contemporâneas, então deve ser possível identificar execuções suspeitas mesmo que não acionem regras preexistentes.”

O hunter, então, busca evidências no SIEM, no EDR, na rede, em Sysmon, nos logs de identidade.

### **8.4.3 Hunting orientado por comportamento (BOH)**

Aqui, a pergunta central é:

> “O que está diferente do normal?”

Essa abordagem depende profundamente de baseline, estatística e padrões históricos.

### **8.4.4 Hunting retrospectivo e prospectivo**

O hunting retrospectivo explora dados antigos em busca de indícios de incidentes anteriores.
O hunting prospectivo busca prever comportamentos futuros com base em tendências.

Ambos dependem de vastos volumes de dados, reforçando a importância das arquiteturas avançadas descritas anteriormente.

---

# **9. Conclusão Geral**

### *O SIEM como instrumento epistemológico, arquitetônico e estratégico da segurança moderna*

Ao longo deste trabalho, reconstruiu-se a compreensão dos Sistemas de Gerenciamento de Informações e Eventos de Segurança (SIEM) não apenas como ferramentas tecnológicas, mas como **máquinas epistemológicas**, capazes de gerar conhecimento, interpretar eventos, reconstruir narrativas, sustentar investigações e possibilitar a defesa ativa de ambientes computacionais complexos.

A análise empreendida, fundamentada e ampliada sobre os documentos fornecidos, procurou aproximar o SIEM de seu caráter mais profundo: **uma infraestrutura cognitiva**, cuja função vai muito além da coleta e apresentação de logs. Ele constitui, na prática, o “sistema nervoso central” da segurança organizacional, exercendo um papel simultaneamente operacional, investigativo, estratégico e hermenêutico.

## **9.1 O SIEM como dispositivo de interpretação da realidade digital**

Toda organização moderna vive inserida em um ambiente computacional massivo, heterogêneo, distribuído e potencialmente caótico. Nesse contexto, cada evento — seja um login, uma conexão de rede, uma mudança de arquivo, um pacote malformado ou uma execução de processo — torna-se um fragmento de realidade.
O SIEM atua como mecanismo de reconstrução dessa realidade, dando forma, coerência e interpretação ao fluxo incessante de eventos.

Assim, o SIEM amplia a capacidade cognitiva humana: oferece ao analista instrumentos para enxergar padrões, reconstruir histórias e identificar movimentos adversários antes que causem danos materiais ou comprometam a integridade dos dados.

Esse caráter interpretativo enfatiza a natureza epistemológica do sistema.
Não se trata apenas de coletar — trata-se de **conhecer**.

## **9.2 A evolução arquitetônica do SIEM como evolução da própria segurança**

A passagem das arquiteturas **básica → intermediária → avançada** revela mais do que simples amadurecimento tecnológico: revela uma profunda transformação epistemológica.
Em sua forma básica, o SIEM aparece como ferramenta monolítica; em sua forma intermediária, como ecossistema distribuído; em sua forma avançada, como **sistema cognitivo complexo**, composto por múltiplos agentes especializados, mediadores de fluxo, mecanismos paralelos de interpretação e estruturas de memória estratificada.

A arquitetura avançada — com message brokers, pipelines paralelos, memória hot–warm–cold e mecanismos de automação — é, na prática, a materialização de um conceito mais amplo: o conceito de **Defesa Baseada em Dados** (*Data-Driven Security*).

Nela, a segurança deixa de ser um conjunto de medidas reativas e se torna um campo de análise contínua, sustentado por dados, modelos e inferências.

## **9.3 As ferramentas Open Source como expressões de epistemologias distintas**

A análise aprofundada das principais soluções Open Source — **Wazuh, Elastic Stack, OpenSearch, Security Onion, OSSIM, Graylog, HELK, MozDef** — demonstra que cada uma delas articula uma filosofia própria:

* **Wazuh** privilegia o host, aproximando-se de um modelo EDR+SIEM.
* **Elastic/OpenSearch** privilegiam a observabilidade e a escalabilidade massiva.
* **Security Onion** privilegia a fenomenologia da rede.
* **OSSIM** privilegia a visão integrada clássica.
* **Graylog** privilegia a velocidade interpretativa.
* **HELK** privilegia a ciência de dados aplicada à segurança.
* **MozDef** privilegia a automação e a ação coordenada.

Essas epistemologias não competem — elas se complementam.
A maturidade de um SOC decorre da capacidade de **compor múltiplos modos de conhecer**, integrando camadas diversas de telemetria, raciocínio e inferência.

Um ambiente verdadeiramente resiliente não depende de uma única ferramenta, mas da orquestração destas epistemologias heterogêneas em um sistema coeso.

## **9.4 Correlação, Hunting e MITRE ATT&CK como tripé da detecção moderna**

A correlação, em sua forma mais profunda, é um processo de interpretação narrativa dos eventos.
O hunting é a ciência ativa que busca aquilo que não foi revelado.
O MITRE ATT&CK é a ontologia da ameaça que orienta e estrutura ambos.

Juntos, estes três elementos formam o tripé epistemológico do SOC moderno:

* A **correlação** transforma eventos dispersos em hipóteses.
* O **hunting** transforma hipóteses em descobertas.
* O **MITRE** transforma descobertas em modelos universais.

Esse ciclo é contínuo, retroalimentado e cumulativo.
Cada descoberta amplia a base de conhecimento; cada base de conhecimento aprimora a detecção; cada detecção fortalece a resiliência organizacional.

## **9.5 O papel do SIEM na governança, compliance e investigação**

Além de sua dimensão técnica, o SIEM cumpre funções estruturais no âmbito da governança e conformidade:

* garante rastreabilidade,
* preserva evidências,
* suporta auditorias,
* fortalece controles regulatórios,
* sustenta investigações internas,
* consolida dados críticos para processos jurídicos.

O SIEM é, ao mesmo tempo, caixa-preta, testemunha, arquivo histórico e ferramenta de análise forense.
Sua presença é, portanto, indispensável para qualquer organização que busque maturidade em segurança.

## **9.6 A maturidade do SOC como maturidade do próprio SIEM**

O SOC não é maduro porque possui analistas experientes; ele se torna maduro **porque possui estruturas epistemológicas adequadas** para interpretar o ambiente.
Essa maturidade manifesta-se em:

* pipelines robustos de ingestão,
* normalização universal,
* regras de correlação continuamente refinadas,
* hunting sistemático,
* dashboards orientados a contexto,
* automação de resposta,
* integração com threat intelligence,
* e alinhamento contínuo ao MITRE ATT&CK.

O SIEM, assim, é o reflexo da cultura de segurança da organização.
Onde há superficialidade, o SIEM será ruidoso.
Onde há rigor, o SIEM será preciso.
Onde há maturidade, o SIEM será **inteligente**.

## **9.7 A síntese final: o SIEM como fundamento da defesa moderna**

Conclui-se, portanto, que o SIEM não deve ser visto como um simples depositório de logs ou ferramenta de alerta, mas como o **coração analítico da defesa cibernética contemporânea**.
Ele é o sistema por meio do qual:

* o passado é preservado,
* o presente é interpretado,
* o futuro é antecipado,
* e a ameaça é compreendida.

Em um mundo no qual dados fluem incessantemente, ataques tornam-se mais sofisticados e fronteiras tecnológicas dissolvem-se entre ambientes locais e nuvens distribuídas, o SIEM é o mecanismo lógico, técnico e epistemológico que permite transformar caos em compreensão, sinais em significado, atividade em narrativa, e ameaça em resposta.

A maturidade digital de uma organização depende diretamente da maneira como ela **observa, interpreta e age** — e o SIEM é o instrumento que articula essas três dimensões.

Concluímos, assim, que o SIEM é mais que tecnologia:
é **inteligência institucional**.

---

# **Referências Bibliográficas**

## **Frameworks e Padrões**

MITRE ATT&CK. **MITRE ATT&CK Framework**. Disponível em: https://attack.mitre.org/. Acesso em: 2024.

NIST. **NIST Cybersecurity Framework (CSF)**. National Institute of Standards and Technology, 2018.

NIST. **SP 800-92: Guide to Computer Security Log Management**. 2006.

ISO/IEC. **ISO/IEC 27001:2013 - Information Security Management Systems**. 2013.

## **Documentação Técnica**

Elastic. **Elastic Stack Documentation**. Disponível em: https://www.elastic.co/guide/. Acesso em: 2024.

Wazuh. **Wazuh Documentation**. Disponível em: https://documentation.wazuh.com/. Acesso em: 2024.

OpenSearch. **OpenSearch Documentation**. Disponível em: https://opensearch.org/docs/. Acesso em: 2024.

Security Onion Solutions. **Security Onion Documentation**. Disponível em: https://docs.securityonion.net/. Acesso em: 2024.

## **Literatura Acadêmica e Profissional**

GARTNER. **Market Guide for Security Information and Event Management**. Gartner Research, 2023.

KENT, Karen; SOUPPAYA, Murugiah. **Guide to Computer Security Log Management**. NIST Special Publication 800-92, 2006.

CHUVAKIN, Anton; SCHMIDT, Kevin; PHILLIPS, Chris. **Logging and Log Management: The Authoritative Guide to Understanding the Concepts Surrounding Logging and Log Management**. Syngress, 2012.

BIANCO, David J. **The Pyramid of Pain**. Enterprise Detection & Response, 2013.

---

# **Apêndices**

## **Apêndice A: Checklist de Implementação de SIEM**

### **Fase 1: Planejamento**
- [ ] Definir objetivos e casos de uso
- [ ] Identificar stakeholders e equipe técnica
- [ ] Estimar volume de EPS
- [ ] Determinar requisitos de retenção
- [ ] Avaliar orçamento (hardware, software, pessoal)

### **Fase 2: Arquitetura**
- [ ] Escolher modelo (básico/intermediário/avançado)
- [ ] Dimensionar infraestrutura
- [ ] Planejar rede e segmentação
- [ ] Definir modelo de alta disponibilidade

### **Fase 3: Implementação**
- [ ] Instalar componentes core
- [ ] Configurar coletores/agentes
- [ ] Implementar parsing e normalização
- [ ] Estabelecer políticas de retenção (ILM)
- [ ] Configurar autenticação e RBAC

### **Fase 4: Operacionalização**
- [ ] Criar regras de correlação iniciais
- [ ] Desenvolver dashboards essenciais
- [ ] Estabelecer procedimentos de resposta
- [ ] Treinar equipe operacional
- [ ] Definir SLAs e métricas

### **Fase 5: Maturação**
- [ ] Ajustar regras (reduzir FP/FN)
- [ ] Implementar threat intelligence
- [ ] Iniciar programa de hunting
- [ ] Mapear cobertura MITRE ATT&CK
- [ ] Integrar com SOAR

## **Apêndice B: Glossário Expandido**

**Aggregation**: Processo de consolidação de múltiplos eventos similares em um único registro representativo.

**Baseline**: Perfil comportamental normal de usuários, sistemas ou redes, usado como referência para detecção de anomalias.

**Cold Storage**: Camada de armazenamento de baixo custo para dados raramente acessados, com alta latência.

**Correlation**: Associação lógica de múltiplos eventos para identificar padrões complexos indicativos de ameaças.

**Hot Storage**: Camada de armazenamento de alta performance para dados recentes e frequentemente consultados.

**Hunting**: Busca proativa por ameaças não detectadas pelos mecanismos automatizados do SIEM.

**Normalization**: Transformação de logs heterogêneos em formato padronizado para facilitar análise.

**Parsing**: Extração de campos estruturados de dados não-estruturados ou semi-estruturados.

**Retention**: Período durante o qual os dados são mantidos no SIEM antes de serem arquivados ou excluídos.

**UEBA** (User and Entity Behavior Analytics): Análise comportamental baseada em machine learning para detecção de anomalias.

---

## **Apêndice C: Recursos Adicionais**

### **Comunidades e Fóruns**
- Reddit: r/siem, r/netsec, r/AskNetsec
- Discord: Security Onion, TheHive Project, Wazuh
- Slack: ThreatHunting, Detection Engineering

### **Treinamentos e Certificações**
- GIAC Certified Intrusion Analyst (GCIA)
- GIAC Security Operations Certified (GSOC)
- Certified SOC Analyst (CSA)
- Splunk/Elastic Certified Engineer

### **Laboratórios e Ambientes de Prática**
- DetectionLab
- Security Onion Lab-in-a-Box
- Wazuh Docker Lab
- Elastic Cloud Trial

### **Conjuntos de Dados para Teste**
- DARPA Intrusion Detection Dataset
- CICIDS Dataset
- Mordor Dataset (hunting scenarios)
- EVTX Attack Samples

---

**Fim da Apostila**

**Versão:** 1.0  
**Data:** 2024  
**Instituição:** Instituto Nacional de Telecomunicações - Inatel  
**Autor:** Vinícius Araujo

---
