---
title: Emparelhamento de Rede Virtual do Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre o emparelhamento de rede virtual no Azure, incluindo como ele permite que você conecte redes na rede virtual do Azure.
services: virtual-network
documentationcenter: na
author: altambaw
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: kumud
ms.openlocfilehash: 1356c896abff8c525df283251240d027123405fc
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778282"
---
# <a name="virtual-network-peering"></a>Emparelhamento de rede virtual

O emparelhamento de rede virtual permite que você conecte diretamente duas ou mais [redes virtuais](virtual-networks-overview.md) no Azure. As redes virtuais aparecem como uma para fins de conectividade. O tráfego entre máquinas virtuais em redes virtuais emparelhadas usa a infraestrutura de backbone da Microsoft. Assim como o tráfego entre as máquinas virtuais na mesma rede, o tráfego é roteado somente pela rede *privada* da Microsoft.

O Azure dá suporte aos seguintes tipos de emparelhamento:

* **Emparelhamento de rede virtual**: Conecte redes virtuais na mesma região do Azure.
* **Emparelhamento de rede virtual global**: conectando redes virtuais em regiões do Azure.

Os benefícios do uso do emparelhamento de rede virtual, seja local ou global, incluem:

* Baixa latência, conexão com largura de banda alta entre os recursos em redes virtuais diferentes.
* A capacidade de recursos em uma rede virtual se comunicarem com recursos em uma rede virtual diferente.
* A capacidade de transferir dados entre redes virtuais entre as assinaturas do Azure, Azure Active Directory locatários, modelos de implantação e regiões do Azure.
* A capacidade de emparelhar redes virtuais criadas por meio do Azure Resource Manager.
* A capacidade de emparelhar uma rede virtual criada por meio do Resource Manager para uma criada por meio do modelo de implantação clássico. Para saber mais sobre os modelos de implantação do Azure, confira [Entender os modelos de implantação do Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Nenhum tempo de inatividade para recursos em qualquer rede virtual ao criar o emparelhamento ou depois que o emparelhamento é criado.

O tráfego de rede entre redes virtuais emparelhadas é particular. O tráfego entre as redes virtuais é mantido na rede de backbone da Microsoft. Não são necessários a Internet pública, os gateways ou a criptografia na comunicação entre as redes virtuais.

## <a name="connectivity"></a>Conectividade

Para redes virtuais emparelhadas, os recursos em qualquer rede virtual podem se conectar diretamente com recursos na rede virtual emparelhada.

A latência de rede entre as máquinas virtuais em redes virtuais emparelhadas na mesma região é a mesma latência de uma única rede virtual. A taxa de transferência da rede tem base na largura de banda permitida para a máquina virtual, que é proporcional ao seu tamanho. Não existe restrição adicional quanto à largura de banda no emparelhamento.

O tráfego entre as máquinas virtuais nas redes virtuais emparelhadas é roteado diretamente pela infraestrutura de backbone da Microsoft, não por um gateway ou pela Internet pública.

Você pode aplicar grupos de segurança de rede em qualquer rede virtual para bloquear o acesso a outras redes virtuais ou sub-redes.
Ao configurar o emparelhamento de rede virtual, abra ou feche as regras do grupo de segurança de rede entre as redes virtuais. Se você abrir a conectividade completa entre redes virtuais emparelhadas, poderá aplicar grupos de segurança de rede para bloquear ou negar acesso específico. A conectividade completa é a opção padrão. Para saber mais sobre grupos de segurança de rede, consulte [grupos de segurança](./network-security-groups-overview.md).

## <a name="service-chaining"></a>Encadeamento de serviços

O encadeamento de serviços permite direcionar o tráfego de uma rede virtual para um dispositivo virtual ou gateway em uma rede emparelhada por meio de rotas definidas pelo usuário.

Para habilitar o encadeamento de serviços, configure rotas definidas pelo usuário que apontem para máquinas virtuais em redes virtuais emparelhadas como o endereço IP do *próximo salto* . As rotas definidas pelo usuário também podem apontar para gateways de rede virtual para habilitar o encadeamento de serviços.

Você pode implantar redes Hub e spoke, onde a rede virtual do Hub hospeda componentes de infraestrutura, como uma solução de virtualização de rede ou um gateway de VPN. Todas as redes virtuais contadas podem emparelhar com a rede virtual do hub. O tráfego flui através de dispositivos de virtualização de rede ou gateways de VPN na rede virtual do Hub.

O emparelhamento de redes virtuais permite que o próximo salto em uma rota definida pelo usuário seja o endereço IP de uma máquina virtual na rede virtual emparelhada, ou o gateway de VPN. Não é possível rotear entre redes virtuais com uma rota definida pelo usuário que especifique um gateway de ExpressRoute do Azure como o tipo do próximo salto. Para saber mais sobre as rotas definidas pelo usuário, confira [visão geral das rotas definidas pelo usuário](virtual-networks-udr-overview.md#user-defined). Para saber como criar uma topologia de rede hub e spoke, confira [topologia de rede hub-spoke no Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Gateways e conectividade local

Cada rede virtual, incluindo uma rede virtual emparelhada, pode ter seu próprio gateway. Uma rede virtual pode usar seu gateway para se conectar a uma rede local. Você também pode configurar [conexões de rede virtual para virtual](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) usando gateways, mesmo para redes virtuais emparelhadas.

Quando você configura as duas opções para a interconectividade de rede virtual, o tráfego entre as redes virtuais flui por meio da configuração de emparelhamento. O tráfego usa o backbone do Azure.

Você também pode configurar o gateway na rede virtual emparelhada como um ponto de trânsito para uma rede local. Nesse caso, a rede virtual que está usando um gateway remoto não pode ter seu próprio gateway. Uma rede virtual tem apenas um gateway. O gateway é um gateway local ou remoto na rede virtual emparelhada, conforme mostrado no diagrama a seguir:

![trânsito de emparelhamento de rede virtual](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

O emparelhamento de rede virtual e o emparelhamento de rede virtual global dão suporte ao gateway de trânsito.

Há suporte para o tráfego de gateway entre redes virtuais criadas por meio de diferentes modelos de implantação. O gateway deve estar na rede virtual no modelo do Resource Manager. Para saber mais sobre como usar um gateway para trânsito, consulte [configurar um gateway de VPN para trânsito em um emparelhamento de rede virtual](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Quando você emparelha redes virtuais que compartilham uma única conexão do Azure ExpressRoute, o tráfego entre elas passa pela relação de emparelhamento. Esse tráfego usa a rede de backbone do Azure. Você ainda pode usar gateways locais em cada rede virtual para se conectar ao circuito local. Caso contrário, você pode usar um gateway compartilhado e configurar o trânsito para conectividade local.

## <a name="troubleshoot"></a>Solucionar problemas

Para confirmar se as redes virtuais estão emparelhadas, você pode verificar as rotas efetivas. Verifique as rotas para uma interface de rede em qualquer sub-rede em uma rede virtual. Se um emparelhamento de rede virtual existir, todas as sub-redes na rede virtual terão rotas com o tipo de próximo salto *Emparelhamento VNet*, para cada espaço de endereço em cada rede virtual emparelhada. Para obter mais informações, consulte [diagnosticar um problema de roteamento de máquina virtual](diagnose-network-routing-problem.md).

Você também pode solucionar problemas de conectividade com uma máquina virtual em uma rede virtual emparelhada usando o observador de rede do Azure. Uma verificação de conectividade permite que você veja como o tráfego é roteado do adaptador de rede da máquina virtual de origem para a interface de rede da máquina virtual de destino. Para obter mais informações, consulte [solucionar problemas de conexões com o observador de rede do Azure usando o portal do Azure](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine).

Você também pode tentar [solucionar problemas de emparelhamento de rede virtual](virtual-network-troubleshoot-peering-issues.md).

## <a name="constraints-for-peered-virtual-networks"></a>Restrições para redes virtuais emparelhadas<a name="requirements-and-constraints"></a>

As seguintes restrições se aplicam somente quando as redes virtuais são emparelhadas globalmente:

* Os recursos em uma rede virtual não podem se comunicar com o endereço IP de front-end de um Load Balancer interno básico (ILB) em uma rede virtual emparelhada globalmente.
* Alguns serviços que usam um balanceador de carga básico não funcionam em emparelhamento de rede virtual global. Para obter mais informações, consulte [quais são as restrições relacionadas ao emparelhamento de VNet global e aos balanceadores de carga?](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

Para saber mais, confira [Requisitos e restrições](virtual-network-manage-peering.md#requirements-and-constraints). Para saber mais sobre o número de emparelhamentos com suporte, consulte [limites de rede](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="permissions"></a>Permissões

Para saber mais sobre as permissões necessárias para criar um emparelhamento de rede virtual, consulte [permissões](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Preços

Há um encargo nominal para o tráfego de entrada e saída que usa uma conexão de emparelhamento de rede virtual. Para obter mais informações, consulte [preços de rede virtual](https://azure.microsoft.com/pricing/details/virtual-network).

O tráfego de gateway é uma propriedade de emparelhamento que permite que uma rede virtual utilize um gateway VPN/ExpressRoute em uma rede virtual emparelhada. O trânsito do gateway funciona para a conectividade entre locais e de rede para rede. O tráfego para o gateway (entrada ou saída) na rede virtual emparelhada incorre em encargos de emparelhamento de rede virtual na VNet do spoke (ou VNet não gateway). Para obter mais informações, consulte [preços de gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) para encargos de gateway de VPN e preço de gateway de expressroute para encargos de gateway de

>[!NOTE]
> Uma versão anterior deste documento declarava que os encargos de emparelhamento de rede virtual não se aplicariam à VNet do spoke (ou VNet que não é de gateway) com trânsito de gateway. Agora, ele reflete os preços precisos de acordo com a página de preços.

## <a name="next-steps"></a>Próximas etapas

* Você pode criar um emparelhamento entre duas redes virtuais. As redes podem pertencer à mesma assinatura, a modelos de implantação diferentes na mesma assinatura ou a assinaturas diferentes. Conclua um tutorial para um dos seguintes cenários:

    |Modelo de implantação do Azure             | Assinatura  |
    |---------                          |---------|
    |Ambos Resource Manager              |[Idêntico](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Diferente](create-peering-different-subscriptions.md)|
    |Um Resource Manager, um clássico  |[Idêntico](create-peering-different-deployment-models.md)|
    |                                   |[Diferente](create-peering-different-deployment-models-subscriptions.md)|

* Para saber como criar uma topologia de rede hub e spoke, confira [topologia de rede hub-spoke no Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Para saber mais sobre todas as configurações de emparelhamento de rede virtual, confira [criar, alterar ou excluir um emparelhamento de rede virtual](virtual-network-manage-peering.md).
* Para obter respostas para o emparelhamento de rede virtual comum e as perguntas de emparelhamento de rede virtual global, consulte [emparelhamento VNet](virtual-networks-faq.md#vnet-peering).
