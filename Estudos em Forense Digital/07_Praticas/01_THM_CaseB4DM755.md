---

title: "THM - Case B4DM755"
tags: [pratica, estudo-de-caso, cadeia-de-custodia, ftk-imager, imagem-forense, artefatos, tribunal]
status: rascunho
link: "https://tryhackme.com/room/caseb4dm755"
---
# THM - Case B4DM755

Este documento registra o estudo da sala **Digital Forensics Case B4DM755** do TryHackMe. A sala foi construída para mostrar um ciclo forense completo usando apenas um artefato físico (um pendrive) e amarrar esse artefato a:

* autorização legal (mandado de busca);

* cadeia de custódia (quem pegou, quem transportou, quem analisou);

* aquisição forense correta (imagem + hash);

* análise em laboratório sobre a cópia;

* extração de metadados e artefatos ligados ao crime;

* preparo da prova para apresentação em tribunal.

* **Link para a sala:** [TryHackMe | Digital Forensics Case B4DM755](https://tryhackme.com/room/caseb4dm755)

A proposta deste material é registrar:

* o que cada tarefa da sala ensina;
* a resposta de cada pergunta;
* por que aquela resposta faz sentido dentro do enredo;
* e onde o caso se encaixa dentro de um estudo maior de forense digital.

---

## 1. Visão geral do caso

A narrativa gira em torno de **William S. McClean** (também chamado de *William Super McClean*), investigado por **espionagem corporativa** e **furto de segredos comerciais**. Um informante passa a localização e o momento de uma transação. A agência obtém **mandado de busca** no mesmo dia e envia uma equipe ao local.

Ao chegar, a equipe encontra sinais de que todos já haviam saído e que houve tentativa de “limpar” o ambiente. O que sobra de útil é um **pendrive** com chaveiro identificado (WSM). Mesmo sendo um único item, ele é tratado como **evidência completa**, com documentação, lacre e envio ao laboratório.

A sala também mostra uma situação comum em órgãos com pouco pessoal: **a mesma pessoa pode atuar como DFIR First Responder e depois analisar no laboratório**, desde que isso seja bem documentado. Isso reduz pontos de contato e, portanto, reduz a chance de contestação da cadeia de custódia.

---

## 2. Tarefas e soluções

### Task 1 – Introduction

Tarefa de abertura. Diz que o cenário é fictício e que a sala parte do que já foi visto em introdução à forense digital.

* **Pergunta:** “I'm ready to investigate the case.”
* **Resposta:** não há resposta.

**Idéia central da tarefa:** preparar o leitor para um caso completo, com foco em aquisição de mídia removível e análise de artefatos de usuário.

---

### Task 2 – Case B4DM755: Details of the Crime

Objetivo: garantir que ficaram claros o papel exercido, o papel assumido no cenário, o que deve ser coletado e qual documento autoriza a busca.

**Soluções:**

1. **What is your official role?**
   **Resposta:** `Forensics Lab Analyst`
   **Por quê?** o enredo descreve que o profissional atua no laboratório analisando artefatos que chegam do campo.

2. **What role was assigned to you for this specific scenario?**
   **Resposta:** `DFIR First Responder`
   **Por quê?** foi destacado para acompanhar a operação e cuidar da aquisição correta na hora da apreensão.

3. **What do you have to gather?**
   **Resposta:** `Digital artefacts and evidence`
   **Por quê?** a sala usa exatamente essa expressão para definir o escopo da coleta.

4. **What document is needed before performing any legal search?**
   **Resposta:** `Search warrant`
   **Por quê?** o mandado é o fundamento legal para entrar no imóvel e coletar o pendrive.

**O que a tarefa ensina:** sem papel, sem exame. E sem papel bem preenchido, cadeia de custódia fica frágil.

---

### Task 3 – Practical Application of the Digital Forensics Process

Aqui aparecem as boas práticas mínimas de coleta e de transporte. A sala descreve um fluxo bem conhecido:

1. checar se há criptografia;
2. produzir imagem (RAM e disco quando aplicável);
3. calcular hash e copiar;
4. embalar, selar e etiquetar;
5. registrar tudo na cadeia de custódia.

**Soluções:**

1. **Before imaging drives, what must we check them for?**
   **Resposta:** `Drive encryption`
   **Por quê?** se o dispositivo estiver cifrado, a estratégia muda; por isso a checagem vem antes da imagem.

2. **What should be done to ensure and maintain the integrity of original files in the Chain of Custody?**
   **Resposta:** `Hash and copy`
   **Por quê?** o hash é o elemento que permite provar que o que chegou ao laboratório é o mesmo que saiu da cena.

3. **What must be done before sending obtained artefacts to the Forensics Laboratory?**
   **Resposta:** `Bag, seal, and tag the obtained artefacts`
   **Por quê?** embalagem + lacre + etiqueta são o que tornam rastreável o transporte até o laboratório.

**Notas forenses que a tarefa introduz:**

* não desligar o equipamento de forma “graciosa” pode ser necessário para evitar rotinas de limpeza;
* o registro deve ser feito “antes, durante e depois” da entrega;
* o DFIR First Responder e o analista de laboratório conferem inventário na troca para garantir que nada sumiu.

---

### Task 4 – Case B4DM755: At the Scene of Crime

Continuação direta do enredo. Só há um artefato útil e ele é uma mídia removível — das mais fáceis de adulterar e das mais fáceis de transportar.

**Soluções:**

1. **What is the only possible artefact found in the suspect's residence?**
   **Resposta:** `Flash drive`

2. **Based on the scenario and the previous task, what should be done with that acquired suspect artefact?**
   **Resposta:** `Taking an image`
   **Por quê?** o fluxo descrito na task anterior aponta exatamente para criação de imagem forense.

3. **What is the crucial aspect of the Chain of Custody that ensures individual accountability and guarantees a transparent and untainted transfer of artefacts and evidence?**
   **Resposta:** `Ensure proper documentation`
   **Por quê?** a sala enfatiza o preenchimento correto dos formulários de cadeia de custódia como forma de garantir responsabilidade individual.

**Ponto importante:** mesmo uma única pendrive encontrada “debaixo da mesa” deve ser tratada como evidência plena — com identificação, imagem e transporte controlado.

---

### Task 5 – Introduction to FTK Imager

A sala apresenta a ferramenta principal do caso. O foco aqui é **reconhecer os elementos da interface** e **lembrar do uso de write-blocker**.

**Soluções:**

1. **What device will prevent tampering when acquiring a forensic disk image?**
   **Resposta:** `Write-blocking device`

2. **What is the UI element of FTK Imager which displays a hierarchical view of the added evidence sources?**
   **Resposta:** `Evidence Tree Pane`

3. **Is the attached flash drive encrypted? (Y/N)**
   **Resposta:** `N`

4. **What is the UI element of FTK Imager which displays a list of files and folders?**
   **Resposta:** `File List Pane`

**O que esta tarefa ensina tecnicamente:**

* FTK Imager serve para adquirir **e** visualizar;
* o pendrive da sala já está conectado de forma protegida, simulando o uso de bloqueador de escrita;
* a detecção de EFS é feita primeiro para saber se há cifragem de arquivos na mídia.

---

### Task 6 – Using FTK Imager to Acquire Digital Artefacts and Evidence

Aqui vem a parte objetiva: criar imagem, pedir verificação, montar a imagem e responder perguntas diretas sobre o conteúdo da mídia.

**Soluções:**

1. **What is the UI element of FTK Imager which displays the content of selected files?**
   **Resposta:** `Viewer Pane`

2. **What is the SHA1 hash of the physical drive and forensic image?**
   **Resposta:** `d82f393a67c6fc87a023b50c785a7247ab1ac395`
   **Por quê?** o FTK mostra o SHA1 do original e o SHA1 da imagem e declara que os dois coincidem.

3. **Including hidden files, how many files are currently stored on the flash drive?**
   **Resposta:** `8`

4. **How many files were deleted in total?**
   **Resposta:** `6`

5. **How many recovered files are corrupted (e.g., 0 file size)?**
   **Resposta:** `3`

**Aspectos de prova que aparecem aqui:**

* o hash é o ponto de ligação com o tribunal: é o que permite dizer “esta imagem representa fielmente o dispositivo apreendido”;
* a contagem de arquivos inclui ocultos e deletados, mostrando que o examinador precisa olhar além da lista padrão do sistema de arquivos;
* a própria ferramenta indica que alguns recuperados estão com tamanho 0, o que ajuda a explicar por que determinados arquivos não podem ser analisados.

---

### Task 7 – Case B4DM755: At the Forensics Laboratory

É a tarefa mais longa e mais “investigativa”. Mostra que, depois de adquirida a imagem, a análise deve ser feita sobre ela e que pode ser necessário usar outra ferramenta além do FTK — no caso, o **ExifTool** que está na área de trabalho.

**Soluções:**

1. **Aside from FTK Imager, what is the directory name of the other tool located in the tools directory under Desktop?**
   **Resposta:** `exiftool-12.47`

2. **What is the visible extension of the "hideout" file?**
   **Resposta:** `.pdf`

3. **View the metadata of the "hideout" file. What is its actual extension?**
   **Resposta:** `.jpg`

4. **A phone was used to photograph the "hideout". What is the phone's model?**
   **Resposta:** `ONEPLUS A6013`

5. **A phone was used to photograph the "warehouse". What is the phone's model?**
   **Resposta:** `Mi 9 Lite`

6. **Are there any indications that the suspect is involved in other illegal activity? (Y/N)**
   **Resposta:** `Y`

7. **Who was the point of contact of Mr William S. McClean in 2022?**
   **Resposta:** `Karl Renato Abelardo`

8. **A meetup occurred in 2022. What are the GPS coordinates during that time?**
   **Resposta:** `14°26'25.7"N 120°59'00.8"E`

9. **What is the password to extract the contents of pandorasbox.zip?**
   **Resposta:** `DarkVault$Pandora=DONOTOPEN!K1ngCr1ms0n!`

10. **From which company did the source code in the pandorasbox directory originate?**
    **Resposta:** `SwiftSpend Financial`

11. **In one of the documents that the suspect has yet to sign, who was listed as the beneficiary?**
    **Resposta:** `Mr. Giovanni Vittorio DeVentura`

12. **What is the hidden flag?**
    **Resposta:** flag no formato `THM{...}` presente no arquivo indicado pela sala (DONOTOPEN).

**O que esta tarefa demonstra:**

* extensão de arquivo pode ser disfarçada; por isso se olha o cabeçalho real e metadados;
* metadados de imagem revelam aparelho e localização, o que ajuda a vincular o suspeito a encontros e a outras pessoas;
* arquivos compactados protegidos podem conter material sensível de empresa, sustentando a acusação de espionagem;
* há documentos inacabados que indicam beneficiário e, portanto, motivação financeira ou vínculo com terceiros.

---

### Task 8 – Post-Analysis of Evidence to Court Proceedings

Fechamento da sala: toda a coleta e toda a análise precisam ser colocadas dentro de um **fluxo de investigação** com quatro fases.

**Soluções:**

1. **In which phase is a warrant obtained for search, seizure, and examination of the suspect's computer data due to violations of domestic and international laws?**
   **Resposta:** `Pre-search`

2. **In which phase is a forensic analysis performed on the acquired digital evidence requested from various sources?**
   **Resposta:** `Post-search`

3. **Which phase involves presenting forensic artefacts and evidence with proper documentation in a court of law?**
   **Resposta:** `Trial`

**Ponto importante:** a sala deixa claro que há também a fase de **Search** (execução do mandado e apreensão). Assim, o ciclo fica: Pre-search → Search → Post-search → Trial.

---

## 3. Relações internas do documento

A sala é encadeada de forma “em degraus”:

* **Tasks 2, 3 e 4**: papéis, documentos legais e cadeia de custódia — mostram que o pendrive não apareceu “do nada”.
* **Tasks 5 e 6**: aquisição e validação com FTK Imager — mostram que o exame vai ser em cima de cópia verificada.
* **Task 7**: análise de conteúdo — mostra que uma única mídia pode revelar autor, local, aparelho, empresa vítima e até beneficiário.
* **Task 8**: volta para o processo — mostra que tudo isso só tem valor porque está dentro das fases de investigação.

Isso transforma um artefato simples (um pendrive) em um caso completo e apresentável.

---

## 4. Referências ao repositório de estudos

* **01_Introducao/01_Fundamentos.md**
  Complemento conceitual para tipos de investigação e necessidade de mandado.

* **02_Coleta_de_Evidencias/01_Imagem_de_Disco.md**
  Detalhamento da etapa que a sala chama de “Taking an image”.

* **05_Procedimentos/06_Documentacao_e_Cadeia_de_Custodia.md**
  Expansão da parte de “Ensure proper documentation” e de “Bag, seal, and tag”.

* **04_Ferramentas/01_Aquisicao/01_FTK_Imager.md**
  Descrição completa da ferramenta usada nas tasks 5 e 6.

* **04_Ferramentas/08_Utilitarios_de_Apoio/03_ExifTool.md**
  Parte técnica que aparece na task 7 para identificar tipo real de arquivo e ler EXIF.