# 3 Obtendo e analisando uma evidência digital

Este capítulo descreve as técnicas de aquisição de dados, validação de imagens forenses e métodos de análise de evidências digitais em mídias diversas.

## 3.1 Formatos de arquivo de imagem forense

Para examinar um disco ou dispositivo sem alterar o original, os analistas criam uma **imagem forense**, isto é, uma cópia bit‑a‑bit completa da mídia.  Os principais formatos são:

1. **RAW (DD)**: uma cópia exata sem compactação.  Contém todos os setores, incluindo espaço não alocado e slack.  Pode gerar arquivos muito grandes, mas é suportado universalmente.
2. **EnCase (E01)**: formato proprietário da Guidance Software.  Suporta compactação, divisão em partes e armazenamento de metadados como hashes e notas.  Amplamente utilizado em laboratórios forenses.
3. **Advanced Forensic Format (AFF)**: formato aberto que oferece compactação e verificação de integridade.  Armazena metadados e permite a segmentação de imagens.
4. **Proprietários de ferramentas**: alguns softwares (Cellebrite, Magnet AXIOM) criam formatos próprios para dispositivos móveis ou nuvens, combinando dados brutos e relatórios.  A escolha do formato deve considerar compatibilidade com as ferramentas que serão utilizadas na análise e requisitos legais.

## 3.2 Validação do arquivo de imagem forense

Após a criação da imagem, é fundamental comprovar que ela é idêntica ao original.  Isso é feito calculando e comparando hashes (MD5, SHA‑1 ou SHA‑256) do disco original e da imagem.  Se os valores coincidirem, a integridade está preservada【38030209527614†L266-L287】.  A documentação deve incluir:  
- Algoritmo de hash utilizado;  
- Valor de hash da mídia original;  
- Valor de hash da imagem;  
- Data/hora da aquisição;  
- Ferramentas utilizadas (incluindo versões);  
- Nome do operador.

## 3.3 Captura de memória volátil

A memória RAM contém dados dinâmicos que desaparecem ao desligar o equipamento.  Em incidentes de *malware fileless* ou para recuperar chaves de criptografia e senhas, é crucial **coletar um dump de memória** antes de qualquer desligamento【347394763141159†L573-L618】.  O processo geralmente segue estes passos:

1. **Isolar o sistema da rede** para impedir a comunicação com atacantes.  
2. **Selecionar uma ferramenta de captura** compatível (Magnet RAM Capturer, Belkasoft RAM Capturer, FTK Imager, WinPMem).  
3. **Guardar a saída em mídia externa** através de um bloqueador de escrita.  
4. **Calcular o hash** do arquivo gerado para comprovar integridade.  
5. **Documentar** data, hora e comandos utilizados.

Durante a captura, evita‑se executar outros programas que possam sobrescrever porções da memória.  Ferramentas avançadas permitem *dump* seletivo de processos, mas a captura completa garante que mesmo dados ocultos (injetados em processos legítimos) sejam preservados.  

**Desafios**: a RAM pode ter dezenas de gigabytes, tornando a análise demorada; a coleta deve ser rápida para minimizar alterações; e a interpretação requer ferramentas especializadas.  Mesmo assim, a análise de dumps de memória permite identificar processos maliciosos, conexões de rede, chaves de criptografia, módulos carregados e conteúdo de discos cifrados【347394763141159†L573-L618】.

## 3.4 Capturando a memória não volátil

Após preservar os dados voláteis, procede‑se à aquisição das mídias permanentes (discos rígidos, SSDs, pendrives).  Os passos essenciais são:

1. **Montar a mídia via bloqueador de escrita** para impedir alterações acidentais.  
2. **Usar ferramenta de imagem** (dd, dc3dd, Guymager, FTK Imager, clonezilla) para criar uma cópia bit‑a‑bit.  
3. **Verificar integridade** comparando hashes.  
4. **Documentar** cada passo e preservar a cadeia de custódia.  

Em SSDs, o comando TRIM pode apagar blocos livremente; por isso, a aquisição deve ser feita o quanto antes.  Dispositivos móveis podem exigir métodos específicos: *logical acquisition* (extração de arquivos via API), *file system acquisition* (extração do sistema de arquivos) ou *physical acquisition* (imagem de baixa nível, possivelmente via chip‑off).

## 3.5 Analisando imagens forenses da unidade de disco rígido

A análise de discos envolve examinar metadados, reconstruir a hierarquia de arquivos, identificar artefatos e extrair dados relevantes.  Algumas técnicas e ferramentas:

- **Montagem em modo somente leitura**: as imagens devem ser montadas em ambientes isolados (virtualização ou sandboxes) para examinar sem alterar os dados.  
- **Sistema de arquivos e logs**: analistas usam *Autopsy* ou *The Sleuth Kit* para explorar tabelas MFT (NTFS), USN Journal e $LogFile, identificando criação, modificação, exclusão e movimentação de arquivos【775749847023820†L74-L104】【775749847023820†L208-L235】.  No ext4, examinam‑se inodes, journals e superblocos【303309481301383†L86-L97】.  
- **File carving**: recupera arquivos apagados ou fragmentados buscando assinaturas de cabeçalhos/rodapés.  Ferramentas como *PhotoRec* e *Scalpel* automatizam o processo.  
- **Análise de espaço slack e não alocado**: dados residuais podem conter porções de documentos ou mensagens apagadas.  
- **Temporalidade**: a correlação de timestamps (criação, modificação, acesso e alteração de metadados) permite reconstruir linhas do tempo e detectar manipulações.  Conversões de formatos de timestamp (Unix, WebKit etc.) devem ser consideradas【838057224104116†L66-L99】.
- **Correlacionar com outros artefatos**: eventos de execução de programas (prefetch, LNK), logs do sistema, registro do Windows, logs de aplicativos e arquivos de configuração fornecem contexto adicional.

## 3.6 Analisando a imagem forense da RAM

Os dumps de memória são examinados com ferramentas de **forense de memória** como *Volatility*, *Rekall* ou *Redline*.  A análise inclui:

1. **Enumeração de processos e threads**: identifica todos os processos em execução no momento da captura.  Permite detectar malware escondido ou injetado em processos legítimos.  
2. **Módulos e bibliotecas carregadas**: verifica DLLs e drivers associados a cada processo.  Driveres não assinados ou desconhecidos podem indicar rootkits.  
3. **Conexões de rede**: lista portas abertas, conexões ativas e endereços IP remotos.  Comparando com logs de firewall e roteador, é possível rastrear a comunicação de malware.  
4. **Handles de arquivos**: identifica arquivos abertos, inclusive dados temporários e arquivos mapeados em memória.  
5. **Strings e indicadores de comprometimento**: busca por padrões de texto (URLs, domínios, comandos) e *hashes* de amostras conhecidas.  
6. **Recuperação de credenciais e chaves**: a RAM pode armazenar senhas em texto claro, *hashes* de senhas e chaves de criptografia; extraí‑las ajuda a decifrar volumes cifrados ou serviços protegidos.  
7. **Time travel**: comparando dumps capturados em diferentes momentos, é possível observar o comportamento de malware e identificar persis­tência.

A interpretação de dumps de memória requer experiência, pois dados estão em constante mudança e fragmentados.  Ferramentas como *Volatility* possuem plugins específicos para examinar estruturas de sistemas operacionais e podem exportar informações em formatos utilizáveis.  Combinada com a análise de disco e logs, a forense de memória oferece uma visão completa do incidente.