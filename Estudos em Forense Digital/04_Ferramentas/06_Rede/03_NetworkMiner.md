---
title: "NetworkMiner"
tags: [ferramenta, rede, pcap, networkminer, extracao, dfir]
status: rascunho
---

# NetworkMiner

Este documento apresenta o **NetworkMiner** como ferramenta de **análise passiva de rede** focada em **extrair artefatos automaticamente** de capturas PCAP/PCAPNG. Enquanto o Wireshark mostra pacote por pacote, o NetworkMiner já organiza por **hosts**, **sessões** e principalmente **arquivos e credenciais recuperáveis**. É uma ótima opção quando você quer ver rapidamente “o que foi transferido” ou “quem falou com quem” sem ficar escrevendo filtros.

Ele complementa o fluxo que você já tem:
1. capturar com tcpdump (ou outra ferramenta);
2. analisar manualmente no Wireshark, se precisar;
3. abrir o mesmo PCAP no NetworkMiner para extrair arquivos, fotos, executáveis, listas de hosts, DNS etc.;
4. anexar os artefatos ao caso.

---

## 1. Conceito

O NetworkMiner é um analisador de tráfego **passivo**: ele não injeta nada na rede, apenas lê o que foi capturado. A partir disso ele:

- identifica hosts (IP, hostname, MAC quando disponível);
- lista sessões e protocolos;
- **reconstrói arquivos transferidos** (HTTP, SMB, FTP, etc.);
- tenta recuperar credenciais em claro;
- exibe imagens;
- organiza tudo em abas.

Isso torna a análise de PCAP muito mais rápida para quem está atrás de “objetos” e não de pacotes.

---

## 2. Fontes de dados

O NetworkMiner trabalha, na prática, com:

- arquivos `.pcap` ou `.pcapng` capturados com tcpdump, Wireshark ou sensor;
- em algumas versões/edições, pode fazer captura ao vivo, mas para forense o uso mais comum é **abrir PCAP pronto**.

Portanto, o fluxo típico é:
- `sudo tcpdump -i eth0 -w incidente.pcap`
- copiar `incidente.pcap` para a estação de análise
- abrir no NetworkMiner

---

## 3. Interface e abas principais

Ao abrir um PCAP, as abas mais importantes são:

1. **Hosts** – mostra todos os hosts vistos na captura, com IP, hostname (se achado), OS guess (às vezes), número de sessões. Ótimo para saber “quem participou”.

2. **Frames/Parameters** – informações mais de baixo nível.

3. **Files** – talvez a aba mais usada em forense. Lista todos os arquivos que o NetworkMiner conseguiu reconstruir a partir do tráfego:
   - nome
   - tipo
   - origem/destino
   - protocolo
   - tamanho
   - e um botão para salvar

4. **Images** – exibe imagens que passaram no tráfego (útil em casos de exfiltração simples ou acesso web).

5. **Credentials** – quando o tráfego tinha autenticação em claro ou fácil de decodificar, ele lista aqui.

6. **DNS** – resolve e mostra os nomes consultados.

Com essas abas você já tem uma visão de alto nível do PCAP sem olhar um único pacote.

---

## 4. Extraindo arquivos

O principal ganho do NetworkMiner é **não precisar remontar manualmente** objetos HTTP/SMB.

Procedimento típico:

1. abrir o PCAP;
2. ir na aba **Files**;
3. ordenar por tipo/tamanho;
4. selecionar o arquivo que interessa (por exemplo, um `.exe` ou `.docx`);
5. salvar em disco para análise posterior (hash, sandbox, strings).

Isso é perfeito para casos em que o PCAP mostra um download de malware ou de documento suspeito.

---

## 5. Identificando comunicação suspeita

Na aba **Hosts** você vê o conjunto de participantes. Coisas para olhar:

- hosts internos falando com IP público incomum;
- quantidade incomum de transferências para o mesmo host;
- DNS apontando para domínios estranhos;
- diferenças de SO (um host que se identifica como algo diferente do padrão da rede).

Isso ajuda a descobrir o “servidor C2” ou o “host que exfiltrou”.

---

## 6. Integração com Wireshark e tcpdump

O trio fica assim:

- **tcpdump** → captura (rápido, CLI)
- **Wireshark** → análise detalhada (filtros, Follow TCP Stream)
- **NetworkMiner** → extração automática (arquivos, imagens, credenciais, hosts)

Ou seja: se você já tem o PCAP, pode abrir no Wireshark **e** no NetworkMiner – não é uma escolha exclusiva.

---

## 7. Blocos operacionais

```text
passo: analisar PCAP no NetworkMiner

1. Abrir o NetworkMiner
2. File -> Open -> selecionar incidente.pcap
3. Aguardar o parsing
4. Ir na aba "Hosts" para ver quem participou
5. Ir na aba "Files" para extrair objetos
6. Salvar artefatos de interesse
````

```text
passo: extrair arquivo suspeito

1. Aba "Files"
2. Filtrar/ordenar por tipo (ex.: application/x-dosexec)
3. Selecionar o arquivo
4. Botão "Save" / "Export"
5. Calcular hash e enviar para análise
```

```text
passo: ver credenciais transitando

1. Aba "Credentials"
2. Ver se há usuário/senha capturados
3. Registrar no relatório
```

---

## 8. Boas práticas

* trabalhar sempre com cópia do PCAP;
* salvar todos os arquivos extraídos em pasta do caso (ex.: `artefatos_rede/`);
* calcular hash dos binários extraídos;
* registrar IP de origem e destino do arquivo extraído (isso vai para o relatório);
* se o PCAP for muito grande, considerar fatiar antes, porque o parsing pode ficar pesado;
* se nada aparecer na aba de arquivos, pode ser que o tráfego estivesse criptografado (HTTPS).

---

## 9. Comparação rápida

* **NetworkMiner** – melhor para extrair e organizar por host/arquivo; análise passiva.
* **Wireshark** – melhor para depurar protocolo, seguir fluxo e aplicar filtros complexos.
* **tcpdump** – melhor para captura.
* **Ferramentas de IDS (Zeek/Suricata)** – melhores para monitoramento contínuo, mas podem gerar logs que você depois correlaciona com o que o NetworkMiner mostra.

O NetworkMiner é, portanto, o “extrator rápido” de evidências de rede a partir de um PCAP que você já tem.