---
title: "Volatility"
tags: [ferramenta, memoria, forense, volatility, ram, dfir]
status: rascunho
---

# Volatility

Este documento apresenta o **Volatility** como framework de **análise forense de memória (RAM)**. A função dele é extrair artefatos do estado em execução de um sistema (processos, conexões, módulos, código injetado, drivers) a partir de um **dump de memória** já coletado. É uma etapa diferente da análise de disco: aqui vemos **o que estava acontecendo naquela hora**.

Fluxo recomendado:

1. coletar a memória (dump) do host;
2. calcular hash e armazenar o dump com a evidência;
3. identificar o sistema operacional/imagem;
4. rodar os plugins do Volatility para listar processos, conexões, DLLs;
5. fazer hunting por malware (malfind, yarascan, ssdt, drivers);
6. documentar achados e horários.

---

## 1. Conceito

O Volatility é um conjunto de **plugins em Python** que entendem como o sistema operacional guarda estruturas na RAM (processos, EPROCESS, SSDT, módulos, sockets). Ele lê o arquivo de memória de forma **independente do sistema** onde o dump foi coletado. Isso significa:

- você pode analisar o dump em outra máquina;
- o sistema analisado não precisa estar ligado;
- você tem visibilidade do estado de execução do momento da coleta.

Atualmente existem duas grandes gerações:
- versões antigas (Volatility 2, Python 2) → exigiam perfil exato do SO;
- **Volatility 3 (Python 3)** → não usa mais o conceito de “perfil” do mesmo jeito; os plugins são separados por SO (windows.*, linux.*, mac.*) e o framework tenta resolver os símbolos automaticamente.

Neste material vamos considerar a sintaxe mais nova (Volatility 3).

---

## 2. Coleta de memória (pré-requisito)

Antes de usar o Volatility você precisa ter o **arquivo de memória**. Ele pode vir de:

- ferramentas de coleta em Windows: FTK Imager, DumpIt, win32dd/win64dd, Memoryze, FastDump;
- coleta em contexto de resposta a incidentes;
- memória extraída de máquina virtual:
  - VMware: `.vmem`
  - Hyper-V: `.bin`
  - Parallels: `.mem`
  - VirtualBox: `.sav` (parcial)

Na maioria dos casos você vai salvar isso como `.raw` ou vai apontar o Volatility direto para o arquivo do hypervisor. Depois da coleta:

1. guardar o dump em pasta do caso;
2. calcular hash do arquivo;
3. trabalhar na cópia.

---

## 3. Instalação e execução

Como o Volatility 3 é Python 3:

```bash
git clone https://github.com/volatilityfoundation/volatility3.git
cd volatility3
python3 vol.py -h
````

Dependências típicas:

* Python 3.5.3 ou superior
* `pefile`
* opcional: `yara-python`, `capstone` (para hunting mais avançado)

Para analisar memória Linux/macOS é comum ter de baixar **tabelas de símbolos** específicas.

A partir daí, todos os comandos seguem o mesmo padrão:

```bash
python3 vol.py -f caminho/para/memoria.raw windows.<plugin> [opções]
```

Note o prefixo do SO: `windows.`, `linux.`, `mac.`.

---

## 4. Identificando o alvo

Em edições antigas usava-se `imageinfo` para sugerir o perfil. No modelo atual a ideia é usar diretamente o plugin de informação do SO:

```bash
python3 vol.py -f memoria.raw windows.info
# ou
python3 vol.py -f memoria.raw linux.info
# ou
python3 vol.py -f memoria.raw mac.info
```

Isso ajuda a confirmar:

* versão do sistema;
* build;
* data/hora da captura (quando disponível);
* outras informações de contexto.

Saber o sistema certo é importante para escolher os plugins corretos (principalmente Windows).

---

## 5. Listando processos e estrutura de execução

Essa é quase sempre a primeira coisa a fazer: **quem estava rodando?**

Plugins principais em Windows:

1. **windows.pslist**

   ```bash
   python3 vol.py -f memoria.raw windows.pslist
   ```

   * lista de processos a partir da lista encadeada padrão;
   * pode não mostrar processos escondidos por rootkits.

2. **windows.psscan**

   ```bash
   python3 vol.py -f memoria.raw windows.psscan
   ```

   * faz varredura na memória procurando estruturas de processo;
   * encontra processos desvinculados da lista (bypass de pslist);
   * pode ter falsos positivos – precisa validar.

3. **windows.pstree**

   ```bash
   python3 vol.py -f memoria.raw windows.pstree
   ```

   * mostra a árvore de processos (quem é pai de quem);
   * ótimo para ver processos estranhos nascendo do `explorer.exe` ou do `winword.exe`.

4. **windows.dlllist**

   ```bash
   python3 vol.py -f memoria.raw windows.dlllist
   ```

   * lista DLLs carregadas por cada processo;
   * útil para ver bibliotecas suspeitas ou injeção.

5. **windows.netstat**

   ```bash
   python3 vol.py -f memoria.raw windows.netstat
   ```

   * tenta listar conexões de rede ativas;
   * em alguns dumps pode ser instável; se não funcionar, complementar com outra abordagem.

Com esses plugins você já consegue responder:

* que processo é suspeito;
* quem é o pai desse processo;
* quais DLLs ele carregou;
* se estava conectado a algum endereço.

---

## 6. Hunting e detecção de malware

Depois do “quem estava rodando”, vem o “o que estava escondido”. O Volatility tem plugins pensados para isso:

1. **windows.malfind**

   ```bash
   python3 vol.py -f memoria.raw windows.malfind
   ```

   * procura regiões de memória com características de injeção (RWE, RX, sem arquivo mapeado);
   * mostra PID, endereço e trechos em hex/ASCII;
   * se aparecer `MZ` em área injetada, atenção.

2. **windows.yarascan**

   ```bash
   python3 vol.py -f memoria.raw windows.yarascan --yara regras.yar
   ```

   * permite varrer o dump com regras YARA;
   * muito bom para procurar família específica de malware.

3. **windows.ssdt**

   ```bash
   python3 vol.py -f memoria.raw windows.ssdt
   ```

   * lista a System Service Descriptor Table;
   * usada para detectar hooks (técnica de rootkit);
   * nem todo hook é malicioso – precisa de análise.

4. **windows.modules** e **windows.driverscan**

   ```bash
   python3 vol.py -f memoria.raw windows.modules
   python3 vol.py -f memoria.raw windows.driverscan
   ```

   * listam módulos/ drivers de kernel carregados;
   * úteis quando o malware está em driver.

A ideia é: primeiro olhar o “normal” (processos, conexões), depois rodar o “caça bicho” (malfind, yarascan, ssdt).

---

## 7. Trabalhando com linha do tempo da captura

Nos dumps em que o horário de aquisição está embutido, o plugin de info mostra quando a memória foi capturada. Isso é útil para:

* cruzar com horário de execução do processo suspeito;
* ver se o processo tinha conexão na hora;
* montar narrativa do incidente.

Sempre que possível, **anote a data/hora da aquisição** do dump no seu relatório.

---

## 8. Fluxo de trabalho sugerido

1. **Identificar o sistema**

   ```bash
   python3 vol.py -f memoria.raw windows.info
   ```
2. **Listar processos (duas formas)**

   ```bash
   python3 vol.py -f memoria.raw windows.pslist
   python3 vol.py -f memoria.raw windows.psscan
   ```
3. **Ver a árvore**

   ```bash
   python3 vol.py -f memoria.raw windows.pstree
   ```
4. **Ver conexões (se suportado)**

   ```bash
   python3 vol.py -f memoria.raw windows.netstat
   ```
5. **Hunting**

   ```bash
   python3 vol.py -f memoria.raw windows.malfind
   python3 vol.py -f memoria.raw windows.yarascan --yara regras.yar
   ```
6. **Kernel / drivers (se suspeitar de rootkit)**

   ```bash
   python3 vol.py -f memoria.raw windows.ssdt
   python3 vol.py -f memoria.raw windows.modules
   python3 vol.py -f memoria.raw windows.driverscan
   ```
7. **Documentar**: PID suspeito, processo pai, caminho, DLLs carregadas, IPs conectados, horário.

---

## 9. Blocos operacionais

```text
comando: reconhecimento inicial de dump Windows

python3 vol.py -f memoria.raw windows.info
python3 vol.py -f memoria.raw windows.pslist
python3 vol.py -f memoria.raw windows.pstree
```

```text
comando: caça a processos escondidos

python3 vol.py -f memoria.raw windows.psscan
python3 vol.py -f memoria.raw windows.malfind
```

```text
comando: varrer memória com YARA

python3 vol.py -f memoria.raw windows.yarascan --yara ./regras/malware.yar
```

```text
comando: olhar drivers e possíveis rootkits

python3 vol.py -f memoria.raw windows.modules
python3 vol.py -f memoria.raw windows.driverscan
python3 vol.py -f memoria.raw windows.ssdt
```

---

## 10. Boas práticas

* coletar memória o mais cedo possível (processos podem sumir);
* trabalhar sobre cópia do dump;
* usar nomes claros: `2025-caso01-mem.raw`;
* registrar versão do Volatility e plugins usados;
* salvar a saída dos comandos (redirecionar para `.txt`) para anexar ao relatório;
* quando um plugin não funciona (ex.: netstat em build antigo), registrar isso como limitação;
* se o dump for de Linux/macOS, baixar e apontar para as tabelas de símbolos adequadas.

---

## 11. Comparação com outras ferramentas de memória

* **Volatility** – padrão de mercado, enorme quantidade de plugins, multiplataforma.
* **Rekall** – também faz memória forense, em alguns casos mais atualizado para algumas estruturas.
* **Ferramentas de coleta** (DumpIt, FTK Imager, Redline) – coletam, mas não analisam com a mesma profundidade.
* **Ferramentas comerciais** – às vezes têm interface melhor, mas o raciocínio é o mesmo: listar processos, conexões, módulos e hunting.

---

## 12. Conclusão

Volatility é a peça de **análise** dentro do fluxo de forense de memória. Ele assume que você já conseguiu o dump e quer descobrir “quem estava rodando” e “o que estava escondido” naquele momento. Dominar os plugins básicos (info, pslist, psscan, pstree, netstat, dlllist) e os plugins de hunting (malfind, yarascan, ssdt, modules, driverscan) é suficiente para conduzir a maior parte das investigações de RAM e encaixar o resultado com o que você já coletou no disco, nos logs e no Registro.