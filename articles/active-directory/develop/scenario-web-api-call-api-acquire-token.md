---
title: Obter um token para uma API Web que chama APIs da Web | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar uma API Web que chama APIs da Web que exigem a aquisição de um token para o aplicativo.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9f9758ec765ad34e5ef5d8b4d4e0a420a6701b6e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98756383"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Uma API Web que chama APIs da Web: adquirir um token para o aplicativo

Depois de criar um objeto de aplicativo cliente, use-o para adquirir um token que você pode usar para chamar uma API da Web.

## <a name="code-in-the-controller"></a>Código no controlador

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Microsoft. Identity. Web* adiciona métodos de extensão que fornecem serviços de conveniência para chamar Microsoft Graph ou uma API da Web downstream. Esses métodos são explicados em detalhes em [uma API Web que chama APIs da Web: chamar uma API](scenario-web-api-call-api-call-api.md). Com esses métodos auxiliares, você não precisa adquirir um token manualmente.

No entanto, se você quiser adquirir um token manualmente, o código a seguir mostrará um exemplo de como usar *Microsoft. Identity. Web* para fazer isso em um controlador de API. Ele chama uma API downstream chamada *ToDoList*.
Para obter um token para chamar a API downstream, insira o `ITokenAcquisition` serviço por injeção de dependência no construtor do controlador (ou no construtor da página se você usar um mais claro) e use-o em suas ações do controlador, obtendo um token para o usuário ( `GetAccessTokenForUserAsync` ) ou para o próprio aplicativo ( `GetAccessTokenForAppAsync` ) no caso de um cenário de daemon.

```csharp
[Authorize]
public class MyApiController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

     static readonly string[] scopesToAccessDownstreamApi = new string[] { "api://MyTodolistService/access_as_user" };

    private readonly ITokenAcquisition _tokenAcquisition;

    public MyApiController(ITokenAcquisition tokenAcquisition)
    {
        _tokenAcquisition = tokenAcquisition;
    }

    public IActionResult Index()
    {
        HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

        string accessToken = _tokenAcquisition.GetAccessTokenForUserAsync(scopesToAccessDownstreamApi);
        return await callTodoListService(accessToken);
    }
}
```

Para obter detalhes sobre o `callTodoListService` método, consulte  [uma API Web que chama APIs da Web: chamar uma API](scenario-web-api-call-api-call-api.md).

### <a name="java"></a>[Java](#tab/java)

Aqui está um exemplo de código que é chamado nas ações dos controladores de API. Ele chama o Microsoft Graph de API downstream.

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

### <a name="python"></a>[Python](#tab/python)
 
Uma API Web do Python requer o uso do middleware para validar o token de portador recebido do cliente. A API da Web pode obter o token de acesso para uma API downstream usando a biblioteca MSAL Python chamando o [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) método.
 
Aqui está um exemplo de código que adquire um token de acesso usando o `acquire_token_on_behalf_of` método e a estrutura Flask. Ele chama a API downstream – o ponto de extremidade de assinaturas de gerenciamento do Azure.
 
```python
def get(self):
 
        _scopes = ["https://management.azure.com/user_impersonation"]
        _azure_management_subscriptions_uri = "https://management.azure.com/subscriptions?api-version=2020-01-01"
 
        current_access_token = request.headers.get("Authorization", None)
        
        #This example only uses the default memory token cache and should not be used for production
        msal_client = msal.ConfidentialClientApplication(
                client_id=os.environ.get("CLIENT_ID"),
                authority=os.environ.get("AUTHORITY"),
                client_credential=os.environ.get("CLIENT_SECRET"))
 
        #acquire token on behalf of the user that called this API
        arm_resource_access_token = msal_client.acquire_token_on_behalf_of(
            user_assertion=current_access_token.split(' ')[1],
            scopes=_scopes
        )
 
        headers = {'Authorization': arm_resource_access_token['token_type'] + ' ' + arm_resource_access_token['access_token']}
 
        subscriptions_list = req.get(_azure_management_subscriptions_uri), headers=headers).json()
 
        return jsonify(subscriptions_list)
```

## <a name="advanced-accessing-the-signed-in-users-token-cache-from-background-apps-apis-and-services"></a>Avançadas Acessando o cache de tokens do usuário conectado de aplicativos, APIs e serviços em segundo plano

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

---

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, [chame uma API](scenario-web-api-call-api-call-api.md).
