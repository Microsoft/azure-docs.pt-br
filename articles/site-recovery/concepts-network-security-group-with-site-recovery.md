---
title: Grupos de Segurança de Rede com Azure Site Recovery | Microsoft Docs
description: Descreve como usar os Grupos de Segurança de Rede com o Azure Site Recovery para recuperação de desastre e migração
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 0c06283080a4ee51f863714e4c515672299b420d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773003"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Grupos de Segurança de Rede com Azure Site Recovery

Os Grupos de Segurança de Rede são usados para limitar o tráfego de rede para recursos em uma rede virtual. Um [Grupo de Segurança de Rede (NSG)](../virtual-network/security-overview.md#network-security-groups) contém uma lista de regras de segurança que permitem ou negam o tráfego de rede de entrada ou saída com base no endereço IP de origem ou destino, na porta e no protocolo.

No modelo de implantação do Gerenciador de Recursos, NSGs podem ser associados a sub-redes ou interfaces de rede individuais. Quando um NSG está associado a uma sub-rede, as regras se aplicam a todos os recursos conectados à sub-rede. O tráfego pode ser restrito associando um NSG a interfaces de rede individuais em uma sub-rede que já tem um NSG associado.

Este artigo descreve como você pode usar Grupos de Segurança de Rede com o Azure Site Recovery.

## <a name="using-network-security-groups"></a>Usando Grupos de Segurança de Rede

Uma sub-rede individual pode ter zero ou um NSG associado. Uma interface de rede individual pode ter zero ou um NSG associado. Portanto, você pode efetivamente ter restrição de tráfego dupla para uma máquina virtual associando um NSG primeiro a uma sub-rede e, em seguida, outro NSG à interface de rede da VM. A aplicação de regras de NSG depende nesse caso da direção do tráfego e da prioridade das regras de segurança aplicadas.

Considere um exemplo simples com uma máquina virtual da seguinte forma:
-   A máquina virtual é colocada dentro da **sub-rede Contoso**.
-   **A sub-rede Contoso** está associada ao **NSG de sub-rede**.
-   A interface de rede de VM é associada adicionalmente ao **NSG de VM**.

![NSG com Site Recovery](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

Neste exemplo, para tráfego de entrada, o NSG de sub-rede é avaliado primeiro. Qualquer tráfego permitido por meio do NSG de sub-rede é avaliado pelo NSG de VM. O inverso é aplicável para o tráfego de saída, com o NSG de VM sendo avaliado primeiro. Qualquer tráfego permitido por meio do NSG de VM é avaliado pelo NSG de sub-rede.

Isso permite a aplicação de regra de segurança granular. Por exemplo, você talvez queira permitir o acesso de internet de entrada para algumas VMs de aplicativo (como VMs front-end) em uma sub-rede, mas restringir o acesso à internet de entrada para outras VMs (como banco de dados e outras VMs de back-end). Nesse caso, você pode ter uma regra mais branda no NSG de sub-rede, permitindo o tráfego de internet e restringir o acesso a VMs específicas por meio de negação do acesso no NSG de VM. O mesmo pode ser aplicado para o tráfego de saída.

Ao definir essas configurações de NSG, certifique-se de que as prioridades corretas sejam aplicadas para as [regras de segurança](../virtual-network/security-overview.md#security-rules). As regras são processadas na ordem de prioridade, com números mais baixos processados antes de números mais altos, pois os números mais baixos têm prioridade mais alta. Depois que o tráfego corresponde a uma regra, o processamento é interrompido. Assim, as regras existentes com baixa prioridade (números mais altos) que têm os mesmos atributos das regras com prioridades mais altas não são processadas.

Você pode não saber quando os grupos de segurança de rede são aplicados a um adaptador de rede e a uma sub-rede. É possível verificar as regras de agregação aplicadas a uma interface de rede exibindo as [regras de segurança efetiva](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules) para uma interface de rede. Você também pode usar o recurso [Verificar o fluxo de IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) no [Observador de Rede do Azure](../network-watcher/network-watcher-monitoring-overview.md) para determinar se a comunicação é permitida na entrada ou saída de um adaptador de rede. A ferramenta informa se a comunicação é permitida e qual regra de segurança de rede permite ou nega o tráfego.

## <a name="on-premises-to-azure-replication-with-nsg"></a>Replicação no local para Azure com NSG

O Azure Site Recovery permite a recuperação de desastres e migração para o Azure para o local [máquinas virtuais Hyper-V](hyper-v-azure-architecture.md), [máquinas virtuais VMware](vmware-azure-architecture.md), e [servidores físicos](physical-azure-architecture.md). Para todos os locais para cenários do Azure, os dados de replicação são enviados e armazenados em uma conta de armazenamento do Azure. Durante a replicação, não é necessário pagar todos os encargos de máquina virtual. Ao executar um failover no Azure, o Site Recovery cria automaticamente as máquinas virtuais da IaaS do Azure.

Depois que as VMs tiverem sido criadas após o failover no Azure, os NSGs poderão ser usados para limitar o tráfego de rede à rede virtual e às VMs. O Site Recovery não cria NSGs como parte da operação de failover. É recomendável criar os NSGs do Azure necessários antes de iniciar o failover. Em seguida, você pode associar os NSGs a VMs com failover automaticamente durante o failover, usando os scripts de automação com os [planos de recuperação](site-recovery-create-recovery-plans.md) poderosos do Site Recovery.

Por exemplo, se a configuração da VM após o failover for semelhante ao [cenário de exemplo](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) detalhado acima:
-   Você pode criar a **Rede Virtual Contoso** e a **Sub-rede Contoso** como parte do planejamento de DR na região do Azure de destino.
-   Você também pode criar e configurar o **NSG de sub-rede**, bem como o **NSG de VM** como parte do mesmo planejamento de DR.
-   **O NSG de sub-rede** pode ser imediatamente associado à **Sub-rede Contoso**, visto que o NSG e a sub-rede já estão disponíveis.
-   **O NSG de VM** pode ser associado a VMs durante o failover usando planos de recuperação.

Depois que os NSGs forem criados e configurados, recomendamos executar um [failover de teste](site-recovery-test-failover-to-azure.md) para verificar as associações de NSG com script e conectividade VM após o failover.

## <a name="azure-to-azure-replication-with-nsg"></a>Replicação do Azure para o Azure com o NSG

O Azure Site Recovery oferece recuperação de[Máquinas Virtuais do Azure](azure-to-azure-architecture.md). Ao habilitar a replicação para VMs do Azure, o Site Recovery pode criar as réplicas das redes virtuais (incluindo sub-redes e sub-redes de gateway) na região de destino, bem como criar os mapeamentos necessários entre as redes virtuais de origem e de destino. Também é possível criar previamente as redes e as sub-redes do lado do destino e usá-las ao habilitar a replicação. O Site Recovery não cria todas as máquinas virtuais na região do Azure de destino antes do [failover](azure-to-azure-tutorial-failover-failback.md).

Para a replicação de VM do Azure, certifique-se de que as regras de NSG na região do Azure de origem permitam a [conectividade de saída](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges) para o tráfego de replicação. Você também pode testar e verificar essas regras necessárias por meio desta [configuração do NSG de exemplo](azure-to-azure-about-networking.md#example-nsg-configuration).

O Site Recovery não cria ou replica NSGs como parte da operação de failover. É recomendável criar os NSGs necessários na região de destino do Azure antes de iniciar o failover. Em seguida, você pode associar os NSGs a VMs com failover automaticamente durante o failover, usando os scripts de automação com os [planos de recuperação](site-recovery-create-recovery-plans.md) poderosos do Site Recovery.

Considerando o [cenário de exemplo](concepts-network-security-group-with-site-recovery.md#using-network-security-groups) descrito anteriormente:
-   O Site Recovery pode criar réplicas de **Rede Virtual Contoso** e **Sub-rede Contoso** na região do Azure de destino quando a replicação estiver habilitada para a VM.
-   Você pode criar as réplicas desejadas de **NSG de sub-rede** e **NSG de VM** (denominadas, por exemplo, **NSG de sub-rede de destino** e **NSG de VM de destino**, respectivamente) na região do Azure de destino, permitindo quaisquer regras adicionais necessárias na região de destino.
-   **O NSG de sub-rede de destino** pode ser imediatamente associado à sub-rede da região de destino, visto que o NSG e a sub-rede já estão disponíveis.
-   **O NSG de VM de destino** pode ser associado a VMs durante o failover usando planos de recuperação.

Depois que os NSGs forem criados e configurados, recomendamos executar um [failover de teste](azure-to-azure-tutorial-dr-drill.md) para verificar as associações de NSG com script e conectividade VM após o failover.

## <a name="next-steps"></a>Próximas etapas
-   Saiba mais sobre [Grupos de Segurança de Rede](../virtual-network/security-overview.md#network-security-groups).
-   Saiba mais sobre as [regras de segurança](../virtual-network/security-overview.md#security-rules) de NSG.
-   Saiba mais sobre [regras de segurança efetiva](../virtual-network/diagnose-network-traffic-filter-problem.md) para um NSG.
-   Saiba mais sobre [planos de recuperação](site-recovery-create-recovery-plans.md) para automatizar o failover de aplicativo.
