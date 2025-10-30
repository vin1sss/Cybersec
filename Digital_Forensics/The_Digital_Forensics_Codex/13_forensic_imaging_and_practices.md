# 13 Imaging forense, Carving de Arquivos e Exercícios Práticos

A investigação de um incidente real raramente se resume à análise teórica.  
Ela exige a aquisição correta das mídias, a recuperação de arquivos perdidos e a criação de linhas do tempo para explicar a sequência de eventos.  
Este capítulo sintetiza técnicas de imaging forense, explica a arte do *file carving* e fornece diretrizes para exercícios práticos de análise, baseando‑se em fontes confiáveis e em procedimentos consagrados pela perícia.

## 13.1 Aquisição de imagens forenses

Uma imagem forense é uma cópia bit a bit de um disco ou outro dispositivo de armazenamento.  
Ao contrário de uma cópia de arquivos convencional, a imagem preserva cada setor, inclusive regiões não alocadas, possibilitando a recuperação de dados deletados.  
O CTF Handbook destaca que a criação de imagens forenses impede alterações no original, permite repetir análises e facilita o transporte das provas【910566956779199†L104-L143】.  
Para garantir a integridade, a imagem deve ser feita com **write‑blockers**, dispositivos que impedem qualquer escrita acidental no meio original【910566956779199†L144-L171】.  

Além disso, a verificação da imagem por meio de *hashes* (MD5 ou SHA‑256) é essencial. O cálculo do hash antes e depois da cópia assegura que a imagem é idêntica ao original, uma prática exigida em tribunais【910566956779199†L104-L143】.  

### Criando uma imagem com FTK Imager

O FTK Imager é uma das ferramentas gratuitas mais utilizadas. O processo básico inclui:

1. Conectar o dispositivo de origem a um write‑blocker e, em seguida, ao computador forense.  
2. Abrir o FTK Imager e selecionar *File > Create Disk Image*. Escolher a opção de mídia (p.ex., *Physical Drive*) e apontar para o disco a ser adquirido.  
3. Definir o local de destino, o tipo de imagem (por exemplo, formato **raw** `.dd`) e preencher informações de caso (nome do examinador, número do caso etc.).  
4. Iniciar a criação da imagem. Ao fim, o programa calculará automaticamente o hash e exibirá um relatório de verificação【910566956779199†L144-L171】.

Essa metodologia pode ser aplicada a outras ferramentas, como **dd** no Linux ou **Guymager**. O importante é garantir a cadeia de custódia e documentar cada passo.

## 13.2 File carving

Em muitos casos, os metadados do sistema de arquivos estão ausentes ou corrompidos – seja por formatação, exclusão ou até tentativa de ocultação. **File carving** é a técnica que permite recuperar arquivos nesses cenários.  
Ela escaneia byte a byte o fluxo de dados em busca de *assinaturas* de início e fim conhecidas (headers/footers) ou estruturas internas específicas. Esse método permite reconstruir arquivos sem depender de metadados【114779916140487†L65-L80】.

### Métodos de carving

* **Carving por cabeçalho‑rodapé ou tamanho máximo:** procura sequências de bytes que identificam o início e o fim de um tipo de arquivo. Um exemplo clássico é o JPEG, cujo cabeçalho é `0xFF 0xD8` e o rodapé `0xFF 0xD9`【114779916140487†L87-L102】.  
  Quando não há rodapé confiável, define‑se um tamanho máximo para o arquivo.

* **Carving baseado em estrutura de arquivo:** explora a organização interna (tabelas de alocação, tamanhos de bloco, campos fixos) para reconstituir o conteúdo【114779916140487†L105-L116】.

* **Carving baseado em conteúdo:** utiliza características do conteúdo, como contagem de caracteres, detecção de idiomas, entropia e assinaturas de formato (HTML, XML, MBOX etc.) para recuperar dados【114779916140487†L115-L124】.

### Ferramentas para carving

O artigo da Cybervie lista várias ferramentas consagradas【114779916140487†L186-L223】:

| Ferramenta            | Características principais |
|----------------------|---------------------------|
| **Scalpel**         | Versão reprogramada do Foremost; utiliza cabeçalhos/rodapés; configuração em `/etc/scalpel/scalpel.conf`; comando típico: `scalpel -b -o /destino imagem.img`【114779916140487†L165-L181】. |
| **bulk_extractor**   | Analisa imagens ou arquivos em busca de strings e padrões (e‑mails, números de cartão, URLs); suporta formatos `.E01` e `.aff`; gera arquivos separados por tipo de evidência【114779916140487†L186-L223】. |
| **Foremost**         | Recupera arquivos de diversos tipos (JPG, GIF, PNG, EXE, RAR, ZIP, MP4 etc.) usando cabeçalhos/rodapés e estruturas; instalado via `sudo apt install foremost` e configurável por linha de comando【114779916140487†L229-L262】. |
| **Outras**           | FTK, EnCase, PhotoRec, TestDisk e Magic Rescue também realizam carving e podem ser usados conforme o ambiente【114779916140487†L266-L275】. |

O perito deve sempre validar os arquivos recuperados comparando com hashes originais (quando disponíveis) e documentar o procedimento.

## 13.3 Análise de sistemas de arquivos EXT e linhas do tempo

As partições Linux baseadas em EXT (2/3/4) armazenam os metadados em **inodes**, estruturas equivalentes às entradas do MFT no NTFS. Cada inode guarda os tempos de acesso (A), modificação (M), alteração de metadados (C) e, no EXT4, também a criação (crtime)【627841881652624†L86-L104】.  
No EXT2/EXT3 não há registro de criação; apenas modificação, acesso, alteração e deleção【627841881652624†L86-L103】.

### Processos básicos

1. **Identificar partições e sistemas de arquivos.** Ferramentas como `mmls` e `fsstat` do Sleuth Kit listam a tabela de partições e exibem detalhes da partição (tipo, tamanho de bloco, número de inodes, timestamps)【627841881652624†L42-L48】.  
2. **Desmontar LVM e montar volumes.** Em discos com **Logical Volume Manager (LVM)**, use `kpartx`, `pvs`, `vgscan` e `vgchange` para mapear volumes e montá‑los como somente leitura【627841881652624†L52-L67】.  
3. **Gerar linha do tempo.** Após montar o volume, utiliza‑se `fls` para produzir um *body file* com MAC times de arquivos alocados e deletados. O utilitário `mactime` converte esse body file em uma linha do tempo legível. Para uma linha do tempo mais abrangente (super timeline), a ferramenta **Plaso (log2timeline)** analisa múltiplos artefatos (syslog, audit, utmp, etc.) e gera um banco de dados Plaso, que pode ser exportado para CSV usando `psort.py`【627841881652624†L112-L124】.

### Leitura de metadados

Para examinar metadados de um arquivo individual, obtém‑se seu inode com `ls -i` e utiliza‑se o comando `istat` apontando para a partição montada e o número do inode. O `istat` exibe tamanhos, permissões, UID/GID e todos os timestamps【627841881652624†L104-L110】.

## 13.4 Diretrizes para análises práticas

Ao lidar com imagens de casos reais ou laboratórios (como os desafios práticos em plataformas de aprendizagem), algumas etapas se repetem independentemente do sistema:

1. **Coletar informações básicas do sistema.** Determine o tipo de sistema de arquivos e o sistema operacional (Windows, Linux, macOS). Em sistemas Windows, recupere número de compilação, chave *ControlSet* atual e nomes de máquina a partir do registro (`System\CurrentControlSet\Control\ProductOptions` e `ComputerName\ComputerName`). Esses dados ajudam a contextualizar a linha do tempo e a identificar configurações de fuso horário.

2. **Analisar o registro (Windows).** Copie os hives (`SAM`, `SYSTEM`, `SOFTWARE`, `SECURITY`, `DEFAULT`) da pasta `%SystemRoot%\System32\Config` e use ferramentas como **RegRipper** ou **Eric Zimmerman Tools** para extrair informações. O artefato **AmCache** registra execução de programas e respectivos hashes, enquanto **BAM/DAM** armazena caminhos completos e dados de execução【622192434118544†L468-L507】. As chaves *NetworkList* e *RecentDocs* revelam redes Wi‑Fi utilizadas e arquivos recentemente acessados【622192434118544†L468-L507】.

3. **Evidências de execução e uso.** Em Windows, utilize artefatos como **Prefetch** (`C:\Windows\Prefetch`), **ShimCache/AppCompatCache**, **UserAssist**, **Jump Lists** e **Timeline Activity** para rastrear programas executados, contagem de execuções, tempos de foco e arquivos acessados. Ferramentas como **PECmd**, **MFTECmd**, **JLECmd** e **WxTCmd** (todas de Eric Zimmerman) automatizam a extração desses dados. Prefetch registra até 30 execuções recentes de cada aplicativo, enquanto a Timeline (Activity Cache) armazena atividades de 30 dias, incluindo duração em foco e documentos abertos.

4. **Dispositivos externos e logs de inicialização.** O arquivo `setupapi.dev.log` contém registros de instalação de dispositivos USB e pode ser utilizado para identificar modelos, números de série e horários de conexão. O registro `USBSTOR` guarda histórico de dispositivos USB montados【622192434118544†L468-L507】.

5. **Recuperação de arquivos deletados.** Após adquirir a imagem, utilize ferramentas de carving (Scalpel, Foremost, PhotoRec) para recuperar documentos, planilhas ou imagens excluídas. Sempre registre o caminho original (quando possível) e os hashes das evidências recuperadas.

6. **Validação e cronologia.** Construa uma linha do tempo combinando metadados de arquivos, logs do sistema e artefatos de execução. Compare horários de modificação, criação e acesso para entender a sequência dos eventos. Em Linux/ext, utilize `fls` e `mactime`; em Windows, artefatos de registro e prefetch; em macOS, explore arquivos plist e bancos SQLite para atividade de usuários.

## 13.5 Exercícios recomendados

Para consolidar o conhecimento, é recomendável praticar com imagens de discos e memórias públicas ou provenientes de laboratórios. Alguns desafios típicos incluem:

* **Análise de um caso Windows:**  
  - Extrair os hives e usar RegRipper/RECmd para identificar contas de usuário, programas instalados e chaves de inicialização.  
  - Utilizar MFTECmd para analisar o `$MFT` e identificar arquivos recém‑criados ou deletados, bem como recuperar nomes de arquivos e tamanhos【974492827060600†L120-L314】.  
  - Executar PECmd e JLECmd para avaliar Prefetch e Jump Lists, verificando quantas vezes e quando determinados executáveis foram acionados.  
  - Verificar `setupapi.dev.log` e chaves `USBSTOR` para enumerar dispositivos externos conectados.

* **Análise de sistema Linux:**  
  - Verificar a partição com `mmls`, montar volumes somente leitura e gerar uma linha do tempo com `fls`/`mactime` ou **Plaso**【627841881652624†L42-L67】.  
  - Analisar logs de autenticação (`/var/log/auth.log`, `btmp`, `wtmp`) para identificar tentativas de login e intrusões.  
  - Usar `grep`, `awk` e `strings` em arquivos de configuração e executáveis suspeitos.

* **File carving:**  
  - Criar uma imagem de um pendrive, calcular o hash e, usando Scalpel ou Foremost, recuperar arquivos de tipos específicos (JPEG, PNG, DOCX).  
  - Documentar o procedimento, incluindo assinaturas de cabeçalho e rodapé usadas e comparar os arquivos recuperados com amostras originais, quando disponíveis.

* **Memória e processos:**  
  - Capturar a memória de uma máquina Windows com **winpmem** (ou Magnet RAM Capturer) e analisar com **Volatility 3**.  
  - Listar processos (`windows.pslist`), sessões de logon (`windows.logon.LogonSessions`), conexões de rede (`windows.netstat.NetStat`) e procurar código injetado com `windows.malfind.Malfind`【968067856108800†L130-L190】.  
  - Em seguida, extrair objetos ou DLLs suspeitos da memória e realizar *strings* ou análise estática.

Esses exercícios ajudam a desenvolver a intuição necessária para correlacionar artefatos e chegar a conclusões sólidas em uma investigação forense.
