---
title: Configurar um túnel de usuário de VPN Always-On
titleSuffix: Azure VPN Gateway
description: Este artigo descreve como configurar um túnel de usuário de VPN Always On para seu gateway de VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 99d21222a62ed6b27a6a1b2a73b704f4cb26457b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89435789"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Configurar um túnel de usuário Always On VPN

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>Configurar o gateway

 Use as instruções no artigo [Configurar uma conexão VPN ponto a site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) para configurar o gateway de VPN para usar a autenticação IKEv2 e baseada em certificado.

## <a name="configure-a-user-tunnel"></a>Configurar um túnel de usuário

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>Para remover um perfil

Para remover um perfil, use as seguintes etapas:

1. Execute o comando a seguir:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Desconecte a conexão e desmarque a caixa de seleção **conectar automaticamente** .

   ![Limpeza](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>Próximas etapas

Para solucionar problemas de conexão que podem ocorrer, consulte [problemas de conexão de ponto a site do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
