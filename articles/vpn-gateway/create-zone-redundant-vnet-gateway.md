---
title: Criar um gateway de rede virtual com redundância de zona em Zonas de Disponibilidade do Azure
description: Saiba como implantar gateways de VPN com redundância de zona e gateways de ExpressRoute no Zonas de Disponibilidade do Azure.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 2eaf1470e2d861ecfc1c1bc96f6040a1c3e0a644
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89425221"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Criar um gateway de rede virtual com redundância de zona em Zonas de Disponibilidade do Azure

Você pode implantar gateways VPN e ExpressRoute nas zonas de disponibilidade do Azure. Isso traz resiliência, escalabilidade e maior disponibilidade para os gateways de rede virtual. A implantação de gateways em Zonas de Disponibilidade do Azure separa de forma física e lógica os gateways em uma região, enquanto protege a conectividade de rede local com o Azure contra falhas no nível da zona. Para obter informações, veja [Sobre gateways de rede virtual com redundância de zona](about-zone-redundant-vnet-gateways.md) e [Sobre Zonas de Disponibilidade do Azure](../availability-zones/az-overview.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-declare-your-variables"></a><a name="variables"></a>1. declare suas variáveis

Declare as variáveis que você quer usar. Use o exemplo a seguir, substituindo os valores existentes pelos seus quando necessário. Se você fechar sua sessão do PowerShell/Cloud Shell a qualquer momento durante o exercício, basta copiar e colar os valores novamente para redeclarar as variáveis. Ao especificar a localização, verifique se a região especificada é compatível. Consulte mais informações em [Perguntas Frequentes](#faq).

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="2-create-the-virtual-network"></a><a name="configure"></a>2. criar a rede virtual

Crie um grupos de recursos.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

Crie uma rede virtual.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="3-add-the-gateway-subnet"></a><a name="gwsub"></a>3. adicionar a sub-rede de gateway

A sub-rede de gateway contém os endereços IP reservados que usam os serviços de gateway de rede virtual. Use os exemplos a seguir para adicionar e configurar uma sub-rede de gateway:

Adicione a sub-rede de gateway.

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Defina a configuração de sub-rede do gateway para a rede virtual.

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="4-request-a-public-ip-address"></a><a name="publicip"></a>4. solicitar um endereço IP público
 
Nesta etapa, escolha as instruções que se aplicam ao gateway que você deseja criar. A seleção de zonas para implantar os gateways depende das zonas especificadas para o endereço IP público.

### <a name="for-zone-redundant-gateways"></a><a name="ipzoneredundant"></a>Para gateways com redundância de zona

Solicite um endereço IP público com a SKU PublicIpaddress **Standard** e não especifique nenhuma zona. Nesse caso, o endereço IP público Standard criado será um IP público com redundância de zona.   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="for-zonal-gateways"></a><a name="ipzonalgw"></a>Para gateways em zona

Solicite um endereço IP público com a SKU PublicIpaddress **Standard**. Especifique a zona (1, 2 ou 3). Todas as instâncias de gateway serão implantadas nesta zona.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="for-regional-gateways"></a><a name="ipregionalgw"></a>Para gateways regionais

Solicite um endereço IP público com a SKU PublicIpaddress **Básica**. Nesse caso, o gateway é implantado como um gateway regional e não tem nenhuma redundância de zona integrada no gateway. As instâncias de gateway são criadas em quaisquer as zonas, respectivamente.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="5-create-the-ip-configuration"></a><a name="gwipconfig"></a>5. criar a configuração de IP

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="6-create-the-gateway"></a><a name="gwconfig"></a>6. criar o gateway

Crie o gateway de rede virtual.

### <a name="for-expressroute"></a>Para ExpressRoute

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute -GatewaySku ErGw1AZ
```

### <a name="for-vpn-gateway"></a>Para Gateway de VPN

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1AZ
```

## <a name="faq"></a><a name="faq"></a>Perguntas frequentes

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>O que mudará quando eu implantar essas novas SKUs?

Da sua perspectiva, você poderá implantar seus gateways com redundância de zona. Isso significa que todas as instâncias dos gateways serão implantadas em Zonas de Disponibilidade do Azure e cada Zona de Disponibilidade será um domínio de atualização e falha diferente. Isso torna seus gateways mais confiáveis, disponíveis e resilientes a falhas de zona.

### <a name="can-i-use-the-azure-portal"></a>Posso usar o portal do Azure?

Sim, você pode usar o portal do Azure para implantar as novas SKUs. No entanto, você verá essas novas SKUs somente em regiões do Azure com Zonas de Disponibilidade do Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Quais regiões estão disponíveis para eu usar as novas SKUs?

Consulte [zonas de disponibilidade](../availability-zones/az-region.md) para obter a lista mais recente de regiões disponíveis.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Posso alterar/migrar/atualizar meus gateways de rede virtual existentes para gateways com redundância de zona ou em zona?

No momento, a migração de gateways de rede virtual existentes para gateways com redundância de zona ou em zona não é compatível. No entanto, você pode excluir o gateway existente e recriar um gateway com redundância de zona ou em zona.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Posso implantar gateways de VPN e ExpressRoute na mesma rede virtual?

A coexistência de gateways de VPN e do ExpressRoute na mesma rede virtual é compatível. No entanto, reserve um intervalo de endereços IP /27 para a sub-rede de gateway.
