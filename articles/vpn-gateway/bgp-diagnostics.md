---
title: Exibir status e métricas do BGP
titleSuffix: Azure VPN Gateway
description: Exiba informações importantes relativas ao BGP para solução de problemas.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: sample
ms.date: 03/10/2021
ms.author: alzam
ms.openlocfilehash: f97bbccc980705699af822ba2730233239cdfd5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103148773"
---
# <a name="view-bgp-metrics-and-status"></a>Exibir métricas e status do BGP

Você pode exibir as métricas e o status do BGP usando o portal do Azure ou o Azure PowerShell.

## <a name="azure-portal"></a>Portal do Azure

No portal do Azure, você pode ver os pares, as rotas aprendidas e as rotas anunciadas do BGP. Você também pode baixar arquivos .csv que contêm esses dados.

1. No [portal do Azure](https://portal.azure.com), navegue até seu gateway de rede virtual.
1. Em **Monitoramento**, selecione **Pares de BGP** para abrir a página de pares no nível de protocolo BGP.

   :::image type="content" source="./media/bgp-diagnostics/bgp-portal.jpg" alt-text="Captura de tela de métrica no portal do Azure.":::

### <a name="learned-routes"></a>Rotas aprendidas

1. Você pode exibir até 50 rotas aprendidas no portal.

   :::image type="content" source="./media/bgp-diagnostics/learned-routes.jpg" alt-text="Captura de tela das rotas aprendidas.":::

1. Você também pode baixar o arquivo de rotas aprendidas. Se você tem mais de 50 rotas aprendidas, a única maneira de ver todas elas é baixando e abrindo o arquivo .csv. Para baixar, selecione **Baixar rotas aprendidas**.

   :::image type="content" source="./media/bgp-diagnostics/download-routes.jpg" alt-text="Captura de tela do download das rotas aprendidas.":::
1. Em seguida, veja o arquivo.

   :::image type="content" source="./media/bgp-diagnostics/learned-routes-file.jpg" alt-text="Captura de tela das rotas aprendidas baixadas.":::

### <a name="advertised-routes"></a>Rotas anunciadas

1. Para ver as rotas anunciadas, selecione o **...** no final da rede que deseja ver e clique em **Exibir rotas anunciadas**.

   :::image type="content" source="./media/bgp-diagnostics/select-route.png" alt-text="Captura de tela mostrando como ver rotas anunciadas." lightbox="./media/bgp-diagnostics/route-expand.png":::
1. Na página **Rotas anunciadas para o par**, você pode ver até 50 rotas anunciadas.

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes.jpg" alt-text="Captura de tela das rotas anunciadas.":::
1. Você também pode baixar o arquivo de rotas anunciadas. Se você tem mais de 50 rotas anunciadas, a única maneira de ver todas elas é baixando e abrindo o arquivo .csv. Para baixar, selecione **Baixar rotas anunciadas**.

   :::image type="content" source="./media/bgp-diagnostics/download-advertised.jpg" alt-text="Captura de tela da seleção das rotas anunciadas baixadas.":::
1. Em seguida, veja o arquivo.

   :::image type="content" source="./media/bgp-diagnostics/advertised-routes-file.jpg" alt-text="Captura de tela das rotas anunciadas baixadas.":::

### <a name="bgp-peers"></a>Pares de BGP

1. Você pode ver até 50 pares de BGP no portal.

   :::image type="content" source="./media/bgp-diagnostics/peers.jpg" alt-text="Captura de tela dos pares de BGP.":::
1. Você também pode baixar o arquivo de pares de BGP. Se você tem mais de 50 pares de BGP, a única maneira de ver todos eles é baixando e abrindo o arquivo .csv. Para baixar, selecione **Baixar pares de BGP** na página do portal.

   :::image type="content" source="./media/bgp-diagnostics/download-peers.jpg" alt-text="Captura de tela do download de pares de BGP.":::
1. Em seguida, veja o arquivo.

   :::image type="content" source="./media/bgp-diagnostics/peers-file.jpg" alt-text="Captura de tela dos pares de BGP baixados.":::

## <a name="powershell"></a>PowerShell

Use **Get-AzVirtualNetworkGatewayBGPPeerStatus** para ver todos os pares de BGP e o status.

[!INCLUDE [VPN Gateway PowerShell instructions](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBgpPeerStatus -ResourceGroupName resourceGroup -VirtualNetworkGatewayName gatewayName

Asn               : 65515
ConnectedDuration : 9.01:04:53.5768637
LocalAddress      : 10.1.0.254
MessagesReceived  : 14893
MessagesSent      : 14900
Neighbor          : 10.0.0.254
RoutesReceived    : 1
State             : Connected
```

Use **Get-AzVirtualNetworkGatewayLearnedRoute** para exibir todas as rotas que o gateway aprendeu pelo BGP.

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName resourceGroup -VirtualNetworkGatewayname gatewayName

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.1.0.0/16
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       :
LocalAddress : 10.1.0.254
Network      : 10.0.0.254/32
NextHop      :
Origin       : Network
SourcePeer   : 10.1.0.254
Weight       : 32768

AsPath       : 65515
LocalAddress : 10.1.0.254
Network      : 10.0.0.0/16
NextHop      : 10.0.0.254
Origin       : EBgp
SourcePeer   : 10.0.0.254
Weight       : 32768
```

Use **Get-AzVirtualNetworkGatewayAdvertisedRoute** para exibir todas as rotas que o gateway está anunciando a seus pares por meio do BGP.

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName gatewayName -ResourceGroupName resourceGroupName -Peer 10.0.0.254
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o BGP, confira [Configurar BGP para o Gateway de VPN](bgp-howto.md).
