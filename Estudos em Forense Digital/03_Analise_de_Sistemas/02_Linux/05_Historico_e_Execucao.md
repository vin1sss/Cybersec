---
title: Histórico e evidência de execução em Linux
tags:
  - forense
  - linux
  - execucao
  - bash-history
  - sudo
  - artefatos
status: concluido
---

# Histórico e evidência de execução em Linux

Linux não tem Prefetch nem o mesmo volume de artefatos de execução que o Windows, então para provar que "alguém rodou tal comando" a gente precisa combinar várias fontes: histórico de shell, logs de autenticação/sudo, arquivos de editor e até registro de instalação de pacotes. Esta nota junta esses pontos para você conseguir reconstruir "o que foi feito" na máquina.

---

## 1. Objetivo

- Identificar onde ficam os comandos digitados pelos usuários
- Diferenciar comandos rodados com e sem sudo
- Recuperar arquivos que o usuário abriu/editar
- Usar logs de pacote para inferir execução
- Montar uma linha do tempo de ações

---

## 2. Histórico de shell do usuário

O artefato mais direto:

- `~/.bash_history`
  - lista comandos digitados naquele shell
  - é por usuário, então coletar de todos os homes
  - pode mostrar download, unzip, execução de malware, limpeza de arquivos
  - pode estar ausente ou truncado se o atacante limpou

Outros shells:

- `~/.zsh_history`
- `~/.local/share/fish/fish_history`

Sempre olhar também:

- `/root/.bash_history`

porque muitas vezes o atacante virou root e continuou o trabalho ali.

Exemplo de coleta:

```bash
cp /mnt/img/home/*/.bash_history ~/caso01/historicos/ 2>/dev/null
cp /mnt/img/root/.bash_history ~/caso01/historicos/root_bash_history 2>/dev/null
````

---

## 3. Histórico vs diretório atual

Muitos comandos em Linux são mais úteis quando você sabe de onde eles foram executados. É aí que entra o `auth.log` para comandos com sudo.

Num log de sudo típico você vê algo assim:

* usuário
* TTY
* PWD (current working directory)
* comando completo

Exemplo realista:

```text
sudo:  ubuntu : TTY=pts/0 ; PWD=/home/ubuntu ; USER=root ; COMMAND=/usr/bin/cat /etc/sudoers
```

Isso diz:

* quem rodou: `ubuntu`
* de onde: `/home/ubuntu`
* como: com sudo
* o que: `cat /etc/sudoers`

Ou seja, mesmo que o atacante apague o `.bash_history`, se ele usou sudo o comando ainda aparece no `auth.log`.

---

## 4. Comandos executados com sudo

Fonte principal:

* `/var/log/auth.log` (ou `/var/log/secure`)

Filtro simples:

```bash
grep -i "COMMAND=" /var/log/auth.log*
grep -i "sudo:" /var/log/auth.log*
```

Com isso você lista só o que foi rodado com privilégio elevado. É ótimo para ver:

* leitura de arquivos sensíveis
* leitura de logs binários (`/var/log/wtmp`, `/var/log/btmp`)
* execução de ferramentas de análise
* instalação de pacote com `apt-get` ou `apt install`

---

## 5. Evidência de execução por editor (vim)

Quando o usuário abre algo no `vim`, fica rastro em:

* `~/.viminfo`

O arquivo guarda:

* arquivo que foi aberto
* caminho
* às vezes posição dentro do arquivo

Por que isso é útil:

* mostra que o usuário abriu um arquivo dentro de `/tmp` ou `/var/tmp`
* mostra que ele abriu um arquivo baixado ou extraído
* mostra que ele estava olhando um arquivo que depois sumiu

Comando para inspecionar:

```bash
cat /home/<usuario>/.viminfo
```

Se houver linha com algo como `~/Downloads/malware` ou `~/Downloads/str`, já dá para amarrar com o resto.

---

## 6. Histórico de instalação de pacotes

Instalar pacote também é uma forma de execução indireta: alguém rodou um comando de instalação.

Olhar:

* `/var/log/dpkg.log`
* `/var/log/apt/history.log`

Buscar:

```bash
grep " install " /var/log/dpkg.log
```

Isso mostra:

* data e hora
* nome do pacote
* versão

Se o incidente foi em 2025-11-06 e você vê pacote "estranho" instalado nessa mesma data, é forte indicativo de ação manual.

---

## 7. Pastas temporárias como ponto de execução

Em muitos cenários o atacante baixa e roda a partir de:

* `/tmp/`
* `/var/tmp/`

Então ao montar timeline, incluir:

* listar arquivos recentes nesses diretórios
* verificar se algum processo apontava para lá (em sistema vivo: `ps aux`, `lsof`, `ss -natp`)
* verificar se cron ou systemd estavam chamando arquivos de lá (ver nota de persistência)

Mesmo com tudo já desligado, o nome do arquivo em `/tmp` e o horário de modificação pode ser suficiente para encaixar na linha do tempo.

---

## 8. Reconstrução básica de timeline

Passos que funcionam bem:

1. Anotar horário do evento principal (ex.: acesso remoto, webshell, alerta)
2. Buscar no `auth.log` o que foi rodado com sudo nesse horário
3. Ver no `.bash_history` do usuário se tem os mesmos comandos (às vezes o comando aparece primeiro no history e depois ele roda com sudo)
4. Conferir se nesse mesmo dia teve instalação de pacote (`dpkg.log`)
5. Conferir se no syslog teve cron ou serviço iniciando coisa diferente

Se mais de uma fonte aponta para o mesmo comando ou arquivo, você tem boa evidência de execução.

---

## 9. Sinais de limpeza de histórico

Atacante que sabe o que está fazendo pode tentar apagar ou reduzir histórico. Sintomas:

* `~/.bash_history` vazio ou com poucas linhas recentes
* timestamps dos arquivos de histórico muito mais novos que o resto
* gaps no `auth.log` (logs rotacionados antes da hora)
* histórico existe para um usuário mas não para outro que claramente logou

Mesmo assim, o `auth.log` costuma sobreviver porque é do sistema. Então sempre comparar o que há no `auth.log` com o que há no history do usuário.

---

## 10. Checklist de histórico e execução

1. Coletar `~/.bash_history` de todos os usuários e de `/root/`
2. Coletar `~/.viminfo` e outros arquivos de editor
3. Buscar `COMMAND=` e `sudo:` em `/var/log/auth.log*`
4. Buscar instalação de pacote em `/var/log/dpkg.log` e `/var/log/apt/history.log`
5. Listar arquivos recentes em `/tmp` e `/var/tmp`
6. Alinhar tudo por data/hora
7. Registrar comandos que chamam scripts em home, tmp ou var

---

## 11. Conclusão

Mesmo sem artefatos ricos como no Windows, Linux ainda entrega bastante sobre o que foi executado se você combinar histórico do usuário com `auth.log`, viminfo e logs de instalação. O segredo é não olhar só um lugar: quem rodou com sudo quase sempre se entrega no log do sistema, mesmo que tenha limpado o próprio history.