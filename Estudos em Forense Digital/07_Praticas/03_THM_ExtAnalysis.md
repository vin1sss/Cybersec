---

title: "THM - EXT Analysis"
tags: [pratica, ext4, sistema-de-arquivos, linux-forense, inodes, timestamps, autopsy, recuperacao]
status: rascunho
link: "https://tryhackme.com/room/extanalysis"
---

# THM - EXT Analysis

Este documento descreve a sala **EXT Analysis** do TryHackMe. A sala foi montada para mostrar o que torna o **EXT4** interessante para forense: estruturas bem definidas (superbloco, grupos de bloco, inodes), vários carimbos de tempo, journaling e a possibilidade de recuperar arquivos que ainda não foram sobrescritos. A ideia é sair do “ls e pronto” e chegar ao nível de “sei olhar o inode, sei de onde vem o tamanho do bloco, sei identificar timestomping”.

* **Link para a sala:** [TryHackMe | EXT Analysis](https://tryhackme.com/room/extanalysis)

A proposta deste material é registrar:

* o que cada tarefa apresenta;
* as respostas pedidas pela sala;
* o contexto forense de cada resposta;
* e os pontos de ligação com outras notas do repositório.

---

## 1. Visão geral

EXT é a família de sistemas de arquivos típica de Linux. Do ponto de vista de quem investiga, ela tem três vantagens claras:

1. **Metadados ricos** – inode guarda dono, permissões, tamanho, *quatro* timestamps (em EXT4 até criação), ponteiros para dados e até checksums. Tudo isso pode ser consultado sem montar o sistema de arquivos, direto na imagem. ([docs.huihoo.com][1])
2. **Estrutura previsível** – superbloco em posição conhecida, grupos de blocos, tabelas de inode. Isso facilita tanto o “ver no hex” quanto o “passar ferramenta”.
3. **Ferramentas maduras** – `debugfs`, `dumpe2fs`, `extundelete`, além dos suítes gráficos (Autopsy) que já sabem ler EXT.

A sala faz o aluno percorrer exatamente esse caminho: ver a estrutura (task 2), olhar artefatos forenses e recuperação (task 3), entender timestamps e antianálise (task 4), usar ferramenta gráfica (task 5) e, por fim, resolver um mini-caso (task 6).

---

## 2. Tarefas e soluções

### Task 1 – Introduction

A tarefa é só de abertura: lembra que o foco é **EXT4**, que haverá VM e que a ideia é detectar criação/remoção/manipulação de arquivos.

* **Pergunta:** “Let’s get started!”
* **Resposta:** não há resposta.

**Ponto de conteúdo:** EXT4 é o padrão de muitas distros porque suporta volume grande, arquivo grande e journaling com integridade (checksums). Isso é o que justifica estudar essa família e não só FAT/NTFS.

---

### Task 2 – EXT File System Structure

A sala recapitula a evolução EXT → EXT2 → EXT3 → EXT4 e mostra que todos têm os mesmos elementos-base: **superblock**, **inode table**, **block groups**, **data blocks** e **bitmaps**. O que muda de uma versão para outra é capacidade, journaling e alguns campos extras. EXT4 continua com o mesmo superbloco descrito no código do kernel: o campo com o primeiro bloco de dados e o campo com a contagem de blocos aparecem logo no começo da estrutura. ([docs.huihoo.com][1])

**Soluções:**

1. **What is the member of the ext4_super_block struct that holds the offset value to the first data block?**
   **Resposta:** `s_first_data_block`
   **Por quê:** no superbloco, depois dos contadores de inode e bloco, vem exatamente o campo que diz “a partir daqui começam os dados”. É ele que permite ao driver saber de onde em diante podem existir diretórios e arquivos. ([docs.huihoo.com][1])

2. **What is the offset where we can find the member s_blocks_count_lo in the ext4_super_block struct? (decimal format)**
   **Resposta:** `4`
   **Por quê:** a estrutura começa em 0 com `s_inodes_count` (4 bytes). Imediatamente depois, no deslocamento 4 (decimal), aparece `s_blocks_count_lo`, que é a parte baixa da contagem de blocos do sistema de arquivos. Em algumas compilações há também `s_blocks_count_hi`, mas a parte “_lo” vem logo no início, no offset 4. ([docs.huihoo.com][1])

**O que esta tarefa ensina:**

* que o superbloco não é algo abstrato – ele está no disco, em offset conhecido, e pode ser lido com `dd | hexdump`;
* que o tamanho do bloco é derivado de `s_log_block_size` pela fórmula `2^(10 + valor)`, o que explica o 4096 mostrado na sala;
* que saber onde o superbloco fica permite validar se a imagem não está corrompida.

**Complemento didático:**

* Em discos maiores, o EXT4 faz cópias de superbloco ao longo dos grupos de bloco; em exame pericial isso é útil quando o início do disco está danificado.
* O fato de o valor do bloco vir em *little-endian* (02 00 00 00) e precisar ser interpretado é o mesmo padrão visto em MBR/GPT: quase tudo em disco Linux segue essa lógica.

---

### Task 3 – Forensic Artifacts in EXT

Aqui a sala entra no que interessa para perito: **inode → metadados → recuperação**. A lógica é sempre essa:

1. cada arquivo/diretório tem um inode;
2. o inode guarda quem é o dono, modo, links, blocos de dados e *quatro* tempos principais (`atime`, `mtime`, `ctime`, `crtime` em EXT4);
3. se o nome do arquivo some do diretório, o inode ainda pode existir;
4. se o bloco de dados ainda não foi sobrescrito, dá para extrair.

Além disso, EXT3/EXT4 usam **journal** – o que significa que operações recentes podem deixar rastro até mesmo quando o arquivo não está mais acessível de forma tradicional.

**Soluções:**

1. **What is the inode number for the file /etc/passwd in the VM?**
   **Resposta:** o número de inode exibido para `/etc/passwd` no ambiente da sala.
   **Observação:** o valor depende da imagem usada na VM; o procedimento mostrado na task (consultar metadados do arquivo no ponto montado ou via `debugfs` apontando para o dispositivo loop) revela esse número.

**O que esta tarefa ensina:**

* inode é o identificador real do arquivo; o nome é só uma entrada de diretório apontando para ele;
* o mesmo inode pode ter mais de um nome (hard link), então trabalhar “por nome” pode enganar;
* recuperar arquivo manualmente é “só” encontrar o bloco certo e despejar para outro lugar – exatamente o que a sala faz com `strings` para localizar a sequência e depois `dd` para salvar o bloco.

**Ampliação de conteúdo (conceitos-chave):**

* **Inode x diretório:** diretórios em EXT são, eles mesmos, arquivos que listam “nome → inode”. Quem apaga o nome, mas não o inode, causa aquele efeito de “arquivo ainda recuperável”.
* **Campo `i_dtime`:** no `ext4_inode` há um campo de “deletion time”; se ele estiver preenchido, é forte indicativo de remoção anterior. Isso é ótimo para timeline.
* **Extents:** EXT4 não fica apontando bloco por bloco se o arquivo é grande; ele aponta “do bloco X até o Y”. Esses *extents* aparecem no final do inode e podem ser vistos no `debugfs` como `(0):24577` etc. Saber ler isso ajuda a chegar no lugar certo dentro da imagem.
* **Recovery manual:** a técnica que a sala mostra (achar string → ver offset → dividir pelo tamanho do bloco → dar `dd` naquele bloco) é uma forma simplificada de *carving* direto na imagem: não depende do sistema de arquivos estar montando.

---

### Task 4 – Analyzing File System Timestamps

Agora a sala mostra o que quase sempre aparece em análise de intrusão: **timestomping**. A situação típica:

* `atime` e `mtime` estão muito antigos (ex.: 2016);
* mas `ctime` e `crtime` são bem recentes (2025);
* conclusão: alguém mexeu no relógio do arquivo, mas o sistema registrou a mudança de metadado agora.

EXT4 é bom nisso porque tem **criação** (`crtime`) gravada no inode – campo que sistemas mais antigos não tinham. E como `ctime` é “change” (mudança de metadado), é o primeiro lugar onde a fraude aparece: mudar permissão, dono ou mesmo o timestamp causa atualização do `ctime`. Discussões em forense Linux ressaltam exatamente isso: confiar só em `mtime`/`atime` não é suficiente, é preciso olhar o conjunto todo. ([Forensic Focus][2])

**Soluções:**

1. **What is the btime for the file /etc/passwd?**
   **Resposta:** o carimbo de criação (`btime`/`crtime`) que o ambiente da sala mostra para `/etc/passwd`, no formato completo com nanossegundos.

**O que esta tarefa ensina:**

* `mtime` pode ser forjado para “parecer antigo”;
* `ctime` denuncia a alteração;
* `crtime` diz quando o inode nasceu – ótimo para detectar arquivo novo disfarçado de velho;
* buscas por intervalo de **ctime** (e não de mtime) revelam arquivos que passaram por alteração de metadado no período do incidente.

**Ampliação de conteúdo:**

* **Timestomping realista:** muitas ferramentas de ofensiva só mexem em três campos (A/M/C) e deixam o de criação porque não existe em todos os sistemas de arquivos. Em EXT4 ele fica visível.
* **Correlação:** quando o tempo parece incoerente, o próximo passo é cruzar com `/var/log/` da mesma janela: se o sistema foi ajustado para outro horário, os logs vão mostrar.
* **Timezone:** o timestamp mostrado no inode é UTC; ao montar timeline para apresentação, vale sempre normalizar.

---

### Task 5 – Tools for EXT Forensics

A sala agora leva o mesmo exame para uma ferramenta gráfica (**Autopsy**), usando uma imagem EXT4 de exemplo. A ideia é mostrar que tudo aquilo que foi feito “na unha” (ver arquivo, ver timestamps, ver arquivo deletado) aparece automaticamente:

* painel de dados → mostra os arquivos;
* metadados → mostra inode, permissões e tempos;
* módulos de ingestão → mostram deletados, strings, timeline.

**Soluções:**

1. **Select Data Sources … What is the inode number of the file `normal_file.txt`?**
   **Resposta:** inode exibido pelo Autopsy na aba **File Metadata** para o arquivo `normal_file.txt` (valor do ambiente).

2. **What is the creation time of the file `timestomped.txt`? (Format: YYYY-MM-DD hh:mm:ss)**
   **Resposta:** data/hora de criação mostrada na mesma aba para o arquivo `timestomped.txt` (valor do ambiente).

**O que esta tarefa ensina:**

* que analisar EXT4 não precisa ser só CLI;
* que as mesmas evidências (inode, tempos, deletados) ficam visíveis para relatório;
* que uma imagem EXT4 pode ser tratada igual a outras imagens de disco em Autopsy.

**Ampliação de conteúdo:**

* **debugfs x Autopsy:** `debugfs` mostra exatamente o que está no disco; Autopsy organiza isso e permite exportar/relatar. Em ambiente pericial real, os dois costumam ser usados (um para confirmar, outro para documentar).
* **extundelete:** em alguns casos, quando o arquivo foi removido há pouco e o journal ainda está quente, dá para pedir recuperação direto com ferramenta de terminal – o raciocínio é o mesmo que a sala mostrou manualmente.
* **Timeline dentro do Autopsy:** como EXT4 tem muitos timestamps, as timelines ficam mais precisas que em sistemas que só têm A/M.

---

### Task 6 – Practical

A sala fecha com um mini-exercício: há um sistema EXT montado em `/mnt/ext_exercises`, com arquivos normais, um arquivo timestomped e um arquivo deletado cujo conteúdo começa com “FFFFFFFFFF…”. A tarefa é aplicar tudo o que foi visto:

* identificar qual arquivo tem datas “antigas” demais → esse é o timestomped;
* olhar o metadado real do arquivo → pegar a data de criação;
* procurar o arquivo deletado e ler o conteúdo → pegar a flag.

**Soluções:**

1. **Identify the timestomped file … What is the original creation date of the file?**
   **Resposta:** data/hora de criação exibida no ambiente da sala para o arquivo que apresenta timestamps incoerentes (formato `YYYY-MM-DD hh:mm:ss`).

2. **What is the flag in the deleted file that starts with the characters "FFFFFFFFFF" … ?**
   **Resposta:** flag no formato `THM{...}` cujo conteúdo começa com `FFFFFFFFFF`, exatamente como mostrado pelo arquivo recuperado no ambiente da sala.

**O que esta tarefa ensina:**

* que detectar timestomping em EXT4 é, na prática, comparar `mtime` (que pode ter sido forjado) com `crtime` e `ctime`;
* que arquivo “apagado” em EXT4 nem sempre está perdido – se o bloco está livre mas não foi reutilizado, dá para ler;
* que a técnica do `dd` por bloco que apareceu na task 3 é exatamente o que está por trás de ferramentas de recuperação.

---

### Task 7 – Conclusion

Tarefa só de encerramento.

* **Pergunta:** sem resposta.

---

## 3. Relações internas do documento

A sala está organizada para que cada tarefa prepare a seguinte:

* **Task 2** dá o mapa do EXT4 (superbloco, blocos, inodes).
* **Task 3** usa esse mapa para mostrar onde estão metadados e como recuperar.
* **Task 4** pega os metadados e usa para montar eventos e detectar antianálise.
* **Task 5** mostra a mesma coisa em ferramenta forense.
* **Task 6** pede que tudo seja aplicado em um caso simples.

Essa ordem é útil porque em discos Linux reais o perito normalmente faz exatamente isso: identifica o filesystem, confirma tamanho de bloco, inspeciona inode de interesse, valida timestamps, tenta recuperar e só então passa para relatório.

---

## 4. Referências ao repositório de estudos

Este caso pode ser mapeado diretamente para outras notas do repositório, mantendo coerência de termos e de fluxo:

* **01_Introducao/03_Sistemas_de_Arquivos.md**
  Base conceitual sobre o que é sistema de arquivos, diferença entre metadado e dado e por que o exame começa pelo superbloco/cabeçalho.

* **02_Coleta_de_Evidencias/01_Imagem_de_Disco.md**
  Para reforçar que o exame do EXT4 é feito **na imagem**, não na mídia original.

* **04_Ferramentas/04_Analise/01_Autopsy.md**
  Detalhamento da ferramenta usada na task 5, incluindo ingest, timeline e exportação.

* **04_Ferramentas/08_Utilitarios_de_Apoio/04_debugfs.md**
  Nota de apoio para leitura direta de inode, visualização de extents e inspeção de superbloco.