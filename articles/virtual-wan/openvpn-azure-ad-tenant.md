---
title: 'Locatário do Azure AD para conexões VPN de usuário: autenticação do Azure AD'
description: Você pode usar a VPN do usuário da WAN virtual do Azure (ponto a site) para se conectar à sua VNet usando a autenticação do Azure AD
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: a54397fe7cfecef6813105645b7f2b218894875e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91367845"
---
# <a name="prepare-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Preparar Azure Active Directory locatário para conexões de protocolo VPN do usuário OpenVPN

Ao conectar-se ao seu hub virtual por meio do protocolo IKEv2, você pode usar a autenticação baseada em certificado ou a autenticação RADIUS. No entanto, ao usar o protocolo OpenVPN, você também pode usar a autenticação Azure Active Directory. Este artigo ajuda você a configurar um locatário do Azure AD para VPN de usuário de WAN virtual (ponto a site) usando a autenticação OpenVPN.

> [!NOTE]
> A autenticação do Azure AD tem suporte apenas para &reg; conexões de protocolo OpenVPN.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Criar o locatário do Azure AD

Verifique se você tem um locatário do Azure AD. Se você não tiver um locatário do Azure AD, poderá criar um usando as etapas no artigo [criar um novo locatário](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Nome da organização
* Nome de domínio inicial

Exemplo:

   ![Novo locatário do Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. criar usuários de locatário do Azure AD

Em seguida, crie duas contas de usuário no locatário do Azure AD recém-criado, uma conta de administrador global e uma conta de usuário. A conta de usuário pode ser usada para testar a autenticação OpenVPN e a conta de administrador global será usada para conceder consentimento ao registro do aplicativo VPN do Azure. Depois de criar uma conta de usuário do Azure AD, atribua uma **função de diretório** ao usuário para delegar permissões administrativas.

Use as etapas neste [artigo](../active-directory/fundamentals/add-users-azure-active-directory.md) para criar os dois usuários para seu locatário do Azure AD. Certifique-se de alterar a **função de diretório** em uma das contas criadas para **administrador global**.

## <a name="3-grant-consent-to-the-azure-vpn-app-registration"></a><a name="enable-authentication"></a>3. conceder consentimento para o registro do aplicativo VPN do Azure

1. Entre no portal do Azure como um usuário que é atribuído à função de **administrador global** .

2. Em seguida, conceda consentimento de administrador para sua organização, isso permite que o aplicativo VPN do Azure entre e leia perfis de usuário. Copie e cole a URL pertencente ao local de implantação na barra de endereços do navegador:

    Público

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Alemanha

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

3. Selecione a conta de **administrador global** , se solicitado.

    ![ID do Diretório](./media/openvpn-create-azure-ad-tenant/pick.png)

4. Selecione **Aceitar** quando solicitado.

    ![Captura de tela mostra a caixa de diálogo com as permissões de mensagem solicitadas aceitar para sua organização e informações adicionais.](./media/openvpn-create-azure-ad-tenant/accept.jpg)

5. Em seu Azure AD, em **aplicativos empresariais**, agora você deve ver a **VPN do Azure** listada.

    ![VPN do Azure](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

## <a name="next-steps"></a>Próximas etapas

Para se conectar às suas redes virtuais usando a autenticação do Azure AD, você deve criar uma configuração de VPN de usuário e associá-la a um hub virtual. Consulte [Configurar a autenticação do Azure ad para a conexão ponto a site com o Azure](virtual-wan-point-to-site-azure-ad.md).
