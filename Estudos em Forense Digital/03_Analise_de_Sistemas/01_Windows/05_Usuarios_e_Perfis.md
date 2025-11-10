---
title: Usuários e Perfis no Windows
tags:
  - forense
  - windows
  - analise-de-sistemas
  - usuarios
  - perfis
status: concluido
---

# Usuários e Perfis no Windows

Grande parte da evidência em Windows está ligada ao **usuário que fez a ação**: quem baixou o arquivo, quem abriu o documento, quem executou o malware, quem usou o PowerShell. Por isso, além de analisar os hives de máquina e os logs globais, é necessário olhar para cada **perfil de usuário** em `C:\Users\`.  
Esta nota mostra como identificar usuários, onde ficam seus dados, quais arquivos de perfil são forensemente relevantes e como correlacionar isso com o Registro e com os Event Logs.

---

## 1. Objetivo

- Mapear todos os usuários existentes na máquina analisada;
- Identificar quais perfis realmente foram usados;
- Localizar o `NTUSER.DAT` de cada um;
- Listar pastas onde costumam aparecer artefatos de interesse (Downloads, Desktop, AppData);
- Diferenciar perfil global (`Public`) de perfil individual.

---

## 2. Localização dos Perfis

Em Windows moderno (7+):

- `C:\Users\`  
  - `C:\Users\Administrador\`
  - `C:\Users\<NOME_USUARIO>\`
  - `C:\Users\Public\`

Em imagem montada em Linux:

- `/mnt/img/Users/`

> Em versões antigas, pode aparecer `C:\Documents and Settings\`. O raciocínio é o mesmo: uma pasta por usuário.

---

## 3. Identificando Usuários Relevantes

Ao listar `C:\Users\`, você pode encontrar:

- contas reais de usuário (ex.: `vinicius`, `aluno`, `adm-lab`);
- contas do sistema ou padrão (`Default`, `Public`);
- contas de domínio com sufixo (`NOME-PC\usuario` pode virar pasta diferente).

**Passo prático:**

```bash
ls -l /mnt/img/Users
````

A partir dessa lista, escolha os perfis que têm data de modificação compatível com o incidente e/ou que pareçam perfis usados por pessoas (nomes reais, “aluno1”, “labuser”, etc.).

---

## 4. Arquivos de Perfil Mais Importantes

### 4.1 NTUSER.DAT

* Caminho: `C:\Users\<USUARIO>\NTUSER.DAT`
* Representa o Registro **do usuário** (HKCU).
* Guarda MRUs, configurações de aplicativos, caminhos recentes, às vezes plugins e credenciais de apps.
* Deve ser extraído para análise offline (regipy, Registry Explorer).

### 4.2 UsrClass.dat

* Caminho comum: `C:\Users\<USUARIO>\AppData\Local\Microsoft\Windows\UsrClass.dat`
* Armazena informações de Shell (ex.: ShellBags).
* Útil para saber que pastas o usuário abriu.

### 4.3 AppData

* `C:\Users\<USUARIO>\AppData\Local\`
* `C:\Users\<USUARIO>\AppData\Roaming\`
* Aqui vivem muitos artefatos de aplicativos: navegadores, mensageria, PowerShell, histórico de RDP, etc.

---

## 5. Pastas de Interesse por Usuário

* **Desktop**
  `C:\Users\<USUARIO>\Desktop\`
  Scripts, atalhos `.lnk`, ferramentas baixadas e deixadas “à mão”.

* **Downloads**
  `C:\Users\<USUARIO>\Downloads\`
  Muito recorrente em CTF/lab: o binário malicioso vem para cá.

* **Documents / Pictures**
  Podem guardar dados exfiltrados ou itens que o usuário estava manipulando.

* **Startup por usuário**
  `C:\Users\<USUARIO>\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\`
  Qualquer coisa aqui pode estar sendo executada no logon daquele usuário.

---

## 6. Perfis Especiais

* **Public** (`C:\Users\Public\`): usado para compartilhar entre usuários. Atacante pode deixar ferramentas aqui.
* **Default**: modelo de novo usuário. Menos relevante para “quem fez”, mas bom saber que existe.
* **Perfis órfãos**: às vezes há pasta de usuário que não aparece mais nas contas ativas - ainda assim pode ter artefato útil.

---

## 7. Correlação Usuário × Registro × Logs

Para dizer que “o usuário X rodou Y”, tente sempre ter 2-3 fontes:

1. **Do perfil**: atalho `.lnk`, Recent, Jump List em `C:\Users\<USUARIO>\AppData\Roaming\...`
2. **Do Registro do usuário**: entradas no `NTUSER.DAT` dele.
3. **Do log**: Event Logs mostrando logon (4624) com aquele usuário no horário.

Se o caminho do malware está dentro do perfil (ex.: `C:\Users\aluno\AppData\Roaming\svchost.exe`), isso já é um indicativo forte de que foi esse usuário ou esse contexto que instalou/rodou.

---

## 8. Timeline por Usuário

Uma forma organizada de trabalhar:

1. Enumerar perfis.
2. Para cada perfil:

   * copiar `NTUSER.DAT`;
   * copiar pasta `Recent`;
   * copiar histórico de PowerShell (se houver);
   * listar Desktop e Downloads.
3. Anotar timestamps mais recentes.
4. Cruzar com Event Logs do mesmo horário.

Isso te dá uma linha de tempo por usuário, não só por máquina.

---

## 9. Comandos Úteis (imagem montada)

```bash
# listar usuários
ls -l /mnt/img/Users

# ver conteúdo do perfil "aluno"
ls -l /mnt/img/Users/aluno

# copiar NTUSER.DAT para análise
cp /mnt/img/Users/aluno/NTUSER.DAT ~/caso01/hives/aluno_NTUSER.DAT
```

---

## 10. Conclusão

Analisar apenas os hives de sistema e os logs globais pode esconder **quem** realmente fez a ação. Ao olhar cada perfil em `C:\Users\`, você encontra o contexto completo do usuário: arquivos recentes, histórico de aplicativos, inicializações no logon e o próprio Registro dele (`NTUSER.DAT`). Essa visão por usuário é essencial em investigações corporativas e ajuda muito em CTFs que simulam “usuário interno comprometido”.