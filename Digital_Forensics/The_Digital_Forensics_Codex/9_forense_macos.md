# 9 Perícia forense em macOS

Os computadores Apple apresentam particularidades de hardware e software que dificultam a aquisição e a análise de evidências.  Políticas de segurança robustas, criptografia padrão e proteção do kernel (System Integrity Protection – SIP) limitam o acesso direto a artefatos.  Este capítulo oferece uma visão geral do processo de forense em macOS, explica como adquirir e analisar informações voláteis e persistentes, e lista artefatos comuns e ferramentas.

## 9.1 Processo de investigação em macOS

O fluxo básico de uma investigação em macOS segue os mesmos princípios de outras plataformas: preservar dados, extrair evidências de forma estruturada e analisá‑las.  A documentação do Japan Security Analyst Conference 2022 descreve as cinco etapas essenciais【541412863458435†L117-L132】:

1. **Aquisição e análise de informações altamente voláteis:** inclui coletar dados de memória, processos em execução, tabelas de conexões, etc.;
2. **Aquisição de arquivos de artefatos:** extração de arquivos de configuração e logs (plist, SQLite, etc.);
3. **Aquisição de imagens de disco:** criação de uma cópia bit‑a‑bit dos volumes APFS ou HFS+;
4. **Análise dos arquivos de artefatos:** interpretação de plists, bancos SQLite, logs unificados e outros;
5. **Análise das imagens de disco:** escanear sistemas de arquivos, recuperar arquivos apagados e correlacionar dados com linhas do tempo.

## 9.2 Captura de memória e limitações

Capturar a memória em macOS nem sempre é trivial.  Para sistemas até macOS 10.15.7, a ferramenta **OSXPmem** pode adquirir uma imagem de RAM, porém é preciso compilar o módulo e carregar um driver.  A partir do macOS 11, as opções se tornam escassas; o documento cita o **Surge Collect Pro** como a única ferramenta suportada e observa que, em geral, a coleta de memória exige reinicializar o sistema ou instalar o driver previamente【541412863458435†L139-L155】.  Além disso, há poucas opções de ferramentas de análise: **Volatility** é praticamente a única suportada, e existem poucos plugins para macOS【541412863458435†L160-L169】.  Em razão dessas limitações, muitas investigações concentram‑se na coleta de artefatos persistentes em vez de análise de memória.

## 9.3 Formatos de artefatos: plist e SQLite

A maioria dos artefatos do macOS é armazenada em dois formatos padronizados【541412863458435†L1083-L1104】【541412863458435†L1174-L1189】:

- **Property Lists (plist):** arquivos usados desde a era NeXTSTEP para armazenar configurações de aplicativos, histórico de arquivos abertos e favoritos.  Inicialmente em XML, passaram a ser binários por padrão a partir do Mac OS X 10.4.  Semelhantes ao Registro do Windows, os plists não formam uma base central, mas são distribuídos em diferentes diretórios (por exemplo, `~/Library/Preferences` ou `/System/Library/`).
- **SQLite:** bancos de dados leves usados para armazenar configurações, histórico de aplicativos, caches de navegação (CFURL cache) e estatísticas.  Cada aplicativo pode manter sua própria base sob `~/Library/Application Support` ou `~/Library/Caches`, contendo tabelas de URLs acessadas, dados de formulários e outras informações de valor probatório.

### 9.3.1 Unificado de logs e outros artefatos

A Apple introduziu um sistema de logs unificado que utiliza arquivos comprimidos em `/var/db/diagnostics` e pode ser consultado via `log show` ou parseado por ferramentas como mac_apt.  Outros artefatos relevantes incluem:

- Arquivo `.DS_Store` em cada diretório, contendo nomes de arquivos exibidos no Finder e caminhos de itens antes da exclusão (semelhante ao $I30 no NTFS).
- O *Keychain*, um cofre que armazena senhas de Wi‑Fi, certificados e chaves; para descriptografá‑lo é necessária a chave mestra do usuário.
- Arquivos de histórico do Safari e cookies, armazenados em bases SQLite (`~/Library/Safari/History.db`).

## 9.4 Ferramentas de análise para macOS

Ferramentas open source facilitam a análise de artefatos sem depender de soluções proprietárias【541412863458435†L1215-L1239】:

- **mac_apt:** conjunto com mais de 40 plugins capaz de processar arquivos individuais ou imagens de disco, analisando plists, bancos SQLite e logs unificados.  Permite gerar relatórios sem montar a imagem.
- **AutoMacTC:** coleção de cerca de 26 plugins desenvolvida pela CrowdStrike; atualmente com manutenção limitada.
- **APOLLO:** parser de bancos de dados com informações estatísticas (por exemplo, histórico de localização e utilização de aplicativos), também com pouca manutenção.
- **DSStoreParser:** utilitário dedicado a decodificar `.DS_Store`, extraindo nomes de arquivos e a estrutura persistente do Finder【541412863458435†L1223-L1231】.
- **Chainbreaker:** utilizado para recuperar senhas de redes Wi‑Fi, contas e senhas de aplicativos.  Exige a chave mestra do Keychain, cuja proteção é implementada pelo SIP【541412863458435†L1232-L1239】.

### Ferramentas complementares

Além das ferramentas acima, peritos podem utilizar utilitários portados do mundo UNIX, como `plutil` (conversão entre XML e binário), `sqlite3` (consulta a bases de dados), `fs_usage` (tracing de syscalls para rastrear processos), `fsevents` (histórico de alterações em arquivos) e `log` (consulta aos logs unificados).  Esses comandos ajudam a obter uma visão contextual dos eventos e a correlacionar atividades de usuários, processos e sistema.

## 9.5 Boas práticas

Ao investigar um Mac, lembre‑se de:

1. **Documentar a coleta:** manter cadeia de custódia ao exportar plists e bancos SQLite; usar ferramentas de hash para validar integridade.
2. **Considerar Time Machine e iCloud:** backups locais e sincronizações na nuvem podem fornecer artefatos adicionais.
3. **Verificar permissões:** proteção SIP e criptografia FileVault podem exigir credenciais ou autorização judicial para acessar dados.
4. **Adaptar‑se à versão do sistema:** o APFS evolui constantemente; consulte documentação sobre volumes `Preboot`, `Recovery` e `VM` para identificar onde residem caches e swap.

## 9.6 Artefatos de aplicações: Safari e outros

As aplicações do macOS gravam informações valiosas para a reconstrução de atividades do usuário.  A **mac4n6** project (adaptado pela Forensics Wiki) fornece um catálogo de artefatos e seus caminhos padrão para o macOS 10.9, muitos dos quais continuam presentes em versões posteriores.  A seguir destacam‑se artefatos do navegador **Safari**, pois ele é nativo do sistema e frequentemente utilizado:

### 9.6.1 Safari

Safari armazena seus dados no diretório `~/Library/Safari` e em subpastas de `~/Library/Caches`【680474238593587†L62-L99】.  Os principais artefatos incluem:

* **Bookmarks** – o arquivo `Bookmarks.plist` lista os favoritos padrão e aqueles adicionados pelo usuário【680474238593587†L115-L122】.
* **Downloads** – `Downloads.plist` registra os arquivos baixados, incluindo nomes, destinos e timestamps【680474238593587†L115-L122】.
* **Extensions** – `Extensions.plist` e a pasta `Extensions/*` descrevem extensões instaladas【680474238593587†L139-L165】.
* **History** – `History.plist` contém o histórico de navegação; a base `HistoryIndex.sk` indexa o histórico para pesquisas rápidas【680474238593587†L49-L62】.  Cada entrada inclui URL, título, visitas e timestamps.
* **LastSession.plist** – registra o estado do navegador quando foi fechado, permitindo reconstruir abas abertas【680474238593587†L63-L70】.
* **LocalStorage** – diretório `LocalStorage/*` guarda bases SQLite `.localstorage` utilizadas por sites para armazenar dados offline; o arquivo `StorageTracker.db` acompanha a alocação de local storage【680474238593587†L73-L86】.
* **TopSites.plist** – lista as páginas fixadas como “Top Sites”【680474238593587†L89-L94】.
* **Caches** – os diretórios `~/Library/Caches/com.apple.Safari/*` e `~/Library/Caches/Metadata/Safari/*` armazenam caches de páginas, imagens, ícones, previews e versões de bookmarks【680474238593587†L111-L189】.  A análise desses caches pode revelar conteúdo visualizado mesmo após a limpeza do histórico.

### 9.6.2 Mail, Mensagens e outros aplicativos

Outros aplicativos nativos também mantêm artefatos úteis:

* **Mail** – O cliente **Mail.app** guarda mensagens, anexos e metadados em `~/Library/Mail/V9` (o número da versão varia conforme o macOS).  Cada conta possui um diretório com arquivos `.emlx` (corpo e cabeçalhos) e anexos em subpastas.  Há também um arquivo SQLite `Envelope Index` que indexa mensagens e facilita buscas.
* **Mensagens (iMessage/FaceTime)** – O histórico de mensagens é mantido no banco `chat.db` em `~/Library/Messages`, contendo tabelas com remetentes, destinatários, texto, data/hora e anexos.  As chaves de criptografia podem ficar no Keychain.
* **Notes, Reminders e Calendário** – Esses apps utilizam bancos SQLite em `~/Library/Group Containers` com IDs associados à Apple; os dados contêm notas, lembretes, eventos e alarmes.
* **Terceiros (Slack, Teams, Zoom)** – Muitos aplicativos modernos são baseados em Electron e armazenam dados em `~/Library/Application Support/<App>` e `~/Library/Caches/<App>`.  Eles usam LevelDB/IndexedDB para mensagens e históricos de chamadas, similar ao observado no caso **Teams** (ver seção 3 do capítulo 15).  A localização e o formato variam, portanto recomenda‑se consultar a documentação específica ou analisar com utilitários como `sqlite3`, `strings` e ferramentas de conversão de LevelDB.

### 9.6.3 Metodologia de extração

1. **Montar um volume somente leitura** – Caso trabalhe com uma imagem de disco, monte o volume APFS ou HFS+ em modo read‑only para prevenir modificações.
2. **Copiar artefatos** – Use comandos como `cp -R` ou `rsync -aH` para copiar pastas de artefatos (`Safari`, `Mail`, `Messages`) preservando permissões e timestamps.
3. **Interpretar plists e bases** – Converta plists binárias para XML com `plutil -convert xml1 arquivo.plist -o saida.xml` e analise com editores.  Bancos SQLite podem ser consultados com `sqlite3` ou explorados por ferramentas gráficas como DB Browser for SQLite.
4. **Correlacionar tempos** – Converta timestamps de epoch (por exemplo, `Mac Absolute Time` cuja época começa em 1 jan 2001) para o fuso horário local, e compare com logs unificados (`log show`) e artefatos de sistema (last login, power events).  Essa correlação ajuda a estabelecer uma narrativa dos eventos.

Ao explorar artefatos de aplicações, mantenha a cadeia de custódia e registre os comandos utilizados, garantindo que a evidência seja admissível em processos judiciais.
