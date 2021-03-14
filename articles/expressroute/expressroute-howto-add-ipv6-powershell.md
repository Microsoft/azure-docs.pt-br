---
title: 'Azure ExpressRoute: adicionar suporte a IPv6 usando o Azure PowerShell'
description: Saiba como adicionar suporte a IPv6 para se conectar a implantações do Azure usando o Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 253fce7d47d694c03d470fefdf81318a6bff77b3
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123047"
---
# <a name="add-ipv6-support-for-private-peering-using-azure-powershell-preview"></a>Adicionar suporte a IPv6 para emparelhamento privado usando Azure PowerShell (versão prévia)

Este artigo descreve como adicionar suporte a IPv6 para se conectar via ExpressRoute a seus recursos no Azure usando o Azure PowerShell.

> [!Note]
> Este recurso está disponível atualmente para visualização em [regiões do Azure com zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-region#azure-regions-with-availability-zones). O circuito do ExpressRoute, portanto, pode ser criado usando qualquer local de emparelhamento, mas as implantações baseadas em IPv6 que se conectam devem estar em uma região com Zonas de Disponibilidade.

## <a name="working-with-azure-powershell"></a>Trabalhando com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="register-for-public-preview"></a>Registrar-se para visualização pública
Antes de adicionar o suporte a IPv6, você deve primeiro registrar sua assinatura. Para se registrar, faça o seguinte por meio de Azure PowerShell:
1.  Entre no Azure e selecione a assinatura. Você deve fazer isso para a assinatura que contém o circuito do ExpressRoute, bem como a assinatura que contém suas implantações do Azure (se forem diferentes).

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. Solicitação para registrar sua assinatura para visualização pública usando o seguinte comando:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowIpv6PrivatePeering -ProviderNamespace Microsoft.Network
    ```

Sua solicitação será então aprovada pela equipe do ExpressRoute dentro de 2-3 dias úteis.

## <a name="add-ipv6-private-peering-to-your-expressroute-circuit"></a>Adicionar emparelhamento privado IPv6 ao circuito do ExpressRoute

1. [Crie um circuito do ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-arm) ou use um circuito existente. Recupere o circuito executando o comando **Get-AzExpressRouteCircuit** :

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Recupere a configuração de emparelhamento privado para o circuito executando **Get-AzExpressRouteCircuitPeeringConfig**:

    ```azurepowershell-interactive
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    ```

3. Adicione um emparelhamento privado IPv6 à sua configuração de emparelhamento privado IPv4 existente. Forneça um par de/126 sub-redes IPv6 que você possui para o link principal e links secundários. De cada uma dessas sub-redes, você atribuirá o primeiro endereço IP utilizável ao seu roteador, já que a Microsoft usa o segundo IP utilizável para o roteador dela.

    > [!Note]
    > O peer ASN e o Vlanid devem corresponder aos da sua configuração de emparelhamento privado IPv4.

    ```azurepowershell-interactive
    Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

    Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
    ```

4. Depois que a configuração tiver sido salva com êxito, obtenha o circuito novamente executando o comando **Get-AzExpressRouteCircuit** . A resposta deve ser semelhante ao exemplo a seguir:

    ```azurepowershell
    Name                             : ExpressRouteARMCircuit
    ResourceGroupName                : ExpressRouteResourceGroup
    Location                         : eastus
    Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                         "Name": "Standard_MeteredData",
                                         "Tier": "Standard",
                                         "Family": "MeteredData"
                                       }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Washington DC",
                                         "BandwidthInMbps": 50
                                       }
    ExpressRoutePort                 : null
    BandwidthInGbps                  :
    Stag                             : 29
    ServiceKey                       : **************************************
    Peerings                         : [
                                         {
                                           "Name": "AzurePrivatePeering",
                                           "Etag": "W/\"facc8972-995c-4861-a18d-9a82aaa7167e\"",
                                           "Id": "/subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                           "PeeringType": "AzurePrivatePeering",
                                           "State": "Enabled",
                                           "AzureASN": 12076,
                                           "PeerASN": 100,
                                           "PrimaryPeerAddressPrefix": "192.168.15.16/30",
                                           "SecondaryPeerAddressPrefix": "192.168.15.20/30",
                                           "PrimaryAzurePort": "",
                                           "SecondaryAzurePort": "",
                                           "VlanId": 200,
                                           "ProvisioningState": "Succeeded",
                                           "GatewayManagerEtag": "",
                                           "LastModifiedBy": "Customer",
                                           "Ipv6PeeringConfig": {
                                             "State": "Enabled",
                                             "PrimaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::/126",
                                             "SecondaryPeerAddressPrefix": "3FFE:FFFF:0:CD30::4/126"
                                           },
                                           "Connections": [],
                                           "PeeredConnections": []
                                         },
                                       ]
    Authorizations                   : []
    AllowClassicOperations           : False
    GatewayManagerEtag               :
    ```

## <a name="update-your-connection-to-an-existing-virtual-network"></a>Atualizar sua conexão com uma rede virtual existente

Siga as etapas abaixo se você tiver um ambiente existente de recursos do Azure em uma região com Zonas de Disponibilidade para o qual você gostaria de usar o emparelhamento privado IPv6 com.

1. Recupere a rede virtual à qual seu circuito do ExpressRoute está conectado.

    ```azurepowershell-interactive
    $vnet = Get-AzVirtualNetwork -Name "VirtualNetwork" -ResourceGroupName "ExpressRouteResourceGroup"
    ```

2. Adicione um espaço de endereço IPv6 à sua rede virtual.

    ```azurepowershell-interactive
    $vnet.AddressSpace.AddressPrefixes.add("ace:daa:daaa:deaa::/64")
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

3. Adicione espaço de endereço IPv6 à sua sub-rede de gateway. A sub-rede IPv6 do gateway deve ser/64 ou maior.

    ```azurepowershell-interactive
    Set-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26", "ace:daa:daaa:deaa::/64"
    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```

4. Se você tiver um gateway com redundância de zona existente, execute o seguinte para habilitar a conectividade IPv6. Caso contrário, [crie o gateway de rede virtual](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager) usando um SKU com redundância de zona (ErGw1AZ, ErGw2AZ, ErGw3AZ).

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name "GatewayName" -ResourceGroupName "ExpressRouteResourceGroup"
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
    ```

## <a name="create-a-connection-to-a-new-virtual-network"></a>Criar uma conexão com uma nova rede virtual

Siga as etapas abaixo se você planeja se conectar a um novo conjunto de recursos do Azure em uma região com Zonas de Disponibilidade usando o emparelhamento privado IPv6.

1. Crie uma rede virtual de pilha dupla com espaço de endereço IPv4 e IPv6. Para obter mais informações, consulte [criar uma rede virtual](https://docs.microsoft.com/azure/virtual-network/quick-create-portal#create-a-virtual-network).

2. [Crie a sub-rede de gateway de pilha dupla](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager#add-a-gateway).

3. [Crie o gateway de rede virtual](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-resource-manager#add-a-gateway) usando um SKU com redundância de zona (ErGw1AZ, ErGw2AZ, ErGw3AZ). Se você planeja usar o FastPath, use ErGw3AZ (Observe que isso só está disponível para circuitos que usam o ExpressRoute Direct).

4. [Vincule sua rede virtual ao circuito do ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm).

## <a name="limitations"></a>Limitações
Embora o suporte a IPv6 esteja disponível para conexões com implantações em regiões com Zonas de Disponibilidade, ele não oferece suporte aos seguintes casos de uso:

* Conexões com implantações no Azure por meio de um SKU de gateway de ExpressRoute não AZ
* Conexões com implantações em regiões não AZ
* Conexões Alcance Global entre circuitos do ExpressRoute
* Uso do ExpressRoute com WAN virtual
* FastPath com circuitos diretos não ExpressRoute
* Coexistência com o gateway de VPN

## <a name="next-steps"></a>Próximas etapas

Para solucionar problemas do ExpressRoute, consulte os seguintes artigos:

* [Verificando a conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Solucionando problemas de desempenho de rede](expressroute-troubleshooting-network-performance.md)
