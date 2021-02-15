---
title: Cenários para usar uma rede virtual
description: Cenários, recursos e limitações para implantar grupos de contêineres em uma rede virtual do Azure.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 65d7fa46ebbb9b072b50731bff68b9b88809075d
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033822"
---
# <a name="virtual-network-scenarios-and-resources"></a>Cenários e recursos de rede virtual

A [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md) fornece uma rede privada segura para seus recursos locais e do Azure. Implantando grupos de contêineres em uma rede virtual do Azure, os contêineres podem se comunicar com segurança com outros recursos na rede virtual. 

Este artigo fornece informações sobre cenários de rede virtual, limitações e recursos. Para obter exemplos de implantação usando o CLI do Azure, consulte [implantar instâncias de contêiner em uma rede virtual do Azure](container-instances-vnet.md).

## <a name="scenarios"></a>Cenários

Os grupos de contêineres implantados em uma rede virtual do Azure permitem cenários como:

* Comunicação direta entre grupos de contêineres na mesma sub-rede
* Enviar saída de carga de trabalho [baseada em tarefa](container-instances-restart-policy.md) de instâncias de contêiner para um banco de dados na rede virtual
* Recuperar o conteúdo para instâncias de contêiner de um [ponto de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) na rede virtual
* Habilitar a comunicação de contêiner com recursos locais por meio de um [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou [ExpressRoute](../expressroute/expressroute-introduction.md)
* Integre com o [Firewall do Azure](../firewall/overview.md) para identificar o tráfego de saída proveniente do contêiner 
* Resolver nomes por meio do DNS interno do Azure para comunicação com recursos do Azure na rede virtual, como máquinas virtuais
* Usar regras NSG para controlar o acesso de contêiner a sub-redes ou outros recursos de rede

## <a name="unsupported-networking-scenarios"></a>Cenários de rede sem suporte 

* Não há suporte para **Azure Load Balancer** colocar uma Azure Load Balancer na frente das instâncias de contêiner em um grupo de contêineres de rede
* **Emparelhamento de rede virtual global** -o emparelhamento global (conexão de redes virtuais entre regiões do Azure) não é suportado
* **Rótulo de IP público ou DNS** -grupos de contêineres implantados em uma rede virtual atualmente não dão suporte à exposição de contêineres diretamente à Internet com um endereço IP público ou um nome de domínio totalmente qualificado
* NAT-grupos de contêineres de **rede virtual** implantados em uma rede virtual não dão suporte ao uso de um recurso de gateway NAT para conectividade de Internet de saída.

## <a name="other-limitations"></a>Outras limitações

* Atualmente, somente os contêineres do Linux têm suporte em um grupo de contêineres implantado em uma rede virtual.
* Para implantar grupos de contêineres em uma sub-rede, a sub-rede não pode conter outros tipos de recursos. Remova todos os recursos existentes de uma sub-rede existente antes de implantar grupos de contêineres nela ou crie uma nova sub-rede.
* Você não pode usar uma [identidade gerenciada](container-instances-managed-identity.md) em um grupo de contêineres implantado em uma rede virtual.
* Você não pode habilitar uma investigação de [vida](container-instances-liveness-probe.md) ou [investigação de preparação](container-instances-readiness-probe.md) em um grupo de contêineres implantado em uma rede virtual.
* Devido aos recursos de rede adicionais envolvidos, as implantações em uma rede virtual normalmente são mais lentas do que a implantação de uma instância de contêiner padrão.
* Se você estiver conectando seu grupo de contêineres a uma conta de armazenamento do Azure, deverá adicionar um [ponto de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) a esse recurso.

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="where-to-deploy"></a>Onde implantar

As seguintes regiões e recursos máximos estão disponíveis para implantar um grupo de contêineres em uma rede virtual do Azure.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="required-network-resources"></a>Recursos de rede necessários

Três recursos de Rede Virtual do Azure são necessários para a implantação de grupos de contêineres em uma rede virtual: a [rede virtual](#virtual-network) propriamente dita, uma [sub-rede delegada](#subnet-delegated) dentro da rede virtual e um [perfil de rede](#network-profile). 

### <a name="virtual-network"></a>Rede virtual

Uma rede virtual define o espaço de endereço no qual você cria uma ou mais sub-redes. Em seguida, você implanta recursos do Azure (como grupos de contêineres) nas sub-redes na sua rede virtual.

### <a name="subnet-delegated"></a>Sub-rede (delegada)

As sub-redes segmentam a rede virtual em espaços de endereço separados utilizáveis pelos recursos do Azure colocados neles. Você cria uma ou várias sub-redes dentro de uma rede virtual.

A sub-rede que você usa para grupos de contêineres poderá conter somente grupos de contêineres. Quando você implanta um grupo de contêineres em uma sub-rede pela primeira vez, o Azure delega essa sub-rede às Instâncias de Contêiner do Azure. Depois de delegada, a sub-rede pode ser usada apenas para grupos de contêineres. Se você tentar implantar recursos diferentes de grupos de contêineres em uma sub-rede delegada, a operação falhará.

### <a name="network-profile"></a>Perfil de rede

Um perfil de rede é um modelo de configuração de rede para recursos do Azure. Ele especifica determinadas propriedades de rede para o recurso; por exemplo, a sub-rede na qual ele deve ser implantado. Quando você usa o comando [az container create][az-container-create] pela primeira vez para implantar um grupo de contêiner em uma sub-rede (e, portanto, em uma rede virtual), o Azure cria um perfil de rede para você. Em seguida, você pode usar esse perfil de rede para implantações futuras na sub-rede. 

Para usar um modelo do Resource Manager, um arquivo YAML ou um método programático para implantar um grupo de contêiner em uma sub-rede, é necessário fornecer o ID completo do recurso do Gerenciador de Recursos de um perfil de rede. Você pode usar um perfil criado anteriormente usando o [az contêiner create][az-container-create] ou criar um perfil usando um modelo do Resource Manager (consulte [exemplo de modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) e [referência](/azure/templates/microsoft.network/networkprofiles)). Para obter o ID de um perfil criado anteriormente, use o comando [lista de perfis de rede az][az-network-profile-list]. 

No diagrama a seguir, vários grupos de contêineres foram implantados em uma sub-rede delegada a Instâncias de Contêiner do Azure. Depois de implantar um grupo de contêineres em uma sub-rede, você pode implantar grupos de contêineres adicionais nela especificando o mesmo perfil de rede.

![Grupos de contêineres dentro de uma rede virtual][aci-vnet-01]

## <a name="next-steps"></a>Próximas etapas

* Para obter exemplos de implantação com o CLI do Azure, consulte [implantar instâncias de contêiner em uma rede virtual do Azure](container-instances-vnet.md).
* Para implantar uma nova rede virtual, sub-rede, perfil de rede e grupo de contêiner usando um modelo do Resource Manager, consulte [criar um grupo de contêiner do Azure com rede virtual](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
).
* Ao usar o [portal do Azure](container-instances-quickstart-portal.md) para criar uma instância de contêiner, você também pode fornecer configurações para uma rede virtual nova ou do exstinger na guia **rede** .


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-virtual-network-concepts/aci-vnet-01.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
