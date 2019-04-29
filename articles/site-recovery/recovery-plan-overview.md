---
title: Usar planos de recuperação na recuperação de desastre com o Azure Site Recovery | Microsoft Docs
description: Saiba mais sobre como usar planos de recuperação para recuperação de desastres com o serviço Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 520f30b5fabebf299b5407a502b76d7d30850bfd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60947315"
---
# <a name="about-recovery-plans"></a>Sobre planos de recuperação

Este artigo descreve os planos de recuperação no [Azure Site Recovery](site-recovery-overview.md).

Um plano de recuperação reúne computadores em grupos de recuperação. É possível personalizar um plano, adicionando ordem, instruções e tarefas ao plano. Após definir um plano, você poderá executar um failover nele.



## <a name="why-use-a-recovery-plan"></a>Por que usar um plano de recuperação?

Um plano de recuperação ajuda-o a definir um processo de recuperação sistemático, criando pequenas unidades independentes com a possibilidade de fazer failover. Uma unidade normalmente representa um aplicativo no ambiente. Um plano de recuperação define como os computadores fazem failover e a sequência em que iniciam após o failover. Use planos de recuperação para:

* Modelar um aplicativo em torno de suas dependências.
* Automatizar as tarefas de recuperação para reduzir o RTO.
* Verifique se você está preparado para migração ou recuperação de desastre, garantindo que os aplicativos façam parte de um plano de recuperação.
* Execute failover de teste nos planos de recuperação para garantir que a recuperação de desastre ou a migração funcionem conforme o esperado.


## <a name="model-apps"></a>Modelar aplicativos

É possível planejar e criar um grupo de recuperação para capturar propriedades específicas de aplicativo. Como exemplo, vamos considerar um aplicativo de três camadas típico com um back-end do SQL Server, middleware e front-end da Web. Normalmente, você personaliza o plano de recuperação para que os computadores em cada camada iniciem na ordem correta após o failover.

    - O back-end do SQL deve iniciar primeiro, o middleware em seguida e, finalmente, o front-end da Web.
    - Essa ordem de início garante que o aplicativo esteja funcionando no momento em que o último computador for iniciado.
    - Essa ordem garante que, quando o middleware iniciar e tentar conectar-se à camada do SQL Server, a camada do SQL Server já esteja em execução. 
    - Essa ordem também ajuda a garantir que o servidor front-end inicie por último, de modo que os usuários finais não conectem-se à URL do aplicativo antes que todos os componentes estejam ativos e em execução, e o aplicativo pronto para aceitar as solicitações.

Para criar essa ordem, adicione grupos ao grupo de recuperação e adicione computadores nos grupos.
- Onde a ordem é especificada, o sequenciamento é usado. As ações executam em paralelo, quando apropriado, para melhorar o RTO de recuperação do aplicativo.
- Computadores em um único grupo fazem failover em paralelo.
- Computadores em grupos diferentes fazem failover na ordem do grupo, para que os computadores do Grupo 2 iniciem o failover somente depois que todos os computadores do Grupo 1 tiverem feito o failover e iniciados.

    ![Exemplo de plano de recuperação](./media/recovery-plan-overview/rp.png)

Com essa personalização estabelecida, veja o que acontece ao executar um failover no plano de recuperação: 

1. Uma etapa de desligamento tenta desativar os computadores locais. A exceção é que, se você executar um failover de teste o site primário continuará em execução. 
2. O desligamento dispara um failover paralelo de todos os computadores no plano de recuperação.
3. O failover prepara os discos de máquina virtual usando dados replicados.
4. Os grupos de inicialização são executados em ordem e iniciam os computadores em cada grupo. Primeiro, o Grupo 1 executa, em seguida, o Grupo 2 e, finalmente, o Grupo 3. Se houver mais de um computador em qualquer grupo, todos os computadores iniciarão em paralelo.


## <a name="automate-tasks"></a>Automatizar tarefas

Recuperar aplicativos grandes pode ser uma tarefa complexa. Etapas manuais tornam o processo propenso a erros e a pessoa que estiver executando o failover pode não estar ciente de todas as complexidades do aplicativo. É possível usar um plano de recuperação para impor uma ordem e automatizar as ações necessárias em cada etapa, usando runbooks de Automação do Azure para failover no Azure, ou scripts. Para tarefas que não podem ser automatizadas, é possível inserir pausas para ações manuais nos planos de recuperação. Há alguns tipos de tarefas que você pode configurar:

* **Tarefas na VM do Azure após failover**: Ao fazer failover para o Azure, normalmente é necessário executar ações para poder conectar-se à VM após o failover. Por exemplo:  
    * Crie um endereço IP público na VM do Azure.
    * Atribua um grupo de segurança de rede ao adaptador de rede da VM do Azure.
    * Adicione um balanceador de carga a um conjunto de disponibilidade.
* **Tarefas dentro da Vm após failover**: Essas tarefas normalmente reconfiguram o aplicativo em execução no computador para que continue funcionando corretamente no novo ambiente. Por exemplo: 
    * Modifique a cadeia de conexão do banco de dados no computador.
    * Altere as regras ou a configuração do servidor Web.


## <a name="test-failover"></a>Failover de Teste

É possível usar um plano de recuperação para acionar um failover de teste. Utilize as melhores práticas a seguir:

- Sempre conclua um failover de teste em um aplicativo, antes de executar um failover completo. Failovers de teste ajudam a verificar se o aplicativo é exibido no Site Recovery.
- Se você detectar algum perda, dispare uma limpeza e execute novamente o failover de teste. 
- Execute um failover de teste várias vezes até certificar-se de que o aplicativo será recuperado sem problemas.
- Como cada aplicativo é exclusivo, é necessário criar planos de recuperação personalizados para cada aplicativo e executar um failover de teste em cada um dos aplicativos.
- Os aplicativos e as respectivas dependências alteram com frequência. Para garantir que os planos de recuperação estejam atualizados, execute um failover de teste para cada aplicativo a cada trimestre.

    ![Captura de tela de um exemplo de plano de recuperação de teste no Site Recovery](./media/recovery-plan-overview/rptest.png)

## <a name="watch-the-video"></a>Assista ao vídeo

Assista a um vídeo de exemplo rápido mostrando um failover de clicar em um aplicativo do WordPress de duas camadas.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Próximas etapas

- [Criar](site-recovery-create-recovery-plans.md) um plano de recuperação.
- Saiba mais sobre [execução de failovers](site-recovery-failover.md).  
