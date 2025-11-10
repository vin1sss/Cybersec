---
title: Usuários e pastas home em Linux
tags:
  - forense
  - linux
  - analise-de-sistemas
  - usuarios
  - ssh
  - bash-history
status: concluido
---

# Usuários e pastas home em Linux

Em Linux, quase tudo que o usuário faz deixa rastro dentro da própria pasta dele em `/home/<usuario>/`. Além disso, a própria existência do usuário, o grupo a que ele pertence e se ele pode usar `sudo` ficam descritos em arquivos de texto em `/etc`. Esta nota mostra onde ver quem é quem na máquina e o que olhar em cada home para achar evidências de acesso, comandos e backdoors.

---

## 1. Objetivo

- Enumerar todos os usuários válidos da máquina
- Ver se existe conta criada só para backdoor
- Mapear pastas home e o que há dentro
- Achar histórico de comandos e chaves SSH
- Revisar arquivos de inicialização usados para persistência

---

## 2. Onde ficam as contas

Arquivos principais:

- `/etc/passwd`
  - lista de contas do sistema
  - formato: `usuario:x:uid:gid:descricao:/home/usuario:/bin/bash`
  - contas de serviço costumam ter `uid < 1000`
  - contas humanas costumam ter `uid >= 1000`
- `/etc/shadow`
  - guarda hashes de senha
  - só root pode ler
- `/etc/group`
  - mostra grupos e quem está em cada grupo

Passo típico de análise:

```bash
cat /etc/passwd
cat /etc/group
````

Com isso você já vê se apareceu um usuário estranho tipo `root2`, `backup`, `support`, `badactor` ou qualquer nome que não deveria existir no servidor.

---

## 3. Homes de usuário

Padrão mais comum:

* `/home/<usuario>/`

  * arquivos pessoais
  * históricos de shell
  * chaves SSH
  * configs de ferramentas

Root costuma ter home em:

* `/root/`

Ao listar `/home/`, anotar:

* quais usuários realmente têm pasta
* quais pastas têm data recente
* se existe home de usuário que não aparece em laudo ou na lista oficial

Exemplo:

```bash
ls -l /home
ls -la /home/ubuntu
```

---

## 4. Arquivos de histórico

O jeito mais rápido de ver o que o usuário fez:

* `~/.bash_history`

  * comandos que o usuário rodou
  * pode mostrar download de malware, unzip, scripts, uso de `strings`, etc.
* `~/.zsh_history`

  * se a máquina usa zsh
* `~/.viminfo`

  * mostra arquivos que o usuário abriu no vim
  * bom para saber que arquivo ele estava inspecionando

Lembre sempre de olhar:

* o history do usuário comum
* e o history do root em `/root/.bash_history`

Porque muitas vezes o atacante faz:

1. entra como usuário comum
2. sobe para root com sudo
3. faz o trabalho sujo como root

---

## 5. SSH e authorized_keys

Backdoor clássico em Linux: colocar uma chave pública no `authorized_keys`.

Locais mais importantes:

* `~/.ssh/authorized_keys`
* `~/.ssh/id_rsa` e `~/.ssh/id_rsa.pub` (chaves do próprio usuário)
* permissões do diretório `.ssh`

O que conferir:

* se existe `authorized_keys` em usuário que não deveria ter acesso remoto
* se existe chave com comentário esquisito (ex.: `attacker@box`, `support@vm`)
* se criaram usuário novo e já deixaram a chave lá

Comando útil:

```bash
grep -R "ssh-rsa" /home/*/.ssh 2>/dev/null
grep -R "ssh-ed25519" /home/*/.ssh 2>/dev/null
```

Isso varre todos os homes atrás de chaves liberadas.

---

## 6. Arquivos de inicialização do usuário

Esses arquivos rodam ou podem rodar toda vez que o shell do usuário inicia. Atacante adora colocar comando aqui.

Principais:

* `~/.bashrc`
* `~/.profile`
* `~/.bash_profile` (se existir)
* arquivos em `/etc/profile` e `/etc/bash.bashrc` (válidos para todos)

O que procurar:

* linha que baixa script remoto
* linha que executa binário em `/tmp/` ou `/var/tmp/`
* alias estranhos que escondem comandos
* export de PATH apontando para diretório do atacante

Exemplo de checagem:

```bash
grep -nE "curl|wget|nc|bash|tmp" /home/<usuario>/.bashrc
```

Se aparecer, anotar como possível persistência por usuário.

---

## 7. Permissões e grupos

O fato de o usuário existir não significa que ele possa fazer tudo. Então é bom casar `/etc/passwd` com `/etc/group`:

* se o usuário está em `sudo`, ele pode virar root
* se o usuário está em `adm`, ele costuma conseguir ler logs
* se o usuário está em grupo customizado, pode ser parte da persistência

Exemplo:

```bash
id ubuntu
id aluno
```

Isso mostra os grupos daquele usuário.

---

## 8. Correlacionando com logs

Depois de achar o usuário e a home dele, volte nos logs:

* `/var/log/auth.log`

  * ver se o mesmo usuário usou sudo
  * ver quando ele acessou
  * ver se o comando de ler `/etc/sudoers` bate com o horário

* `/var/log/wtmp` com `last`

  * ver se o usuário logou via SSH naquele horário

Assim você consegue dizer: "usuário X, com home em Y, logou em tal horário e logo depois rodou esses comandos".

---

## 9. Checklist de usuários e homes

1. Listar contas em `/etc/passwd`
2. Marcar contas humanas (uid >= 1000)
3. Conferir se todas têm pasta em `/home`
4. Verificar `/root/` também
5. Coletar:

   * `.bash_history`
   * `.ssh/authorized_keys`
   * `.viminfo`
   * `.bashrc`
6. Procurar persistência e chaves estranhas
7. Cruzar horário do histórico com `/var/log/auth.log`

---

## 10. Conclusão

Usuários e pastas home são o ponto onde o atacante costuma deixar o rastro mais pessoal: histórico de comandos, chave SSH dele, script que ele baixou. Como tudo fica em arquivo texto, é fácil ver, mas tem que lembrar de olhar usuário por usuário, inclusive o root. Guardando essa nota no vault, você já tem o procedimento para qualquer máquina Linux que aparecer.