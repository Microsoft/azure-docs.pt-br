---
title: Baixar perfis VPN do Azure virtual WAN global ou baseado em Hub | Microsoft Docs
description: Saiba como a WAN virtual do Azure oferece dois tipos de conectividade para usuários remotos e como baixar um perfil.
services: virtual-wan
author: kumudD
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: 6d5c4ba0f1f55119d1ec38296e67ae3e90c52650
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91313715"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>Baixar um perfil global ou baseado em Hub para clientes VPN de usuário

A WAN virtual do Azure oferece dois tipos de conectividade para usuários remotos: global e baseado em Hub. Use as seções a seguir para saber mais sobre o e baixar um perfil. 

> [!IMPORTANT]
> A autenticação RADIUS dá suporte apenas ao perfil baseado em Hub.

## <a name="global-profile"></a>Perfil global

O perfil aponta para um balanceador de carga que inclui todos os hubs de VPN de usuário ativos. O usuário é direcionado para o Hub que está mais próximo do local geográfico do usuário. Esse tipo de conectividade é útil quando os usuários viajam para locais diferentes com frequência. Para baixar o perfil **global** :

1. Navegue até a WAN virtual.
2. Clique em **configuração de VPN de usuário**.
3. Realce a configuração para a qual você deseja baixar o perfil.
4. Clique em **baixar perfil VPN de usuário de WAN virtual**.

   ![Perfil global](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>Perfil baseado em Hub

O perfil aponta para um único Hub. O usuário só pode se conectar ao Hub específico usando esse perfil. Para baixar o perfil **baseado em Hub** :

1. Navegue até a WAN virtual.
2. Clique em **Hub** na página Visão geral.

    ![Perfil de Hub 1](./media/global-hub-profile/hub1.png)
3. Clique em **VPN do usuário (ponto a site)**.
4. Clique em **baixar perfil de VPN de usuário do Hub virtual**.

   ![Perfil do Hub 2](./media/global-hub-profile/hub2.png)
5. Verifique **EAPTLS**.
6. Clique em **gerar e baixar perfil**.

   ![Perfil do Hub 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a WAN Virtual, consulte a página [Visão geral de WAN Virtual](virtual-wan-about.md).
