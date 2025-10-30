# 10 Perícia forense em sistemas Linux

Sistemas Linux dominam os servidores web e embarcados, e a perícia digital frequentemente precisa lidar com incidentes envolvendo essas plataformas.  Segundo a Forensic Focus, cerca de **96,3 %** dos web servers utilizam Linux【400025389998378†L80-L95】, o que torna essencial dominar técnicas de aquisição e análise para esse ecossistema.  Este capítulo apresenta princípios, comandos e procedimentos para investigar sistemas Linux em execução e imagens de disco.

## 10.1 Princípios gerais e o “1000:1 rule”

O blog *THE DFIR Blog* ressalta a assimetria na defesa de sistemas Linux: enquanto defensores precisam monitorar milhares de vetores de ataque, invasores necessitam de apenas uma brecha para comprometer o sistema【68018425506088†L21-L33】.  Uma vez comprometido, no entanto, o perito precisa encontrar apenas um vestígio para reconstruir o ataque.  Essa regra destaca a importância de examinar sistematicamente diretórios, arquivos e processos com ferramentas simples antes de recorrer a soluções complexas.

## 10.2 Identificando diretórios, arquivos e processos suspeitos

Durante a análise de um sistema ativo ou de uma imagem montada, concentre‑se em três áreas principais【68018425506088†L47-L79】:

1. **Diretórios suspeitos:** procurar pastas que tentam imitar nomes legítimos, uso de caracteres incomuns ou permissões anormais.  Diretórios ocultos (começando com ponto) e localizados fora das convenções padrão também são sinais de alerta.
2. **Arquivos suspeitos:** logs apagados ou truncados, arquivos com extensões que não correspondem ao conteúdo real e arquivos de sistema em locais incomuns sugerem adulteração.  Ferramentas como `file` e `strings` ajudam a verificar a integridade.
3. **Processos suspeitos:** processos que usam nomes semelhantes aos do sistema, processos cuja execução persiste mesmo após a exclusão do executável e aqueles que abrem portas inesperadas devem ser investigados.  Comandos como `ps aux` e `netstat -tuln` permitem listar processos e conexões ativas【68018425506088†L80-L97】.

### Comandos essenciais

O uso de ferramentas nativas é suficiente para grande parte da triagem inicial【68018425506088†L80-L100】:

- `ls -la /caminho` – lista arquivos e permissões, ajudando a identificar itens ocultos ou com atributos incomuns;
- `ps aux` ou `ps -ef` – exibe processos em execução, permitindo localizar nomes disfarçados e processos zumbis;
- `netstat -tuln` – mostra portas TCP/UDP escutando e conexões estabelecidas, útil para detectar atividades clandestinas;
- `strings arquivo` – extrai texto legível de binários, revelando indicadores de malware ou scripts embutidos;
- `last` ou `last -f` – analisa registros de login (`wtmp`, `btmp`, `lastlog`) para identificar acessos legítimos e tentativas de intrusão【400025389998378†L200-L227】.

## 10.3 Hierarquia do sistema de arquivos Linux

A compreensão da hierarquia de diretórios é fundamental.  O blog destaca uma mnemônica para lembrar os diretórios de topo: *“Binny’s boot doesn’t even have leather material; might sell used version”*, correspondendo a `/bin`, `/boot`, `/dev`, `/etc`, `/home`, `/lib`, `/mnt`, `/media`, `/sbin`, `/usr` e `/var`【68018425506088†L146-L170】.  Algumas pastas e arquivos de interesse incluem【68018425506088†L146-L170】:

- **/etc/passwd** – lista usuários e diretórios home;
- **/etc/shadow** – armazena hashes de senhas; o acesso requer privilégios de root;
- **/etc/profile** e scripts em `/etc/init.d` – executados no login ou na inicialização; podem ser adulterados para persistência;
- **/etc/network/interfaces** e **/etc/hosts** – contêm configuração de rede e mapeamento de nomes;
- **/var/log** – repositório central de logs; arquivos como `auth.log`, `wtmp` e `btmp` registram autenticações, logins e falhas【400025389998378†L200-L227】;
- **/home/[usuário]** – diretórios pessoais com documentos, históricos, downloads e configurações.

## 10.4 Análise de imagens de servidores Linux

Quando um servidor é apreendido, a análise é geralmente realizada em uma cópia forense da unidade (`.E01`, `.dd` etc.).  O estudo de caso publicado pela Forensic Focus demonstra etapas para montar e examinar um servidor Ubuntu comprometido【400025389998378†L100-L124】:

1. **Verificação da imagem:** utilizar `ewfverify` (biblioteca *libewf*) para confirmar a integridade de uma imagem .E01 antes da análise.
2. **Identificação de volumes:** usar `mmls` (do *Sleuth Kit*) para listar partições e volumes; é comum encontrar volumes gerenciados por LVM (Logical Volume Manager)【400025389998378†L116-L124】.
3. **Montagem de arquivos EWF:** `ewfmount` gera um dispositivo raw (`ewf1`), permitindo acesso ao conteúdo da imagem【400025389998378†L130-L133】.
4. **Mapeamento de volumes LVM:** ferramentas como `kpartx -a` criam mapeamentos de volumes lógicos para que possam ser montados【400025389998378†L138-L148】.  Depois, `lvs`, `lvdisplay`, `pvs` e `vgdisplay` mostram informações sobre volumes lógicos, físicos e grupos de volumes【400025389998378†L154-L164】.
5. **Montagem do volume raiz:** ao montar volumes ext4 usando `mount -o ro,noload`, evita‑se a reprodução do journal e danos à imagem.  Uma vez montado, os arquivos são acessados normalmente【400025389998378†L177-L188】.
6. **Análise de logs de autenticação:** arquivos `btmp` (tentativas falhadas), `wtmp` (logins bem‑sucedidos) e `auth.log` revelam ataques de força bruta, sessões de usuários e uso de `sudo`【400025389998378†L200-L227】.

Ao final da análise, as informações coletadas de diretórios, processos, registros de login e configuração de redes podem ser correlacionadas para reconstruir a linha do tempo do ataque e identificar vulnerabilidades exploradas.

## 10.5 Comandos essenciais para análises em tempo real

Além dos utilitários específicos de cada caso, existem comandos clássicos do Linux que auxiliam investigadores a coletar evidências sem alterar o estado do sistema.  O artigo do **The DFIR Blog** apresenta dez comandos úteis【621343320387133†L14-L93】, explicados a seguir:

| Comando | Função e motivo de uso |
| --- | --- |
| `dd if=/dev/sda of=/path/image.dd bs=4M` | O **dd** cria uma cópia bit‑a‑bit de um dispositivo de armazenamento.  Essa imagem pode ser analisada posteriormente sem risco de modificar o original【621343320387133†L21-L25】. |
| `grep -i "termo" arquivo` | O **grep** procura padrões em grandes volumes de texto.  É ideal para encontrar senhas, IPs ou strings específicas em logs ou arquivos de configuração【621343320387133†L27-L31】. |
| `find /dir -name "*.txt" -mtime -7` | **find** localiza arquivos conforme critérios (nome, data de modificação, permissões).  Ajuda a identificar documentos ou scripts recentes【621343320387133†L33-L39】. |
| `strings /binário | grep "indicador"` | **strings** extrai texto legível de executáveis ou dumps de memória.  Pode revelar URLs, comandos ou chaves ocultas【621343320387133†L41-L46】. |
| `netstat -tuln` | **netstat** mostra conexões de rede e portas abertas, permitindo descobrir serviços desconhecidos ou backdoors【621343320387133†L49-L53】. |
| `ps aux` | **ps** lista processos em execução e argumentos; usado para detectar programas maliciosos, processos disfarçados e relações de parent/child【621343320387133†L55-L60】. |
| `lsof -i :80` | **lsof** (list open files) revela quais processos abriram determinados arquivos ou portas, útil para relacionar conexões a processos suspeitos【621343320387133†L62-L67】. |
| `mount -o ro /dev/sdb1 /mnt/evidencia` | O **mount** monta sistemas de arquivos ou imagens de forma somente leitura.  Isso permite analisar conteúdo sem alterar timestamps ou journals【621343320387133†L69-L74】. |
| `md5sum /caminho/arquivo` | **md5sum** calcula o hash MD5 de arquivos, verificando integridade ou comparando com listas de malware conhecidos【621343320387133†L77-L82】. |
| `hexdump -C arquivo` | **hexdump** exibe o conteúdo de um arquivo em hexadecimal e ASCII.  Auxilia na análise de formatos desconhecidos ou dados brutos【621343320387133†L84-L89】. |

O domínio desses comandos, aliado às práticas descritas neste capítulo, fornece uma base sólida para análises em tempo real e respostas a incidentes em sistemas Linux.
