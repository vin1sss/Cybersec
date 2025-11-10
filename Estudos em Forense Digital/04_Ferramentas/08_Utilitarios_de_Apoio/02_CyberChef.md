---
title: "CyberChef"
tags: [ferramenta, utilitario, conversao, decodificacao, cyberchef, dfir]
status: rascunho
---

# CyberChef

Este documento apresenta o **CyberChef** como utilitário de apoio para análise forense e DFIR. Ele é um “canivete suíço” de transformações de dados: você arrasta/solta uma amostra (texto, base64, hexdump, JSON, timestamps) e monta uma **receita** com operações (decodificar, descomprimir, converter, procurar) até chegar ao formato que interessa.

É extremamente útil quando você extraiu algo de:
- um PCAP (payload em base64);
- um dump de memória (string suspeita);
- um log (timestamp estranho);
- um artefato de navegador (dados encodados);
- um malware que escondeu o comando em várias camadas.

Em vez de escrever script para cada caso, você resolve no CyberChef.

---

## 1. Conceito

O CyberChef funciona em cima de três coisas:

1. **Entrada** – o dado bruto que você quer analisar (texto, hex, base64…).
2. **Receita** – sequência de operações que serão aplicadas.
3. **Saída** – o resultado final, já transformado.

Exemplo de receita simples:
1. From Base64
2. Gunzip
3. Extract URLs

Ao rodar, ele faz as três etapas e mostra tudo.

---

## 2. Onde usar

Existem duas formas principais:

- **version online** (roda no navegador) – prático, mas cuidado com dados sensíveis;
- **versão local/offline** – você baixa o projeto e roda localmente, ideal para ambiente forense.

Para arquivos de caso, o ideal é usar **local/offline**.

---

## 3. Casos de uso forense comuns

### 3.1 Decodificar base64/hex

Você encontra uma string assim:

```text
U3lzdGVtIEluZm8gUmVkaXJlY3Rvcnk=
````

Receita:

* From Base64

Resultado:

```text
System Info Redirectory
```

Ou, se estiver em hex:

* From Hex → To UTF-8

---

### 3.2 Converter timestamps

Logs e artefatos costumam trazer timestamps em formatos esquisitos:

* Unix epoch (segundos desde 1970)
* Windows Filetime (100-ns desde 1601)
* WebKit/Chrome time

Receita:

* From UNIX Timestamp
* From Windows Filetime
* From Chrome Timestamp

Assim você consegue colocar o horário do artefato na mesma linha do tempo do caso.

---

### 3.3 Extrair URLs, IPs e domínios

Você tem um blob de texto grande (talvez de memória, talvez de log) e quer achar IOC:

Receita:

* Extract URLs
* Extract IP addresses
* Unique

Isso dá uma lista limpinha para colocar no relatório ou testar em outras ferramentas.

---

### 3.4 Descompactar / deofuscar

Alguns payloads vêm em várias camadas:

* base64
* depois gzip
* depois JSON

Receita:

1. From Base64
2. Gunzip
3. JSON Beautify

E você chega nos campos legíveis.

---

### 3.5 Trabalhar com binário em hexdump

Se você tem só um trecho hexadecimal (por exemplo, copiado do Volatility ou do Autopsy):

1. Converter para bytes (From Hex)
2. Tentar identificar string (To UTF-8)
3. Tentar extrair arquivos (File extraction – dependendo do caso)

---

## 4. Montando uma receita

A lógica é sempre:

1. Colar o dado na aba de entrada.
2. No painel de operações, buscar o que você quer (ex.: “base64”, “timestamp”).
3. Arrastar para o meio na ordem que quer aplicar.
4. Olhar a saída.

Se não ficou bom:

* reordenar as operações;
* mudar o encoding (UTF-8 vs Windows-1252);
* limpar caracteres.

O bom é que tudo é visual e rápido.

---

## 5. Integração com o restante do exame

O CyberChef costuma entrar **no meio** da análise, quando você já extraiu alguma coisa e ela não está legível. Exemplos:

* você extraiu um objeto HTTP no Wireshark e o corpo está em base64 → CyberChef.
* você rodou Volatility e o malfind trouxe payload em hex → CyberChef.
* você pegou uma chave do Registro que está em base64/JSON → CyberChef.
* você precisa converter um horário para UTC → CyberChef.

Ou seja, ele não coleta nem “faz forense” sozinho – ele **transforma** dados para que você consiga usar.

---

## 6. Blocos operacionais

```text
receita: decodificar base64 e extrair URLs

1. From Base64
2. Extract URLs
3. Unique
```

```text
receita: converter timestamp do Windows para legível

1. From Windows Filetime
2. To ISO 8601
```

```text
receita: desfazer camadas de payload

1. From Base64
2. Gunzip
3. JSON Beautify
```

```text
receita: normalizar texto e procurar IP

1. Remove null bytes
2. Extract IP addresses
3. Unique
```

---

## 7. Boas práticas

* para dados de caso, usar versão local do CyberChef;
* salvar a receita junto do caso (muitas vezes o CyberChef gera uma URL com a receita embutida);
* anotar qual era o dado de entrada e qual foi a saída que você usou no relatório;
* aplicar o mínimo de operações necessário (para não distorcer o dado);
* se a saída vier truncada, tentar exportar como arquivo;

---

## 8. Comparação rápida

* **CyberChef** – melhor para transformação rápida, visual e combinável.
* **scripts Python/PowerShell** – melhores para automação e grandes volumes.
* **hexdump/xxd** – bom para olhar bytes, mas menos confortável para decodificar várias camadas.
* **ferramentas de SUITE forense** – às vezes têm decoders, mas o CyberChef costuma ter **mais** operadores e é mais rápido de testar.

O CyberChef é, na prática, o “laboratório de conversões” que você deixa do lado para resolver aquilo que não vale escrever script só para uma vez.