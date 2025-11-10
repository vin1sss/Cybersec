---
title: "regipy e regrippy (registro Windows em Python)"
tags: [ferramenta, windows, registro, artefatos, regipy, regrippy, python]
status: rascunho
---

# regipy e regrippy (registro Windows em Python)

Este documento apresenta o uso de **regipy** e **regrippy** como alternativas em Python para analisar hives do Registro do Windows de forma offline, principalmente em ambiente Linux ou em pipelines de automação. A ideia é fazer com Python o que o RegRipper faz com plug-ins: abrir o hive, percorrer chaves conhecidas e extrair artefatos forenses importantes.

É especialmente útil quando:
- a imagem foi montada em Linux (`/mnt/img/Windows/System32/config/...`);
- o analista quer integrar a análise de Registro a um script ou notebook;
- o ambiente não tem RegRipper instalado;
- deseja-se fazer buscas mais livres no hive.

---

## 1. Conceito

O **regipy** é uma biblioteca Python que entende o formato dos hives de Registro do Windows e permite:
- abrir o hive direto do disco;
- navegar por chaves e valores;
- resolver tipos comuns (strings, timestamps);
- percorrer o hive inteiro e filtrar registros.

O **regrippy** é um conjunto de ferramentas/CLI que usa o regipy por baixo para facilitar o uso no terminal. Assim você ganha:
- comandos prontos para listar chaves;
- busca por padrões;
- execução com poucos argumentos.

Em resumo: regipy é a base em Python, regrippy é o jeito fácil de usar.

---

## 2. Cenários de uso

Use regipy/regrippy quando:

1. estiver no Linux analisando uma imagem Windows montada;
2. quiser criar um script que leia vários hives de vários casos;
3. precisar procurar por termos específicos em todo o hive;
4. quiser extrair artefatos que não têm plug-in pronto no RegRipper;
5. quiser trabalhar em cima de caminhos de arquivos do tipo:
   - `/mnt/img/Windows/System32/config/SOFTWARE`
   - `/mnt/img/Users/aluno/NTUSER.DAT`

Eles se encaixam bem naquele fluxo de montar a imagem, copiar hives e analisar.

---

## 3. Hives a serem analisados

Os mesmos hives principais do Windows:

- `SYSTEM`
- `SOFTWARE`
- `SAM`
- `SECURITY`
- `NTUSER.DAT` (por usuário)
- `USRCLASS.DAT` (por usuário)

Montou a imagem? Então o caminho costuma ser algo como:

```text
/mnt/img/Windows/System32/config/SOFTWARE
/mnt/img/Users/vinicius/NTUSER.DAT
````

Basta apontar o regipy/regrippy para esses arquivos.

---

## 4. Instalação básica

Em um ambiente Python:

```bash
pip install regipy
# se estiver usando a CLI do regrippy, seguir as instruções do repositório
```

Alguns pacotes podem exigir Python 3.x e ambiente virtual. Em laboratório é comum instalar em um venv só para forense.

---

## 5. Uso típico com regipy (Python)

Exemplo mínimo em Python para listar subchaves:

```python
from regipy import RegistryHive

hive = RegistryHive('/mnt/img/Windows/System32/config/SOFTWARE')
key = hive.get_key('Microsoft\\Windows\\CurrentVersion')
for subkey in key.iter_subkeys():
    print(subkey.name)
```

Neste exemplo:

* abrimos o hive SOFTWARE;
* fomos até a chave `Microsoft\Windows\CurrentVersion`;
* listamos subchaves.

A partir daí você pode pegar valores, timestamps e montar sua própria lógica de relatório.

---

## 6. Uso típico com regrippy (CLI)

Dependendo da versão/fork que você estiver usando, a CLI costuma ter comandos do tipo:

```bash
regrippy print --hive /mnt/img/Windows/System32/config/SOFTWARE --key "Microsoft\Windows\CurrentVersion"
```

Ou ainda:

```bash
regrippy find --hive /mnt/img/Users/aluno/NTUSER.DAT --pattern "Run"
```

A ideia é:

* `print` para mostrar uma chave/valor;
* `find` para varrer atrás de nomes que combinem com o padrão.

Isso é ótimo para achar rapidamente chaves de persistência como `...CurrentVersion\Run`.

---

## 7. O que dá para extrair

Com regipy/regrippy você consegue extrair praticamente os mesmos artefatos de registro que veria manualmente, por exemplo:

* programas iniciados automaticamente (Run, RunOnce);
* informações de fuso horário e system root (SYSTEM);
* aplicações instaladas (SOFTWARE);
* dados de usuário e shell (NTUSER.DAT, USRCLASS.DAT);
* rastros de execução armazenados no perfil.

A diferença é que você pode acrescentar lógica sua: por exemplo, ler todas as chaves `Run` de todos os usuários e gerar um JSON.

---

## 8. Integração com imagem montada

Fluxo comum em Linux:

```text
1. Montar a imagem em /mnt/img (somente leitura)
2. Copiar os hives que interessam para uma pasta de trabalho:
   cp /mnt/img/Windows/System32/config/SOFTWARE ./hives/
   cp /mnt/img/Users/vinicius/NTUSER.DAT ./hives/
3. Rodar regipy/regrippy nesses arquivos copiados
4. Guardar o resultado (txt/json) junto do caso
```

Dessa forma você mantém a imagem intacta e trabalha sempre em cima da cópia dos hives.

---

## 9. Blocos operacionais

```text
comando: listar subchaves de SOFTWARE com regrippy

regrippy print --hive ./hives/SOFTWARE --key "Microsoft\\Windows\\CurrentVersion"
```

```text
comando: procurar chaves de inicialização com regrippy

regrippy find --hive ./hives/NTUSER.DAT --pattern "Run"
# analisar a saída e ver quais caminhos apontam para executáveis suspeitos
```

```text
comando: script rápido em Python para pegar uma chave

from regipy import RegistryHive

hive = RegistryHive('./hives/NTUSER.DAT')
key = hive.get_key('Software\\Microsoft\\Windows\\CurrentVersion\\Run')
for val in key.iter_values():
    print(val.name, val.value)
```

---

## 10. Boas práticas

* trabalhar sobre cópias dos hives, não sobre os arquivos montados direto;
* registrar de onde o hive veio (caminho dentro da imagem e caminho no disco de análise);
* salvar a saída dos comandos em arquivos de texto para anexar ao relatório do caso;
* quando a chave não existir, registrar isso também (é achado negativo);
* manter a mesma versão do regipy/regrippy em todos os alunos/analistas para evitar diferenças de saída.

---

## 11. Comparação rápida

* **RegRipper** - mais pronto, grande coleção de plug-ins, ideal para Windows e para quem quer relatório direto.
* **regipy/regrippy** - mais flexível, bom em Linux, bom para integrar com scripts e automação.
* **Registry Explorer e afins** - bons para navegação manual e comparação de hives.

Usar os três dá cobertura completa: automático (RegRipper), scriptável (regipy) e visual (Registry Explorer).