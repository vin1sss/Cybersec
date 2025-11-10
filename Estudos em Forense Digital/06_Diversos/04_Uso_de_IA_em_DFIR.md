---
title: "Uso de IA em DFIR"
tags: [diversos, ia, dfir, automacao, etica, cadeia-de-custodia]
status: rascunho
---

# Uso de IA em DFIR

Este documento registra **como** usar ferramentas com inteligência artificial/aprendizado de máquina para ajudar na forense digital **sem perder caráter forense**. A ideia aqui não é ensinar IA, e sim dizer: “ok, posso usar, mas onde, até onde e o que preciso documentar”.

Ponto central: **IA ajuda, mas quem conclui é o analista.**

---

## 1. Por que IA ajuda na forense?

Porque DFIR tem muito disso:

- volumes grandes de logs;
- muitos eventos parecidos;
- correlação entre fontes diferentes (Windows, proxy, EDR, firewall);
- tarefas repetitivas (classificar, marcar suspeitos, priorizar).

IA/ML é boa em:
- **classificação** (isto parece suspeito / isto parece normal);
- **detecção de anomalia** (isto não é o padrão deste usuário/host);
- **agregação e timeline** (juntar eventos que parecem do mesmo fluxo).

Então ela é ótima para **triagem** e **priorização**.

Exemplos de uso seguro:
- marcar quais logs olhar primeiro;
- sugerir quais arquivos têm alta entropia ou nome estranho;
- agrupar eventos para montar um rascunho de timeline;
- apontar padrões de exfiltração.

---

## 2. Onde **não** usar IA sozinha

Existem partes da investigação que precisam ser **explicáveis**:

- conclusão de que “o usuário X exfiltrou o arquivo Y”;
- afirmação de autoria;
- laudo final para área jurídica;
- decisão sobre se a evidência é válida ou não.

IA costuma ser **probabilística** (mesmo input → pode mudar o output) e às vezes não diz por que chegou naquela resposta (“black box”). Então nesses pontos ela **não substitui** a análise humana.

---

## 3. Riscos típicos

1. **Não determinismo**  
   Mesmo log, prompt parecido → resultado diferente. Em forense isso é ruim porque você precisa repetir o exame.

2. **GIGO (Garbage In, Garbage Out)**  
   Se o modelo foi treinado com dados ruins, vai sugerir coisa errada com cara de certa.

3. **Viés**  
   Modelo pode priorizar certos idiomas, formatos ou caminhos e ignorar outros. Em investigação isso pode atrasar a descoberta.

4. **Explicabilidade limitada**  
   Se você não consegue mostrar “por que” a ferramenta marcou um e-mail como suspeito, isso pode ser questionado.

5. **Cadeia de custódia confusa**  
   Se você manda evidência pra um serviço externo de IA sem registrar, você perde o controle de onde o dado passou.

---

## 4. Como usar de forma forense

Para usar IA e continuar no padrão do repositório:

1. **Registrar a ferramenta e versão**  
   “Ferramenta: classificador de logs interno (scikit-learn 1.x) – 2025-11-07”

2. **Registrar o input**  
   Qual arquivo/log você passou? (`/var/log/auth.log`, `evt_exported.evtx`, `timeline.csv`)

3. **Guardar o output**  
   Salvar o CSV/JSON que a IA gerou em `02_processados/` ou equivalente.

4. **Dizer que foi sugestão**  
   No relatório: “os eventos abaixo foram priorizados por ferramenta de classificação; foram revistos manualmente”.

5. **Evitar envio externo de dado sensível**  
   Se for dado de cliente, forense de colaborador, PII, etc., usar modelo on-prem ou ambiente controlado.

6. **Revalidar o que ela marcou**  
   IA marcou `/tmp/.syncd` como suspeito? Abrir, ler, confirmar. IA marcou código-fonte como suspeito só porque era incomum? Documentar como falso positivo.

---

## 5. Casos de uso bons

- **Triagem de logs de autenticação**  
  Classificar falhas x sucessos em horários estranhos para você olhar só o que importa.

- **Detecção de arquivos fora do padrão**  
  Olhar tamanho, extensão, entropia, caminho → marcar suspeitos.

- **Agrupamento para timeline**  
  Juntar vários eventos relacionados em um “bloco” do ataque, e você só organiza.

- **Auxílio em análise de comunicação**  
  Buscar termos de spear phishing, urgência, spoofing de executivo.

- **Auxílio em hunting de malware**  
  Destacar processos/artefatos que fogem da base, você valida com Volatility ou artefatos de execução.

---

## 6. O que precisa constar na documentação

No seu registro do caso (aquele que fizemos em procedimentos), inclua uma sessão tipo:

```text
Ferramentas assistidas por IA utilizadas:
- classificador_de_logs.py (scikit-learn 1.5) em 2025-11-07 15:20 -03
  Entrada: /evidencias/caso01/01_brutos/auth.log
  Saída: /evidencias/caso01/02_processados/auth_classificado.csv
  Uso: priorização de eventos para análise humana.
- file_anomalies.py (modelo interno, 2025-11-07)
  Entrada: /opt/robbco/..., /tmp/...
  Saída: /evidencias/caso01/02_processados/anomalias.csv
  Observação: 2 falsos positivos (código-fonte proprietário).
````

Assim, se alguém perguntar “de onde veio essa suspeita?”, está lá.

---

## 7. Relação com cadeia de custódia

* se a IA **só leu** arquivos da cópia de trabalho, ok;
* se a IA foi um **serviço externo**, registrar isso (data, endpoint, quem autorizou);
* se a IA **transformou** o dado (resumiu, classificou), o arquivo de output também vira evidência derivada e deve ser guardado.

Isso mantém o alinhamento com `05_Procedimentos/06_Documentacao_e_Cadeia_de_Custodia.md`.

---

## 8. Limite saudável

Use IA para:

* reduzir volume,
* apontar padrões,
* sugerir prioridades.

Não use IA para:

* concluir autoria,
* descartar evidência sem olhar,
* gerar relatório final sem revisão.

---

## 9. Relacionados neste repositório

* documentação e cadeia → `05_Procedimentos/06_Documentacao_e_Cadeia_de_Custodia.md`
* timeline e correlação → `04_Ferramentas/05_Timeline_e_Correlacao/`
* coleta e imaging → `06_Diversos/03_Imaging_no_Linux_com_dc3dd.md`
* conceitos gerais → `06_Diversos/01_Glossario_Forense.md`

IA é uma **lupa**, não o perito.