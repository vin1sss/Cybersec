# 5 Perícia forense em navegadores web e e‑mails

Navegadores e sistemas de e‑mail são fontes ricas de evidência, pois registram históricos de navegação, downloads, caches, cookies, credenciais e comunicações.  A análise desses artefatos fornece insights sobre atividades on‑line de usuários, acesso a contas e caminhos de envio de mensagens.

## 5.1 Perícia forense no navegador web

Cada navegador armazena dados em estruturas específicas, mas os tipos de artefatos são similares:

1. **Histórico de navegação**: mostra URLs visitadas, títulos de páginas e carimbos de tempo.  O Chrome e navegadores baseados em Chromium mantêm este histórico em um banco de dados SQLite (`History`) localizado em `C:\Users\<usuário>\AppData\Local\Google\Chrome\User Data\Default`.  Firefox utiliza o arquivo `places.sqlite` e a pasta `sessionstore-backups` para sessões.  A análise do histórico revela padrões de acesso, pesquisas e sites visitados.  
2. **Downloads**: registros de arquivos baixados, incluindo nome, caminho local, URL de origem, tamanho e data.  O banco de dados de downloads complementa o histórico para demonstrar transferência de arquivos suspeitos.  
3. **Cookies e sessões**: cookies contêm tokens de sessão e preferências; podem ser usados para reconstruir sessões autenticadas.  Os arquivos `Cookies` do Chrome (SQLite) e `cookies.sqlite` do Firefox armazenam data de criação e expiração, domínios e flags de segurança.  
4. **Cache e arquivos temporários**: páginas e recursos armazenados localmente.  A análise de cache pode recuperar imagens, scripts ou páginas visitadas mesmo que o histórico tenha sido apagado.  
5. **Credenciais salvas**: navegadores podem armazenar senhas em bancos de dados encriptados (por exemplo, `Login Data` no Chrome).  Para acessar as credenciais, é necessário decifrar usando APIs do sistema operacional e credenciais do usuário.  
6. **Extensões e plugins**: arquivos de configuração e armazenamento de extensões (como carteiras de criptomoedas) podem revelar atividades adicionais ou malware.  
7. **Modo privado/incógnito**: limita o armazenamento de histórico, mas artefatos podem persistir em outras partes do sistema, como no arquivo de sessão ou no cache.  Um blog sobre forense de Chrome mostrou que mesmo após limpar o histórico, o Chrome mantém arquivos `Session_<data>` e `Tabs_<data>` na pasta `Sessions` (`AppData\Local\Google\Chrome\User Data\Default\Sessions`).  Esses arquivos armazenam o estado das abas e podem conter informações sobre páginas visitadas antes da limpeza【692290592680618†L29-L39】.  A persistência desses dados destaca a importância de examinar todos os artefatos relacionados a sessões.

### Procedimento de análise

1. **Preservação**: faça uma cópia da pasta de perfis do navegador ou da imagem do disco em modo somente leitura.  
2. **Ferramentas de extração**: use ferramentas como *Browser History Examiner*, *Hindsight* (para Chrome/Chromium), *NetAnalysis* ou *Belkasoft X* para extrair e exportar o histórico, cookies, downloads e outros dados.  Elas convertem timestamps no formato WebKit/Chrome (microsegundos desde 1601) para formatos legíveis【838057224104116†L66-L99】.  
3. **Interpretação**: correlacione as entradas com o contexto da investigação.  Verifique a primeira e a última visita a um domínio, a frequência de acesso e a sequência de cliques.  Analise o cache para recuperar imagens ou páginas que podem não estar mais acessíveis on‑line.  
4. **Corroborar com artefatos de sistema**: use prefetches, logs de eventos e arquivos LNK para confirmar a execução do navegador no horário em questão【915223127759981†L117-L163】.  
5. **Analisar privacidade e antiforense**: identifique se o usuário utilizou extensões ou modos privados para ocultar rastros.  Pesquisar em arquivos de sessão e no registro do Windows pode revelar vestígios deixados por essas ações.

## 5.2 Perícia forense em e‑mails

Os e‑mails são uma forma comum de comunicação e, consequentemente, de prova.  A análise de e‑mails foca tanto no conteúdo quanto nos **headers**, que contêm metadados sobre o percurso da mensagem.

### 5.2.1 Estrutura de um e‑mail

Um e‑mail consiste em duas partes principais:

1. **Header**: conjunto de campos que descrevem remetente, destinatário, servidores envolvidos e políticas de autenticação.  Um artigo de 2025 da Stellar Info ressalta que cabeçalhos incluem o nome do destinatário e do remetente, horários de envio e recebimento, cliente de e‑mail, provedor de internet e o endereço IP do remetente【767280015996818†L246-L256】.  Esses dados ajudam a verificar a legitimidade da mensagem e rastrear a origem.  
2. **Body**: corpo da mensagem, podendo ser texto simples ou HTML, e incluir anexos.  Os anexos podem conter malware ou informações de interesse.

### 5.2.2 Análise de headers

Os headers contêm diversos campos, e cada um revela aspectos distintos:

- **Delivered‑To**: endereço do destinatário final.  Divergências entre este campo e o endereço real podem indicar redirecionamento ou spoofing【767280015996818†L279-L287】.  
- **Received**: lista de servidores SMTP pelos quais a mensagem passou.  Inclui o IP e o identificador do servidor, além de data e hora em que o e‑mail foi recebido【767280015996818†L294-L304】.  A ordem inversa das entradas permite rastrear o caminho da mensagem.  
- **X‑Received**: cabeçalho não padronizado criado por alguns servidores (como o Gmail) para adicionar detalhes adicionais (endereço IP do servidor receptor, ID SMTP, horário)【767280015996818†L308-L321】.  
- **Return‑Path**: endereço para onde a mensagem deve ser enviada caso não possa ser entregue; pode diferir do remetente mostrado e revelar serviços de envio terceiros【767280015996818†L323-L343】.  
- **Received‑SPF** e **Authentication‑Results**: resultados das verificações de autenticação (SPF, DKIM, DMARC).  O campo Received‑SPF indica se o endereço IP do remetente é autorizado pelo domínio; os códigos “pass”, “fail” e “softfail” mostram o resultado【767280015996818†L352-L377】.  O campo Authentication‑Results contém os resultados combinados das verificações de SPF, DKIM e DMARC e o identificador do servidor que realizou a verificação【767280015996818†L379-L394】.  Falhas nesses campos podem indicar falsificação ou spoofing.

Ao analisar um e‑mail suspeito:

1. **Extraia o cabeçalho completo** no cliente de e‑mail (por exemplo, “Mostrar original” no Gmail【767280015996818†L260-L270】).  
2. **Leia os campos na ordem inversa de recebimento**; o último “Received” é o servidor que enviou a mensagem originalmente.  
3. **Verifique divergências** entre os campos “From”, “Return‑Path” e “Received‑SPF”.  
4. **Geolocalize** os endereços IP usando serviços de whois ou bases de dados de geolocalização para identificar região e provedor.  
5. **Avalie a assinatura digital (DKIM)** e o status DMARC nos campos de autenticação para verificar se a mensagem passou pelas políticas do domínio remetente.  
6. **Examine anexos e links** com ferramentas de sandbox ou antivírus para detectar malware.

### 5.2.3 Aquisição e análise de caixas de correio

Além de cabeçalhos individuais, é comum coletar **arquivos de caixa de correio** (por exemplo, `.pst` ou `.ost` do Outlook, `.mbox` do Thunderbird).  Esses arquivos contêm todas as mensagens, pastas, anexos e metadados.  A aquisição deve ser feita em modo somente leitura, calculando hash e registrando a cadeia de custódia.  Ferramentas como *X1 Social Discovery*, *Belkasoft X* ou *MailXaminer* permitem examinar múltiplas contas, filtrar mensagens por data, remetente ou palavras‑chave e exportar os resultados para relatório.  O conteúdo pode ser correlacionado com logs do servidor de e‑mail e registros do sistema para confirmar horários e origens.

A análise forense de e‑mails não se limita ao cabeçalho.  O corpo e os anexos devem ser interpretados no contexto da investigação, e a integridade das mensagens precisa ser mantida.  Em ambientes corporativos, políticas de retenção e logs de servidores (como Exchange ou Office 365) oferecem informações adicionais sobre encaminhamentos, exclusões e tentativas de acesso.