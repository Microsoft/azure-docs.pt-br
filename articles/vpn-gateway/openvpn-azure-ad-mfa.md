---
title: 'Habilitar MFA para usuários VPN: autenticação do Azure AD'
description: Habilitar a autenticação multifator para usuários VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 9ab595d3eb81cc7d1bdd65d1e4c3f4273a2460ea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96019099"
---
# <a name="enable-azure-ad-multi-factor-authentication-mfa-for-vpn-users"></a>Habilitar a MFA (autenticação multifator) do Azure AD para usuários VPN

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enable-authentication"></a><a name="enableauth"></a>Habilitar autenticação

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="configure-sign-in-settings"></a><a name="enablesign"></a>Definir configurações de entrada

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="option-1---per-user-access"></a><a name="peruser"></a>Opção 1-acesso por usuário

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="option-2---conditional-access"></a><a name="conditional"></a>Opção 2-acesso condicional

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>Próximas etapas

Para se conectar à sua rede virtual, você deve criar e configurar um perfil de cliente VPN. Consulte [configurar um cliente VPN para conexões VPN P2S](openvpn-azure-ad-client.md).