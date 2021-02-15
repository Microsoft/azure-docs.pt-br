---
title: 'Gateway de VPN: locatário do Azure AD para grupos de usuários diferentes: autenticação do Azure AD'
description: Você pode usar a VPN P2S para se conectar à sua VNet usando a autenticação do Azure AD
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: cherylmc
ms.openlocfilehash: 1305ca603aef63dafcc7b055d55e3f0fe281f4fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91819680"
---
# <a name="create-an-active-directory-ad-tenant-for-p2s-openvpn-protocol-connections"></a>Criar um locatário Active Directory (AD) para conexões de protocolo P2S OpenVPN

Ao conectar-se à sua VNet, você pode usar a autenticação baseada em certificado ou a autenticação RADIUS. No entanto, ao usar o protocolo VPN aberto, você também pode usar a autenticação Azure Active Directory. Se desejar que um conjunto diferente de usuários seja capaz de se conectar a diferentes gateways de VPN, você poderá registrar vários aplicativos no AD e vinculá-los a diferentes gateways de VPN. Este artigo ajuda você a configurar um locatário do Azure AD para autenticação P2S OpenVPN e criar e registrar vários aplicativos no Azure AD para permitir acesso diferente para diferentes usuários e grupos.

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. habilitar a autenticação no gateway

Nesta etapa, você habilitará a autenticação do Azure AD no gateway de VPN.

1. Habilite a autenticação do Azure AD no gateway de VPN navegando para **configuração de ponto a site** e selecionando **OpenVPN (SSL)** como o **tipo de túnel**. Selecione **Azure Active Directory** como o **tipo de autenticação** e preencha as informações na seção **Azure Active Directory** .

    ![portal do Azure exibição](./media/openvpn-azure-ad-tenant-multi-app/azure-ad-auth-portal.png)

    > [!NOTE]
    > Não use a ID do aplicativo do cliente de VPN do Azure: ele concederá a todos os usuários acesso ao gateway de VPN. Use a ID dos aplicativos que você registrou.

2. Crie e baixe o perfil clicando no link **baixar cliente VPN** .

3. Extraia o arquivo zip baixado.

4. Navegue até a pasta "AzureVPN" descompactada.

5. Anote o local do arquivo "azurevpnconfig.xml". O azurevpnconfig.xml contém a configuração para a conexão VPN e pode ser importado diretamente para o aplicativo cliente VPN do Azure. Você também pode distribuir esse arquivo para todos os usuários que precisam se conectar por email ou outros meios. O usuário precisará de credenciais válidas do Azure AD para se conectar com êxito.

## <a name="next-steps"></a>Próximas etapas

Para se conectar à sua rede virtual, você deve criar e configurar um perfil de cliente VPN. Consulte [configurar um cliente VPN para conexões VPN P2S](openvpn-azure-ad-client.md).
