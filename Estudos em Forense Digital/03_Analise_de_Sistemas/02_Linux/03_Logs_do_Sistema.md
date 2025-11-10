---
title: Logs do sistema em Linux
tags:
  - forense
  - linux
  - logs
  - var-log
  - autenticacao
  - incidentes
status: concluido
---

# Logs do sistema em Linux

Em Linux, quase tudo que acontece passa por algum arquivo em `/var/log/`. É dali que a gente descobre quando o sistema iniciou, quem autenticou, se instalaram pacote estranho, se rodou cron e se o serviço web recebeu requisição suspeita. Esta nota organiza os principais logs que você deve olhar primeiro e explica o que cada um costuma registrar.

---

## 1. Objetivo

- Mapear os arquivos de log mais importantes em `/var/log/`
- Diferenciar logs de sistema, de autenticação e de pacote
- Explicar rotação de logs
- Mostrar como filtrar eventos de interesse (sudo, cron, instalação)
- Preparar análise de serviços específicos (ex.: Apache)

---

## 2. Estrutura geral de `/var/log/`

Diretório padrão:

- `/var/log/`
  - arquivos principais: `syslog`, `auth.log`, `kern.log`, `dpkg.log`
  - logs rotacionados: `syslog.1`, `syslog.2.gz`, `auth.log.1`, etc.
  - diretórios de serviços: `apache2/`, `samba/`, `journal/`, `apt/`

Pontos importantes:
- arquivos com `.gz` são logs antigos comprimidos
- para ver tudo de uma vez, dá para usar `cat /var/log/syslog* | ...`
- em algumas distros, o principal é `/var/log/messages` em vez de `syslog`

---

## 3. Syslog (atividade geral)

Arquivo típico:

- `/var/log/syslog`

O que costuma ter:
- inicialização de serviços
- execução de cron (`CRON[...]`)
- mensagens de sistema (`systemd[...]`)
- mensagens de atualização de pacotes
- avisos de serviços rodando ou falhando

Comandos úteis:

```bash
# ver começo do syslog atual
head /var/log/syslog

# ver só cron dentro do syslog
grep CRON /var/log/syslog*

# ver tudo de todos os syslogs (inclui rotacionados)
cat /var/log/syslog* | less
````

Por que é importante:

* confirma que o cron realmente rodou
* confirma o horário em que o sistema estava ativo
* ajuda a ver se um serviço que você suspeita foi iniciado sozinho

---

## 4. Auth.log (autenticação e sudo)

Arquivo típico:

* `/var/log/auth.log` (Debian/Ubuntu)
* `/var/log/secure` (RedHat-like)

O que costuma ter:

* logins de usuário
* logins de SSH
* falhas de autenticação
* comandos executados com `sudo` (inclusive o comando inteiro)
* criação de usuário e adição a grupos

Exemplos de uso:

```bash
# ver últimas linhas
tail /var/log/auth.log

# ver só comandos via sudo
grep -i "sudo:" /var/log/auth.log*

# ver acesso SSH
grep -i "sshd" /var/log/auth.log*
```

Por que é importante:

* mostra exatamente quem virou root e quando
* mostra tentativa de ler arquivos sensíveis (`sudo cat /etc/sudoers`)
* mostra criação de usuários que não existiam

---

## 5. Logs de login binários (wtmp, btmp)

Arquivos:

* `/var/log/wtmp` (logins válidos, histórico)
* `/var/log/btmp` (logins falhos)

Eles são binários, então usar:

```bash
sudo last -f /var/log/wtmp
sudo lastb -f /var/log/btmp
```

Isso dá:

* quem logou
* quando
* quanto tempo ficou logado
* de onde (IP)

Bom para montar linha do tempo de acesso.

---

## 6. Logs de pacotes e atualizações

Dois alvos fáceis de esquecer:

* `/var/log/dpkg.log`
* `/var/log/apt/history.log`

Eles dizem:

* que pacote foi instalado
* qual versão
* quando
* às vezes por qual usuário/como

Isso é ótimo para achar "instalaram um pacote que não faz sentido neste servidor".

Exemplos:

```bash
# procurar "install" no dpkg.log
grep " install " /var/log/dpkg.log

# ver histórico mais amigável
cat /var/log/apt/history.log
```

Se você vê um pacote com nome estranho instalado minutos antes do incidente, marque.

---

## 7. Logs de serviços

Dentro de `/var/log/` ficam subpastas de serviços comuns. Olhar sempre que a máquina é servidor.

Exemplos:

* `/var/log/apache2/`

  * `access.log` (quem acessou, de onde, qual caminho)
  * `error.log` (erros de script/PHP, às vezes mostram inclusão de arquivo remoto)
* `/var/log/samba/`
* `/var/log/openvpn/`
* `/var/log/mysql/` ou `mariadb/` dependendo do setup

O `access.log` do webserver costuma entregar:

* IP de origem do ataque
* caminho acessado
* user-agent (às vezes mostra ferramenta)
* horário exato

---

## 8. Rotação de logs

Linux não deixa o `syslog` crescer para sempre. Quando ele atinge certo tamanho ou antiguidade, ele é "rotacionado" e vira:

* `syslog` (atual)
* `syslog.1` (anterior)
* `syslog.2.gz` (mais antigo, comprimido)
* e assim por diante

Por isso, ao procurar algo de dias atrás, sempre usar coringa:

```bash
cat /var/log/auth.log* | grep -i ubuntu
cat /var/log/syslog* | grep CRON
```

Senão você pode concluir "não tem" só porque está no arquivo rotacionado.

---

## 9. Live vs imagem montada

* se você está na máquina viva:

  * dá para usar `journalctl` e comandos interativos (`ss`, `netstat`, `last`)
  * dá para ver conexões atuais e processos

* se você só tem a imagem:

  * navega no disco até `/var/log/`
  * copia os arquivos e abre localmente
  * para os `.gz`, descompacta com `zcat` ou `gunzip`

Exemplo em imagem montada:

```bash
cp /mnt/img/var/log/auth.log* ~/caso01/logs/
cp /mnt/img/var/log/syslog* ~/caso01/logs/
```

---

## 10. Checklist de logs em Linux

1. Copiar tudo de `/var/log/` (pelo menos `auth.log*`, `syslog*`, `dpkg.log`, `apt/`)
2. Procurar por:

   * `sudo:`
   * `CRON`
   * `session opened`
   * `useradd`/`adduser`
3. Ver wtmp/btmp com `last` e `lastb`
4. Ver se houve instalação de pacote perto do incidente
5. Ver se o serviço web registrou upload ou caminho suspeito
6. Registrar horários para bater com histórico de shell e cron

---

## 11. Conclusão

Os logs em Linux contam praticamente toda a história se eles não tiverem sido apagados: quem entrou, quem virou root, o que o sistema executou sozinho e que pacote foi instalado. O segredo é não olhar só o arquivo atual, mas todos os rotacionados, e cruzar sempre com o horário que você já tem de outros artefatos (home do usuário, cron, serviços).