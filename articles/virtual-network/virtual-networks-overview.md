---
title: Rede Virtual do Azure
description: Saiba mais sobre os conceitos e recursos da Rede Virtual do Azure, incluindo espaço de endereço, sub-redes, regiões e assinaturas.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: kumud
ms.openlocfilehash: 927ff80a544ef8fbff348aeb8781f8a55c84e109
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061886"
---
# <a name="what-is-azure-virtual-network"></a>O que é a Rede Virtual do Azure?

A Rede Virtual do Azure (VNet) é o bloco de construção fundamental de sua rede privada no Azure. Ela permite vários tipos de recursos do Azure, como VMs (Máquinas Virtuais) do Azure, a fim de se comunicar de forma segura com a Internet, com as redes locais e com outras VMs. A VNet é semelhante a uma rede tradicional que você operaria em seu próprio data center, mas traz benefícios adicionais da infraestrutura do Azure, como escala, disponibilidade e isolamento.

## <a name="why-use-an-azure-virtual-network"></a>Por que usar uma rede virtual do Azure?
A rede virtual do Azure permite que os recursos do Azure se comuniquem com segurança entre si, com a Internet e com redes locais. Os principais cenários do que você pode realizar com uma rede virtual incluem: a comunicação de recursos do Azure com a Internet, a comunicação entre recursos do Azure, a comunicação com recursos locais, a filtragem de tráfego de rede, o roteamento do tráfego de rede e a integração aos serviços do Azure.

### <a name="communicate-with-the-internet"></a>Comunicação com a Internet

Por padrão, todos os recursos em uma VNet podem se comunicar na saída com a Internet. Você pode se comunicar na entrada com um recurso, atribuindo um endereço IP público ou um Load Balancer público. Você também pode usar o IP público ou Load Balancer público para gerenciar suas conexões de saída.  Para saber mais sobre as conexões de saída no Azure, confira [Conexões de saída](../load-balancer/load-balancer-outbound-connections.md), [Endereços IP públicos](virtual-network-public-ip-address.md) e [Load Balancer](../load-balancer/load-balancer-overview.md).

>[!NOTE]
>Ao usar somente o [Standard Load Balancer](../load-balancer/load-balancer-overview.md) interno, a conectividade de saída não está disponível até que você defina como quer que as [conexões de saída](../load-balancer/load-balancer-outbound-connections.md) trabalhem com um IP público no nível da instância, ou um Load Balancer público.

### <a name="communicate-between-azure-resources"></a>Comunicação entre recursos do Azure

Os recursos do Azure se comunicam com segurança entre si de uma das seguintes maneiras:

- **Por meio de uma rede virtual**: você pode implantar VMs e vários outros tipos de recursos do Azure em uma rede virtual, tais como Ambientes de Serviço de Aplicativo do Azure, o Serviço de Kubernetes do Azure (AKS) e Conjuntos de Dimensionamento de Máquinas Virtuais do Azure. Para exibir uma lista completa de recursos do Azure que podem ser implantados em uma rede virtual, confira [Integração de serviços de rede virtual](virtual-network-for-azure-services.md).
- **Por meio de um ponto de extremidade de serviço de rede virtual**: estenda o espaço de endereço privado da rede virtual e a identidade da rede virtual aos recursos de serviços do Azure, como as contas do Armazenamento do Azure e o Banco de Dados SQL do Azure, em uma conexão direta. Os pontos de extremidade de serviço permitem que você proteja os recursos essenciais do serviço do Azure somente em uma rede virtual. Para obter mais detalhes, confira [Visão geral dos pontos de extremidade de serviço de rede virtual](virtual-network-service-endpoints-overview.md).
- **Por Emparelhamento VNet**: É possível conectar redes virtuais umas às outras, permitindo que os recursos em uma rede virtual se comuniquem com os de qualquer outra por meio de emparelhamento de rede virtual. As redes virtuais que você conecta podem estar na mesma região ou em regiões diferentes do Azure. Para saber mais, confira [Emparelhamento de rede virtual](virtual-network-peering-overview.md).

### <a name="communicate-with-on-premises-resources"></a>Comunicação com os recursos locais

Você pode conectar suas redes e computadores locais a uma rede virtual usando qualquer combinação das seguintes opções:

- **VPN (rede privada virtual) ponto a site:** estabelecida entre uma rede virtual e um único computador em sua rede. Cada computador que deseja estabelecer conectividade com uma rede virtual precisa configurar suas conexões. Esse tipo de conexão é ótimo se você estiver começando a usar o Azure, ou para os desenvolvedores, pois exige pouca ou nenhuma alteração em sua rede existente. A comunicação entre seu computador e uma rede virtual é enviada via Internet, por um túnel criptografado. Para obter mais informações, confira [VPN ponto a site](../vpn-gateway/point-to-site-about.md?toc=%2fazure%2fvirtual-network%2ftoc.json#).
- **VPN site a site:** estabelecida entre o dispositivo VPN local e um Gateway de VPN do Azure implantado em uma rede virtual. Esse tipo de conexão permite que qualquer recurso local que você autorizou acesse uma rede virtual. A comunicação entre o dispositivo VPN local e um gateway de VPN do Azure é enviada via Internet, por um túnel criptografado. Para obter mais informações, confira [VPN site a site](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute:** estabelecido entre sua rede e o Azure, por meio de um parceiro de ExpressRoute. Essa conexão é privada. O tráfego não passa pela Internet. Para saber mais, confira [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="filter-network-traffic"></a>Filtrar tráfego de rede

Filtre o tráfego de rede entre sub-redes usando uma ou as duas opções a seguir:

- **Grupos de segurança de rede:** grupos de segurança de rede e grupos de segurança de aplicativo podem conter várias regras de segurança de entrada e saída que permitem filtrar o tráfego para e através de recursos por endereço IP de origem e de destino, porta e protocolo. Para saber mais, confira [Grupos de segurança de rede](./network-security-groups-overview.md#network-security-groups) ou [Grupos de segurança de aplicativo](./network-security-groups-overview.md#application-security-groups).
- **Soluções de virtualização de rede:** uma solução de virtualização de rede é uma VM que executa uma função de rede, como um firewall, otimização WAN ou outra função de rede. Para exibir uma lista de soluções de virtualização de rede disponíveis que você pode implantar em uma rede virtual, confira o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

### <a name="route-network-traffic"></a>Rotear tráfego de rede

Por padrão, o Azure encaminha o tráfego entre sub-redes, redes virtuais conectadas, redes locais e a Internet. Você pode implementar uma ou as duas opções a seguir para substituir as rotas padrão criadas pelo Azure:

- **Tabelas de rotas:** é possível criar tabelas de rotas personalizadas com rotas que controlam para que local o tráfego será roteado para cada sub-rede. Saiba mais sobre [tabelas de rota](virtual-networks-udr-overview.md#user-defined).
- **Rotas BGP (Border Gateway Protocol):** se você conectar sua rede virtual à rede local usando um Gateway de VPN do Azure ou conexão do ExpressRoute, será possível propagar as rotas BGP locais para suas redes virtuais. Saiba mais sobre como usar o BGP com [Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange).

### <a name="virtual-network-integration-for-azure-services"></a>Integração de rede virtual para os serviços do Azure

A integração dos serviços do Azure a uma rede virtual do Azure permite o acesso privado ao serviço de máquinas virtuais ou de recursos de computação na rede virtual.
Você pode integrar serviços do Azure à sua rede virtual com as seguintes opções:
- Implantando [instâncias dedicadas do serviço](virtual-network-for-azure-services.md) em uma rede virtual. Os serviços podem ser acessados de maneira privada dentro da rede virtual e de redes locais.
- Usar [Link Privado](../private-link/private-link-overview.md) para acessar de maneira privada uma instância específica do serviço por meio da sua rede virtual e de redes locais.
- Você também pode acessar o serviço usando pontos de extremidade públicos, estendendo uma rede virtual para o serviço, por meio de [pontos de extremidade de serviço](virtual-network-service-endpoints-overview.md). Pontos de extremidade de serviço permitem que os recursos de serviço da rede virtual sejam protegidos.
 

## <a name="azure-vnet-limits"></a>Limites da VNet do Azure

Há certos limites com relação ao número de recursos do Azure que você pode implantar. A maioria dos limites de rede do Azure estão com os valores máximo. No entanto, você pode [aumentar determinados limites de rede](../azure-portal/supportability/networking-quota-requests.md) conforme especificado na [Página de limites da VNet](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits). 

## <a name="pricing"></a>Preços

O uso do Azure VNet não tem qualquer custo, é gratuito. Os recursos como VMs (máquinas virtuais) e outros produtos sofrem cobranças padrão. Para saber mais, confira [Preços de VNet](https://azure.microsoft.com/pricing/details/virtual-network/) e a [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/) do Azure.

## <a name="next-steps"></a>Próximas etapas
 - Saiba mais sobre os [conceitos e as melhores práticas da Rede Virtual do Azure](concepts-and-best-practices.md).
 - Para começar a usar uma rede virtual, crie uma, implante algumas VMs nela, e se comunique entre as VMs. Para saber como, confira o início rápido [Criar uma rede virtual](quick-create-portal.md).
