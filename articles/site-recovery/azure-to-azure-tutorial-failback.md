---
title: Tutorial sobre como fazer failback de VMs do Azure para uma região primária durante a recuperação de desastre usando o Azure Site Recovery.
description: Tutorial para saber mais sobre como fazer failback de VMs do Azure para uma região primária usando o Azure Site Recovery.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 5c127010a7988bf08c77340a4fc10bb32dc76f87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93393842"
---
# <a name="tutorial-fail-back-azure-vm-to-the-primary-region"></a>Tutorial: fazer failback de VMs do Azure para uma região primária

Após fazer failover de uma VM do Azure para uma região secundária do Azure, siga este tutorial para fazer failback da VM para a região primária do Azure, usando o [Azure Site Recovery](site-recovery-overview.md).  Neste artigo, você aprenderá como:

> [!div class="checklist"]
> 
> * Examine os pré-requisitos.
> * Fazer failback da VM na região secundária.
> * Proteger novamente as VMs primárias que fizeram failback para a região secundária.
> 
> [!NOTE]
> Este tutorial mostrará como fazer failback em poucas etapas. Caso queira executar um failover com configurações completas, saiba mais sobre a [rede](azure-to-azure-about-networking.md), a [automação](azure-to-azure-powershell.md) e a [solução de problemas](azure-to-azure-troubleshoot-errors.md) da VM do Azure.



## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você deve:

1. [Configurar a replicação](azure-to-azure-tutorial-enable-replication.md) de no mínimo uma VM do Azure e experimentar a [análise de recuperação de desastre](azure-to-azure-tutorial-dr-drill.md) dela.
2. [Fazer failover da VM](azure-to-azure-tutorial-failover-failback.md) da região primária para uma região secundária, além de protegê-la novamente para que ela seja replicada da região secundária para a primária. 
3. Verifique se a região primária está disponível e, em seguida, você poderá criar e acessar os novos recursos nela.

## <a name="fail-back-to-the-primary-region"></a>Failback para a região primária

Depois que as VMs forem protegidas novamente, você poderá fazer failback para a região primária, conforme necessário.

1. No cofre > **Itens replicados**, selecione a VM.

2. Antes de executar um failover, verifique a integridade da VM e se a sincronização foi concluída na página de visão geral da VM. A VM deverá estar em um estado *Protegido*.

    ![A página de visão geral da VM mostrando a VM em um estado protegido](./media/azure-to-azure-tutorial-failback/protected-state.png)

3. Na página de visão geral, selecione **Failover**. Como não estamos executando um failover de teste desta vez, uma solicitação de confirmação será exibida.

    [Uma página mostrando que concordamos em executar o failover sem um failover de teste](./media/azure-to-azure-tutorial-failback/no-test.png)

4. Em **Failover**, observe a direção da região secundária para a primária e selecione um ponto de recuperação. A VM do Azure no destino (região primária) será criada usando dados deste ponto.
   - **Mais recente processado**: usa o último ponto de recuperação processado pelo Site Recovery. A carimbo de data/hora é mostrado. Nenhum tempo será gasto no processamento de dados. Portanto, ele fornecerá um RTO (objetivo do tempo de recuperação) baixo.
   -  **Mais recente**: processa todos os dados enviados ao Site Recovery para criar um ponto de recuperação para cada VM antes do failover. Fornece o RPO (objetivo do ponto de recuperação) mais baixo porque todos os dados serão replicados para o Site Recovery quando o failover for disparado.
   - **Consistente com o aplicativo mais recente**: essa opção faz failover de VMs para o ponto de recuperação mais recente e consistente com o aplicativo. A carimbo de data/hora é mostrado.
   - **Personalizado**: efetua o failover para um determinado ponto de recuperação. A configuração personalizada só está disponível quando você efetua o failover de uma única VM e não usa um plano de recuperação.

    > [!NOTE]
    > Caso faça failover de uma VM na qual você adicionou um disco depois de habilitar a replicação dela, os pontos de replicação mostrarão os discos disponíveis para recuperação. Por exemplo, um ponto de replicação que foi criado antes de você adicionar um segundo disco será exibido como "1 de 2 discos".

4. Selecione **Desligar o computador antes de iniciar o failover** caso queira que o Site Recovery tente desligar as VMs de origem antes de iniciar o failover. O desligamento ajuda a evitar a perda de dados. O failover continuará mesmo o desligamento falhar. 

    ![Página de configurações de failover](./media/azure-to-azure-tutorial-failback/failover.png)    

3. Para iniciar o failover, selecione **OK**.
4. Monitore o failover nas notificações.

    ![Notificação de progresso do failover](./media/azure-to-azure-tutorial-failback/notification-progress.png)  
    ![Notificação de êxito do failover](./media/azure-to-azure-tutorial-failback/notification-success.png)   

## <a name="reprotect-vms"></a>Proteger novamente as VMs

Depois de fazer failback de VMs para a região primária, será necessário protegê-las novamente para que elas iniciem a replicação para a região secundária outra vez.

1. Na página de **Visão Geral** da VM, selecione **Proteger novamente**.

    ![Botão de nova proteção da região primária](./media/azure-to-azure-tutorial-failback/reprotect.png)  

2. Examine as configurações de destino da região primária. Os recursos marcados como novos serão criados pelo Site Recovery como parte da operação de nova proteção.
3. Clique em **OK** para iniciar o processo de nova proteção. O processo enviará dados iniciais para o local de destino e replicará informações delta de VMs para o destino.

     ![Uma página mostrando as configurações de replicação](./media/azure-to-azure-tutorial-failback/replication-settings.png) 

4. Monitore o progresso da nova proteção por meio de notificações. 

    ![Notificação de progresso da nova proteção](./media/azure-to-azure-tutorial-failback/notification-reprotect-start.png) [Notificação de progresso da nova proteção](./media/azure-to-azure-tutorial-failback/notification-reprotect-finish.png)
    
  

## <a name="clean-up-resources"></a>Limpar recursos

Para VMs com discos gerenciados, após a conclusão do failback e depois que as VMs forem protegidas novamente para replicação da região primária para a secundária, o Site Recovery limpará de modo automático os computadores na região secundária de recuperação de desastre. Não é necessário excluir VMs nem NICs de modo manual na região secundária. As VMs com discos não gerenciados não serão limpas.

Caso desabilite completamente a replicação após o failback, o Site Recovery limpará os computadores protegidos por ele. Nesse caso, ele também limpará discos de VMs que não usam discos gerenciados. 
 
## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez failback de VMs da região secundária para a primária. Esta é a última etapa do processo que inclui: habilitar a replicação de uma VM, experimentar a análise de recuperação de desastre, fazer failover da região primária para a secundária e, por fim, fazer failback.

> [!div class="nextstepaction"]
> Agora, experimente executar a recuperação de desastre de uma [VM local](vmware-azure-tutorial-prepare-on-premises.md) no Azure

