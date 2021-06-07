---
title: 'WAN virtual do Azure: perfis de cliente VPN de usuário'
description: Isso ajuda você a trabalhar com o arquivo de perfil do cliente
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/08/2021
ms.author: cherylmc
ms.openlocfilehash: d83b6ed2ae83db569d3c61e3cf4cd887f875eb25
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99980908"
---
# <a name="working-with-user-vpn-client-profile-files"></a>Trabalhando com arquivos de perfil de cliente VPN de usuário

Os arquivos de perfil contêm informações necessárias para configurar uma conexão VPN. Este artigo ajuda você a obter e entender as informações necessárias para um perfil de cliente VPN de usuário.

## <a name="download-the-profile"></a>Baixar o perfil

Você pode usar as etapas no artigo [baixar perfis](global-hub-profile.md) para baixar o arquivo zip do perfil do cliente.

[!INCLUDE [client profiles](../../includes/vpn-gateway-vwan-vpn-profile-download.md)]

* A pasta **OpenVPN** contém o perfil *ovpn* que precisa ser modificado para incluir a chave e o certificado. Para obter mais informações, consulte [configurar clientes do OpenVPN](../virtual-wan/howto-openvpn-clients.md#windows).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre VPN de usuário de WAN virtual, consulte [criar uma conexão VPN de usuário](virtual-wan-point-to-site-portal.md).
