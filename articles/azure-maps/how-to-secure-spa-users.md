---
title: Como proteger um aplicativo de página única com entrada do usuário
titleSuffix: Azure Maps
description: Como configurar um aplicativo de página única que dá suporte ao logon único do Azure AD com o SDK da Web do Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 60d8dc45fb26ea210b1827a6938716474faa0304
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895606"
---
# <a name="secure-a-single-page-application-with-user-sign-in"></a>Proteger um aplicativo de página única com entrada do usuário

O guia a seguir pertence a um aplicativo hospedado em um servidor de conteúdo ou tem dependências de servidor Web mínimas. O aplicativo fornece recursos protegidos protegidos somente para usuários do Azure AD. O objetivo do cenário é habilitar o aplicativo Web para autenticar no Azure AD e chamar as APIs REST do Azure Maps em nome do usuário.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Criar um registro de aplicativo no Azure AD

Crie o aplicativo Web no Azure AD para que os usuários entrem. O aplicativo Web delega o acesso do usuário às APIs REST do Azure Maps.

1. Na portal do Azure, na lista de serviços do Azure, selecione **Azure Active Directory**  >  **registros de aplicativo**  >  **novo registro**.  

    > [!div class="mx-imgBorder"]
    > ![Registro do Aplicativo](./media/how-to-manage-authentication/app-registration.png)

2. Insira um **nome**, escolha um **tipo de conta de suporte**, forneça um URI de redirecionamento que representará a URL para a qual o Azure ad emitirá o token e é a URL onde o controle de mapa está hospedado. Para obter um exemplo detalhado, confira exemplos do Azure [AD do Azure Maps](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant). Em seguida, selecione **Registrar**.  

3. Para atribuir permissões de API delegadas para mapas do Azure, vá para o aplicativo. Em **registros de aplicativo**, selecione **permissões**  >  **de API adicionar uma permissão**. Em **APIs que minha organização usa**, pesquise e selecione **mapas do Azure**.

    > [!div class="mx-imgBorder"]
    > ![Adicionar permissões de API de aplicativo](./media/how-to-manage-authentication/app-permissions.png)

4. Marque a caixa de seleção ao lado de **acessar mapas do Azure** e, em seguida, selecione **adicionar permissões**.

    > [!div class="mx-imgBorder"]
    > ![Selecionar permissões de API de aplicativo](./media/how-to-manage-authentication/select-app-permissions.png)

5. Habilitar `oauth2AllowImplicitFlow`. Para habilitá-lo, na seção **manifesto** do registro do aplicativo, defina `oauth2AllowImplicitFlow` como `true` .

6. Copie a ID do aplicativo do Azure AD e a ID de locatário do Azure AD do registro do aplicativo para usar no SDK da Web. Adicione os detalhes de registro do aplicativo do Azure AD e o `x-ms-client-id` da conta do Azure MAP ao SDK da Web.

    ```javascript
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js" />
        <script>
            var map = new atlas.Map("map", {
                center: [-122.33, 47.64],
                zoom: 12,
                language: "en-US",
                authOptions: {
                    authType: "aad",
                    clientId: "<insert>",  // azure map account client id
                    aadAppId: "<insert>",  // azure ad app registration id
                    aadTenant: "<insert>", // azure ad tenant id
                    aadInstance: "https://login.microsoftonline.com/"
                }
            });
        </script>   
    ```

7. Configure o controle de acesso baseado em função do Azure (RBAC do Azure) para usuários ou grupos. Consulte as [seções a seguir para habilitar o RBAC do Azure](#grant-role-based-access-for-users-to-azure-maps).
   
[!INCLUDE [grant role access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Próximas etapas

Compreensão adicional do cenário de aplicativo de página única:
> [!div class="nextstepaction"]
> [Aplicativo de página única](../active-directory/develop/scenario-spa-overview.md)

Localize as métricas de uso da API para sua conta do Azure Maps:
> [!div class="nextstepaction"]
> [Obter métricas de uso](how-to-view-api-usage.md)

Explore os exemplos que mostram como integrar o Azure AD ao Azure Maps:
> [!div class="nextstepaction"]
> [Exemplos do Azure Maps](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ImplicitGrant)