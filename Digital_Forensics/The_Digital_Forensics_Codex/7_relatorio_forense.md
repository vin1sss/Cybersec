# 7 Relatório forense digital

O relatório final é o produto do trabalho pericial e deve apresentar de forma clara, objetiva e estruturada todas as atividades realizadas e as conclusões obtidas.  Um bom relatório é compreensível para pessoas sem formação técnica, resistente a questionamentos jurídicos e suficientemente detalhado para permitir a reprodutibilidade dos procedimentos.

## 7.1 Principais elementos do relatório

1. **Identificação**: capa com título do caso, número de processo ou investigação, nome do perito ou equipe, data e assinatura.  
2. **Objetivo**: descrição do propósito da perícia — por exemplo, investigar um vazamento de dados, determinar se houve acesso não autorizado ou reconstruir a atividade de um usuário.  
3. **Escopo e limitações**: delimitação do que foi examinado (dispositivos, períodos de tempo) e o que estava fora do escopo.  Quaisquer limitações (ex.: dados corrompidos, falta de logs) devem ser explicitadas para contextualizar as conclusões.  
4. **Metodologia**: descrição dos procedimentos usados, incluindo ferramentas, versões, parâmetros e motivos da escolha.  Mencione como a evidência foi adquirida (formato de imagem), como os hashes foram calculados e quais técnicas de análise foram aplicadas【38030209527614†L266-L287】.  
5. **Cronologia dos eventos**: apresentação dos principais acontecimentos em ordem temporal, com referência às fontes de cada timestamp (sistema de arquivos, logs de eventos, prefetch, registro, dump de memória)【775749847023820†L74-L104】【915223127759981†L117-L163】.  Use tabelas ou gráficos para facilitar a visualização.  A conversão de timestamps para o fuso horário relevante deve ser explicitada【764236959555076†L48-L87】.  
6. **Evidências encontradas**: descrição detalhada dos arquivos, registros, logs e artefatos relevantes.  Para cada evidência, inclua localização (caminho), hash, data/hora de criação/modificação, e uma breve explicação de sua relevância.  Quando aplicável, transcreva trechos de e‑mails, mensagens ou strings encontradas, respeitando privacidade e legislações vigentes.  
7. **Análises e interpretações**: explique como as evidências sustentam as conclusões.  Por exemplo, correlacione um arquivo prefetch com um registro de execução, um endereço IP em log com um e‑mail suspeito ou a presença de um malware detectado em dump de memória.  Destaque quaisquer inconsistências ou sinais de antiforense (como discrepâncias de timestamps ou uso de ferramentas de limpeza).  
8. **Conclusões**: apresente as respostas às perguntas da investigação, indicando se as hipóteses foram confirmadas ou refutadas.  As conclusões devem ser baseadas nas evidências documentadas e não incluir suposições.  Se houver vários cenários possíveis, descreva cada um e indique o mais provável.  
9. **Recomendações**: quando apropriado, sugira medidas de mitigação ou prevenção (por exemplo, reforço de políticas de senha, atualização de sistemas, segmentação de rede, implementação de logs adequados).  
10. **Apêndices**: inclua a lista completa de evidências, tabelas de hash, relatórios de ferramentas, cópias de capturas de tela, tabelas de conversão de timestamps e qualquer material complementar.

## 7.2 Boas práticas na redação do relatório

* **Imparcialidade e objetividade**: o perito não deve assumir um papel de acusador; suas conclusões devem se limitar aos fatos observados.  A linguagem deve ser neutra e sem adjetivos subjetivos.  
* **Clareza**: use termos técnicos apenas quando necessários e inclua explicações ou glossário para facilitar a compreensão de leitores não técnicos.  Parágrafos curtos e listas auxiliam na leitura.  
* **Coesão e lógica**: organize as seções de modo que o leitor compreenda o caminho percorrido — desde a coleta até as conclusões.  
* **Citações e referências**: quando apropriado, referencie normas, literatura e fontes públicas (por exemplo, NIST SP 800‑86, artigos especializados) para justificar métodos e interpretações【270434847143426†L129-L161】.  
* **Integridade**: não omita dados que contradigam a hipótese principal.  Inconsistências devem ser relatadas e, se possível, explicadas.  
* **Proteção de dados sensíveis**: o relatório pode conter informações pessoais; portanto, siga as leis de proteção de dados, anonimização e minimização de dados.  
* **Reprodutibilidade**: forneça detalhes suficientes para que outro profissional possa repetir os procedimentos e chegar às mesmas conclusões.  
* **Assinatura digital**: utilize assinaturas digitais ou certificações baseadas em hash para garantir a autenticidade do relatório e detectar alterações futuras【38030209527614†L343-L347】.

Um relatório bem estruturado aumenta a confiança do tribunal e das partes interessadas e assegura que o trabalho pericial seja valorizado como evidência robusta.  Ao aplicar as boas práticas descritas, o perito demonstra profissionalismo e reforça a credibilidade do processo investigativo.