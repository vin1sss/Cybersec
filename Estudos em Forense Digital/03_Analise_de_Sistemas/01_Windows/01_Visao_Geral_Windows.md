---
title: Visão Geral de Análise em Windows
tags:
  - forense
  - windows
  - analise-de-sistemas
  - artefatos
  - estrutura
status: concluido
---

# Visão Geral de Análise em Windows

A análise forense em sistemas Windows exige conhecer onde o sistema guarda configurações, contas, logs e rastros de execução. Esta nota serve como porta de entrada para as demais notas de Windows dentro de `03_Analise_de_Sistemas` e aponta os diretórios, hives e arquivos que mais aparecem em laboratório, CTF e incidentes reais. As notas de detalhe (Registro, Event Logs, Artefatos de Execução) devem ser consultadas em seguida.

---

## 1. Contexto e Objetivo

O Windows concentra evidências em três lugares principais:

1. **Arquivos de sistema** (hives, logs, prefetch);
2. **Perfis de usuários** (NTUSER.DAT, AppData, Desktop);
3. **Locais de inicialização/persistência**.

O objetivo desta nota é listar esses pontos e mostrar o “caminho do disco” típico, tanto quando você está na própria máquina, quanto quando montou a imagem em Linux e está navegando via `/mnt/img/...`.

---

## 2. Estrutura Geral de Diretórios

Em uma instalação padrão de Windows 10/11:

- `C:\Windows\` - diretórios e binários de sistema;
- `C:\Windows\System32\` - executáveis e DLLs principais;
- `C:\Windows\System32\config\` - **hives de registro de máquina** (HKLM);
- `C:\Users\` - perfis de usuário (um por conta criada).

Em ambiente forense montado em Linux, o mesmo conteúdo costuma aparecer assim:

- `/mnt/img/Windows/`
- `/mnt/img/Windows/System32/config/`
- `/mnt/img/Users/`

> Em versões mais antigas, pode aparecer `/mnt/img/Documents and Settings/`.

---

## 3. Hives de Registro (Visão Rápida)

> Detalhamento completo em: **03_Analise_de_Sistemas/Windows/02_Registro_Hives.md**

Os hives de máquina ficam em:

- `C:\Windows\System32\config\SAM`
- `C:\Windows\System32\config\SYSTEM`
- `C:\Windows\System32\config\SOFTWARE`
- `C:\Windows\System32\config\SECURITY`
- `C:\Windows\System32\config\DEFAULT`

Funções principais:

- **SYSTEM** - infos de boot e control sets;
- **SOFTWARE** - programas instalados, chaves da Microsoft;
- **SAM** - contas locais;
- **SECURITY** - políticas de segurança.

O hive de usuário fica no perfil:

- `C:\Users\<USUARIO>\NTUSER.DAT` → representa o **HKCU** daquele usuário.

Por isso, em imagens com vários usuários, você tem vários NTUSER.DAT para inspecionar.

---

## 4. Perfis de Usuário

Cada pasta em `C:\Users\` representa um contexto de usuário e costuma guardar:

- documentos e downloads do suspeito;
- atalhos e scripts na área de trabalho;
- dados de aplicativos em `AppData`;
- o hive `NTUSER.DAT`.

Caminhos importantes:

- `C:\Users\<USUARIO>\Desktop\`
- `C:\Users\<USUARIO>\Downloads\`
- `C:\Users\<USUARIO>\Documents\`
- `C:\Users\<USUARIO>\AppData\Local\`
- `C:\Users\<USUARIO>\AppData\Roaming\`

A partir daí você encontra histórico de navegadores, mensageria, configs de ferramentas e, às vezes, o próprio binário malicioso.

---

## 5. Logs do Windows

> Detalhamento completo em: **03_Analise_de_Sistemas/Windows/03_EventLogs.md**

Local padrão:

- `C:\Windows\System32\winevt\Logs\`

Principais arquivos `.evtx`:

- `System.evtx`
- `Application.evtx`
- `Security.evtx`

Outros logs podem surgir conforme a função: PowerShell, RemoteDesktop, Firewall. Em análise de incidente vale sempre extrair todos os `.evtx` desse diretório.

---

## 6. Artefatos de Execução

> Detalhamento completo em: **03_Analise_de_Sistemas/Windows/04_Artefatos_de_Execucao.md**

Principais pontos:

- **Prefetch**: `C:\Windows\Prefetch\` - indica programas que rodaram.
- **Recent/Jumplists**: dentro de `C:\Users\<USUARIO>\AppData\Roaming\Microsoft\Windows\Recent\` e subpastas - mostram arquivos e apps usados recentemente.
- **AppCompat/ ShimCache**: registros no Windows que ajudam a provar que algo foi executado.

Esses artefatos são essenciais quando o malware já foi apagado do disco, mas o rastro de execução ficou.

---

## 7. Locais de Inicialização e Persistência

Dois grupos principais:

**Global (todos os usuários)**
- `C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\`

**Por usuário**
- `C:\Users\<USUARIO>\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\`

E, no Registro (ver próxima nota), chaves como:

- `HKLM\Software\Microsoft\Windows\CurrentVersion\Run`
- `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`

Malwares simples e scripts de laboratório costumam usar exatamente esses lugares.

---

## 8. Checklist Rápido de Análise

1. Enumerar usuários em `C:\Users\`.
2. Copiar hives de sistema em `C:\Windows\System32\config\`.
3. Copiar `NTUSER.DAT` dos usuários principais.
4. Extrair `.evtx` de `C:\Windows\System32\winevt\Logs\`.
5. Verificar `C:\Windows\Prefetch\`.
6. Verificar pastas de Startup (global e por usuário).
7. Procurar executáveis fora do padrão (Downloads, Temp, Desktop).

Esse checklist funciona bem para CTF, para laboratório e para responder a incidente interno.

---

## 9. Comandos Úteis em Ambiente Montado

Quando a imagem estiver montada em Linux:

```bash
# montar a imagem em somente leitura
sudo mount -o loop,ro disk.img /mnt/img

# ver hives de sistema
ls -l /mnt/img/Windows/System32/config

# ver usuários
ls -l /mnt/img/Users
````

A partir daí você aponta as ferramentas forenses (regipy, regripper, evtx_dump) para esses caminhos.

---

## 10. Conclusão

Esta nota organiza “onde olhar” em Windows antes de sair extraindo dado sem critério. As próximas notas quebram cada fonte de evidência em detalhes (chaves de registro de persistência, logs com IDs relevantes, e artefatos que provam execução). Mantendo esse padrão, toda análise de Windows dentro do vault fica previsível e reaproveitável.