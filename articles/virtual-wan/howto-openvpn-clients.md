---
title: Configurar clientes OpenVPN para a WAN virtual do Azure
description: Etapas para configurar clientes OpenVPN para a WAN virtual do Azure
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 05502b344b1224dff5d12f95b96b05baace98970
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94490994"
---
# <a name="configure-an-openvpn-client-for-azure-virtual-wan"></a>Configurar um cliente OpenVPN para a WAN virtual do Azure

Este artigo ajuda você a configurar clientes do **&reg; protocolo OpenVPN** . Você também pode usar o cliente VPN do Azure para Windows 10 para se conectar por meio do protocolo OpenVPN. Mais instruções podem ser encontradas [aqui](openvpn-azure-ad-client.md)

## <a name="before-you-begin"></a>Antes de começar

Crie uma configuração de VPN de usuário (ponto a site). Certifique-se de selecionar "OpenVPN" para o tipo de túnel. Para obter as etapas, consulte [criar uma configuração de P2S para a WAN virtual do Azure](virtual-wan-point-to-site-portal.md#p2sconfig).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre VPN de usuário (ponto a site), consulte [criar conexões VPN de usuário](virtual-wan-point-to-site-portal.md).

**"OpenVPN" é uma marca comercial da OpenVPN Inc.**
