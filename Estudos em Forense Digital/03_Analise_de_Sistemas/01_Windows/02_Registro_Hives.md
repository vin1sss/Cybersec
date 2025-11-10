---
title: Registro do Windows e Hives principais
tags:
  - forense
  - windows
  - analise-de-sistemas
  - registro
  - persistencia
status: concluido
---

# Registro do Windows e Hives Principais

O Registro do Windows é uma base de dados hierárquica que armazena configurações do sistema, dos aplicativos e dos usuários. Em forense, ele é valioso porque guarda rastros de **programas instalados**, **dispositivos conectados**, **usuários**, **execuções recentes** e, principalmente, **mecanismos de persistência** usados por malware e scripts.  
Esta nota descreve onde ficam os hives na imagem, quais são os principais, como diferenciar hives de máquina e de usuário e quais caminhos devem ser inspecionados primeiro.

---

## 1. Conceitos Básicos

**Hive**: arquivo físico no disco que corresponde a uma parte do Registro.  
**HKLM (HKEY_LOCAL_MACHINE)**: configurações da máquina inteira.  
**HKCU (HKEY_CURRENT_USER)**: configurações do usuário logado (vem do NTUSER.DAT dele).  
**Chaves de persistência**: locais do Registro que mandam o Windows rodar algo na inicialização ou no logon.

Em análise forense offline, você não abre o “regedit” da máquina vítima; você **lê o arquivo de hive** extraído da imagem.

---

## 2. Localização dos Hives no Disco

Em uma imagem montada, os hives de máquina ficam em:

- `C:\Windows\System32\config\`  
  - `SAM`
  - `SYSTEM`
  - `SOFTWARE`
  - `SECURITY`
  - `DEFAULT`

Em Linux, após montar a imagem, o caminho típico é:

- `/mnt/img/Windows/System32/config/`

Já os hives de **usuário** ficam dentro do perfil:

- `C:\Users\<USUARIO>\NTUSER.DAT`
- (às vezes) `C:\Users\<USUARIO>\AppData\Local\Microsoft\Windows\UsrClass.dat` (associações de arquivo, ShellBags, etc.)

---

## 3. Principais Hives de Máquina

### 3.1 SYSTEM
- Guarda informações de inicialização, control sets e serviços.
- Caminho lógico: `HKEY_LOCAL_MACHINE\SYSTEM`
- Útil para:
  - ver serviços instalados;
  - ver dispositivos;
  - ver time zone.

### 3.2 SOFTWARE
- Guarda informações de software instalado e configurações globais.
- Caminho lógico: `HKEY_LOCAL_MACHINE\SOFTWARE`
- Útil para:
  - identificar programas instalados;
  - caminhos de instalação;
  - chaves de produtos Microsoft.

### 3.3 SAM
- Guarda contas locais de usuário.
- Caminho lógico: `HKEY_LOCAL_MACHINE\SAM`
- Em forense, costuma ser combinado com `SYSTEM` para extrair hashes.

### 3.4 SECURITY
- Guarda políticas de segurança.
- Menos usado em CTF, mas importante em incidentes reais.

### 3.5 DEFAULT
- Configurações padrão do usuário padrão.

---

## 4. Hive de Usuário (HKCU)

Cada usuário tem o seu:

- `C:\Users\<USUARIO>\NTUSER.DAT`

Quando esse arquivo é carregado, ele aparece como:

- `HKEY_USERS\<SID_DO_USUARIO>\`
- e como atalho: `HKEY_CURRENT_USER\` (HKCU)

Por isso, para descobrir o que **um usuário específico** executou, configurou ou deixou em MRU (Most Recently Used), você precisa pegar o **NTUSER.DAT** dele, não só os hives de máquina.

---

## 5. Chaves Clássicas de Persistência

Estas são as primeiras chaves que um analista olha quando suspeita de malware simples:

1. **Logon do usuário**
   - `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`
   - `HKCU\Software\Microsoft\Windows\CurrentVersion\RunOnce`

2. **Para todos os usuários**
   - `HKLM\Software\Microsoft\Windows\CurrentVersion\Run`
   - `HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce`

3. **Variantes em Wow6432Node** (máquinas 64 bits):
   - `HKLM\Software\WOW6432Node\Microsoft\Windows\CurrentVersion\Run`

4. **Serviços e drivers** (mais discretos):
   - `HKLM\SYSTEM\CurrentControlSet\Services\`

Nessas chaves você costuma encontrar **nome do valor** e **caminho do executável**. Em forense, você registra os dois.

---

## 6. Serviços (CurrentControlSet)

Dentro do hive `SYSTEM` há normalmente:

- `HKLM\SYSTEM\CurrentControlSet\`
- ou vários `ControlSet001`, `ControlSet002`, etc.

O Windows usa o `CurrentControlSet` como o conjunto ativo. Em:

- `HKLM\SYSTEM\CurrentControlSet\Services\`

você encontra serviços legítimos e, às vezes, serviços criados por malware. Cada serviço tem subchaves com:

- `ImagePath` → aponta para o executável
- `Start` → modo de inicialização
- `Type` → tipo de serviço

Se o caminho estiver estranho (ex.: aponta para `C:\Users\<USUARIO>\AppData\Local\Temp\algo.exe`), é um indicador de comprometimento.

---

## 7. Ferramentas e Leitura Offline

Como o Registro está em arquivos binários, você usa ferramentas que “montam” o hive:

- **regipy / regrippy** (Python) - muito usadas em Linux
- **Registry Explorer** (Eric Zimmermann) - GUI para examinar hives
- **chntpw / reged** - também leem hives offline

Exemplo de leitura rápida em Python (regipy) depois de montar a imagem:

```python
from regipy import RegistryHive

hive = RegistryHive('/mnt/img/Windows/System32/config/SOFTWARE')
key = hive.get_key('Microsoft\\Windows\\CurrentVersion\\Run')
for val in key.values:
    print(val.name, val.value)
````

Isso permite listar rapidamente programas que iniciam com o Windows.

---

## 8. Itens de Interesse por Tipo de Caso

* **Persistência / malware simples:**

  * `HKLM/HKCU ...\Run`
  * Pastas de Startup (fora do Registro, mas correlatas)
  * Serviços em `CurrentControlSet\Services`
* **Uso de programas / histórico:**

  * MRUs e listas recentes em `NTUSER.DAT`
  * Associações de arquivo em `UsrClass.dat`
* **Contas e logon:**

  * `SAM` + `SYSTEM`
* **Configuração de rede / firewall:**

  * várias chaves dentro de `HKLM\SYSTEM\CurrentControlSet\Services\...`

---

## 9. Checklist de Registro

1. Extrair todos os hives de `/Windows/System32/config/`.
2. Extrair `NTUSER.DAT` de **cada** usuário relevante em `/Users/...`.
3. Carregar `SOFTWARE` e checar `...\CurrentVersion\Run`.
4. Carregar `SYSTEM` e checar `CurrentControlSet\Services`.
5. Registrar nome do valor, tipo e caminho completo do executável.
6. Correlacionar com:

   * Prefetch do mesmo executável;
   * Logs de segurança com criação de processo;
   * Arquivo físico encontrado no disco.

---

## 10. Conclusão

O Registro é uma das fontes mais ricas de evidência em Windows porque mostra **o que deveria rodar** (persistência) e **o que foi configurado** (software, usuários). Em ambiente forense, trabalhar com os hives offline garante que você não altera o original e ainda consegue automatizar a extração de chaves suspeitas. As próximas notas detalham os logs (`.evtx`) e os artefatos de execução para fechar a correlação.