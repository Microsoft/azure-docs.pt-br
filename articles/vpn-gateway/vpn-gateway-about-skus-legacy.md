---
title: SKUs de gateway de VPN de rede virtual do Azure herdadas
description: Como trabalhar com as SKUs do gateway de rede virtual antigas; Basic, Standard e de Alto desempenho.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/15/2019
ms.author: cherylmc
ms.openlocfilehash: 9c5e6d5aca51bd560a46837ba47de86362665773
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "84687779"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Trabalhar com SKUs de gateway de rede virtual (SKUs herdadas)

Este artigo contém informações sobre as SKUs de gateway de rede virtual herdadas (antigas). As SKUs herdadas ainda funcionam em ambos os modelos de implantação de gateways de VPN que já foram criados. Os gateways de VPN clássicos continuam a usar as SKUs herdadas, para os gateways existentes e para os novos gateways. Ao criar novos gateways de VPN do Resource Manager, use as novas SKUs de gateway. Para saber mais sobre os novo SKUs, veja [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md).

## <a name="gateway-skus"></a><a name="gwsku"></a>SKUs do Gateway

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

Você pode exibir os preços do gateway herdado na seção **gateways de rede virtual** , que está localizada na [página de preços do ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute).

## <a name="estimated-aggregate-throughput-by-sku"></a><a name="agg"></a>Taxa de transferência agregada estimada por SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="supported-configurations-by-sku-and-vpn-type"></a><a name="config"></a>Configurações com suporte pelo tipo de SKU e de VPN

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize-a-gateway"></a><a name="resize"></a>Redimensionar um gateway

Você pode redimensionar sua gateway para uma SKU de gateway dentro da mesma família de SKUs. Por exemplo, se você tiver uma SKU Standard, poderá redimensionar para uma SKU HighPerformance. No entanto, você não pode redimensionar seu gateway de VPN entre as SKUs antigas e as novas famílias de SKU. Por exemplo, você não pode ir de uma SKU padrão para uma SKU VpnGw2, ou de uma SKU Básica para VpnGw1.

### <a name="resource-manager"></a>Gerenciador de Recursos

Para redimensionar um gateway para o modelo de implantação do Gerenciador de Recursos usando o PowerShell, use o seguinte comando:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

Você também pode redimensionar um gateway no portal do Azure.

### <a name="classic"></a><a name="classicresize"></a>Clássico

Para redimensionar um gateway para o modelo de implantação clássico, você deve usar os cmdlets do PowerShell de gerenciamento de serviços. Use o seguinte comando:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="change-to-the-new-gateway-skus"></a><a name="change"></a>Alterar para as novas SKUs de gateway

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre as novas SKUs do Gateway, consulte [SKUs de Gateway](vpn-gateway-about-vpngateways.md#gwsku).

Para saber mais sobre definições de configuração, veja [Sobre definições de configuração do Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md).
