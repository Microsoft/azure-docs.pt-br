---
title: Configurar o failover de VMs do Hyper-V para o Azure no Azure Site Recovery
description: Saiba como fazer failover de VMs do Hyper-V no Azure com o Azure Site Recovery.
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.custom: MVC
ms.openlocfilehash: bb012f7cc5faa5e286b617785512ffa463c213bb
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579539"
---
# <a name="fail-over-hyper-v-vms-to-azure"></a>Fazer failover de VMs do Hyper-V no Azure

Este tutorial descreve como fazer failover de VMs do Hyper-V no Azure com o [Azure Site Recovery](site-recovery-overview.md). Depois de executar failover, execute failback para o site local quando ele estiver disponível. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Verificar as propriedades da VM do Hyper-V para saber se elas estão em conformidade com os requisitos do Azure.
> * Faça failover de VMs específicas no Azure.


Este tutorial é o quinto tutorial de uma série. Presume-se que você já tenha concluído as tarefas nos tutoriais anteriores.    

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Preparar Hyper-V local](./hyper-v-prepare-on-premises-tutorial.md)
3. Configurar a recuperação de desastres para as [VMs do Hyper-V](./hyper-v-azure-tutorial.md) ou para [VMs do Hyper-V gerenciadas em nuvens do System Center VMM](./hyper-v-vmm-azure-tutorial.md)
4. [Realizar uma simulação de recuperação de desastre](tutorial-dr-drill-azure.md)

[Saiba mais sobre](failover-failback-overview.md#types-of-failover) diferentes tipos de failover. Se você quiser fazer failover de várias VMs em um plano de recuperação, examine [este artigo](site-recovery-failover.md).

## <a name="prepare-for-failover"></a>Preparar para failover 
Certifique-se de que não haja instantâneos na VM e que a VM local esteja desativada durante o failback. Isso ajuda a garantir a consistência dos dados durante a replicação. Não ative a VM local durante o failback. 

O failover e o failback têm três estágios:

1. **Failover para Azure**: fazer failover de VMs do Hyper-V do site local para Azure.
2. **Failback para local**: fazer failover de VMs do Azure para o site local, quando disponível. Ele inicia a sincronização de dados do Azure para o local e, após a conclusão, ele abre as VMs no local.  
3. **Replicação inversa de VMs locais**: após o failback no local, replique inversamente as VMs locais para começar a replicá-las no Azure.

## <a name="verify-vm-properties"></a>Verificar as propriedades da VM

Antes do failover, verifique as propriedades da VM e verifique se a VM atende aos [requisitos do Azure](hyper-v-azure-support-matrix.md#replicated-vms).

Em **Itens Protegidos**, clique em **Itens Replicados** > VM.

1. No painel **Item Replicado**, há um resumo das informações da VM, o status de integridade e os últimos pontos de recuperação disponíveis. Clique em **Propriedades** para exibir mais detalhes.

1. Em **Computação e Rede**, você pode modificar o nome do Azure, o grupo de recursos, o tamanho do destino, o [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) e as configurações de disco gerenciado.

1. Você pode exibir ou modificar as configurações de rede, incluindo a rede/sub-rede na qual a VM do Azure estará localizada após o failover e o endereço IP que será atribuído a ela.

1. Em **Discos**, é possível visualizar as informações sobre o sistema operacional e os discos de dados na VM.

## <a name="fail-over-to-azure"></a>Fazer failover para o Azure

1. Em **Configurações** > **Itens replicados** clique em VM > **Failover**.
2. Em **Failover**, selecione o **Último** ponto de recuperação. 
3. Selecione **Desligar o computador antes do início do failover**. O Site Recovery tenta desligar as VMs de origem antes de acionar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .
4. Depois de verificar o failover, clique em **Confirmar**. Exclui todos os pontos de recuperação disponíveis.

> [!WARNING]
> **Não cancele um failover em andamento**: se você cancelar em andamento, o failover será interrompido, mas a VM não será replicada novamente.

## <a name="connect-to-failed-over-vm"></a>Conectar-se à VM cujo failover foi realizado

1. Se você quiser se conectar a VMs do Azure após fazer failover usando o protocolo RDP e o SSH (Secure Shell), [verifique se os requisitos foram atendidos](failover-failback-overview.md#connect-to-azure-after-failover).
2. Após o failover, vá para a VM e valide [conectando](../virtual-machines/windows/connect-logon.md)-se a ela.
3. Use **Alterar ponto de recuperação** se você quiser usar um ponto de recuperação diferente após o failover. Após você confirmar o failover na próxima etapa, essa opção não estará mais disponível.
4. Após a validação, selecione **Confirmar** para finalizar o ponto de recuperação da VM após o failover.
5. Após a confirmação, todos os outros pontos de recuperação disponíveis são excluídos. A etapa conclui o failover.

>[!TIP]
> Se você encontrar problemas de conectividade após o failover, siga o [guia de solução de problemas](site-recovery-failover-to-azure-troubleshoot.md).


## <a name="next-steps"></a>Próximas etapas

Após o failover, proteja novamente as VMs do Azure para que elas sejam replicadas do Azure para o local. Em seguida, depois que as VMs estiverem protegidas novamente e replicando para o site local, faça failback do Azure quando estiver pronto.
