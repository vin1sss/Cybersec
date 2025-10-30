# The Digital Forensics Codex – Complementos

Este repositório complementa o **The Digital Forensics Codex** com temas que surgem das salas de estudo do TryHackMe e de pesquisas correlatas.  Os capítulos originais abordam fundamentos, aquisição e análise de evidências, forense em Windows, web e e‑mail, redes e relatórios.  Nesta extensão acrescentamos seções sobre logs, ferramentas especializadas, artefatos de macOS e Linux, práticas de análise de memória, forense a frio (cold boot), inteligência artificial aplicada à perícia e tópicos práticos diversos.  Todo o conteúdo é independente, didático e rico em referências.

## Estrutura de Diretórios

| Capítulo | Descrição |
| --- | --- |
| [8_ferramentas_e_complementos.md](8_ferramentas_e_complementos.md) | Explora ferramentas usadas em forense digital, incluindo **Volatility** para análise de memória, **Autopsy**, **KAPE**, **NetworkMiner** e utilitários para registro do Windows e artefatos de macOS. Também introduz técnicas de logs e outras utilidades. |
| [9_forense_macos.md](9_forense_macos.md) | Detalha o processo de forense em computadores Apple: história do macOS, sistemas de arquivos (HFS+, APFS), estrutura de diretórios, formatos de artefatos (plist e SQLite) e ferramentas open source como *mac_apt*. |
| [10_forense_linux.md](10_forense_linux.md) | Introduz a forense em sistemas Linux, destacando a análise de diretórios, arquivos e processos suspeitos, comandos essenciais (ls, ps, netstat, strings, last), artefatos de login e a montagem de imagens para servidores Linux com Logical Volume Manager (LVM). |
| [11_logs_fundamentals.md](11_logs_fundamentals.md) | Examina a importância dos logs, tipos (sistema, segurança, rede), uso de ferramentas para análise (como o Event Viewer do Windows) e técnicas para lidar com logs de servidores web. |
| [12_cold_system_ai_forensics.md](12_cold_system_ai_forensics.md) | Discute ataques de cold boot e a importância de capturar a RAM antes que dados desapareçam, além de apresentar aplicações de inteligência artificial na perícia digital. |
| [13_forensic_imaging_and_practices.md](13_forensic_imaging_and_practices.md) | Recapitula técnicas de imageamento forense (cópia bit a bit com preservação de integridade), explica o conceito e os métodos de *file carving* (cabeçalho‑rodapé, estrutura e conteúdo) e aborda análises de sistemas EXT e criação de linhas do tempo. Inclui diretrizes para exercícios práticos (recuperação de arquivos deletados, análise de memória com Volatility, timelines em Linux e Windows). |
| [14_windows_artifacts_and_memory.md](14_windows_artifacts_and_memory.md) | Documenta artefatos do Windows (prefetch, amcache, shimcache, shellbags, LNK, Jump Lists, UserAssist), técnicas de recuperação de arquivos e análise de memória com Volatility. Também explica estruturas internas do NTFS (MFT, $LogFile, USN Journal) e oferece métodos para correlacionar evidências. |
| [15_additional_topics.md](15_additional_topics.md) | Reúne tópicos avançados: comparação forense entre partições MBR e GPT, análise do sistema de arquivos Ext3/Ext4 (extents, timestamps em nanosegundos, journaling), metodologia de análise de aplicações (como Microsoft Teams), técnicas de análise ao vivo em Linux e orientações para estudos de caso e investigações sem supervisão. |

Consulte também os arquivos originais (1 a 7) para uma visão completa do processo forense digital.
