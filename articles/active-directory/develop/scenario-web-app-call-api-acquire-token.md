---
title: Obter um token em um aplicativo Web que chama APIs da Web | Azure
titleSuffix: Microsoft identity platform
description: Saiba como adquirir um token para um aplicativo Web que chama APIs da Web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: cfd479382cb69e7355b033312e165699223fdbf0
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756301"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>Um aplicativo Web que chama APIs da Web: adquirir um token para o aplicativo

Você criou o objeto de aplicativo cliente. Agora, você o usará para adquirir um token para chamar uma API da Web. No ASP.NET ou ASP.NET Core, a chamada de uma API da Web é feita no controlador:

- Obtenha um token para a API Web usando o cache de token. Para obter esse token, chame o método MSAL `AcquireTokenSilent` (ou o equivalente em Microsoft. Identity. Web).
- Chame a API protegida, passando o token de acesso para ele como um parâmetro.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Microsoft. Identity. Web* adiciona métodos de extensão que fornecem serviços de conveniência para chamar Microsoft Graph ou uma API da Web downstream. Esses métodos são explicados em detalhes em [um aplicativo Web que chama APIs da Web: chamar uma API](scenario-web-app-call-api-call-api.md). Com esses métodos auxiliares, você não precisa adquirir um token manualmente.

No entanto, se você quiser adquirir um token manualmente, o código a seguir mostrará um exemplo de como usar *Microsoft. Identity. Web* para fazer isso em um controlador doméstico. Ele chama Microsoft Graph usando a API REST (em vez do SDK do Microsoft Graph). Para obter um token para chamar a API downstream, insira o `ITokenAcquisition` serviço por injeção de dependência no construtor do controlador (ou no construtor da página se você usar o mais bem) e use-o em suas ações do controlador, obtendo um token para o usuário ( `GetAccessTokenForUserAsync` ) ou para o próprio aplicativo ( `GetAccessTokenForAppAsync` ) em um cenário de daemon.

Os métodos do controlador são protegidos por um `[Authorize]` atributo que garante que somente usuários autenticados possam usar o aplicativo Web.

```csharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions (see code below)

}
```

O `ITokenAcquisition` serviço é injetado por ASP.NET usando injeção de dependência.

Aqui está o código simplificado para a ação do `HomeController` , que obtém um token para chamar Microsoft Graph:

```csharp
[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

Para entender melhor o código necessário para esse cenário, consulte a etapa 2 ([2-1-chamadas de aplicativo Web Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) do tutorial [MS-Identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) .

O `AuthorizeForScopes` atributo na parte superior da ação do controlador (ou da página Razor, se você usar um modelo do Razor) é fornecido pelo Microsoft. Identity. Web. Ele garante que o usuário seja solicitado a fornecer consentimento, se necessário, e incrementalmente.

Há outras variações complexas, como:

- Chamar várias APIs.
- Processando o consentimento incremental e o acesso condicional.

Essas etapas avançadas são abordadas no capítulo 3 do tutorial [3-webapp-multi-APIs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

O código para ASP.NET é semelhante ao código mostrado para ASP.NET Core:

- Uma ação do controlador, protegida por um atributo [Authorize], extrai a ID do locatário e a ID de usuário do `ClaimsPrincipal` membro do controlador. (O ASP.NET usa `HttpContext.User` .)
- A partir daí, ele cria um `IConfidentialClientApplication` objeto MSAL.net.
- Por fim, ele chama o `AcquireTokenSilent` método do aplicativo cliente confidencial.
- Se a interação for necessária, o aplicativo Web precisará desafiar o usuário (entrar novamente) e solicitar mais declarações.

O trecho de código a seguir é extraído de [HomeController. cs # L157-L192](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/257c8f96ec3ff875c351d1377b36403eed942a18/WebApp/Controllers/HomeController.cs#L157-L192) no exemplo de código do [MS-Identity-ASPNET-webapp-openidconnect](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) ASP.NET MVC:

```C#
public async Task<ActionResult> ReadMail()
{
    IConfidentialClientApplication app = MsalAppBuilder.BuildConfidentialClientApplication();
    AuthenticationResult result = null;
    var account = await app.GetAccountAsync(ClaimsPrincipal.Current.GetMsalAccountId());
    string[] scopes = { "Mail.Read" };

    try
    {
        // try to get token silently
        result = await app.AcquireTokenSilent(scopes, account).ExecuteAsync().ConfigureAwait(false);
    }
    catch (MsalUiRequiredException)
    {
        ViewBag.Relogin = "true";
        return View();
    }

    // More code here
    return View();
}
```

Para obter detalhes, consulte o código para [BuildConfidentialClientApplication ()](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs) e [GetMsalAccountId](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/257c8f96ec3ff875c351d1377b36403eed942a18/WebApp/Utils/ClaimPrincipalExtension.cs#L38) no exemplo de código


# <a name="java"></a>[Java](#tab/java)

No exemplo de Java, o código que chama uma API está no método getUsersFromGraph em [AuthPageController. java # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62).

O método tenta chamar `getAuthResultBySilentFlow` . Se o usuário precisar dar consentimento a mais escopos, o código processará o `MsalInteractionRequiredException` objeto para desafiar o usuário.

```java
@RequestMapping("/msal4jsample/graph/me")
public ModelAndView getUserFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
        throws Throwable {

    IAuthenticationResult result;
    ModelAndView mav;
    try {
        result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
    } catch (ExecutionException e) {
        if (e.getCause() instanceof MsalInteractionRequiredException) {

            // If the silent call returns MsalInteractionRequired, redirect to authorization endpoint
            // so user can consent to new scopes.
            String state = UUID.randomUUID().toString();
            String nonce = UUID.randomUUID().toString();

            SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

            String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                    httpRequest.getParameter("claims"),
                    "User.Read",
                    authHelper.getRedirectUriGraph(),
                    state,
                    nonce);

            return new ModelAndView("redirect:" + authorizationCodeUrl);
        } else {

            mav = new ModelAndView("error");
            mav.addObject("error", e);
            return mav;
        }
    }

    if (result == null) {
        mav = new ModelAndView("error");
        mav.addObject("error", new Exception("AuthenticationResult not found in session."));
    } else {
        mav = new ModelAndView("auth_page");
        setAccountInfo(mav, httpRequest);

        try {
            mav.addObject("userInfo", getUserInfoFromGraph(result.accessToken()));

            return mav;
        } catch (Exception e) {
            mav = new ModelAndView("error");
            mav.addObject("error", e);
        }
    }
    return mav;
}
// Code omitted here
```

# <a name="python"></a>[Python](#tab/python)

No exemplo de Python, o código que chama Microsoft Graph está em [app. py # L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

O código tenta obter um token do cache de token. Em seguida, depois de definir o cabeçalho de autorização, ele chama a API da Web. Se não for possível obter um token, ele será autenticado pelo usuário novamente.

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, [chame uma API da Web](scenario-web-app-call-api-call-api.md).
