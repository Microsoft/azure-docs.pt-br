---
title: Criar e gerenciar o gateway de VPN do Microsoft Azure usando o PowerShell | Microsoft Docs
description: Tutorial - Criar e gerenciar o gateway de VPN com o módulo Microsoft Azure PowerShell
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: cherylmc
ms.custom: mvc
ms.openlocfilehash: d1c90e61890ee98dc5371faed872d03409aaf31f
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489556"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-powershell"></a>Tutorial: Criar e gerenciar um gateway de VPN usando o PowerShell

As conexões VPN do Microsoft Azure fornecem conectividade segura entre os locais do cliente e o Microsoft Azure. Este tutorial aborda itens básicos da implantação de gateway VPN do Azure como criar e gerenciar um gateway de VPN. Você aprenderá como:

> [!div class="checklist"]
> * Criar um gateway de VPN
> * Exibir o endereço IP público
> * Redimensionar um gateway VPN
> * Redefinir um gateway de VPN

O diagrama a seguir mostra a rede virtual e o gateway da VPN criados como parte deste tutorial.

![Gateway de VNet e VPN](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)

### <a name="azure-cloud-shell-and-azure-powershell"></a>Azure Cloud Shell e Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="common-network-parameter-values"></a>Valores de parâmetros de rede comuns

Abaixo estão os valores de parâmetro usados para este tutorial. Nos exemplos, as variáveis são traduzidas para o seguinte:

```
#$RG1         = The name of the resource group
#$VNet1       = The name of the virtual network
#$Location1   = The location region
#$FESubnet1   = The name of the first subnet
#$BESubnet1   = The name of the second subnet
#$VNet1Prefix = The address range for the virtual network
#$FEPrefix1   = Addresses for the first subnet
#$BEPrefix1   = Addresses for the second subnet
#$GwPrefix1   = Addresses for the GatewaySubnet
#$VNet1ASN    = ASN for the virtual network
#$DNS1        = The IP address of the DNS server you want to use for name resolution
#$Gw1         = The name of the virtual network gateway
#$GwIP1       = The public IP address for the virtual network gateway
#$GwIPConf1   = The name of the IP configuration
```

Altere os valores abaixo com base na configuração da rede e do ambiente e, em seguida, copie-os e cole-os para definir as variáveis para este tutorial. Se a sessão do Cloud Shell atingir o tempo limite ou você precisar usar outra janela do PowerShell, copie e cole as variáveis na nova sessão e continue o tutorial.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$VNet1ASN    = 65010
$DNS1        = "8.8.8.8"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Um grupo de recursos deve ser criado primeiro. No exemplo a seguir, um grupo de recursos chamado *TestRG1* é criado na região *Leste dos EUA*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

O gateway VPN do Azure fornece conectividade entre locais e a funcionalidade do servidor VPN de P2S para sua rede virtual. Adicione o gateway VPN a uma rede virtual existente ou crie uma nova rede virtual e o gateway. Observe que o exemplo especifica o nome da sub-rede de gateway especificamente. Você sempre deve especificar o nome da sub-rede de gateway como "GatewaySubnet" para que ela funcione corretamente. Este exemplo cria uma rede virtual com três sub-redes: Front-end, Back-end e GatewaySubnet usando [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) e [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -AddressPrefix $GwPrefix1
$vnet   = New-AzVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>Solicitar um endereço IP público para o gateway de VPN

Os gateways VPN do Azure se comunicam com os dispositivos VPN local pela Internet para realizar a negociação de IKE (Internet Key Exchange) e estabelecer os túneis IPsec. Crie e atribua um endereço IP público ao gateway de VPN, conforme mostrado no exemplo abaixo com [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) e [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig):

> [!IMPORTANT]
> No momento, você pode atribuir apenas um endereço IP público dinâmico a um gateway. Não há suporte para o endereço IP estático em gateways de VPN do Azure.

```azurepowershell-interactive
$gwpip    = New-AzPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-a-vpn-gateway"></a>Criar um gateway de VPN

Pode demorar 45 minutos ou mais para que o gateway VPN seja criado. Uma vez que o gateway tenha sido concluído, você pode criar uma conexão entre sua rede virtual e outra rede virtual. Ou criar uma conexão entre sua rede virtual e as redes locais. Crie um gateway de VPN usando o cmdlet [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway).

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

Valores de parâmetros de execução:
* GatewayType: use **Vpn** para conexões site a site e VNet a VNet
* VpnType: use **RouteBased** para interagir com um intervalo mais amplo de dispositivos VPN e mais recursos de roteamento
* GatewaySku: **VpnGw1** é o padrão; altere-o para VpnGw2 ou VpnGw3 se precisar de taxas de transferência mais altas ou mais conexões. Para obter mais informações, confira [SKUs de gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

Se você estiver usando o TryIt, sua sessão poderá atingir o tempo limite. Não se preocupe. O gateway ainda será criado.

Concluída a criação de gateway, crie uma conexão entre a rede virtual e outro VNet ou crie uma conexão entre a rede virtual e um local. Você também pode configurar uma conexão P2S para sua rede virtual em um computador cliente.

## <a name="view-the-gateway-public-ip-address"></a>Exibir o endereço IP público do gateway

Se você souber o nome do endereço IP público, use [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) para mostrar o endereço IP público atribuído ao gateway.

Se a sessão atingiu o tempo limite, copie os parâmetros comuns de rede do início deste tutorial para a nova sessão e continue.

```azurepowershell-interactive
$myGwIp = Get-AzPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="resize-a-gateway"></a>Redimensionar um gateway

Você poderá alterar a SKU de gateway do VPN depois que o gateway for criado. Especificações diferentes de suporte de SKUs de gateway como taxas de transferência, número de conexões etc. O exemplo a seguir usa [Resize-AzVirtualNetworkGateway](/powershell/module/az.network/Resize-azVirtualNetworkGateway) para redimensionar o gateway de VpnGw1 para VpnGw2. Para obter mais informações, confira [SKUs de gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

Redimensionar um gateway VPN também leva cerca de 30 a 45 minutos, embora esta operação **não** interrompa ou remove as conexões existentes e as configurações.

## <a name="reset-a-gateway"></a>Redefinir um gateway

Como parte das etapas de solução de problemas, é possível redefinir seu gateway de VPN do Azure para forçar o gateway VPN para reiniciar as configurações de túnel IPsec/IKE. Use [Reset-AzVirtualNetworkGateway](/powershell/module/az.network/Reset-azVirtualNetworkGateway) para redefinir o gateway.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

Para obter mais informações, consulte [Redefinir um gateway VPN](vpn-gateway-resetgw-classic.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se você avançar para o [próximo tutorial](vpn-gateway-tutorial-vpnconnection-powershell.md), o ideal será manter esses recursos porque eles são os pré-requisitos.

No entanto, se o gateway fizer parte de uma implantação de protótipo, teste ou prova de conceito, você poderá usar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, o gateway de VPN e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre a criação do gateway VPN e o gerenciamento como:

> [!div class="checklist"]
> * Criar um gateway de VPN
> * Exibir o endereço IP público
> * Redimensionar um gateway VPN
> * Redefinir um gateway de VPN

Ir para os tutoriais a seguir para saber mais sobre conexões de P2S, S2S e VNet para VNet.

> [!div class="nextstepaction"]
> * [Criar Conexões S2S](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [Criar conexões VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Criar conexões P2S](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
