---
title: Configurar um túnel de usuário de VPN Always-On
titleSuffix: Azure Virtual WAN
description: Este artigo descreve como configurar um túnel de usuário de VPN Always On para sua WAN virtual
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: e83ca64d2b0e50ec02007a3cd878e6bf034d0961
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91313579"
---
# <a name="configure-an-always-on-vpn-user-tunnel-for-virtual-wan"></a>Configurar um túnel de usuário de VPN Always On para WAN virtual

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você deve criar uma configuração ponto a site e editar a atribuição de Hub virtual. Consulte as seguintes seções para obter instruções:

* [Criar uma configuração do P2S](virtual-wan-point-to-site-portal.md#p2sconfig)
* [Criar Hub com o gateway P2S](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-a-user-tunnel"></a>Configurar um túnel de usuário

[!INCLUDE [user tunnel](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Para remover um perfil

Para remover um perfil, use as seguintes etapas:

1. Execute o comando a seguir:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Desconecte a conexão e desmarque a caixa de seleção **conectar automaticamente** .

   ![Limpeza](./media/howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).
