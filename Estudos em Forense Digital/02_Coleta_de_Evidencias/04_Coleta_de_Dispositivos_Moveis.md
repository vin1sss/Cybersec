---
title: "Coleta de Dispositivos Móveis"
tags: [coleta, mobile, android, ios, forense, aquisicao, cadeia-de-custodia]
status: rascunho
---

# Coleta de Dispositivos Móveis

Dispositivos móveis (smartphones e tablets) costumam conter **mais informação útil do que o próprio computador** do usuário: mensagens, apps de chat, histórico de chamadas, geolocalização, fotos com EXIF, arquivos recebidos e até tokens de acesso. Por isso, a coleta precisa ser **rápida, bem documentada e com o menor impacto possível** no estado do aparelho.

Este documento descreve o que coletar, níveis de aquisição e cuidados práticos para Android e iOS.

---

## 1. Princípios gerais para mobile

1. **Legalidade primeiro**  
   A coleta de dados pessoais de um dispositivo quase sempre exige autorização formal. Registre o número do mandado/ordem/autorização.

2. **Preservar o estado atual**  
   Se a tela está desbloqueada, não deixe bloquear. Se está ligado, não desligue até entender o impacto (pode perder dados voláteis ou acesso).

3. **Isolar de rede**  
   Atacantes ou o próprio usuário podem apagar dados remotamente. Assim que possível:
   - modo avião;
   - desligar Wi-Fi/Bluetooth;
   - colocar em bolsa de Faraday (ideal).

4. **Documentar fisicamente**  
   Fotos do aparelho (frente, verso, IMEI, número de série, cartão SIM, estado da tela) e anotar modelo, versão do SO, operador.

5. **Trabalhar em cópia, não no original**  
   A aquisição vai gerar um artefato (imagem/backup). Toda análise deve ser feita nele.

---

## 2. Níveis de aquisição móvel

Dependendo do modelo, versão do sistema e ferramentas disponíveis, nem sempre dá para fazer a “aquisição física total”. Na prática, você escolhe o nível mais alto possível:

1. **Logical acquisition (lógica)**  
   - Usa APIs oficiais do sistema (iTunes, ADB, MDM) para puxar contatos, SMS, chamadas, histórico de apps.  
   - Rápida e com baixo risco de corromper o aparelho.  
   - Pode NÃO trazer dados apagados ou de apps com criptografia forte.

2. **File system acquisition (sistema de arquivos)**  
   - Extrai o *filesystem* completo do usuário (pastas de apps, bancos SQLite, configs).  
   - Já permite análise de artefatos forenses (mensagens, mídias, bases de apps).  
   - Pode exigir desbloqueio/jailbreak/root dependendo da plataforma.

3. **Physical acquisition (física)**  
   - Imagem de baixo nível (setor a setor / dump de partição).  
   - Melhor opção para recuperar dados apagados, mas **nem sempre é possível** em aparelhos modernos (bootloader bloqueado, criptografia por hardware).  
   - Pode exigir métodos invasivos (chip-off, JTAG) - normalmente só em laboratório especializado.

Registre no relatório **qual nível foi alcançado e por quê** (ex.: “aparelho com criptografia de hardware ativa; realizada extração lógica via ferramenta X”).

---

## 3. Procedimento básico de coleta

1. **Identificar o dispositivo**
   - Marca/modelo (ex.: Samsung SM-A125M, iPhone 13);
   - Versão do sistema (Android 13 / iOS 17);
   - IMEI e número de série (fotografe a tela de “Sobre” ou a bandeja do SIM);
   - Estado: ligado/desligado, bloqueado/desbloqueado.

2. **Isolar**
   - Ativar modo avião;
   - Remover chip SIM (anotar operadora e número);
   - Colocar em bolsa de Faraday, se disponível.

3. **Manter desbloqueado**
   - Se o aparelho estiver desbloqueado, aumente o tempo de bloqueio automático no próprio aparelho (se isso não violar a ordem);
   - Se estiver bloqueado e você não tem o código, **não fique tentando** para não acionar wipe ou bloqueio permanente.

4. **Conectar à estação forense / ferramenta**
   - Usar cabo original ou homologado;
   - Em ferramentas forenses (Cellebrite UFED, Magnet AXIOM, XRY, Oxygen), seguir o fluxo de extração para o modelo específico;
   - Registrar todas as opções escolhidas na ferramenta (tipo de extração, módulos coletados).

5. **Armazenar saída em mídia externa**
   - Nunca salve a extração em disco do próprio aparelho;
   - Nomear com padrão: `case01_mobile_usuarioA_2025-11-05.ufd` ou similar.

6. **Calcular hash do artefato gerado**
   - Mesmo que a ferramenta já gere, calcule de novo (SHA-256) na estação forense;
   - Salvar junto um `.txt` ou `.sha256` com o valor.

7. **Documentar**
   - Data/hora de início e término;
   - Operador;
   - Ferramenta e versão;
   - Modelo e IMEI do aparelho;
   - Tipo de extração realizada.

---

## 4. Pontos específicos para Android

- **Depuração USB (USB debugging):** se estiver ativada, facilita extração; se não estiver e o aparelho estiver bloqueado, pode ser difícil ativar sem o PIN.
- **Criptografia total de disco:** aparelhos mais novos criptografam por padrão; sem desbloqueio, a extração fica limitada.
- **ADB backup (mais antigo):** pode ser usado, mas é limitado e muitos apps bloqueiam.
- **Partições e dados de apps:** ferramentas forenses costumam ter scripts para cada fabricante/modelo; siga o perfil indicado pela ferramenta.

Checklist Android rápido:
- [ ] aparelho em modo avião
- [ ] SIM removido
- [ ] identificado modelo/IMEI
- [ ] executada extração lógica ou filesystem
- [ ] hash do arquivo gerado calculado

---

## 5. Pontos específicos para iOS

- **Backups criptografados:** se o iPhone/iPad estiver emparelhado com um computador e tiver backup permitido, pode-se fazer um backup lógico via ferramenta forense ou iTunes (preferir modo forense).
- **Bloqueio por biometria:** se o aparelho estiver desbloqueado, **desativar o bloqueio automático por Face/Touch temporariamente** pode ajudar a não perder acesso (se a ordem permitir).
- **iCloud / serviços Apple:** muitas evidências ficam na nuvem. A coleta do dispositivo não substitui requisição legal ao iCloud.
- **Criptografia forte:** em aparelhos modernos, sem o código do usuário, a extração física costuma ser inviável; nesse caso, documentar tentativa e fazer logical.

---

## 6. Coleta de mídias e apps

Ao fazer a extração, confirme se o artefato inclui:

- histórico de chamadas e SMS/MMS;
- bases de mensageria (WhatsApp, Telegram, Signal - o que for possível);
- geolocalização e histórico de localização;
- galeria de fotos e **EXIF** das imagens;
- arquivos enviados/recebidos via apps;
- tokens e contas configuradas.

Caso não inclua, faça extrações adicionais ou registre que esses dados não estavam acessíveis.

---

## 7. Armazenamento e integridade

Depois da coleta:

1. **Empacotar** a pasta/arquivo gerado pela ferramenta (se ela não tiver formato próprio) em `.zip` ou `.tar.gz`;
2. **Calcular SHA-256** do pacote:
   ```bash
   sha256sum case01_mobile_usuarioA_2025-11-05.tar.gz > 
   case01_mobile_usuarioA_2025-11-05.tar.gz.sha256
	 ````

3. **Guardar em mídia de evidência** com controle de acesso (apenas peritos);
4. **Atualizar cadeia de custódia** com entrada “dispositivo móvel coletado” e “artefato extraído”.

---

## 8. Limitações e observações

* Se o aparelho estiver **totalmente bloqueado** e com criptografia de hardware, a melhor evidência pode ser apenas o **próprio aparelho+SIM**, bem embalados e com cadeia de custódia - para tentativa posterior em laboratório.
* Técnicas invasivas (JTAG, chip-off) **não devem ser feitas improvisadas**: risco de dano e perda de prova.
* Sempre que possível, **fotografe a tela** mostrando os apps instalados e notificações - isso às vezes é a única prova visual de que tal app estava ali.
* Se o dispositivo foi **remotamente apagado** antes da coleta, registre isso explicitamente no relatório.

---

## 9. Checklist rápido

* [ ] Autorização/ordem registrada
* [ ] Fotos do aparelho e identificação (modelo, IMEI, SIM)
* [ ] Dispositivo isolado de rede (modo avião / Faraday)
* [ ] Método de extração escolhido (logical / filesystem / physical)
* [ ] Extração concluída e salva em mídia externa
* [ ] Hash calculado
* [ ] Dados de data/hora e operador registrados
* [ ] Atualização da cadeia de custódia