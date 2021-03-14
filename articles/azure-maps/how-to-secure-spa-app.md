---
title: Como proteger um aplicativo de página única com entrada não interativa
titleSuffix: Azure Maps
description: Como configurar um aplicativo de página única com o controle de acesso baseado em função do Azure não interativo (RBAC do Azure) e o SDK da Web do Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 9d2af0bf731ab069a8512cb10feccf5ba18d3fa0
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092731"
---
# <a name="how-to-secure-a-single-page-application-with-non-interactive-sign-in"></a>Como proteger um aplicativo de página única com entrada não interativa

O guia a seguir pertence a um aplicativo que usa Azure Active Directory (Azure AD) para fornecer um token de acesso para os aplicativos do Azure Maps quando o usuário não consegue entrar no Azure AD. Esse fluxo requer a hospedagem de um serviço Web que deve ser protegido para ser acessado apenas pelo aplicativo Web de página única. Há várias implementações que podem realizar a autenticação no Azure AD. Este guia aproveita o produto, o Azure function para adquirir tokens de acesso.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

> [!Tip]
> O Azure Maps pode dar suporte a tokens de acesso de fluxos de logon/interatividade do usuário. Os fluxos interativos permitem um escopo mais restrito de revogação de acesso e gerenciamento de segredos.

## <a name="create-azure-function"></a>Criar Função do Azure

Crie um aplicativo de serviço Web seguro que seja responsável pela autenticação no Azure AD. 

1. Crie uma função no portal do Azure. Para obter mais informações, consulte [criar Azure function](../azure-functions/functions-get-started.md).

2. Configure a política de CORS na função do Azure para ser acessível pelo aplicativo Web de página única. Isso protegerá os clientes do navegador para as origens permitidas do seu aplicativo Web. Consulte [adicionar funcionalidade CORS](../app-service/app-service-web-tutorial-rest-api.md#add-cors-functionality).

3. [Adicione uma identidade atribuída pelo sistema](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) no Azure function para habilitar a criação de uma entidade de serviço para autenticar no Azure AD.  

4. Conceda acesso baseado em função para a identidade atribuída pelo sistema à conta do Azure Maps. Consulte [conceder acesso baseado em função](#grant-role-based-access) para obter detalhes.

5. Escreva o código para o Azure function para obter tokens de acesso do Azure Maps usando a identidade atribuída pelo sistema com um dos mecanismos com suporte ou o protocolo REST. Consulte [obter tokens para recursos do Azure](../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)

    Um exemplo de protocolo REST de exemplo:

    ```http
    GET /MSI/token?resource=https://atlas.microsoft.com/&api-version=2019-08-01 HTTP/1.1
    Host: localhost:4141
    ```

    Exemplo de resposta:

    ```http
    HTTP/1.1 200 OK
    Content-Type: application/json

    {
        "access_token": "eyJ0eXAi…",
        "expires_on": "1586984735",
        "resource": "https://atlas.microsoft.com/",
        "token_type": "Bearer",
        "client_id": "..."
    }
    ```

6. Configurar a segurança para a função HttpTrigger do Azure

   * [Criar uma chave de acesso de função](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#authorization-keys)
   * [Ponto de extremidade HTTP seguro](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production) para a função do Azure na produção.
   
7. Configurar aplicativo Web SDK do Azure Maps Web. 

    ```javascript
    //URL to custom endpoint to fetch Access token
    var url = 'https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>';

    var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                view: "Auto",
            authOptions: {
                authType: "anonymous",
                clientId: "<insert>", // azure map account client id
                getToken: function(resolve, reject, map) {
                    fetch(url).then(function(response) {
                        return response.text();
                    }).then(function(token) {
                        resolve(token);
                    });
                }
            }
        });

        // use the following events to debug, you can remove them at any time.
        map.events.add("tokenacquired", function () {
            console.log("token acquired");
        });
        map.events.add("error", function (err) {
            console.log(JSON.stringify(err.error));
        });
    ```

## <a name="grant-role-based-access"></a>Conceder acesso baseado em função

Conceda acesso ao Azure *RBAC (controle de acesso baseado em função)* atribuindo a identidade atribuída pelo sistema a uma ou mais definições de função do Azure. Para exibir as definições de função do Azure que estão disponíveis para mapas do Azure, vá para **controle de acesso (iam)**. Selecione **funções** e, em seguida, procure funções que começam com o *Azure Maps*.

1. Vá para sua **conta do Azure Maps**. Selecione atribuição **de função de controle de acesso (iam)**  >  .

    > [!div class="mx-imgBorder"]
    > ![Conceder acesso usando o RBAC do Azure](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Na guia **atribuições de função** , em **função**, selecione uma definição de função interna do Azure Maps, como **leitor de dados do Azure Maps** ou colaborador de **dados do Azure Maps**. Em **atribuir acesso a**, selecione **aplicativo de funções**. Selecione a entidade de segurança por nome. Depois, selecione **Salvar**.

   * Consulte os detalhes em [atribuir funções do Azure](../role-based-access-control/role-assignments-portal.md).

> [!WARNING]
> As definições de função internas do Azure Maps fornecem um acesso de autorização muito grande a muitas APIs REST do Azure Maps. Para restringir o acesso de APIs a um mínimo, consulte [criar uma definição de função personalizada e atribuir a identidade atribuída pelo sistema](../role-based-access-control/custom-roles.md) à definição de função personalizada. Isso habilitará o privilégio mínimo necessário para que o aplicativo acesse o Azure Maps.

## <a name="next-steps"></a>Próximas etapas

Compreensão adicional do cenário de aplicativo de página única:
> [!div class="nextstepaction"]
> [Aplicativo de página única](../active-directory/develop/scenario-spa-overview.md)

Localize as métricas de uso da API para sua conta do Azure Maps:
> [!div class="nextstepaction"]
> [Obter métricas de uso](how-to-view-api-usage.md)

Explore outros exemplos que mostram como integrar o Azure AD ao Azure Maps:
> [!div class="nextstepaction"]
> [Exemplos do Azure Maps](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/ClientGrant)