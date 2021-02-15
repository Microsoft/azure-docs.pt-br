---
title: Como proteger um aplicativo Web com logon único interativo
titleSuffix: Azure Maps
description: Como configurar um aplicativo Web que dá suporte ao logon único do Azure AD com o SDK da Web do Azure Maps usando o protocolo OpenID Connect.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: ebdc4b219e0840c18e6bef8ebfe9b8eefa8faf3b
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895556"
---
# <a name="secure-a-web-application-with-user-sign-in"></a>Proteger um aplicativo Web com entrada do usuário

O guia a seguir pertence a um aplicativo hospedado em servidores Web, mantém vários cenários de negócios e implanta em servidores Web. O aplicativo tem a necessidade de fornecer recursos protegidos protegidos somente aos usuários do Azure AD. O objetivo do cenário é habilitar o aplicativo Web para autenticar no Azure AD e chamar as APIs REST do Azure Maps em nome do usuário.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Criar um registro de aplicativo no Azure AD

Você deve criar o aplicativo Web no Azure AD para que os usuários entrem. Esse aplicativo Web delegará o acesso do usuário às APIs REST do Azure Maps.

1. Na portal do Azure, na lista de serviços do Azure, selecione **Azure Active Directory**  >  **registros de aplicativo**  >  **novo registro** .  

    > [!div class="mx-imgBorder"]
    > ![Registro do Aplicativo](./media/how-to-manage-authentication/app-registration.png)

2. Insira um **nome** , escolha um **tipo de conta de suporte** , forneça um URI de redirecionamento que representará a URL para a qual o Azure ad emitirá o token e é a URL onde o controle de mapa está hospedado. Para obter mais detalhes, consulte cenário do Azure AD [: aplicativo Web que conecta usuários](../active-directory/develop/scenario-web-app-sign-user-overview.md). Conclua as etapas fornecidas do cenário do Azure AD.  

3. Quando o registro do aplicativo for concluído, confirme se a entrada do aplicativo funciona para os usuários. Depois que a entrada funciona, o aplicativo pode receber acesso delegado às APIs REST do Azure Maps.
    
4.  Para atribuir permissões de API delegadas para mapas do Azure, vá para o aplicativo. Em seguida, selecione **permissões**  >  **de API adicionar uma permissão** . Em **APIs que minha organização usa** , pesquise e selecione **mapas do Azure** .

    > [!div class="mx-imgBorder"]
    > ![Adicionar permissões de API de aplicativo](./media/how-to-manage-authentication/app-permissions.png)

5. Marque a caixa de seleção ao lado de **acessar mapas do Azure** e, em seguida, selecione **adicionar permissões** .

    > [!div class="mx-imgBorder"]
    > ![Selecionar permissões de API de aplicativo](./media/how-to-manage-authentication/select-app-permissions.png)

6. Habilite o aplicativo Web para chamar as APIs REST do Azure Maps Configurando o registro do aplicativo com um segredo do aplicativo, para obter etapas detalhadas, consulte [um aplicativo Web que chama APIs da Web: registro de aplicativo](../active-directory/develop/scenario-web-app-call-api-app-registration.md). É necessário um segredo para autenticar o Azure AD em nome do usuário. O certificado de registro do aplicativo ou segredo deve ser armazenado em um repositório seguro para que o aplicativo Web seja recuperado para autenticar no Azure AD. 
   
   * Se o aplicativo já tiver configurado um registro de aplicativo do Azure AD e um segredo, essa etapa poderá ser ignorada.

> [!Tip]
> Se o aplicativo estiver hospedado em um ambiente do Azure, é recomendável usar [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) e uma instância de Azure Key Vault para acessar os segredos [adquirindo um token de acesso](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) para acessar Azure Key Vault segredos ou certificados. Para se conectar ao Azure Key Vault para recuperar segredos, consulte [tutorial para se conectar por meio de identidade gerenciada](../key-vault/general/tutorial-net-create-vault-azure-web-app.md).
   
7. Implemente um ponto de extremidade de token seguro para o SDK da Web do Azure Maps acessar um token. 
   
   * Para um controlador de token de exemplo, consulte exemplos do Azure AD do Azure [Maps](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/blob/master/src/OpenIdConnect/AzureMapsOpenIdConnectv1/AzureMapsOpenIdConnect/Controllers/TokenController.cs). 
   * Para uma implementação não AspNetCore ou outra, consulte [adquirir token para o aplicativo](../active-directory/develop/scenario-web-app-call-api-acquire-token.md) na documentação do Azure AD.
   * O ponto de extremidade de token protegido é responsável por retornar um token de acesso para o usuário autenticado e autorizado a chamar as APIs REST do Azure Maps.

8. Configure o controle de acesso baseado em função do Azure (RBAC do Azure) para usuários ou grupos. Consulte [conceder acesso baseado em função para usuários](#grant-role-based-access-for-users-to-azure-maps).

9. Configure a página do aplicativo Web com o SDK da Web do Azure Maps para acessar o ponto de extremidade de token seguro. 

```javascript
var map = new atlas.Map("map", {
        center: [-122.33, 47.64],
        zoom: 12,
        language: "en-US",
        authOptions: {
            authType: "anonymous",
            clientId: "<insert>",  // azure map account client id
            getToken: function (resolve, reject, map) {
                var xhttp = new XMLHttpRequest();
                xhttp.open("GET", "/api/token", true); // the url path maps to the token endpoint.
                xhttp.onreadystatechange = function () {
                    if (this.readyState === 4 && this.status === 200) {
                        resolve(this.responseText);
                    } else if (this.status !== 200) {
                        reject(this.responseText);
                    }
                };

                xhttp.send();
            }
        }
    });
    map.events.add("tokenacquired", function () {
        console.log("token acquired");
    });
    map.events.add("error", function (err) {
        console.log(JSON.stringify(err.error));
    });
```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Próximas etapas

Compreensão adicional do cenário do aplicativo Web:
> [!div class="nextstepaction"]
> [Cenário: Aplicativo Web que conecta usuários](../active-directory/develop/scenario-web-app-sign-user-overview.md)

Localize as métricas de uso da API para sua conta do Azure Maps:
> [!div class="nextstepaction"]
> [Obter métricas de uso](how-to-view-api-usage.md)

Explore os exemplos que mostram como integrar o Azure AD ao Azure Maps:
> [!div class="nextstepaction"]
> [Exemplos do aplicativo Web Azure AD do Azure Maps](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/OpenIdConnect)