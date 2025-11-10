---
title: Sistemas de arquivos no macOS - APFS e HFS+
tags:
  - forense
  - macos
  - apfs
  - hfs
  - analise-de-sistemas
status: concluido
---

# Sistemas de arquivos no macOS - APFS e HFS+

O macOS moderno trabalha principalmente com APFS, mas ainda é comum encontrar discos e mídias formatadas em HFS+ (Mac OS Extended), especialmente em máquinas mais antigas ou em volumes externos. Para fins de forense, o importante é entender que o APFS introduziu o conceito de contêiner e múltiplos volumes lógicos, o que faz com que uma única partição aparente "conter" vários volumes. Isso impacta a aquisição e a montagem da imagem.

---

## 1. APFS - visão geral

O APFS (Apple File System) foi projetado para SSDs e para suportar recursos como snapshots, clonagem e criptografia. Em uma investigação isso significa:

- você pode ver vários volumes APFS dentro de um mesmo contêiner;
- o volume de sistema pode ser separado do volume de dados;
- snapshots podem guardar estados anteriores do sistema.

Termos importantes:

- **Container APFS** - estrutura que agrupa volumes.
- **Volume APFS** - onde ficam os dados propriamente ditos.
- **Preboot, Recovery, VM** - volumes auxiliares que podem aparecer.

Em ferramentas forenses, o APFS às vezes precisa de suporte específico para ser montado corretamente, principalmente quando há criptografia.

---

## 2. Estrutura típica de instalação recente

Uma instalação recente de macOS costuma ter algo assim:

- Contêiner APFS
  - `Macintosh HD` (somente sistema, às vezes montado como read-only)
  - `Macintosh HD - Data` (onde ficam os dados do usuário)
  - `Preboot`
  - `Recovery`
  - `VM`

Para análise, o volume que interessa quase sempre é o de dados. O volume de sistema serve para confirmar versão do macOS, componentes instalados e integridade do sistema.

---

## 3. Criptografia (FileVault)

Se o FileVault estiver habilitado, o volume APFS estará criptografado. Para análise isso implica:

- você precisa da chave/senha/usuário autorizado para descriptografar;
- em alguns casos a aquisição deve ser feita com o volume ainda montado/decriptado;
- sem a chave, você pode ver a estrutura mas não os arquivos.

Registrar no laudo se o volume estava criptografado ou não.

---

## 4. Snapshots APFS

O APFS suporta snapshots, que são "fotografias" do estado do volume em determinado momento. Do ponto de vista forense:

- podem existir artefatos apagados que ainda estão presentes em um snapshot;
- podem ajudar a demonstrar que um arquivo existia em data anterior;
- exigem ferramenta que entenda APFS para listar e montar snapshots.

Quando estiver documentando, anotar: nome do snapshot, data/hora e se houve extração.

---

## 5. HFS+ (Mac OS Extended)

Antes do APFS o macOS usava HFS+. Você ainda pode encontrar:

- discos externos mais antigos;
- Macs não atualizados;
- partições de recuperação antigas.

Características do HFS+ relevantes:

- estrutura mais simples que APFS;
- sem conceito de contêiner;
- ferramentas forenses mais antigas costumam suportar bem.

Se estiver analisando mídia que veio de ambiente misto (ex.: aluno trouxe um .dmg antigo ou um HD externo), vale registrar no vault que aquele volume é HFS+ e não APFS.

---

## 6. Aquisição e montagem

Na prática de laboratório/CTF, você pode cair em três situações:

1. **Imagem já extraída** - você recebe um `.dmg`, `.E01` ou um dump bruto. Nesse caso precisa montar com ferramenta que entenda APFS.
2. **Disco físico de Mac** - aquisição no próprio macOS ou via live. Atenção ao FileVault.
3. **Partição isolada APFS** - precisa primeiro montar o contêiner para depois montar o volume.

Pontos de atenção:

- montar sempre em modo somente leitura;
- registrar o mapeamento: disco físico → contêiner → volumes;
- se a ferramenta não exibir o volume de dados, conferir se é APFS criptografado.

---

## 7. Locais importantes dentro do volume de dados

Depois de montado o volume que contém os dados, a navegação volta a ser a mesma descrita na nota anterior:

- `/Users/`
- `/Library/`
- `/private/var/log/`
- `/Applications/`

Ou seja, o sistema de arquivos (APFS ou HFS+) é a base, mas os artefatos de interesse continuam sendo aqueles diretórios de usuário e de sistema.

---

## 8. Limitações e cuidados

- APFS muda rápido entre versões de macOS, então nem toda ferramenta antiga lê todos os campos.
- Volumes criptografados podem ser listados mas não lidos.
- Snapshots aumentam o tamanho lógico da análise - documentar se você decidiu não extrair todos.
- Em ambiente de ensino, vale mostrar aos alunos o "porquê" de às vezes o macOS parecer ter "duas" partições do mesmo nome.

---

## 9. Checklist rápido

1. Identificar se o disco é APFS ou HFS+.
2. Se for APFS, listar contêiner e volumes.
3. Verificar se há criptografia/FileVault.
4. Procurar por snapshots e anotar as datas.
5. Montar o volume de dados em modo somente leitura.
6. Navegar até `/Users/` e demais pastas de interesse.
7. Registrar tudo no relatório/laudo.

---

## 10. Conclusão

Saber se está diante de APFS ou HFS+ é o primeiro passo para não "se perder" na imagem de macOS. O APFS adiciona contêiner, múltiplos volumes e snapshots, mas o destino final da análise continua sendo o mesmo: chegar aos diretórios de usuário e aos locais de persistência. Nas próximas notas da pasta macOS você pode detalhar logs unificados e artefatos específicos de aplicativos.