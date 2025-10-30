# 8 Ferramentas e complementos em forense digital

Ferramentas especializadas potencializam a capacidade de um perito extrair, analisar e correlacionar dados de diversas fontes.  Além dos conceitos gerais discutidos nos capítulos anteriores, este capítulo apresenta utilitários amplamente usados na comunidade de resposta a incidentes e investigações forenses.

## 8.1 Análise de memória com LiME e Volatility

A memória volátil armazena processos em execução, chaves criptográficas e dados transitórios que desaparecem quando o equipamento é desligado.  **LiME (Linux Memory Extractor)** é um módulo de kernel que permite capturar uma imagem da RAM em sistemas Linux.  Para utilizá‑lo deve‑se compilar e carregar o módulo no kernel; o arquivo gerado terá o mesmo tamanho da RAM e deve ser removido após a captura【454964452511384†L115-L183】.  

**Volatility** é uma estrutura de análise de memória multiplataforma escrita em Python.  O procedimento típico inclui clonar o repositório, instalar dependências como `pycrypto` e `distorm3`, listar perfis suportados (`python2 vol.py --info`) e criar um perfil personalizado quando se trata de distribuições Linux não suportadas【454964452511384†L238-L370】.  Uma vez criado o perfil, diversos plugins permitem identificar o banner do sistema (`linux_banner`), listar processos, extrair conexões de rede, analisar tabelas de arquivos abertos e muitas outras ações.  A combinação LiME + Volatility permite preservar e analisar artefatos de memória que não estão presentes em disco, como processos em execução, chaves de criptografia e sessões de rede.

## 8.2 Autopsy

**Autopsy** é uma plataforma de código aberto para exames forenses em sistemas operacionais Windows, Linux e macOS.  Seu conjunto de recursos inclui:

- Análise de sistemas de arquivos como NTFS, FAT, exFAT, HFS+, ext2/3/4 e UFS;
- Busca por palavras‑chave e expressões regulares;
- Geração de linhas do tempo para correlacionar eventos;
- Extração de artefatos de navegadores, histórico de arquivos e análise de log do Registro do Windows;
- Análise de e‑mails e anexos;
- Carving de arquivos apagados ou fragmentados;
- Relatórios em HTML, KML ou planilhas【819336456215537†L184-L269】.

Por ser modular e extensível, o Autopsy suporta plugins para novos artefatos e pode ser utilizado tanto em investigações forenses quanto em auditorias de conformidade.

## 8.3 KAPE (Kroll Artifact Parser & Extractor)

**KAPE** automatiza a coleta de artefatos e a execução de módulos de análise.  O fluxo de trabalho é dividido em dois componentes principais【300544319078826†L117-L150】:

1. **Targets** – coleções de caminhos de arquivos e diretórios considerados relevantes (por exemplo, evidências de execução, históricos de navegador, dados de contas).  KAPE possui mais de 60 targets pré‑definidos que simplificam a coleta sem exigir que o examinador memorize caminhos específicos.
2. **Modules** – scripts ou programas que processam os arquivos coletados.  Há mais de 90 módulos que executam tarefas como conversão de logs, extração de dados do registro e geração de relatórios.

Com esse modelo, o analista pode apontar para um volume ou imagem, selecionar os targets adequados e executar os módulos, gerando rapidamente evidências estruturadas.  A ferramenta é gratuita para uso governamental, educacional ou interno corporativo.

## 8.4 NetworkMiner

**NetworkMiner** é um analisador de tráfego em modo passivo que extrai artefatos de capturas de pacotes (PCAP) ou escutas ao vivo.  Entre suas capacidades estão:

- Extração de arquivos, imagens, e‑mails e credenciais de protocolos FTP, TFTP, HTTP, SMB e SMTP【714057939806739†L11-L80】;
- Geração de um inventário de hosts a partir de endereços IP e MAC, facilitando a identificação de dispositivos na rede;
- Suporte a arquivos pcapng e ETL, decapsulando túneis GRE, VLAN 802.1Q, PPPoE, VXLAN e outros【714057939806739†L79-L100】;
- Identificação do sistema operacional por fingerprinting, além de JA3/JA4 (impressões digitais de TLS);
- Lookup de certificados X.509 extraídos de sessões TLS;
- Pesquisa de palavras‑chave e integração com serviços OSINT para enriquecer endereços IP.

O NetworkMiner roda em Windows e Linux, possui versão gratuita, é portátil (pode ser executado a partir de pendrive) e permite monitorar ou analisar tráfego sem interferir na rede.

## 8.5 Análise forense do Registro do Windows

O **Registro do Windows** armazena configurações de usuários, hardware e software.  Possui cinco chaves principais (hives): `HKEY_CURRENT_USER`, `HKEY_LOCAL_MACHINE`, `HKEY_CLASSES_ROOT`, `HKEY_USERS` e `HKEY_CURRENT_CONFIG`.  Para análises offline, a Microsoft documenta que os arquivos que compõem esses hives ficam em `%SystemRoot%\System32\Config`, contendo cópias completas, logs de transação e backups【822691451735216†L270-L314】.  Ferramentas como **Registry Explorer** e **RegRipper** permitem carregar hives e extrair valores.

Artefatos importantes incluem【622192434118544†L468-L507】:

- **NetworkList\Profiles** – lista redes sem fio conectadas anteriormente, armazenando SSID, timestamps e endereços MAC, útil para provar a presença em um local;
- **RecentDocs** – mantém nomes de arquivos recentemente acessados;
- **TypedURLs** – registra URLs digitadas nos navegadores do Internet Explorer/Edge;
- **ShellBags** – rastreia pastas navegadas no Explorer, contendo timestamp de última interação;
- **UserAssist**, **AmCache**, **BAM/DAM** – artefatos que indicam programas executados, contagem de execuções e caminhos completos;
- **USBSTOR** – identifica dispositivos USB conectados (fabricante, número de série, letra da unidade etc.).

Esses dados ajudam a reconstruir o uso do computador (usuários, dispositivos montados, software executado) e podem ser correlacionados com outras evidências.

## 8.6 Utilitários para macOS

Na forense de macOS, a maioria dos artefatos são armazenados em dois formatos padrão: **Property Lists (plist)** e **SQLite**.  Arquivos plist existem desde a era NeXTSTEP, passaram por formato XML no Mac OS X 10.0 e adotaram um formato binário a partir da versão 10.4【541412863458435†L1083-L1104】.  Esses arquivos gravam configurações de aplicativos, histórico de arquivos abertos e marcadores, funcionando de forma semelhante ao Registro do Windows, porém distribuídos por diversos diretórios.  Já as bases **SQLite** armazenam definições de aplicativos, histórico de navegação e cache HTTP/HTTPS, podendo conter blobs de dados e estatísticas【541412863458435†L1174-L1189】.

Ferramentas open source para analisar esses artefatos incluem:

- **mac_apt** – utilitário com mais de 40 plugins que analisa arquivos individuais e imagens de disco, permitindo processar artefatos sem montar a imagem【541412863458435†L1215-L1221】;
- **AutoMacTC** – projeto da CrowdStrike com cerca de 26 plugins, hoje menos mantido【541412863458435†L1215-L1221】;
- **APOLLO** – parser de bases de dados com estatísticas de utilização, também com manutenção limitada【541412863458435†L1215-L1221】;
- **DSStoreParser** – decodifica os arquivos `.DS_Store`, análogos ao atributo $I30 do NTFS, registrando nomes de arquivos em pastas e caminhos anteriores de itens apagados【541412863458435†L1223-L1231】;
- **Chainbreaker** – extrai chaves de Wi‑Fi e senhas de aplicativos/contas, exigindo a chave mestra do Keychain【541412863458435†L1232-L1239】.

Esses utilitários, combinados com conhecimentos sobre a estrutura de diretórios e sistemas de arquivos do macOS (ver Capítulo 9), são indispensáveis para examinar computadores Apple.
