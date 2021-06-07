---
title: Sobre os planos de recuperação no Azure Site Recovery
description: Saiba mais sobre planos de recuperação no Azure Site Recovery.
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 8d191781cacc37242dd1be31d6cb87ef196e5e7a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "84343907"
---
# <a name="about-recovery-plans"></a>Sobre planos de recuperação

Este artigo fornece uma visão geral dos planos de recuperação no [Azure site Recovery](site-recovery-overview.md).

Um plano de recuperação reúne computadores em grupos de recuperação para fins de failover. Um plano de recuperação ajuda-o a definir um processo de recuperação sistemático, criando pequenas unidades independentes com a possibilidade de fazer failover. Uma unidade normalmente representa um aplicativo no ambiente.

- Um plano de recuperação define como os computadores fazem failover e a sequência em que iniciam após o failover.
- Os planos de recuperação podem ser usados para failover e failback do Azure.
- Até 100 instâncias protegidas podem ser adicionadas a um plano de recuperação.
- É possível personalizar um plano, adicionando ordem, instruções e tarefas ao plano.
- Após definir um plano, você poderá executar um failover nele.
- Os computadores podem ser referenciados em vários planos de recuperação, nos quais os planos subsequentes ignoram a implantação/inicialização de um computador se ele foi implantado anteriormente usando outro plano de recuperação.



### <a name="why-use-a-recovery-plan"></a>Por que usar um plano de recuperação?

Use planos de recuperação para:

* Modelar um aplicativo em torno de suas dependências.
* Automatize as tarefas de recuperação para reduzir o RTO (objetivo de tempo de recuperação).
* Verifique se você está preparado para migração ou recuperação de desastre, garantindo que os aplicativos façam parte de um plano de recuperação.
* Execute failovers de teste em planos de recuperação para garantir que a recuperação ou a migração de desastres funcione conforme o esperado.


## <a name="model-apps"></a>Modelar aplicativos 
É possível planejar e criar um grupo de recuperação para capturar propriedades específicas de aplicativo. Como exemplo, vamos considerar um aplicativo de três camadas típico com um back-end do SQL Server, middleware e front-end da Web. Normalmente, você personaliza o plano de recuperação para que os computadores em cada camada iniciem na ordem correta após o failover.

- O back-end do SQL deve iniciar primeiro, o middleware em seguida e, finalmente, o front-end da Web.
- Essa ordem de início garante que o aplicativo esteja funcionando no momento em que o último computador for iniciado.
- Essa ordem garante que, quando o middleware iniciar e tentar conectar-se à camada do SQL Server, a camada do SQL Server já esteja em execução. 
- Essa ordem também ajuda a garantir que o servidor front-end inicie por último, de modo que os usuários finais não conectem-se à URL do aplicativo antes que todos os componentes estejam ativos e em execução, e o aplicativo pronto para aceitar as solicitações.

Para criar essa ordem, adicione grupos ao grupo de recuperação e adicione computadores nos grupos.
- Onde a ordem é especificada, o sequenciamento é usado. As ações são executadas em paralelo conforme apropriado, para melhorar o RTO da recuperação de aplicativos.
- Computadores em um único grupo fazem failover em paralelo.
- Computadores em grupos diferentes fazem failover na ordem do grupo, para que os computadores do Grupo 2 iniciem o failover somente depois que todos os computadores do Grupo 1 tiverem feito o failover e iniciados.

    ![Exemplo de plano de recuperação](./media/recovery-plan-overview/rp.png)

Com essa personalização estabelecida, veja o que acontece ao executar um failover no plano de recuperação: 

1. Uma etapa de desligamento tenta desativar os computadores locais. A exceção é que, se você executar um failover de teste o site primário continuará em execução. 
2. O desligamento dispara um failover paralelo de todos os computadores no plano de recuperação.
3. O failover prepara os discos de máquina virtual usando dados replicados.
4. Os grupos de inicialização são executados em ordem e iniciam os computadores em cada grupo. Primeiro, o Grupo 1 executa, em seguida, o Grupo 2 e, finalmente, o Grupo 3. Se houver mais de um computador em qualquer grupo, todos os computadores iniciarão em paralelo.


## <a name="automate-tasks-in-recovery-plans"></a>Automatizar tarefas em planos de recuperação

Recuperar aplicativos grandes pode ser uma tarefa complexa. Etapas manuais tornam o processo propenso a erros e a pessoa que estiver executando o failover pode não estar ciente de todas as complexidades do aplicativo. É possível usar um plano de recuperação para impor uma ordem e automatizar as ações necessárias em cada etapa, usando runbooks de Automação do Azure para failover no Azure, ou scripts. Para tarefas que não podem ser automatizadas, é possível inserir pausas para ações manuais nos planos de recuperação. Há alguns tipos de tarefas que você pode configurar:

* **Tarefas na VM do Azure após failover**: ao fazer failover para o Azure, normalmente é necessário executar ações para poder conectar-se à VM após o failover. Por exemplo: 
    * Crie um endereço IP público na VM do Azure.
    * Atribua um grupo de segurança de rede ao adaptador de rede da VM do Azure.
    * Adicione um balanceador de carga a um conjunto de disponibilidade.
* **Tarefas na VM após o failover**: essas tarefas normalmente reconfiguram o aplicativo em execução no computador para que continue funcionando corretamente no novo ambiente. Por exemplo:
    * Modifique a cadeia de conexão do banco de dados no computador.
    * Altere as regras ou a configuração do servidor Web.


### <a name="run-a-test-failover-on-recovery-plans"></a>Executar um failover de teste nos planos de recuperação

É possível usar um plano de recuperação para acionar um failover de teste. Utilize as melhores práticas a seguir:

- Sempre conclua um failover de teste em um aplicativo, antes de executar um failover completo. Failovers de teste ajudam a verificar se o aplicativo é exibido no Site Recovery.
- Se você achar que perdeu alguma coisa, dispare uma limpeza e execute novamente o failover de teste. 
- Execute um failover de teste várias vezes até certificar-se de que o aplicativo será recuperado sem problemas.
- Como cada aplicativo é exclusivo, é necessário criar planos de recuperação personalizados para cada aplicativo e executar um failover de teste em cada um dos aplicativos.
- Os aplicativos e as respectivas dependências alteram com frequência. Para garantir que os planos de recuperação estejam atualizados, execute um failover de teste para cada aplicativo a cada trimestre.

    ![Captura de tela de um exemplo de plano de recuperação de teste no Site Recovery](./media/recovery-plan-overview/rptest.png)

## <a name="watch-a-recovery-plan-video"></a>Assista a um vídeo do plano de recuperação

Assista a um vídeo de exemplo rápido mostrando um failover de clique para um plano de recuperação para um aplicativo WordPress de duas camadas.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Próximas etapas

- [Crie](site-recovery-create-recovery-plans.md) um plano de recuperação.
- [Executar](site-recovery-failover.md) failovers. 
