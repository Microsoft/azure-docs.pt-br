---
title: "Mapeamento de rede entre duas regiões do Azure no Azure Site Recovery | Microsoft Docs"
description: "O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos. Saiba mais sobre o failover no Azure ou em um datacenter secundário."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/11/2017
ms.author: pratshar
ms.openlocfilehash: 9d6a806ec533259797080fbfee2c38f918ebd8a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="network-mapping-between-two-azure-regions"></a>Mapeamento de rede entre duas regiões do Azure


Este artigo descreve como mapear as redes virtuais do Azure de duas regiões do Azure entre si. O mapeamento de rede garante que quando a máquina virtual replicada for criada na região do Azure de destino, ela será criada em uma rede virtual que é mapeada para uma rede virtual da máquina virtual de origem.  

## <a name="prerequisites"></a>Pré-requisitos
Antes de mapear as redes, certifique-se de que você criou [redes virtuais do Azure](../virtual-network/virtual-networks-overview.md) em ambas as regiões do Azure de origem e de destino.

## <a name="map-networks"></a>Mapear redes

Para mapear uma rede virtual do Azure em uma região do Azure para outra rede virtual em outra região, vá para a Infraestrutura do Site Recovery -> Mapeamento de Rede (para Máquinas virtuais do Azure) e crie um mapeamento de rede.

![Mapeamento de rede](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


No exemplo a seguir, minha máquina virtual está em execução na região da Ásia Oriental e está sendo replicada como Sudeste Asiático.

Selecione a rede de origem e de destino e, em seguida, clique em OK para criar um mapeamento de rede da Ásia Oriental como Sudeste Asiático.

![Mapeamento de rede](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


Faça o mesmo para criar um mapeamento de rede do Sudeste Asiático para Ásia Oriental.  
![Mapeamento de rede](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="mapping-network-when-enabling-replication"></a>Mapeamento de rede ao habilitar a replicação

Se o mapeamento de rede não for feito, quando estiver replicando uma máquina virtual pela primeira vez, formando uma região do Azure para outra, então você poderá escolher a rede de destino como parte do mesmo processo. O Site Recovery cria mapeamentos de rede a partir da região de origem para a região de destino e a partir da região de destino para a região de origem com base nessa seleção.   

![Mapeamento de rede](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

Por padrão, o Site Recovery cria uma rede na região de destino que é idêntica à rede de origem e adicionando “-asr” como um sufixo ao nome da rede de origem. Você pode escolher uma rede já criada, clicando em Personalizar.

![Mapeamento de rede](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)


Se o mapeamento de rede já foi feito, você não pode alterar a rede virtual de destino durante a habilitação da replicação. Para alterá-la, modifique o mapeamento de rede existente.  

![Mapeamento de rede](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![Mapeamento de rede](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> Se você modificar um mapeamento de rede da região 1 para a região 2, certifique-se de que você modificou o mapeamento de rede da região 2 para a região 1 também.
>
>


## <a name="subnet-selection"></a>Seleção de sub-rede
A sub-rede da máquina virtual de destino é selecionada de acordo com o nome da sub-rede da máquina virtual de origem. Se houver uma sub-rede com o mesmo nome da máquina virtual de origem disponível na rede de destino, então ele será escolhido para a máquina virtual de destino. Se não houver nenhuma sub-rede com o mesmo nome na rede de destino, então a primeira sub-rede em ordem alfabética será escolhida como a sub-rede de destino. Você pode modificar esta sub-rede indo para as configurações de Computação e Rede da máquina virtual.

![Modificar sub-rede](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>Endereço IP

O endereço IP para cada interface de rede da máquina virtual de destino é escolhido da seguinte maneira:

### <a name="dhcp"></a>DHCP
Se a interface de rede da máquina virtual de origem estiver usando DHCP, a interface de rede da máquina virtual de destino também será definida como DHCP.

### <a name="static-ip"></a>IP Estático
Se a interface de rede da máquina virtual de origem estiver usando um IP estático, a interface de rede da máquina virtual de destino também será definida para usar IP estático. O IP estático é escolhido da seguinte maneira:

#### <a name="same-address-space"></a>Mesmo espaço de endereço

Se a sub-rede de origem e a sub-rede de destino tiverem o mesmo espaço de endereço, o IP de destino será definido igual ao IP da interface de rede da máquina virtual de origem. Se o mesmo IP não estiver disponível, alguns outros IP disponíveis serão definidos como o IP de destino.

#### <a name="different-address-space"></a>Espaço de endereço diferente

Se a sub-rede de origem e a sub-rede de destino tiverem um espaço de endereço diferente, então o IP de destino será definido como qualquer IP disponível na sub-rede de destino.

Você pode modificar o IP de destino em cada interface de rede, vá para as configurações de Computação e Rede da máquina virtual.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [diretrizes de rede para replicação de VMs do Azure](site-recovery-azure-to-azure-networking-guidance.md).
