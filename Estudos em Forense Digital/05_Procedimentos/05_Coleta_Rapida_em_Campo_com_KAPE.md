---
title: "Coleta Rápida em Campo com KAPE"
tags: [procedimento, coleta-rapida, kape, windows, campo, dfir]
status: rascunho
---

# Coleta Rápida em Campo com KAPE

Este procedimento descreve como **usar o KAPE em um host Windows no local (campo)** para capturar rapidamente os artefatos de maior valor forense e trazer para o laboratório. Ele é para situações em que:

- você **não tem tempo** de fazer imagem completa;
- a máquina **precisa ser devolvida rápido** ao usuário;
- você quer garantir que **logs, hives, prefetch, artefatos de usuário** não se percam;
- você já tem o KAPE preparado em mídia removível.

A ideia é sair do local com uma pasta organizada, compactada e com hash, pronta para análise.

---

## 1. Pré-requisitos

- pendrive/HD com:
  - pasta do KAPE (kape.exe + targets/modules);
  - espaço para saída (ou usar outro disco de destino);
- acesso administrativo na máquina-alvo;
- relógio anotado (timezone);
- política/autorização de coleta.

Opcional, mas recomendado: ferramenta de hash (ex.: `sha256sum` em Windows via WSL, ou um hasher portátil).

---

## 2. Ordem geral (resumo)

1. **Identificar a máquina e registrar contexto**
2. **Conectar mídia e escolher destino**
3. **Rodar KAPE com target de triagem**
4. **(Opcional) Rodar módulos de parsing**
5. **Compactar e calcular hash da coleta**
6. **Etiquetar e levar ao laboratório**

---

## 3. Identificar e registrar contexto

Antes de rodar qualquer coisa:

- anotar: hostname, IP, usuário logado;
- data/hora local;
- motivo da coleta (“suspeita de malware”, “exfiltração”, “movimentação lateral”);
- se conseguir, anotar também versão do Windows.

Isso vai para o relatório e para o nome da pasta.

---

## 4. Conectar mídia e escolher destino

O ideal é **não gravar na própria máquina-alvo**, mas sim em disco externo, por exemplo `E:\`.

Crie uma pasta com nome claro:

```text
E:\coleta_HOST123_2025-11-07\
````

Dentro dela o KAPE vai criar `KAPE-Targets` e (se você rodar) `KAPE-Modules`.

---

## 5. Rodar KAPE (coleta)

Com CMD/PowerShell **como administrador**, na pasta do KAPE:

```text
kape.exe --tsource C: --tdest E:\coleta_HOST123_2025-11-07 --target !SANS_Triage
```

Explicando:

* `--tsource C:` → é o disco de origem (máquina-alvo)
* `--tdest ...` → é o destino, no seu disco externo
* `--target !SANS_Triage` → target padrão de alto valor (logs, hives, artefatos de usuário, etc.)

Se você tiver outro target da sua equipe (ex.: `IR_Windows_Base`), use ele, mas a lógica é essa: **um target amplo de triagem**.

Aguardar o término e conferir se a pasta `KAPE-Targets` foi criada.

---

## 6. (Opcional) Rodar módulos

Se você tem **um pouco mais de tempo** e quer sair já com CSVs prontos:

```text
kape.exe --msource E:\coleta_HOST123_2025-11-07 --mdest E:\coleta_HOST123_2025-11-07\parsed --module !EZParser
```

* `--msource` aponta para o que você acabou de coletar;
* `--mdest` cria uma pasta com resultados parseados;
* `!EZParser` roda vários parsers úteis (eventos, registro, etc.).

Se o host estiver muito sobrecarregado ou você estiver com pressa, **não rode módulos em campo** - faça no laboratório apontando para a pasta coletada.

---

## 7. Compactar e calcular hash

No próprio disco externo (ou depois, já no laboratório), compacte:

```text
coleta_HOST123_2025-11-07.zip
```

Depois calcule o hash desse ZIP e anote:

```text
sha256sum coleta_HOST123_2025-11-07.zip > coleta_HOST123_2025-11-07.zip.sha256
```

Ou use sua ferramenta de hash portátil.

Registrar isso no relatório: nome do host, data, hash do pacote.

---

## 8. O que vai ter dentro

Depois do KAPE, a estrutura costuma ser:

```text
E:\coleta_HOST123_2025-11-07\
  ├── KAPE-Targets\
  │     ├── Registry\
  │     ├── WindowsEventLogs\
  │     ├── Prefetch\
  │     ├── Users\
  │     └── ...
  └── KAPE-Modules\   (se você rodou)
        └── ... (CSVs prontos)
```

Tudo isso pode ser analisado no laboratório com:

* RegRipper / regipy (para hives);
* Chainsaw / EvtxECmd (para logs);
* análise de artefatos de usuário;
* timeline, se você quiser jogar no plaso (dá pra apontar para essa pasta).

---

## 9. Boas práticas

* rodar como administrador;
* usar sempre **destino externo** (não poluir o disco C: do alvo);
* não renomear o que o KAPE gerou (melhor compactar e renomear o ZIP);
* anotar **o comando exato** que foi usado (ou fazer foto da tela);
* se o antivírus gritar, registrar isso também;
* se o tempo acabar, priorizar **targets** e deixar **modules** para o laboratório.

---

## 10. Relação com outros procedimentos

* se o caso for maior e houver tempo → seguir o `05_Procedimentos/01_Aquisicao_Padrao_Host_Windows.md`
* se a análise for Windows completa → `05_Procedimentos/02_Analise_Inicial_de_Imagem_Windows.md`
* se o alerta for de malware → `05_Procedimentos/04_Resposta_a_Incidente_de_Malware.md`

Este aqui é o “modo rápido”: sair do local com material suficiente para trabalhar depois.