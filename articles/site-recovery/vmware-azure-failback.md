---
title: Fazer failback de VMs VMware/servidores físicos do Azure com Azure Site Recovery
description: Saiba como fazer o failback para o site local após o failover para o Azure durante a recuperação de desastre de VMs e servidores físicos VMware para o Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: aed015b67aa36e7678b31d7f2f047cb1e77c6a3d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004187"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>Failback de VMs do VMware para o site local

Este artigo descreve como fazer failback de VMs do Azure para um site local, seguindo o [failover](site-recovery-failover.md) de VMs locais para o Azure com [Azure site Recovery](site-recovery-overview.md). Após o failback para o local, você habilita a replicação para que as VMs locais iniciem a replicação no Azure.

## <a name="before-you-start"></a>Antes de começar

1. Saiba mais sobre [failback do VMware](failover-failback-overview.md#vmwarephysical-reprotectionfailback). 
2. Verifique se você analisou e concluiu as etapas para [preparar-se para o failback](vmware-azure-prepare-failback.md) e se todos os componentes necessários estão implantados. Os componentes incluem um servidor de processo no Azure, um servidor de destino mestre local e uma conexão VPN site a site (ou emparelhamento privado do ExpressRoute) para failback.
3. Verifique se você concluiu os [requisitos](vmware-azure-reprotect.md#before-you-begin) de nova proteção e failback e se [habilitou](vmware-azure-reprotect.md#enable-reprotection) a nova proteção de VMs do Azure, para que elas estejam replicando do Azure para o site local. As VMs devem estar em um estado replicado para fazerem failback.




## <a name="run-a-failover-to-fail-back"></a>Executar um failover para failback

1. Verifique se as VMs do Azure são protegidas novamente e replicando para o site local.
    - Uma VM precisa de pelo menos um ponto de recuperação para fazer failback.
    - Se você fizer failback de um plano de recuperação, todos os computadores no plano deverão ter pelo menos um ponto de recuperação.
2. No cofre > **Itens replicados**, selecione a VM. Clique com o botão direito do mouse em VM > **Failover Não Planejado**.
3. Em **Confirmar Failover**, verifique a direção do failover (do Azure).
4. Selecione o ponto de recuperação que você deseja usar para o failover.
    - Recomendamos que você use o ponto de recuperação **Mais recente**. O ponto consistente do aplicativo está atrás do último ponto no tempo e causa a perda de alguns dados.
    - O **Mais recente**, há um ponto de recuperação consistente em termos de falha.
    - Com **Mais recente**, uma VM faz failover para o ponto no tempo mais recente disponível. Se você tiver um grupo de replicação para consistência de várias VMs em um plano de recuperação, cada VM no grupo passará por failover para seu último ponto independente no tempo.
    - Caso você use um ponto de recuperação consistente com o aplicativo, cada VM fará failback para o último ponto disponível. Se um plano de recuperação tiver um grupo de replicação, cada grupo será recuperado para seu ponto de recuperação disponível comum.
5. O failover começa. O Site Recovery desliga as VMs do Azure.
6. Após a conclusão do failover, verifique se tudo está funcionando conforme o esperado. Verifique se as VMs do Azure estão desligadas. 
7. Com tudo verificado, clique com o botão direito do mouse na VM > **Commit** para concluir o processo de failover. O commit remove a VM do Azure com failover. 

> [!NOTE]
> Para VMs do Windows, o Site Recovery desabilita as ferramentas do VMware durante o failover. Durante o failback da VM do Windows, as ferramentas do VMware são habilitadas novamente. 




## <a name="reprotect-from-on-premises-to-azure"></a>Proteger novamente do local para o Azure

Depois de confirmar o failback, as VMs do Azure são excluídas. A VM está de volta no site local, mas não está protegida. Para começar a replicar VMs para o Azure novamente da seguinte maneira:

1. Em cofre > **Itens replicados**, selecione VMs com failback e **Proteger novamente**.
2. Especifique o servidor de processo que é usado para enviar dados de volta ao Azure.
3. Selecione **OK** para iniciar o trabalho de proteger novamente.

> [!NOTE]
> Depois que uma VM local é iniciada, leva até 15 minutos para que o agente seja registrado novamente no servidor de configuração. Durante esse tempo, proteger novamente falhará e retornará uma mensagem de erro informando que o agente não está instalado. Se isso ocorrer, aguarde alguns minutos e proteja novamente.

## <a name="next-steps"></a>Próximas etapas

Após a conclusão do trabalho de nova proteção, a VM local está replicando para o Azure. Conforme necessário, você pode [executar outro failover](site-recovery-failover.md) para o Azure.

