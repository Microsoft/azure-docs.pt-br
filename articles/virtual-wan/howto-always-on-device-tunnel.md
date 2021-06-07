---
title: Configurar um túnel de VPN Always-On
titleSuffix: Azure Virtual WAN
description: Etapas para configurar o túnel de dispositivo VPN Always On para WAN virtual
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: e814487cb4dab9c8c19daab2ea3bb81391d4a98f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90983679"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Configurar um túnel de dispositivo VPN Always On para WAN virtual

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você deve criar uma configuração ponto a site e editar a atribuição de Hub virtual. Consulte as seguintes seções para obter instruções:

* [Criar uma configuração do P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Criar Hub com o gateway P2S](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-the-device-tunnel"></a>Configurar o túnel do dispositivo

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>Para remover um perfil

Para remover o perfil, execute o seguinte comando:

![Captura de tela mostra uma janela do PowerShell que executa o comando Remove-VpnConnection-Name MachineCertTest.](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).