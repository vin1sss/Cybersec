---
title: Visão geral de análise em Linux
tags:
  - forense
  - linux
  - analise-de-sistemas
  - logs
  - persistencia
status: concluido
---

# Visão geral de análise em Linux

Em Linux, quase tudo que interessa para forense está em arquivo de texto sob `/etc`, `/var/log` ou dentro do `/home` dos usuários. Diferente do Windows, não há um "Registro" único, então o trabalho é saber qual arquivo guarda qual tipo de informação. Esta nota dá o mapa dos principais artefatos de sistema, usuários, logs e persistência que você deve olhar primeiro numa máquina Linux apreendida ou montada.

---

## 1. Objetivo

- Identificar versão e flavor do sistema
- Enumerar usuários e grupos
- Ver logins e autenticações
- Ver configuração de rede
- Mapear mecanismos de persistência mais comuns
- Preparar terreno para notas mais específicas (logs, execução, serviços)

---

## 2. Identificação do sistema

Principais arquivos/comandos que descrevem o sistema:

- `/etc/os-release`  
  - mostra nome da distro, versão, codinome
  - exemplo de campos: `NAME=`, `VERSION=`, `ID=`, `PRETTY_NAME=`

- `uname -a`  
  - mostra kernel, arquitetura, data de compilação

- `hostname` ou `cat /etc/hostname`  
  - nome atual da máquina

- `cat /etc/timezone`  
  - fuso horário configurado

Essas informações ajudam a ajustar o parsing de logs (datas) e a entender se é Debian/Ubuntu ou RedHat-like (muda um pouco o caminho de logs e serviços).

---

## 3. Usuários, grupos e privilégios

Em Linux, contas e grupos ficam expostos em arquivos texto.

- `/etc/passwd`
  - lista de contas
  - campos separados por `:` (usuário, x, uid, gid, descrição, home, shell)
  - usuários humanos costumam ter `uid >= 1000`

- `/etc/shadow`
  - senhas/hashees
  - exige root para leitura

- `/etc/group`
  - grupos e quais usuários pertencem a cada grupo

- `/etc/sudoers` e `/etc/sudoers.d/*`
  - define quem pode usar `sudo` e com quais comandos
  - só root lê/edita
  - se o usuário suspeito está aqui, ele pode ter escalado

Checklist rápido:
1. listar `/etc/passwd` e anotar contas com `uid >= 1000`
2. conferir se alguma conta "estranha" está em grupos sensíveis (`sudo`, `adm`)
3. conferir `/etc/sudoers` para ver se alguém ganhou sudo geral (`ALL=(ALL:ALL) ALL`)

---

## 4. Informações de login e autenticação

Linux guarda histórico de logins e tentativas:

- `/var/log/auth.log` (Debian/Ubuntu)
  - sudo usado
  - falha de autenticação
  - criação de usuário
  - sessões abertas/fechadas

- `/var/log/wtmp` e `/var/log/btmp`
  - binários, ler com `last` e `lastb`
  - `wtmp` → logins válidos
  - `btmp` → logins falhos

Exemplos úteis:
- `last -f /var/log/wtmp`
- `grep COMMAND /var/log/auth.log*` para ver comandos via sudo
- `tail /var/log/auth.log` para pegar ações recentes de um usuário específico

Isso permite responder "quem entrou", "quando", "de onde" e "o que rodou com sudo".

---

## 5. Configuração de rede e conexões

Para entender o contexto de ataque (exposto na internet? tinha um serviço aberto?):

- `/etc/hostname` e `/etc/hosts`
  - identidade local e resoluções estáticas

- `/etc/network/interfaces` ou netplan (dependendo da distro)
  - interfaces e DHCP

- `/etc/resolv.conf`
  - DNS em uso

- `ip address show`, `ip route`, `ss -natp`, `netstat -natp`
  - em sistema vivo, mostram conexões ativas e quem está escutando
  - bom para descobrir VNC local (`127.0.0.1:5901`), SSH (`0.0.0.0:22`), HTTP (`:80`)

Em forense de servidor, sempre anotar:
- portas que estavam escutando (pode ser backdoor)
- processos associados às portas
- se havia serviço só no loopback (acesso via túnel)

---

## 6. Logs do sistema

O centro dos logs em Linux é `/var/log/`. Principais:

- `/var/log/syslog` ou `/var/log/messages`
  - eventos gerais do sistema
  - execução de cron
  - atualização de pacotes
  - rotação de logs (syslog.1, syslog.2.gz...)

- `/var/log/auth.log`
  - já visto acima, focado em autenticação e sudo

- `/var/log/dpkg.log` e `/var/log/apt/history.log`
  - o que foi instalado, quando
  - bom para achar "pacote suspeito" instalado pouco antes do incidente

- diretórios de serviços
  - `/var/log/apache2/`
  - `/var/log/samba/`
  - `/var/log/openvpn/`
  - cada um pode mostrar o vetor de entrada (upload, RFI, brute force, etc.)

Em investigação de comprometimento, olhar se:
- houve instalação de pacote que não faz sentido para aquele servidor
- houve execução de cron incomum registrada no syslog
- houve criação de usuário no auth.log

---

## 7. Persistência em Linux

Linux dá várias formas simples de executar algo a cada boot ou login. Locais que devem ser inspecionados:

- `/etc/crontab`
  - cron global com usuário e comando
  - ataques simples colocam linha aqui

- `/etc/cron.d/`, `/etc/cron.daily/`, `/etc/cron.hourly/`
  - scripts que rodam sozinhos

- `/etc/init.d/` e `/etc/systemd/system/`
  - serviços e unidades do sistema
  - vale procurar nomes que não combinam com o resto ou que chamam `nc`, `bash`, scripts em `/home/...`

- `~/.bashrc`, `/etc/bash.bashrc`, `/etc/profile`
  - inicialização de shell
  - se tiver linha chamando script remoto ou em `/tmp`, é suspeito

- crontab por usuário
  - `crontab -l` (como o usuário suspeito)
  - muito usado pra `@reboot /home/.../script`

Regra prática:
- tudo que executa coisa fora de `/usr/bin`, `/bin`, `/usr/sbin` ou `/sbin` merece ser visto
- tudo que executa a partir de `/home/`, `/tmp/` ou `/var/tmp/` é ainda mais suspeito

---

## 8. Evidência de execução

Linux não tem Prefetch, então a gente olha:
- histórico de shell do usuário
  - `~/.bash_history`
  - lembrar de olhar do root também
- logs de sudo em `/var/log/auth.log`
  - mostram comando completo com caminho, diretório atual e usuário
- arquivos de editor como `~/.viminfo`
  - mostram que arquivo foi aberto
- arquivos temporários em `/tmp` e `/var/tmp`
  - muitos ataques baixam e executam daqui
- pacotes instalados em data próxima ao incidente
  - `grep " install " /var/log/dpkg.log`

Com isso dá para reconstituir a sequência:
1. usuário fez login
2. elevou com sudo
3. leu logs ou `/etc/sudoers`
4. instalou pacote ou baixou arquivo
5. criou cron ou serviço

---

## 9. Locais de usuário e servidores

Para máquinas que também rodam serviço web:

- `/home/<usuario>/` 
  - histórico de shell
  - chaves SSH (`.ssh/authorized_keys`)
  - scripts que o atacante deixou

- `/var/www/` ou onde o site estiver
  - uploads suspeitos
  - webshells
  - arquivos com permissão diferente do padrão

- `/var/log/apache2/access.log` e `error.log`
  - confirma exploração e IP de origem

---

## 10. Checklist rápido de análise em Linux

1. Identificar distro e versão (`/etc/os-release`, `uname -a`)
2. Listar usuários e grupos (`/etc/passwd`, `/etc/group`)
3. Ver se o usuário suspeito tem sudo (`/etc/sudoers`, grupos `sudo`/`adm`)
4. Ler autenticacões recentes (`/var/log/auth.log`, `last`, `lastb`)
5. Ver cron e systemd por persistência (`/etc/crontab`, `/etc/systemd/system/`)
6. Ver histórico de shell do(s) usuário(s) (`~/.bash_history`)
7. Ver instalações recentes de pacotes (`/var/log/dpkg.log`, `/var/log/apt/history.log`)
8. Ver serviços e portas abertas (em sistema vivo: `ss -natp`, `netstat -natp`)
9. Ver `/tmp` e `/var/tmp` atrás de executáveis e logs criados por binário estranho
10. Registrar tudo na nota do caso

---

## 11. Conclusão

Forense em Linux é menos "centralizado" que em Windows, mas mais previsível: quase tudo está em texto e quase tudo está em `/etc`, `/var/log` e `/home`. Se você passar por esses três e pelos mecanismos de persistência, já cobre a maioria dos vestígios de comprometimento em servidor e em workstation Linux. As próximas notas podem detalhar logs (`/var/log`), usuários e homes e persistência (cron/systemd/bash).
