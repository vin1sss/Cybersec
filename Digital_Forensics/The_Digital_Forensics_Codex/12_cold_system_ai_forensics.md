# 12 Forense em sistemas frios e inteligência artificial

## 12.1 Ataques *cold boot* e forense em sistemas desligados

Um **cold boot attack** (ou ataque por reinicialização a frio) é um método de extração de dados em que o invasor, com acesso físico ao equipamento, desliga e reinicia o computador para copiar o conteúdo da memória RAM.  Esse ataque explora a **remanência de dados**: a RAM mantém cargas elétricas por alguns segundos ou minutos após a perda de energia, permitindo que chaves de criptografia e outras informações sensíveis sejam recuperadas【55890635480540†L143-L165】.  

O procedimento típico envolve ligar o sistema a partir de um dispositivo externo (USB ou rede) que realiza um *dump* completo da memória.  Como a criptografia de disco protege dados apenas quando o disco está inativo, atacar a RAM contorna essa proteção e permite obter chaves de volume ou senhas.  

**Contramedidas:**

- Proteger fisicamente o equipamento para impedir acesso não autorizado;
- Utilizar hardware com criptografia de memória ou tecnologias de *Trusted Platform Module* (TPM) que armazenam chaves fora da RAM;
- Configurar o sistema para limpar chaves de criptografia ao entrar em modo de suspensão ou hibernação;
- Evitar deixar máquinas em estado de sleep quando contêm informações sensíveis.

Forenses que investigam dispositivos comprometidos por ataques a frio devem considerar a possibilidade de que a memória tenha sido copiada e buscar evidências em dispositivos removíveis ou logs de boot.  A coleta imediata da RAM, conforme descrito no Capítulo 8, é crucial para preservar qualquer vestígio.

## 12.2 DiskFiltration e exfiltração acústica

Além dos ataques que exploram a remanência da RAM, pesquisadores demonstraram que computadores isolados por **air‑gap** também podem ser violados por canais de comunicação inesperados.  O estudo **DiskFiltration** descreve um método de exfiltração em que malware controla o braço atuador do disco rígido (HDD) para gerar sinais acústicos que modulam dados【73998737484145†L14-L44】.  Esses sinais, emitidos sem necessidade de alto‑falantes, podem ser captados por dispositivos próximos (smartphones ou laptops) equipados com microfones.

O artigo explica que a técnica funciona mesmo quando o computador não possui hardware de áudio e está fisicamente isolado: o malware realiza operações de **seek** no HDD para produzir sons em frequências específicas【73998737484145†L14-L32】.  Esses sons são modulados com bits de informação e capturados a até **dois metros** de distância, alcançando taxas de **180 bits/minuto**【73998737484145†L39-L44】.  Uma vez decodificados, os dados (por exemplo, chaves, senhas ou registros) podem ser transmitidos ao atacante.

Investigadores devem considerar cenários de exfiltração por **canais encobertos** ao analisar sistemas críticos.  A detecção pode envolver monitorar ruídos anômalos de HDD, revisar registros de acesso físico e procurar malware especializado em gerar padrões de escrita incomuns.  Proteções incluem desativar a alimentação de discos em ambientes de alta segurança, utilizar SSDs (que não emitem ruído mecânico) ou isolar fisicamente o equipamento para reduzir captação acústica.

## 12.3 Inteligência Artificial aplicada à forense digital

## 12.2 Inteligência Artificial aplicada à forense digital

O rápido avanço da **Inteligência Artificial (IA)** vem transformando a perícia digital.  Artigo da Marymount University destaca que algoritmos de IA podem automatizar a análise de grandes volumes de dados, acelerar investigações e reduzir erros humanos【244175373537925†L115-L159】.  A IA ajuda a:

- **Processar evidências em larga escala:** ferramentas de *machine learning* podem filtrar milhões de registros ou mensagens, identificar correlações e apresentar prioridades a analistas humanos;
- **Detectar padrões e anomalias:** algoritmos identificam comportamentos incomuns em logs de rede, registros de sistema e interações de usuários, auxiliando na detecção precoce de intrusões【244175373537925†L170-L200】;
- **Análise de imagens e vídeos:** redes neurais especializadas melhoram a qualidade de imagens de baixa resolução, reconhecem rostos e objetos, extraem texto (OCR) e indexam grandes repositórios visuais【244175373537925†L115-L159】;
- **Investigações de cibercrime:** IA analisa tráfego de rede e atributos de malware para classificar ameaças e identificar famílias de código malicioso【244175373537925†L170-L200】;
- **Apoio a disciplinas tradicionais:** técnicas de IA estão sendo empregadas em análises de DNA, impressões digitais, patologia forense, avaliação de risco e detecção de mentiras, trazendo maior velocidade e precisão【244175373537925†L170-L200】.

Apesar das vantagens, a aplicação da IA levanta desafios éticos, como o risco de vieses nos algoritmos, transparência na tomada de decisões e possibilidade de falsas correlações.  Peritos devem validar os resultados, compreender os limites dos modelos e garantir que ferramentas de IA sirvam como auxílio – e não substituição – à análise humana.
