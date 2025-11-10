---
title: Logs e eventos no macOS
tags:
  - forense
  - macos
  - logs
  - analise-de-sistemas
status: concluido
---

# Logs e eventos no macOS

O macOS registra atividades em dois jeitos principais:
- logs de arquivos tradicionais, localizados em `/var/log/`, `/Library/Logs/` e `~/Library/Logs/`;
- o sistema de unified logging (mais novo), acessível via comando `log`.

Para análise forense isso significa que você pode ter tanto arquivos de texto quanto um repositório mais estruturado de eventos. O objetivo desta nota é mostrar onde procurar e o que vale a pena coletar.

---

## 1. Objetivo

- Identificar os diretórios de logs de sistema e de usuário no macOS.
- Registrar quais logs são mais úteis em uma investigação.
- Indicar o uso básico do unified logging para extração.
- Montar um checklist de coleta.

---

## 2. Diretórios principais de logs

O macOS mantém logs em três níveis:

1. **Sistema (global)**  
   - `/var/log/`
   - `/Library/Logs/`

2. **Por aplicativo (global)**  
   - `/Library/Logs/<app>`  
   - usado por apps instalados para todos os usuários.

3. **Por usuário**  
   - `~/Library/Logs/`  
   - usado por apps que rodam no contexto daquele usuário.

Em uma imagem, sempre que você identificar o usuário alvo, entre na pasta dele e colete `~/Library/Logs/` junto com o restante da `~/Library/`.

---

## 3. Logs clássicos em `/var/log/`

Alguns nomes comuns que vale procurar:

- `/var/log/system.log` - um dos principais logs de sistema.
- `/var/log/install.log` - registros de instalação e atualização.
- `/var/log/appstore.log` - pode indicar instalação via App Store.
- `/var/log/apache2/` - se o serviço web estiver habilitado.
- outros arquivos em `/var/log/` podem aparecer conforme serviços forem ativados.

Esses arquivos podem estar rotacionados (`system.log.0.gz`, etc.). Em forense, coletar todos, não só o atual.

---

## 4. `/Library/Logs/` e `~/Library/Logs/`

- `/Library/Logs/` - costuma ter logs de serviços e apps em nível de sistema.
- `~/Library/Logs/` - apps que rodam no usuário podem registrar aqui, incluindo ferramentas de terceiros.

Isso é útil para montar linha do tempo por usuário:
- abrir os arquivos de log do dia do incidente;
- verificar horário de abertura/fechamento de aplicativos;
- cruzar com artefatos de execução e persistência.

---

## 5. Unified logging (comando `log`)

Versões mais recentes do macOS usam o sistema de logs unificado, que não fica exposto só como arquivos de texto. O acesso costuma ser via:

```bash
log show --style syslog --info --last 1h
````

ou

```bash
log show --predicate 'process == "Safari"' --last 2h
```

Pontos importantes para forense:

* você precisa de ferramenta/sistema que entenda o formato de log do macOS;
* o comando acima é útil quando você ainda está dentro da máquina-alvo;
* em imagem offline, algumas ferramentas forenses já interpretam o unified log, mas nem todas.

Se estiver documentando para alunos, vale anotar pelo menos os dois comandos acima.

---

## 6. O que procurar nos logs

* inicialização do sistema e reinicializações;
* instalação de aplicativos (correlacionar com `/var/log/install.log`);
* erros de aplicativos suspeitos;
* logs próximos ao horário em que surgiu o artefato malicioso;
* logs do próprio sistema de atualização;
* entradas que apontem para binários fora do lugar padrão (por exemplo, executando algo em `/Users/<usuario>/Downloads/...`).

---

## 7. Correlação com persistência

Como o macOS usa LaunchAgents e LaunchDaemons, é comum encontrar nos logs:

* erros ao carregar um `.plist`;
* reclamação de caminho inexistente;
* tentativa de executar binário que foi apagado.

Isso ajuda a provar que a persistência existiu mesmo que o arquivo atual tenha sido removido. Por isso:

* primeiro colete os logs,
* depois liste os arquivos em `/Library/LaunchAgents`, `/Library/LaunchDaemons` e `~/Library/LaunchAgents`,
* por fim, procure no log os nomes que você achou.

---

## 8. Checklist de coleta de logs no macOS

1. Coletar `/var/log/` completo (inclusive arquivos rotacionados).
2. Coletar `/Library/Logs/` completo.
3. Para cada usuário relevante, coletar `~/Library/Logs/`.
4. Registrar versão do macOS (impacta no formato dos logs).
5. Se estiver na máquina viva, rodar comandos:

   * `log show --style syslog --last 1h`
   * `log show --info --last 24h`
   * e salvar a saída.
6. Anotar erros de carregamento de `.plist` e de serviços.
7. Cruzar horários dos logs com horários de criação/modificação dos artefatos suspeitos.

---

## 9. Conclusão

Os logs do macOS não ficam todos em um lugar só como muitos alunos esperam. Eles se dividem em sistema, biblioteca e usuário, e ainda existe o mecanismo de unified logging. Se você coletar os três níveis e registrar a versão do macOS, já terá material suficiente para montar uma linha do tempo básica e para comprovar execução de serviços, falhas de persistência e instalação de aplicativos.