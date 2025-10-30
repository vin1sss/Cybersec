# 1 Introdução e definições

## 1.1 Visão geral da forense digital

A forense digital é a disciplina que **recupera, preserva e analisa** informações armazenadas em dispositivos eletrônicos para apoiar investigações civis, administrativas ou criminais.  O Departamento de Segurança Interna dos EUA define a forense digital como o processo de *recuperar e preservar material em dispositivos digitais durante investigações criminais*【599437810137191†L233-L257】.  Para o NIST, *evidência digital* inclui qualquer informação em formato binário que possa ser usada em tribunal; os dados residem em mídias físicas como discos rígidos, SSDs, smartphones ou sistemas de armazenamento em nuvem【599437810137191†L233-L257】【722876861364628†L180-L184】.

O campo evoluiu a partir da “computação forense”, mas hoje engloba computadores pessoais, dispositivos móveis, sistemas embarcados, redes, aplicações em nuvem e bancos de dados.  À medida que crimes digitais se tornam mais sofisticados, a forense digital combina conhecimentos em sistemas operacionais, redes, criptografia e direito para reconstruir atividades, identificar autores e proteger a cadeia de custódia das evidências.

## 1.2 Crimes cometidos com o uso de equipamentos computacionais

Os crimes digitais podem envolver fraude financeira, invasões de sistemas, furto de propriedade intelectual, distribuição de malware, pornografia infantil, violação de direitos autorais, chantagem ou espionagem industrial.  Muitos delitos tradicionais — como estelionato ou difamação — ganharam variantes eletrônicas por meio de redes sociais e serviços de mensagens.  Além dos crimes cometidos diretamente via computador, há casos em que dispositivos são usados para planejar ou facilitar crimes físicos (por exemplo, coordenação de ataques ou comunicação entre criminosos).  A investigação exige coletar registros de servidores, e‑mails, logs de aplicativos e rastros em redes sociais.

## 1.3 Categorias e tipos de perícia forense digital

A classificação mais comum divide a perícia em **categorias de fontes de evidência** e **áreas de especialização**.  Uma fonte de evidência pode ser:

- **Mídias físicas** (discos rígidos, SSDs, drives USB, cartões de memória, CDs/DVDs);  
- **Imagens ou cópias de arquivos** (imagens forenses de disco, snapshots de máquinas virtuais, backups);  
- **Objetos digitais diversos** (logs de sistemas, registros de aplicações, dados de sensores, cópias de mensagens instantâneas);  
- **Evidências geradas pela aplicação da lei**, como relatórios e registros de investigações anteriores【599437810137191†L259-L273】.

As áreas de especialização incluem:

- **Forense de computadores**: análise de desktops, laptops e servidores, incluindo sistemas de arquivos e registros de sistema.  
- **Forense de dispositivos móveis**: extração de dados de smartphones e tablets.  
- **Forense de redes**: captura e análise de dados em trânsito para identificar intrusões, reconstruir ataques e monitorar desempenho【70435781892618†L81-L90】.  
- **Forense de memória**: coleta e análise de dados voláteis (RAM) para identificar malware injetado e chaves de criptografia【347394763141159†L573-L618】.  
- **Forense de nuvem**: coleta e preservação de dados armazenados em serviços de nuvem.  
- **Forense de bancos de dados**: reconstrução de operações em bancos de dados e análise de logs de transações.  
- **Análise forense de dados**: uso de técnicas estatísticas ou de aprendizado de máquina para examinar grandes volumes de dados em busca de padrões ou anomalias【270434847143426†L97-L115】.

Os processos seguem um ciclo com quatro fases descritas no NIST SP 800‑86: **coleta**, **exame**, **análise** e **relato**【270434847143426†L129-L161】.  Cada fase deve assegurar a integridade das evidências por meio de procedimentos padronizados e documentação adequada.

## 1.4 Resposta a incidentes

A resposta a incidentes é o conjunto de ações tomadas para identificar, conter e remediar incidentes de segurança.  A revisão 3 da NIST SP 800‑61 integrou a resposta a incidentes ao **Framework de Cibersegurança** (CSF) 2.0, dividindo o ciclo em três fases contínuas: **Detectar**, **Responder** e **Recuperar**, apoiadas por atividades de **Governança, Identificação e Proteção**【670819047839443†L123-L145】.  O objetivo é preparar organizações para responder rapidamente a incidentes, preservar evidências, avaliar o impacto e restaurar os serviços com mínimo de danos.  A documentação da resposta (logs, relatórios e cronologias) faz parte da evidência digital, sendo posteriormente utilizada pela equipe forense.

## 1.5 Evidência digital

Evidências digitais incluem **qualquer informação em formato binário** que possa ser útil em uma investigação ou processo judicial.  Esses dados residem em discos, SSDs, smartphones, sistemas de armazenamento em nuvem, dispositivos IoT ou redes.  A preservação da cadeia de custódia exige que a coleta e o armazenamento sejam realizados de maneira a garantir a integridade (por exemplo, utilizando algoritmos de hash para comprovar que a cópia é idêntica ao original【38030209527614†L266-L287】).  Segundo NIST, as evidências devem ser adquiridas com **procedimentos seguros** que impeçam a modificação dos dados e permitam reprodutibilidade【722876861364628†L180-L184】.

## 1.6 Representação de dados

Os computadores armazenam dados como sequências de bits.  Para interpretar esses bits corretamente, é necessário entender:

- **Sistemas numéricos**: binário (base 2), hexadecimal (base 16) e decimal (base 10).  Hexadecimal é frequentemente utilizado em análises forenses porque compacta quatro bits em um dígito e facilita a visualização de dados brutos.  
- **Codificação de caracteres**: esquemas como ASCII e Unicode mapeiam números a símbolos.  Ao examinar arquivos, analistas convertem sequências de bytes em texto para identificar strings legíveis.  
- **Endianess**: ordem dos bytes em números multibyte.  Sistemas “little endian” armazenam o byte menos significativo primeiro; sistemas “big endian” armazenam o mais significativo primeiro.  Ao interpretar campos de cabeçalhos (por exemplo, tamanho de setores em um volume FAT), é fundamental converter corretamente de acordo com a endianness para obter valores corretos.  
- **Estruturas de dados**: muitas evidências residem em formatos estruturados (por exemplo, tabela MFT do NTFS, registros do registry).  Ferramentas de hex‑editor permitem visualizar a representação bruta e identificar assinaturas (magic numbers) e cabeçalhos/rodapés que delimitam arquivos.

## 1.7 Estrutura de arquivos

Cada tipo de arquivo possui **cabeçalhos** e **rodapés** que indicam onde o conteúdo começa e termina.  Por exemplo, arquivos JPEG começam com `FFD8` e terminam com `FFD9` em hexadecimal.  Em investigações, a técnica de **file carving** utiliza essas assinaturas para recuperar arquivos de setores não alocados ou fragmentados.  Além disso, sistemas de arquivos mantêm tabelas (como a MFT no NTFS) que armazenam metadados sobre cada arquivo: nome, tamanho, timestamps, localização dos blocos e permissões.  Esses registros permitem reconstruir a estrutura de diretórios mesmo quando o arquivo foi deletado.

## 1.8 Metadados de arquivos digitais

Metadados são dados que descrevem outros dados.  Podem ser **descritivos** (ex.: título, autor e palavras‑chave em um documento), **estruturais** (organização interna de um arquivo) ou **administrativos** (informações sobre criação, modificação, permissões e localização)【351744340932798†L42-L64】.  A análise de metadados envolve as etapas de coleta, exame, correlação e interpretação【351744340932798†L83-L104】.  Investigadores utilizam metadados para construir cronologias, identificar autores, verificar a autenticidade de documentos e detectar manipulações (por exemplo, divergências entre o timestamp embutido em um arquivo e o timestamp do sistema de arquivos).

## 1.9 Decodificação de timestamps

Timestamps registram quando eventos ocorreram, mas diferentes sistemas usam **épocas e resoluções distintas**.  Investigações devem considerar:

- **Formato Unix/POSIX**: segundos (e às vezes microssegundos) desde 1º de janeiro de 1970 (UTC).  
- **WebKit/Chrome**: microsegundos desde 1º de janeiro de 1601.  
- **Apple Cocoa**: segundos desde 1º de janeiro de 2001.  
- Alguns sistemas armazenam timestamps em nanosegundos ou com resoluções maiores e podem usar fuso horário UTC ou local.  Para apresentação em tribunal, peritos devem converter para o horário da jurisdição e explicar a origem do valor【838057224104116†L66-L99】【764236959555076†L48-L87】.  

Timestamps podem estar ocultos em fluxos de dados ou codificados em estrutura binária.  Ferramentas como *Cellebrite Physical Analyzer* decodificam automaticamente timestamps de diferentes formatos【764236959555076†L90-L112】, mas analistas devem validar os resultados manualmente para garantir precisão.

## 1.10 Análise de hash

Um **hash** é uma sequência de caracteres gerada por uma função matemática a partir de uma entrada arbitrária.  Para fins forenses, as funções de hash (MD5, SHA‑1, SHA‑256) são **unidirecionais** (não é possível reconstruir a entrada a partir do hash) e **sensíveis a alterações** (qualquer modificação no arquivo altera completamente o hash).  Agências de segurança como a CISA destacam que valores de hash provam que uma cópia é idêntica ao original e podem ser usados para certificar evidências em tribunal【38030209527614†L266-L287】.  As regras federais de evidência dos EUA (regra 902 (13) e 902 (14)) permitem a **auto‑autenticação** de registros digitais quando acompanhados de uma certificação que ateste a integridade baseada em hash【38030209527614†L343-L347】.  Durante a coleta, peritos calculam o hash da mídia original e de cada cópia; qualquer discrepância indica adulteração.  Hashes também são utilizados para detectar arquivos conhecidos (listas *whitelist* ou *blacklist*) e identificar duplicatas.

## 1.11 Tipos de memória

Os computadores utilizam diferentes tipos de memória com características distintas:

1. **Memória volátil**: inclui a *memória de acesso aleatório (RAM)* e caches da CPU.  Os dados são mantidos apenas enquanto o equipamento está ligado; após desligar, a informação se perde.  Durante incidentes, é crucial capturar a memória RAM imediatamente, pois ela pode conter processos em execução, conexões de rede, senhas e chaves criptográficas【347394763141159†L573-L618】.  
2. **Memória não volátil**: mantém dados mesmo sem alimentação elétrica.  Exemplos incluem ROM, EEPROM, e NVRAM em dispositivos embarcados.  Essas áreas armazenam firmware e configurações básicas.
3. **Memória secundária (armazenamento)**: discos rígidos (HDD), unidades de estado sólido (SSD), cartões SD e pendrives.  Não são apagados ao desligar o equipamento e servem para armazenamento permanente.  A análise forense concentra‑se em setores, clusters e sistemas de arquivos.

## 1.12 Tipos de armazenamento dos computadores

A mídia de armazenamento influencia a forma de aquisição da evidência:

- **HDD (Hard Disk Drive)**: utiliza pratos magnéticos giratórios e um braço mecânico.  Permite capacidades elevadas e custo reduzido, mas tem latência maior comparado a SSDs.  A recuperação de dados pode envolver a análise de áreas não alocadas, espaço slack e setor danificado.  
- **SSD (Solid State Drive)**: baseado em memória flash NAND.  Oferece alta velocidade, ausência de partes móveis e suporta comandos de gerenciamento como TRIM (que pode apagar permanentemente blocos não utilizados).  A TRIM e a compressão interna dificultam a recuperação de dados deletados; por isso, ferramentas de aquisição devem ser compatíveis com SSDs.  
- **NVMe**: interface moderna que utiliza o barramento PCI Express para comunicação de alta velocidade.  Possui latência menor que SATA e protocolos paralelos.  A aquisição pode exigir adaptadores específicos para preservar integridade.
- **Armazenamento ótico** (CD, DVD, Blu‑ray) e **fitas magnéticas**: ainda utilizados em ambientes corporativos para backups.  A leitura requer drives adequados e verificação de integridade.  
- **Armazenamento na nuvem**: dados hospedados em provedores externos podem ser replicados geograficamente.  A obtenção de evidências exige cooperação do provedor, análise de logs de acesso e cumprimento de legislações de privacidade.

## 1.13 Sistemas de arquivos

Sistemas de arquivos organizam dados em volumes e fornecem metadados.  Cada sistema oferece recursos e desafios específicos para forense:

- **FAT (FAT12/FAT16/FAT32)**: surgiu em disquetes e foi adaptado para HDs e dispositivos removíveis.  Sua simplicidade facilita a análise — a tabela de alocação registra nome, tamanho, atributos e cluster inicial, mas não armazena a data de criação e não mantém histórico de exclusões; nomes de arquivos apagados e dados slack podem ser perdidos【303309481301383†L56-L70】.  
- **NTFS (New Technology File System)**: padrão de sistemas Windows.  Possui segurança embutida, journaling, suporte a arquivos grandes, compressão e criptografia.  Os metadados são centralizados na *Master File Table (MFT)*, que registra detalhes e múltiplos timestamps para cada arquivo.  O NTFS mantém artefatos importantes como o **$LogFile**, com registros de transações, e o **USN Journal ($UsnJrnl)**, que registra alterações em arquivos e diretórios — ambos auxiliam na reconstrução de eventos e identificação de deleções【775749847023820†L74-L104】【775749847023820†L208-L235】.  
- **EXT (ext2/ext3/ext4)**: sistema de arquivos predominante no Linux.  Suporta journaling (ext3/ext4), metadados detalhados, volumes grandes e diversas características.  Possui estruturas como Superblock, Group Descriptors e Inode Table.  Registra as datas de criação, modificação, acesso e exclusão, preserva slack de arquivos e nomes de arquivos apagados, similar ao NTFS【303309481301383†L86-L97】.  
- **APFS (Apple File System)**: sistema moderno dos dispositivos Apple, otimizado para SSDs.  Oferece snapshots, clonagem de arquivos, criptografia robusta e mecanismo *copy‑on‑write*.  As snapshots permitem ao investigador obter um *time‑lapse* do estado do sistema; porém, uma vez que blocos são reutilizados, a recuperação torna‑se difícil【303309481301383†L99-L111】.  O APFS cria desafios específicos e exige ferramentas especializadas.

## 1.14 Endereços IP

O endereço IP identifica um dispositivo em uma rede e fornece pistas sobre a origem ou destino de comunicações.  O IPv4 utiliza 32 bits, permitindo cerca de 4 bilhões de endereços, enquanto o IPv6 utiliza 128 bits e oferece um espaço gigantesco.  Para fins forenses:

- **Logs de servidores, roteadores e firewalls** armazenam endereços IP de origem e destino; combinar esses registros com horários precisos permite rastrear ações de usuários.  
- **NAT (Network Address Translation)** pode mascarar endereços internos, exigindo que investigadores obtenham logs do gateway para relacionar endereços privados a endereços públicos.  
- **IP público vs. IP privado**: IPs privados não são roteáveis na internet; portanto, logs fornecem apenas a identificação local.  
- **Geolocalização e provedores**: serviços de whois e geolocalização inferem a região e o provedor associado a um IP; tais informações ajudam na identificação inicial, mas não substituem ordens judiciais ou solicitações formais aos provedores.

Compreender endereços IP e a estrutura de pacotes (TCP/UDP, portas de origem e destino, timestamps) é fundamental para a análise de logs e reconstrução de sessões em investigações de rede.