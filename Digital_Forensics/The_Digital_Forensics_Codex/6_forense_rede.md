# 6 Perícia forense em redes de computadores

Enquanto a análise de discos e sistemas revela o que ocorreu localmente, a **forense de redes** foca na detecção e reconstrução de eventos que ocorrem **em trânsito**.  Ela é fundamental para entender como um ataque foi executado, identificar comunicações entre vítimas e atacantes e preservar evidências de intrusões.

## 6.1 Definição e objetivos

A forense de redes envolve capturar, registrar e analisar o tráfego de rede para **coletar informações e identificar incidentes**【70435781892618†L81-L90】.  O processo inclui detectar anomalias, preservar cópias de logs e pacotes, examinar o tráfego para reconstruir eventos, localizar vulnerabilidades exploradas e apoiar a resposta a incidentes【70435781892618†L95-L116】.  Entre os objetivos estão:

- **Identificar a origem e o alvo de ataques** (por exemplo, endereços IP, portas, países).  
- **Reconstruir sequências de comunicação** para entender a cronologia e o vetor de ataque.  
- **Detectar exfiltração de dados** ou espionagem.  
- **Monitorar desempenho e disponibilidade** da rede.  
- **Coletar evidências admissíveis em tribunal**, garantindo integridade e cadeia de custódia.

## 6.2 Coleta e preservação de dados

1. **Logs de dispositivos de rede**: roteadores, firewalls, IDS/IPS, proxies e servidores registram conexões, fluxos e alertas.  É importante configurar um sistema de *log centralizado* para evitar perda de dados e registrar carimbos de tempo precisos (sincronizados via NTP).  
2. **Captura de pacotes (packet capture)**: ferramentas como *Wireshark*, *TCPDump* ou *TShark* permitem capturar pacotes brutos e exportá‑los em formato PCAP.  A captura deve ser feita preferencialmente em um espelhamento de porta (SPAN) ou TAP para não afetar a rede.  
3. **Fluxos de rede (NetFlow/IPFIX)**: ao invés de capturar todo o conteúdo, fluxos resumem estatísticas (IP origem/destino, portas, número de pacotes/bytes, tempo de início/fim).  Úteis para análise de tráfego em larga escala.  
4. **Logs de sistemas**: servidores web, DNS, DHCP e correio eletrônico registram requisições e podem complementar a análise de pacotes.  
5. **Preservação e hash**: após a coleta, os arquivos (PCAP, logs) devem ser armazenados de forma imutável e ter seus hashes calculados para garantir integridade【38030209527614†L266-L287】.  A documentação deve descrever o ponto de coleta, horário e configurações.

## 6.3 Análise de tráfego

1. **Filtragem e triagem**: inicialmente, filtra‑se o tráfego por endereços IP, portas ou protocolos relevantes para o incidente.  Ferramentas como Wireshark permitem aplicar filtros BPF (Berkeley Packet Filter).  
2. **Reconstituição de sessões**: pacotes TCP podem ser remontados para extrair arquivos (ex.: HTTP, FTP) ou fluxos de dados.  Ferramentas como *NetworkMiner* e *TShark* automatizam a remontagem.  
3. **Detecção de anomalias**: variações incomuns no volume de tráfego, protocolos ou destinos podem indicar ataques (DDoS, varredura de portas, beaconing de malwares).  Ferramentas de análise estatística ou de aprendizado de máquina ajudam a detectar padrões.  
4. **Análise de intrusão**: examina‑se a carga útil para identificar exploits, comandos e malware.  Signaturas de IDS (Snort, Suricata) ou regras YARA podem detectar strings maliciosas.  
5. **Correlacionar com logs**: cruzar pacotes com logs de firewall, DNS e host ajuda a confirmar a autenticidade dos eventos e identificar a intenção.  
6. **Determinar a linha do tempo**: ao combinar timestamps de pacotes com outras evidências (logs, timestamps de arquivos), é possível reconstruir a cronologia do ataque.  Lembre‑se de ajustar fusos horários e sincronizar relógios.

## 6.4 Ferramentas e práticas recomendadas

- **Wireshark**: interface gráfica para captura e análise de pacotes; permite decodificar centenas de protocolos e reconstruir sessões.  
- **TCPDump/TShark**: ferramentas de linha de comando para captura e filtragem de pacotes; adequadas para ambientes de produção.  
- **Bro/Zeek**: framework de monitoramento que gera logs ricos a partir do tráfego (HTTP, DNS, SSL, etc.), facilitando a pesquisa de eventos.  
- **Logstash/Elasticsearch/Kibana (ELK)**: armazena e analisa grandes volumes de logs e fluxos, permitindo visualização de padrões e detecção de anomalias.  
- **SIEM (Security Information and Event Management)**: plataformas como Splunk, QRadar ou SecurityOnion coletam, correlacionam e alertam eventos de múltiplas fontes.  
- **Captura forense de malware**: sandboxes de rede (Cuckoo, CAPEv2) permitem executar arquivos suspeitos e observar o tráfego gerado, identificando C2 (Command and Control) e exfiltração.

Práticas recomendadas incluem:

1. **Sincronizar relógios** (NTP) em todos os dispositivos para que os timestamps sejam comparáveis.  
2. **Configurar níveis de log apropriados**, evitando tanto o excesso (que dificulta análise) quanto a falta de detalhes.  
3. **Segmentar redes** para limitar o impacto de incidentes e facilitar o monitoramento.  
4. **Automatizar alertas** e correlação de eventos para detecção precoce.  
5. **Manter políticas de retenção** de logs e capturas que atendam requisitos legais e possibilitem investigações retroativas.

A forense de redes complementa a análise de evidências locais e fornece uma perspectiva externa sobre o ataque.  Ela ajuda a responder perguntas como “como o invasor entrou?” e “o que foi exfiltrado?”, enriquecendo o relatório final.