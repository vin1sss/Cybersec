---
title: Event Logs do Windows (.evtx)
tags:
  - forense
  - windows
  - analise-de-sistemas
  - logs
  - evtx
status: concluido
---

# Event Logs do Windows (.evtx)

Os logs de eventos do Windows registram atividades do sistema operacional, aplicativos e segurança. Em forense, eles ajudam a montar linha do tempo, comprovar logon/logoff, criação de processo, falhas, instalação de software e até atividade suspeita de rede/remota.  
Esta nota descreve **onde ficam** os logs (`.evtx`), **quais são os principais**, **quais IDs são mais úteis** em laboratório/CTF e **como extrair/analisar** quando a imagem está montada em Linux.

---

## 1. Localização dos Logs

Local padrão em Windows:

- `C:\Windows\System32\winevt\Logs\`

Em imagem montada em Linux:

- `/mnt/img/Windows/System32/winevt/Logs/`

Nesse diretório você encontra vários arquivos `.evtx` - cada um corresponde a um log de origem diferente (Sistema, Aplicativo, Segurança, PowerShell, TerminalServices, etc.).

---

## 2. Principais Logs de Interesse

1. **System.evtx**
   - Eventos relacionados ao sistema operacional, drivers, inicialização, desligamento.
   - Útil para saber quando a máquina esteve ligada/desligada ou se houve falhas de serviço.

2. **Application.evtx**
   - Eventos gerados por aplicativos.
   - Útil para ver erros de programas, instalações, e algumas ações de software.

3. **Security.evtx**
   - O mais importante para auditoria.
   - Registra logon/logoff, uso de credenciais, criação de conta (quando auditado).
   - Em ambientes bem configurados, é o log que prova “quem fez o quê”.

4. **Microsoft-Windows-PowerShell/Operational** (pode aparecer com outro nome ou dentro de subpastas de logs)
   - Muito útil quando o atacante usou PowerShell.
   - Pode mostrar o comando que foi executado.

5. **TerminalServices / RemoteDesktop** (nomes variam)
   - Mostra conexões RDP, muito comum em incidentes.

---

## 3. IDs de Eventos Frequentes

Abaixo alguns IDs clássicos que costumam cair em CTF, laboratórios e investigação básica. (Os números podem variar conforme o Windows e a política de auditoria, mas são boas referências.)

### 3.1 Logon e Logoff (Security.evtx)
- **4624** – Logon bem-sucedido.
- **4625** – Tentativa de logon falhou.
- **4634** – Logoff.
- **4672** – Logon com privilégios especiais (admin).

Com esses eventos você verifica se houve acesso interativo, remoto ou via serviço.

### 3.2 Execução / Criação de Processo
- **4688** – Um novo processo foi criado.  
  Quando esse evento está habilitado, é excelente para ver comando malicioso, PowerShell, `cmd.exe`, `whoami`, etc.

### 3.3 Sistema e Inicialização (System.evtx)
- **6005** – O serviço de log foi iniciado (máquina ligando).
- **6006** – O serviço de log foi parado (máquina desligando).
- **6008** – Desligamento inesperado.

Isso ajuda a montar a linha do tempo: quando a máquina estava operacional para o ataque acontecer.

### 3.4 Política / Contas
Dependem de auditoria, mas podem mostrar criação/alteração de conta.

---

## 4. Extração e Conversão

Como `.evtx` é formato binário, você normalmente:

1. Copia os arquivos do diretório de logs da imagem montada;
2. Converte ou lê com ferramenta apropriada.

Exemplo de cópia em Linux:

```bash
# copiar todos os evtx para análise local
cp /mnt/img/Windows/System32/winevt/Logs/*.evtx ~/caso01/logs/
````

Ferramentas comuns:

* **EvtxECmd** (Eric Zimmermann) - converte .evtx em CSV/JSON.
* **Windows Event Viewer** (no próprio Windows) - para visualização rápida.
* **LogParser** - consultas mais avançadas.
* **chainsaw** - hunting rápido em cima de .evtx.

---

## 5. Leitura Focada (o que procurar)

Ao abrir os logs, procure por:

* Período do incidente (filtrar por data/hora);
* Eventos de logon (4624, 4625);
* Execução de processo (4688), se existir;
* Eventos de serviço iniciando algo inesperado;
* Logs de PowerShell, se o atacante tiver usado scripts.

É recomendável sempre montar uma **timeline** combinando:

* Event Logs
* Prefetch
* Registro (Run/Services)
* Artefatos de usuário

Assim você confirma que o binário estava no disco **e** foi executado **naquele horário**.

---

## 6. Correlação com Outros Artefatos

Os logs, sozinhos, mostram que algo aconteceu. Em forense a gente tenta **corroborar**:

* Se o log mostra processo `malware.exe` às 10:32 → verificar Prefetch de `MALWARE.EXE` no mesmo dia.
* Se o log mostra logon de usuário `aluno1` → verificar `C:\Users\aluno1\` e o `NTUSER.DAT` dele.
* Se o log mostra desligamento inesperado → investigar falha, possível queda durante ataque ou tentativa de limpar rastros.

---

## 7. Problemas Comuns

* **Logs apagados**: atacante pode limpar o Security.evtx. Mesmo assim, o próprio ato de limpar pode gerar evento e o tamanho do arquivo pode denunciar.
* **Logs sobrescritos**: em máquinas muito usadas, logs antigos somem. Por isso é importante copiar logo.
* **Fuso horário**: conferir o timezone da máquina. Em timeline misturando Linux + Windows + SIEM, alinhar UTC.

---

## 8. Checklist de Event Logs

1. Listar todos os `.evtx` em `Windows/System32/winevt/Logs/`.
2. Copiar e preservar cópia original.
3. Converter os mais importantes (System, Application, Security).
4. Filtrar por data/hora do incidente.
5. Buscar IDs 4624, 4625, 4688, 6005, 6006.
6. Correlacionar com artefatos de execução e Registro.
7. Registrar achados na nota do caso.

---

## 9. Conclusão

Os Event Logs são a principal fonte de **linha do tempo** em Windows. Eles mostram quando a máquina estava ativa, quem entrou, se houve falhas de autenticação e, em ambientes auditados, até que processo rodou. Coletá-los cedo, converter para formato legível e cruzar com outros artefatos é o caminho para uma narrativa forense sólida.