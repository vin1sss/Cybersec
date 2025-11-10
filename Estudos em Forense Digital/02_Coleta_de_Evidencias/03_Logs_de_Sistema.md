---
title: "Coleta de Logs de Sistema"
tags: [coleta, logs, evidencias, windows, linux, web, hash]
status: rascunho
---

# Coleta de Logs de Sistema

Este documento descreve **como extrair e preservar logs** de sistemas para uso forense, sem confundir com o documento de introdução a logs (que explica o que são e como analisar). Aqui o foco é: **onde estão, como copiar, como empacotar e como garantir integridade**.

A ideia é que, ao final da coleta, você tenha um **pacote de logs** (ex.: `.zip` ou `.tar.gz`) com hash calculado e dados de origem registrados.

---

## 1. Princípios gerais

1. **Coletar o mais rápido possível**  
   Logs são rotacionados e podem ser sobrescritos. Em incidentes ativos, colete antes de reiniciar ou atualizar.

2. **Preservar a forma original**  
   Sempre que possível, colete no formato nativo (ex.: `.evtx` no Windows). Evite copiar “prints” quando o próprio sistema permite exportar o arquivo bruto.

3. **Registrar contexto**  
   Horário do host, timezone, nome da máquina, IP e usuário logado devem ser anotados - isso é essencial para casar com outros logs.

4. **Empacotar e hashear**  
   Depois de copiar todos os logs necessários, gere um pacote único e calcule hash (SHA-256). O pacote será a evidência.

5. **Documentar fonte e caminho**  
   Para cada arquivo: de onde veio e qual o caminho original.

---

## 2. Fontes de log por sistema

### 2.1 Windows

O Windows guarda a maioria dos eventos em **Event Logs** (`.evtx`), acessíveis pelo **Event Viewer**.

Principais locais:

- `C:\Windows\System32\winevt\Logs\`
  - `System.evtx`
  - `Security.evtx`
  - `Application.evtx`
  - mais logs específicos (Setup, Microsoft-Windows-*, etc.)

**Formas de coletar:**

1. **Via Event Viewer (GUI)**  
   - Abrir o Event Viewer  
   - Botão direito no log desejado → **Save All Events As…**  
   - Salvar em `.evtx` em pasta temporária  
   - Repetir para `Security`, `System` e outros relevantes  
   - Copiar esses `.evtx` para a mídia de evidências

2. **Via linha de comando (wevtutil)**  
   ```cmd
   wevtutil epl Security C:\coleta\Security.evtx
   wevtutil epl System   C:\coleta\System.evtx
   wevtutil epl Application C:\coleta\Application.evtx
   ```

* `epl` = export log
* Gere em uma pasta que depois será copiada

3. **Outros logs Windows**

   * `C:\Windows\Logs\DISM\`
   * `C:\Windows\Logs\CBS\`
   * Logs de aplicações em `%ProgramData%` ou `%LOCALAPPDATA%`

**Coleta mínima Windows (incidente comum):**

* `Security.evtx`
* `System.evtx`
* `Application.evtx`
* logs específicos do serviço afetado (IIS, RDP, AD, etc.)

---

### 2.2 Linux/Unix

A maioria dos logs fica em `/var/log/`. Alguns arquivos importantes:

* `/var/log/syslog` ou `/var/log/messages` - eventos gerais do sistema
* `/var/log/auth.log` - logins, sudo, falhas de autenticação
* `/var/log/secure` - similar ao auth.log (dependendo da distro)
* `/var/log/kern.log` - kernel
* `/var/log/apache2/access.log` e `/var/log/apache2/error.log` - servidores Apache
* `/var/log/nginx/access.log` e `/var/log/nginx/error.log` - Nginx
* arquivos rotacionados: `.1`, `.2.gz`, etc.

**Coleta típica (Linux):**

```bash
mkdir -p /tmp/coleta-logs
cp /var/log/syslog /tmp/coleta-logs/ 2>/dev/null
cp /var/log/messages /tmp/coleta-logs/ 2>/dev/null
cp /var/log/auth.log /tmp/coleta-logs/ 2>/dev/null
cp /var/log/secure /tmp/coleta-logs/ 2>/dev/null
cp -r /var/log/apache2 /tmp/coleta-logs/ 2>/dev/null
cp -r /var/log/nginx /tmp/coleta-logs/ 2>/dev/null
```

Depois, empacotar:

```bash
cd /tmp
tar czf logs-host01-2025-11-05.tar.gz coleta-logs
```

E calcular o hash:

```bash
sha256sum logs-host01-2025-11-05.tar.gz > logs-host01-2025-11-05.tar.gz.sha256
```

---

### 2.3 Aplicações e serviços

Alguns serviços escrevem fora dos locais padrão (ex.: `/opt/app/logs`, `%ProgramData%\App\Logs`). Na coleta, vale:

* procurar por diretórios `log`, `logs`, `logging`;
* verificar configurações do serviço (ex.: `httpd.conf`, `nginx.conf`) para saber onde ele grava.

Registre o caminho original junto com o arquivo.

---

## 3. Rotação e período de interesse

Logs costumam ter rotação (`access.log`, `access.log.1`, `access.log.2.gz`). Para uma investigação você deve:

1. identificar a **janela de tempo** do incidente;
2. trazer **todos os arquivos da rotação** que cubram a janela;
3. manter o nome original (não renomear de forma que perca a ordem);
4. empacotar na ordem e só então comprimir.

Exemplo:

```bash
tar czf apache-logs-06jun2024.tar.gz \
  /var/log/apache2/access.log \
  /var/log/apache2/access.log.1 \
  /var/log/apache2/access.log.2.gz
```

---

## 4. Tempo e fuso

Para correlacionar com outros hosts e com a futura análise, registre:

* data/hora do host no momento da coleta;
* timezone configurado (ex.: America/Sao_Paulo);
* se o host estava ou não sincronizado por NTP.

Isso vai para o relatório de coleta. Pequenas diferenças de minuto entre hosts podem causar confusão na hora de montar a linha do tempo.

---

## 5. Empacotamento e hash

Depois de juntar tudo que vem de “logs de sistema”:

1. **Empacotar** (zip/tar.gz):

   ```bash
   tar czf LOGS-host01-2025-11-05.tar.gz /tmp/coleta-logs
   ```

   ou no Windows:

   * selecionar pasta → botão direito → Enviar para → Pasta compactada (.zip)

2. **Calcular hash** (preferencialmente SHA-256):

   ```bash
   sha256sum LOGS-host01-2025-11-05.tar.gz > LOGS-host01-2025-11-05.tar.gz.sha256
   ```

3. **Mover para mídia de evidência** (HD externo, pasta de caso) sem alterar o nome.

4. **Registrar no relatório**:

   * nome do arquivo;
   * hash;
   * host de origem;
   * coletor (seu nome);
   * data/hora.

---

## 6. Cadeia de custódia (resumo)

Mesmo logs sendo “só texto”, devem seguir a mesma lógica de outras evidências:

* identificar quem coletou;
* quando e de onde;
* como foram transferidos;
* onde estão armazenados agora.

Se os logs foram extraídos de ambiente em nuvem ou SIEM, anotar **quem exportou** e **qual filtro** foi aplicado (para não parecer que se perdeu evento no meio).

---

## 7. Coleta mínima recomendada

Se você precisar de algo rápido, sem pensar muito:

**Windows**

* Security.evtx
* System.evtx
* Application.evtx

**Linux**

* /var/log/syslog ou /var/log/messages
* /var/log/auth.log ou /var/log/secure
* pasta do serviço alvo (/var/log/apache2 ou /var/log/nginx)

Empacote, calcule o hash e anote o horário. Isso já deixa o caso “congelado” para análise futura.