---
title: Artefatos de Execução em Windows
tags:
  - forense
  - windows
  - analise-de-sistemas
  - artefatos
  - execucao
status: concluido
---

# Artefatos de Execução em Windows

Artefatos de execução são rastros que o Windows (ou aplicativos do Windows) deixa quando um programa é aberto, um arquivo é acessado ou uma ação é realizada. Em forense, eles são fundamentais para responder à pergunta: **“isso realmente rodou?”**  
Esta nota reúne os artefatos mais comuns em laboratório/CTF e indica onde eles ficam, para que você possa correlacioná-los com Registro e Event Logs.

---

## 1. Objetivo

- Identificar os principais artefatos que provam ou indicam execução/acesso;
- Mostrar o caminho padrão no disco;
- Sugerir uma ordem de análise;
- Facilitar a correlação com os logs (`.evtx`) e com as chaves de persistência.

---

## 2. Prefetch

**O que é:** mecanismo do Windows que otimiza a inicialização de programas gravando informações sobre sua execução.

**Caminho:**

- `C:\Windows\Prefetch\`

**Extensão:**

- Arquivos `.pf` (ex.: `MALWARE.EXE-12345678.pf`)

**Por que é útil:**

- Indica que um executável foi rodado (em condições padrão - em alguns Windows Server ou quando o prefetch está desativado, pode não existir).
- Guarda **data/hora da última execução** e **quantidade de vezes executado**.
- Pode listar arquivos e DLLs acessados pelo programa.

**Uso forense:**

- Se você encontrar um binário suspeito no disco, procure o `.pf` com o mesmo nome.
- Se existir o `.pf`, isso reforça que o binário rodou naquela máquina.

---

## 3. Jump Lists e Recent

**Jump Lists** registram itens usados recentemente por um aplicativo (por exemplo, arquivos abertos pelo Word).

**Caminhos comuns:**

- `C:\Users\<USUARIO>\AppData\Roaming\Microsoft\Windows\Recent\`
- `C:\Users\<USUARIO>\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations\`
- `C:\Users\<USUARIO>\AppData\Roaming\Microsoft\Windows\Recent\CustomDestinations\`

**Por que é útil:**

- Mostra que o usuário abriu/visualizou determinado arquivo.
- Ajuda a ligar *usuário* + *arquivo* + *momento aproximado*.

**Em CTF/lab:**

- Às vezes o desafio deixa um arquivo “secreto” aberto recentemente e você consegue chegar ao caminho real via `Recent`.

---

## 4. LNK (Atalhos)

Quando o usuário abre um arquivo a partir do Explorer, o Windows costuma criar um atalho `.lnk` que guarda metadados sobre o acesso.

**Caminho típico:**

- `C:\Users\<USUARIO>\AppData\Roaming\Microsoft\Windows\Recent\*.lnk`

**Contém:**

- caminho original do arquivo;
- horário aproximado;
- volume/drive onde o arquivo estava.

**Uso forense:**

- Bom para provar que **aquele usuário** abriu **aquele arquivo** mesmo que o arquivo não esteja mais ali.

---

## 5. Amcache e AppCompat (Execução Histórica)

Sistemas Windows mais novos mantêm bancos que registram execução/instalação de binários:

- **Amcache.hve** geralmente em:
  - `C:\Windows\AppCompat\Programs\Amcache.hve`
- **AppCompatCache / Shimcache** é obtido via Registro (SYSTEM).

**Por que é útil:**

- Ajuda a provar que determinados executáveis estiveram presentes ou foram executados.
- Mesmo que o arquivo tenha sido apagado.

**Observação:**

- Normalmente você usa ferramentas para extrair/parsear (ex.: utilitários do Eric Zimmermann).

---

## 6. PowerShell e Script Execution

Se o ataque usou PowerShell, muitas vezes há rastros:

- Logs em:
  - `C:\Windows\System32\winevt\Logs\Microsoft-Windows-PowerShell/Operational.evtx`
- Histórico de PSReadLine em:
  - `C:\Users\<USUARIO>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt`

**Por que é útil:**

- Às vezes o comando malicioso (download, invoke-mimikatz, etc.) aparece quase em texto puro.
- É um ótimo complemento aos Event Logs.

---

## 7. Pastas de Startup (Execução no Logon)

Embora não sejam exatamente “artefatos de execução”, são **locais que causam execução** e por isso entram na rotina de análise:

- Global:
  - `C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\`
- Por usuário:
  - `C:\Users\<USUARIO>\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\`

Se houver `.lnk` ou script aqui, o Windows vai tentar rodar no logon.

---

## 8. Ordem de Análise Recomendada

1. **Prefetch** → provar que o executável rodou.
2. **Recent / Jump Lists / LNK** → amarrar usuário + arquivo.
3. **Amcache / AppCompat** → provar presença/execução histórica.
4. **PowerShell / Logs** → recuperar comando ou script.
5. **Startup + Registro** → ver se havia persistência para reexecutar.

Essa ordem funciona bem porque vai do mais óbvio (prefetch) para o mais específico (PowerShell).

---

## 9. Correlação com Registro e Logs

Para fechar a narrativa:

- Se o Registro mostra persistência em `Run` apontando para `C:\Users\aluno\AppData\Roaming\svchost.exe`
  - verifique Prefetch de `SVCHOST.EXE`
  - verifique se há `.lnk` ou Recent apontando para o mesmo local
  - verifique Event Logs por criação de processo no mesmo horário

Quanto mais artefatos apontarem para o mesmo executável, mais forte fica a evidência.

---

## 10. Checklist de Artefatos de Execução

1. Listar arquivos em `C:\Windows\Prefetch\`.
2. Listar `.lnk` e itens em `C:\Users\<USUARIO>\AppData\Roaming\Microsoft\Windows\Recent\`.
3. Extrair/parsear `Amcache.hve` se existir.
4. Procurar histórico de PowerShell.
5. Verificar pastas de Startup.
6. Registrar horários para montar timeline.

Assim o analista consegue navegar entre “fonte de execução”, “persistência” e “prova de que rodou”.

---

## 11. Conclusão

Artefatos de execução são o elo entre “o arquivo estava no disco” e “o arquivo realmente foi usado”. Em Windows eles são abundantes, distribuídos entre sistema e perfil do usuário, e quase sempre permitem reconstituir a atividade mesmo quando o atacante apagou o binário. Coletar todos, cronometrar e correlacionar com logs e Registro é o passo final para uma linha do tempo coerente.
