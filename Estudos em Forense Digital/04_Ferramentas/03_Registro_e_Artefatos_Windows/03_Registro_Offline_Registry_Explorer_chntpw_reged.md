---
title: "Registro offline: Registry Explorer, chntpw e reged"
tags: [ferramenta, windows, registro, offline, registry-explorer, chntpw, reged]
status: rascunho
---

# Registro offline: Registry Explorer, chntpw e reged

Este documento apresenta ferramentas e métodos para abrir e analisar **Registro do Windows offline**, isto é, diretamente dos hives copiados de uma imagem/disco, sem inicializar o Windows original. O objetivo é dar alternativas gráficas e de linha de comando para navegar, pesquisar e - quando for o caso de laboratório - alterar chaves de Registro fora do sistema.

Ele complementa os documentos sobre:
- aquisição e montagem da imagem;
- análise de Registro com RegRipper;
- análise de Registro com bibliotecas em Python.

Aqui o foco é: "quero abrir o hive e ver as chaves com meus olhos".

---

## 1. Conceito

Quando coletamos uma evidência Windows (imagem de disco), os arquivos de Registro ficam guardados no disco, mas o Windows não está rodando. Para analisar esses hives, precisamos de ferramentas que:

1. entendam o formato de hive;
2. consigam abrir o arquivo direto do disco;
3. não precisem que o Windows monte o Registro.

As três opções mais comuns em ambiente de perícia/lab são:

- **Registry Explorer** (GUI, da Eric Zimmermann): excelente para navegação e comparação;
- **chntpw** (CLI, muito usado em Linux live): abre e edita Registro offline;
- **reged** (CLI): permite ver e modificar chaves em offline.

Para forense, usamos principalmente para **ler** (não alterar), mas é importante conhecer as duas coisas.

---

## 2. Principais hives de interesse

Relembrando os hives mais usados:

- `C:\Windows\System32\config\SYSTEM`
- `C:\Windows\System32\config\SOFTWARE`
- `C:\Windows\System32\config\SAM`
- `C:\Windows\System32\config\SECURITY`
- `C:\Users\<usuario>\NTUSER.DAT`
- `C:\Users\<usuario>\AppData\Local\Microsoft\Windows\USRCLASS.DAT`

Em imagem montada (Linux), isso vira algo como:

```text
/mnt/img/Windows/System32/config/SYSTEM
/mnt/img/Users/vinicius/NTUSER.DAT
````

O procedimento padrão é: copiar para uma pasta de trabalho e abrir com a ferramenta escolhida.

---

## 3. Registry Explorer (GUI)

O **Registry Explorer** é uma ferramenta gratuita muito usada em DFIR para:

* abrir hives offline;
* navegar em árvore (igual ao regedit);
* ver metadados de chave e de valor;
* fazer buscas por nome e por conteúdo;
* comparar hives;
* exportar chaves.

Características importantes:

1. roda em Windows;
2. não precisa carregar o Registro do sistema;
3. mostra timestamps de chave;
4. tem exibição pensada para forense (campos extras).

### Uso típico

1. abrir o Registry Explorer;
2. `File` - `Open Hive`;
3. escolher o hive copiado da imagem;
4. navegar até a chave de interesse (ex.: `Software\Microsoft\Windows\CurrentVersion\Run`);
5. exportar ou anotar o valor.

Vantagem: excelente para ensino, porque o aluno vê exatamente a chave que o RegRipper mostrou no relatório.

---

## 4. chntpw (Linux)

O **chntpw** ficou conhecido por resetar senhas locais do Windows, mas ele também consegue **abrir e editar hives**. Em ambiente forense, o que interessa é: ele lê hive offline em Linux, então podemos usá-lo em uma máquina que montou a imagem.

Exemplo de leitura de hive:

```bash
sudo chntpw -l /mnt/img/Windows/System32/config/SYSTEM
```

* `-l` lista o conteúdo básico do hive.

Para navegar em uma chave específica:

```bash
sudo chntpw -e /mnt/img/Windows/System32/config/SOFTWARE
```

Isso entra em modo "editor" (CLI) e permite ir descendo pelas chaves.

Importante: o chntpw permite editar. Em contexto forense real, **não editar** o hive original. Trabalhar sempre na cópia.

---

## 5. reged (CLI)

O **reged** (presente em alguns pacotes, ou como parte do chntpw/suíte) é outra forma de acessar Registro offline pelo terminal. É útil quando se quer:

* ler um valor específico;
* scriptar leitura de chaves;
* trabalhar direto no Linux sem interface.

Exemplo de uso genérico (a sintaxe pode variar por distribuição):

```bash
reged -e /mnt/img/Windows/System32/config/SOFTWARE
```

Ou, em algumas versões, comandos do tipo:

```bash
reged /mnt/img/Windows/System32/config/SOFTWARE 'Software\Microsoft\Windows\CurrentVersion'
```

O objetivo é o mesmo: acessar a chave sem precisar inicializar o Windows.

---

## 6. Quando usar cada uma

* **Registry Explorer** - quando você quer ver tudo de forma visual, explorar, mostrar em aula, comparar hives e ver timestamps.
* **chntpw** - quando você está em live Linux e quer abrir o hive rapidamente; quando quer examinar Registro sem GUI.
* **reged** - quando você quer scriptar ou quando só tem terminal.

Todas se encaixam bem depois da etapa de montar a imagem e copiar hives.

---

## 7. Limitações e cuidados

* trabalhar SEMPRE sobre a cópia do hive;
* registrar o caminho original na imagem (para apontar no relatório de onde veio o hive);
* se a ferramenta permitir edição, não salvar alterações na evidência;
* algumas versões do Windows têm hives grandes - abrir pode ser mais lento;
* em hives de usuário (`NTUSER.DAT`), lembrar que cada usuário tem o seu.

---

## 8. Blocos operacionais

```text
comando: abrir hive no Registry Explorer (Windows)

1. Abrir Registry Explorer
2. File -> Open Hive
3. Selecionar o arquivo NTUSER.DAT (cópia)
4. Navegar até Software\Microsoft\Windows\CurrentVersion\Run
5. Anotar valores de inicialização
```

```text
comando: listar informações básicas de hive no Linux com chntpw

sudo chntpw -l /mnt/img/Windows/System32/config/SOFTWARE
```

```text
comando: entrar em modo edição/navegação com chntpw

sudo chntpw -e /mnt/img/Users/vinicius/NTUSER.DAT
# usar os comandos internos para navegar e listar chaves
```

```text
comando: abrir hive com reged (exemplo)

reged -e /mnt/img/Windows/System32/config/SYSTEM
# navegar pela chave SYSTEM de forma offline
```

---

## 9. Comparação com outras abordagens

* **RegRipper** - extrai e interpreta automaticamente, mas você não "vê" a chave, só o resultado.
* **regipy/regrippy** - scriptável, ótimo para Linux e automação.
* **Registry Explorer/chntpw/reged** - ótimos quando você quer abrir, navegar e confirmar manualmente o que uma ferramenta automática disse.

O ideal em um laboratório de forense é o profissional ver os dois lados:

1. o relatório automático (RegRipper);
2. a mesma chave aberta manualmente (Registry Explorer).
