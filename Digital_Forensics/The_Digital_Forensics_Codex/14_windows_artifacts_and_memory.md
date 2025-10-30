# 14 Artefatos e Análise de Memória no Windows

Este capítulo aprofunda‑se em artefatos específicos do Windows utilizados para reconstruir a execução de programas, monitorar a atividade de usuários e analisar a memória de sistemas Windows.  Esses artefatos complementam os temas abordados no capítulo 4 e são essenciais para investigações como as salas **Windows Forensics 1**, **Windows Forensics 2**, **Windows Applications**, **Windows Memory and Processes**, **Windows Memory and User Activity** e **Blue Team Windows Internals**.

## 14.1 Prefetch

O sistema Windows mantém **arquivos Prefetch** (`.pf`) no diretório `C:\Windows\Prefetch`.  Esses arquivos são criados automaticamente quando um programa é executado e armazenam informações para acelerar futuros carregamentos.  Cada arquivo contém:

* **Nome do executável** e caminho de execução.
* **Timestamps de execução** (última execução e execuções anteriores), fornecendo um histórico de quando a aplicação foi iniciada.
* **Contador de execuções** indicando quantas vezes o programa foi executado.
* **Referências a arquivos e bibliotecas DLL** que foram carregados durante a execução【417353193534993†L115-L169】.

Para os investigadores, esses metadados revelam se um aplicativo foi executado, quando e com que frequência, mesmo que o arquivo original tenha sido excluído【417353193534993†L117-L174】.  A análise de Prefetch é valiosa para reconstruir linhas do tempo, detectar execução de malware e corroborar outras evidências.  Devem‑se considerar desafios como compressão MAM em versões recentes do Windows, limite de arquivos armazenados (128 no Windows 7, 1024 no Windows 8+) e possíveis tentativas de exclusão ou modificação【417353193534993†L189-L260】.

Ferramentas úteis incluem **PECmd** e **windowsPrefetch** (Python), que decodificam entradas de versões recentes e extraem hashes e timestamps.

## 14.2 Amcache e Shimcache

Dois artefatos do registro monitoram a execução de programas no Windows:

### Amcache (Application Activity Cache)

* Localizado em `C:\Windows\AppCompat\Programs\Amcache.hve`.
* Armazena metadados de programas executados ou instalados: caminhos completos, hashes SHA‑1, datas de instalação e última modificação【885404554722988†L120-L150】.
* É confiável para confirmar se um aplicativo foi realmente instalado ou executado, mesmo após desinstalação【885404554722988†L142-L150】.

### Shimcache (Application Compatibility Cache)

* Localizado em `HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\AppCompatCache`.
* Armazena informações de compatibilidade para permitir que softwares antigos sejam executados.  Registra caminhos e timestamps de arquivos executados, mesmo que a execução não tenha sido bem‑sucedida【885404554722988†L154-L160】.

**Diferenças:**  Amcache armazena hashes e timestamps de instalação/execução, sendo mais confiável para provar execução; Shimcache registra tentativas de execução, inclusive falhas, o que ajuda a identificar atividades suspeitas【885404554722988†L166-L184】.  Usados em conjunto, fornecem uma visão mais completa da atividade do sistema【885404554722988†L186-L192】.  Ferramentas como **AmcacheParser** e **AppCompatCacheParser** automatizam a extração desses dados【885404554722988†L211-L219】.

## 14.3 Shellbags

**Shellbags** são artefatos do Windows que registram o histórico de interações do usuário com pastas.  Armazenam o caminho da pasta, as preferências de visualização (ícones, ordem de ordenação) e timestamps de último acesso【142134848569929†L120-L148】.  Esses dados são gravados em chaves do registro (`HKCU\Software\Microsoft\Windows\Shell\BagMRU` e `Shell\Bags`), permitindo que o Windows restaure a visualização das pastas.  Para a perícia:

* **Rastreamento de atividade** – Shellbags mostram quais diretórios foram abertos, mesmo após exclusão ou alteração do caminho original【142134848569929†L150-L160】.
* **Identificação de arquivos ocultos ou apagados** – caminhos registrados podem revelar diretórios que não existem mais【142134848569929†L176-L183】.
* **Construção de linhas do tempo** – timestamps permitem correlacionar acessos a pastas com outras evidências【142134848569929†L184-L186】.

Limitações incluem dados fragmentados distribuídos em várias chaves e possibilidade de entradas serem sobrescritas ou modificadas【142134848569929†L229-L239】.  Ferramentas como **ShellBags Explorer** ou **RBCmd** facilitam a análise.

## 14.4 Atalhos (LNK) e Jump Lists

### Atalhos (.lnk)

Arquivos `.lnk` são criados automaticamente quando o usuário abre documentos ou aplicações via Windows Explorer.  Eles contêm:

* Caminho original do arquivo ou pasta.
* Timestamps de criação, modificação e acesso【633016924704301†L56-L83】.
* Tamanho do arquivo, número de série do volume e nome de compartilhamento de rede, quando aplicável【633016924704301†L58-L71】.
* Podem ser abusados por atacantes: o campo **Target** pode ser alterado para executar código malicioso (por exemplo, `cmd.exe /c powershell -ExecutionPolicy Bypass -NoProfile -Command "IEX ..."`), permitindo inicialização de malware por meio de um atalho【633016924704301†L96-L149】.  Investigadores devem examinar atalhos em `C:\Users\<usuario>\AppData\Roaming\Microsoft\Windows\Recent` e na pasta `Startup` para detectar persistência.

### Jump Lists

Introduzidas no Windows 7, **Jump Lists** registram arquivos e tarefas recentemente acessados por aplicativos.  Existem dois tipos:

1. **Automáticas** – geradas pelo sistema para aplicativos compatíveis (Notepad, MS Word).  Localizadas em `%APPDATA%\Microsoft\Windows\Recent\AutomaticDestinations`.
2. **Personalizadas** – criadas por programas específicos para armazenar itens personalizados.  Localizadas em `%APPDATA%\Microsoft\Windows\Recent\CustomDestinations`【261020002357916†L58-L64】.

Jump Lists fornecem informações valiosas para perícia, incluindo nome do aplicativo, caminhos dos arquivos, timestamps de acesso e tarefas executadas【261020002357916†L66-L91】.  Ferramentas como **JLECmd** e **JumpList Explorer** decodificam os arquivos `.automaticDestinations-ms` e `.customDestinations-ms`.

## 14.5 UserAssist e outros artefatos de atividade do usuário

O **UserAssist** é uma chave do Registro (`HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist`) que rastreia programas e arquivos abertos via Windows Explorer.  As entradas são codificadas em ROT13 e incluem o nome do arquivo, contagem de execuções e timestamp da última execução【548716244196739†L55-L90】.  Analisar o UserAssist revela quais programas foram executados por determinado usuário e com que frequência【548716244196739†L71-L78】.

Outros artefatos relevantes incluem:

* **Run/RunOnce** – chaves que definem programas a serem executados na inicialização; frequentemente exploradas por malware.
* **Start Menu RunMRU** – histórico de comandos digitados na caixa *Run*.
* **RecentDocs/LastVisitedMRU** – listas de documentos e pastas recentemente acessados.
* **BAM/DAM (Background Activity Moderator/Device Activity Moderator)** – rastreiam processos em segundo plano e dispositivos utilizados; úteis para detectar execução de malware.
* **SRUM (System Resource Usage Monitor)** – banco de dados SQLite (`SRUDB.dat`) em `C:\Windows\System32\SRU`; registra uso de CPU, rede e energia por processos ao longo do tempo, permitindo identificar programas que consumiram recursos ou acessaram a rede.

## 14.6 Análise de Memória e Processos com Volatility

Os dumps de memória capturam dados voláteis essenciais, como processos em execução, conexões de rede, chaves criptográficas e malware residente.  Ferramentas como **Volatility 3** e **MemProcFS** permitem analisar memórias de Windows sem depender de perfis estáticos.  A análise segue etapas:

1. **Identificação da imagem:** o plugin `windows.info` exibe a versão do Windows e o horário da captura【702251023194189†L73-L88】.
2. **Listagem de processos:** `windows.pslist` mostra processos ativos; `windows.psscan` pode recuperar processos terminados; `windows.pstree` apresenta relações pai‑filho, ajudando a detectar anomalias【702251023194189†L90-L107】.
3. **Investigação de processos suspeitos:** plugins `windows.cmdline` mostram a linha de comando que iniciou o processo, `windows.handles` lista objetos (arquivos, chaves, mutexes) abertos, `windows.dlllist` lista DLLs carregadas【702251023194189†L123-L150】.  O plugin `windows.dumpfiles.DumpFiles` extrai executáveis e DLLs para análise offline【702251023194189†L152-L154】.
4. **Conexões de rede:** `windows.netscan` mostra conexões TCP/UDP ativas associadas a cada processo, permitindo identificar comunicações maliciosas【702251023194189†L156-L162】.
5. **Detecção de anomalias:** `windows.malfind` procura regiões de memória com permissões de leitura/escrita/execução e cabeçalho `MZ`, típicas de código injetado ou malware em memória【702251023194189†L169-L180】.  Outros plugins (por exemplo, `windows.psxview`, `atomscan`) ajudam a detectar rootkits e ocultação de processos.

**MemProcFS**, uma camada sobre o Volatility, monta a imagem de memória como um sistema de arquivos virtual.  Ele agrupa a saída de vários plugins em diretórios organizados por PID e categoria (processos, sistema, etc.), facilitando a navegação e exportação de artefatos【702251023194189†L187-L211】.

Ao analisar dumps de memória, é essencial capturá‑los o mais rápido possível após o incidente, pois dados voláteis desaparecem após desligamento.  Ferramentas como **Magnet RAM Capturer**, **FTK Imager** e **WinPmem** permitem a coleta segura de memória.

## 14.7 Windows Internals e Estruturas de Arquivos

O Windows utiliza o sistema de arquivos **NTFS**, composto por estruturas que preservam metadados e logs de alterações.  Três componentes merecem destaque:

1. **Master File Table (MFT)** – é o banco de dados central que contém registros de cada arquivo/diretório.  Cada entrada inclui um identificador único, atributos de informação padrão (timestamps de criação, modificação, acesso), nome do arquivo e atributos de dados.  O MFT mantém as entradas mesmo após exclusão, permitindo recuperar arquivos e reconstruir linhas do tempo【400244050850565†L70-L107】.  Ferramentas como **MFTECmd** e *Sleuth Kit* analisam o MFT【400244050850565†L111-L118】.
2. **$LogFile** – arquivo de log transacional que registra operações no volume.  Contém registros de redo/undo de alterações em entradas da MFT e diretórios, possibilitando rastrear modificações granulares e identificar operações que falharam【400244050850565†L169-L196】.
3. **USN Journal ($UsnJrnl)** – mantido em `$Extend\$UsnJrnl`, registra cada criação, exclusão, renomeação ou modificação de arquivo/diretório.  Cada registro inclui o identificador do arquivo, tipo de mudança e timestamps【400244050850565†L210-L234】.  Ferramentas como **UsnJrnl2Csv** e **RECmd** convertem os registros em formatos legíveis【400244050850565†L248-L254】.

Compreender essas estruturas e correlacionar seus registros com Prefetch, Amcache/Shimcache, Shellbags e logs de eventos permite reconstruir a atividade do sistema com alto grau de precisão.  Esse conhecimento é essencial em investigações avançadas e para as salas de práticas que simulam casos reais de exfiltração e análise de artefatos.
