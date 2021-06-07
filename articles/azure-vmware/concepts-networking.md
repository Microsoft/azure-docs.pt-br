---
title: Conceitos-interconectividade de rede
description: Saiba mais sobre os principais aspectos e casos de uso de rede e interconectividade na solução do Azure VMware.
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 4c964151c49e2fea56031dd24bacf4655753a18d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103491802"
---
# <a name="azure-vmware-solution-networking-and-interconnectivity-concepts"></a>Conceitos de rede e interconectividade da solução do Azure VMware

[!INCLUDE [avs-networking-description](includes/azure-vmware-solution-networking-description.md)]

Há duas maneiras de interconectividade na nuvem privada da solução Azure VMware:

- A [**interconectividade básica somente do Azure**](#azure-virtual-network-interconnectivity) permite que você gerencie e use sua nuvem privada com apenas uma única rede virtual no Azure. Essa implementação é mais adequada para avaliações ou implementações da solução Azure VMware que não exigem acesso de ambientes locais.

- A [**interconectividade completa de local para nuvem privada**](#on-premises-interconnectivity) estende a implementação básica somente do Azure para incluir a interconectividade entre as nuvens privadas da solução do Azure VMware e locais.
 
Neste artigo, abordaremos os principais conceitos que estabelecem rede e interconectividade, incluindo requisitos e limitações. Este artigo fornece as informações que você precisa saber para configurar sua rede para trabalhar com a solução VMware do Azure.

## <a name="azure-vmware-solution-private-cloud-use-cases"></a>Casos de uso de nuvem privada da solução Azure VMware

Os casos de uso das nuvens privadas da solução Azure VMware incluem:
- Novas cargas de trabalho de VM VMware na nuvem
- Carga de trabalho de VM intermitente para a nuvem (somente para a solução do Azure VMware)
- Migração de carga de trabalho de VM para a nuvem (somente para solução de VMware local para Azure)
- Recuperação de desastre (solução do Azure VMware para solução Azure VMware ou local para solução VMware do Azure)
- Consumo de serviços do Azure

> [!TIP]
> Todos os casos de uso para o serviço de solução do Azure VMware são habilitados com conectividade de nuvem local para privada.

## <a name="azure-virtual-network-interconnectivity"></a>Interconectividade de rede virtual do Azure

Você pode interconectar sua rede virtual do Azure à implementação da nuvem privada da solução Azure VMware. Você pode gerenciar sua nuvem privada da solução Azure VMware, consumir cargas de trabalho em sua nuvem privada e acessar outros serviços do Azure.

O diagrama a seguir mostra a interconectividade de rede básica estabelecida no momento de uma implantação de nuvem privada. Ele mostra a rede lógica entre uma rede virtual no Azure e uma nuvem privada. Essa conectividade é estabelecida por meio de um ExpressRoute de back-end que faz parte do serviço de solução do Azure VMware. A interconectividade atende aos seguintes casos de uso primário:

- Acesso de entrada ao vCenter Server e ao NSX-T Manager que é acessível de VMs em sua assinatura do Azure.
- Acesso de saída de VMs na nuvem privada para os serviços do Azure.
- Acesso de entrada das cargas de trabalho em execução na nuvem privada.


:::image type="content" source="media/concepts/adjacency-overview-drawing-single.png" alt-text="Rede virtual básica para conectividade de nuvem privada" border="false":::

## <a name="on-premises-interconnectivity"></a>Interconectividade local

No cenário totalmente interconectado, você pode acessar a solução Azure VMware de suas redes virtuais do Azure e locais. Essa implementação é uma extensão da implementação básica descrita na seção anterior. Um circuito do ExpressRoute é necessário para conectar-se do local à sua nuvem privada da solução Azure VMware no Azure.

O diagrama a seguir mostra a interconectividade de local para nuvem privada, que habilita os seguintes casos de uso:

- O vMotion vCenter quente/frio entre a solução local e o Azure VMware.
- Acesso de gerenciamento de nuvem privada de solução local para o Azure VMware.

:::image type="content" source="media/concepts/adjacency-overview-drawing-double.png" alt-text="Conectividade de nuvem privada completa local e de rede virtual" border="false":::

Para ter total interconectividade com sua nuvem privada, você precisa habilitar o ExpressRoute Alcance Global e, em seguida, solicitar uma chave de autorização e uma ID de emparelhamento privado para Alcance Global no portal do Azure. A chave de autorização e a ID de emparelhamento são usadas para estabelecer Alcance Global entre um circuito de ExpressRoute em sua assinatura e o circuito de ExpressRoute para sua nuvem privada. Uma vez vinculado, os dois circuitos do ExpressRoute roteiam o tráfego de rede entre seus ambientes locais para sua nuvem privada. Para obter mais informações sobre os procedimentos, consulte o [tutorial para criar um ExpressRoute alcance global emparelhamento para uma nuvem privada](tutorial-expressroute-global-reach-private-cloud.md).

## <a name="limitations"></a>Limitações
[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## <a name="next-steps"></a>Próximas etapas 

Agora que você abordou os conceitos de rede e interconectividade da solução do Azure VMware, talvez queira saber mais sobre:

- [Conceitos de armazenamento da solução VMware do Azure](concepts-storage.md).
- [Conceitos de identidade da solução VMware do Azure](concepts-identity.md).
- [Como habilitar o recurso de solução VMware do Azure](enable-azure-vmware-solution.md).

<!-- LINKS - external -->
[enable Global Reach]: ../expressroute/expressroute-howto-set-global-reach.md

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
[concepts-storage]: ./concepts-storage.md
