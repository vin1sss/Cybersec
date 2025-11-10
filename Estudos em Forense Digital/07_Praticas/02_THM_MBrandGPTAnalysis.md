---
title: "THM - MBR and GPT Analysis"
tags: [pratica, mbr, gpt, boot-process, forense, hex, uefi, cadeia-de-custodia]
status: rascunho
link: "https://tryhackme.com/room/mbrandgptanalysis"
---

# THM - MBR and GPT Analysis

Este documento registra o estudo da sala **MBR and GPT Analysis** do TryHackMe. A sala foi feita para mostrar como o processo de boot depende do setor 0 do disco, como o **MBR** e o **GPT** descrevem as partições e por que essa área vira alvo de malware (bootkits, ransomware que mexe no MBR, wipers etc.). Além disso, a sala tem duas partes práticas que simulam exatamente isso: um MBR adulterado e, depois, um caso de bootkit em ambiente UEFI/GPT.

- **Link para a sala:** [TryHackMe | MBR and GPT Analysis](https://tryhackme.com/room/mbrandgptanalysis)

A proposta deste material é registrar:
- o que cada tarefa da sala ensina;
- a resposta de cada pergunta;
- por que aquela resposta faz sentido dentro do próprio enredo da sala;
- e comentários de forense digital para aprofundamento.

---

## 1. Visão geral do caso

A sala parte de uma analogia didática: o disco é um **prédio** e cada **andar/sala** é uma partição. Mas prédio sem planta vira bagunça: o sistema precisa de uma **tabela de partições** para saber onde cada partição começa, qual delas é a de boot e qual espaço está livre. Quem fornece essa “planta” logo no primeiro setor do disco são os dois esquemas clássicos:

- **MBR (Master Boot Record)** – formato mais antigo, criado para BIOS, muito simples: cabe em 512 bytes, suporta 4 partições, chega até 2 TB.
- **GPT (GUID Partition Table)** – formato moderno, pensado para UEFI, muito mais descritivo, com redundância e suporte a 128 partições.

Essa área do disco é lida antes do sistema operacional, portanto tem grande valor forense e é um alvo comum de ataque.

> **Nota ampliada:** em perícia de incidentes envolvendo “máquina não inicializa”, “aparece tela diferente após o POST” ou “o disco passou a ser exibido como não alocado”, o primeiro lugar a ser verificado costuma ser justamente esse início do disco. Isso vale tanto para problemas acidentais (setor ruim, atualização de firmware interrompida) quanto para problemas intencionais (malware que sobrescreve o boot). Quanto mais simples o esquema de particionamento, mais óbvio fica o dano.

---

## 2. Tarefas e soluções

### Task 1 – Introduction

A sala apresenta o conceito de partição e lembra que há malware que atua especificamente no processo de boot. Também deixa claro que MBR/GPT ficam no início do disco, o que simplifica tanto o exame quanto o ataque.

**Soluções:**

1. **What are the separate sections on a disk known as?**  
   **Resposta:** `Partitions`

2. **Which type of malware infects the boot process?**  
   **Resposta:** `Bootkit`

**Ponto didático:** partições organizam o disco; bootkits atacam a fase mais sensível do carregamento.

**Complemento:**  
- Em sistemas multi-boot (ex.: um mesmo disco com Windows e Linux), a presença de partições bem descritas é ainda mais importante, porque o bootloader precisa saber qual partição carregar.  
- Bootkits tiram proveito justamente de estar “antes” do sistema operacional: conseguem instalar rootkits, esconder arquivos e até alterar a forma como o próprio OS enxerga o disco.  
- Esse tipo de malware costuma ser mais persistente que malware de usuário, porque não depende de login, serviços do sistema ou agendamentos.

---

### Task 2 – Boot Process

A sala descreve a sequência de inicialização: energia → firmware (BIOS/UEFI) → POST → localizar dispositivo de boot → ler o primeiro setor (onde está MBR ou GPT). Também estabelece o mapeamento clássico:

- BIOS costuma trabalhar com MBR;
- UEFI costuma trabalhar com GPT.

**Soluções:**

1. **What is the name of the hardware diagnostic check performed during the boot process?**  
   **Resposta:** `Power-On-Self-Test`

2. **Which firmware supports a GPT partitioning scheme?**  
   **Resposta:** `UEFI`

3. **Which device has the operating system to boot the system?**  
   **Resposta:** `Bootable device`

**Ponto didático:** MBR/GPT não são o começo absoluto; eles entram logo depois que o firmware achou o disco inicializável.

**Complemento:**  
- O **POST** serve também para registrar erros em hardware; em ambientes forenses, o relato “a máquina apitou várias vezes e não iniciou” ajuda a separar o que é falha física do que é corrupção lógica do disco.  
- **BIOS/Legacy** enxerga apenas MBR porque foi criado na época em que discos eram menores — por isso aparece tanto a limitação de 2 TB.  
- **UEFI** trouxe o **Secure Boot**, um mecanismo que impede execução de bootloaders não assinados. Quando esse recurso está desativado (ou mal configurado), abre-se a porta para o cenário que a sala mostra na Task 8.

---

### Task 3 – What if MBR?

A sala disseca o MBR em 3 partes:

1. **Bootloader code** (bytes 0–445): código pequeno que verifica a tabela de partições e descobre qual deve iniciar.
2. **Partition table** (bytes 446–509): até 4 entradas de 16 bytes com boot flag, tipo de partição, LBA inicial e número de setores.
3. **Assinatura** (bytes 510–511): `55 AA`, que marca o fim do MBR válido.

Esse formato simples facilita o exame em editor hexadecimal.

**Soluções:**

1. **Which component of the MBR contains the details of all the partitions present on the disk?**  
   **Resposta:** `Partition table`

2. **What is the standard sector size of a disk in bytes?**  
   **Resposta:** `512`

3. **Which component of the MBR is responsible for finding the bootable partition?**  
   **Resposta:** `Bootloader code`

4. **What is the magic number inside the MBR?**  
   **Resposta:** `55 AA`

5. **What is the maximum number of partitions MBR can support?**  
   **Resposta:** `4`

6. **What is the size of the second partition in the MBR attached to the machine? (rounded to the nearest GB)**  
   **Resposta:** `16`

**Pontos de exame:**

- valores de LBA em MBR vêm em little-endian e precisam ser invertidos;
- o setor padrão é 512 bytes, então o cálculo é sempre LBA × 512;
- se a assinatura `55 AA` estiver ausente, o boot pode falhar.

**Complemento:**  
- Cada entrada da tabela de partições MBR segue o mesmo layout:  
  - **Byte 0** – boot indicator (0x80 = ativa, 0x00 = não ativa);  
  - **Bytes 1–3** – endereço CHS inicial (mais histórico que prático hoje);  
  - **Byte 4** – tipo de partição (ex.: 0x07 = NTFS/HPFS, 0x0B/0x0C = FAT32, 0xEE = protective MBR de GPT);  
  - **Bytes 5–7** – endereço CHS final;  
  - **Bytes 8–11** – LBA inicial (a parte realmente útil hoje);  
  - **Bytes 12–15** – número de setores da partição.  
- Saber o tipo de partição pelo byte 4 ajuda a achar disfarces (ex.: partição que diz ser FAT mas tem sistema de arquivos NTFS, problema comum quando alguém renomeia ou monta algo manualmente).  
- Um MBR pode indicar que a partição inicial está em LBA 2048: isso é comum porque muitos sistemas modernos alinham as partições a 1 MB por performance (2048 × 512 = 1.048.576 bytes).  
- Em análise de incidentes, uma mudança sutil de LBA (ex.: 00 08 00 00 → 00 00 00 00) já é suficiente para que o sistema pare de enxergar a partição, mesmo que o conteúdo dela ainda esteja intacto.

---

### Task 4 – Threats Targeting MBR

A sala mostra por que o MBR é alvo: basta alterar 512 bytes para controlar o início do sistema. São citados:

- **bootkits** – rodam antes do SO;
- **ransomware de MBR** – troca o MBR e exibe nota;
- **wipers** – corrompem o MBR para tornar o sistema inoperante.

**Solução:** tarefa sem resposta.

**Ponto didático:** quando o problema relatado é “a máquina não inicia” ou “inicia com mensagem estranha logo no começo”, o MBR é área de interesse imediato.

**Complemento:**  
- O ataque ao MBR costuma ser menos visível para antivírus porque o arquivo malicioso não está numa pasta comum do sistema, e sim numa área de disco que normalmente não é verificada.  
- Ataques como o **Petya** ganharam fama justamente por atacarem o boot em vez de cifrar arquivo por arquivo; isso reduz tempo e elimina o efeito de “parcialmente recuperado”.  
- Em ambientes corporativos, a simples presença de um MBR diferente do padrão da imagem corporativa já é um IOC (indicator of compromise) interessante.  
- Ferramentas de IR (resposta a incidentes) às vezes trazem função de “MBR backup/restore” por exatamente esse motivo: recuperar o setor 0 rapidamente.

---

### Task 5 – MBR Tampering Case

Cenário: servidor importante, e-mail malicioso, reboot, sistema não inicia. Exame do MBR mostra duas corrupções: endereço lógico da primeira partição alterado e assinatura alterada. Ao corrigir esses bytes e salvar, a imagem volta a ser reconhecida pelo analisador.

**Soluções:**

1. **How many partitions are on the disk?**  
   **Resposta:** `1`

2. **What is the first byte at the starting LBA of the partition? (represented by two hexadecimal digits)**  
   **Resposta:** `EB`

3. **What is the type of the partition?**  
   **Resposta:** `NTFS`

4. **What is the size of the partition? (rounded to the nearest GB)**  
   **Resposta:** `32`

5. **What is the flag hidden in the Administrator's Documents folder?**  
   **Resposta:** `THM{Cure_The_MBR}`

**Pontos de exame:**

- duas alterações mínimas no MBR são suficientes para tornar o disco “unrecognized file system”;
- corrigir o LBA da partição devolve o acesso ao sistema de arquivos;
- a assinatura final (`55 AA`) precisa estar correta para que o MBR seja aceito;
- validar a correção em uma ferramenta de análise garante que o filesystem voltou a ser visto.

**Complemento:**  
- O primeiro byte `EB` que aparece no setor de boot da partição é típico de boot sector x86 (instrução de salto curto). É um bom indicador visual de que se chegou ao lugar certo.  
- Em um caso real, antes de salvar a alteração, é comum fazer um dump do setor 0 inteiro (512 bytes) para manter evidência do estado corrompido.  
- A descrição da sala fala em “abrir no FTK depois de corrigir”: isso imita a etapa de **validação** da imagem — não basta consertar o hex, é preciso provar que o sistema de arquivos reaparece.  
- Esse tipo de exercício mostra bem a diferença entre “corrompido” e “perdido”: o conteúdo da partição não foi apagado, só ficou inacessível por causa de ponteiros errados.

---

### Task 6 – What if GPT?

Nesta parte a sala mostra o equivalente moderno do MBR. O GPT organiza-se assim:

1. **Protective MBR** (setor 0) – apenas sinaliza que o disco é GPT.
2. **Primary GPT Header** (setor 1) – descreve o disco, a área útil, o local da lista de partições e o GUID do disco.
3. **Partition Entry Array** (a partir do setor 2) – até 128 entradas de 128 bytes, cada uma com GUID de tipo, GUID único, LBA inicial e final, atributos e nome.
4. **Cópias de segurança** – backup do array e do header no fim do disco.

Esse formato permite mais partições, discos bem maiores e recuperação se o header principal for danificado.

**Soluções:**

1. **How many partitions are supported by the GPT?**  
   **Resposta:** `128`

2. **What is the partition type GUID of the 2nd partition given in the attached GPT file?**  
   **Resposta:** `E3C9E316-0B5C-4DB8-817D-F92DF00215AE`

**Pontos de exame:**

- campos de LBA no GPT também usam little-endian, então o mesmo procedimento do MBR (reverter → converter → multiplicar por 512) continua válido;
- o GUID de tipo permite identificar rapidamente se a partição é do sistema EFI, de dados, de recuperação etc.;
- a presença de header e array de backup no fim do disco é um indicador de integridade a mais.

**Complemento:**  
- O campo **Signature** do GPT header é sempre `EFI PART` em ASCII (`45 46 49 20 50 41 52 54` em hex). Se isso não aparecer no setor 1, é sinal de que o header principal está ausente ou danificado.  
- O GPT também traz campos de **CRC32** tanto para o header quanto para o array de partições. Isso é excelente para detecção de alteração: se o CRC não bate, houve mudança.  
- Como cada partição tem nome em UTF-16, dá para recuperar o propósito da partição mesmo que o sistema operacional não monte automaticamente.  
- A existência de **backup GPT header** no final do disco é um dos maiores ganhos sobre o MBR: em casos de corrupção no início do disco (por exemplo, malware que sobrescreveu o começo), ainda há metadados válidos para tentar reconstrução.

---

### Task 7 – Threats Targeting GPT

Mesmo com redundância, o GPT pode ser alvo. A sala discute:

- substituição ou infecção de arquivos `.efi` na **EFI System Partition (ESP)**;
- tentativas de cifrar a ESP para impedir o boot;
- ataques que destroem tanto o header principal quanto o de backup.

**Solução:** tarefa sem resposta.

**Ponto didático:** em sistemas modernos o ataque pode não estar no setor 0, mas na ESP, que também é parte de interesse forense.

**Complemento:**  
- A ESP normalmente é uma partição pequena (100–500 MB), formatada em FAT32 e montada em locais como `\EFI\Microsoft\Boot\` ou similares. Justamente por ser FAT32 e pequena, é relativamente simples para um invasor escrever nela.  
- Em alguns casos, a simples remoção ou renomeação do arquivo de boot principal (`bootx64.efi`, `bootmgfw.efi`, `bootmgr.efi`) já é suficiente para impedir o carregamento.  
- Malware que atua sobre GPT pode tentar apagar **apenas** o array de partições: o disco passa a parecer “vazio” para o sistema operacional. A presença do array de backup permite recuperar essa descrição.

---

### Task 8 – UEFI Bootkit Case

Cenário: disco GPT, firmware UEFI, bootloader em arquivo (`bootmgr.efi`) e um bootkit que inseriu uma string no espaço não usado. O exame do arquivo em editor hexadecimal permite encontrar esse conteúdo.

**Soluções:**

1. **Which partition has the bootloader in it?**  
   **Resposta:** `EFI System Partition`

2. **What is the malicious string embedded in the bootloader?**  
   **Resposta:** `Hello, EFI Bootkit!`

**Ponto didático:** em ambientes UEFI o boot pode ser comprometido por alteração de arquivo, não apenas de setor; por isso a ESP também deve ser analisada.

**Complemento:**  
- Arquivos EFI são executáveis em formato PE/COFF para o ambiente UEFI; por isso, mesmo que estejam em FAT32, podem conter código complexo e, ao mesmo tempo, trechos de dados simples (como a string pedida na sala).  
- Espaços “não usados” dentro de um arquivo de boot são alvos comuns de esteganografia simples e de marcação de versões por malware.  
- Se o Secure Boot estivesse ativo e configurado com chaves válidas, a modificação do arquivo deveria quebrar a assinatura e impedir o boot — o que mostra a importância de manter o Secure Boot habilitado em ambientes sensíveis.

---

## 3. Relações internas do documento

A sala segue uma progressão lógica:

- Tasks 1 e 2: conceitos básicos de disco e de boot;
- Task 3: estrutura do MBR em nível de byte;
- Task 4: motivos para atacar o MBR;
- Task 5: exemplo prático de MBR corrompido e recuperação;
- Task 6: estrutura do GPT e diferenças em relação ao MBR;
- Task 7: ameaças voltadas ao GPT/UEFI;
- Task 8: exemplo prático de bootkit em UEFI.

Essa progressão permite ver o mesmo problema (controle do boot) nos dois mundos (legado e moderno).

**Complemento:**  
- Esse tipo de sala é útil para conectar análise de disco com análise de memória: um bootkit que alterou o MBR/GPT pode também deixar artefatos em memória logo após o boot, e o conhecimento da etapa de inicialização ajuda a decidir o momento ideal de adquirir RAM.  
- Também é um bom ponto de ligação com timeline forense: alterações no setor inicial do disco costumam ter timestamp diferente das alterações de arquivos de usuário, o que ajuda a identificar o momento do comprometimento.

---

## 4. Referências ao repositório de estudos

- **02_Coleta_de_Evidencias/01_Imagem_de_Disco.md**  
  Conteúdo complementar sobre aquisição de disco/partição antes da análise.

- **04_Ferramentas/01_Aquisicao/01_FTK_Imager.md**  
  Detalhamento do uso de FTK Imager para abrir e validar imagens de disco.

- **05_Procedimentos/06_Documentacao_e_Cadeia_de_Custodia.md**  
  Procedimentos de registro, integridade e transporte de mídia apreendida.

- **06_Diversos/02_Cold_Forensics_e_Ordem_de_Volatilidade.md**  
  Considerações sobre trabalhar em cópias e preservar o original.

- **07_Praticas/**  
  Conjunto de demais exercícios práticos no mesmo padrão.
