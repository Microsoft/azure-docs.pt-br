---
title: Conexões VPN site a site por meio do emparelhamento privado do ExpressRoute
description: Este artigo ajuda você a habilitar a VPN site a site por meio do emparelhamento privado do ExpressRoute para criptografar o tráfego.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/16/2020
ms.author: cherylmc
ms.openlocfilehash: 01d87bcb5697326fa87b25b20354897049900d9d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880518"
---
# <a name="configure-a-site-to-site-vpn-connection-over-expressroute-private-peering"></a>Configurar uma conexão VPN site a site por meio do emparelhamento privado do ExpressRoute

Você pode configurar uma VPN site a site para um gateway de rede virtual em um emparelhamento privado do ExpressRoute usando um endereço IP RFC 1918. Essa configuração fornece os seguintes benefícios:

* O tráfego sobre o emparelhamento privado é criptografado.

* Os usuários ponto a site que se conectam a um gateway de rede virtual podem usar o ExpressRoute (por meio do túnel site a site) para acessar recursos locais.

>[!NOTE]
>Esse recurso só tem suporte em gateways com redundância de zona. Por exemplo, VpnGw1AZ, VpnGw2AZ, etc.
>

Para concluir essa configuração, verifique se você atende aos seguintes pré-requisitos:

* Você tem um circuito de ExpressRoute em funcionamento que está vinculado à VNet onde o gateway de VPN é (ou será) criado.

* Você pode acessar recursos em um IP RFC1918 (privado) na VNet no circuito do ExpressRoute.

## <a name="routing"></a><a name="routing"></a>Roteamento

A **Figura 1** mostra um exemplo de conectividade de VPN em relação ao emparelhamento privado do ExpressRoute. Neste exemplo, você vê uma rede dentro da rede local que está conectada ao gateway de VPN do Hub do Azure por meio do emparelhamento privado do ExpressRoute. Um aspecto importante dessa configuração é o roteamento entre as redes locais e o Azure por meio dos caminhos de ExpressRoute e VPN.

A figura 1

:::image type="content" source="media/site-to-site-vpn-private-peering/routing.png" alt-text="Figura 1":::

Estabelecer a conectividade é simples:

1. Estabeleça a conectividade do ExpressRoute com um circuito de ExpressRoute e emparelhamento privado.

1. Estabeleça a conectividade VPN usando as etapas neste artigo.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Tráfego de redes locais para o Azure

Para o tráfego de redes locais para o Azure, os prefixos do Azure são anunciados por meio do BGP de emparelhamento privado do ExpressRoute e do BGP VPN. O resultado são duas rotas de rede (caminhos) para o Azure a partir das redes locais:

• Uma rota de rede sobre o caminho protegido por IPsec.

• Uma rota de rede diretamente no ExpressRoute sem proteção de IPsec.

Para aplicar a criptografia à comunicação, você deve certificar-se de que, para a rede conectada VPN na **Figura 1**, as rotas do Azure por meio do gateway de VPN local são preferenciais sobre o caminho direto do ExpressRoute.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Tráfego do Azure para redes locais

O mesmo requisito se aplica ao tráfego do Azure para redes locais. Para garantir que o caminho IPsec seja preferencial em relação ao caminho direto do ExpressRoute (sem IPsec), você tem duas opções:

• **Anuncie prefixos mais específicos na sessão BGP VPN para a rede conectada VPN**. Você pode anunciar um intervalo maior que abrange a rede conectada VPN por meio do emparelhamento privado do ExpressRoute e intervalos mais específicos na sessão BGP VPN. Por exemplo, Anunci 10.0.0.0/16 por meio do ExpressRoute e 10.0.1.0/24 pela VPN.

• **Anuncie prefixos não contíguos para VPN e ExpressRoute**. Se os intervalos de rede conectados à VPN forem separados de outras redes conectadas do ExpressRoute, você poderá anunciar os prefixos nas sessões VPN e BGP do ExpressRoute, respectivamente. Por exemplo, Anunci 10.0.0.0/24 por meio do ExpressRoute e 10.0.1.0/24 pela VPN.

Em ambos os exemplos, o Azure enviará tráfego para 10.0.1.0/24 pela conexão VPN em vez de diretamente pelo ExpressRoute sem proteção de VPN.

>[!Warning]
>Se você anunciar os mesmos prefixos em conexões de ExpressRoute e VPN, >o Azure usará o caminho do ExpressRoute diretamente sem a proteção de VPN.
>

## <a name="portal-steps"></a><a name="portal"></a>Etapas do portal

1. Configure uma conexão site a site. Para obter as etapas, consulte o artigo [configuração site a site](./tutorial-site-to-site-portal.md) . Certifique-se de escolher um SKU de gateway com redundância de zona para o gateway. 

   Os SKUs com redundância de zona têm "AZ" no final da SKU. Por exemplo, **VpnGw1AZ**. Gateways com redundância de zona só estão disponíveis em regiões em que o serviço de zona de disponibilidade está disponível. Para obter informações sobre as regiões nas quais damos suporte a zonas de disponibilidade, consulte [regiões que dão suporte a zonas de disponibilidade](../availability-zones/az-region.md).

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway.png" alt-text="IPs privados do gateway":::
1. Habilite os IPs privados no gateway. Selecione **configuração** e defina **IPS privados de gateway** como **habilitado**. Selecione **Salvar** para salvar as alterações.
1. Na página **visão geral** , selecione **Ver mais** para exibir o endereço IP privado. Anote essas informações para uso posterior nas etapas de configuração.

   :::image type="content" source="media/site-to-site-vpn-private-peering/gateway-overview.png" alt-text="Página de visão geral" lightbox="media/site-to-site-vpn-private-peering/gateway-overview.png":::
1. Para habilitar o **uso do endereço IP privado do Azure** na conexão, selecione  **configuração**. Defina **usar o endereço IP privado do Azure** como **habilitado** e, em seguida, selecione **salvar**.

   :::image type="content" source="media/site-to-site-vpn-private-peering/connection.png" alt-text="Habilitado para IPs privados de gateway":::
1. No firewall, execute ping no IP privado que você anotou na etapa 3. O IP privado deve ser acessível pelo emparelhamento privado do ExpressRoute.
1. Use esse IP privado como o IP remoto no firewall local para estabelecer o túnel site a site por meio do emparelhamento privado do ExpressRoute.

## <a name="powershell-steps"></a><a name="powershell"></a>etapas do PowerShell

1. Configure uma conexão site a site. Para obter as etapas, consulte o artigo [Configurar uma VPN site a site](./tutorial-site-to-site-portal.md) . Certifique-se de escolher um SKU de gateway com redundância de zona para o gateway. Os SKUs com redundância de zona têm "AZ" no final da SKU. Por exemplo, VpnGw1AZ.
1. Defina o sinalizador para usar o IP privado no gateway usando os seguintes comandos do PowerShell:

   ```azurepowershell-interactive
   $Gateway = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroup <name of resource group>

   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -EnablePrivateIpAddress $true
   ```

   Você deve ver um endereço IP público e privado. Anote o endereço IP na seção "TunnelIpAddresses" da saída. Você usará essas informações em uma etapa posterior.
1. Defina a conexão para usar o endereço IP privado usando o seguinte comando do PowerShell:

   ```azurepowershell-interactive
   $Connection = get-AzVirtualNetworkGatewayConnection -Name <name of the connection> -ResourceGroupName <name of resource group>

   Set-AzVirtualNetworkGatewayConnection --VirtualNetworkGatewayConnection $Connection -UseLocalAzureIpAddress $true
   ```
1. No firewall, execute ping no IP privado que você anotou na etapa 2. Ele deve ser acessível pelo emparelhamento privado do ExpressRoute.
1. Use esse IP privado como o IP remoto no firewall local para estabelecer o túnel site a site por meio do emparelhamento privado do ExpressRoute.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o gateway de VPN, consulte [o que é o gateway de VPN?](vpn-gateway-about-vpngateways.md)