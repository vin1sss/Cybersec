---
title: Visão geral de análise em macOS
tags:
  - forense
  - macos
  - analise-de-sistemas
  - estrutura
status: concluido
---

# Visão geral de análise em macOS

A análise forense em macOS segue a mesma lógica do Windows e do Linux que você já colocou no vault: identificar onde o sistema guarda configurações, onde ficam os dados por usuário e quais pastas e bancos de dados o sistema usa para registrar atividades. A diferença é que o macOS organiza muita coisa dentro de `~/Library` e usa APFS/HFS+ como sistemas de arquivos, o que influencia na aquisição e na navegação da imagem.

---

## 1. Objetivo

- Mostrar quais são os diretórios mais relevantes do macOS para fins de forense.
- Indicar onde ficam os dados por usuário.
- Apontar o que costuma ser coletado em labs/CTFs quando o alvo é macOS.
- Servir de base para as notas seguintes da pasta macOS (sistemas de arquivos, logs, artefatos de usuário).

---

## 2. Visão geral do sistema de arquivos

Instalações modernas de macOS usam APFS, com a ideia de volumes separados (sistema, dados, pré-boot). Em análise isso aparece como múltiplos volumes/pontos de montagem.

Principais locais:

- `/System` - componentes principais do sistema.
- `/Library` - recursos do sistema disponíveis para todos os usuários.
- `/Applications` - aplicativos instalados para todos.
- `/Users` - diretórios de usuários.
- `/private` - onde ficam vários itens que parecem "sumidos" (logs, tmp, var).

Em imagens montadas fora do macOS, algumas pastas podem aparecer com caminhos um pouco diferentes dependendo da ferramenta que fez a aquisição. O princípio é o mesmo: localizar `/Users` e o volume de dados.

---

## 3. Usuários e diretórios de usuário

O equivalente ao `C:\Users\` do Windows é `/Users/`.

- `/Users/<usuario>/` - pasta home de cada usuário.
- `/Users/Shared/` - área compartilhada entre usuários.
- `/Users/<usuario>/Desktop`, `Documents`, `Downloads` - mesmos nomes que você já está acostumado.
- `/Users/<usuario>/Library/` - pasta essencial em forense macOS.

Dentro de `~/Library/` ficam muitos artefatos de uso:

- `~/Library/Application Support/` - dados de aplicativos.
- `~/Library/Preferences/` - arquivos `.plist` com preferências.
- `~/Library/Logs/` - logs específicos do usuário.
- `~/Library/Mail/` - dados de e-mail, quando configurado.
- `~/Library/Safari/` ou de outros browsers - histórico, cookies, downloads.

Em investigações, grande parte do "quem fez" e "qual app usou" vem daqui.

---

## 4. Pastas de sistema e de configuração global

Além da Library do usuário, há a Library do sistema:

- `/Library/` - configurações e recursos para todos os usuários.
- `/Library/Logs/` - logs em nível de sistema.
- `/Library/LaunchAgents` e `/Library/LaunchDaemons` - muito usados para persistência.
- `/System/Library/` - componentes do próprio sistema operacional.

Esses diretórios costumam ser varridos quando se procura por itens de inicialização, artefatos de segurança ou componentes instalados por agentes.

---

## 5. Locais comuns de persistência

No macOS a persistência costuma aparecer como "launch items" (LaunchAgents e LaunchDaemons):

- `/Library/LaunchAgents/` - inicia para usuários.
- `/Library/LaunchDaemons/` - inicia em nível de sistema.
- `~/Library/LaunchAgents/` - inicia só para aquele usuário.

Arquivos aqui são normalmente `.plist` que apontam para um binário ou script. Em análise forense, qualquer `.plist` recente, com nome estranho ou apontando para caminho em `~/Downloads` ou dentro de `/tmp` merece ser anotado.

---

## 6. Aplicativos

Aplicativos no macOS geralmente ficam em:

- `/Applications/<App>.app`
- alguns podem ficar em `~/Applications` se foram instalados só para aquele usuário.

Cada `<App>.app` é na verdade uma pasta. Dentro dela há arquivos de configuração e às vezes recursos úteis para identificar versão, fornecedor e data de instalação. Em aquisição isso ajuda a comprovar que um determinado software estava presente.

---

## 7. Logs e registros

O macOS tem logs em:

- `/var/log/`
- `/Library/Logs/`
- `~/Library/Logs/`

Versões mais novas usam o sistema de "unified logging" (comando `log show ...`). Na nota específica de logs você pode detalhar como extrair e filtrar. Aqui basta registrar que existem logs em nível de sistema e em nível de usuário e que ambos podem ser relevantes.

---

## 8. Checklist rápido de análise

1. Identificar todos os usuários em `/Users/`.
2. Copiar o conteúdo de `~/Library/` dos usuários relevantes.
3. Coletar `/Library/LaunchAgents` e `/Library/LaunchDaemons`.
4. Coletar `/var/log/` e `/Library/Logs/`.
5. Listar aplicativos em `/Applications/` e `~/Applications/`.
6. Procurar `.plist` recentes ou com nomes incomuns.
7. Registrar datas de criação/modificação dos itens de inicialização.

---

## 9. Conclusão

Com esse mapeamento mínimo você consegue navegar em uma imagem de macOS do mesmo jeito que já está fazendo com Windows e Linux no seu vault: começa pela pasta de usuários, entra na Library, coleta logs e olha os pontos de inicialização. As próximas notas da pasta macOS podem aprofundar em sistemas de arquivos (APFS/HFS+), em análise de logs unificados e em artefatos de usuário específicos (Mail, Safari, Messages).