---
title: "Fundamentos de Logs para Investigação"
tags: [forense, logs, windows, web, incident-response, analise]
status: concluido
---

# Fundamentos de Logs

Logs são os **rastros digitais** deixados por sistemas operacionais, aplicações, serviços e dispositivos de rede. Em uma investigação ou resposta a incidentes, eles cumprem o mesmo papel que pegadas, marcas de porta arrombada ou gravações de CFTV em uma cena física: permitem **reconstruir o que aconteceu, quando, de onde e com qual usuário/dispositivo**.

Atacantes costumam tentar reduzir sua própria pegada, mas dificilmente conseguem eliminar todos os registros. Por isso, conhecer **onde os logs ficam, quais tipos existem e como analisá-los** é essencial para a forense digital.

Este documento apresenta:
- conceitos básicos de logs;
- principais tipos de logs;
- análise de **Windows Event Logs**;
- análise de **logs de acesso de servidores web**;
- comandos práticos para análise manual.

---

## 1. Por que logs são importantes

Logs são úteis em pelo menos cinco frentes:

1. **Monitoramento de eventos de segurança**  
   Permitem detectar comportamentos anômalos (ex.: muitas tentativas de login, criação de contas suspeitas, acessos fora de horário).

2. **Investigação e forense de incidentes**  
   São a trilha do que realmente aconteceu. Combinando diferentes logs, chega-se ao passo a passo do ataque.

3. **Troubleshooting**  
   Erros de sistema e de aplicação são registrados em log, o que permite diagnosticar falhas.

4. **Monitoramento de desempenho**  
   Aplicações costumam registrar tempo de resposta, falhas e sobrecarga.

5. **Auditoria e compliance**  
   Algumas normas exigem registro e retenção de logs para demonstrar quem fez o quê e quando.

De forma geral: **a maioria dos rastros de ataque em um sistema digital será encontrada nos logs** - desde que eles existam, estejam habilitados e não tenham sido apagados.

---

## 2. Tipos de logs

Como cada sistema registra muitos eventos de naturezas diferentes, os logs são normalmente **segmentados por categoria**. Isso facilita a investigação: em vez de ler tudo, o analista procura diretamente o log que contém o fato de interesse.

### 2.1 System Logs
- Foco: funcionamento do sistema operacional.
- Exemplos: inicialização e desligamento, carregamento de drivers, erros de hardware, falhas de serviço.
- Uso: diagnóstico de problemas de OS e identificação de indisponibilidades.

### 2.2 Security Logs
- Foco: eventos de segurança e controle de acesso.
- Exemplos: autenticação (logon/logoff), autorização, alteração de política de segurança, criação/alteração de contas, atividades anormais.
- Uso: investigação de acessos não autorizados, força bruta, criação de usuário malicioso.

### 2.3 Application Logs
- Foco: atividades internas de uma aplicação específica.
- Exemplos: erros da aplicação, interações de usuário, atualizações, falhas de módulo.
- Uso: troubleshooting de app e correlação com incidentes gerados pela aplicação.

### 2.4 Audit Logs
- Foco: rastreabilidade de mudanças e acessos.
- Exemplos: acesso a dados, alteração de configuração, atividades de usuário privilegiado, aplicação de políticas.
- Uso: compliance, trilha de auditoria e monitoração de abuso interno.

### 2.5 Network Logs
- Foco: tráfego de rede, conexões e filtragem.
- Exemplos: conexões de entrada e saída, logs de firewall, NAT, proxy.
- Uso: investigar origem/destino de ataques, exfiltração, scanners.

### 2.6 Access Logs
- Foco: acesso a recursos específicos (web, banco, APIs).
- Exemplos: logs de acesso do Apache/nginx, logs de banco de dados, logs de API gateway.
- Uso: reconstruir como um recurso foi usado e por quem.

> Observação: aplicações e appliances diferentes podem definir logs adicionais, mas quase todas se encaixam nessas famílias.

---

## 3. Análise de Windows Event Logs

O Windows registra grande parte das atividades do sistema e dos usuários no **Event Viewer**. Os três grupos principais de logs são:

- **Application**: eventos gerados por aplicações;
- **System**: eventos gerados pelo próprio Windows (drivers, serviços, hardware, startup/shutdown);
- **Security**: eventos de segurança - o mais relevante para investigações.

### 3.1 Event Viewer
O próprio Windows fornece uma interface para leitura e filtragem:

1. Abrir o **Event Viewer** (Pesquisar “Event Viewer” / “Visualizador de Eventos”).
2. Navegar até **Windows Logs**.
3. Selecionar o log desejado (Application, Security, System).
4. Usar **Filter Current Log** para filtrar por período, por usuário ou por **Event ID**.

### 3.2 Campos importantes de um evento
Cada evento Windows traz campos que o perito deve observar:

- **Log Name**: de qual log veio (Security, System, Application).
- **Logged**: data e hora do evento.
- **Event ID**: identificador único do tipo de evento.
- **Description**: descrição detalhada do que ocorreu.
- Outros campos podem trazer usuário, computador, processo e endereço de rede.

### 3.3 Event IDs úteis para segurança

| Event ID | Descrição                                                |
|----------|----------------------------------------------------------|
| 4624     | Conta de usuário fez logon com sucesso                   |
| 4625     | Tentativa de logon falhou                                |
| 4634     | Usuário fez logoff                                       |
| 4720     | Conta de usuário foi criada                              |
| 4724     | Tentativa de redefinir senha de uma conta                |
| 4722     | Conta de usuário foi habilitada                          |
| 4725     | Conta de usuário foi desabilitada                        |
| 4726     | Conta de usuário foi deletada                            |

Esses IDs permitem, por exemplo:
- descobrir **quando** um atacante criou uma conta;
- saber **quem** criou essa conta;
- verificar se ela foi **habilitada** ou **teve a senha alterada**;
- listar logons válidos (4624) e falhos (4625) para ver o momento de acesso.

O recurso de filtro do Event Viewer permite digitar, por exemplo, “4624,4720,4724” e visualizar rapidamente uma trilha de criação de conta → habilitação → logon.

---

## 4. Logs de acesso de servidores web

Servidores web (Apache, nginx e outros) registram **todas as requisições** feitas ao site. Um único arquivo, como `/var/log/apache2/access.log`, já costuma trazer:

- IP de origem da requisição;
- data e hora;
- método HTTP (GET, POST, etc.);
- recurso/URL acessado;
- código de status (200, 404, 500…);
- user-agent (navegador/SO do cliente).

### 4.1 Exemplo de linha de log do Apache

```text
172.16.0.1 - - [06/Jun/2024:13:58:44] "GET /products HTTP/1.1" 404 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) ..."
````

**Campos:**

* **IP Address**: `172.16.0.1`
* **Timestamp**: `[06/Jun/2024:13:58:44]`
* **HTTP Method**: `GET`
* **URL**: `/products`
* **Status Code**: `404`
* **User-Agent**: identifica SO e navegador

A partir disso é possível:

* identificar o IP que fez o último acesso a um endpoint sensível;
* ver se houve tentativa de **enumeração** (muitos 404);
* ver se houve erro no servidor (500) após requisição de um IP específico;
* reconstruir a cronologia de uma exploração.

### 4.2 Rotação de logs

Servidores normalmente fazem **log rotation**: criam vários arquivos menores por período (diário, semanal), em vez de manter um arquivo gigantesco. Quando é preciso analisar um intervalo maior, basta **concatenar** os arquivos.

---

## 5. Análise manual de logs em Linux

Mesmo quando existe uma ferramenta SIEM ou painel gráfico, o perito deve saber fazer análise manual com comandos básicos. Os principais:

### 5.1 `cat`

Exibe o conteúdo de um ou mais arquivos de log.

```bash
cat access.log
cat access1.log access2.log > combined_access.log
```

Útil para:

* ver rapidamente o conteúdo;
* **combinar** dois logs em um só.

### 5.2 `grep`

Filtra por padrão (IP, URL, user-agent, método).

```bash
grep "192.168.1.1" access.log
grep "POST" access.log
grep "/contact" access.log
```

Útil para:

* procurar todas as requisições de um IP;
* localizar a primeira/última vez que um recurso foi acessado;
* extrair somente linhas que contenham um indicador.

### 5.3 `less`

Permite navegar pagina a pagina e fazer busca dentro do arquivo.

```bash
less access.log
```

Dentro do `less`:

* `space` → próxima página
* `b` → página anterior
* `/termo` → buscar termo
* `n` / `N` → próxima / anterior ocorrência

Muito útil para logs grandes.

---

## 6. Correlação de logs

Na prática, quase nunca um único log responde tudo. A abordagem profissional é:

1. **Descobrir o momento do incidente** (ex.: 06/Jun/2024 por volta de 13:55);
2. **Filtrar todos os logs para esse intervalo** (Windows Security, web access, firewall);
3. **Identificar o ator** (IP, hostname, usuário Windows);
4. **Seguir o ator** nos demais logs:

   * no Windows: event IDs 4624, 4720, 4724…
   * no web: IP e método;
   * na rede: IP de origem e destino.

Assim o investigador passa de “houve um ataque” para “o host X, autenticado como Y, acessou o recurso Z às HH:MM e logo depois fez requisições ao servidor de arquivos”.

---

## 7. Boas práticas para logs

* **Habilitar logs relevantes** no sistema operacional e nos serviços críticos.
* **Sincronizar horário** (NTP) de todos os hosts, para que timestamps sejam comparáveis.
* **Proteger os arquivos de log** contra alteração e exclusão (permissões, cópias remotas).
* **Centralizar** (syslog/SIEM) quando possível, para reduzir o risco de o atacante apagar evidências no host comprometido.
* **Documentar filtros e consultas** usados durante a investigação - isso faz parte da trilha de auditoria.

---

## 8. Conclusão

* Logs são uma das **principais fontes de evidência** em investigações digitais.
* Há várias categorias (sistema, segurança, aplicação, auditoria, rede, acesso) - saber qual consultar reduz drasticamente o tempo de análise.
* No Windows, o **Event Viewer** e os **Event IDs de segurança** formam a base da investigação de atividades de usuários.
* Em servidores web, os **logs de acesso** permitem rastrear IP, horário, recurso e navegador, ajudando a identificar exploração de endpoints.
* Comandos simples (`cat`, `grep`, `less`) já permitem uma análise manual poderosa.
* Para que tudo isso seja válido em ambiente forense, os logs devem ser coletados, preservados e documentados de forma íntegra.