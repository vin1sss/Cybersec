---
title: "Autopsy"
tags: [ferramenta, analise, imagem-forense, autopsy, tsk, timeline, ingest, relatorios]
status: rascunho
---

# Autopsy

Este documento descreve o uso do **Autopsy** como plataforma de análise forense sobre imagens de disco, mídias e outros contêineres de evidência. Ele é a interface gráfica do The Sleuth Kit (TSK) e foi pensado para tornar mais simples tarefas que, na linha de comando, exigiriam várias etapas: montar a imagem, percorrer sistema de arquivos, localizar artefatos de usuário, buscar arquivos suspeitos e gerar um relatório final.

A ideia central é: **a aquisição é feita antes, em outra ferramenta (FTK Imager, Guymager, dd/dc3dd), e o Autopsy entra para examinar o que foi adquirido**, sem alterar a evidência original.

Fluxo recomendado de trabalho:

1. adquirir e validar a imagem;
2. criar/abrir um caso no Autopsy;
3. adicionar a imagem como fonte de dados;
4. configurar e rodar módulos de ingestão;
5. navegar pelos artefatos e evidências;
6. gerar relatório e registrar achados.

---

## 1. Conceito

O Autopsy é uma solução de **exame e análise**. Diferente das ferramentas de aquisição (que copiam bit a bit), ele trabalha sobre o que já foi copiado:

- lê vários formatos de imagem (RAW, E01, VMDK, VHD);
- reconstrói partições e sistemas de arquivos;
- extrai automaticamente artefatos de interesse (histórico de navegador, programas instalados, documentos, arquivos apagados);
- fornece visualizações variadas (árvore, tabela, conteúdo, timeline);
- permite gerar relatórios completos.

Por ter arquitetura modular, você pode escolher o que quer extrair - o que torna o trabalho mais rápido quando o caso é grande.

---

## 2. Fluxo de trabalho detalhado

### 2.1 Criar ou abrir caso
Ao iniciar o Autopsy você tem duas possibilidades principais:

- **New Case** - criar um caso novo, informando:
  - nome do caso;
  - diretório base, onde o Autopsy vai guardar tudo (configurações, banco local, relatórios, arquivos temporários);
  - tipo de caso (single user ou multiuser, quando há servidor).
- **Open Case** - abrir um caso já existente com extensão `.aut`.

O conceito de "caso" é importante porque **tudo que você fizer fica encapsulado ali**: as fontes de dados, os módulos que rodaram, os resultados e os relatórios.

### 2.2 Adicionar fonte de dados (data source)
Depois de ter o caso, você adiciona o que vai analisar. Os tipos mais comuns:

- imagem de disco ou VM;
- pasta de arquivos;
- disco físico conectado.

Para forense de disco, o mais comum é selecionar **Disk Image or VM File** e apontar para o arquivo de imagem que você criou na fase de aquisição. Se a imagem for dividida (E01, E02, E03...), basta apontar para o primeiro arquivo.

### 2.3 Configurar ingestão
Na sequência, o Autopsy pergunta quais **ingest modules** devem rodar. É aqui que você define o "quanto" de análise automática ele vai fazer:

- módulos de histórico de navegador;
- detecção de arquivos interessantes;
- recuperação de arquivos apagados;
- análise de e-mail, documentos, miniaturas, etc.

Quanto mais módulos você ativar, mais completo é o resultado - mas mais tempo leva.

### 2.4 Revisar e relatar
Com os módulos concluídos, você passa a navegar pelos artefatos extraídos, filtrar, buscar e, por fim, gerar o relatório no formato desejado.

---

## 3. Fontes de dados suportadas

O Autopsy consegue trabalhar com os formatos que você já usa nas ferramentas de aquisição:

- **RAW/IMG/DD** - cópia direta setorial;
- **RAW dividido** - `.001`, `.002`, etc.;
- **EnCase (E01)** - muito usado em ambiente pericial;
- **VMs** - `.vmdk`, `.vhd`.

Pontos importantes:

- se a imagem estiver particionada, o Autopsy mostra cada volume separadamente;
- se a imagem estiver dividida em várias partes, ele reconstrói sozinho;
- se a imagem estiver em outro local que não o original, o Autopsy pode pedir para você apontar o caminho.

Isso permite que você mantenha uma rotina de aquisição padronizada e apenas analise no Autopsy.

---

## 4. Módulos de ingestão

Os **ingest modules** são o coração da automação do Autopsy. Eles são responsáveis por "varrer" a fonte de dados e popular o painel de resultados.

Características:

- podem ser habilitados quando você adiciona a fonte ou mais tarde;
- alguns têm configuração por execução (você escolhe o escopo ou a lista de arquivos);
- os resultados vão para o nó **Results** na árvore;
- o andamento aparece na barra de status.

Exemplos de módulos comuns:

- **File Type Identification** - classifica por tipo real de arquivo;
- **Interesting Files** - procura arquivos que batem com padrões pré-definidos;
- **Web History / Web Bookmarks** - extrai artefatos de navegação;
- **Installed Programs / OS Info** - ajuda a identificar o sistema;
- **Keyword Search** - busca por termos específicos;
- **Recent Activity** - junta de forma fácil atividades recentes do usuário.

Vantagem: mesmo que você não saiba exatamente onde está o artefato na imagem, o módulo já te leva para o lugar certo.

---

## 5. Interface do Autopsy (visão completa)

A interface é pensada para você chegar ao artefato com poucos cliques. Ela é composta de cinco áreas principais:

### 5.1 Tree Viewer
Painel da esquerda. Tem alguns nós importantes:

- **Data Sources** - mostra a imagem, volumes e diretórios como se fosse um explorador de arquivos;
- **Views** - mostra o mesmo conteúdo, mas agrupado por tipo (por extensão, por MIME, por tamanho, por arquivos apagados);
- **Results** - onde aparecem os resultados dos módulos de ingestão;
- **Tags** - itens que você marcou manualmente;
- **Reports** - relatórios que você já gerou.

Dica: quando estiver procurando "algo suspeito", o nó **Views** é muito útil porque mostra coisas como "arquivos apagados" e "por extensão".

### 5.2 Result Viewer
Painel central que muda conforme você clica na árvore. É onde você vê:

- lista de arquivos;
- lista de eventos;
- lista de artefatos extraídos.

Tem abas de exibição (tabela, miniaturas, resumo) para você alternar conforme o tipo de dado.

### 5.3 Contents Viewer
Painel inferior/direito que mostra o conteúdo do item selecionado: texto, hex, metadados. É útil quando você quer olhar o arquivo sem precisar exportar.

### 5.4 Keyword Search
Barra de busca superior que permite buscas rápidas por string, caso você queira procurar um nome de arquivo, um domínio ou uma palavra-chave sem montar uma regra complexa.

### 5.5 Status Area
Área inferior que mostra o que está rodando. Se os módulos estiverem lentos (imagem grande), dá para acompanhar o progresso e até cancelar.

---

## 6. Análise prática dentro do Autopsy

Depois que os módulos terminam, o que normalmente se faz?

1. **Olhar o resumo da fonte de dados**  
   - para entender sistema operacional, tamanho, tipos de arquivos predominantes.

2. **Explorar o nó Results**  
   - ver se há arquivos “interessantes” encontrados automaticamente;
   - ver se há programas instalados que chamam atenção;
   - ver histórico de navegação.

3. **Explorar o nó Views**  
   - checar arquivos apagados;
   - checar por tipo real (MIME) - útil para descobrir arquivos renomeados.

4. **Extrair arquivo específico**  
   - clicando com o botão direito em um item e escolhendo “extract”.

5. **Montar linha do tempo**  
   - para correlacionar quando o usuário mexeu em determinados arquivos.

Esse ciclo pode ser repetido até que você encontre o artefato que responde à pergunta do caso.

---

## 7. Timeline (linha do tempo)

A linha do tempo do Autopsy ajuda a responder “o que aconteceu quando?”. Ela:

- agrupa eventos de sistema e de arquivos por data/hora;
- permite filtrar por tipo de evento;
- tem modos de visualização (contagem, detalhes, lista);
- permite destacar (pin) grupos de eventos importantes.

É especialmente útil para:

- detectar picos de atividade (ex.: dia em que muitos arquivos foram acessados);
- localizar quando um artefato foi criado/modificado;
- ver se uma atividade suspeita coincide com o horário do incidente.

---

## 8. Relatórios

Ao final da análise, o Autopsy permite gerar relatórios em formatos comuns (por exemplo, HTML). Características dos relatórios:

- reúnem em um só lugar o que estava espalhado nos painéis;
- podem ser abertos sem o Autopsy;
- servem de base para laudo ou documentação final;
- ajudam quando a máquina onde o Autopsy está rodando é fraca (analisa-se dentro do Autopsy, exporta-se o relatório e continua-se a leitura no navegador).

Você escolhe o que entra no relatório conforme o escopo do seu exame.

---

## 9. Bloco operacional

```text
comando: análise de imagem com Autopsy

1. Abrir o Autopsy
2. Criar caso novo (informar nome e diretório base)
3. Adicionar fonte: Disk Image or VM File
4. Selecionar a imagem (RAW, E01, VMDK...)
5. Na etapa de ingestão, marcar os módulos desejados
6. Aguardar o processamento completo
7. Navegar por Data Sources, Views e Results
8. Extrair arquivos ou artefatos necessários
9. Gerar relatório em HTML para documentação
````

---

## 10. Comparação com outras ferramentas de análise

* **Autopsy** - completo, gráfico, bom para ensino e relatórios, modular.
* **The Sleuth Kit (CLI)** - mesmo motor, mas requer conhecimento de comandos; ótimo para automação e ambientes sem GUI.
* **Soluções comerciais (X-Ways, Magnet Axiom, EnCase Forensics)** - geralmente mais integradas, com mais parseadores prontos e suporte corporativo.
* **Ferramentas específicas (RegRipper, EvtxECmd, Chainsaw)** - ótimas para artefatos específicos, mas não substituem a visão geral que o Autopsy oferece.

---

## 11. Boas práticas

* trabalhar sempre sobre imagem, nunca no disco original;
* nomear os casos de forma lógica (ex.: ano-caso-numero);
* não ativar todos os módulos se a máquina for fraca - começar pelos essenciais;
* exportar e versionar o relatório gerado;
* registrar horários de início e fim do processamento - útil para documentação forense.