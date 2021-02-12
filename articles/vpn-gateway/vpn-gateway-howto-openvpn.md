---
title: Como configurar o OpenVPN no gateway de VPN do Azure
description: Saiba como usar o PowerShell para habilitar o protocolo OpenVPN no gateway de VPN do Azure para um ambiente ponto a site.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/05/2021
ms.author: cherylmc
ms.openlocfilehash: 34f24b8fbdb28e1b1f73e9db428c510d3f4661ce
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804829"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Configurar o OpenVPN para gateway de VPN ponto a site do Azure

Este artigo ajuda você a configurar o **protocolo OpenVPN®** no gateway de VPN do Azure. Você pode usar o portal ou as instruções do PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

* O artigo supõe que você já tem um ambiente de trabalho de ponto a site. Se você não fizer isso, crie um usando um dos métodos a seguir.

  * [Portal-criar ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)

  * [PowerShell-criar ponto a site](vpn-gateway-howto-point-to-site-rm-ps.md)

* Verifique se o seu gateway de VPN não usa o SKU básico. Não há suporte para o SKU Básico no OpenVPN.

## <a name="portal"></a>Portal

1. No portal, navegue até o **Gateway de rede virtual-> configuração ponto a site**.
1. Para **tipo de túnel**, selecione **OpenVPN (SSL)** ou **IKEv2 e OpenVPN (SSL)** na lista suspensa.

   :::image type="content" source="./media/vpn-gateway-howto-openvpn/portal.png" alt-text="Selecione OpenVPN SSL na lista suspensa":::
1. Salve suas alterações e continue com **as próximas etapas**.

Habilite o OpenVPN no gateway.

1. Habilite OpenVPN em seu gateway usando o exemplo a seguir:

   ```azurepowershell-interactive
   $gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
   Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
   ```
1. Continue com **as próximas etapas**.

## <a name="next-steps"></a>Próximas etapas

Para configurar clientes para OpenVPN, veja [Configurar clientes do OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" é uma marca comercial da OpenVPN Inc.**
