---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 02/21/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 922ac7eb6cb9676af65700a6a2fe7fbae35a0dc5
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172455"
---
As etapas para essa tarefa usam uma VNet com base nos valores na lista de referência de configuração a seguir. Nomes e configurações adicionais também são descritos nesta lista. Não usamos essa lista diretamente em nenhuma uma das etapas, embora adicionemos variáveis com base nos valores contidos nessa lista. É possível fazer uma cópia da lista para usá-la como referência, substituindo os valores pelos seus próprios.

* Nome da Rede Virtual = “TestVNet”
* Espaço de endereço da Rede Virtual = 192.168.0.0/16
* Grupo de recursos = “TestRG”
* Nome de Subnet1 = “FrontEnd” 
* Espaço de endereço de sub-rede1 = "192.168.1.0/24"
* Nome da sub-rede do gateway: “GatewaySubnet” Deve-se sempre nomear uma sub-rede do gateway como *GatewaySubnet*.
* Espaço de endereço da Sub-Rede do Gateway = “192.168.200.0/26”
* Região = “Leste dos EUA”
* Nome do Gateway = “GW”
* Nome do IP do Gateway = “GWIP”
* Nome da configuração de IP do Gateway = “gwipconf”
* Tipo = “ExpressRoute” Este tipo é necessário para uma configuração de ExpressRoute.
* Nome do IP público do Gateway = “gwpip”

## <a name="add-a-gateway"></a>Adicionar um gateway
1. Conecte-se à sua Assinatura do Azure.

   [!INCLUDE [Sign in](expressroute-cloud-shell-connect.md)]
2. Declare as variáveis para este exercício. Lembre-se de editar o exemplo para que elas reflitam as configurações que deseja usar.

   ```azurepowershell-interactive 
   $RG = "TestRG"
   $Location = "East US"
   $GWName = "GW"
   $GWIPName = "GWIP"
   $GWIPconfName = "gwipconf"
   $VNetName = "TestVNet"
   ```
3. Armazene o objeto de rede virtual como uma variável.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   ```
4. Adicione uma sub-rede de gateway à sua Rede Virtual. A sub-rede de gateway deve ser nomeada “GatewaySubnet”. Você deve criar uma sub-rede de gateway que seja /27 ou maior (/26, /25 etc.).

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26
   ```
5. Defina a configuração.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Armazene a sub-rede de gateway como uma variável.

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
   ```
7. Solicite um endereço IP público. O endereço IP é solicitado antes da criação do gateway. Não é possível especificar o endereço IP que você deseja usar; ele é alocado dinamicamente. Você usará esse endereço IP na próxima seção de configuração. O AllocationMethod deve ser Dynamic.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName  -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   ```
8. Crie a configuração de seu gateway. A configuração do gateway define a sub-rede e o endereço IP público a serem usados. Nesta etapa, você especificará a configuração que será usada quando o gateway for criado. Essa etapa não cria, de fato, o objeto de gateway. Use o exemplo a seguir para criar a configuração do gateway.

   ```azurepowershell-interactive
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```
9. Crie o gateway. Nesta etapa, o **-GatewayType** é especialmente importante. É necessário usar o valor **ExpressRoute**. Observe que, depois de executar esses cmdlets, o gateway pode levar 45 minutos ou mais para ser criado.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard
   ```

## <a name="verify-the-gateway-was-created"></a>Verificar se o gateway foi criado
Use os comandos a seguir para verificar se o gateway foi criado:

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -ResourceGroupName $RG
```

## <a name="resize-a-gateway"></a>Redimensionar um gateway
Há uma série de [SKUs de Gateway](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Você pode usar o comando a seguir para alterar a SKU de gateway a qualquer momento.

> [!IMPORTANT]
> Esse comando não funciona para o gateway UltraPerformance. Para alterar o gateway para um gateway UltraPerformance, primeiro remova o gateway do ExpressRoute existente e crie um novo gateway UltraPerformance. Para fazer downgrade do gateway de um gateway UltraPerformance, primeiro remova o gateway UltraPerformance e crie um novo gateway.
> 
> 

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="remove-a-gateway"></a>Remover um gateway
Use o seguinte comando para remover um gateway:

```azurepowershell-interactive
Remove-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
```
