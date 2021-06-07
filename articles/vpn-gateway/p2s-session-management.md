---
title: 'Gateway de VPN do Azure: gerenciamento de sessão de VPN ponto a site'
description: Este artigo ajuda você a exibir e desconectar sessões de VPN ponto a site.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/23/2020
ms.author: cherylmc
ms.openlocfilehash: 2f2184507e17e3ecae40bb33be4202c183d32b77
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91274226"
---
# <a name="point-to-site-vpn-session-management"></a>Gerenciamento de sessão de VPN ponto a site

Os gateways de rede virtual do Azure fornecem uma maneira fácil de exibir e desconectar sessões de VPN ponto a site atuais. Este artigo ajuda você a exibir e desconectar as sessões atuais.

>[!NOTE]
>O status da sessão é atualizado a cada 5 minutos. Ele não é atualizado imediatamente.
>

## <a name="portal"></a>Portal

Para exibir e desconectar uma sessão no Portal:

1. Navegue até o gateway de VPN.
1. Na seção **monitoramento** , selecione **sessões ponto a site**.

   :::image type="content" source="./media/p2s-session-management/portal.png" alt-text="Exemplo de portal":::
1. Você pode exibir todas as sessões atuais no windowpane.
1. Selecione **"..."** para a sessão que você deseja desconectar e, em seguida, selecione **Desconectar**.

## <a name="powershell"></a>PowerShell

Para exibir e desconectar uma sessão usando o PowerShell:

1. Execute o seguinte comando do PowerShell para listar sessões ativas:

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGatewayVpnClientConnectionHealth -VirtualNetworkGatewayName <name of the gateway>  -ResourceGroupName <name of the resource group>
   ```
1. Copie o **VpnConnectionId** da sessão que você deseja desconectar.

   :::image type="content" source="./media/p2s-session-management/powershell.png" alt-text="Exemplo de PowerShell":::
1. Para desconectar a sessão, execute o seguinte comando:

   ```azurepowershell-interactive
   Disconnect-AzVirtualNetworkGatewayVpnConnection -VirtualNetworkGatewayName <name of the gateway> -ResourceGroupName <name of the resource group> -VpnConnectionId <VpnConnectionId of the session>
   ```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre conexões ponto a site, consulte [sobre VPN ponto a site](point-to-site-about.md).
