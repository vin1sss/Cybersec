---
title: "Modelo de Relatório Simples"
tags: [diversos, relatorio, laudo, dfir, documentacao]
status: rascunho
---

# Modelo de Relatório Simples

Este modelo serve para registrar os resultados de uma **aquisição** ou **análise forense** feita seguindo os procedimentos deste repositório. Ele não é um laudo jurídico completo, mas já traz os campos mínimos para mostrar:

- o que você recebeu,
- o que você fez,
- com que ferramenta,
- o que encontrou,
- e quais arquivos (com hash) estão sendo entregues.

---

## 1. Identificação do caso

- **Título do caso:**  
  (ex.: Incidente em estação WIN10-SAC-01)

- **ID interno:**  
  (ex.: INC-2025-11-07-01)

- **Data da análise:**  
  (ex.: 2025-11-07)

- **Analista responsável:**  
  (nome completo)

- **Origem da demanda:**  
  (ex.: SOC, Jurídico, Auditoria, Equipe de Segurança)

---

## 2. Escopo

Descreva **o que estava dentro** e **o que estava fora** da análise.

**Dentro do escopo:**
- análise de imagem de disco do host `WIN10-SAC-01`;
- verificação de indícios de execução de malware;
- extração de logs de eventos Windows.

**Fora do escopo:**
- análise de dispositivos móveis;
- engenharia reversa detalhada de binários;
- análise de e-mail em servidor externo.

> Ter escopo ajuda a justificar por que algo não foi analisado.

---

## 3. Evidências recebidas

Liste **todas** as evidências brutas que você usou, com hash.

| ID | Descrição | Arquivo / Mídia | Hash (SHA-256) | Local de armazenamento |
|----|-----------|-----------------|----------------|-------------------------|
| EV-001 | Imagem de disco do host WIN10-SAC-01 | `2025-11-07_WIN10-SAC-01_disk.E01` | `...` | `\\servidor\casos\...\01_brutos\` |
| EV-002 | Dump de memória do host WIN10-SAC-01 | `2025-11-07_WIN10-SAC-01_memdump.raw` | `...` | `\\servidor\casos\...\01_brutos\` |

Se a evidência veio por HD externo, anote número de série e etiqueta.

---

## 4. Procedimentos executados

Conte **o que você fez**, em ordem cronológica. Quanto mais específico, mais fácil repetir.

Exemplo:

1. **2025-11-07 15:05 -03** – Montagem da imagem `2025-11-07_WIN10-SAC-01_disk.E01` em estação LAB-02 (read-only).
2. **2025-11-07 15:20 -03** – Extração de logs de eventos com EvtxECmd.
3. **2025-11-07 15:40 -03** – Execução do Volatility3 sobre o dump de memória.
4. **2025-11-07 16:10 -03** – Criação de timeline com `log2timeline.py` (plaso).
5. **2025-11-07 16:30 -03** – Correlação de artefatos de execução do Windows (prefetch + RecentDocs).

---

## 5. Ferramentas e versões

Sempre liste a versão. Ferramenta muda resultado.

- FTK Imager 4.x
- dc3dd 7.2.646
- Volatility3 1.0.1
- plaso/log2timeline 20241001
- Chainsaw 2.6.0
- Autopsy 4.x (se usado)

Se houver uso de IA/ML (triagem, priorização), registrar aqui também:
- classificador de logs (scikit-learn 1.x) – usado apenas para priorização

---

## 6. Achados principais

Aqui vai o **resumo técnico** do que você encontrou. Use tópicos.

- Foram identificados artefatos de execução de `malware.exe` em `C:\Users\...\AppData\...` (prefetch + RecentFileCache).
- Logs do Windows (Security.evtx) mostram autenticação bem-sucedida do usuário `admin` às **2025-11-07 13:12:54 -03** a partir do IP interno `10.0.0.5`.
- No dump de memória há processo suspeito `@WanaDecryptor@` (PID 740) semelhante a ransomware conhecido.
- Não foram encontrados indícios de exfiltração por navegador.
- Houve tentativa de persistência via chave de Registro `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`.

Se alguma evidência esperada **não** foi encontrada, anote (“não havia MFT”, “log estava corrompido”, “imagem parcial”).

---

## 7. Análise / Interpretação

Aqui você pode conectar os pontos:

- A ordem dos artefatos sugere que o usuário estava logado quando o binário foi executado.
- O horário de execução é compatível com o alerta do SOC.
- O processo malicioso não parece ter se mantido após o reboot (sem serviço/persistência sólida).
- A presença do dump de memória e da imagem íntegra permite aprofundar, se necessário.

Se algo depender de outro time (rede, AD), indique.

---

## 8. Conclusão

Escreva em linguagem direta:

> “Com base nas evidências analisadas, é possível afirmar que houve execução de um binário suspeito no host WIN10-SAC-01 no dia 2025-11-07 por volta de 13:12:54 -03, sob contexto de usuário autenticado. Não foram identificados, nesta análise, indícios claros de exfiltração de dados. Recomenda-se a correção das chaves de inicialização e varredura adicional no segmento de rede.”

Se a conclusão for limitada (“imagem incompleta”, “logs ausentes”), diga.

---

## 9. Arquivos gerados (saídas)

Liste tudo que você criou e que está sendo entregue:

| Arquivo | Descrição | Pasta |
|---------|-----------|-------|
| `vol_pslist.txt` | Lista de processos do dump de memória | `02_processados/` |
| `timeline.plaso` | Timeline bruta para Timesketch | `02_processados/` |
| `chainsaw_findings.csv` | Eventos Windows marcados como suspeitos | `02_processados/` |
| `relatorio_preliminar.md` | Este relatório | `03_relatorios/` |

Isso ajuda quem for importar no Autopsy/Timesketch depois.

---

## 10. Limitações

- imagem recebida já era parcial (1,1 GB);
- logs do proxy não estavam disponíveis;
- host já havia sido reiniciado antes da coleta (RAM perdida).

Limitações bem descritas protegem a análise.

---

## 11. Assinatura

- **Analista:** ______________________
- **Data:** __________________________
- **Contato:** _______________________

---

## Observação

Se o relatório for virar documento externo (cliente, jurídico), você pode ajustar o tom (menos comando, mais descrição). Para uso interno de laboratório, este formato técnico é suficiente.