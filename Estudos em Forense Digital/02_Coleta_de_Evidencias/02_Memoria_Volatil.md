---
title: "Memória Volátil"
tags: [coleta, memoria, ram, volatil, resposta-a-incidentes, integridade]
status: rascunho
---

# Memória Volátil

A memória RAM armazena **dados temporários e altamente sensíveis**: processos em execução, conexões de rede ativas, comandos recentes, chaves de criptografia, credenciais e até partes de arquivos que nunca foram gravados em disco. Como todo esse conteúdo **desaparece ao desligar ou reiniciar**, a coleta de memória volátil deve ser feita **antes** de qualquer aquisição de disco.

Este documento descreve o procedimento recomendado para coletar dumps de memória, as ferramentas típicas e os pontos de atenção para manter a integridade da evidência.

---

## 1. Quando coletar memória

Coletar RAM é prioritário quando:

- há suspeita de **malware fileless** ou injetado em processo legítimo;
- o sistema está com **volume criptografado montado** (a chave pode estar na RAM);
- o host está mantendo **conexões C2** (command and control);
- você precisa saber **quais usuários/processos estavam ativos** no momento do incidente;
- desligar a máquina **não é opção** porque causaria perda de artefatos.

Regra prática: **se o equipamento está ligado e comprometido, capture a memória primeiro.**

---

## 2. Princípios da coleta de RAM

1. **Mínima interferência**  
   Toda ferramenta que você executa consome memória e altera o estado. Por isso, use uma ferramenta única e conhecida e evite abrir outros programas.

2. **Saída para mídia externa**  
   Salve o dump diretamente em um disco USB limpo, de preferência ligado via bloqueador de escrita no sentido máquina → mídia.

3. **Rapidez**  
   Quanto mais tempo demora, mais o sistema muda. Faça a coleta logo após o isolamento.

4. **Hash e documentação**  
   Ao finalizar, calcule um hash (SHA-256 preferencialmente) do arquivo de dump e registre horário, ferramenta e operador.

---

## 3. Fluxo recomendado

1. **Isolar o host da rede**  
   - desconectar Wi-Fi ou cabo;
   - ou aplicar regra temporária de bloqueio;
   - objetivo: impedir que o atacante apague rastros ou feche o C2.

2. **Conectar a mídia de destino**  
   - pendrive/HD externo previamente preparado;
   - espaço livre suficiente (a RAM coletada costuma ter o mesmo tamanho da RAM física: 8 GB, 16 GB, 32 GB…).

3. **Executar a ferramenta de coleta**  
   - escolha uma ferramenta compatível com o SO e já validada no laboratório;
   - direcione a saída para a mídia externa.

4. **Calcular hash do dump**  
   - no mesmo host ou em outra estação forense;
   - registrar o valor no relatório e, se possível, armazenar junto com o arquivo (`.sha256`).

5. **Documentar**  
   - data e hora da coleta;
   - hostname/IP da máquina;
   - usuário logado;
   - ferramenta e versão;
   - local onde o dump foi salvo.

---

## 4. Ferramentas comuns

**Windows:**
- **DumpIt**: muito simples (executável único), gera um `.raw` com toda a RAM.
- **FTK Imager (memory capture)**: versão GUI e CLI; permite escolher destino.
- **WinPMem / Velociraptor pmem**: mais atual, suporta formatos como AFF4.
- **Magnet RAM Capturer / Belkasoft RAM Capturer**: ferramentas forenses comerciais bem aceitas.

**Linux:**
- ` LiME (Linux Memory Extractor)` para capturas em kernel;
- imagens via `/dev/mem` ou `/dev/crash` quando disponíveis;
- sempre direcionando a saída para mídia externa.

**Formato de saída:** RAW é o mais comum; alguns capturadores permitem AFF4. O importante é que o formato seja aceito pelas ferramentas de análise (Volatility/Rekall).

---

## 5. Exemplo de coleta (Windows, DumpIt)

1. Copie `DumpIt.exe` para a máquina-alvo (de preferência a partir da mídia externa).
2. Execute como administrador.
3. Informe o caminho de saída na mídia externa.
4. Aguarde a finalização (o tempo depende do tamanho da RAM).
5. Leve o arquivo para a estação forense e calcule o hash:
   ```powershell
	   certutil -hashfile C:\evidencias\host01.mem SHA256
     ````

6. Salve o valor do hash no relatório.

---

## 6. Validação e integridade

Mesmo sendo volátil, o dump é uma **evidência digital** e deve ser tratado como o disco:

* calcular **SHA-256** do arquivo de memória;
* registrar o nome da ferramenta e versão (porque ferramentas diferentes podem gerar cabeçalhos diferentes);
* guardar o dump em armazenamento seguro, de preferência duplicado.

Exemplo de registro mínimo:

* Host: DESKTOP-INTRUSO
* Data/hora (UTC): 2025-11-05 16:32
* Ferramenta: DumpIt 3.x
* Tamanho do dump: 16 384 000 000 bytes
* Hash SHA-256: `...`
* Operador: V. Santos

---

## 7. Pontos de atenção

* **Tamanho**: dumps de 16 GB ou 32 GB são comuns; verifique se há espaço antes.
* **Criptografia**: se o disco está cifrado mas montado, a chave pode estar na RAM - isso torna a coleta ainda mais valiosa.
* **Processos maliciosos injetados**: muitas campanhas residem apenas em memória; se não houver dump, não haverá evidência.
* **Ferramentas que escrevem muito**: evite abrir navegador, gerenciador de arquivos ou antivírus na máquina-alvo durante a coleta.
* **Host crítico em produção**: combine com a equipe de infraestrutura, pois a coleta pode causar breve impacto.

---

## 8. Relação com a análise (Volatility, Rekall)

A análise **não acontece aqui**, mas é bom registrar o que será feito depois:

* identificar perfil do sistema (profile / kdbg);
* listar processos e conexões;
* extrair DLLs e módulos suspeitos;
* buscar strings e IoCs dentro do dump;
* correlacionar com logs e com a futura imagem de disco.

Isso justifica, no relatório de coleta, porque a RAM foi adquirida: **ela será usada para enriquecer a análise de disco e de logs.**

---

## 9. Checklist rápido

* [ ] Host isolado da rede
* [ ] Ferramenta de captura definida
* [ ] Mídia externa preparada e com espaço
* [ ] Dump gerado sem erros
* [ ] Hash calculado e registrado
* [ ] Dados de data/hora e operador registrados
* [ ] Dump transferido para armazenamento seguro