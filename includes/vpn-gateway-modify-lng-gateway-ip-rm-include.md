---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6505b12b35ee436930ba6571c27db30c12030041
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419111"
---
### <a name="gwipnoconnection"></a> Para modificar o gateway de rede local 'gatewayIpAddress' - sem conexão de gateway

Se o dispositivo VPN ao qual você deseja se conectar mudou seu endereço IP público, você precisará modificar o gateway de rede local para refletir essa alteração. Use o exemplo para modificar um gateway de rede local que não tenha uma conexão de gateway.

Ao modificar esse valor, você também pode modificar os prefixos do endereço ao mesmo tempo. Use o nome existente do seu gateway de rede local para poder sobrescrever as configurações atuais. Se você usar um nome diferente, um novo gateway de rede local será criado, em vez de substituir o existente.

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name Site1 `
-Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName TestRG1
```

### <a name="gwipwithconnection"></a> Para modificar o gateway de rede local 'gatewayIpAddress' - conexão de gateway existente

Se o dispositivo VPN ao qual você deseja se conectar mudou seu endereço IP público, você precisará modificar o gateway de rede local para refletir essa alteração. Se uma conexão de gateway já existir, primeiro você precisa remover a conexão. Após a conexão ser removida, você pode modificar o endereço IP do gateway e recriar uma nova conexão. Você também pode modificar os prefixos do endereço ao mesmo tempo. Isso resulta em algum tempo de inatividade para a conexão VPN. Ao modificar o endereço IP de gateway, você não precisa excluir o gateway de VPN. Você precisa apenas remover a conexão.
 

1. Remova a conexão. Você pode encontrar o nome da sua conexão usando o cmdlet 'Get-AzVirtualNetworkGatewayConnection'.

   ```azurepowershell-interactive
   Remove-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 `
   -ResourceGroupName TestRG1
   ```
2. Modifique o valor de ‘GatewayIpAddress’. Você também pode modificar os prefixos do endereço ao mesmo tempo. Use o nome existente do seu gateway de rede local para sobrescrever as configurações atuais. Se você não fizer isso, um novo gateway de rede local será criado, em vez de substituir o existente.

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name Site1 `
   -Location "East US" -AddressPrefix @('10.101.0.0/24','10.101.1.0/24') `
   -GatewayIpAddress "104.40.81.124" -ResourceGroupName TestRG1
   ```
3. Crie a conexão. Neste exemplo, configuramos um tipo de conexão IPsec. Quando você recriar a conexão, use o tipo de conexão especificado para sua configuração. Para outros tipos de conexão, consulte a página [Cmdlet do PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .  Para obter o nome do VirtualNetworkGateway, você pode executar o cmdlet 'Get-AzVirtualNetworkGateway'.
   
    Defina as variáveis.

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1

   $vnetgw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
   ```
   
    Crie a conexão.

   ```azurepowershell-interactive 
   New-AzVirtualNetworkGatewayConnection -Name VNet1Site1 -ResourceGroupName TestRG1 `
   -Location "East US" `
   -VirtualNetworkGateway1 $vnetgw `
   -LocalNetworkGateway2 $local `
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```