---
title: "Rekall"
tags: [ferramenta, memoria, forense, rekall, ram, dfir]
status: rascunho
---

# Rekall

Este documento apresenta o **Rekall** como alternativa ao Volatility para **análise forense de memória**. Ele também trabalha sobre dumps de RAM já coletados e permite listar processos, conexões, módulos, plugins de hunting e exportar resultados. Em muitos ambientes ele surgiu como fork/continuação mais moderna do Volatility 2, com foco em tornar o parsing mais consistente e menos dependente de perfis estáticos.

A ideia é a mesma:
1. coletar a memória;
2. apontar o Rekall para o dump;
3. deixar o próprio Rekall identificar o perfil/sistema;
4. rodar comandos de enumeração e hunting;
5. guardar a saída para o relatório.

---

## 1. Conceito

O Rekall é uma ferramenta de **memory forensics** escrita em Python que:

- analisa dumps de Windows, Linux e macOS;
- possui comandos (plugins) parecidos com os do Volatility;
- consegue, em muitos casos, **identificar o perfil** da imagem automaticamente;
- foi pensada para ser usada tanto interativamente (shell) quanto por linha de comando.

Por isso ele é útil quando:
- o Volatility não está disponível;
- o Volatility não reconhece bem o dump;
- você quer rodar análise dentro de um script Python;
- você quer comparar o resultado de duas ferramentas, o que é saudável em forense.

---

## 2. Pré-requisitos: o dump de memória

Assim como no Volatility, o Rekall **não coleta** a memória; ele **analisa**. Então você precisa primeiro ter o arquivo de memória extraído por:

- DumpIt, win32dd/win64dd, FTK Imager, Memoryze (Windows);
- arquivo de memória da VM (`.vmem`, `.raw`, `.bin`, etc).

Depois de obter o dump:
1. guardar em pasta do caso;
2. calcular hash;
3. trabalhar sobre a cópia.

---

## 3. Execução básica

Há duas formas comuns de usar o Rekall:

1. **Linha de comando direta** (um comando → uma saída)
2. **Shell interativo** (você entra e vai rodando comandos)

Exemplo bem comum de linha de comando:

```bash
rekall -f memoria.raw pslist
````

Explicação:

* `-f memoria.raw` → caminho do dump;
* `pslist` → plugin/comando a ser executado.

Se quiser o modo interativo:

```bash
rekall -f memoria.raw shell
```

Dentro do shell você pode rodar:

```text
(rekall) pslist
(rekall) netstat
(rekall) modules
```

Isso é útil para ir explorando sem ficar digitando o caminho do dump toda hora.

---

## 4. Identificando o perfil / sistema

Um ponto importante do Rekall é que ele tem um bom suporte para **autodetecção**. Normalmente você começa com:

```bash
rekall -f memoria.raw info
```

ou

```bash
rekall -f memoria.raw pslist
```

Se ele não souber qual perfil usar, ele vai avisar/sugerir. Em casos mais antigos do Volatility isso era bem mais manual; no Rekall isso costuma ser mais suave.

---

## 5. Plugins/comandos mais usados

A lógica é a mesma do Volatility. Os nomes podem variar um pouco dependendo da versão, mas o objetivo é idêntico.

1. **pslist**
   Lista processos a partir das estruturas normais do sistema:

   ```bash
   rekall -f memoria.raw pslist
   ```

2. **pstree**
   Mostra a relação pai/filho dos processos:

   ```bash
   rekall -f memoria.raw pstree
   ```

3. **psscan** / **psxview**
   Usados para detectar processos ocultos ou inconsistências (comparando várias formas de listar processos):

   ```bash
   rekall -f memoria.raw psscan
   rekall -f memoria.raw psxview
   ```

   * `psxview` é especialmente legal porque mostra “várias visões” do mesmo processo; se ele some de uma visão, pode indicar tentativa de esconder.

4. **netscan** / **netstat**
   Para listar conexões de rede que estavam abertas:

   ```bash
   rekall -f memoria.raw netscan
   ```

5. **dlllist** / **modules**
   Para ver DLLs/módulos carregados por processo ou no kernel:

   ```bash
   rekall -f memoria.raw dlllist
   rekall -f memoria.raw modules
   ```

6. **handles**
   Lista handles abertos pelos processos – útil para ver arquivos, mutex, pipes:

   ```bash
   rekall -f memoria.raw handles
   ```

---

## 6. Hunting e detecção

O Rekall também tem comandos voltados para encontrar atividade suspeita. Dependendo da versão/plug-ins:

* checagem de injeção;
* verificação de seções de memória com permissões incomuns;
* listagem de drivers;

A ideia é a mesma: você primeiro entende o que está rodando (pslist/pstree) e depois busca o que **não deveria** estar ali.

---

## 7. Exportando resultados

Em forense, não basta “ver” no terminal – é preciso guardar. O Rekall permite exportar em formatos mais amigáveis (JSON, texto, às vezes CSV) usando parâmetros de output. Exemplo genérico:

```bash
rekall -f memoria.raw pslist --output text --output-file pslist.txt
```

Ou, dependendo da versão:

```bash
rekall -f memoria.raw pslist --format json --output pslist.json
```

Assim você anexa o resultado ao caso e consegue comparar depois com o Volatility.

---

## 8. Fluxo de trabalho sugerido

1. **Reconhecimento**

   ```bash
   rekall -f memoria.raw pslist
   rekall -f memoria.raw pstree
   ```

2. **Procura de ocultação**

   ```bash
   rekall -f memoria.raw psxview
   rekall -f memoria.raw psscan
   ```

3. **Rede e módulos**

   ```bash
   rekall -f memoria.raw netscan
   rekall -f memoria.raw modules
   rekall -f memoria.raw dlllist
   ```

4. **Salvar resultados**

   ```bash
   rekall -f memoria.raw pslist --output text --output-file ./saida/pslist.txt
   ```

5. **Correlacionar** com horários do incidente e demais evidências de disco.

---

## 9. Blocos operacionais

```text
comando: listar processos

rekall -f memoria.raw pslist
```

```text
comando: ver hierarquia de processos

rekall -f memoria.raw pstree
```

```text
comando: procurar processos escondidos

rekall -f memoria.raw psxview
rekall -f memoria.raw psscan
```

```text
comando: listar conexões de rede

rekall -f memoria.raw netscan
```

```text
comando: salvar saída para o caso

rekall -f memoria.raw pslist --output text --output-file ./pslist.txt
```

---

## 10. Boas práticas

* usar a mesma cópia do dump que está sendo usada no Volatility (para poder comparar);
* salvar todas as saídas em `./saida/` com nome de comando e data;
* registrar a versão do Rekall usada (algumas mudanças de plugins acontecem por versão);
* quando um comando não reconhecer o perfil, tentar rodar comandos de detecção primeiro;
* manter instruções de coleta no material de estudo, para deixar claro que o Rekall não coleta.

---

## 11. Comparação com Volatility

* **Volatility**:

  * comunidade maior;
  * mais material de aprendizado;
  * muitos plugins de hunting bem conhecidos.
* **Rekall**:

  * bom autodetect de perfil;
  * bom para integração em alguns ambientes;
  * sintaxe simples e direta;
  * útil para “segunda opinião” sobre o dump.

Saber os dois é vantajoso: se um não reconhece bem o dump, você tenta o outro.