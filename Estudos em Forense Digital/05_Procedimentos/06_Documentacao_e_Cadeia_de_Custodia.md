---
title: "Documentação e Cadeia de Custódia"
tags: [procedimento, documentacao, cadeia-de-custodia, hash, evidencias, dfir]
status: rascunho
---

# Documentação e Cadeia de Custódia

Este procedimento define **como registrar, nomear, proteger e rastrear** as evidências digitais que você coletar ao longo de uma investigação forense ou resposta a incidente. Ele complementa o que foi explicado em `01_Introducao/02_Cadeia_de_Custodia.md`, mas aqui está escrito para o **dia a dia de laboratório**: você coletou memória, imagem, PCAP, saída do KAPE — e agora precisa garantir que isso não se perde, não se mistura e pode ser comprovado.

Ideia-chave: **se não estiver documentado, não aconteceu**.

---

## 1. Objetivos

- Ter **rastreabilidade**: saber de onde veio cada evidência.
- Ter **integridade**: provar que o arquivo não foi alterado (hash).
- Ter **controle de posse**: saber quem teve acesso e quando.
- Ter **reprodutibilidade**: alguém pode repetir o exame porque você anotou comandos e versões.
- Ter **organização**: separar o que é bruto do que é processado.

---

## 2. Estrutura recomendada por caso

Crie uma estrutura base para **todo caso**. Exemplo:

```text
caso_2025-11-07_incidente-windows/
  00_docs/
  01_brutos/
  02_processados/
  03_relatorios/
  hashes/
  _tmp/   (opcional)
````

**O que vai em cada pasta:**

* **00_docs/** → anotações de campo, formulário de coleta, prints de tela, e-mail autorizando, foto do equipamento, recibo de apreensão.
* **01_brutos/** → TUDO que veio direto do alvo e não foi modificado:

  * imagem de disco (RAW, E01…)
  * dump de memória
  * pasta do KAPE
  * PCAPs de coleta
* **02_processados/** → saídas de ferramentas:

  * CSVs do EvtxECmd
  * saída de Chainsaw
  * cronologias do psort
  * parsers de Registro
  * dumps extraídos do Volatility
* **03_relatorios/** → relatório preliminar, relatório final, notas de conclusão.
* **hashes/** → todos os arquivos `.sha256` / `.md5` e, se quiser, um `manifesto_hashes.txt`.
* **_tmp/** → se precisar trabalhar com algo que depois será descartado.

Assim você nunca mistura **evidência** com **artefato derivado**.

---

## 3. Registro mínimo de evidência (modelo)

Para **cada** evidência, crie um registro. Pode ser um markdown dentro de `00_docs/` chamado `evidencias.md` ou uma planilha. Modelo:

```text
ID da evidência: EV-001
Nome do arquivo: 2025-11-07_WIN10-SAC-01_memdump.raw
Descrição: Dump de memória do host WIN10-SAC-01 (Windows 10)
Origem: Coletado em campo no endereço X, host ligado, usuário logado
Data/hora da coleta: 2025-11-07 14:20:33 -03
Ferramenta e versão: DumpIt 3.0.202
Tamanho: 4,096,000,000 bytes
Hash (SHA-256): 8b6a... (valor completo)
Coletado por: João Silva
Local de armazenamento: \\servidor\casos\caso_2025-11-07_incidente-windows\01_brutos\
Observações: Coleta feita antes de desligar o host. Nenhum antivírus interceptou.
```

Se tiver outra evidência (imagem de disco), vira **EV-002**. Se tiver pasta do KAPE, **EV-003**, e assim por diante.

**Importante:** o ID (EV-001, EV-002…) deve ser único dentro do caso.

---

## 4. Cálculo e guarda de hash

### 4.1 Quando calcular

* logo após coletar (ainda no campo, se der);
* logo após copiar para o servidor de casos;
* sempre que o arquivo for transferido para outro meio (HD, pen, nuvem interna).

### 4.2 Como calcular (exemplo)

```bash
sha256sum 2025-11-07_WIN10-SAC-01_memdump.raw > 2025-11-07_WIN10-SAC-01_memdump.raw.sha256
```

Isso gera um arquivo de texto com o hash. Coloque esse `.sha256` em `hashes/`.

Faça o mesmo para a imagem de disco:

```bash
sha256sum 2025-11-07_WIN10-SAC-01_disk.E01 > 2025-11-07_WIN10-SAC-01_disk.E01.sha256
```

### 4.3 Verificando depois

Se você copiar a evidência para outro lugar, verifique:

```bash
sha256sum -c 2025-11-07_WIN10-SAC-01_memdump.raw.sha256
```

Se vier “OK”, a integridade está mantida. Se não vier, registre o incidente (não apague a evidência, mas anote que houve divergência).

---

## 5. Cadeia de custódia (controle de posse)

A cadeia de custódia é só um **histórico de quem teve a evidência em mãos e o que fez**. Pode ser simples assim:

```text
[2025-11-07 14:30 -03] EV-001 coletada por João Silva (Analista).
[2025-11-07 15:05 -03] EV-001 entregue a Maria Costa (Laboratório) para cópia e análise.
[2025-11-07 16:10 -03] EV-001 armazenada em \\servidor\casos\...\01_brutos\ (somente leitura).
[2025-11-08 09:20 -03] EV-001 montada em estação LAB-02 para análise (cópia de trabalho).
```

Se houver **mídia física** (HD externo, pen drive):

* etiquetar com:

  * ID do caso
  * ID da evidência
  * data
  * hash principal
* guardar em local trancado/registrado.

O importante é que se alguém perguntar “quem mexeu nisso?”, você consegue mostrar a linha.

---

## 6. Registro de comandos e versões

Isso é o que todo mundo esquece e depois se arrepende 😅
Toda vez que você usar uma ferramenta do repositório, anote:

* comando exato;
* versão da ferramenta;
* data/hora;
* arquivo de entrada;
* arquivo de saída.

Exemplos:

```text
[2025-11-07 17:02 -03]
Ferramenta: Volatility3 1.0.1
Comando: python3 vol.py -f 2025-11-07_WIN10-SAC-01_memdump.raw windows.pslist
Entrada: 01_brutos/2025-11-07_WIN10-SAC-01_memdump.raw
Saída: 02_processados/vol_pslist.txt
```

```text
[2025-11-07 17:20 -03]
Ferramenta: Chainsaw 2.6.0
Comando: chainsaw scan .\evtx\ --mapping winlogbeat --rules ./rules/
Entrada: 01_brutos/KAPE-Targets/WindowsEventLogs/
Saída: 02_processados/chainsaw_findings.csv
```

```text
[2025-11-07 17:40 -03]
Ferramenta: plaso 20241001
Comando: log2timeline.py timeline.plaso /mnt/evidencia
Entrada: imagem montada (read-only)
Saída: 02_processados/timeline.plaso
```

Por que isso importa? Porque ferramentas mudam parse e output de versão para versão. Se amanhã alguém rodar com outra versão e der resultado diferente, o seu documento mostra qual versão você usou.

---

## 7. Nomeação de arquivos (padrão sugerido)

Use nomes que contem:

1. data
2. host/origem
3. tipo
4. extensão

Exemplos:

```text
2025-11-07_WIN10-SAC-01_memdump.raw
2025-11-07_WIN10-SAC-01_disk.E01
2025-11-07_WIN10-SAC-01_kape.zip
2025-11-07_WIN10-SAC-01_timeline.csv
2025-11-07_WIN10-SAC-01_evtx-parsed.csv
```

Para hashes:

```text
2025-11-07_WIN10-SAC-01_memdump.raw.sha256
2025-11-07_WIN10-SAC-01_disk.E01.sha256
```

Assim você bate o olho e sabe o que é cada coisa sem abrir.

---

## 8. Documentando divergências e limitações

Nem sempre tudo sai perfeito. Documente também o que **não** deu certo:

```text
[2025-11-07 15:30 -03] Tentativa de imagem completa falhou por falta de espaço no disco externo.
[2025-11-07 15:40 -03] Coleta parcial feita com KAPE apenas.
[2025-11-07 15:50 -03] Host não pôde ser desligado por exigência do responsável.
```

Isso protege você e explica por que a evidência está incompleta.

---

## 9. Armazenamento e permissões

* evidências brutas (`01_brutos/`) devem ser **somente leitura** no servidor;
* acesso controlado (somente grupo de forense/IR);
* backups automáticos do servidor de casos;
* nunca trabalhar diretamente na evidência bruta → sempre copiar para área de trabalho (`_tmp/` ou máquina de análise) e analisar lá.

Se for preciso enviar evidência para outro time:

* compactar (zip/7z)
* anexar o arquivo de hash
* informar o hash por canal separado (ex.: e-mail ou ticket)

---

## 10. Checklist rápido

* [ ] Pasta do caso criada com estrutura padrão
* [ ] Cada evidência tem ID, descrição, data/hora, ferramenta e hash
* [ ] Hashes salvos em `hashes/`
* [ ] Cadeia de custódia atualizada (quem teve posse)
* [ ] Comandos e versões das ferramentas registrados
* [ ] Arquivos nomeados com data e host
* [ ] Divergências (falha de imagem, falta de espaço, antivírus) documentadas
* [ ] Evidências brutas em local somente leitura

---

## 11. Relação com o resto do repositório

* fundamentos → `01_Introducao/02_Cadeia_de_Custodia.md`
* cálculo de hash → `04_Ferramentas/08_Utilitarios_de_Apoio/01_Hashes_hashdeep_sha256sum_md5sum.md`
* coleta em campo → `05_Procedimentos/05_Coleta_Rapida_em_Campo_com_KAPE.md`
* aquisição completa → `05_Procedimentos/01_Aquisicao_Padrao_Host_Windows.md`

Este documento é o que faz todas as outras pastas “valerem” juridicamente e tecnicamente: ele mostra que você sabe **o que pegou**, **quando pegou**, **como pegou** e **como provou que não mexeu**.