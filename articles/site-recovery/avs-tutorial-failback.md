---
title: Fazer failback de VMs da Solução VMware no Azure do Azure com o Azure Site Recovery
description: Saiba como fazer failback para a nuvem privada da Solução VMware no Azure após o failover para o Azure durante a recuperação de desastre.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: fb14e647d3444f2f0d0cb86901f93582a18848f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91814162"
---
# <a name="fail-back-vms-to-azure-vmware-solution-private-cloud"></a>Failback de VMs para a nuvem privada da Solução VMware no Azure

Este artigo descreve como fazer failback de VMs do Azure para uma nuvem privada da Solução VMware no Azure, seguindo o [failover](avs-tutorial-failover.md) de VMs da Solução VMware no Azure para o Azure com o [Azure Site Recovery](site-recovery-overview.md). Após o failback, você habilita a replicação para que as VMs da Solução VMware no Azure comecem a replicar para o Azure.

## <a name="before-you-start"></a>Antes de começar

1. Saiba mais sobre [failback do VMware](failover-failback-overview.md#vmwarephysical-reprotectionfailback). 
2. Verifique se você analisou e concluiu as etapas para [preparar-se para o failback](vmware-azure-prepare-failback.md) e se todos os componentes necessários estão implantados. Os componentes incluem um servidor de processo no Azure, um servidor de destino mestre e uma conexão site a site da VPN (ou emparelhamento privado do ExpressRoute) para failback.
3. Verifique se você concluiu os [requisitos](avs-tutorial-reprotect.md#before-you-begin) para nova proteção e failback e [habilitou a nova proteção](avs-tutorial-reprotect.md#enable-reprotection) de VMs do Azure, de modo que estejam replicando do Azure para a nuvem privada da Solução VMware no Azure. As VMs devem estar em um estado replicado para fazerem failback.




## <a name="run-a-failover-to-fail-back"></a>Executar um failover para failback

1. Verifique se as VMs do Azure foram protegidas novamente e estão replicando para a nuvem privada da Solução VMware no Azure.
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




## <a name="reprotect-from-azure-vmware-solution-to-azure"></a>Proteja novamente da Solução VMware no Azure para o Azure

Depois de confirmar o failback, as VMs do Azure são excluídas. A VM volta para a nuvem privada da Solução VMware no Azure, mas não está protegida. Para começar a replicar VMs para o Azure novamente da seguinte maneira:

1. Em cofre > **Itens replicados**, selecione VMs com failback e **Proteger novamente**.
2. Especifique o servidor de processo que é usado para enviar dados de volta ao Azure.
3. Selecione **OK** para iniciar o trabalho de proteger novamente.

> [!NOTE]
> Depois que uma VM da Solução VMware no Azure é iniciada, são necessários até 15 minutos para o agente ser registrado novamente no servidor de configuração. Durante esse tempo, proteger novamente falhará e retornará uma mensagem de erro informando que o agente não está instalado. Se isso ocorrer, aguarde alguns minutos e proteja novamente.

## <a name="next-steps"></a>Próximas etapas

Após a conclusão do trabalho de nova proteção, a VM da Solução VMware no Azure está replicando para o Azure. Conforme necessário, você pode [executar outro failover](avs-tutorial-failover.md) para o Azure.

