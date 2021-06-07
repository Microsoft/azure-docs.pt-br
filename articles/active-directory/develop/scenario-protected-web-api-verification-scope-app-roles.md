---
title: Verificar escopos e funções de aplicativo protegidos por API Web | Azure
titleSuffix: Microsoft identity platform
description: Verifique se a API é chamada apenas por aplicativos em nome dos usuários que têm os escopos corretos e por aplicativos de daemon que têm as funções de aplicativo corretas.
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
ms.openlocfilehash: 6d737f107e7a9b1476fdf86ac0320931ea137671
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94442898"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>API Web protegida: Verificar escopos e funções de aplicativo

Este artigo descreve como adicionar autorização à sua API Web. Essa proteção garante que a API seja chamada apenas por:

- Aplicativos em nome dos usuários que têm os escopos corretos.
- Aplicativos daemon que têm as funções de aplicativo corretas.

> [!NOTE]
> Os trechos de código neste artigo são extraídos dos seguintes exemplos de código no GitHub:
>
> - [Tutorial incremental da API Web do ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs)
> - [Amostra de API Web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/master/TodoListService/Controllers/TodoListController.cs)

Para proteger uma API Web ASP.NET ou ASP.NET Core, é preciso adicionar o atributo `[Authorize]` a um dos seguintes itens:

- O próprio controlador se você quiser que todas as ações do controlador sejam protegidas
- A ação do controlador individual para sua API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

No entanto, essa proteção não é suficiente. Ela garante apenas que ASP.NET e ASP.NET Core validem o token. Sua API precisa verificar se o token usado para chamar a API é solicitado com as declarações esperadas. Essas declarações em particular precisam de verificação:

- Os *escopos* se a API for chamada em nome de um usuário.
- As *funções de aplicativo* se a API puder ser chamada de um aplicativo daemon.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Verificar escopos em APIs chamadas em nome dos usuários

Se um aplicativo cliente chamar sua API em nome de um usuário, a API precisará solicitar um token de portador que tenha escopos específicos para a API. Para obter mais informações, confira [Configuração de código | Token de portador](scenario-protected-web-api-app-configuration.md#bearer-token).

### <a name="net-core"></a>.NET Core

#### <a name="verify-the-scopes-on-each-controller-action"></a>Verificar os escopos em cada ação do controlador

```csharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
         HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);
        // Do the work and return the result.
        // ...
    }
...
}
```

O método `VerifyUserHasAnyAcceptedScope` usa um processo semelhante a estas etapas:

- Verifique se há uma declaração chamada `http://schemas.microsoft.com/identity/claims/scope` ou `scp`.
- Verifique se a declaração tem um valor que contém o escopo esperado pela API.


#### <a name="verify-the-scopes-more-globally"></a>Verificar os escopos de forma mais global

Definir escopos granulares para sua API Web e verificar os escopos em cada ação do controlador é a abordagem recomendada. No entanto, também é possível verificar os escopos no nível do aplicativo ou de um controlador usando ASP.NET Core. Para obter detalhes, consulte [autorização baseada em declarações](/aspnet/core/security/authorization/claims) na documentação do ASP.NET Core.

### <a name="net-mvc"></a>MVC DO .NET

Para ASP.NET, basta substituir `HttpContext.User` por `ClaimsPrincipal.Current` e substituir o tipo de declaração `"http://schemas.microsoft.com/identity/claims/scope"` por `"scp"`. Veja também o snippet de código mais adiante neste artigo.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Verificar funções de aplicativo em APIs chamadas por aplicativos daemon

Se sua API Web for chamada por um [aplicativo daemon](scenario-daemon-overview.md), esse aplicativo deverá solicitar uma permissão de aplicativo para sua API Web. Conforme mostrado em [Expondo permissões de aplicativo (funções de aplicativo)](./scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles), sua API expõe essas permissões. Um exemplo é a função de aplicativo `access_as_application`.

Agora você precisa fazer com que sua API verifique se o token recebido contém a declaração `roles` e se essa declaração tem o valor esperado. O código de verificação é semelhante ao código que verifica as permissões delegadas, exceto que a ação do controlador testa funções em vez de escopos:

### <a name="aspnet-core"></a>ASP.NET Core

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        HttpContext.ValidateAppRole("access_as_application");
        ...
    }
```

O `ValidateAppRole` método é definido em Microsoft. Identity. Web em [RolesRequiredHttpContextExtensions. cs](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/Resource/RolesRequiredHttpContextExtensions.cs#L28).

### <a name="aspnet-mvc"></a>ASP.NET MVC

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found"
        });
    }
}
}
```

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Aceitar tokens somente de aplicativo caso a API Web deva ser chamada somente por aplicativos daemon

Os usuários também podem usar declarações de funções em padrões de atribuição de usuário, conforme mostrado em [Como: adicionar funções de aplicativo em seu aplicativo e recebê-las no token](howto-add-app-roles-in-azure-ad-apps.md). Se as funções puderem ser atribuídas a ambas, a verificação de funções permitirá que os aplicativos entrem como usuários e usuários entrem como aplicativos. Recomendamos que você declare diferentes funções para usuários e aplicativos para evitar essa confusão.

Se você quiser que apenas aplicativos daemon chamem sua API Web, adicione a condição de que o token é um token somente de aplicativo ao validar a função do aplicativo.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

A verificação da condição inversa permite somente aplicativos que se conectam a um usuário para chamar sua API.

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, [vá para produção](scenario-protected-web-api-production.md).
