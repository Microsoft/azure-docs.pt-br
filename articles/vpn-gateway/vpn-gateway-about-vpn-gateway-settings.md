---
title: 'Gateway de VPN do Azure: definições de configuração'
description: Saiba mais sobre os recursos e as configurações do gateway de VPN para uma rede virtual criada no modelo de implantação do Gerenciador de recursos.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: 1aba87b2139fb8a7d395fb3180d2074e47310fa9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010741"
---
# <a name="about-vpn-gateway-configuration-settings"></a>Sobre definições de configuração do Gateway de VPN

Um gateway de VPN é um tipo de gateway de rede virtual que envia o tráfego criptografado entre sua rede virtual e seu local em uma conexão pública. Você também pode usar o Gateway de VPN para enviar o tráfego entre redes virtuais no backbone do Azure.

Uma conexão de Gateway de VPN tem base na configuração de vários recursos, cada um deles contendo definições configuráveis. As seções neste artigo abordam os recursos e configurações relacionados a um gateway de VPN para uma rede virtual criada no modelo de implantação do Resource Manager. Você pode encontrar descrições e diagramas da topologia para cada solução de conexão no artigo [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md) .

Os valores neste artigo aplicam gateways VPN (gateways de rede virtual que usam o -GatewayType Vpn). Este artigo não abrange todos os tipos de gateway ou gateways redundantes de zona.

* Para obter valores que se aplicam a -GatewayType 'ExpressRoute', consulte [Gateways de rede virtual para o ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

* Para gateways redundantes de zona, consulte [Sobre gateways redundantes de zona](about-zone-redundant-vnet-gateways.md).

* Para WAN virtual, consulte [Sobre a WAN virtual](../virtual-wan/virtual-wan-about.md).

## <a name="gateway-types"></a><a name="gwtype"></a>Tipos de gateway

Cada rede virtual pode ter apenas um gateway de rede virtual de cada tipo. Quando estiver criando um gateway de rede virtual, você deve garantir que o tipo de gateway seja o correto para sua configuração.

Os valores disponíveis para o -GatewayType são:

* Vpn
* ExpressRoute

Um gateway de VPN exige o `-GatewayType` *Vpn*.

Exemplo:

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gateway-skus"></a><a name="gwsku"></a>SKUs do Gateway

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>Configurar uma SKU de gateway

**Portal do Azure**

Se você usa o portal do Azure para criar um gateway de rede virtual do Resource Manager, é possível selecionar o SKU do gateway usando o menu suspenso. As opções apresentadas correspondem ao tipo de Gateway e ao tipo de VPN que você selecionar.

**PowerShell**

O exemplo do PowerShell a seguir especifica o `-GatewaySku` como VpnGw1. Ao usar o PowerShell para criar um gateway, você precisa primeiro criar a configuração de IP e, em seguida, usar uma variável para fazer referência a ele. Neste exemplo, a variável de configuração é $gwipconfig.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

**CLI do Azure**

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizing-or-changing-a-sku"></a><a name="resizechange"></a>Redimensionar ou alterar uma SKU

Se você tiver um gateway de VPN e quer usar uma SKU de gateway diferente, suas opções serão redimensionar a SKU do gateway ou alterar para outra SKU. Ao alterar para outra SKU de gateway, você exclui o gateway existente e cria um novo. Um gateway pode levar até 45 minutos para ser criado. Em comparação, quando você redimensiona uma SKU de gateway, não há muito tempo de inatividade porque você não precisa excluir e recompilar o gateway. Se você tiver a opção de redimensionar a SKU do gateway, em vez de alterá-la, convém fazer isso. No entanto, há regras sobre o redimensionamento:

1. Com exceção da SKU básica, você pode redimensionar um SKU de gateway de VPN para outro SKU de gateway de VPN dentro da mesma geração (Generation1 ou Generation2). Por exemplo, VpnGw1 de Generation1 pode ser redimensionado para VpnGw2 de Generation1, mas não para VpnGw2 de Generation2.
2. Ao trabalhar com SKUs antigas, você ainda pode redimensionar entre SKUs Básicas, Standard e de Alto Desempenho.
3. **Não é possível** redimensionar de SKUs Basic/Standard/HighPerformance para SKUs VpnGw. Em vez disso, você deve [alterar](#change) para as novas SKUs.

#### <a name="to-resize-a-gateway"></a><a name="resizegwsku"></a>Para redimensionar um gateway

**Azure portal**

[!INCLUDE [Resize a SKU - portal](../../includes/vpn-gateway-resize-gw-portal-include.md)]

**PowerShell**

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="to-change-from-an-old-legacy-sku-to-a-new-sku"></a><a name="change"></a>Para alterar de uma SKU antiga (herdada) para uma nova SKU

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connection-types"></a><a name="connectiontype"></a>Tipos de conexão

No modelo de implantação do Resource Manager, cada configuração exige um tipo específico de conexão de gateway de rede virtual. Os valores disponíveis do PowerShell do Gerenciador de Recursos para o `-ConnectionType` são:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

No exemplo do PowerShell a seguir, criamos uma conexão S2S que exige o tipo de conexão *IPsec*.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpn-types"></a><a name="vpntype"></a>Tipos de VPN

Quando você cria o gateway de rede virtual para uma configuração de gateway de VPN, é preciso especificar um tipo de VPN. O tipo de VPN que você escolhe depende da topologia de conexão que quer criar. Por exemplo, uma conexão P2S requer um tipo de VPN RouteBased. Um tipo de VPN também pode depender do hardware que você usa. As configurações S2S requerem um dispositivo VPN. Alguns dispositivos VPN recebem suporte apenas de um determinado tipo de VPN.

O tipo de VPN que você escolher deve atender a todos os requisitos de conexão da solução que você quer criar. Por exemplo, se você quiser criar uma conexão de gateway de VPN S2S e uma conexão de gateway de VPN P2S para a mesma rede virtual, use o tipo de VPN *RouteBased* , pois P2S requer um tipo de VPN RouteBased. Você também precisa verificar se o seu dispositivo VPN é compatível com uma conexão VPN RouteBased. 

Depois que um gateway de rede virtual é criado, não é possível alterar o tipo de VPN. Você precisa excluir o gateway de rede virtual e criar outro. Há dois tipos de VPN:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

O exemplo do PowerShell a seguir especifica o `-VpnType` como *RouteBased*. Ao criar um gateway, você deve garantir que o -VpnType seja correto para sua configuração.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="gateway-requirements"></a><a name="requirements"></a>Requisitos do gateway

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gateway-subnet"></a><a name="gwsub"></a>Sub-rede do gateway

Antes de criar um gateway de VPN, crie uma sub-rede de gateway. A sub-rede de gateway contém os endereços IP que as VMs do gateway de rede virtual e os serviços usam. Quando você cria o gateway de rede virtual, as VMs de gateway são implantadas na sub-rede de gateway e definidas com as configurações necessárias de gateway de VPN. Nunca implante mais nada (por exemplo, VMs adicionais) para a sub-rede de gateway. A sub-rede do gateway deve ser nomeada como GatewaySubnet para funcionar corretamente. Chamar a sub-rede de gateway de 'GatewaySubnet' permite que o Azure saiba que essa é a sub-rede para implantação nas VMs de gateway de rede virtual e nos serviços.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Quando você cria a sub-rede de gateway, pode especificar o número de endereços IP que contém a sub-rede. Os endereços IP na sub-rede do gateway são alocados para as VMs de gateway e para os serviços de gateway. Algumas configurações exigem mais endereços IP do que outras. 

Ao planejar o tamanho da sub-rede do gateway, consulte a documentação da configuração que você planeja criar. Por exemplo, a configuração de gateway ExpressRoute/VPN coexistente requer uma sub-rede de gateway maior do que a maioria das outras configurações. Além disso, convém certificar-se de que sua sub-rede de gateway contenha endereços IP suficientes para acomodar possíveis configurações adicionais futuras. Embora seja possível criar uma sub-rede de gateway tão pequena quanto/29, recomendamos que você crie uma sub-rede de gateway de/27 ou maior (/27,/26, etc.) se tiver o espaço de endereço disponível para fazer isso. Isso irá acomodar a maioria das configurações.

O exemplo de PowerShell do Resource Manager a seguir mostra uma sub-rede de gateway chamada GatewaySubnet. Você pode ver que a notação CIDR especifica /27, que permite endereços IP suficientes para a maioria das configurações existentes no momento.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="local-network-gateways"></a><a name="lng"></a>Gateways de rede locais

Um gateway de rede local e diferente de um gateway de rede virtual. Ao criar uma configuração de gateway de VPN, o gateway de rede local geralmente representa a rede no local e o dispositivo VPN correspondente. No modelo de implantação clássico, o gateway de rede local era conhecido como um Site Local.

Você dá um nome ao gateway de rede local, o endereço IP público ou o FQDN (nome de domínio totalmente qualificado) do dispositivo VPN local e especifica os prefixos de endereço que estão localizados no local. O Azure examina os prefixos de endereço de destino para o tráfego de rede, consulta a configuração que você especificou para o gateway de rede local e roteia os pacotes adequadamente. Se você usar o Border Gateway Protocol (BGP) em seu dispositivo VPN, você fornecerá o endereço IP do par de BGP do seu dispositivo VPN e o número do sistema autônomo (ASN) da sua rede local. Você também especifica os gateways de rede para configurações de rede virtual para rede virtual que usam uma conexão de gateway de VPN.

O seguinte exemplo de PowerShell cria um novo gateway de rede local:

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Às vezes, você precisa modificar as configurações do gateway de rede local. Por exemplo, quando você adicionar ou modificar o intervalo de endereços, ou se o endereço IP do dispositivo VPN mudar. Consulte [Modificar as configurações de gateway de rede local usando o PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="rest-apis-powershell-cmdlets-and-cli"></a><a name="resources"></a>APIs REST, cmdlets do PowerShell e CLI

Para obter recursos técnicos adicionais e requisitos de sintaxe específicos ao usar APIs REST, cmdlets do PowerShell na CLI do Azure para configurações do Gateway de VPN, veja as seguintes páginas:

| **Clássico** | **Resource Manager** |
| --- | --- |
| [PowerShell](/powershell/module/az.network/#networking) |[PowerShell](/powershell/module/az.network#vpn) |
| [REST API](/previous-versions/azure/reference/jj154113(v=azure.100)) |[REST API](/rest/api/network/virtualnetworkgateways) |
| Sem suporte | [CLI do Azure](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as configurações de conexão disponíveis, confira [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md).