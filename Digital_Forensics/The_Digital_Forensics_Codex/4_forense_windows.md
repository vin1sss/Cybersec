# 4 Análise forense no Windows

Os sistemas Windows armazenam numerosos artefatos que revelam atividades de usuários e aplicações.  A análise forense envolve correlacionar múltiplas fontes — como logs, registros, arquivos de sistema e metadados — para reconstruir eventos com precisão.

## 4.1 Análise de linha do tempo

Construir uma linha do tempo é essencial para entender quando e como certos eventos ocorreram.  Em Windows, fontes importantes de tempo incluem:

1. **Timestamps de sistema de arquivos**: o NTFS armazena vários carimbos (criação, modificação, acesso e alteração do MFT) para cada arquivo; o USN Journal e o $LogFile registram alterações em arquivos e diretórios【775749847023820†L74-L104】【775749847023820†L208-L235】.  Ao correlacionar esses registros, é possível descobrir quando arquivos foram criados, renomeados ou excluídos.  
2. **Logs de eventos (Event Logs)**: arquivos `.evtx` registram eventos do sistema (inicialização, logon/logoff, instalações, falhas).  Ferramentas como *Event Log Explorer*, *Chainsaw* ou *Hayabusa* ajudam a filtrar eventos relevantes por ID, categoria ou usuário.  
3. **Prefetch**: o Windows cria arquivos `.pf` na pasta `C:\Windows\Prefetch` contendo informações sobre cada programa executado — nome do executável, caminho, bibliotecas utilizadas, contagem de execuções e **timestamps de execução**【915223127759981†L117-L163】.  Esses arquivos persistem mesmo após a exclusão do programa, permitindo verificar se e quando um aplicativo foi aberto【915223127759981†L136-L152】.  
4. **LNK (atalhos)**: arquivos `.lnk` guardam caminho de destino, data/hora de criação, último acesso e modificação do arquivo original.  Podem revelar unidades removíveis ou arquivos acessados anteriormente.
5. **Jump Lists e RecentDocs**: o Windows mantém listas de arquivos recentemente abertos por aplicativos, armazenadas em pastas de usuário (`C:\Users\<user>\AppData\Roaming\Microsoft\Windows\Recent`).  
6. **System Resource Usage Monitor (SRUM)**: banco de dados que registra o uso de recursos (CPU, memória, rede) por processos; permite verificar quanto tempo um aplicativo permaneceu em execução.
7. **Shadow Copies**: snapshots automáticos que capturam o estado do volume em momentos distintos.  Além de permitir recuperação de arquivos, podem ser usados para verificar versões anteriores de artefatos e identificar alterações.

Ao compilar os timestamps dessas fontes em ordem cronológica, o investigador obtém uma visão detalhada das ações do usuário.  É importante normalizar os formatos (Unix, FILETIME, WebKit) e converter para o fuso horário correto【764236959555076†L48-L87】.

## 4.2 Recuperação de arquivos

Usuários podem excluir arquivos, mas isso geralmente remove apenas as referências, deixando os dados físicos intactos até serem sobrescritos.  Técnicas comuns incluem:

1. **Restaurar da Lixeira**: arquivos enviados para a Recycle Bin podem ser recuperados se não foram esvaziados.  O Windows cria entradas no `\$Recycle.Bin` com metadados, incluindo o caminho original e a data de deleção.  
2. **Recuperação por backups e sombras**: *Volume Shadow Copies* e backups do sistema podem conter versões anteriores de arquivos.  Ferramentas como *ShadowExplorer* permitem montar snapshots e restaurar arquivos específicos.  
3. **File carving**: para arquivos permanentemente excluídos, ferramentas de carving buscam assinaturas de cabeçalho/rodapé (por exemplo, JPEG `FFD8 FFD9` ou PDF `%PDF`).  O método examina espaço não alocado e slack para recuperar fragmentos.  
4. **Sistemas de arquivos e logs**: no NTFS, o USN Journal registra alterações e pode fornecer o nome de arquivos apagados e o momento da exclusão【775749847023820†L208-L235】.  O $LogFile permite reconstruir transações e alterações não confirmadas.  
5. **Recuperação de arquivos em SSDs**: devido ao comando TRIM, dados excluídos em SSDs podem ser imediatamente apagados; a recuperação é mais difícil e depende de ferramentas especializadas.

## 4.3 Análise do Registro do Windows

O **Windows Registry** é uma base de dados hierárquica usada pelo sistema operacional e aplicativos para armazenar configurações e dados de usuários.  Ele contém informações sobre programas instalados, dispositivos conectados, preferências, senhas armazenadas e muito mais.  Analisar o registro pode fornecer evidências cruciais de atividade:

- **Hives e arquivos**: os dados do registro são armazenados em arquivos `REGF` no diretório `C:\Windows\System32\Config\` (por exemplo, `SYSTEM`, `SOFTWARE`, `SECURITY`) e em arquivos específicos do usuário (`NTUSER.dat`, `USRCLASS.dat`)【129643209287328†L107-L129】.  Cada hive possui **logs de transação** que registram alterações pendentes【129643209287328†L124-L176】.  
- **Chaves e valores**: chaves são pastas lógicas que contêm subchaves e valores.  Cada chave possui um **timestamp de último write** que registra quando foi modificada【129643209287328†L139-L151】.  Os valores guardam dados em diferentes tipos (strings, números, binários).  
- **Artefatos importantes**: 
  * **Run/RunOnce**: chaves usadas para iniciar programas automaticamente; podem indicar persistência de malware.  
  * **RecentDocs** e **UserAssist**: listas de arquivos e programas abertos recentemente.  
  * **TypedPaths** e **TypedURLs**: históricos de caminhos e URLs digitados.  
  * **USBSTOR** (no hive SYSTEM): registra dispositivos USB conectados (VendorID, ProductID e número de série).  
  * **LastVisitedMRU**: histórico de arquivos e locais acessados no Explorer.  
- **Análise temporal**: correlacionar os timestamps de chaves com eventos de logs e arquivos ajuda a determinar quando um usuário executou certos programas ou conectou dispositivos.  
- **Ferramentas**: *RegRipper*, *Registry Explorer* e *Cyber Triage* auxiliam na extração e interpretação de valores.  O artigo da Cyber Triage destaca que o registro contém dados sobre atividade do usuário, configuração do sistema, dispositivos conectados e persistência de malware【129643209287328†L83-L99】.

## 4.4 Análise forense de recursos do Windows

Além de arquivos e registro, diversos recursos do Windows armazenam rastros importantes:

1. **Prefetch**: como visto, os arquivos `.pf` revelam programas executados e detalhes de carregamento.  O artigo da Salvation Data enfatiza que prefetches guardam o nome do programa, timestamps de execução, contagem de execuções e referências a bibliotecas, servindo como evidência robusta de atividade【915223127759981†L117-L163】【915223127759981†L136-L152】.
2. **Event Tracing for Windows (ETW)**: infraestrutura interna para rastrear eventos de baixo nível; logs de ETW podem registrar chamadas de API, permitindo detectar comportamentos suspeitos.  Ferramentas como *Kape* ou *Hayabusa* podem extrair e analisar logs ETW.
3. **System Resource Usage Monitor (SRUM)**: banco de dados SQLite (`SRUDB.dat`) que registra uso de recursos e conexão de rede por aplicativos; útil para identificar programas que consumiram largura de banda em um período específico.
4. **LNK/Shortcut Files**: atalho criado ao abrir arquivos executáveis ou documentos; armazena caminho original, timestamps e drive serial.  
5. **Jump Lists**: arquivos `.automaticDestinations-ms` e `.customDestinations-ms` localizados em `AppData\Roaming\Microsoft\Windows\Recent\JumpList`.  Contêm listas de arquivos e pastas recentemente acessados por aplicativos como Microsoft Office e navegadores.
6. **SRUM e Prefetch**: combinados, permitem inferir quanto tempo um programa ficou em execução e quando foi iniciado.  A contagem de execuções nos prefetch files ajuda a identificar padrões de uso.
7. **Arquivos de paginação e hibernação (pagefile.sys e hiberfil.sys)**: podem conter dados sensíveis que foram trocados da memória para o disco, incluindo textos de chat, chaves ou senhas.  Embora a análise seja complexa, estes arquivos podem revelar informações que não aparecem em dumps de memória.

A análise forense no Windows exige considerar a interação entre esses artefatos e entender como o sistema opera internamente.  Correlacionar dados de múltiplas fontes produz uma narrativa robusta dos acontecimentos e ajuda a detectar manipulações ou antievidências.