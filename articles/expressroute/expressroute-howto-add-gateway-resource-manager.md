---
title: 'Tutorial – Azure ExpressRoute: Adicionar um gateway a uma VNet – Azure PowerShell'
description: Este tutorial ajuda você a adicionar gateway de VNet a uma VNet do Resource Manager já existente do ExpressRoute usando o Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 120bfe2eefae3c1721073060231c6c2a1962b7c8
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106110267"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Tutorial: Configurar um gateway de rede virtual para ExpressRoute usando PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - portal do Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Clássico - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Vídeo – Portal do Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

Este tutorial ensina a adicionar, redimensionar e remover um gateway de VNet (rede virtual) para uma rede virtual já existente. As etapas para essa configuração se aplicam a VNets criadas usando o modelo de implantação do Resource Manager em uma configuração do ExpressRoute. Para obter mais informações, confira [Sobre os gateways de rede virtual para ExpressRoute](expressroute-about-virtual-network-gateways.md).

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> - Criar uma sub-rede de gateway.
> - Criar gateway de rede virtual.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="configuration-reference-list"></a>Lista de referência de configuração

As etapas para essa tarefa usam uma VNet com base nos valores na lista de referência de configuração a seguir. Nomes e configurações adicionais também são descritos nesta lista. Não usamos essa lista diretamente em nenhuma uma das etapas, embora adicionemos variáveis com base nos valores contidos nessa lista. É possível fazer uma cópia da lista para usá-la como referência, substituindo os valores pelos seus próprios.

| Configuração                   | Valor                                              |
| ---                       | ---                                                |
| Nome da VNET | *TestVNet* |    
| Espaço de endereço da rede virtual | *192.168.0.0/16* |
| Grupo de recursos | *TestRG* |
| Nome da sub-rede 1 | *FrontEnd* |   
| Espaço de endereço da sub-rede 1 | *192.168.1.0/24* |
| Nome da sub-rede 1 | *FrontEnd* |
| Nome da sub-rede do gateway | *GatewaySubnet* |    
| Espaço de endereço da sub-rede do gateway | *192.168.200.0/26* |
| Região | *Leste dos EUA* |
| Nome do Gateway | *GW* |   
| Nome do IP do gateway | *GWIP* |
| Nome de configuração de IP do gateway | *gwipconf* |
| Type | *ExpressRoute* |
| Nome do IP público do gateway  | *gwpip* |

> [!IMPORTANT]
> A compatibilidade com IPv6 para emparelhamento privado está atualmente em **Versão Prévia Pública**. Se você quiser conectar sua rede virtual a um circuito do ExpressRoute com o emparelhamento privado configurado baseado em IPv6, verifique se a rede virtual é de pilha dupla e se segue as diretrizes descritas [aqui](../virtual-network/ipv6-overview.md).
> 
> 

## <a name="add-a-gateway"></a>Adicionar um gateway

1. Para se conectar com o Azure, execute `Connect-AzAccount`.

1. Declare as variáveis para este exercício. Lembre-se de editar o exemplo para que elas reflitam as configurações que deseja usar.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
1. Armazene o objeto de rede virtual como uma variável.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
1. Adicione uma sub-rede de gateway à sua Rede Virtual. A sub-rede de gateway deve ser nomeada “GatewaySubnet”. A sub-rede do gateway precisa ser /27 ou maior (/26, /25 e assim por diante). Se você planeja conectar 16 circuitos do ExpressRoute ao seu gateway, você **precisa** criar uma sub-rede de gateway de /26 ou maior.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
    Se você estiver usando uma rede virtual de pilha dupla e planeja usar o emparelhamento privado baseado em IPv6 por meio do ExpressRoute, crie uma sub-rede de gateway de pilha dupla em vez disso.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/26","ace:daa:daaa:deaa::/64"
   ```
1. Defina a configuração.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
1. Armazene a sub-rede de gateway como uma variável.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
1. Solicite um endereço IP público. O endereço IP é solicitado antes da criação do gateway. Não é possível especificar o endereço IP que você deseja usar; ele é atribuído dinamicamente. Você usará esse endereço IP na próxima seção de configuração. O AllocationMethod deve ser Dynamic.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
1. Crie a configuração de seu gateway. A configuração do gateway define a sub-rede e o endereço IP público a serem usados. Nesta etapa, você especificará a configuração que será usada quando o gateway for criado. Use o exemplo a seguir para criar a configuração do gateway.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
1. Crie o gateway. Nesta etapa, o **-GatewayType** é especialmente importante. É necessário usar o valor **ExpressRoute**. Observe que, depois de executar esses cmdlets, o gateway pode levar 45 minutos ou mais para ser criado.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```
> [!IMPORTANT]
> Se você planeja usar o emparelhamento privado baseado em IPv6 por meio do ExpressRoute, não se esqueça de selecionar um SKU do AZ (ErGw1AZ, ErGw2AZ, ErGw3AZ) para **-GatewaySku**.
> 
> 

## <a name="verify-the-gateway-was-created"></a>Verificar se o gateway foi criado
Use os comandos a seguir para verificar se o gateway foi criado:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Redimensionar um gateway
Há uma série de [SKUs de Gateway](expressroute-about-virtual-network-gateways.md). Você pode usar o comando a seguir para alterar a SKU de gateway a qualquer momento.

> [!IMPORTANT]
> Esse comando não funciona para o gateway UltraPerformance. Para alterar o gateway para um gateway UltraPerformance, primeiro remova o gateway do ExpressRoute existente e crie um novo gateway UltraPerformance. Para fazer downgrade do gateway de um gateway UltraPerformance, primeiro remova o gateway UltraPerformance e crie um novo gateway.
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="clean-up-resources"></a>Limpar os recursos
Use o seguinte comando para remover o gateway:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```

## <a name="next-steps"></a>Próximas etapas
Depois de criar o gateway de VNet, é possível vincular sua VNet a um circuito do ExpressRoute. 

> [!div class="nextstepaction"]
> [Vincular uma rede virtual a um circuito de ExpressRoute](expressroute-howto-linkvnet-arm.md)
