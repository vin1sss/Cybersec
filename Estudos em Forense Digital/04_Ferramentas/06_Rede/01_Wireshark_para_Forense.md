---
title: "Wireshark para Forense"
tags: [ferramenta, rede, pcap, wireshark, analise-de-pacotes, dfir]
status: rascunho
---

# Wireshark para Forense

Este documento descreve o uso do **Wireshark** em contexto de **análise forense de rede**. Diferente do uso “ao vivo” (sniffando tráfego em tempo real), aqui o foco é analisar **capturas já coletadas** (PCAP/PCAPNG) vindas de um incidente, de um sensor ou extraídas de memória/disk. A ideia é identificar comunicações suspeitas, arquivos transferidos, credenciais em claro e, principalmente, **cronologia de conexões**.

Fluxo recomendado:

1. coletar o tráfego (tcpdump, sensor, appliance, extração de memória);
2. garantir integridade do arquivo (hash);
3. abrir no Wireshark em ambiente de análise;
4. aplicar filtros e seguir fluxos;
5. extrair artefatos (arquivos, credenciais, http objects);
6. documentar hosts, portas, horários e payloads relevantes.

---

## 1. Conceito

O Wireshark é um analisador de protocolos que entende centenas de protocolos de rede (Ethernet, IP, TCP, UDP, HTTP, TLS, SMB, DNS, etc.). Em forense, o que interessa é que ele:

- reagrupa pacotes em conversas (TCP streams);
- exibe dados de aplicação reconstruídos;
- permite filtrar por IP, porta, protocolo, string;
- exporta partes do tráfego para arquivo.

Ou seja: você consegue sair do “pacote cru” e chegar em “o usuário baixou isso deste IP neste horário”.

---

## 2. Fontes de captura

Você pode receber o PCAP de várias formas:

- `tcpdump` rodando em servidor comprometido;
- coleta do próprio Wireshark (quando permitido);
- sensores de rede (Suricata, Zeek, appliances);
- extração de PCAP de memória (algumas ferramentas conseguem);
- PCAP parcial de proxy/firewall.

Independente da origem, o Wireshark vai abrir o arquivo e mostrar os pacotes em ordem temporal.

---

## 3. Interface e filtros

A parte mais importante em forense é saber **filtrar**.

Filtros comuns:

- por IP de interesse:
```text
  ip.addr == 192.168.0.10
````

* por conversa TCP:

  ```text
  tcp.stream == 5
  ```

* por protocolo:

  ```text
  http
  dns
  smb2
  ```

* por porta:

  ```text
  tcp.port == 4444
  ```

* por host/domínio (quando não criptografado):

  ```text
  http.host contains "example.com"
  ```

Você aplica o filtro, reduz o ruído e passa a ver só o que importa.

---

## 4. Reconstruindo conversas

Um dos recursos mais usados em DFIR:

1. encontrar o pacote HTTP/TCP suspeito;
2. clicar com o botão direito;
3. **Follow** → **TCP Stream** (ou HTTP stream).

O Wireshark então mostra toda a conversa daquela conexão em ordem, com requisições e respostas. Isso ajuda a descobrir:

* URLs acessadas;
* comandos enviados (em protocolos em texto);
* payloads transferidos;
* partes de malware baixadas por HTTP.

Quando o tráfego não é criptografado, muitas vezes dá para ver tudo.

---

## 5. Extraindo arquivos/objetos

Quando o incidente envolve download de arquivo ou exfiltração simples, é útil tentar **extrair objetos**:

* `File` → `Export Objects` → `HTTP`
* ou `Export Objects` → `SMB`

Se o arquivo estiver completo no PCAP, você consegue salvá-lo para análise estática depois (hash, AV, strings, etc.).

---

## 6. Identificando comportamento suspeito

Coisas que vale procurar em PCAP forense:

* conexões a IPs externos incomuns;
* HTTP GET/POST para caminhos estranhos;
* DNS com domínios que não fazem sentido;
* uso de portas altas/túnel;
* SMB entre hosts que normalmente não trocam arquivos;
* credenciais em claro (FTP, HTTP Basic, Telnet).

Para isso, você pode navegar pela aba **Statistics** → **Endpoints / Conversations / Protocol Hierarchy** para ver rapidamente “quem falou com quem”.

---

## 7. Integração com tcpdump

Em muitos casos a captura é feita com `tcpdump` e analisada no Wireshark. Exemplo de captura:

```bash
sudo tcpdump -i eth0 -w incidente.pcap
```

Depois:

* copiar `incidente.pcap` para a máquina de análise;
* abrir no Wireshark;
* aplicar os filtros.

Também dá para fazer capturas mais focadas:

```bash
sudo tcpdump -i eth0 host 10.0.0.5 and port 80 -w http_10.0.0.5.pcap
```

Quanto mais focada a captura, mais simples a análise.

---

## 8. Blocos operacionais

```text
comando: abrir captura e filtrar por IP

1. Abrir o arquivo .pcap no Wireshark
2. No campo de filtro: ip.addr == 192.168.1.50
3. Aplicar
4. Seguir o fluxo de interesse (Follow TCP Stream)
```

```text
comando: listar quem falou com quem

1. Statistics -> Conversations
2. Ordenar por bytes/packets
3. Identificar pares de comunicação incomuns
```

```text
comando: exportar objetos HTTP

1. File -> Export Objects -> HTTP
2. Selecionar o objeto desejado
3. Salvar para análise posterior
```

---

## 9. Boas práticas

* trabalhar sempre na cópia do PCAP;
* documentar hora de início e fim da captura;
* registrar timezone do analista e da máquina que capturou;
* se o PCAP for muito grande, usar filtros logo no começo para não travar a interface;
* salvar os fluxos importantes (Follow TCP) em arquivos separados;
* calcular hash dos arquivos exportados.

---

## 10. Comparação rápida

* **Wireshark** – melhor para análise manual, visual, passo a passo.
* **tcpdump** – melhor para coleta rápida em linha de comando.
* **NetworkMiner** – melhor para extração automática de artefatos (vamos ver no arquivo 03).
* **Zeek/Suricata** – melhores para monitoramento contínuo, mas geram artefatos que você pode correlacionar aqui.

O Wireshark é o “canivete suíço” da análise de PCAP: abre, filtra, segue e extrai.