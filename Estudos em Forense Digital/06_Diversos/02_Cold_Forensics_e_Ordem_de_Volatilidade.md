---
title: "Cold Forensics e Ordem de Volatilidade"
tags: [diversos, cold-forensics, ordem-de-volatilidade, coleta, cadeia-de-custodia]
status: rascunho
---

# Cold Forensics e Ordem de Volatilidade

Este documento explica **quando e por que** fazer análise de sistema desligado (cold) em vez de análise ao vivo (live), e registra a **ordem de volatilidade** para você saber o que deve ser coletado primeiro quando houver risco de perda de dados. Ele também amarra com aquisição de disco, write blocker e cadeia de custódia.

A ideia é: **quanto menos você mexe no sistema ligado, mais íntegra fica a evidência** — mas você pode perder coisas voláteis. Então precisa saber escolher.

---

## 1. Live x Cold: o que muda?

| Aspecto                    | Live forensics (sistema ligado)                                 | Cold forensics (sistema desligado / imagem)                         |
|---------------------------|------------------------------------------------------------------|---------------------------------------------------------------------|
| Estado do sistema         | Em uso, processos rodando                                        | Congelado, sem mudanças                                             |
| Evidência volátil         | Dá pra pegar (RAM, conexões, chaves)                            | Normalmente já perdeu                                               |
| Risco de alterar dados    | Maior (toda ação grava algo)                                     | Mínimo (trabalha-se na cópia)                                       |
| Adequação legal           | Pode ser questionado                                             | Muito boa (integridade mais fácil de demonstrar)                    |
| Cobertura de disco        | Limitada                                                         | Completa (bit a bit)                                                |
| Tempo de atuação          | Mais rápido para achar o básico                                  | Mais demorado (imagem, validação, análise)                          |
| Acesso a arquivos cifrados| Melhor (se o volume estiver montado)                             | Pode perder o acesso se desligar                                    |

**Resumo:**  
- se você **precisa muito da RAM** → faça live antes;  
- se você **precisa muito de integridade** (caso jurídico, ambiente hostil) → desligue e faça cold.

---

## 2. Quando preferir cold forensics

Use cold (desligado / imagem) quando:

1. **Integridade é prioridade**  
   Casos que vão para processo, auditoria, compliance: é melhor ter uma imagem estática e hash do que explicar por que você rodou 20 comandos em produção.

2. **Você pode desligar a máquina**  
   Servidor não crítico, estação de trabalho, notebook apreendido.

3. **Você quer o disco inteiro**  
   Recuperação de arquivos apagados, file carving, análise de espaço não alocado, partições ocultas.

4. **Ambiente é instável ou legado**  
   Máquina velha, sistema que quebra se você rodar ferramenta live, appliance desconhecido.

5. **Precisa de cadeia de custódia clara**  
   “Desliguei, removi o disco, imageei com write blocker, calculei hash.” Fica limpo.

Se **não** puder desligar (serviço crítico, VM de cliente, cloud rodando), faz live primeiro e depois tenta fazer uma imagem de snapshot / disco anexado.

---

## 3. Ordem de volatilidade

A **ordem de volatilidade** diz: “colete o que some mais rápido primeiro”.

Ordem típica (do mais volátil para o menos volátil):

1. **CPU, registradores, cache**  
   Praticamente impossível de pegar fora de contexto — em geral só em ferramentas muito específicas e live.

2. **RAM (memória)**  
   Processos, conexões, chaves de criptografia, código injetado. Some ao desligar.

3. **Tabelas e estados de rede do sistema**  
   ARP, rotas, conexões ativas.

4. **Sistemas de arquivos temporários**  
   `/tmp`, caches, spool, sessões.

5. **Disco local (sistema de arquivos)**  
   Arquivos, logs, MFT, Registry, artefatos de usuário.

6. **Logs remotos / SIEM / monitoramento**  
   Normalmente mais estáveis.

7. **Backups e mídias de arquivo**  
   Ficam por último.

**O conflito clássico:** se você desligar para fazer cold, você perde 1–4, mas ganha um 5 perfeito. Então a decisão tem que ser consciente.

---

## 4. Aquisição em cold (desligado)

Quando já decidiu que vai fazer cold:

1. **Isolar o equipamento**  
   Nada de deixar ele ligando e desligando sozinho, nada de atualizar.

2. **Remover o disco / anexar mídia**  
   Se for máquina física, retirar o disco e levar para a estação forense. Se for VM, exportar / snapshot.

3. **Usar write blocker** (hardware de preferência)  
   Para garantir que o sistema host não vai escrever nada no disco de evidência.

4. **Fazer imagem bit a bit**  
   RAW, E01, AFF — o que o laboratório usar. (ver `02_Coleta_de_Evidencias/01_Imagem_de_Disco.md` e o complemento em `06_Diversos/03_Imaging_no_Linux_com_dc3dd.md`)

5. **Calcular e registrar hash**  
   SHA-256 da mídia original (se a ferramenta permitir) e da imagem gerada.

6. **Documentar**  
   data/hora, operador, série do disco, ferramenta, destino da imagem.

Assim, mesmo tendo desligado e “perdido” a RAM, você tem uma evidência muito forte de disco.

---

## 5. Desafios do cold

- **Perda de acesso a volume cifrado**: se o disco estava montado porque o usuário estava logado e você desligou, pode perder a senha/chave que estava na RAM.
- **Tempo e tamanho**: imagem completa de disco grande demora e ocupa espaço.
- **Dispositivos móveis / IoT**: às vezes não dá para “desligar e imagear”, aí tem que usar técnicas próprias (chip-off, JTAG), mas o princípio de preservar continua.

Por isso, em incidentes reais, muita equipe faz um **modelo híbrido**:
1. coleta live do mínimo volátil (RAM, conexões, processos);
2. depois coleta cold (imagem de disco).

---

## 6. Cadeia de custódia em cold

Mesmo em cold, **documentar tudo**:

- quem desligou;
- quem retirou o disco;
- quem imageou;
- hash calculado;
- onde foi guardado.

Isso casa com o que está em `05_Procedimentos/06_Documentacao_e_Cadeia_de_Custodia.md`.

---

## 7. Relacionados neste repositório

- Aquisição de disco (passo a passo): `02_Coleta_de_Evidencias/01_Imagem_de_Disco.md`
- Imaging em Linux com exemplo: `06_Diversos/03_Imaging_no_Linux_com_dc3dd.md`
- Documentação e cadeia: `05_Procedimentos/06_Documentacao_e_Cadeia_de_Custodia.md`

Assim você consegue explicar por que **escolheu** cold e mostrar que seguiu uma ordem racional de coleta.