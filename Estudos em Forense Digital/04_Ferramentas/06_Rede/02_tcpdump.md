---
title: "tcpdump"
tags: [ferramenta, rede, pcap, tcpdump, captura, forense]
status: rascunho
---

# tcpdump

Este documento apresenta o **tcpdump** como ferramenta de **captura forense de tráfego de rede** em linha de comando. Ele é o par perfeito do Wireshark: você captura com tcpdump (rápido, leve, remoto) e analisa depois no Wireshark ou em outra ferramenta. Em incidentes, o tcpdump é muito útil porque roda em servidor, não precisa de GUI e permite filtrar já na coleta.

Fluxo recomendado:

1. identificar a interface de rede que tem o tráfego de interesse;
2. definir um filtro BPF (host, porta, protocolo);
3. capturar para arquivo `.pcap` com `-w`;
4. transferir o `.pcap` para estação de análise;
5. abrir no Wireshark e aplicar filtros mais ricos;
6. documentar horário e parâmetros usados.

---

## 1. Conceito

O tcpdump é um sniffer de pacotes baseado em **libpcap**. Ele:

- escuta uma interface de rede;
- aplica um filtro (opcional);
- grava os pacotes em um arquivo de captura.

Por ser CLI, ele é ideal para:
- servidores sem interface gráfica;
- ambientes de nuvem;
- coleta rápida em máquina comprometida;
- capturas longas com rotação de arquivo.

---

## 2. Identificando interfaces

Antes de capturar, descubra quais interfaces existem:

```bash
tcpdump -D
````

Isso lista as interfaces disponíveis. Em Linux, a mais comum é `eth0`, `ens33`, `wlan0` etc.

---

## 3. Captura básica

Capturar tudo que passa na interface e mostrar na tela:

```bash
sudo tcpdump -i eth0
```

Para **forense**, quase sempre queremos salvar em arquivo:

```bash
sudo tcpdump -i eth0 -w incidente.pcap
```

Explicação:

* `-i eth0` → interface
* `-w incidente.pcap` → escreve o tráfego bruto no arquivo, sem decodificar

Depois você pega o `incidente.pcap` e analisa em outra máquina.

---

## 4. Usando filtros (BPF)

Capturar tudo pode gerar arquivos enormes. Use filtros.

### 4.1 Por host

```bash
sudo tcpdump -i eth0 host 192.168.1.50 -w host50.pcap
```

### 4.2 Por origem ou destino

```bash
sudo tcpdump -i eth0 src 192.168.1.50 -w src50.pcap
sudo tcpdump -i eth0 dst 192.168.1.50 -w dst50.pcap
```

### 4.3 Por porta

```bash
sudo tcpdump -i eth0 port 80 -w http.pcap
```

Ou só destino 443:

```bash
sudo tcpdump -i eth0 dst port 443 -w https_out.pcap
```

### 4.4 Combinando

```bash
sudo tcpdump -i eth0 host 10.0.0.5 and port 445 -w smb_10.0.0.5.pcap
```

Isso é ótimo para capturar apenas a comunicação suspeita.

---

## 5. Limitando tempo e tamanho

Para não lotar o disco durante a coleta, dá para:

* limitar o número de pacotes:

  ```bash
  sudo tcpdump -i eth0 -c 1000 -w curto.pcap
  ```

* rotacionar arquivos com `-C` (tamanho em MB) e `-W` (quantidade):

  ```bash
  sudo tcpdump -i eth0 -w captura.pcap -C 50 -W 10
  ```

  Isso faz até 10 arquivos de 50 MB cada, útil em monitoramento curto de incidente.

---

## 6. Visualizando rapidamente

Mesmo sem Wireshark, você pode dar uma olhada rápida:

```bash
tcpdump -r incidente.pcap
```

* `-r` lê de um pcap
* dá pra aplicar filtro na leitura também:

  ```bash
  tcpdump -r incidente.pcap http
  ```

Isso ajuda a conferir se a captura pegou o que você queria.

---

## 7. Integração com Wireshark e outras ferramentas

O objetivo final quase sempre é abrir o `.pcap` no Wireshark:

1. copiar `incidente.pcap` para a estação de análise;
2. abrir no Wireshark;
3. aplicar filtros mais avançados;
4. seguir fluxos, exportar objetos.

Também dá para usar PCAP capturado pelo tcpdump em:

* NetworkMiner;
* ferramentas de IDS/IPS para replay;
* scripts de extração.

---

## 8. Blocos operacionais

```text
comando: captura forense básica

sudo tcpdump -i eth0 -w incidente.pcap
```

```text
comando: capturar só o host suspeito

sudo tcpdump -i eth0 host 192.168.1.50 -w host50.pcap
```

```text
comando: capturar HTTP apenas

sudo tcpdump -i eth0 port 80 -w http.pcap
```

```text
comando: ler o pcap depois

tcpdump -r incidente.pcap
```

---

## 9. Boas práticas

* rodar como root/sudo para pegar todo o tráfego;
* anotar data/hora de início e fim da captura;
* usar nomes de arquivo que indiquem host e protocolo (`2025-11-07-host50-http.pcap`);
* calcular hash do arquivo final;
* **não** analisar diretamente na máquina comprometida – copiar para ambiente de análise;
* usar filtros na captura para reduzir volume e evitar perda de pacote.

---

## 10. Comparação rápida

* **tcpdump** – captura enxuta, CLI, ideal para servidores e resposta a incidentes.
* **Wireshark** – melhor para análise visual e detalhada.
* **NetworkMiner** – melhor para extrair arquivos e artefatos automaticamente a partir do PCAP.
* **appliances/sensores** – ótimos para captura contínua, mas nem sempre você tem acesso na hora; tcpdump é universal.

O tcpdump é o “gravador de rede” que cabe em qualquer lugar; você captura com ele e analisa com as outras ferramentas.