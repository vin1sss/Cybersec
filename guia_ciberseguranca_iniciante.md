# Guia completo para entrar em Cibersegurança, Segurança Ofensiva e Hacking Ético

> Um roteiro realmente abrangente, em ordem de estudo, para alguém totalmente iniciante construir base técnica, estudar com método, montar laboratório, praticar com segurança e evoluir até áreas como web security, pentest, bug bounty, redes, Active Directory, mobile, cloud e reverse engineering.

## Como usar este guia

Este material foi escrito para quem está começando do zero ou quase do zero.

A ideia central é simples:

1. Primeiro construir base técnica real.
2. Depois aprender segurança como disciplina.
3. Só então aprofundar em ofensiva, testes, exploração e especializações.

Muita gente entra em segurança tentando aprender ferramentas antes de entender sistemas, redes, HTTP, autenticação, permissões, logs e programação. Isso quase sempre atrasa o progresso.

**Regra de ouro deste guia:**

- Não comece por ferramenta.
- Comece por fundamento.
- Ferramenta sem fundamento vira apertar botão.
- Fundamento com ferramenta vira competência.

---

# 1. O que você realmente está tentando aprender

Antes de falar de ferramentas e trilhas, é importante alinhar termos.

## 1.1 Cibersegurança

Cibersegurança é o campo mais amplo. Inclui prevenção, proteção, detecção, resposta, governança, conformidade, arquitetura, desenvolvimento seguro, threat intelligence, forense, cloud security, engenharia reversa, AppSec, gestão de vulnerabilidades, IAM e muito mais.

## 1.2 Segurança ofensiva

Segurança ofensiva é a parte da cibersegurança focada em pensar como atacante autorizado para encontrar falhas antes do atacante real. Inclui, por exemplo:

- pentest
- red team
- avaliação de aplicações web
- avaliação de APIs
- exploração controlada em laboratório
- simulação de técnicas adversárias
- validação de controles de segurança

## 1.3 Hacking ético

Hacking ético é o uso autorizado, legal e controlado de técnicas de teste, exploração e validação de segurança para melhorar a proteção de sistemas.

**Sem autorização explícita, escopo claro e permissão formal, não é ético nem profissional.**

---

# 2. Mentalidade correta para começar

## 2.1 Você não precisa ser um gênio em matemática para começar

Você precisa, principalmente, de:

- curiosidade técnica
- disciplina
- tolerância a frustração
- consistência
- capacidade de ler documentação
- disposição para testar, quebrar, errar e corrigir

## 2.2 Você precisa gostar de entender como as coisas funcionam

Segurança não é decorar exploit. É entender:

- como um sistema foi construído
- como ele autentica usuários
- como armazena dados
- como expõe serviços
- como falha
- como um atacante abusaria dessas falhas
- como defender, detectar e corrigir

## 2.3 O profissional forte em ofensiva quase sempre tem boa base em defesa

Quanto melhor você entende:

- logs
- hardening
- controle de acesso
- monitoramento
- arquitetura
- desenvolvimento seguro

melhor você ataca de forma técnica, metódica e útil.

---

# 3. Regras legais, éticas e profissionais que você deve fixar desde o dia 1

## 3.1 Só pratique em ambientes permitidos

Use apenas:

- laboratórios próprios
- VMs próprias
- CTFs
- plataformas de treino
- programas públicos de bug bounty com regras claras
- ambientes de empresa quando você tiver autorização formal

## 3.2 Nunca teste fora de escopo

Mesmo um teste “simples” como enumeração, fuzzing, brute force, varredura massiva ou exploração de falha pode violar política, contrato ou lei se estiver fora de escopo.

## 3.3 Documente autorização

Em contexto profissional, guarde:

- escopo
- datas
- contatos
- autorização
- restrições
- regras de engajamento

## 3.4 Aprenda a reportar com clareza

Descobrir falha sem conseguir explicar:

- impacto
- condição de exploração
- evidência
- risco
- correção

é competência incompleta.

---

# 4. Mapa geral da jornada

A ordem mais saudável para um iniciante costuma ser esta:

## Fase 0 - Base de computação

- hardware e sistemas operacionais
- terminal
- arquivos, processos, serviços
- noções de programação
- Git
- virtualização

## Fase 1 - Redes e web

- TCP/IP
- DNS
- HTTP/HTTPS
- cookies, sessões, headers
- APIs
- autenticação e autorização

## Fase 2 - Fundamentos de segurança

- CIA
- identidade e acesso
- criptografia básica
- vulnerabilidades comuns
- hardening
- gestão de vulnerabilidades
- logging
- modelos e frameworks

## Fase 3 - Ferramentas fundamentais

- navegador e DevTools
- curl
- Burp Suite
- Nmap
- Wireshark / TShark
- Bash / PowerShell
- Python
- Docker

## Fase 4 - Prática guiada

- labs para iniciantes
- rooms e trilhas guiadas
- CTFs introdutórios
- write-ups próprios

## Fase 5 - Especialização

Escolha uma trilha principal:

- web / appsec
- pentest de rede
- Active Directory
- bug bounty
- mobile
- cloud
- reverse / malware
- DFIR / forense

---

# 5. O que estudar primeiro, em ordem, com objetivos claros

# 5.1 Informática e sistemas operacionais

Se você ainda não domina o básico do computador, comece aqui sem culpa.

## O que você precisa saber

- diferença entre arquivo, diretório, processo, serviço e porta
- instalar programas
- variáveis de ambiente
- permissões básicas
- caminhos absolutos e relativos
- compactação e extração
- noções de logs
- noções de memória, CPU, disco e rede

## O que dominar no Linux

Você não precisa começar direto em Kali. Para base, Ubuntu ou Debian costuma ser melhor para aprender o sistema com calma.

Aprenda:

- `pwd`, `ls`, `cd`, `mkdir`, `rm`, `cp`, `mv`, `cat`, `less`, `head`, `tail`
- `grep`, `find`, `sort`, `uniq`, `cut`, `sed`, `awk`
- permissões com `chmod`, `chown`
- usuários e grupos
- processos com `ps`, `top`, `htop`, `kill`
- serviços com `systemctl`
- pacotes com `apt`
- pipes e redirecionamento
- leitura de manual com `man`

### Recursos recomendados

- [Linux man-pages project](https://www.kernel.org/doc/man-pages/) - referência oficial das páginas de manual do Linux
- [man7.org - Linux man pages online](https://man7.org/linux/man-pages/) - excelente forma de consultar páginas `man` no navegador
- [man(1)](https://man7.org/linux/man-pages/man1/man.1.html) - para aprender a usar o próprio manual

## O que dominar no Windows

Mesmo que você goste mais de Linux, não ignore Windows. Em segurança ofensiva e defesa, ele é central.

Aprenda:

- estrutura de arquivos e permissões
- usuários e grupos
- serviços
- processos
- registro em nível conceitual
- Event Viewer em nível conceitual
- `cmd`
- PowerShell básico

### Recursos recomendados

- [Windows Commands - documentação oficial](https://learn.microsoft.com/pt-br/windows-server/administration/windows-commands/windows-commands)
- [Documentação do PowerShell](https://learn.microsoft.com/pt-br/powershell/)
- [O sistema de ajuda do PowerShell](https://learn.microsoft.com/pt-br/powershell/scripting/learn/ps101/02-help-system?view=powershell-7.6)

## Critério de prontidão

Você está pronto para seguir quando consegue:

- navegar confortavelmente no terminal
- instalar ferramentas
- ler manual / help
- entender onde arquivos e logs ficam
- matar processos, editar arquivos de texto e automatizar pequenas tarefas

---

# 5.2 Redes - fundamento obrigatório

Se você quiser atuar em segurança ofensiva, redes são obrigatórias.

## O que entender de verdade

- modelo TCP/IP
- IP, máscara, gateway
- roteamento em nível básico
- DNS
- DHCP em nível conceitual
- TCP vs UDP
- portas e serviços
- handshake TCP em nível conceitual
- TLS/HTTPS em nível conceitual
- NAT em nível conceitual
- proxy e VPN em nível conceitual

## Perguntas que você precisa conseguir responder

- O que significa um host estar “escutando” em uma porta?
- O que muda entre HTTP e HTTPS?
- O que o DNS resolve?
- O que é uma conexão TCP e por que ela importa?
- Por que um firewall altera o comportamento observado?
- O que um scanner de portas realmente está tentando descobrir?

## Recursos recomendados

- [Cisco Skills for All](https://www.netacad.com/courses/all-courses) - procure cursos introdutórios de redes e cibersegurança
- [Nmap Reference Guide](https://nmap.org/book/man.html) - excelente para aprender como redes e serviços aparecem durante enumeração
- [Nmap Network Scanning - guia oficial](https://nmap.org/book/)
- [Wireshark User's Guide](https://www.wireshark.org/docs/wsug_html_chunked/)
- [TShark manual page](https://www.wireshark.org/docs/man-pages/tshark.html)

## Exercícios mínimos

- descobrir seu IP local
- descobrir gateway e DNS
- fazer `ping` para um host conhecido
- resolver nomes com `nslookup` ou `dig`
- rodar uma captura no Wireshark e identificar DNS, TCP e HTTP
- escanear sua própria VM com Nmap e entender o resultado

## Critério de prontidão

Você está pronto quando consegue olhar um fluxo simples de rede e explicar, em linhas gerais:

- quem iniciou a conexão
- qual protocolo foi usado
- em qual porta
- se houve criptografia de transporte
- o que o scanner / navegador / cliente tentou fazer

---

# 5.3 Fundamentos da web - indispensável para quase todo iniciante

A maioria das pessoas começa melhor por segurança web porque a curva de feedback é mais rápida.

## O que estudar

- requisição e resposta HTTP
- métodos HTTP: GET, POST, PUT, PATCH, DELETE, OPTIONS
- status codes
- headers
- cookies
- sessão
- parâmetros
- corpo da requisição
- formulários
- JSON
- autenticação
- autorização
- same-origin em nível conceitual
- CORS em nível conceitual
- APIs REST em nível introdutório

## Recursos recomendados

- [MDN - HTTP](https://developer.mozilla.org/pt-BR/docs/Web/HTTP)
- [MDN - Uma visão geral do HTTP](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Guides/Overview)
- [MDN - Cookies HTTP](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Guides/Cookies)
- [MDN - Header Cookie](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Reference/Headers/Cookie)
- [MDN - Header Set-Cookie](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Reference/Headers/Set-Cookie)
- [MDN - métodos HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods)

## Exercícios mínimos

- abrir DevTools do navegador e inspecionar requests
- identificar cookies de sessão
- comparar GET e POST
- observar headers de resposta
- usar `curl` para repetir uma requisição simples
- enviar JSON para uma API de teste

## Critério de prontidão

Você está pronto quando consegue ler uma request raw e dizer:

- para onde ela vai
- qual método usa
- quais headers importam
- se existe cookie de sessão
- se o corpo é formulário, JSON ou outro formato

---

# 5.4 Programação e automação

Você não precisa virar engenheiro de software antes de entrar em segurança, mas precisa programar o suficiente para automatizar, testar, transformar dados e entender lógica.

## Linguagens mais úteis para começar

### Python

É a linguagem mais amigável para automação, parsing, scripts, PoCs simples e integração com ferramentas.

Aprenda:

- variáveis
- condicionais
- loops
- funções
- listas, dicionários, conjuntos e tuplas
- leitura e escrita de arquivos
- tratamento de exceções
- módulos
- requests HTTP
- regex em nível básico
- subprocess
- parsing de JSON

### Bash

Bash é essencial no Linux para automação rápida.

Aprenda:

- variáveis
- pipes
- redirecionamento
- `for`, `if`
- uso de comandos em cadeia

### PowerShell

Muito útil para ambientes Windows, automação administrativa e análises em hosts Windows.

Aprenda:

- objetos e pipeline em nível introdutório
- `Get-Help`, `Get-Command`, `Get-Process`, `Get-Service`
- leitura de arquivos e filtragem básica

### JavaScript - ao menos leitura básica

Você não precisa começar desenvolvendo front-end, mas precisa entender o suficiente para:

- ler código client-side
- entender DOM em nível básico
- compreender XSS, fetch, local storage, event handlers

## Recursos recomendados

- [Python 3 - documentação oficial](https://docs.python.org/3/)
- [Python Tutorial](https://docs.python.org/3/tutorial/index.html)
- [Tutorial do Python em pt-BR](https://docs.python.org/pt-br/3/tutorial/index.html)
- [PowerShell Documentation](https://learn.microsoft.com/en-us/powershell/)

## Critério de prontidão

Você está pronto quando consegue escrever scripts simples para:

- fazer requests HTTP
- parsear JSON
- ler uma lista de alvos ou URLs
- extrair linhas de um arquivo
- automatizar uma tarefa repetitiva

---

# 5.5 Git e controle de versão

Git é obrigatório. Você vai usar para:

- salvar seus scripts
- versionar notas técnicas
- acompanhar mudanças em projetos
- colaborar com outros profissionais
- manter portfólio

## O que aprender

- `git init`
- `git clone`
- `git status`
- `git add`
- `git commit`
- `git log`
- `git branch`
- `git switch`
- `git merge`
- `git pull`
- `git push`
- resolução básica de conflitos

## Recursos recomendados

- [Pro Git Book](https://git-scm.com/book/en/v2)
- [O Básico do Git - em pt-BR](https://git-scm.com/book/pt-br/v2/Come%C3%A7ando-O-B%C3%A1sico-do-Git)
- [Git Reference](https://git-scm.com/docs)

## Critério de prontidão

Você está pronto quando consegue manter um repositório pessoal com:

- scripts
- anotações
- README bem escrito
- histórico compreensível de commits

---

# 5.6 Virtualização, containers e laboratório

Você não aprende segurança ofensiva seriamente sem laboratório.

## O que aprender

- o que é VM
- snapshots
- redes NAT vs bridged em nível conceitual
- isolamento básico
- restore de snapshot
- containers em nível introdutório

## Ferramentas úteis

- [Oracle VirtualBox](https://www.virtualbox.org/)
- [Manual do VirtualBox](https://www.virtualbox.org/manual/)
- [Docker Docs](https://docs.docker.com/)
- [Get Started with Docker](https://docs.docker.com/get-started/)
- [WSL - visão geral](https://learn.microsoft.com/en-us/windows/wsl/about)

## Recomendação prática de laboratório inicial

Monte algo assim:

- Host principal: seu Windows ou Linux
- VM 1: Ubuntu ou Debian para aprender Linux
- VM 2: Kali Linux opcional para usar ferramentas específicas depois que você já tiver base
- VM 3: Windows de laboratório, se possível
- Algumas aplicações vulneráveis em Docker ou VMs próprias de treino

## Regras de segurança do seu lab

- use snapshots antes de grandes mudanças
- mantenha ambientes de treino separados do uso pessoal
- não misture credenciais reais com laboratório
- não exponha lab sem necessidade
- não pratique em terceiros

## Critério de prontidão

Você está pronto quando consegue:

- subir e derrubar VMs
- isolar o ambiente
- fazer snapshot
- instalar ferramentas
- restaurar o estado do lab sem drama

---

# 6. Fundamentos de segurança que vêm antes da ofensiva séria

# 6.1 Princípios centrais

## Tríade CIA

Você precisa dominar:

- confidencialidade
- integridade
- disponibilidade

Depois acrescente também:

- autenticidade
- autorização
- responsabilização / rastreabilidade
- irretratabilidade em certos contextos

## Autenticação vs autorização

Isso precisa ficar muito claro:

- autenticação responde “quem é você?”
- autorização responde “o que você pode fazer?”

Muita falha grave nasce exatamente dessa diferença mal implementada.

### Leitura útil

- [OWASP Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)

## Gestão de sessão

Você deve entender:

- cookie de sessão
- expiração
- invalidação
- fixation em nível conceitual
- roubo de sessão em nível conceitual

## Controle de acesso

Controle de acesso é um dos assuntos mais importantes de web, API, mobile e enterprise.

Leia também:

- [IDOR Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Insecure_Direct_Object_Reference_Prevention_Cheat_Sheet.html)

---

# 6.2 Criptografia - base conceitual suficiente para não se perder

Você não precisa começar provando teoremas, mas precisa saber o essencial.

## O que estudar

- hashing
- salt em nível conceitual
- KDF em nível conceitual
- criptografia simétrica
- criptografia assimétrica
- assinatura digital em nível conceitual
- TLS/HTTPS em nível conceitual
- certificados em nível conceitual

## O que não fazer

- não inventar criptografia própria
- não tratar hash como criptografia reversível
- não assumir que HTTPS resolve todos os problemas

## Leituras úteis

- [NIST SP 800-63B](https://pages.nist.gov/800-63-4/sp800-63b.html)
- [NIST - Strength of Passwords](https://pages.nist.gov/800-63-4/sp800-63b/passwords/)
- [OWASP Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)

---

# 6.3 Vulnerabilidades, fraquezas, CVEs e severidade

Você deve aprender a distinguir:

- **fraqueza** - classe de erro de software ou design
- **vulnerabilidade** - ocorrência concreta explorável em um produto ou sistema
- **CVE** - identificador de vulnerabilidade
- **CWE** - categoria ou tipo de fraqueza
- **CVSS** - forma padronizada de pontuar severidade

## Recursos essenciais

- [CWE - Common Weakness Enumeration](https://cwe.mitre.org/)
- [CWE Top 25](https://cwe.mitre.org/top25/)
- [NVD - National Vulnerability Database](https://nvd.nist.gov/)
- [CVSS - FIRST](https://www.first.org/cvss/)
- [CVSS v4.0 Specification](https://www.first.org/cvss/specification-document)
- [CVSS training](https://www.first.org/cvss/training)

## O que isso muda na prática

Quando você encontra uma falha, precisa aprender a classificá-la por:

- tipo técnico
- pré-condições
- impacto
- superfície afetada
- facilidade de exploração
- prioridade de correção

---

# 6.4 Threat modeling e linguagem comum de segurança

Mesmo quem vai para ofensiva se beneficia muito de threat modeling.

## O que estudar

- ativos
- superfície de ataque
- fluxo de dados
- fronteiras de confiança
- abuso de funcionalidade
- abuso de permissões
- impacto de falha de design

## Recursos úteis

- [OWASP Threat Dragon](https://owasp.org/www-project-threat-dragon/)
- [OWASP Threat Model Library](https://owasp.org/www-project-threat-model-library/)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [Get Started with ATT&CK](https://attack.mitre.org/resources/)
- [ATT&CK Data & Tools](https://attack.mitre.org/resources/attack-data-and-tools/)
- [CAPEC](https://capec.mitre.org/)

## Por que isso importa

Você deixa de pensar só em “payload” e passa a pensar em:

- como o atacante progride
- quais técnicas usa
- quais controles quebram essa cadeia
- como detectar cada fase

---

# 7. Frameworks e referências que todo iniciante deve conhecer

Não para decorar inteiro no início, mas para saber que existem e voltar a eles constantemente.

## 7.1 OWASP Top 10

É uma ótima porta de entrada para segurança de aplicações web.

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [OWASP Top 10 2025](https://owasp.org/Top10/2025/)

## 7.2 OWASP Web Security Testing Guide

Excelente para metodologia de teste.

- [OWASP WSTG](https://owasp.org/www-project-web-security-testing-guide/)
- [WSTG - latest](https://owasp.org/www-project-web-security-testing-guide/latest/)
- [WSTG - Web Application Security Testing](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/)

## 7.3 OWASP API Security Top 10

Importantíssimo hoje.

- [OWASP API Security Project](https://owasp.org/www-project-api-security/)
- [OWASP API Security Top 10 - 2023](https://owasp.org/API-Security/editions/2023/en/0x00-header/)

## 7.4 OWASP Cheat Sheet Series

Extremamente útil no dia a dia.

- [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/index.html)

## 7.5 ASVS

Muito importante para quem quer testar ou desenvolver aplicações com mais rigor.

- [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/)

## 7.6 Mobile

Se quiser ir para mobile, estas são referências centrais.

- [OWASP MASVS](https://mas.owasp.org/MASVS/)
- [OWASP MASTG](https://mas.owasp.org/MASTG/)

## 7.7 Carreira e papéis

Para entender o ecossistema profissional:

- [NIST NICE Framework Resource Center](https://www.nist.gov/itl/applied-cybersecurity/nice/nice-framework-resource-center)
- [NICE current versions](https://www.nist.gov/itl/applied-cybersecurity/nice/nice-framework-resource-center/nice-framework-current-versions)

---

# 8. Ferramentas essenciais que você deve aprender cedo

A ordem abaixo é proposital.

# 8.1 Navegador + DevTools

Antes de Burp, aprenda DevTools.

Você deve conseguir:

- ver requests e responses
- inspecionar cookies
- ver local storage e session storage
- identificar JS carregado
- analisar erros no console

---

# 8.2 curl

`curl` ensina HTTP de forma crua.

Aprenda a:

- mandar GET e POST
- definir headers
- enviar JSON
- mandar cookies
- seguir redirects
- salvar resposta em arquivo

---

# 8.3 Burp Suite

Ferramenta central em segurança web.

## Aprenda primeiro

- Proxy
- Repeater
- intrusão manual e análise básica
- Decoder
- Comparer
- organização do histórico

## Recursos

- [Burp Suite documentation](https://portswigger.net/burp/documentation)
- [Getting started with Burp Suite](https://portswigger.net/burp/documentation/desktop/getting-started)
- [Burp Suite tools](https://portswigger.net/burp/documentation/desktop/tools)

---

# 8.4 Nmap

Ferramenta obrigatória para enumeração de rede.

## O que aprender

- descoberta de hosts
- portas abertas
- versão de serviço
- scripts básicos do NSE
- interpretação de resultados

## Recurso

- [Nmap Reference Guide](https://nmap.org/book/man.html)

---

# 8.5 Wireshark e TShark

Aprenda a capturar e interpretar tráfego.

## O que aprender

- filtros básicos
- seguir stream TCP
- identificar DNS, HTTP, TLS
- exportar e revisar pacotes
- leitura de pcap

## Recursos

- [Wireshark docs](https://www.wireshark.org/docs/)
- [Wireshark User's Guide](https://www.wireshark.org/docs/wsug_html/)
- [TShark manual](https://www.wireshark.org/docs/man-pages/tshark.html)

---

# 8.6 Kali Linux - use certo

Kali é útil, mas muita gente usa cedo demais e aprende pouco.

## Regra prática

- use Kali como caixa de ferramentas
- não use Kali como muleta conceitual
- aprenda o que a ferramenta faz, não só como abrir a ferramenta

## Recursos

- [Kali Linux](https://www.kali.org/)
- [Kali Docs](https://www.kali.org/docs/)
- [What is Kali Linux?](https://www.kali.org/docs/introduction/what-is-kali-linux/)
- [Kali Tools](https://www.kali.org/tools/all-tools/)

---

# 8.7 Docker

Muito útil para subir alvos de treino e ambientes reproduzíveis.

## Use Docker para

- aplicações vulneráveis locais
- stacks web simples
- APIs de teste
- bancos de dados para estudar autenticação e autorização

## Recurso

- [Docker get started](https://docs.docker.com/get-started/)

---

# 9. Plataformas práticas recomendadas para iniciantes

A prática precisa ser progressiva.

## 9.1 OverTheWire - para terminal e raciocínio básico

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)

Excelente para:

- terminal
- arquivos
- permissões
- automação simples
- cultura Linux

## 9.2 PortSwigger Web Security Academy - melhor trilha web gratuita

- [Web Security Academy](https://portswigger.net/web-security)
- [Getting started](https://portswigger.net/web-security/getting-started)
- [All labs](https://portswigger.net/web-security/all-labs)

Use para aprender:

- HTTP na prática
- autenticação
- sessões
- XSS
- SQLi
- SSRF
- access control
- API testing
- lógica de negócios

## 9.3 TryHackMe - bom para progressão guiada

- [TryHackMe resources](https://tryhackme.com/resources)

Procure as trilhas para iniciantes e web security.

## 9.4 Hack The Box Academy - forte para base técnica e ofensiva prática

- [HTB Academy](https://academy.hackthebox.com/)
- [Paths](https://academy.hackthebox.com/catalogue/paths)
- [Information Security Foundations](https://academy.hackthebox.com/path/preview/information-security-foundations)
- [Operating System Fundamentals](https://academy.hackthebox.com/path/preview/operating-system-fundamentals)
- [Basic Toolset](https://academy.hackthebox.com/path/preview/basic-toolset)
- [Cracking into Hack The Box](https://academy.hackthebox.com/path/preview/cracking-into-hack-the-box)

## 9.5 picoCTF - ótimo para base gamificada

- [picoCTF](https://picoctf.org/)
- [picoCTF learning resources](https://picoctf.org/resources.html)

---

# 10. A melhor ordem de prática para um novato absoluto

## Etapa 1 - terminal e sistema

Faça primeiro:

- Bandit
- exercícios básicos de Linux
- scripts simples em Bash e Python

## Etapa 2 - HTTP e navegador

Faça:

- DevTools
- `curl`
- requests simples
- autenticação e cookies

## Etapa 3 - Burp + Web Academy

Comece por:

- requests interceptadas
- Repeater
- labs básicos de auth, session e input handling

## Etapa 4 - enumeração de rede

Faça:

- Nmap em seu próprio lab
- Wireshark para ver o que acontece no fio

## Etapa 5 - labs guiados e depois menos guiados

- TryHackMe
- HTB Academy
- Web labs
- CTFs introdutórios

## Etapa 6 - escrever seus próprios relatórios

Depois de cada laboratório, documente:

- objetivo
- contexto
- superfície observada
- falha
- exploração
- impacto
- correção

---

# 11. O que estudar em segurança web primeiro

Se você quer uma trilha inicial realmente eficiente, faça quase nesta ordem:

1. HTTP básico
2. cookies e sessão
3. autenticação
4. autorização
5. input validation
6. SQL injection em nível conceitual e prático controlado
7. XSS
8. CSRF em nível conceitual
9. upload de arquivos
10. path traversal
11. command injection em ambiente controlado
12. SSRF em nível conceitual e labs
13. lógica de negócios
14. API security
15. segurança de headers
16. same-origin / CORS em nível introdutório

## Recursos centrais

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [OWASP API Security Top 10](https://owasp.org/API-Security/)
- [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/index.html)
- [HTTP Security Headers Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Headers_Cheat_Sheet.html)
- [PortSwigger Web Security Academy](https://portswigger.net/web-security)

---

# 12. O que estudar em pentest de rede / infraestrutura primeiro

Depois da base em Linux, redes e enumeração, avance para:

- reconhecimento
- enumeração de serviços
- SMB em nível conceitual
- autenticação de rede em nível conceitual
- compartilhamentos
- serviços web internos
- bancos expostos em laboratório
- vulnerabilidades conhecidas em ambiente de treino
- privilege escalation em Linux e Windows em laboratório
- Active Directory em nível introdutório

## Observação importante

Muita gente tenta entrar cedo demais em AD ofensivo. Sem base em:

- Windows
- redes
- autenticação
- serviços
- PowerShell

o aprendizado fica superficial.

---

# 13. O que estudar em bug bounty primeiro

Bug bounty não é boa primeira experiência para quem ainda não domina o básico. O risco é ficar varrendo alvo sem método.

## Entre em bug bounty quando você já dominar razoavelmente

- HTTP
- cookies
- autenticação
- autorização
- Burp
- leitura de aplicações
- validação de impacto
- escrita de relatório

## Recursos úteis

- [Hacker101](https://www.hackerone.com/hackers/hacker101)
- [HackerOne bug bounty programs](https://www.hackerone.com/bug-bounty-programs)
- [Intigriti Hackademy](https://www.intigriti.com/researchers/hackademy)
- [Bugcrowd](https://www.bugcrowd.com/)

## Regra essencial

Leia cuidadosamente:

- escopo
- exclusões
- rate limits
- safe harbor
- política de disclosure

---

# 14. O que estudar em mobile, cloud e reverse - e quando

## 14.1 Mobile

Entre em mobile quando já tiver boa base em:

- web
- APIs
- autenticação
- armazenamento local
- Android ou iOS em nível funcional

Comece por:

- [OWASP Mobile Application Security](https://mas.owasp.org/)
- [MASVS](https://mas.owasp.org/MASVS/)
- [MASTG](https://mas.owasp.org/MASTG/)

## 14.2 Cloud

Entre em cloud quando já dominar:

- Linux
- redes
- IAM em nível conceitual
- containers
- aplicações web

Comece aprendendo:

- IAM
- buckets / storage
- rede virtual em nível conceitual
- secrets e configurações erradas
- logging e policy

## 14.3 Reverse engineering / malware

Entre em reverse depois de boa base em:

- sistemas operacionais
- formato de executáveis em nível conceitual
- C em nível introdutório
- assembly em nível básico
- debugging em ambiente controlado

Ferramenta importante:

- [Ghidra](https://ghidra-sre.org/)

---

# 15. Montando um laboratório de estudos bom de verdade

## Estrutura mínima recomendada

### Opção A - notebook/desktop pessoal

- host Windows 11 ou Linux
- WSL2 ou VirtualBox
- 1 VM Linux para estudo
- 1 VM Kali opcional
- Docker para alvos web

### Opção B - laboratório mais robusto

- host principal
- 1 VM Linux
- 1 VM Windows
- 1 VM Kali
- uma pequena rede interna virtual
- aplicações vulneráveis via Docker

## O que instalar no início

### No Linux de base

- `curl`
- `git`
- `python3`
- `pip`
- editor de texto
- `net-tools` ou equivalentes quando necessário
- `nmap`

### Na estação web

- navegador moderno
- extensão de ajuda para DevTools, se quiser
- Burp Suite Community

### Na parte de análise de tráfego

- Wireshark
- TShark

## O que evitar no início

- instalar 300 ferramentas sem entender 10
- usar wordlists gigantes sem propósito
- automatizar cedo demais sem compreender o fluxo
- misturar ambiente pessoal e lab ofensivo

---

# 16. Método de estudo que costuma funcionar melhor

## 16.1 O ciclo mais eficiente

Use este loop:

1. Conceito
2. Demonstração simples
3. Laboratório guiado
4. Laboratório menos guiado
5. Script ou automação curta
6. Nota técnica / write-up
7. Revisão

## 16.2 Regra 70 / 20 / 10

Uma divisão que costuma funcionar bem:

- 70% prática
- 20% leitura de documentação e referência
- 10% teoria complementar e revisão

## 16.3 Estude com perguntas, não só com títulos

Exemplos:

- Como uma sessão é criada, mantida e invalidada?
- O que exatamente torna um IDOR possível?
- Como um scanner deduz versão de serviço?
- O que diferencia autenticação forte de autorização correta?
- Como um upload “inofensivo” vira execução de código em certos cenários?

## 16.4 Faça notas de verdade

Crie um repositório ou sistema de anotações com:

- conceito
- exemplo
- comando
- saída relevante
- armadilha comum
- fonte
- mini exercício

---

# 17. O que um iniciante deve produzir como portfólio

Mesmo sem experiência formal, você pode demonstrar evolução.

## Produza

- notas técnicas organizadas
- scripts pequenos e úteis
- relatórios de laboratório
- README claros
- mini automações em Python ou Bash
- explicações próprias de conceitos
- write-ups de labs permitidos

## Evite

- copiar write-up de terceiros sem entendimento
- publicar credenciais, alvos reais ou material sensível
- mostrar comportamento fora de escopo

---

# 18. Como escrever um bom relatório técnico

Mesmo em laboratório, acostume-se a reportar assim:

## Estrutura mínima

### Título

Ex.: Broken Access Control permitindo acesso indevido a faturas de outros usuários

### Resumo executivo

Em 3 a 5 linhas:

- o que é a falha
- impacto
- risco

### Escopo / contexto

- aplicação / host
- funcionalidade afetada
- condição necessária

### Passos para reproduzir

- passo 1
- passo 2
- passo 3

### Evidências

- request/response relevante
- screenshots se necessário
- logs relevantes em ambiente de treino

### Impacto

- o que um atacante consegue fazer
- quais dados ou funções ficam expostos

### Severidade

- baixa / média / alta / crítica
- justificativa

### Recomendação

- correção técnica
- defesa em profundidade
- validações adicionais

---

# 19. Certificações - opcionais, mas úteis se vierem na hora certa

Certificação não substitui prática, mas pode organizar estudo e sinalizar competência.

## Boas opções dependendo do momento

### Base / geral

- [CompTIA Security+](https://www.comptia.org/en-us/certifications/security/)

### Júnior ofensivo prático

- [eJPT](https://ine.com/security/certifications/ejpt-certification)
- [PJPT](https://certifications.tcm-sec.com/pjpt/)

### Intermediário / mais pesado

- [PNPT](https://certifications.tcm-sec.com/pnpt/)
- [OSCP / OSCP+ via PEN-200](https://www.offsec.com/courses/pen-200/)

## Ordem sugerida

### Se você quer começar do zero

1. Base técnica
2. labs práticos
3. talvez Security+ se fizer sentido para seu contexto
4. eJPT ou PJPT
5. PNPT ou OSCP mais tarde

## Observação importante

Não compre certificação para “virar especialista” em semanas. Use certificação como:

- estrutura
- validação
- pressão saudável por prática

---

# 20. Um plano de estudo de 12 meses para quem está começando do zero

Ajuste à sua rotina. Melhor estudar 1 hora por dia por muitos meses do que maratonar e parar.

## Meses 1 e 2 - base forte

### Objetivos

- Linux básico
- Windows básico
- terminal
- Git
- Python básico
- redes básicas
- virtualização

### Metas

- fazer Bandit
- escrever 5 scripts simples
- usar Git em tudo
- capturar tráfego simples no Wireshark
- rodar scans básicos com Nmap no seu lab

## Meses 3 e 4 - web e HTTP

### Objetivos

- HTTP
- cookies
- sessão
- DevTools
- curl
- Burp Suite

### Metas

- reproduzir requests com `curl`
- entender login e sessão em apps simples
- completar labs básicos do PortSwigger

## Meses 5 e 6 - vulnerabilidades web comuns

### Objetivos

- OWASP Top 10
- API Security basics
- metodologia de teste

### Metas

- completar labs de auth, access control, XSS, SQLi e file handling
- começar a escrever relatórios consistentes

## Meses 7 e 8 - enumeração e infraestrutura

### Objetivos

- Nmap melhor
- Wireshark melhor
- serviços de rede em lab
- Linux e Windows mais sólidos

### Metas

- montar mini rede de laboratório
- enumerar serviços e documentar hipóteses
- fazer labs guiados de infraestrutura

## Meses 9 e 10 - escolha inicial de trilha

Escolha uma principal:

- web / appsec
- bug bounty
- network / AD
- mobile
- cloud

### Metas

- seguir uma trilha de plataforma prática
- construir 3 a 5 write-ups próprios realmente bons

## Meses 11 e 12 - consolidação

### Objetivos

- aprofundar na trilha principal
- melhorar metodologia
- melhorar report
- montar portfólio

### Metas

- publicar material técnico permitido
- revisar anotações
- decidir próxima certificação ou próximo foco

---

# 21. Roteiro enxuto para quem quer entrar especificamente em segurança ofensiva

Se seu foco principal é ofensiva, siga esta sequência:

1. Linux
2. redes
3. HTTP e web
4. Python + Bash + PowerShell básico
5. Git
6. virtualização e lab
7. Burp + Nmap + Wireshark
8. OWASP Top 10 + API Security
9. PortSwigger labs
10. TryHackMe / HTB Academy
11. enumeração de serviços e privilege escalation em laboratório
12. metodologia e relatórios
13. escolha de subárea

---

# 22. Erros mais comuns de iniciantes

## Erro 1 - aprender ferramenta antes de conceito

Exemplo clássico: usar Burp sem entender HTTP.

## Erro 2 - tentar aprender tudo ao mesmo tempo

Web, mobile, AD, cloud, malware, hardware e OSINT simultaneamente costuma gerar ansiedade e pouca profundidade.

## Erro 3 - viver de vídeo e não tocar em laboratório

Vídeo ajuda. Laboratório ensina.

## Erro 4 - só consumir conteúdo “mastigado”

Em segurança, ler documentação oficial é parte da profissão.

## Erro 5 - não escrever notas

Quem não registra o que aprendeu reaprende toda semana.

## Erro 6 - confundir scan com avaliação técnica

Rodar ferramenta não é igual a entender risco.

## Erro 7 - tentar partir cedo para alvo real

Sem base e sem método, isso vira comportamento arriscado e improdutivo.

## Erro 8 - negligenciar Windows

Mesmo quem ama Linux precisa estudar Windows para segurança séria.

## Erro 9 - ignorar relatório

Profissional que não comunica bem perde valor.

## Erro 10 - depender de distro “mágica”

Kali ajuda. Conceito resolve.

---

# 23. Checklist mestre - o que você precisa dominar para dizer que saiu do absoluto zero

## Sistemas

- [ ] navego com conforto no terminal Linux
- [ ] entendo arquivos, processos, serviços e permissões
- [ ] sei o básico de Windows e PowerShell

## Redes

- [ ] entendo IP, portas, DNS, TCP, UDP e TLS em nível funcional
- [ ] consigo interpretar um scan simples do Nmap
- [ ] consigo capturar e filtrar tráfego básico no Wireshark

## Web

- [ ] entendo request, response, headers, cookies e sessão
- [ ] consigo reproduzir requests com `curl`
- [ ] sei usar DevTools e Burp em nível inicial

## Programação

- [ ] escrevo scripts simples em Python
- [ ] automatizo tarefas pequenas com Bash ou PowerShell
- [ ] consigo ler código simples em JavaScript

## Segurança

- [ ] entendo CIA, authn, authz, sessão e controle de acesso
- [ ] sei o básico de hashing, criptografia simétrica e assimétrica
- [ ] conheço OWASP Top 10, API Top 10, CWE, CVE e CVSS

## Prática

- [ ] tenho um laboratório funcional
- [ ] completei labs introdutórios
- [ ] escrevi notas e relatórios próprios

Se a maioria desses itens estiver marcada, você já saiu do estágio de iniciante absoluto e pode aprofundar com muito mais eficiência.

---

# 24. Glossário essencial do iniciante

## API

Interface pela qual sistemas trocam dados e funções.

## Autenticação

Processo de verificar quem é o usuário.

## Autorização

Processo de verificar o que o usuário pode fazer.

## Cookie

Dado que o servidor manda ao navegador e que costuma ser reutilizado em requisições futuras.

## CVE

Identificador público de vulnerabilidade conhecida.

## CWE

Categoria padronizada de fraqueza de software/hardware.

## CVSS

Sistema de pontuação de severidade de vulnerabilidade.

## DNS

Serviço que resolve nomes para endereços IP.

## Header

Metadado em requisição ou resposta HTTP.

## HTTP

Protocolo usado para troca de dados na web.

## IDOR

Falha em que um identificador exposto permite acessar objetos indevidos por falta de autorização adequada.

## Lab

Ambiente controlado de estudo e prática.

## Nmap

Ferramenta de exploração e auditoria de rede focada em descoberta e enumeração.

## Porta

Ponto lógico usado por serviços para comunicação.

## Proxy

Intermediário entre cliente e servidor.

## Sessão

Mecanismo de continuidade de estado entre requisições.

## TLS

Camada que protege transporte, usada em HTTPS.

## XSS

Falha que permite injeção de script em contexto web.

---

# 25. Ordem recomendada de fontes e plataformas

Se eu tivesse que resumir tudo em um caminho prático, eu faria assim:

## Etapa 1 - Base

- [Python Tutorial](https://docs.python.org/3/tutorial/index.html)
- [Git Book](https://git-scm.com/book/en/v2)
- [man7.org](https://man7.org/linux/man-pages/)
- [Windows Commands](https://learn.microsoft.com/pt-br/windows-server/administration/windows-commands/windows-commands)
- [PowerShell Docs](https://learn.microsoft.com/pt-br/powershell/)

## Etapa 2 - Redes e análise

- [Nmap Guide](https://nmap.org/book/)
- [Wireshark Docs](https://www.wireshark.org/docs/)

## Etapa 3 - Web

- [MDN HTTP](https://developer.mozilla.org/pt-BR/docs/Web/HTTP)
- [PortSwigger Web Security Academy](https://portswigger.net/web-security)

## Etapa 4 - Segurança aplicada

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [OWASP API Security](https://owasp.org/www-project-api-security/)
- [OWASP WSTG](https://owasp.org/www-project-web-security-testing-guide/)
- [OWASP Cheat Sheets](https://cheatsheetseries.owasp.org/index.html)

## Etapa 5 - Prática guiada

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
- [TryHackMe](https://tryhackme.com/)
- [HTB Academy](https://academy.hackthebox.com/)
- [picoCTF](https://picoctf.org/)

---

# 26. Fontes e referências recomendadas

## Oficiais / principais

### Fundamentos e carreira

- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
- [NIST NICE Framework Resource Center](https://www.nist.gov/itl/applied-cybersecurity/nice/nice-framework-resource-center)
- [NICE current versions](https://www.nist.gov/itl/applied-cybersecurity/nice/nice-framework-resource-center/nice-framework-current-versions)
- [NIST Cybersecurity Basics](https://www.nist.gov/itl/smallbusinesscyber/cybersecurity-basics)
- [NIST free and low cost learning content](https://www.nist.gov/itl/applied-cybersecurity/nice/resources/online-learning-content)

### Aplicações, web e APIs

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [OWASP Top 10 2025](https://owasp.org/Top10/2025/)
- [OWASP WSTG](https://owasp.org/www-project-web-security-testing-guide/)
- [OWASP WSTG latest](https://owasp.org/www-project-web-security-testing-guide/latest/)
- [OWASP API Security Project](https://owasp.org/www-project-api-security/)
- [OWASP API Security Top 10 2023](https://owasp.org/API-Security/editions/2023/en/0x00-header/)
- [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/)
- [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/index.html)

### Mobile

- [OWASP MASVS](https://mas.owasp.org/MASVS/)
- [OWASP MASTG](https://mas.owasp.org/MASTG/)

### Web e HTTP

- [MDN HTTP](https://developer.mozilla.org/pt-BR/docs/Web/HTTP)
- [MDN HTTP Overview](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Guides/Overview)
- [MDN Cookies](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Guides/Cookies)

### Redes e tráfego

- [Nmap Reference Guide](https://nmap.org/book/man.html)
- [Nmap Official Guide](https://nmap.org/book/)
- [Wireshark Docs](https://www.wireshark.org/docs/)
- [Wireshark User's Guide](https://www.wireshark.org/docs/wsug_html/)

### Sistemas e shell

- [Linux man-pages project](https://www.kernel.org/doc/man-pages/)
- [man7.org](https://man7.org/linux/man-pages/)
- [Python docs](https://docs.python.org/3/)
- [Git docs](https://git-scm.com/docs)
- [PowerShell docs](https://learn.microsoft.com/pt-br/powershell/)
- [Windows Commands](https://learn.microsoft.com/pt-br/windows-server/administration/windows-commands/windows-commands)

### Threat intel, linguagem comum e vulnerabilidades

- [MITRE ATT&CK](https://attack.mitre.org/)
- [ATT&CK resources](https://attack.mitre.org/resources/)
- [CAPEC](https://capec.mitre.org/)
- [CWE](https://cwe.mitre.org/)
- [NVD](https://nvd.nist.gov/)
- [CVSS - FIRST](https://www.first.org/cvss/)

### Prática e labs

- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
- [PortSwigger Web Security Academy](https://portswigger.net/web-security)
- [TryHackMe](https://tryhackme.com/)
- [Hack The Box Academy](https://academy.hackthebox.com/)
- [picoCTF](https://picoctf.org/)

### Certificações

- [CompTIA Security+](https://www.comptia.org/en-us/certifications/security/)
- [INE eJPT](https://ine.com/security/certifications/ejpt-certification)
- [TCM PJPT](https://certifications.tcm-sec.com/pjpt/)
- [TCM PNPT](https://certifications.tcm-sec.com/pnpt/)
- [OffSec PEN-200 / OSCP](https://www.offsec.com/courses/pen-200/)

---

# 27. Fechamento direto

Se você é totalmente novato, guarde isto:

1. Não tente começar por exploit avançado.
2. Comece por Linux, redes, HTTP e programação básica.
3. Aprenda Burp, Nmap e Wireshark cedo, mas com fundamento.
4. Use laboratórios e plataformas seguras.
5. Escreva notas e relatórios desde o início.
6. Escolha uma trilha principal depois de construir base.
7. Mantenha constância por meses, não por fins de semana isolados.

O profissional que evolui bem em cibersegurança quase sempre faz o básico muito bem.

Fundação forte acelera tudo que vem depois.

---

# 28. Como escolher sua trilha principal sem se perder

Depois da base, escolha sua trilha principal com base no tipo de problema que mais te atrai.

## Se você gosta de navegador, requests, lógica e aplicações

Seu caminho mais natural tende a ser:

- segurança web
- AppSec
- API security
- bug bounty

## Se você gosta de infraestrutura, protocolos, hosts e serviços

Seu caminho tende a ser:

- pentest de rede
- infraestrutura
- Active Directory
- hardening e validação técnica

## Se você gosta de engenharia, debugging e executáveis

Seu caminho tende a ser:

- reverse engineering
- malware analysis
- exploit development mais tarde

## Se você gosta de nuvem, automação e arquitetura

Seu caminho tende a ser:

- cloud security
- DevSecOps
- AppSec com foco em pipeline e configuração

## Se você gosta de investigar incidentes e artefatos

Seu caminho tende a ser:

- DFIR
- forense
- threat hunting
- detecção e resposta

## Regra prática

No começo, você não precisa se casar com uma trilha. Você só precisa escolher **uma principal** para aprofundar e manter **uma secundária** como complemento.

Exemplo saudável:

- principal: web
- secundária: redes

ou

- principal: infraestrutura / AD
- secundária: web

---

# 29. Rotina semanal recomendada para não estudar de forma caótica

## Se você tem 5 horas por semana

### Sugestão

- 2h de prática em laboratório
- 1h30 de fundamento / leitura
- 1h de scripting / automação
- 30min de revisão e notas

## Se você tem 10 horas por semana

### Sugestão

- 4h de laboratórios
- 2h de fundamentos
- 2h de scripts / tooling
- 1h de revisão
- 1h de relatório / write-up

## Se você tem 15 horas por semana

### Sugestão

- 6h de labs
- 3h de fundamentos
- 2h de programação
- 2h de revisão e notas
- 2h de relatório, portfólio ou aprofundamento

## Modelo de semana eficiente

### Dia 1

- estudar conceito novo
- fazer notas curtas

### Dia 2

- laboratório guiado do conceito

### Dia 3

- repetir sem guia ou com menos apoio

### Dia 4

- escrever script curto ou comando reproduzível

### Dia 5

- registrar aprendizados e erros

---

# 30. Modelo de nota técnica que vale a pena reutilizar

Copie e use este template para seus estudos:

```md
# Título do conceito ou laboratório

## Objetivo

## Contexto

## Conceitos envolvidos

## Ambiente / Ferramentas

## Passo a passo

## Request / Response / Comandos relevantes

## O que observei

## O que deu errado

## Como corrigi

## Impacto técnico

## Mitigação / correção

## Palavras-chave

## Fontes
```

---

# 31. Modelo de relatório de vulnerabilidade para treino

```md
# [Título da vulnerabilidade]

## Resumo

## Ativo afetado

## Severidade sugerida

## Descrição técnica

## Pré-requisitos

## Passos para reproduzir
1.
2.
3.

## Evidências

## Impacto

## Recomendação

## Referências
```

---

# 32. O que revisar periodicamente para consolidar base

A cada 2 ou 4 semanas, revise:

## Linux

- navegação
- permissões
- processos
- serviços
- logs

## Redes

- DNS
- TCP/UDP
- portas
- TLS
- NAT / proxy em nível conceitual

## Web

- métodos HTTP
- status codes
- headers
- cookies
- sessão
- autenticação vs autorização

## Segurança

- OWASP Top 10
- CWE comuns
- noções de CVSS
- controles de autenticação e autorização

## Ferramentas

- Burp Repeater
- Nmap básico
- Wireshark filtros básicos
- Python para automação curta

---

# 33. Sinais de que você está evoluindo de forma saudável

## Bons sinais

- você entende melhor o porquê dos comandos
- você lê documentação sem desespero
- você consegue reproduzir fluxos manualmente
- você depende menos de copiar e colar pronto
- você escreve notas mais claras
- você começa a enxergar impacto, não só técnica
- você consegue explicar o problema para outra pessoa

## Maus sinais

- você só sabe apertar botão em ferramenta
- você roda scanner sem entender o resultado
- você pula de assunto toda semana
- você coleciona cursos e não termina laboratórios
- você lê write-up antes de tentar pensar sozinho
- você não consegue descrever uma request HTTP simples

---

# 34. Perguntas que todo iniciante deveria conseguir responder depois da base

- O que diferencia autenticação de autorização?
- Como uma sessão web é mantida?
- O que um cookie de sessão faz?
- O que o DNS resolve e em que momento?
- O que o Nmap realmente está inferindo?
- O que o Wireshark mostra e o que ele não “adivinha” sozinho?
- Como um IDOR acontece tecnicamente?
- Por que input validation sozinha não resolve broken access control?
- O que um hash faz e o que ele não faz?
- Por que HTTPS melhora transporte, mas não corrige falhas de lógica?
- O que torna um relatório técnico útil para correção?

Se você responde essas perguntas com segurança, sua base já está ficando forte.

---

# 35. Resumo final em uma única página mental

## Primeiro construa

- sistema
- terminal
- redes
- HTTP
- programação básica
- Git
- laboratório

## Depois acrescente

- fundamentos de segurança
- OWASP Top 10
- API Security
- Burp
- Nmap
- Wireshark
- documentação e relatório

## Depois aprofunde

- web
- pentest de rede
- AD
- bug bounty
- mobile
- cloud
- reverse

## Sempre mantenha

- legalidade
- escopo
- ética
- prática controlada
- documentação
- consistência

Esse é o caminho mais confiável para entrar no mundo da cibersegurança de forma séria, sustentável e profissional.
