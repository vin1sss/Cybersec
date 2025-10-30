# 11 Fundamentos de logs e análise

Logs são registros sistemáticos de eventos gerados por sistemas operacionais, aplicações e dispositivos de rede.  Constituem as “pegadas digitais” das atividades – legítimas ou maliciosas – e são essenciais para detectar incidentes e reconstruir ações de um atacante.  Um artigo introdutório destaca que logs contêm rastros de quase todas as atividades em um sistema digital, facilitando a identificação do responsável e do modo de execução【269537297753396†L70-L77】.

## 11.1 Importância e casos de uso

Os logs são utilizados em várias áreas【269537297753396†L83-L121】:

- **Monitoramento de eventos de segurança:** permitem identificar comportamentos anômalos quando monitorados em tempo real;
- **Investigação de incidentes:** fornecem detalhes sobre o que ocorreu durante um ataque e ajudam a realizar análise de causa raiz【269537297753396†L96-L104】;
- **Correção de problemas (troubleshooting):** erros e avisos gravados nos logs auxiliam na resolução de falhas em sistemas e aplicações【269537297753396†L106-L110】;
- **Monitoramento de desempenho:** métricas de utilização registradas em logs permitem otimizar o desempenho de aplicativos【269537297753396†L112-L114】;
- **Auditoria e conformidade:** logs criam trilhas de auditoria e ajudam a provar conformidade com regulamentos e políticas【269537297753396†L116-L121】.

## 11.2 Tipos de logs

Os logs são classificados conforme o tipo de informação que registram【269537297753396†L145-L170】:

- **Logs de rede:** registram tráfego de entrada e saída na rede.  São comparáveis a livros de visitas, permitindo verificar conexões estabelecidas e detectar atividades suspeitas【269537297753396†L190-L198】;
- **Logs de segurança:** capturam eventos de autenticação e autorização.  Funcionam como câmeras de vigilância digitais, anotando quem acessou o quê e quando【269537297753396†L200-L212】;
- **Logs de aplicação:** armazenam erros, alertas e informações específicas de software;
- **Logs de sistema:** registram eventos do próprio sistema operacional, como inicialização, desligamento, erros de drivers e serviços【269537297753396†L216-L233】.

No Windows, as três categorias principais de logs de eventos são Application, System e Security.  A ferramenta **Event Viewer** fornece uma interface gráfica para pesquisar e filtrar esses logs.  Para acessá‑la, basta digitar *Event Viewer* no menu Iniciar; à esquerda, os logs são listados e podem ser expandidos【269537297753396†L214-L249】.  O log **Application** registra erros e alertas de softwares, o log **System** documenta problemas de hardware e serviços e o log **Security** grava autenticação de usuários, mudanças de permissões e outras ações sensíveis【269537297753396†L221-L231】.

## 11.3 Análise de logs

Analisar logs consiste em extrair dados valiosos e identificar anomalias.  O volume de entradas pode ser grande, portanto filtrar por tipos de eventos e intervalos de tempo acelera a investigação.  Técnicas de análise incluem:

1. **Busca por padrões:** utilização de expressões regulares ou ferramentas de correlação para localizar eventos específicos;
2. **Detecção de anomalias:** comparação do comportamento atual com padrões históricos para apontar desvios;
3. **Correlacionamento multi‑fonte:** combinar logs de rede, sistema e aplicação para construir uma linha do tempo completa;
4. **Automação:** uso de sistemas SIEM (Security Information and Event Management) para coletar e correlacionar logs em tempo real, gerando alertas automáticos.

Nos capítulos sobre Windows, Linux e redes deste codex são apresentadas técnicas específicas de análise de logs para cada plataforma.  A coleta e preservação de logs deve respeitar boas práticas de cadeia de custódia e integridade (ver Capítulo 3), garantindo que as evidências possam ser apresentadas em procedimentos legais.

## 11.4 Logs de eventos do Windows

O Windows grava eventos em arquivos `.evtx` acessíveis pelo **Event Viewer**.  Conforme descrito por especialistas, os logs são classificados em cinco níveis de severidade – **informação**, **aviso**, **erro**, **crítico** e **auditoria de sucesso/falha**【536392158545277†L8-L10】.  Esses níveis ajudam a priorizar quais eventos merecem atenção durante uma investigação.

Além disso, os eventos são organizados em cinco **canais** principais【536392158545277†L41-L56】:

1. **Application** – registros de aplicações instaladas (erros, avisos e informações).
2. **Security** – eventos auditados pelo sistema (logon/logoff, acesso indevido).  IDs como **4624** (logon bem‑sucedido) e **4625** (falha de logon) são cruciais para reconstruir acessos【536392158545277†L24-L31】.
3. **Setup** – incidentes de instalação ou atualização do sistema operativo【536392158545277†L51-L53】.
4. **System** – mensagens do sistema, incluindo drivers, serviços e hardware.
5. **Forwarded Events** – logs recebidos de outras máquinas quando o encaminhamento de eventos está ativado【536392158545277†L54-L56】.

Os arquivos de log ficam em `C:\Windows\System32\winevt\Logs`【536392158545277†L32-L39】 e também podem ser configurados via registro em `HKLM\SYSTEM\CurrentControlSet\Services\EventLog`【536392158545277†L32-L40】.  Cada evento contém metadados como nível, canal, ID, provedor, timestamp e outros campos que facilitam o filtro e a correlação【536392158545277†L95-L118】.

Para examinar os logs manualmente, o Event Viewer permite filtrar por período, nível e ID, mas a análise manual pode ser demorada.  Scripts em PowerShell ou ferramentas como **Chainsaw** e **Hayabusa** podem automatizar a extração e alerta de eventos relevantes.  A correlação de logs de eventos com artefatos como Prefetch, Amcache, Shellbags e MFT possibilita reconstruir com precisão a linha do tempo de atividades de um usuário ou malware.
