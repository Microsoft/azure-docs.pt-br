---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/15/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c3236f9c60cb359349d96e93f674c3e278e44f1e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93375877"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Criar o locatário do Azure AD

Crie um locatário do Azure AD usando as etapas no artigo [Criar um novo locatário](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md):

* Nome organizacional
* Nome de domínio inicial

  Exemplo:

   ![Novo locatário do Azure AD](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. Criar usuários do locatário

Nesta etapa, você cria dois usuários do locatário do Azure AD: Uma conta de Administrador Global e uma conta de usuário mestre. A conta de usuário mestre é usada como a conta de incorporação mestre (conta de serviço). Ao criar uma conta de usuário do locatário do Azure AD, você ajusta a função do Diretório para o tipo de usuário que deseja criar. Use as etapas [neste artigo](../articles/active-directory/fundamentals/add-users-azure-active-directory.md) para criar pelo menos dois usuários para o locatário do Azure AD. Não deixe de alterar a **Função do diretório** para criar os tipos de conta:

* Administrador global
* Usuário

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. Registrar o cliente VPN

Registre o cliente VPN no locatário do Azure AD.

1. Localize a ID do diretório que você deseja usar para autenticação. Está listada na seção Propriedades da página Active Directory.

    ![ID do Diretório](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. Copie a ID do diretório.

3. Entre no portal do Azure como usuário atribuído à função de **Administrador global**.

4. Em seguida, dê o consentimento do administrador. Copie e cole a URL pertencente ao local de implantação na barra de endereços do navegador:

    Público

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Alemanha

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

> [!NOTE]
> Se você usar uma conta de administrador global que não seja nativa ao locatário do Azure AD para fornecer consentimento, substitua "Common" pela ID de diretório do Azure AD na URL. Você também pode precisar substituir "Common" pela sua ID de diretório em alguns outros casos também.
>

5. Selecione a conta de **Administrador global**, se solicitado.

    ![ID do diretório 2](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Selecione **Aceitar** quando solicitado.

    ![Captura de tela mostra uma janela com as permissões de mensagem solicitadas de aceitação para sua organização e informações sobre a solicitação.](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. No Azure AD, em **aplicativos Empresariais**, confira a **VPN do Azure** listada.

     ![VPN do Azure](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. Registrar aplicativos adicionais

Nesta etapa, registre os aplicativos adicionais para vários usuários e grupos.

1. No Azure Active Directory, clique em **Registros de aplicativo** e depois em **+ Novo registro**.

    ![VPN do Azure 2](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. Na página **Registrar um aplicativo**, insira o **Nome**. Selecione os **tipos de conta com suporte** desejados e clique em **Registrar**.

    ![VPN do Azure 3](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Após registrar o novo aplicativo, clique em **Expor uma API** na folha do aplicativo.

4. Clique em **+ Adicionar um escopo**.

5. Deixe o **URI da ID do aplicativo** padrão. Clique em **Salvar e continuar**.

    ![VPN do Azure 4](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. Preencha os campos obrigatórios e verifique se o **Estado** está **Habilitado**. Clique em **Adicionar escopo**.

    ![VPN do Azure 5](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. Clique em **Expor uma API** e depois em **+ Adicionar um aplicativo cliente**.  Para **ID do cliente**, insira os seguintes valores, dependendo da nuvem:

    - Insira **41b23e61-6c1e-4545-b367-cd054e0ed4b4** para Azure **Público**
    - Insira **51bb15d4-3a4f-4ebf-9dca-40096fe32426** para Azure **Governamental**
    - Insira **538ee9e6-310a-468d-afef-ea97365856a9** para Azure **Alemanha**
    - Insira **49f817b6-84ae-4cc0-928c-73f27289b3aa** para Azure **China 21Vianet**

8. Clique em **Adicionar aplicativo**.

    ![VPN do Azure 6](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. Copie a **ID do aplicativo (cliente)** na página **Visão geral**. Você precisará dessas informações para configurar os gateways de VPN.

    ![VPN do Azure 7](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Repita as etapas nesta seção [Registrar os aplicativos adicionais](#register-apps) para criar quantos aplicativos forem necessários de acordo com o requisito de segurança. Cada aplicativo será associado a um gateway de VPN e pode ter um conjunto diferente de usuários. Somente um aplicativo pode ser associado a um gateway.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. Atribuir usuários a aplicativos

Atribua os usuários aos aplicativos.

1. Em **Azure AD -> aplicativos Empresariais**, selecione o aplicativo recém-registrado e clique em **Propriedades**. Verificar se a **Atribuição de usuário é necessária?** está definido como **Sim**. Clique em **Save** (Salvar).

    ![VPN do Azure 8](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Na página do aplicativo, clique em **usuários e grupos** e depois em **+ Adicionar usuário**.

    ![VPN do Azure 9](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. Em **Adicionar atribuição**, clique em **Usuários e grupos**. Selecione os usuários que você deseja que acessem este aplicativo VPN. Clique em **Selecionar**.

    ![VPN do Azure 10](./media/openvpn-azure-ad-tenant-multi-app/user4.png)
