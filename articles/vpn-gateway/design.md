---
title: Sobre o design do gateway de VPN do Azure
description: Saiba mais sobre as maneiras como você pode criar uma topologia de gateway de VPN para se conectar às redes virtuais do Azure.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 61732f66aef58f5a9edcb9e095782e19e8aaffdd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91397208"
---
# <a name="vpn-gateway-design"></a>Design de gateway de VPN

É importante saber que há diferentes configurações disponíveis para conexões de gateway de VPN. Você precisa determinar qual configuração melhor atende às suas necessidades. Nas seções a seguir, você pode exibir as informações de design e os diagramas de topologia sobre as seguintes conexões de gateway de VPN. Use os diagramas e as descrições para ajudar a selecionar a topologia de conexão adequada a seus requisitos. Os diagramas mostram as principais topologias de linha de base, mas é possível criar configurações mais complexas usando os diagramas como diretrizes.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a><a name="s2smulti"></a>Site a Site e Multissite (túnel VPN IPsec/IKE)

### <a name="site-to-site"></a><a name="S2S"></a>Site a site

Uma conexão de gateway VPN Site a Site (S2S) é uma conexão por túnel VPN IPsec/IKE (IKEv1 ou IKEv2). As conexões S2S podem ser usadas para configurações entre instalações e híbridas. Uma conexão site a site exige um dispositivo VPN local com um endereço IP público atribuído a ele. Para saber mais sobre como selecionar um dispositivo VPN, confira as [Perguntas Frequentes de Gateway de VPN – dispositivos VPN](vpn-gateway-vpn-faq.md#s2s).

![Exemplo de conexão Site a Site do Gateway de VPN do Azure](./media/design/vpngateway-site-to-site-connection-diagram.png)

O gateway de VPN pode ser configurado no modo de espera ativo usando um IP público ou em modo ativo-ativo usando dois IPs públicos. No modo de espera ativo, um túnel IPsec está ativo e o outro túnel está em espera. Nessa configuração, o tráfego flui pelo túnel ativo e, se ocorrer algum problema com esse túnel, o tráfego mudará para o túnel em espera. A configuração do gateway de VPN no modo ativo-ativo é *recomendável* no qual ambos os túneis IPsec estão ativos simultaneamente, com dados fluindo por ambos os túneis ao mesmo tempo. Uma vantagem adicional do modo ativo-ativo é que os clientes enfrentam mais taxas de transferência.

### <a name="multi-site"></a><a name="Multi"></a>Multissite

Esse tipo de conexão é uma variação da conexão Site a Site. Você pode criar mais de uma conexão de VPN em seu gateway de rede virtual, normalmente se conectando a vários sites locais. Ao trabalhar com várias conexões, você deve usar um tipo de VPN baseado em rota (conhecido como gateway dinâmico ao trabalhar com redes virtuais clássicas). Como cada rede virtual pode ter apenas um gateway de VPN, todas as conexões por meio do gateway compartilham a largura de banda disponível. Esse tipo de conexão é frequentemente chamado de conexão de "vários sites".

![Exemplo de conexão Multissite do Gateway de VPN do Azure](./media/design/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Modelos de implantação e métodos para Site a Site e Vários Sites

[!INCLUDE [site-to-site and multi-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="point-to-site-vpn"></a><a name="P2S"></a>VPN ponto a site

Uma conexão de gateway de VPN Ponto a Site (P2S) permite que você crie uma conexão segura para sua rede virtual a partir de um computador cliente individual. Uma conexão P2S é estabelecida iniciando-a do computador cliente. Essa solução é útil para pessoas que trabalham remotamente que querem se conectar às VNets do Azure de um local remoto, como de casa ou de uma conferência. A VPN P2S também é uma solução útil para usar em vez de uma VPN S2S, quando você tiver apenas alguns clientes que precisam se conectar a uma rede virtual.

Ao contrário das conexões S2S, as conexões P2S não exigem um endereço IP público local ou um dispositivo VPN. As conexões P2S podem ser usadas com as conexões S2S pelo mesmo gateway de VPN, desde que todos os requisitos de configuração de ambas as conexões sejam compatíveis. Para saber mais sobre conexões Ponto a Site, confira [Sobre VPN Ponto a Site](point-to-site-about.md).

![Exemplo de conexão Ponto de Site do Gateway de VPN do Azure](./media/design/point-to-site.png)

### <a name="deployment-models-and-methods-for-p2s"></a>Modelos e métodos de implantação para P2S

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="vnet-to-vnet-connections-ipsecike-vpn-tunnel"></a><a name="V2V"></a>Conexões de VNet para VNet (túnel VPN IPsec/IKE)

Conectar uma rede virtual a outra rede virtual é semelhante a conectar uma rede virtual (Rede Virtual para Rede Virtual) a um site local. Os dois tipos de conectividade usam um gateway de VPN para fornecer um túnel seguro usando IPsec/IKE. Você pode até combinar a comunicação VNet a VNet com as configurações de conexão de vários sites. Isso permite estabelecer topologias de rede que combinam conectividade entre instalações a conectividade de rede intervirtual.

As redes virtuais às quais você se conecta podem estar:

* na mesma região ou em regiões diferentes
* na mesma assinatura ou em assinaturas diferentes 
* nos mesmos modelos de implantação ou em modelos diferentes

![Exemplo de conexão VNet a VNet do Gateway de VPN do Azure](./media/design/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>Conexões entre os modelos de implantação

Atualmente, o Azure tem dois modelos de implantação: o clássico e o Resource Manager. Se você já usa o Azure há algum tempo, provavelmente terá as VMs do Azure e as funções de instância em execução em uma Rede Virtual clássica. Suas VMs e instâncias de função mais recentes podem estar em execução em uma Rede Virtual criada no Resource Manager. Você pode criar uma conexão entre as Redes Virtuais para permitir que os recursos em uma rede virtual se comuniquem diretamente com os recursos em outra.

### <a name="vnet-peering"></a>Emparelhamento VNet

Talvez você possa usar o emparelhamento VNet para criar sua conexão, desde que a rede virtual atenda a certos requisitos. O emparelhamento de Rede Virtual não usa um gateway de rede virtual. Para obter mais informações, consulte [Emparelhamento da VNet](../virtual-network/virtual-network-peering-overview.md).

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Modelos de implantação e métodos para VNet a VNet

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="expressroute-private-connection"></a><a name="ExpressRoute"></a>ExpressRoute (conexão privada)

O ExpressRoute permite que você estenda as redes locais para a nuvem da Microsoft em uma conexão privada facilitada por um provedor de conectividade. Com o ExpressRoute, você pode estabelecer conexões com os serviços de nuvem da Microsoft, como Microsoft Azure, Microsoft 365 e CRM Online. A conectividade pode ocorrer de uma rede any-to-any (VPN de IP), uma rede Ethernet ponto a ponto ou uma conexão cruzada virtual por meio de um provedor de conectividade em uma colocação.

As conexões de ExpressRoute não passam pela Internet pública. Isso permite que as conexões de ExpressRoute ofereçam mais confiabilidade, mais velocidade, latências menores e muito mais segurança do que as conexões típicas pela Internet.

Uma conexão ExpressRoute usa um gateway de rede virtual como parte da configuração necessária. Em uma conexão ExpressRoute, um gateway de rede virtual é configurado com o tipo de gateway 'ExpressRoute', em vez de 'Vpn'. Embora o tráfego que passa por um circuito do ExpressRoute não seja criptografado por padrão, é possível criar uma solução que permite enviar tráfego criptografado em um circuito ExpressRoute. Para obter mais informações sobre o ExpressRoute, consulte a [Visão geral técnica do ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="site-to-site-and-expressroute-coexisting-connections"></a><a name="coexisting"></a>Conexões coexistentes Site a Site e de ExpressRoute

O ExpressRoute é uma conexão direta e privada da sua WAN (não pela Internet pública) para Serviços Microsoft, incluindo o Azure. O tráfego da VPN Site a Site é criptografado ao percorer a Internet pública. Poder configurar conexões VPN Site a Site e o ExpressRoute para a mesma rede virtual oferece várias vantagens.

Você pode configurar uma VPN Site a Site como um caminho de failover seguro para o ExpressRoute ou usar VPNs Site a Site para se conectar a sites que não fazem parte de sua rede, mas estão conectados por meio do ExpressRoute. Observe que essa configuração exige dois gateways de rede virtual para a mesma rede virtual, um usando o tipo de gateway 'Vpn' e o outro usando o tipo de gateway 'ExpressRoute'.

![Exemplos de conexões coexistentes do ExpressRoute e do Gateway de VPN](./media/design/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute-coexist"></a>Coexistência de modelos e métodos de implantação para S2S e ExpressRoute

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="highly-available-connections"></a><a name="highly-available"></a>Conexões altamente disponíveis

Para o planejamento e o design de conexões altamente disponíveis, consulte [conexões altamente disponíveis](vpn-gateway-highlyavailable.md).

## <a name="next-steps"></a>Próximas etapas

* Exiba as [Perguntas frequentes sobre o Gateway de VPN](vpn-gateway-vpn-faq.md) para saber mais.

* Saiba mais sobre [as definições de configuração do gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md).

* Para obter considerações sobre BGP de gateway de VPN, consulte [sobre o BGP](vpn-gateway-bgp-overview.md).

* Exiba os [Limites de serviço e assinatura](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

* Saiba mais sobre alguns dos outros principais [recursos de rede](../networking/networking-overview.md) do Azure.
