---
title: Tutorial sobre como executar uma análise de recuperação de desastre de VMs do Azure usando o Azure Site Recovery
description: Neste tutorial, você executará uma análise de recuperação de desastre de VMs do Azure de outra região usando o Site Recovery.
services: site-recovery
ms.topic: tutorial
ms.date: 11/05/2020
ms.custom: mvc
ms.openlocfilehash: c7cd1898f27f3b7255009efb40f6bcc8938dbf9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395558"
---
# <a name="tutorial-run-a-disaster-recovery-drill-for-azure-vms"></a>Tutorial: executar uma análise de recuperação de desastre para VMs do Azure

Saiba como executar uma análise de recuperação de desastre em outra região do Azure para a replicação de VMs do Azure usando o [Azure Site Recovery](site-recovery-overview.md). Neste artigo você:

> [!div class="checklist"]
> * Verificar pré-requisitos
> * Verificar as configurações de uma VM antes da análise
> * Execute um teste de failover
> * Fazer uma limpeza após a análise


> [!NOTE]
> Este tutorial fornecerá poucas etapas para executar uma análise de recuperação de desastre. Caso queira executar uma análise com testes completos de infraestrutura, saiba mais sobre a [rede](azure-to-azure-about-networking.md) de VMs do Azure, a [automação](azure-to-azure-powershell.md) e [solução de problemas](azure-to-azure-troubleshoot-errors.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, será necessário habilitar a recuperação de desastre de uma ou mais VMs do Azure. [Conclua o primeiro tutorial](azure-to-azure-tutorial-enable-replication.md) desta série para fazer isso.

## <a name="verify-vm-settings"></a>Verificar as configurações da VM

1. No cofre > **Itens replicados**, selecione a VM.

    ![Opção para abrir a página de Recuperação de Desastre nas propriedades da VM](./media/azure-to-azure-tutorial-dr-drill/vm-settings.png)

2. Na página de **Visão Geral** da VM, verifique se ela está protegida e íntegra.
3. Ao executar um failover de teste, selecione uma rede virtual do Azure na região de destino. As VMs do Azure criadas após o failover serão colocadas nessa rede. 

    - Neste tutorial, selecionaremos uma rede existente quando executarmos o failover de teste.
    - Recomendamos escolher uma rede que não seja de produção para a análise, de modo que endereços IP e componentes de rede permaneçam disponíveis nas redes de produção.
   - Também será possível pré-configurar as definições de rede a serem usadas no failover de teste. As configurações granulares que podem ser atribuídas a cada NIC incluem: sub-rede, endereço IP privado, endereço IP público, balanceador de carga e grupo de segurança de rede. Não usaremos esse método aqui, porém você poderá [examinar este artigo](azure-to-azure-customize-networking.md#customize-failover-and-test-failover-networking-configurations) para saber mais.


## <a name="run-a-test-failover"></a>Execute um teste de failover


1. Na página de **Visão Geral**, selecione **Failover de Teste**.

    
    ![Botão do failover de teste do item replicado](./media/azure-to-azure-tutorial-dr-drill/test-failover-button.png)

2. Em **Failover de Teste**, escolha um ponto de recuperação. A VM do Azure na região de destino é criada usando dados desse ponto de recuperação.
  
   - **Mais recente processado**: usa o último ponto de recuperação processado pelo Site Recovery. A carimbo de data/hora é mostrado. Nenhum tempo será gasto no processamento de dados. Portanto, ele fornecerá um RTO (objetivo do tempo de recuperação) baixo.
   -  **Mais recente**: processa todos os dados enviados ao Site Recovery para criar um ponto de recuperação para cada VM antes do failover. Fornece o RPO (objetivo do ponto de recuperação) mais baixo porque todos os dados serão replicados para o Site Recovery quando o failover for disparado.
   - **Consistente com o aplicativo mais recente**: essa opção faz failover de VMs para o ponto de recuperação mais recente e consistente com o aplicativo. A carimbo de data/hora é mostrado.
   - **Personalizado**: efetua o failover para um determinado ponto de recuperação. A configuração personalizada só está disponível quando você efetua o failover de uma única VM e não usa um plano de recuperação.

3. Em **Rede virtual do Azure**, selecione a rede de destino na qual você deseja colocar as VMs do Azure criadas após o failover. Se possível, selecione uma rede que não seja de produção em vez da rede que foi criada quando a replicação foi habilitada.

    ![Página de configurações do failover de teste](./media/azure-to-azure-tutorial-dr-drill/test-failover-settings.png)    

4. Para iniciar o failover, selecione **OK**.
5. Monitore o failover de teste por meio de notificações.

    ![Notificação de andamento](./media/azure-to-azure-tutorial-dr-drill/notification-start-test-failover.png) ![Notificação de êxito](./media/azure-to-azure-tutorial-dr-drill/notification-finish-test-failover.png)     


5. Após a conclusão do failover, a VM do Azure criada na região de destino aparecerá no portal do Azure em **Máquinas Virtuais**. Verifique se a VM está em execução, tem o tamanho apropriado e está conectada à rede selecionada.

## <a name="clean-up-resources"></a>Limpar recursos

1. Na página **Essenciais**, selecione **Limpar failover de teste**.

    ![Botão para iniciar o processo de limpeza](./media/azure-to-azure-tutorial-dr-drill/select-cleanup.png)

2. Em **Limpeza do failover de teste** > **Observações**, registre e salve as observações associadas ao failover de teste. 
3. Selecione **O teste foi concluído** para excluir as VMs criadas durante o failover de teste.

    ![Página com opções de limpeza](./media/azure-to-azure-tutorial-dr-drill/cleanup-failover.png)

4. Monitore o andamento da limpeza por meio de notificações.

    ![Notificação de andamento da limpeza](./media/azure-to-azure-tutorial-dr-drill/notification-start-cleanup.png) ![Notificação de êxito da limpeza](./media/azure-to-azure-tutorial-dr-drill/notification-finish-cleanup.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você executou uma análise de recuperação de desastre para verificar se um failover funciona conforme o esperado. Agora, você pode experimentar um failover completo.

> [!div class="nextstepaction"]
> [Executar um failover de produção](azure-to-azure-tutorial-failover-failback.md)
