---
title: "RegRipper"
tags: [ferramenta, windows, registro, artefatos, regripper, hives]
status: rascunho
---

# RegRipper

Este documento descreve o uso do **RegRipper** para extração e interpretação rápida de artefatos do Registro do Windows a partir de hives coletados de uma máquina em análise. O objetivo é automatizar aquilo que seria demorado fazer manualmente com um editor de registro offline: abrir o hive, navegar até a chave, localizar valores e interpretar datas.

Ele complementa o estudo sobre Registro em:
- `03_Analise_de_Sistemas/01_Windows/02_Registro_Hives.md`
- e os artefatos típicos de execução, usuários e persistência.

Fluxo recomendado:

1. coletar os hives de Registro do disco/imagem (por exemplo: `NTUSER.DAT`, `SAM`, `SYSTEM`, `SOFTWARE`);
2. trabalhar sempre na cópia (nunca no hive original da imagem);
3. rodar o RegRipper com o perfil/plug-ins adequados;
4. analisar o relatório de saída em texto;
5. correlacionar com outros artefatos do caso.

---

## 1. Conceito

O RegRipper é uma ferramenta forense criada para:
- ler hives de Registro do Windows offline;
- executar uma coleção de **plug-ins** que já sabem onde ficam chaves importantes;
- produzir um **relatório em texto** com os dados extraídos e já parcialmente interpretados (datas, caminhos, MRUs, execuções recentes).

Em vez de você abrir `NTUSER.DAT` e procurar manualmente as chaves de "Run" ou "UserAssist", o plug-in já faz isso e escreve o resultado.

---

## 2. O que o RegRipper consegue extrair

Depende dos plug-ins, mas os mais comuns incluem:

- programas executados recentemente (UserAssist, MUICache);
- programas configurados para iniciar com o sistema (Run, RunOnce);
- evidências de USBs conectados (em hives de sistema);
- informações de usuário e de ambiente;
- aplicações instaladas;
- MRUs (Most Recently Used) de diversos componentes;
- chaves de persistência e de malware conhecidas.

Isso torna o RegRipper muito útil na fase de “o que este usuário/sistema andou fazendo?” sem precisar montar o Windows.

---

## 3. Hives mais importantes para usar com RegRipper

Em uma coleta de disco Windows, estes hives são os principais:

- `C:\Windows\System32\config\SYSTEM`
- `C:\Windows\System32\config\SOFTWARE`
- `C:\Windows\System32\config\SAM`
- `C:\Windows\System32\config\SECURITY`
- `C:\Users\<usuario>\NTUSER.DAT`
- `C:\Users\<usuario>\AppData\Local\Microsoft\Windows\USRCLASS.DAT`

Na imagem montada, o caminho real pode variar (por exemplo, `/mnt/img/Windows/System32/config/SOFTWARE`), mas a ideia é sempre copiar o hive para uma pasta de trabalho e rodar o RegRipper sobre essa cópia.

---

## 4. Estrutura do RegRipper

O RegRipper costuma vir com:

- um executável/launcher (ex.: `rip.exe` no Windows);
- uma pasta de plug-ins (`.pl`) que sabem extrair artefatos específicos;
- perfis (profiles) que agrupam vários plug-ins por tipo de hive.

Você pode:
1. rodar um único plug-in sobre um hive;
2. rodar um profile inteiro (mais comum), que executa vários plug-ins na sequência e gera um relatório completo.

---

## 5. Uso básico (linha de comando)

Exemplo clássico de uso (sintaxe pode variar conforme a versão):

```text
rip.exe -r C:\caso01\hives\NTUSER.DAT -f ntuser > ntuser_reporte.txt
````

Explicando:

* `-r` aponta para o hive;
* `-f` indica o “perfil” (profile) adequado para aquele hive;
* o resultado é redirecionado para um `.txt` para leitura posterior.

Para o hive `SOFTWARE`, por exemplo:

```text
rip.exe -r C:\caso01\hives\SOFTWARE -f software > software_reporte.txt
```

Assim você já sai com um relatório pronto para anexar ao caso.

---

## 6. Quando usar cada hive

* **NTUSER.DAT** - artefatos de usuário logado: programas usados, MRUs, shellbags, associações, atalhos. Útil para saber o que aquela conta fez.
* **USRCLASS.DAT** - artefatos de interface e shell do usuário (complementa o NTUSER.DAT).
* **SOFTWARE** - informações de software instalado, versão de Windows, configurações de sistema.
* **SYSTEM** - informações de hardware, control sets, USBs, timezone.
* **SAM** - contas locais, SIDs.
* **SECURITY** - políticas de segurança.

O RegRipper tem plug-ins específicos para cada um.

---

## 7. Interpretação do relatório

O relatório do RegRipper é puro texto. Ele geralmente traz:

* cabeçalho com nome do plug-in;
* caminho da chave lida;
* valores encontrados;
* data/hora convertida;
* às vezes comentários do próprio plug-in.

Exemplo fictício de saída:

```text
Plugin: userassist
Key: HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist\...
Last Write: 2025-11-06 12:03:11Z
Program: C:\Users\vinicius\Desktop\sys.exe
Run Count: 3
```

Isso já diz: este usuário executou `sys.exe` 3 vezes, última em tal horário.

O passo seguinte é correlacionar esse horário com outros artefatos (event logs, prefetch, timeline de disco).

---

## 8. Integração com o restante do exame

O RegRipper não substitui a extração de Registro que você faz manualmente, mas acelera:

* você pode rodar o RegRipper logo após montar a imagem;
* guardar todos os `.txt` em uma pasta do caso;
* e, se precisar de algo muito específico que o plug-in não trouxe, abrir o hive no Registry Explorer ou em outra ferramenta.

Ele também é útil quando você quer **padronizar a análise dos alunos**: todos rodam o mesmo comando sobre o mesmo hive e comparam resultados.

---

## 9. Blocos operacionais

```text
comando: extrair artefatos de NTUSER.DAT com RegRipper

1. Copiar o hive NTUSER.DAT da imagem para a máquina de análise
2. Executar:
   rip.exe -r NTUSER.DAT -f ntuser > ntuser_reporte.txt
3. Abrir ntuser_reporte.txt e procurar por:
   - programas executados
   - MRUs
   - chaves de persistência
4. Anotar horários e caminhos relevantes
```

```text
comando: extrair artefatos de SOFTWARE com RegRipper

1. Copiar o hive SOFTWARE da imagem
2. Executar:
   rip.exe -r SOFTWARE -f software > software_reporte.txt
3. Verificar informações de sistema e software instalado
```

```text
comando: listar plug-ins disponíveis

rip.exe -l
# ou
rip.exe -h
```

---

## 10. Boas práticas

* trabalhar sempre sobre cópia dos hives;
* nomear o relatório de saída com o nome do hive (facilita correlacionar);
* registrar a versão do RegRipper utilizada;
* guardar os `.txt` junto com o restante da documentação do caso;
* se um plug-in não trouxe o que você queria, verificar se existe versão mais nova ou plug-in específico para aquela chave.

---

## 11. Comparação rápida

* **RegRipper** - rápido, automatiza chaves conhecidas, ótimo para exame inicial de Registro.
* **Registry Explorer / ferramentas GUI** - bom para navegação manual, comparação de hives e buscas específicas.
* **Ferramentas de linha de comando (reg.exe offline, regipy)** - úteis para scripts e para Linux.

O importante é entender que o RegRipper já vem com o conhecimento “de onde estão as chaves forenses” embutido nos plug-ins, o que economiza tempo e reduz erros de localização de chave.