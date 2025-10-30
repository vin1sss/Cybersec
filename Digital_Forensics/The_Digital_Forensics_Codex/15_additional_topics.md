# Tópicos Avançados de Forense Digital

Este capítulo agrega temas que complementam os conteúdos já abordados no **Digital Forensics Codex**.  Os tópicos foram inspirados por salas de estudo orientado da plataforma TryHackMe e incluem análise de partições MBR/GPT, investigação de aplicações no Windows, análise de sistemas de arquivos **EXT**, procedimentos de análise ao vivo em Linux, e orientações gerais para estudos de caso e investigações em ambientes sem supervisão.  Todo o conteúdo foi pesquisado de forma independente em fontes públicas, sem utilizar materiais proprietários.

## 1 Análise de Partições MBR e GPT

Antes de examinar discos e seus sistemas de arquivos, é necessário compreender os esquemas de particionamento que definem a estrutura lógica da unidade.  Historicamente, os computadores IBM PC utilizavam a combinação do **BIOS** e da **Master Boot Record (MBR)**.  A MBR fica no setor 0 do disco e contém um pequeno código de bootstrap e uma tabela com **quatro entradas de partição**【299614274795463†L150-L156】.  Como o campo de tamanho de partição na MBR tem 32 bits, discos particionados nesse esquema suportam partições de no máximo **2 terabytes**【299614274795463†L165-L170】.  Para permitir mais de quatro partições, sistemas baseados em MBR usam partições estendidas que armazenam partições lógicas em cadeia【299614274795463†L172-L177】【299614274795463†L255-L279】.

Com o aumento no tamanho dos discos e a adoção da interface de firmware **EFI/UEFI**, surgiu o esquema **GUID Partition Table (GPT)**.  O GPT possui as seguintes características:

* **Suporte a discos maiores** – o campo de tamanho de partição é de 64 bits, permitindo volumes de até 8 zettabytes (teoricamente)【299614274795463†L165-L170】.
* **Número de partições** – enquanto a MBR permite apenas quatro partições primárias, o GPT suporta até 128 partições em implementações comuns【299614274795463†L171-L175】.
* **Identificadores únicos** – cada disco e partição recebe um **GUID** (identificador global único), facilitando a identificação e reduzindo colisões【299614274795463†L182-L185】.
* **Verificação de integridade** – o GPT mantém **checksums CRC32** do cabeçalho e da tabela de partições.  Ele também armazena **cópias de backup** desses dados nos últimos setores do disco, permitindo recuperar a estrutura caso a tabela primária seja danificada【299614274795463†L186-L190】.
* **Protective MBR** – para que ferramentas antigas não sobrescrevam um disco GPT, o setor 0 contém uma entrada de partição do tipo `0xEE` que representa todo o disco.  Esse *protective MBR* faz com que softwares legados acreditem que o disco já está totalmente ocupado【299614274795463†L313-L324】.  Não existe cópia de segurança desse setor【299614274795463†L343-L381】.

### 1.1 Implicações Forenses

Durante a aquisição de discos GPT, o examinador deve capturar também o cabeçalho de backup localizado nos últimos setores do disco.  Ferramentas como **mmls** (Sleuth Kit) permitem visualizar partições GPT e calcular a localização do cabeçalho de backup.  Ao analisar discos MBR, é importante preservar o setor 0 (MBR) e examinar as entradas de partições em busca de setores ocultos ou anomalias, verificando se há partições estendidas que possam esconder dados.  Em discos híbridos usados por sistemas *Boot Camp*, pode existir simultaneamente uma MBR e uma tabela GPT (chamada de “hybrid MBR”), o que requer atenção ao duplicar o disco【299614274795463†L423-L428】.

## 2 Sistemas de Arquivos EXT (Ext3/Ext4)

Os sistemas **Ext2/3/4** são amplamente utilizados em distribuições Linux e dispositivos Android.  O **Ext4** introduziu melhorias significativas sobre o Ext3, que impactam diretamente a análise forense:

* **Suporte a grandes volumes** – o Ext4 suporta volumes de até 64 zebibytes (ZiB) e arquivos individuais de até 16 terabytes com blocos de 4 KiB【398674476518208†L253-L266】.  Para blocos de 64 KiB, teoricamente é possível até 1 YiB【398674476518208†L253-L259】.
* **Extents** – em vez de mapear cada bloco em um vetor, o Ext4 usa **extents**, que definem intervalos contíguos de blocos.  Um extent pode mapear até 128 MiB de espaço contíguo; se um arquivo possuir mais de quatro extents, os adicionais são organizados em uma árvore B (*extent tree*)【398674476518208†L262-L270】.  As extents reduzem a fragmentação e melhoram a performance, mas complexificam a recuperação de arquivos apagados, pois um único campo de mapeamento cobre várias centenas de blocos.
* **Compatibilidade e modos de alocação** – o Ext4 é compatível com Ext2/Ext3; é possível montar partições ext2/3 como ext4 e usar os novos algoritmos de alocação de blocos para melhorar a performance【398674476518208†L271-L276】.  O sistema implementa **alocação diferida (allocate‑on‑flush)**, que retarda a alocação de blocos até que os dados sejam gravados em disco, reduzindo fragmentação【398674476518208†L287-L293】.
* **Número de subdiretórios** – diferentemente do Ext3, que limita diretórios a 32 000 subdiretórios, o Ext4 permite praticamente número ilimitado de subdiretórios.  Para manter a performance, utiliza um **índice HTree** (variação de B‑tree) para organizar entradas e permite diretórios com milhões de arquivos【398674476518208†L295-L304】.
* **Checksums e confiabilidade** – o Ext4 implementa **checksums no diário** e **checksums de metadados**, aumentando a confiabilidade e permitindo detectar corrupção【398674476518208†L305-L319】.  O sistema também sinaliza blocos e inodes não inicializados, acelerando o `fsck`【398674476518208†L320-L324】.
* **Carimbo de data em nanosegundos** – o Ext4 registra tempos de acesso (atime), modificação (mtime), alteração de metadados (ctime) e criação (crtime) com resolução de nanosegundos e adiciona bits extras para mitigar o *Year 2038 problem*【398674476518208†L333-L339】.  Isso oferece maior precisão para construir linhas do tempo forenses.

### 2.1 Considerações Forenses

Ao analisar imagens Ext3/Ext4 com ferramentas como **Sleuth Kit** (`fsstat`, `icat`, `blkls`), verifique o superbloco e a tabela de descritores de grupo para confirmar parâmetros (tamanho de bloco, quantidade de inodes) e localizar as tabelas de inodes e bitmaps.  O diário do Ext4 (arquivo `.journal` ou bloco reservado) contém transações e pode revelar operações de criação, alteração e deleção.  O campo de extents indica se um arquivo usa mapeamento por extents; para recuperar arquivos apagados é necessário percorrer a *extent tree* e examinar entradas de diretório.  A precisão de nanosegundos nos timestamps permite diferenciar acessos próximos no tempo, mas muitos utilitários só exibem segundos; utilize ferramentas que exibem as frações de segundo.

## 3 Análise de Aplicações no Windows: Caso Microsoft Teams

Além de examinar o sistema operacional, o analista precisa entender onde aplicações específicas armazenam dados.  A investigação do cliente de **Microsoft Teams** demonstra como aplicações Electron‑based guardam uma variedade de artefatos úteis:

### 3.1 Arquivos de instalação

A instalação padrão via **Squirrel** copia os componentes para `C:\Users\%USERNAME%\AppData\Local\Microsoft\Teams`.  Nesse diretório encontram-se arquivos como `app.ico`, `Resources.pri`, `setup.json`, `Update.exe` e `Update.VisualElementsManifest.xml`, além de subdiretórios `current`, `packages` e `previous`【631526010372088†L45-L90】.  O arquivo `SquirrelSetup.log` registra verificações de atualização e instalações, fornecendo uma linha do tempo das versões instaladas【631526010372088†L98-L101】.

### 3.2 Artefatos de uso (AppData \Roaming)

Os artefatos gerados pelo usuário ficam em `C:\Users\%USERNAME%\AppData\Roaming\Microsoft\Teams`【631526010372088†L111-L117】.  Dentre os itens de maior interesse estão【631526010372088†L188-L216】:

* **Cookies** – um banco de dados SQLite que contém cookies de sessão.  Na versão analisada, esses cookies não estavam criptografados.
* **desktop-config.json** – arquivo JSON que armazena configurações, incluindo nome do usuário, endereço IP público e endereços de e‑mail【631526010372088†L188-L204】.
* **installTime.txt** – data/hora da primeira instalação do Teams【631526010372088†L195-L196】.
* **logs.txt** – log contínuo que registra comunicações com o middleware, armazenando muitos timestamps com fuso horário.  Pode-se reconstruir aberturas, fechamentos e até redimensionamento da janela【631526010372088†L197-L201】.
* **storage.json** – arquivo JSON com `auth_tenant_users_map`, onde consta o nome completo, e‑mail e endereço IP público do último login de cada usuário e o timestamp de autenticação【631526010372088†L202-L206】.
* **IndexedDB** – diretório contendo bancos LevelDB com mensagens, posts, comentários, contatos, compromissos e registros de chamadas【631526010372088†L208-L210】.
* **Session Storage** – LevelDB com tokens JWT usados para autenticar a sessão【631526010372088†L211-L213】.
* **Local Storage** – LevelDB que registra transferências de arquivos e rascunhos de mensagens【631526010372088†L214-L216】.
* **Cache** – cache de disco que armazena cópias de arquivos e imagens【631526010372088†L217-L218】.

### 3.3 Procedimentos de Análise

1. **Isolar o perfil**:  crie uma cópia forense do diretório `AppData\Local\Microsoft\Teams` e `AppData\Roaming\Microsoft\Teams` usando uma ferramenta que mantenha os metadados intactos (ex.: `robocopy /DCOPY:DAT` ou `FTK Imager`).
2. **Examinar logs e configurações**:  abra `SquirrelSetup.log` e `logs.txt` para construir uma linha do tempo de instalações, atualizações e eventos de execução.  Extraia dados de `desktop-config.json`, `installTime.txt` e `storage.json` para identificar usuários, IPs e horários de login.
3. **Extrair bancos de dados**:  use ferramentas como `DB Browser for SQLite` para examinar `Cookies` e exportar cookies de sessão; use `LevelDB-Dump` ou scripts Python para processar os bancos de dados da pasta `IndexedDB` e `Local Storage`, identificando mensagens, chamadas, transferências de arquivos e tokens.
4. **Correlacionar com o sistema**:  combine os artefatos da aplicação com **prefetch**, **Jump Lists**, **Shellbags** e **event logs** do Windows para confirmar horários e identificar o usuário ativo.  Isso ajuda a reconstruir ações do suspeito e a demonstrar conhecimento ou intenção.

## 4 Análise Ao Vivo em Linux

A análise ao vivo (ou *live response*) em sistemas Linux é essencial quando o investigado está em produção e não pode ser desligado.  O objetivo é capturar informações voláteis sem alterar significativamente o sistema.  Algumas práticas recomendadas incluem:

1. **Identificar diretórios e arquivos suspeitos** – use `ls -lah /tmp /var/tmp /dev/shm` para listar conteúdos de diretórios temporários; scripts maliciosos e backdoors costumam ser armazenados nesses locais【68018425506088†L47-L79】.  Verifique também arquivos de inicialização em `/etc/init.d`, `/etc/rc*.d` e `/etc/systemd/system` para identificar programas persistentes【68018425506088†L80-L97】.
2. **Listar processos em execução** – o comando `ps auxf` mostra hierarquia de processos e argumentos, facilitando a detecção de serviços clandestinos.  Ferramentas como `lsof -p <pid>` permitem descobrir conexões de rede e arquivos abertos por um processo【68018425506088†L80-L97】.
3. **Analisar conexões de rede** – use `ss -tulpn` ou `netstat -antup` para identificar portas abertas e processos associados; conexões incomuns ou para hosts externos podem indicar exfiltração de dados【68018425506088†L80-L97】.
4. **Examinar usuários e logins** – consulte `/etc/passwd` e `/etc/shadow` para verificar contas legítimas, e analise logs de autenticação (`/var/log/auth.log`, `/var/run/utmp`, `/var/log/wtmp`) com ferramentas como `last` e `lastlog` para detectar logins suspeitos【400025389998378†L100-L124】.
5. **Capturar memória** – para análise de malware ou descriptografia, gere um *dump* de memória com ferramentas como **LiME** (Linux Memory Extractor).  A captura deve ser feita com o menor impacto possível e salva em um disco externo.  Posteriormente, use **Volatility** para analisar o dump, identificando processos ocultos, módulos carregados e chaves de criptografia【454964452511384†L115-L183】.

Ao finalizar a coleta, desligue a máquina (se permitido) e prossiga com a aquisição de discos usando métodos tradicionais.  Lembre-se de registrar cada comando e hora de execução para manter a cadeia de custódia.

## 5 Orientações de Estudo de Caso e Investigação “Unattended”

Algumas salas práticas da TryHackMe focam em casos de estudo e cenários em que um sistema foi deixado sem supervisão (**unattended**).  A seguir, recomendações gerais para conduzir investigações completas:

1. **Planejamento e documentação** – siga as etapas de apreensão, aquisição, análise e relatório descritas no capítulo 1.  Defina hipóteses e objetivos claros (ex.: determinar se houve exfiltração de dados ou execução de malware).
2. **Aquisição múltipla** – obtenha imagens de disco, cópia da memória RAM e exporte logs de rede.  A aquisição de memória é crucial para revelar processos em execução, conexões ativas e possíveis chaves de descriptografia【702251023194189†L73-L107】.
3. **Linha do tempo integrada** – use ferramentas como **log2timeline/Plaso** ou **KAPE** para coletar artefatos (prefetch, shimcache, amcache, event logs, etc.) e construir uma **super timeline** que correlacione eventos do sistema, aplicações e rede.  Investigue o registro do Windows para evidências de dispositivos removíveis, programas executados e chaves de execução automática【622192434118544†L468-L507】.
4. **Verificação de persistência** – examine tarefas agendadas (`schtasks /query`), serviços (`sc query`), entradas de inicialização (`HKLM\Software\Microsoft\Windows\CurrentVersion\Run`), bem como Shellbags e Jump Lists.  Esses artefatos demonstram quais programas foram usados após o sistema ficar sem supervisão【142134848569929†L120-L160】【261020002357916†L66-L91】.
5. **Corroboração com eventos** – utilize logs do Windows (`Security.evtx` para logins bem‑sucedidos/fracassados – eventos 4624/4625, `System.evtx` para inicializações/desligamentos), registros de antivírus, logs de firewall e logs de proxies para reforçar as conclusões【536392158545277†L8-L10】【536392158545277†L41-L56】.
6. **Análise de exfiltração/disco filtration** – se houver suspeita de exfiltração por canais laterais, considere técnicas como **DiskFiltration**, em que malware modula ruídos do atuador do disco rígido para transmitir dados; ataques desse tipo podem alcançar até ~180 bits/minuto e dois metros de distância【73998737484145†L14-L44】.  Embora raros, reforçam a necessidade de inspecionar todos os vetores (inclusive acústicos) quando se investiga uma máquina “sem vigilância”.
7. **Relatório claro** – compile um relatório que descreva cada artefato, as ferramentas utilizadas, a metodologia e as conclusões.  Use linguagem acessível, inclua hashes de verificação e apresente a linha do tempo de forma visual para facilitar a compreensão por partes interessadas não técnicas.

---

**Próximo passo:** Consulte o `README.md` para navegar pelos capítulos e utilize este documento em conjunto com as demais seções do codex para consolidar seus conhecimentos em forense digital.