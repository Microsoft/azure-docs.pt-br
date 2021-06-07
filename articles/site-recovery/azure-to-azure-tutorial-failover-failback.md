---
title: Tutorial para fazer failover de VMs do Azure para uma região secundária para recuperação de desastre com o Azure Site Recovery.
description: Tutorial para aprender a fazer failover e proteger novamente as VMs do Azure replicadas para uma região secundária do Azure para recuperação de desastre com o serviço do Azure Site Recovery.
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: 99263c83d25542073d63c1cba394a147bd5b2170
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392700"
---
# <a name="tutorial-fail-over-azure-vms-to-a-secondary-region"></a>Tutorial: fazer failover de VMs do Azure para uma região secundária

Saiba como fazer failover de VMs do Azure habilitadas para recuperação de desastre para uma região do Azure secundária com o [Azure Site Recovery](site-recovery-overview.md). Após o failover, você protege novamente as VMs na região de destino para que elas sejam replicadas de volta para a região primária. Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Verificar pré-requisitos
> * Verificar as configurações da VM
> * Executar um failover para a região secundária
> * Inicie a replicação da VM de volta para a região primária.


> [!NOTE]
> Este tutorial mostra como fazer failover de VMs em poucas etapas. Caso queira executar um failover com configurações completas, saiba mais sobre a [rede](azure-to-azure-about-networking.md), a [automação](azure-to-azure-powershell.md) e a [solução de problemas](azure-to-azure-troubleshoot-errors.md) da VM do Azure.



## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, você deve:

1. Configurar a replicação para uma ou mais VMs do Azure. Caso não tenha feito isso, [conclua o primeiro tutorial](azure-to-azure-tutorial-enable-replication.md) nesta série.
2. É recomendável [fazer uma análise de recuperação de desastre](azure-to-azure-tutorial-dr-drill.md) para as VMs replicadas. Fazer uma análise antes do failover completo ajuda a garantir que tudo funcione como o esperado, sem afetar o ambiente de produção. 


## <a name="verify-the-vm-settings"></a>Verificar as configurações da VM

1. No cofre > **Itens replicados**, selecione a VM.

    ![Opção de abrir as propriedades da VM na página de visão geral](./media/azure-to-azure-tutorial-failover-failback/vm-settings.png)

2. Na página de **visão geral** da VM, verifique se ela está protegida e íntegra antes de executar um failover.
    ![Página para verificar as propriedades e o estado da VM](./media/azure-to-azure-tutorial-failover-failback/vm-state.png)

3. Antes de fazer o failover, verifique se:
    - A VM tem um sistema operacional [Windows](azure-to-azure-support-matrix.md#windows) ou [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage) compatível.
    - A VM está em conformidade com os requisitos de [computação](azure-to-azure-support-matrix.md#replicated-machines---compute-settings), [armazenamento](azure-to-azure-support-matrix.md#replicated-machines---storage) e [rede](azure-to-azure-support-matrix.md#replicated-machines---networking).

## <a name="run-a-failover"></a>Executar um failover


1. Na página de **visão geral** da VM, selecione **Failover**.

    ![Botão de failover para o item replicado](./media/azure-to-azure-tutorial-failover-failback/failover-button.png)

3. Em **Failover**, escolha um ponto de recuperação. A VM do Azure na região de destino é criada usando dados desse ponto de recuperação.
  
   - **Mais recente processado**: usa o último ponto de recuperação processado pelo Site Recovery. A carimbo de data/hora é mostrado. Nenhum tempo será gasto no processamento de dados. Portanto, ele fornecerá um RTO (objetivo do tempo de recuperação) baixo.
   -  **Mais recente**: processa todos os dados enviados ao Site Recovery para criar um ponto de recuperação para cada VM antes do failover. Fornece o RPO (objetivo do ponto de recuperação) mais baixo porque todos os dados serão replicados para o Site Recovery quando o failover for disparado.
   - **Consistente com o aplicativo mais recente**: essa opção faz failover de VMs para o ponto de recuperação mais recente e consistente com o aplicativo. A carimbo de data/hora é mostrado.
   - **Personalizado**: efetua o failover para um determinado ponto de recuperação. A configuração personalizada só está disponível quando você efetua o failover de uma única VM e não usa um plano de recuperação.

    > [!NOTE]
    > Se você adicionou um disco a uma VM depois de habilitar a replicação, os pontos de replicação mostrarão os discos disponíveis para recuperação. Por exemplo, um ponto de replicação criado antes de você adicionar um segundo disco será exibido como "1 de 2 discos".

4. Selecione **Desligar o computador antes do início do failover** se quiser que o Site Recovery tente desligar as VMs de origem antes de iniciar o failover. O desligamento ajuda a evitar a perda de dados. O failover continuará mesmo o desligamento falhar. 

    ![Página de configurações de failover](./media/azure-to-azure-tutorial-failover-failback/failover-settings.png)    

3. Para iniciar o failover, selecione **OK**.
4. Monitore o failover nas notificações.

    ![Notificação de andamento](./media/azure-to-azure-tutorial-failover-failback/notification-failover-start.png) ![Notificação de êxito](./media/azure-to-azure-tutorial-failover-failback/notification-failover-finish.png)     

5. Após o failover, a VM do Azure criada na região de destino aparecerá em **Máquinas Virtuais**. Verifique se a VM está em execução e dimensionada adequadamente. Para usar um ponto de recuperação diferente para a VM, selecione **Alterar ponto de recuperação** na página **Essenciais**.
6. Quando estiver satisfeito com a VM com failover, selecione **Confirmar** na página de visão geral para concluí-lo.

    ![Botão Confirmar](./media/azure-to-azure-tutorial-failover-failback/commit-button.png) 

7. Em **Confirmar**, clique em **OK** para confirmar. Isso exclui todos os pontos de recuperação disponíveis para a VM no Site Recovery, e você não poderá alterar o ponto de recuperação.

8. Monitore o progresso da confirmação na barra de notificações.

    ![Notificação de progresso da confirmação](./media/azure-to-azure-tutorial-failover-failback/notification-commit-start.png) ![Notificação de sucesso da confirmação](./media/azure-to-azure-tutorial-failover-failback/notification-commit-finish.png)    

9. O Site Recovery não limpa a VM de origem após o failover. Você precisa fazer isso manualmente.


## <a name="reprotect-the-vm"></a>Proteger a VM novamente

Após o failover, proteja a VM novamente na região secundária para que ela seja replicada de volta para a região primária. 

1. Verifique se o **Status** da VM é *Failover confirmado* antes de começar.
2. Verifique se você pode acessar a região primária e se tem permissões para criar VMs nela.
3. Na página de **visão geral** da VM, selecione **Proteger novamente**.

   ![Botão para habilitar a nova proteção para uma VM.](./media/azure-to-azure-tutorial-failover-failback/reprotect-button.png)

4. Em **Proteger novamente**, verifique a direção da replicação (da região secundária para a primária) e revise as configurações de destino da região primária. Os recursos marcados como novos serão criados pelo Site Recovery como parte da operação de nova proteção.

     ![Página de configurações da nova proteção](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

6. Clique em **OK** para iniciar o processo de nova proteção. O processo enviará dados iniciais para o local de destino e replicará informações delta de VMs para o destino.
7. Monitore o progresso da nova proteção nas notificações. 

    ![Notificação de progresso da nova proteção](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-start.png) ![Notificação de sucesso da nova proteção](./media/azure-to-azure-tutorial-failover-failback/notification-reprotect-finish.png)
    

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez failover da região primária para a secundária e iniciou a replicação de VMs de volta para a região primária. Agora é possível fazer failback da região secundária para a primária.

> [!div class="nextstepaction"]
> [Failback para a região primária](azure-to-azure-tutorial-failback.md)
