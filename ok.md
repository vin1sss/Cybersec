# Motivos pelos quais não foi possível implementar o Relatório 16

## 1. Contextualização da proposta  

A proposta do Relatório 16 era replicar a atividade realizada no Relatório 4, porém migrando todo o cenário para a nuvem pública da Azure. Ou seja, a mesma lógica de laboratório, mas em vez de usar o ambiente local, utilizar recursos de computação em nuvem.

## 2. Complexidade do Relatório 4 e impacto na migração para a nuvem  

O Relatório 4 já possui, por si só, uma complexidade e uma extensão consideráveis. Replicar esse mesmo cenário na Azure aumenta significativamente o nível de dificuldade, tanto em termos de configuração quanto de acompanhamento dos alunos, o que torna a implementação pouco viável na prática.  

Além disso, a versão em nuvem do relatório teria a tendência de ficar ainda mais longa e detalhada, o que, na prática, dificultaria que os alunos concluíssem todas as etapas dentro do tempo de aula disponível.

## 3. Configuração de VMs e redes na Azure  

A configuração de máquinas virtuais e redes na nuvem é mais complexa e trabalhosa do que em um ambiente local, mesmo quando se tenta simplificar o cenário em relação ao Relatório 4. Isso inclui:

- Criação e gerenciamento de VMs  
- Configuração de redes virtuais, sub-redes e regras de segurança  
- Integração entre os diferentes recursos para que o laboratório funcione de ponta a ponta  

## 4. Dificuldades prévias dos alunos  

Os alunos já apresentaram dificuldades para executar, de forma autônoma, atividades mais simples presentes em outros relatórios. Em alguns casos, mesmo relatórios menos complexos não foram concluídos dentro do tempo de aula.  

Diante disso, uma atividade mais complexa, mais longa e em um ambiente de nuvem, sem um direcionamento ao vivo e contínuo, tende a ampliar ainda mais as dificuldades.

## 5. Limitações e problemas da conta gratuita da Azure  

A conta gratuita da Azure disponibiliza uma quantidade limitada de créditos para uso dos serviços. Durante a preparação da atividade foram observados alguns problemas:

- O serviço da Azure crashou algumas vezes  
- Mesmo durante as quedas do serviço, os créditos continuaram sendo consumidos  

## 6. Custo de assinaturas pagas  

Para que a atividade pudesse ser aplicada em escala, seria necessário disponibilizar mais de 100 assinaturas pagas da Azure (uma por aluno ou por grupo).  

O custo associado a essa quantidade de contas pagas pode ser inviável, considerando que se trata de apenas um relatório dentro da disciplina.

## 7. Nova política de rede do laboratório de redes  

Com a nova proposta de o laboratório de redes possuir uma rede própria, isolada e sem acesso à internet, a utilização da Azure se torna ainda mais inviável.  

## 8. Conclusão  

Diante da combinação de fatores - alta complexidade técnica, tendência do relatório ficar muito longo para o tempo de aula disponível, dificuldades já observadas no perfil dos alunos, limitações e instabilidade da conta gratuita da Azure - a implementação do Relatório 16 na nuvem pública da Azure não se mostrou viável.  
