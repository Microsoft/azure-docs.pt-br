---
title: Rede Virtual do Azure | Microsoft Docs
description: Saiba mais sobre os conceitos e recursos da Rede Virtual do Azure.
services: virtual-network
documentationcenter: na
author: anavinahar
tags: azure-resource-manager
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: anavin
ms.openlocfilehash: 22c1e3050915fc697a62862620ef492ef22f80b8
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542827"
---
# <a name="what-is-azure-virtual-network"></a>O que é a Rede Virtual do Azure?

A Rede Virtual do Azure (VNet) é o bloco de construção fundamental de sua rede privada no Azure. Ela permite vários tipos de recursos do Azure, como VMs (Máquinas Virtuais) do Azure, a fim de se comunicar de forma segura com a Internet, com as redes locais e com outras VMs. A VNet é semelhante a uma rede tradicional que você operaria em seu próprio data center, mas traz benefícios adicionais da infraestrutura do Azure, como escala, disponibilidade e isolamento.

## <a name="vnet-concepts"></a>Conceitos de VNet

- **Espaço de endereço:** Ao criar uma VNet, é necessário especificar um espaço de endereço IP privado personalizado usando endereços públicos e privados (RFC 1918). O Azure atribui um endereço IP particular aos recursos em uma rede virtual do espaço de endereço que você atribuiu. Por exemplo, se você implantar uma VM em uma VNet com o espaço de endereço 10.0.0.0/16, a VM receberá um IP privado como 10.0.0.4.
- **Sub-redes:** As sub-redes permitem que você segmente a rede virtual em uma ou mais sub-redes e aloque uma parte do espaço de endereço da rede virtual a cada sub-rede. Em seguida, você pode implantar recursos do Azure em uma sub-rede específica. Assim como em uma rede tradicional, as sub-redes permitem que você divida o espaço de endereço da sua VNet em segmentos que são apropriados para a rede interna da organização. Isso também melhora a eficiência de alocação de endereço. É possível proteger os recursos dentro de sub-redes usando grupos de segurança de rede. Para saber, confira [Grupos de segurança](security-overview.md).
- **Regiões**: Uma VNet tem como escopo uma única região/localização; no entanto, é possível conectar várias redes virtuais de regiões diferentes usando o Emparelhamento de Rede Virtual.
- **Assinatura:** A VNet está no escopo de uma assinatura. É possível implementar várias redes virtuais em cada [assinatura](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e [região](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) do Azure.

## <a name="best-practices"></a>Práticas recomendadas

Ao criar sua rede no Azure, é importante se lembrar dos seguintes princípios de design universais:

- Cuidado para não sobrepor espaços de endereço. Verifique se o espaço de endereço de sua VNet (bloco CIDR) não se sobrepõe aos outros intervalos de rede da sua organização.
- Suas sub-redes não devem abranger todo o espaço de endereço da VNet. Planeje com antecedência e reserve algum espaço de endereço para o futuro.
- Recomendamos que você tenha menos VNets maiores do que várias VNets pequenas. Isso evitará a sobrecarga no gerenciamento.
- Proteja sua VNet usando os NSGs (Grupos de Segurança de Rede).

## <a name="communicate-with-the-internet"></a>Comunicação com a Internet

Por padrão, todos os recursos em uma VNet podem se comunicar na saída com a Internet. Você pode se comunicar na entrada com um recurso, atribuindo um endereço IP público ou um Load Balancer público. Você também pode usar o IP público ou Load Balancer público para gerenciar suas conexões de saída.  Para saber mais sobre as conexões de saída no Azure, confira [Conexões de saída](../load-balancer/load-balancer-outbound-connections.md), [Endereços IP públicos](virtual-network-public-ip-address.md) e [Load Balancer](../load-balancer/load-balancer-overview.md).

>[!NOTE]
>Ao usar somente o [Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md) interno, a conectividade de saída não está disponível até que você defina como quer que as [conexões de saída](../load-balancer/load-balancer-outbound-connections.md) trabalhem com um IP público no nível da instância, ou um Load Balancer público.

## <a name="communicate-between-azure-resources"></a>Comunicação entre recursos do Azure

Os recursos do Azure se comunicam com segurança entre si de uma das seguintes maneiras:

- **Por meio de uma rede virtual**: você pode implantar VMs e vários outros tipos de recursos do Azure em uma rede virtual, tais como Ambientes de Serviço de Aplicativo do Azure, o Serviço de Kubernetes do Azure (AKS) e Conjuntos de Dimensionamento de Máquinas Virtuais do Azure. Para exibir uma lista completa de recursos do Azure que podem ser implantados em uma rede virtual, confira [Integração de serviços de rede virtual](virtual-network-for-azure-services.md).
- **Por meio de um ponto de extremidade de serviço de rede virtual**: estenda seu espaço de endereço privado da rede virtual e a identidade de sua rede virtual para os recursos de serviço do Azure, como contas de Armazenamento do Azure e Bancos de Dados SQL do Azure, em uma conexão direta. Os pontos de extremidade de serviço permitem que você proteja os recursos essenciais do serviço do Azure somente em uma rede virtual. Para obter mais detalhes, confira [Visão geral dos pontos de extremidade de serviço de rede virtual](virtual-network-service-endpoints-overview.md).
- **Por Emparelhamento VNet**: É possível conectar redes virtuais umas às outras, permitindo que os recursos em uma rede virtual se comuniquem com os de qualquer outra por meio de emparelhamento de rede virtual. As redes virtuais que você conecta podem estar na mesma região ou em regiões diferentes do Azure. Para saber mais, confira [Emparelhamento de rede virtual](virtual-network-peering-overview.md).

## <a name="communicate-with-on-premises-resources"></a>Comunicação com os recursos locais

Você pode conectar suas redes e computadores locais a uma rede virtual usando qualquer combinação das seguintes opções:

- **VPN (rede privada virtual) ponto a site:** estabelecida entre uma rede virtual e um único computador em sua rede. Cada computador que deseja estabelecer conectividade com uma rede virtual precisa configurar suas conexões. Esse tipo de conexão é ótimo se você estiver começando a usar o Azure, ou para os desenvolvedores, pois exige pouca ou nenhuma alteração em sua rede existente. A comunicação entre seu computador e uma rede virtual é enviada via Internet, por um túnel criptografado. Para obter mais informações, confira [VPN ponto a site](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#P2S).
- **VPN site a site:** estabelecida entre o dispositivo VPN local e um Gateway de VPN do Azure implantado em uma rede virtual. Esse tipo de conexão permite que qualquer recurso local que você autorizou acesse uma rede virtual. A comunicação entre o dispositivo VPN local e um gateway de VPN do Azure é enviada via Internet, por um túnel criptografado. Para obter mais informações, confira [VPN site a site](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute:** estabelecido entre sua rede e o Azure, por meio de um parceiro de ExpressRoute. Essa conexão é privada. O tráfego não passa pela Internet. Para saber mais, confira [ExpressRoute](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ExpressRoute).

## <a name="filter-network-traffic"></a>Filtrar tráfego de rede

Filtre o tráfego de rede entre sub-redes usando uma ou as duas opções a seguir:

- **Grupos de segurança:** grupos de segurança de rede e grupos de segurança de aplicativo podem conter várias regras de segurança de entrada e saída que permitem filtrar o tráfego para e através de recursos por endereço IP de origem e de destino, porta e protocolo. Para saber mais, confira [Grupos de segurança de rede](security-overview.md#network-security-groups) ou [Grupos de segurança de aplicativo](security-overview.md#application-security-groups).
- **Soluções de virtualização de rede:** uma solução de virtualização de rede é uma VM que executa uma função de rede, como um firewall, otimização WAN ou outra função de rede. Para exibir uma lista de soluções de virtualização de rede disponíveis que você pode implantar em uma rede virtual, confira o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

## <a name="route-network-traffic"></a>Rotear tráfego de rede

Por padrão, o Azure encaminha o tráfego entre sub-redes, redes virtuais conectadas, redes locais e a Internet. Você pode implementar uma ou as duas opções a seguir para substituir as rotas padrão criadas pelo Azure:

- **Tabelas de rotas:** é possível criar tabelas de rotas personalizadas com rotas que controlam para que local o tráfego será roteado para cada sub-rede. Saiba mais sobre [tabelas de rota](virtual-networks-udr-overview.md#user-defined).
- **Rotas BGP (Border Gateway Protocol):** se você conectar sua rede virtual à rede local usando um Gateway de VPN do Azure ou conexão do ExpressRoute, será possível propagar as rotas BGP locais para suas redes virtuais. Saiba mais sobre como usar o BGP com [Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange).

## <a name="azure-vnet-limits"></a>Limites da VNet do Azure

Há certos limites com relação ao número de recursos do Azure que você pode implantar. A maioria dos limites de rede do Azure estão com os valores máximo. No entanto, você pode [aumentar determinados limites de rede](../azure-supportability/networking-quota-requests.md) conforme especificado na [Página de limites da VNet](../azure-subscription-service-limits.md#networking-limits). 

## <a name="pricing"></a>Preços

O uso do Azure VNet não tem qualquer custo, é gratuito. Os recursos como VMs (máquinas virtuais) e outros produtos sofrem cobranças padrão. Para saber mais, confira [Preços de VNet](https://azure.microsoft.com/pricing/details/virtual-network/) e a [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/) do Azure.

## <a name="next-steps"></a>Próximas etapas

 Para começar a usar uma rede virtual, crie uma, implante algumas VMs nela, e se comunique entre as VMs. Para saber como, confira o início rápido [Criar uma rede virtual](quick-create-portal.md).
