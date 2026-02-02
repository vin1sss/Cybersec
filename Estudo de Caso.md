# Estudo de Caso: Ataque Cibernético à Rede Elétrica Ucraniana (2015)

## Visão geral e contexto

Em **23 de dezembro de 2015**, três distribuidoras de energia na Ucrânia sofreram um apagão causado deliberadamente por um ataque cibernético.  O incidente é considerado o primeiro ataque público bem‑sucedido contra **sistemas de controle de uma concessionária elétrica** e foi conduzido pelo grupo Sandworm (associado ao Estado russo), utilizando o **malware BlackEnergy 3** e módulos destrutivos como o **KillDisk**.  
O ataque ocorreu durante a **Guerra Russo‑Ucraniana (2014–presente)** e foi amplamente atribuído a um **grupo de Ameaça Persistente Avançada (APT) russo conhecido como Sandworm**, tornando‑se a **primeira vez que um ataque cibernético provocou uma interrupção de energia elétrica**.  
A interrupção durou cerca de seis horas, afetou aproximadamente **225 000 consumidores**, derrubou **50 subestações** e causou a perda de cerca de **130 MW** de energia.  Devido à destruição dos controladores e à interrupção das comunicações, a recuperação só foi possível com **operações manuais nas subestações**.

O ataque foi cuidadosamente cronometrado: ocorreu durante a **temporada de festas de fim de ano**, quando apenas equipes de plantão (“skeleton crew”) estavam presentes nos centros de controle.  Essa escolha dificultou a resposta rápida e aumentou o impacto do incidente.

Embora a interrupção de energia tenha sido o evento mais notório, investigações posteriores mostram que a campanha não se restringiu ao setor elétrico.  Relatórios da Booz Allen indicam que os mesmos agentes já haviam atacado **outros setores de infraestrutura crítica na Ucrânia**, incluindo agências governamentais, emissoras de mídia, empresas ferroviárias e operações de mineração.  Esse histórico demonstra que o objetivo da campanha era amplo e que a cadeia de ataque aplicada ao setor elétrico poderia ser adaptada a outros ambientes industriais.

Os investigadores identificaram uma campanha clandestina que começou **no início de 2014/2015**.  Agentes de ameaças ganharam acesso às redes corporativas das distribuidoras usando **spear phishing**, implantaram backdoors, mapearam as redes e moveram‑se lateralmente até chegar ao sistema SCADA.  A execução do ataque envolveu não apenas a abertura remota de disjuntores, mas também a **interrupção de linhas telefônicas (TDoS)** e a **destruição de sistemas** via KillDisk para atrasar a resposta.  O caso tornou‑se referência para as equipes de segurança de Infraestrutura Crítica (ICS) ao mostrar como técnicas de TI relativamente comuns (phishing, roubo de credenciais, malware modular) podem causar **efeitos físicos** em sistemas elétricos.

## Panorama da rede e arquitetura alvo

As distribuidoras ucranianas operavam em um modelo comum de **redes corporativas conectadas a ambientes de controle**.  A infraestrutura podia ser dividida em:

1. **Rede corporativa/ de TI** – usava sistemas Windows para tarefas administrativas, recursos de escritório e e‑mail.
2. **Rede de controle** – incluía Servidores de Gerenciamento de Distribuição (DMS), servidores de comunicação, estações de trabalho com Interface Homem–Máquina (IHM/HMI) e controladores SCADA que supervisionavam e comandavam subestações e disjuntores.
3. **Dispositivos de campo** – controladores remotos (RTUs), conversores **serial‑para‑Ethernet** (como Moxa UC 7408‑LX Plus e iRZ‑RUH2) e **disjuntores** que controlam o fluxo de energia.  Esses dispositivos forneciam a interface de dados entre a rede de controle e os equipamentos elétricos.
  
Investigadores relataram que a maior parte dos clientes desligados estava sob responsabilidade da **Prykarpattyaoblenergo**, onde **30 subestações** foram desativadas (incluindo **7 subestações de 110 kV** e **23 de 35 kV**).  Outras duas distribuidoras, **Chernivtsioblenergo** e **Kyivoblenergo**, também tiveram subestações comprometidas, porém em menor escala.  Esse nível de detalhamento demonstra que os invasores conseguiram atingir dispositivos de diferentes tensões e regiões, reforçando a complexidade do ataque.

Embora exista separação lógica (firewalls, VPN), a conexão entre a TI e a rede de controle permitia que usuários administrativos acessassem sistemas operacionais de HMI via **VPN** e ferramentas como **Remote Desktop Protocol (RDP) e Radmin**.  O ataque explorou exatamente esse elo.

## Cadeia de ataque (kill chain) e etapas

A campanha seguiu uma sequência semelhante ao modelo **ICS Kill Chain**, com atividades de **reconhecimento**, **preparação**, **acesso inicial**, **instalação**, **descoberta**, **movimentação lateral**, **execução** e **ações adicionais**.  A seguir, cada etapa é descrita em detalhes, com ênfase em TTPs (táticas, técnicas e procedimentos) observados.

### 1. Reconhecimento e preparação

- **Exploração de vetores de entrada:** Os agentes analisaram a superfície exposta das distribuidoras, avaliando portais, endereços de e‑mail e usuários que poderiam ser alvos de spear phishing.  O relatório do E‑ISAC estima que o grupo obteve acesso **pelo menos seis meses antes** do apagão.
- **Definição da estratégia de invasão:** Após estudar a organização, os atacantes escolheram o **phishing direcionado** como vetor principal, sabendo que documentos de ofício com macros eram plausíveis e que as vítimas usavam Windows e MS Office.
- **Vetores adicionais considerados:** abuso de credenciais, varredura e enumeração (port scanning) e exploração de vulnerabilidades nos conversores serial‑para‑Ethernet.

### 2. Acesso inicial (Entrega)

- **Campanhas de spear phishing:** Desde **março de 2015** circulavam e‑mails forjados que imitavam correspondências do Ministério de Energia da Ucrânia.  Os e‑mails continham planilhas (Додаток1.xls) ou documentos Word (por exemplo, *$RR143TB.doc*) com macros maliciosas.  Quando a vítima abria o documento e **habilitava macros**, a macro executava um dropper que instalava o **malware BlackEnergy 3 (BE3)**.
- **Engenharia social:** Os e‑mails foram enviados para indivíduos específicos dentro das distribuidoras e de outras organizações (como mídia e ferrovias) como parte de uma campanha de reconhecimento ampliada.
- **Tempo de permanência:** A infiltração inicial possivelmente ocorreu entre março e maio de 2014 e continuou até janeiro de 2016, permitindo que os atacantes se mantivessem na rede por meses.

### 3. Instalação de backdoors

- **Malware BlackEnergy 3:** Após a habilitação das macros, o **BE3** era instalado no computador da vítima.  O BE3 é modular; sua função principal é estabelecer uma **conexão com servidores de comando e controle (C2)** utilizando **HTTP POST** e modificar configurações do registro para reduzir a segurança do Internet Explorer.
* **Plugins e persistência:** O BE3 é acompanhado de diversos **plugins .dll** que fornecem funcionalidades especializadas.  O plugin **SI.dll** executa uma varredura detalhada no sistema, utilizando utilitários nativos (`systeminfo`, `tasklist`, `ipconfig`, `netstat` e `route`) para coletar informações de configuração, lista de processos ativos, rotas de rede e tabelas de roteamento.  Ele também identifica softwares instalados e **extrai senhas de gerenciadores de credenciais**, navegadores e clientes de e‑mail, inclusive credenciais de mensageiros e de acesso remoto.  Outros plugins incluem **PS.dll**, que busca e exfiltra credenciais, e **KI.dll**, responsável por registrar teclas digitadas (keylogger).  Esse arsenal permitiu aos invasores roubar credenciais válidas, monitorar atividades e preparar terreno para a instalação de outras ferramentas.
* **Backdoor e RATs adicionais:** Além do BE3, os invasores implantaram outros *Remote Access Trojans*.  Uma versão modificada do **Dropbear SSH**, entregue por meio de scripts Visual Basic, foi configurada para escutar na porta **6789** e continha **dois backdoors internos** (chaves públicas codificadas e credenciais embutidas), facilitando o acesso remoto sem autenticação legítima.  O uso de um servidor SSH de código aberto permitia que os atacantes mantivessem persistência de forma discreta e evitassem a detecção prematura.
- **Acesso garantido:** O backdoor dava acesso contínuo mesmo se senhas fossem alteradas.  Isso permitiu que o grupo reconectasse à rede corporativa sempre que necessário.

### 4. Descoberta (reconhecimento interno)

- **Mapeamento da rede:** Com os backdoors ativos, os invasores passaram meses explorando a infraestrutura das distribuidoras.  Eles identificaram servidores **Active Directory**, estações de trabalho de operadores de SCADA, roteadores VPN e conversores serial‑para‑Ethernet.
- **Coleta de credenciais:** Ferramentas de força bruta, plugins de BE3 e sniffers de tráfego foram usados para **comprometer credenciais de usuários e administradores**.  Em alguns casos, contas de domínio adicionais foram criadas para garantir privilégios elevados.
- **Integração ao domínio:** Os atacantes acessaram controladores de domínio para obter credenciais de VPN, que são usadas por operadores para acessar a rede de controle.

### 5. Movimentação lateral

- **Escalada de privilégios:** Com credenciais válidas, o grupo usou ferramentas de administração remota (RDP, Radmin e SSH) para se mover das redes corporativas para as redes de controle, através dos túneis VPN habilitados.
- **Acesso ao Centro de Controle:** O grupo conseguiu estabelecer sessões nas estações de trabalho HMI, que possuem a interface gráfica utilizada pelos operadores para abrir e fechar disjuntores e monitorar o sistema.  Ao obter acesso a múltiplas HMIs, os atacantes estavam prontos para controlar remotamente os disjuntores.

### 6. Execução do ataque

- **Data e hora:** A operação final ocorreu em **23 de dezembro de 2015**.  Três ondas de ataque foram observadas: às **15h30**, **15h31** e **16h**.
- **Comprometimento prévio de IHMs:** Antes de iniciar o ataque final, os agentes maliciosos **reconheceram e comprometeram 17 Interfaces Homem–Máquina (IHMs)**, preparando o terreno para controlar os disjuntores de maneira coordenada.
- **Controle remoto das IHM:** Os invasores usaram ferramentas de acesso remoto para interagir com os sistemas SCADA e **abriram disjuntores de forma manual**, na interface gráfica.  Os operadores podiam ver os comandos sendo executados, mas não conseguiam controlar o mouse e teclado.
- **Vasta interrupção:** Pelo menos **57 subestações** tiveram os disjuntores abertos, sendo 27 da Prykarpattyaoblenergo e 7 subestações de 110 kV e 23 de 35 kV da Kyivoblenergo.  Várias cidades sofreram apagões completos ou parciais.
- **Manipulação de credenciais:** Pouco antes da execução, os atacantes **alteraram senhas de usuários** para impedir a recuperação imediata.

### 7. Ações adicionais para amplificar o impacto

* **Atualização maliciosa de conversores serial‑para‑Ethernet:** Após abrir os disjuntores, os invasores enviaram uma **atualização de firmware uniforme** a dezenas de conversores **Moxa UC 7408‑LX Plus** e **iRZ‑RUH2** instalados nas subestações.  O novo firmware corrompeu permanentemente os equipamentos e **severou a comunicação entre o centro de controle e os dispositivos de campo**.  Investigadores observaram que pelo menos **16 subestações** foram isoladas como resultado dessas atualizações.  Embora esses conversores permitam atualizações remotas legítimas, ambos apresentavam vulnerabilidades: o Moxa continha diversos problemas de execução arbitrária e negação de serviço, e seu código‑fonte estava publicamente disponível; o iRZ‑RUH2 permitia aceitar firmware não assinado.  Com as credenciais roubadas, os atacantes exploraram essas falhas para enviar firmware malicioso e “queimar as pontes”, impedindo qualquer comando remoto de reenergização.
* **TDoS (Telephony Denial of Service):** Para atrapalhar a resposta, os invasores realizaram um **ataque de negação de serviço telefônico**.  Pouco antes da abertura dos disjuntores, os call centers de pelo menos uma distribuidora receberam **milhares de ligações robóticas supostamente originadas de Moscou**, saturando as linhas.  Este ataque, similar a um DoS em redes de dados, teve o objetivo de bloquear a comunicação com os clientes e ocultar a extensão do apagão.  Embora detalhes técnicos não sejam públicos, o relatório sugere que **ferramentas de telefonia de código aberto**, como **Asterisk IP PBX** e geradores SIP, podem ser usadas para automatizar chamadas; serviços pagos de TDoS também são comercializados em fóruns clandestinos.  Além de impedir que clientes relatassem a falta de energia, o ataque aumentou a frustração e desorientação da população.
- **Desativação de fontes de alimentação ininterrupta (UPS):** Os atacantes programaram remotamente algumas UPS para desligar, derrubando servidores de comunicação e data centers.
- **Execução do malware KillDisk:** Em seguida, executaram o **KillDisk**, que sobrescreveu o **Master Boot Record** e destruiu dados em servidores e estações de trabalho, tornando‑os inoperáveis.  A destruição concentrou‑se principalmente na rede corporativa, mas pelo menos um controlador remoto (RTU) também foi inutilizado.

* **Variações do KillDisk e sabotagem de sistemas industriais:** Pesquisadores da ESET identificaram que o **KillDisk** usado no ataque era uma variante ajustada para sabotagem de Sistemas de Controle Industrial.  Além de apagar arquivos e tornar computadores inoperáveis, essa variante podia **definir um atraso temporário** antes de ativar a carga destrutiva e tentava **encerrar e sobrescrever executáveis** associados a plataformas industriais, como os processos `komut.exe` e `sec_service.exe`.  O `sec_service.exe` está relacionado a softwares de comunicação **serial‑para‑Ethernet**, de modo que sua eliminação dificultava a recuperação de conectividade.  Estes recursos mostram que o KillDisk foi projetado para **sabotar dispositivos industriais e comunicação serial**, não apenas para causar indisponibilidade.

### Técnicas MITRE ATT&CK associadas

A campanha de 2015 foi posteriormente mapeada no **MITRE ATT&CK**, que cataloga táticas e técnicas usadas por grupos de ameaças.  As técnicas abaixo (domínio **Enterprise**) ajudam a entender como cada etapa do ataque foi executada:

- **T1566.001 – Spear Phishing via Attachment:** os atacantes enviaram e‑mails direcionados com documentos do Office contendo macros maliciosas para obter acesso inicial.
- **T1204.002 – User Execution: Malicious File:** a intrusão dependia de que o destinatário habilitasse macros, permitindo a execução do dropper e instalação do BE3.
- **T1071.001 – Application Layer Protocol (Web Protocols):** o BE3 comunicava‑se com o servidor C2 por meio de requisições HTTP POST.
- **T1133 – External Remote Services:** os invasores utilizaram **VPNs**, **RDP**, **Radmin** e **SSH** para acessar a rede de controle e as IHMs.
- **T1105 – Ingress Tool Transfer:** ferramentas adicionais, como o Dropbear modificado e scripts, foram transferidas para as máquinas comprometidas para garantir acesso persistente.
- **T1056.001 – Input Capture: Keylogging:** o plugin **KI.dll** capturou teclas digitadas e credenciais dos usuários.
- **T1112 – Modify Registry:** o BE3 modificou entradas no registro do Windows para reduzir a segurança do navegador e estabelecer persistência.
- **T1070.004 – Indicator Removal (File Deletion):** o dropper e outras ferramentas apagaram seus próprios rastros (arquivos e logs) após a instalação.

Estas técnicas ilustram como uma cadeia de ataque bem coordenada pode explorar fraquezas humanas e técnicas em ambientes corporativos e de controle.

## Impacto do ataque

- **Duração do apagão:** Entre três e seis horas, dependendo da região.
- **Consumidores afetados:** Cerca de **225 000 clientes**.
- **Número de subestações impactadas:** Pelo menos **50** de acordo com o estudo original e **57** segundo investigações posteriores.
- **Energia perdida:** Aproximadamente **130 MW**.
- **Consequências secundárias:** Destruição de equipamentos de rede, interrupção dos centros de atendimento e aumento do tempo de restauração; aumento dos custos de recuperação devido à substituição de conversores e servidores.

## Lições aprendidas e recomendações

O caso ucraniano mostra que ataques a **infraestruturas críticas** podem ser realizados com técnicas relativamente simples quando as redes de TI e de operação estão interligadas.  Entre as principais lições citadas por especialistas da **CIP/HS** e do **E‑ISAC** estão:

- **Treinamento e conscientização de usuários:** O vetor inicial foi o spear‑phishing.  Organizações devem criar **programas de simulação de phishing** e reforçar treinamentos que ensinem a identificar mensagens suspeitas.
- **Ferramentas de inspeção de e‑mail:** Implementar **sandboxing** ou filtros que analisem anexos e links antes de entregá‑los ao destinatário.
- **Segmentação e segregação de redes:** Isolar a rede de TI da rede de controle, utilizando firewalls, VLANs e DMZs.  O acesso remoto aos sistemas de controle deve ser mínimo e baseado em **VPNs com autenticação multifator**.
- **Monitoramento e detecção:** Implementar sistemas de detecção de intrusão (IDS) tanto na rede corporativa quanto na de controle.  Monitorar logs para detectar **movimentação lateral** e anomalias de autenticação; o acesso inicial durou meses sem ser detectado.
- **Gerenciamento de credenciais:** Uso de **senhas fortes**, rotação periódica e privilegiar a criação de **contas limitadas**.  Monitorar contas administrativas para detectar adições não autorizadas.
- **Proteção de dispositivos de campo:** Atualizar firmware de conversores e RTUs regularmente e validar assinaturas digitais antes de instalação; limitar o número de dispositivos expostos à internet.
- **Planos de contingência:** Dispor de procedimentos para operar subestações manualmente e restabelecer rapidamente comunicações quando sistemas de supervisão forem comprometidos.  Exercícios regulares de resposta a incidentes ajudam a identificar fragilidades.

## Conclusão

O apagão cibernético de 2015 na Ucrânia marcou um divisor de águas no campo da segurança de sistemas de controle industrial.  O ataque demonstrou que **uma campanha bem coordenada combinando phishing, malware modular e exploração de credenciais** pode transpor a barreira entre TI e infraestrutura operacional e **causar efeitos físicos reais**.  Ao dividir o ataque em fases – reconhecimento, acesso inicial, instalação, descoberta, movimentação lateral, execução e ações de impacto – fica claro que existiam **diversas oportunidades de detecção e mitigação**.  A implementação de controles técnicos (segmentação, autenticação multifator, monitoramento) aliados a uma forte cultura de segurança entre os funcionários é essencial para reduzir o risco de ataques semelhantes.
