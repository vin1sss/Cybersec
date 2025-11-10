---
title: "Aquisição Padrão de Host Windows (campo/laboratório)"
tags: [procedimento, aquisicao, windows, cadeia-de-custodia, coleta, dfir]
status: rascunho
---

# Aquisição Padrão de Host Windows (campo/laboratório)

Este procedimento descreve **a ordem recomendada de ações** ao chegar em um host Windows que precisa ser preservado para análise forense. Ele junta o que já está documentado em `02_Coleta_de_Evidencias`, nos arquivos de aquisição de disco e memória, e em `04_Ferramentas/01_Aquisicao`, mas coloca tudo em **sequência prática**.

Objetivo: sair da máquina com **memória**, **imagem ou cópia forense de disco (quando viável)**, **hashes registrados** e **anotações de contexto**, sem comprometer a cadeia de custódia.

---

## 1. Premissas

- o host está **ligado**;
- você tem permissão para coletar;
- você tem mídia de destino suficiente (HD externo, SSD, NAS ou rede segura);
- você tem as ferramentas portáteis (KAPE, FTK Imager, Guymager em estação externa, calc de hash);
- tudo que for coletado será **hashado e documentado**.

Se o host estiver **desligado**, o fluxo muda (não dá para coletar RAM); aqui focamos no cenário mais comum de IR: máquina viva.

---

## 2. Ordem geral (resumo)

1. **Registrar contexto e horário**
2. **Coletar memória volátil** (dump)
3. **Coletar artefatos rápidos (opcional)** – KAPE
4. **Aquisição de disco/imagem** (FTK Imager, Guymager, dd/dc3dd)
5. **Calcular e registrar hashes**
6. **Embalar e documentar**

Depois disso, a máquina pode ser desligada ou isolada conforme política.

---

## 3. Passo a passo

### 3.1 Registrar contexto e horário

- anotar data/hora de chegada (com timezone);
- anotar usuário logado, hostname, IP, sistema;
- fotografar/tirar print de telas relevantes (se houver janelas abertas, mensagens, criptografadores);
- se houver mídia acoplada (USB, cartão), anotar.

**Por quê?** Porque memória e logs vão mostrar eventos próximos dessa hora; você precisa de referência.

---

### 3.2 Coletar memória volátil

**Por que primeiro?** Porque é o que se perde ao desligar ou se o sistema mudar de estado.

- usar ferramenta apropriada (Magnet RAM Capture, winpmem, FTK Imager RAM, DumpIt);
- salvar o dump em disco externo/não-sistema (ex.: `E:\caso01\2025-11-07-memdump.raw`);
- imediatamente depois rodar hash (SHA-256) do dump e registrar.

Exemplo de registro:

```text
Evidência: memdump.raw
Hash (SHA-256): ...
Data/hora: 2025-11-07 14:20:33 -03
Operador: ...
Ferramenta: DumpIt 3.x
````

Depois, esse dump será analisado com **Volatility** ou **Rekall** (ver `04_Ferramentas/04_Memoria/...`).

---

### 3.3 Coleta rápida de artefatos (opcional, mas recomendada)

Se você está em IR e quer garantir que os artefatos de Windows não vão sumir, pode rodar **KAPE** logo após a memória:

```text
kape.exe --tsource C: --tdest E:\caso01\kape --target !SANS_Triage
```

Isso te dá uma cópia rápida de logs, hives, prefetch, etc. Se o tempo for MUITO curto, pode pular e ir direto para disco.

---

### 3.4 Aquisição de disco / imagem

Agora sim, a parte mais demorada.

**Opção 1 – FTK Imager (Windows)**

* conectar disco de destino;
* abrir FTK Imager;
* `Create Disk Image` → escolher fonte (físico ou lógico);
* escolher formato (RAW/E01);
* habilitar cálculo de hash;
* iniciar aquisição.

**Opção 2 – Guymager (em estação Linux)**

* conectar o disco da máquina suspeita em estação forense via write blocker;
* identificar o dispositivo;
* criar imagem em E01/RAW;
* calcular hash.

**Opção 3 – dd/dc3dd (Linux)**

* montar origem como somente leitura;
* rodar dd com `conv=noerror,sync` e bloco maior;
* calcular hash depois.

A escolha da ferramenta está descrita na pasta `04_Ferramentas/01_Aquisicao/`, este procedimento só diz **“faça agora”**.

---

### 3.5 Calcular e registrar hashes

Para cada arquivo gerado:

* dump de memória
* imagem de disco
* pacote do KAPE
* PCAP (se coletou rede)

calcular **SHA-256** (e se precisar MD5 também):

```bash
sha256sum caso01.img > caso01.img.sha256
```

Guardar os valores no relatório do caso. Isso amarra a cadeia de custódia.

---

### 3.6 Embalar e documentar

* colocar tudo em estrutura de pastas do caso:

  ```text
  caso01/
    memoria/
    disco/
    kape/
    hashes/
    docs/
  ```
* salvar o procedimento/relatório parcial com:

  * quem coletou
  * quando
  * o quê
  * com qual ferramenta/versão
  * hashes

---

## 4. Checklist rápido

* [ ] Data/hora e contexto anotados
* [ ] Dump de memória coletado
* [ ] Hash do dump registrado
* [ ] (Opcional) KAPE rodado e pasta salva
* [ ] Imagem de disco iniciada / concluída
* [ ] Hash da imagem registrado
* [ ] Mídia de destino identificada e etiquetada
* [ ] Relatório preliminar preenchido

---

## 5. Observações de segurança e cadeia de custódia

* sempre que possível, usar **write blocker** ao conectar mídia da máquina alvo;
* nunca editar arquivos originais no host (trabalhar em cópia);
* se não for possível fazer imagem completa naquele momento, registrar o motivo (tempo, espaço, autorização);
* manter evidências etiquetadas e acompanhadas de seus hashes.

---

## 6. Relação com outras pastas

* detalhes de **como** fazer imagem → `04_Ferramentas/01_Aquisicao/*`
* detalhes de **como** analisar a memória depois → `04_Ferramentas/04_Memoria/*`
* detalhes de **como** validar integridade → `04_Ferramentas/08_Utilitarios_de_Apoio/01_Hashes_...`
* fundamentos de cadeia de custódia → `01_Introducao/02_Cadeia_de_Custodia.md`

Este arquivo aqui é o “roteiro de campo” que aponta para os outros.