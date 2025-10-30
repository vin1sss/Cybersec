# 2 Resposta inicial e tarefas do responsável

Quando um incidente de segurança é descoberto ou há suspeita de atividade criminosa em um sistema, as primeiras ações são críticas para preservar a integridade das evidências e evitar contaminação.  Este capítulo descreve o papel do **primeiro respondente** (first responder) e os procedimentos iniciais que devem ser seguidos antes da chegada dos analistas forenses.

## 2.1 Busca e confisco

1. **Planejamento legal**: verifique a existência de mandados de busca ou autorizações legais, respeitando leis de privacidade e a jurisdição local.  A obtenção das autorizações corretas evita que evidências sejam inadmissíveis em tribunal.
2. **Proteção da cena**: ao chegar ao local, isole a área física para impedir que pessoas não autorizadas acessem os dispositivos.  Registre a hora de chegada, identifique testemunhas e documente a configuração física (fotos, vídeos ou diagramas).  
3. **Estado do sistema**: antes de desligar qualquer equipamento, avalie se a máquina está ligada.  Sistemas em execução podem conter evidências voláteis (por exemplo, processos, chaves de criptografia, conexões de rede) que serão perdidas se forem desligados.  Na maioria dos casos, recomenda‑se **preservar a energia** e realizar uma captura de memória volátil antes de qualquer desligamento【347394763141159†L573-L618】.  Desconecte o equipamento da rede para impedir a comunicação externa e a alteração remota de dados.
4. **Captura de dispositivos**: recolha dispositivos de armazenamento (HDs, SSDs, pendrives), dispositivos móveis, documentos e mídias ópticas.  Embale cada item individualmente em embalagens antiestáticas ou envelopes lacrados, identificando a origem e o responsável pelo recolhimento.  Selo de evidência e documentação são essenciais para a cadeia de custódia.

## 2.2 Kit de ferramentas do responsável

Um kit de primeiros respondentes deve incluir ferramentas de hardware e software para preservar dados sem alterá‑los:

- **Bloqueadores de escrita** (write blockers) para impedir qualquer gravação em discos durante a aquisição.  
- **Dispositivos de aquisição de imagem** (por exemplo, Cellebrite, FTK Imager, Guymager) para clonar discos e criar imagens forenses nos formatos E01 ou RAW.  
- **Ferramentas de captura de memória** como Magnet RAM Capturer, Belkasoft RAM Capturer ou WinPMem, que criam *dumps* da RAM sem modificar significativamente o estado do sistema【347394763141159†L573-L618】.  
- **Notebooks forenses ou estações de trabalho** configurados especificamente para análise, com softwares como Autopsy/The Sleuth Kit, X‑Ways, EnCase ou Volatility.  
- **Equipamentos de documentar**: câmera digital, lacres numerados, etiquetas de evidência, formulários de cadeia de custódia, canetas e papel.  
- **Cabos e adaptadores** (SATA/IDE/PCIe, USB, adaptadores de dispositivos móveis) para conectar diversas mídias.  
- **Material de isolamento eletromagnético** (sacos Faraday) para dispositivos móveis, prevenindo que se comuniquem com redes celulares ou Wi‑Fi.

## 2.3 Tarefas do responsável

O responsável inicial deve desempenhar tarefas precisas e documentadas:

1. **Documentar tudo**: registre cada ação executada, horário, nomes das pessoas envolvidas e justificativas.  Anotações detalhadas fornecem a base para o relatório final e para a cadeia de custódia.  
2. **Preservar evidências voláteis**: se o sistema estiver ligado, capture a memória RAM, conexões de rede e outros dados voláteis antes de desligar.  Utilize ferramentas aprovadas e calcule o hash do arquivo resultante para garantir integridade【347394763141159†L573-L618】.  
3. **Desligar de forma apropriada**: após capturar a memória, desligue o equipamento de maneira controlada (por exemplo, através do sistema operacional, se possível) para evitar corrupção.  
4. **Aquisitar mídias não voláteis**: use bloqueadores de escrita e ferramentas de imagem para clonar discos, mídias removíveis ou dispositivos móveis.  Para dispositivos móveis, escolha métodos específicos (lógico, físico ou por chip‑off) dependendo do modelo.  
5. **Embalar e rotular**: cada item deve ser embalado separadamente, com etiquetas que incluam número de evidência, data/hora, local de apreensão e assinaturas das pessoas que manusearam.  
6. **Transferência segura**: transporte as evidências para o laboratório forense em recipientes seguros.  A cadeia de custódia deve registrar todas as transferências e armazenamento em local apropriado, com controle de acesso.  
7. **Comunicação**: notifique a equipe de resposta a incidentes, autoridades legais ou superiores sobre o andamento da apreensão.  A transparência é fundamental para que outras equipes possam tomar medidas paralelas (por exemplo, mitigação de incidentes, notificação a vítimas ou clientes).  
8. **Preservação de logs e registros**: se a investigação envolver servidores ou redes, providencie cópia de logs de firewall, roteador, proxy, DNS e sistemas.  Esses logs podem ser rotacionados rapidamente; coletá‑los cedo evita perda de informação.

O trabalho do primeiro respondente estabelece as bases para o sucesso da análise forense.  Falhas nesta fase — como não capturar dados voláteis ou contaminar a mídia — podem comprometer a investigação e inviabilizar o uso das evidências em tribunal.