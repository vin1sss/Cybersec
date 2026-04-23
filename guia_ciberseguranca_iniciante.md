# Guia de Entrada em Cibersegurança, Segurança Ofensiva e Hacking Ético

> Guia técnico para quem está começando e quer evoluir com base forte, prática controlada e método.

## Como usar este guia

- Siga a ordem proposta pelo menos na primeira leitura.
- Não tente aprender todas as trilhas ao mesmo tempo.
- Use os critérios de prontidão para decidir quando avançar.
- Pratique só em ambiente autorizado.

> **Aviso operacional**
> Este material cobre estudo e prática em ambiente controlado. Fora de escopo legal, técnica vira problema jurídico.

---

## 1. O que você está aprendendo de fato

### 1.1 Cibersegurança

Cibersegurança é o campo amplo: prevenção, detecção, resposta, arquitetura, AppSec, cloud, IAM, threat intel, DFIR e governança.

### 1.2 Segurança ofensiva

Segurança ofensiva é testar sistemas como atacante autorizado para encontrar risco real antes de incidente real.

### 1.3 Hacking ético

Hacking ético exige três coisas ao mesmo tempo:

- autorização explícita
- escopo claro
- registro do que foi feito

Sem isso, não é prática profissional.

---

## 2. Regras que vêm antes de qualquer ferramenta

### 2.1 Regras legais e de escopo

- pratique só em laboratório próprio, CTF, plataformas de treino ou programa autorizado
- nunca teste alvo fora de escopo
- leia regras de engajamento, limites e exclusões
- guarde autorização e contexto

### 2.2 Regras profissionais

- documente comandos, hipóteses e resultados
- registre limitações do teste
- trate evidência técnica com rastreabilidade
- reporte impacto com clareza, sem exagero

### 2.3 Mentalidade de trabalho

- menos “rodar ferramenta”, mais “formular hipótese”
- menos volume de scan, mais qualidade de leitura
- menos improviso, mais repetibilidade

---

## 3. Mapa de progressão (visão rápida)

| Fase | Objetivo | Entrega mínima |
| --- | --- | --- |
| Fundamentos | Entender como sistemas e rede funcionam | Explicar fluxo básico de autenticação e HTTP |
| Sistemas e redes | Operar Linux/Windows e analisar tráfego | Navegar no terminal, interpretar portas e serviços |
| Programação | Automatizar tarefas repetitivas | Scripts simples em Python e shell |
| Web e APIs | Ler e manipular requests/responses | Reproduzir fluxo com DevTools, curl e Burp |
| Segurança aplicada | Entender risco técnico e mitigação | Resolver labs básicos com relatório |
| Laboratório e prática guiada | Ganhar consistência | Rotina semanal com notas e write-ups |
| Especialização | Escolher trilha principal | Plano de estudo focado em uma área |

---

## 4. Base técnica obrigatória

### 4.1 Sistemas operacionais

Domine o básico de Linux e Windows antes de avançar para ofensiva mais pesada.

#### Linux: o mínimo operacional

- navegação e arquivos: `pwd`, `ls`, `cd`, `cp`, `mv`, `rm`, `cat`, `less`, `head`, `tail`
- busca e filtro: `grep`, `find`, `sort`, `uniq`, `cut`, `sed`, `awk`
- permissões e usuários: `chmod`, `chown`, grupos
- processos e serviços: `ps`, `top`, `kill`, `systemctl`
- pacotes e documentação: `apt`, `man`

#### Windows: o mínimo operacional

- estrutura de arquivos e permissões
- serviços e processos
- noções de registro e Event Viewer
- comandos básicos em `cmd`
- PowerShell básico para automação e triagem

#### Referências diretas

- [Linux man-pages project](https://www.kernel.org/doc/man-pages/)
- [man7.org](https://man7.org/linux/man-pages/)
- [Windows Commands](https://learn.microsoft.com/pt-br/windows-server/administration/windows-commands/windows-commands)
- [PowerShell Docs](https://learn.microsoft.com/pt-br/powershell/)

### 4.2 Redes

Sem rede, você fica dependente de ferramenta.

Entenda com segurança:

- IP, máscara, gateway, DNS, NAT
- TCP vs UDP, portas e serviços
- handshake TCP em nível conceitual
- TLS/HTTPS em nível conceitual
- proxy e VPN em nível conceitual

Perguntas que você precisa responder sem hesitar:

- o que significa “porta aberta” na prática?
- o que um scan realmente inferiu?
- o que mudou quando você colocou firewall/proxy no caminho?

Referências:

- [Nmap Reference Guide](https://nmap.org/book/man.html)
- [Nmap Network Scanning](https://nmap.org/book/)
- [Wireshark Docs](https://www.wireshark.org/docs/)
- [TShark manual](https://www.wireshark.org/docs/man-pages/tshark.html)

### 4.3 Programação e automação

Você não precisa começar como dev pleno, mas precisa automatizar o básico.

#### Python

- tipos e estruturas básicas
- funções e módulos
- leitura/escrita de arquivos
- JSON
- requests HTTP
- tratamento de exceções

#### Bash e PowerShell

- pipes e redirecionamento
- loops e condicionais simples
- filtros de texto
- automação de tarefas de host

Referências:

- [Python Docs](https://docs.python.org/3/)
- [Python Tutorial](https://docs.python.org/3/tutorial/index.html)
- [Tutorial Python pt-BR](https://docs.python.org/pt-br/3/tutorial/index.html)
- [PowerShell Documentation](https://learn.microsoft.com/en-us/powershell/)

### 4.4 Web e APIs

A maior parte dos iniciantes evolui mais rápido aqui por feedback curto.

Domine:

- request/response
- métodos HTTP (`GET`, `POST`, `PUT`, `PATCH`, `DELETE`)
- headers
- cookies e sessão
- autenticação vs autorização
- JSON e APIs REST
- same-origin e CORS em nível introdutório

Referências:

- [MDN HTTP](https://developer.mozilla.org/pt-BR/docs/Web/HTTP)
- [MDN HTTP Overview](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Guides/Overview)
- [MDN Cookies](https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Guides/Cookies)
- [MDN Methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Methods)

### 4.5 Git e versionamento

Use Git desde cedo para estudar como profissional:

- versionar scripts e notas
- manter histórico de decisão técnica
- publicar material de portfólio com clareza

Referências:

- [Pro Git Book](https://git-scm.com/book/en/v2)
- [Pro Git pt-BR](https://git-scm.com/book/pt-br/v2/Come%C3%A7ando-O-B%C3%A1sico-do-Git)
- [Git Reference](https://git-scm.com/docs)

---

## 5. Fundamentos de segurança antes da ofensiva

### 5.1 Conceitos centrais

- confidencialidade, integridade, disponibilidade (CIA)
- autenticação vs autorização
- gestão de sessão
- controle de acesso

Referências:

- [OWASP Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
- [IDOR Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Insecure_Direct_Object_Reference_Prevention_Cheat_Sheet.html)

### 5.2 Criptografia: o que precisa saber no começo

- hash não é criptografia reversível
- KDF e salt importam em armazenamento de senha
- TLS protege transporte, não corrige lógica fraca

Referências:

- [NIST SP 800-63B](https://pages.nist.gov/800-63-4/sp800-63b.html)
- [NIST Password Guidance](https://pages.nist.gov/800-63-4/sp800-63b/passwords/)
- [OWASP Password Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)

### 5.3 Vulnerabilidades e severidade

Saiba diferenciar:

- `CWE`: classe de fraqueza
- `CVE`: vulnerabilidade identificada
- `CVSS`: método de pontuação de severidade

Referências:

- [CWE](https://cwe.mitre.org/)
- [CWE Top 25](https://cwe.mitre.org/top25/)
- [NVD](https://nvd.nist.gov/)
- [CVSS - FIRST](https://www.first.org/cvss/)
- [CVSS v4.0](https://www.first.org/cvss/specification-document)

### 5.4 Threat modeling e linguagem comum

Use threat modeling para pensar além do payload:

- ativos críticos
- fluxo de dados
- fronteiras de confiança
- cadeia de ataque e pontos de detecção

Referências:

- [OWASP Threat Dragon](https://owasp.org/www-project-threat-dragon/)
- [OWASP Threat Model Library](https://owasp.org/www-project-threat-model-library/)
- [MITRE ATT&CK](https://attack.mitre.org/)
- [ATT&CK Resources](https://attack.mitre.org/resources/)
- [CAPEC](https://capec.mitre.org/)

---

## 6. Ferramentas essenciais (e para que servem)

| Ferramenta | Para que serve | Erro comum |
| --- | --- | --- |
| DevTools | inspecionar tráfego e comportamento client-side | pular para scanner sem entender request |
| `curl` | reproduzir chamadas HTTP de forma crua | usar sem ler headers/cookies |
| Burp Suite | interceptar, modificar e testar fluxo web/API | usar só no automático |
| Nmap | descoberta de host/porta/serviço | tratar saída como “verdade absoluta” |
| Wireshark/TShark | análise de pacotes e fluxo de rede | olhar pacote isolado sem contexto |
| Docker | subir alvo reproduzível de treino | confundir lab com ambiente real |
| Kali Linux | caixa de ferramentas ofensivas | usar como muleta conceitual |

Referências:

- [Burp Documentation](https://portswigger.net/burp/documentation)
- [Burp Getting Started](https://portswigger.net/burp/documentation/desktop/getting-started)
- [Kali Linux](https://www.kali.org/)
- [Kali Docs](https://www.kali.org/docs/)
- [Kali Tools](https://www.kali.org/tools/all-tools/)
- [Docker Get Started](https://docs.docker.com/get-started/)

---

## 7. Laboratório sem caos

### 7.1 Estrutura mínima

- host principal (Windows ou Linux)
- VM Linux de base
- VM Kali opcional
- VM Windows de laboratório (quando possível)
- alvos vulneráveis em Docker/VM

### 7.2 Regras de isolamento

- snapshot antes de mudança grande
- ambiente de treino separado do ambiente pessoal
- sem credencial real em laboratório
- sem exposição desnecessária à internet

### 7.3 Ferramentas de lab

- [VirtualBox](https://www.virtualbox.org/)
- [VirtualBox Manual](https://www.virtualbox.org/manual/)
- [WSL overview](https://learn.microsoft.com/en-us/windows/wsl/about)

---

## 8. Ordem prática para quem está começando

### 8.1 Etapa 1: sistema e terminal

- OverTheWire Bandit
- comandos Linux básicos
- scripts curtos em Python/Bash

### 8.2 Etapa 2: HTTP e navegador

- DevTools no fluxo real
- `curl` com headers/cookies
- leitura de autenticação e sessão

### 8.3 Etapa 3: Burp + labs web

- Proxy e Repeater
- manipulação manual de request
- labs de auth, sessão e controle de acesso

### 8.4 Etapa 4: rede e tráfego

- Nmap no seu laboratório
- captura no Wireshark/TShark
- correlação serviço x pacote x resposta

### 8.5 Etapa 5: prática guiada contínua

- [PortSwigger Web Security Academy](https://portswigger.net/web-security)
- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
- [TryHackMe resources](https://tryhackme.com/resources)
- [HTB Academy Paths](https://academy.hackthebox.com/catalogue/paths)
- [picoCTF](https://picoctf.org/)

### 8.6 Etapa 6: relatório e repetibilidade

Após cada lab:

- registre objetivo e hipótese
- descreva passos reproduzíveis
- explique impacto técnico
- proponha correção

---

## 9. Trilhas de especialização (entrada clara)

### 9.1 Web / AppSec / API Security

Entre aqui se você gosta de navegador, lógica de aplicação e fluxo HTTP.

Base de referência:

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [OWASP Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/latest/)
- [OWASP API Security Top 10](https://owasp.org/API-Security/editions/2023/en/0x00-header/)
- [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/index.html)
- [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/)

### 9.2 Pentest de rede / infraestrutura / AD

Entre aqui se você prefere host, serviço, protocolo e ambiente corporativo.

Foco inicial:

- enumeração de serviços
- autenticação de rede em nível conceitual
- SMB e serviços internos
- privilege escalation em laboratório
- AD introdutório com base de Windows + PowerShell

### 9.3 Bug bounty

Entre depois de dominar base web.

Antes de começar:

- leia escopo, exclusões, rate limits e safe harbor
- valide impacto antes de reportar
- evite caça de volume sem análise

Referências:

- [Hacker101](https://www.hackerone.com/hackers/hacker101)
- [HackerOne Programs](https://www.hackerone.com/bug-bounty-programs)
- [Intigriti Hackademy](https://www.intigriti.com/researchers/hackademy)
- [Bugcrowd](https://www.bugcrowd.com/)

### 9.4 Mobile

Entre depois de boa base em web e API.

Referências:

- [OWASP MASVS](https://mas.owasp.org/MASVS/)
- [OWASP MASTG](https://mas.owasp.org/MASTG/)

### 9.5 Cloud Security

Entre com base prévia em Linux, rede, IAM e containers.

Foco inicial:

- IAM
- storage/buckets
- rede virtual
- secrets
- logging e policy

### 9.6 Reverse engineering / malware

Entre depois de base em sistemas, C e assembly básico.

Ferramenta de entrada:

- [Ghidra](https://ghidra-sre.org/)

### 9.7 DFIR e resposta a incidente

Entre se você gosta de investigação, artefato, timeline e correlação.

Foco inicial:

- coleta e preservação
- logs e contexto temporal
- triagem de host
- documentação de evidência

---

## 10. Plano de 12 meses (enxuto e realista)

### 10.1 Meses 1-2

- Linux/Windows básicos
- terminal
- Git
- redes básicas
- Python básico
- virtualização

Entregas:

- Bandit concluído
- 5 scripts simples
- scans básicos no seu lab

### 10.2 Meses 3-4

- HTTP
- cookies e sessão
- DevTools
- `curl`
- Burp básico

Entregas:

- reproduzir fluxos de login manualmente
- resolver labs iniciais de web

### 10.3 Meses 5-6

- OWASP Top 10
- API security básica
- metodologia de teste

Entregas:

- labs de auth, access control, XSS, SQLi
- relatórios mais consistentes

### 10.4 Meses 7-8

- Nmap e Wireshark em nível melhor
- serviços de rede em laboratório
- host Linux/Windows mais sólido

Entregas:

- mini rede de laboratório
- documentação de hipóteses e validações

### 10.5 Meses 9-10

- escolher trilha principal
- seguir path prático focado

Entregas:

- 3 a 5 write-ups próprios de qualidade

### 10.6 Meses 11-12

- consolidar metodologia
- fortalecer portfólio técnico
- decidir próxima certificação (se fizer sentido)

Entregas:

- material técnico público permitido
- revisão de notas e lacunas

---

## 11. Método de estudo com boa taxa de retenção

### 11.1 Loop de execução

- conceito
- demonstração curta
- laboratório guiado
- laboratório menos guiado
- script curto
- write-up
- revisão

### 11.2 Regra prática de tempo

- 70% prática
- 20% documentação técnica
- 10% revisão e teoria complementar

### 11.3 Rotina semanal sugerida

Para 5h semanais:

- 2h laboratório
- 1h30 fundamentos
- 1h script/tooling
- 30min revisão

Para 10h semanais:

- 4h laboratório
- 2h fundamentos
- 2h automação
- 1h revisão
- 1h relatório/write-up

Para 15h semanais:

- 6h laboratório
- 3h fundamentos
- 2h programação
- 2h revisão/notas
- 2h portfólio/aprofundamento

---

## 12. Portfólio técnico e comunicação

### 12.1 O que produzir

- notas técnicas organizadas
- scripts pequenos e úteis
- relatórios de laboratório
- README claros
- write-ups permitidos

### 12.2 O que evitar

- copiar write-up sem entendimento
- publicar credencial, dado sensível ou alvo real
- confundir prova de conceito com impacto real

### 12.3 Estrutura mínima de relatório

- contexto e escopo
- passos reproduzíveis
- evidência objetiva
- impacto técnico
- recomendação

---

## 13. Certificações: quando ajudam

Certificação pode ser útil para estrutura e validação, não para substituir prática.

Sequência comum:

- base forte + labs
- [CompTIA Security+](https://www.comptia.org/en-us/certifications/security/) (opcional, contexto dependente)
- [eJPT](https://ine.com/security/certifications/ejpt-certification) ou [PJPT](https://certifications.tcm-sec.com/pjpt/)
- [PNPT](https://certifications.tcm-sec.com/pnpt/) ou [PEN-200/OSCP](https://www.offsec.com/courses/pen-200/) mais tarde

---

## 14. Checklist: saindo do zero com consistência

### 14.1 Sistemas

- [ ] navego bem em terminal Linux
- [ ] entendo processo, serviço e permissão
- [ ] opero básico de Windows e PowerShell

### 14.2 Redes

- [ ] entendo IP, porta, DNS, TCP/UDP e TLS no nível funcional
- [ ] interpreto scan básico do Nmap
- [ ] analiso tráfego simples no Wireshark

### 14.3 Web

- [ ] leio requests/responses sem confusão
- [ ] reproduzo fluxo com `curl`
- [ ] uso Burp básico com clareza

### 14.4 Programação

- [ ] escrevo script simples em Python
- [ ] automatizo tarefa curta em Bash ou PowerShell
- [ ] leio JavaScript simples

### 14.5 Segurança aplicada

- [ ] diferencio autenticação e autorização
- [ ] conheço OWASP Top 10 e API Top 10
- [ ] classifico fraqueza/vulnerabilidade com CWE/CVE/CVSS

### 14.6 Prática

- [ ] tenho laboratório funcional
- [ ] concluí trilhas guiadas básicas
- [ ] escrevi relatórios próprios

---

## 15. Perguntas de autoavaliação

Se você responde isso com segurança, sua base está madura:

- qual diferença prática entre autenticação e autorização?
- como sessão web é criada, mantida e invalidada?
- o que o scanner realmente inferiu e o que ele não prova?
- por que TLS ajuda no transporte, mas não corrige lógica de negócio?
- como você demonstraria impacto de IDOR sem exagero?
- qual é a evidência mínima para um relatório técnico confiável?

---

## 16. Glossário rápido

- **API**: interface de troca de dados/funções entre sistemas.
- **Autenticação**: processo de validar identidade.
- **Autorização**: processo de validar permissão.
- **Cookie**: dado persistido pelo cliente para manter estado.
- **CVE**: identificador público de vulnerabilidade.
- **CWE**: classificação de fraqueza.
- **CVSS**: pontuação de severidade.
- **DNS**: resolução de nome para IP.
- **HTTP**: protocolo de aplicação para web.
- **IDOR**: acesso indevido por controle de autorização falho.
- **Nmap**: ferramenta de descoberta/enumeração de rede.
- **Sessão**: continuidade de estado entre requisições.
- **TLS**: proteção de transporte usada em HTTPS.
- **XSS**: injeção de script em contexto web.

---

## 17. Fontes e referências por prioridade

### 17.1 Núcleo (use sempre)

- [MDN HTTP](https://developer.mozilla.org/pt-BR/docs/Web/HTTP)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [OWASP WSTG](https://owasp.org/www-project-web-security-testing-guide/latest/)
- [OWASP API Security Top 10 2023](https://owasp.org/API-Security/editions/2023/en/0x00-header/)
- [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/index.html)
- [Nmap Book](https://nmap.org/book/)
- [Wireshark Docs](https://www.wireshark.org/docs/)
- [Python Docs](https://docs.python.org/3/)
- [Git Docs](https://git-scm.com/docs)
- [PowerShell Docs](https://learn.microsoft.com/pt-br/powershell/)

### 17.2 Linguagem comum de segurança

- [MITRE ATT&CK](https://attack.mitre.org/)
- [CAPEC](https://capec.mitre.org/)
- [CWE](https://cwe.mitre.org/)
- [NVD](https://nvd.nist.gov/)
- [CVSS - FIRST](https://www.first.org/cvss/)

### 17.3 Plataformas de prática

- [PortSwigger Web Security Academy](https://portswigger.net/web-security)
- [OverTheWire Bandit](https://overthewire.org/wargames/bandit/)
- [TryHackMe](https://tryhackme.com/)
- [Hack The Box Academy](https://academy.hackthebox.com/)
- [picoCTF](https://picoctf.org/)

### 17.4 Referências de carreira

- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
- [NICE Framework Resource Center](https://www.nist.gov/itl/applied-cybersecurity/nice/nice-framework-resource-center)
- [NICE current versions](https://www.nist.gov/itl/applied-cybersecurity/nice/nice-framework-resource-center/nice-framework-current-versions)

---

## 18. Templates reutilizáveis

### 18.1 Modelo de nota técnica

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

### 18.2 Modelo de relatório de vulnerabilidade

```md
# [Título da vulnerabilidade]

## Resumo
## Ativo afetado
## Severidade sugerida
## Descrição técnica
## Pré-requisitos
## Passos para reproduzir
## Evidências
## Impacto
## Recomendação
## Referências
```

---

## 19. Erros frequentes no início

- aprender ferramenta antes de conceito
- estudar tudo ao mesmo tempo
- viver de vídeo e evitar laboratório
- ignorar documentação oficial
- não escrever notas
- confundir scan com avaliação técnica
- pular cedo para alvo real
- negligenciar Windows
- subestimar relatório

---

## 20. Fechamento direto

Se você mantiver base técnica, prática controlada e registro consistente, a evolução fica previsível. O oposto também é previsível: pressa sem método trava aprendizado.

O ponto central deste guia é simples: hipótese, teste, evidência, correção, repetição.
