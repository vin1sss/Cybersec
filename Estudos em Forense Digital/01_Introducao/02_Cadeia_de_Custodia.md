---
title: "Cadeia de Custódia"
tags: [introducao, cadeia-de-custodia, evidencia, forense, integridade]
status: rascunho
---

# Cadeia de Custódia

A cadeia de custódia é o **registro contínuo e documentado** de tudo o que acontece com uma evidência, desde o momento em que ela é coletada até sua apresentação (relatório, processo administrativo ou judicial). Ela responde a perguntas simples:

- Quem coletou?
- O que foi coletado?
- Quando foi coletado?
- Onde foi armazenado?
- Quem teve acesso depois?

Se alguma dessas respostas não existe ou está incompleta, a evidência pode ser **questionada** - mesmo que tecnicamente esteja perfeita.

---

## 1. Objetivo

Garantir e demonstrar que **a evidência apresentada é a mesma que foi coletada**, sem adulteração, perda ou inclusão de novos dados.  
Em forense digital isso é ainda mais crítico porque arquivos podem ser copiados e alterados com facilidade; por isso, além do registro de custódia, usamos **hashes** para provar integridade.

---

## 2. Quando iniciar

A cadeia de custódia começa **no momento da apreensão/coleta** - não depois.  
Exemplo: pegou o notebook? Já registra. Fez dump de memória? Já registra. Gerou imagem de disco? Já registra.

---

## 3. Informações mínimas do registro

Um formulário de cadeia de custódia (digital ou em papel) costuma ter pelo menos:

1. **Identificação da evidência**
   - número do caso / processo
   - descrição (ex.: “HD Seagate 1TB, SN: XYZ123”, “arquivo LOGS-host01-2025-11-05.tar.gz”, “smartphone Samsung SM-A125M”)
   - origem (local/apreensão)

2. **Dados da coleta**
   - nome e função de quem coletou
   - data e hora da coleta
   - local da coleta
   - método/ferramenta (ex.: “imagem criada com FTK Imager 4.x em formato E01”)

3. **Integridade**
   - algoritmo de hash (MD5, SHA-256…)
   - valor do hash
   - tamanho do arquivo/imagem

4. **Transferências / custódia**
   - data e hora da entrega/recebimento
   - de quem → para quem
   - motivo (análise, armazenamento, perícia complementar)
   - assinatura ou identificação de ambos

5. **Armazenamento**
   - onde ficou guardado (sala/cofre/pasta restrita)
   - número de lacre, se houver

---

## 4. Exemplo de linha de custódia

| Data/Hora           | Ação              | Responsável (entrega) | Responsável (recebe) | Descrição / Observação                                   |
|---------------------|-------------------|------------------------|-----------------------|----------------------------------------------------------|
| 05/11/2025 14:10    | Coleta            | Insp. M. Silva         | —                     | Notebook Dell, SN ABC123, apreendido na sala 02          |
| 05/11/2025 15:05    | Imagem de disco   | Perito V. Santos       | —                     | Imagem E01 criada com FTK Imager; SHA-256: `...`         |
| 05/11/2025 15:20    | Armazenamento     | Perito V. Santos       | Tec. Arquivo J. Lima  | Arquivado em cofre digital pasta “Caso 2025-11-05”       |

Esse tipo de tabela pode viver em uma única nota do Obsidian ou ser exportada quando você for montar o relatório.

---

## 5. Cadeia de custódia e arquivos digitais

No caso de **imagens forenses, dumps de memória e pacotes de logs**, além do registro de quem manipulou, é fundamental:

1. **Calcular o hash imediatamente após criar o arquivo**;
2. **Salvar o hash junto da evidência** (ex.: `evidencia.img` + `evidencia.img.sha256`);
3. **Registrar no formulário** esse valor: isso amarra o arquivo ao registro de custódia.

Assim, se alguém questionar, você mostra:
- o arquivo que está apresentando;
- o hash calculado na data da coleta;
- o mesmo hash recalculado agora.

Se os valores batem, você mostra que o arquivo não foi modificado durante a cadeia.

---

## 6. Boas práticas

- **Um item, um registro**: não misture vários dispositivos diferentes num mesmo campo descritivo.
- **Escreva como alguém de fora leria**: “HD 1TB preto” não é bom; “HD Seagate Barracuda 1TB, SATA, SN: XYZ123” é melhor.
- **Não deixe campos em branco**: se algo não se aplica, escreva “N/A”.
- **Controle de versões**: se a evidência gerou cópias (ex.: imagem dividida em partes), todas as partes precisam ter hash e entrar na custódia.
- **Acesso mínimo**: quanto menos gente manipular a evidência, mais simples de defender.

---

## 7. Relação com outras notas

- Coleta de disco: [[01_Imagem_de_Disco]]  
  (usa cadeia de custódia para registrar imagem e hash)
- Coleta de memória: [[02_Memoria_Volatil]]  
  (usa cadeia de custódia para registrar o dump)
- Coleta de logs: [[03_Logs_de_Sistema]]  
  (usa cadeia de custódia para registrar o pacote de logs coletados)

---

## 8. Conclusão

A cadeia de custódia não é um “extra”: ela é o que **torna a evidência defensável**.  
Sem ela, mesmo a melhor análise pode ser descartada. Com ela, você consegue mostrar que a evidência:
1. foi obtida de forma regular;
2. não foi alterada;
3. foi manipulada apenas por pessoas autorizadas.