---
title: 'Como configurar o OpenVPN no gateway de VPN do Azure: PowerShell'
description: Saiba como usar o PowerShell para habilitar o protocolo OpenVPN no gateway de VPN do Azure para um ambiente ponto a site.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/05/2021
ms.author: cherylmc
ms.openlocfilehash: 1e2f7f754ae9a1547d6543dba65c69511ab7ceb1
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99624905"
---
# <a name="configure-openvpn-for-azure-point-to-site-vpn-gateway"></a>Configurar o OpenVPN para gateway de VPN ponto a site do Azure

Este artigo ajuda você a configurar o **protocolo OpenVPN®** no gateway de VPN do Azure. O artigo supõe que você já tem um ambiente de trabalho de ponto a site. Se você não tiver, use as instruções na etapa 1 para criar uma VPN ponto a site.



## <a name="1-create-a-point-to-site-vpn"></a><a name="vnet"></a>1. criar uma VPN ponto a site

Se você ainda não tiver um ambiente de ponto a site funcionando, siga as instruções para criar um. Veja [Criar uma VPN ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) para criar e configurar um gateway de VPN ponto a site com a autenticação de certificado nativa do Azure. 

> [!IMPORTANT]
> Não há suporte para o SKU Básico no OpenVPN.

## <a name="2-enable-openvpn-on-the-gateway"></a><a name="enable"></a>2. habilitar OpenVPN no gateway

Habilite o OpenVPN no gateway.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -ResourceGroupName $rgname -name $name
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientProtocol OpenVPN
```

## <a name="next-steps"></a>Próximas etapas

Para configurar clientes para OpenVPN, veja [Configurar clientes do OpenVPN](vpn-gateway-howto-openvpn-clients.md).

**"OpenVPN" é uma marca comercial da OpenVPN Inc.**
