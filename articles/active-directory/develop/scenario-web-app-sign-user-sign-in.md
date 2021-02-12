---
title: Gravar um aplicativo Web que se conecta aos usuários | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo Web que entra/sai de usuários
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: f8fa5532a5664741c9ddb9b78b35d5eed8e2e4e0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937844"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>Aplicativo Web que conecta usuários: entrar e sair

Saiba como adicionar entrada ao código para seu aplicativo Web que faz logon em usuários. Em seguida, saiba como deixá-las sair.

## <a name="sign-in"></a>Conexão

A entrada consiste em duas partes:

- O botão de entrada na página HTML
- A ação de entrada no code-behind no controlador

### <a name="sign-in-button"></a>Botão de entrada

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

No ASP.NET Core, para aplicativos da plataforma Microsoft Identity, o botão **entrar** é exposto no `Views\Shared\_LoginPartial.cshtml` (para um aplicativo MVC) ou `Pages\Shared\_LoginPartial.cshtm` (para um aplicativo Razor). Ele é exibido somente quando o usuário não é autenticado. Ou seja, ele é exibido quando o usuário ainda não tiver entrado ou tiver se desconectado. Por contrário, o botão **sair** é exibido quando o usuário já está conectado. Observe que o controlador de conta está definido no pacote NuGet **Microsoft. Identity. Web. UI** , na área chamada **MicrosoftIdentity**

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

No ASP.NET MVC, o botão de saída é exposto no `Views\Shared\_LoginPartial.cshtml` . Ele é exibido somente quando há uma conta autenticada. Ou seja, ele é exibido quando o usuário entrou anteriormente.

```html
@if (Request.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="java"></a>[Java](#tab/java)

Em nosso início rápido do Java, o botão entrar está localizado no arquivo [main/resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/resources/templates/index.html) .

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>HomePage</title>
</head>
<body>
<h3>Home Page</h3>

<form action="/msal4jsample/secure/aad">
    <input type="submit" value="Login">
</form>

</body>
</html>
```

# <a name="python"></a>[Python](#tab/python)

No início rápido do Python, não há nenhum botão de conexão. O code-behind solicita automaticamente que o usuário entre quando está atingindo a raiz do aplicativo Web. Consulte [app. py # L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn` ação do controlador

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

No ASP.NET, a seleção do botão de **entrada** no aplicativo Web dispara a `SignIn` ação no `AccountController` controlador. Nas versões anteriores dos modelos do ASP.NET Core, o `Account` controlador foi inserido com o aplicativo Web. Esse não é mais o caso porque o controlador agora faz parte do pacote NuGet **Microsoft. Identity. Web. UI** . Consulte [AccountController.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) para obter detalhes.

Esse controlador também lida com os aplicativos Azure AD B2C.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

No ASP.NET, sair é disparado do `SignOut()` método em um controlador (por exemplo, [AccountController. cs # L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Esse método não faz parte da estrutura ASP.NET (ao contrário do que acontece em ASP.NET Core). Ele envia um desafio de logon de OpenID depois de propor um URI de redirecionamento.

```csharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Em Java, a saída é tratada chamando o ponto de extremidade da plataforma Microsoft Identity `logout` diretamente e fornecendo o `post_logout_redirect_uri` valor. Para obter detalhes, consulte [AuthPageController. java # L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48).

```Java
@Controller
public class AuthPageController {

    @Autowired
    AuthHelper authHelper;

    @RequestMapping("/msal4jsample")
    public String homepage(){
        return "index";
    }

    @RequestMapping("/msal4jsample/secure/aad")
    public ModelAndView securePage(HttpServletRequest httpRequest) throws ParseException {
        ModelAndView mav = new ModelAndView("auth_page");

        setAccountInfo(mav, httpRequest);

        return mav;
    }

    // More code omitted for simplicity
```

# <a name="python"></a>[Python](#tab/python)

Ao contrário de outras plataformas, o Python MSAL cuida de permitir que o usuário entre na página de logon. Consulte [app. py # L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28).

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use an empty list [] to just sign in
                           # Here we choose to also collect user consent up front
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

O `_build_msal_app()` método é definido em [app. py # L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) da seguinte maneira:

```Python
def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)

def _get_token_from_cache(scope=None):
    cache = _load_cache()  # This web app maintains one cache per session
    cca = _build_msal_app(cache)
    accounts = cca.get_accounts()
    if accounts:  # So all accounts belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

Depois que o usuário tiver entrado em seu aplicativo, você desejará habilitá-los para sair.

## <a name="sign-out"></a>Sair

Sair de um aplicativo Web envolve mais do que remover as informações sobre a conta conectada do estado do aplicativo Web.
O aplicativo Web também deve redirecionar o usuário para o ponto de extremidade da plataforma Microsoft Identity `logout` para sair.

Quando seu aplicativo Web redireciona o usuário para o ponto de `logout` extremidade, esse ponto de extremidade limpa a sessão do usuário no navegador. Se seu aplicativo não for para o `logout` ponto de extremidade, o usuário será autenticado novamente em seu aplicativo sem inserir suas credenciais novamente. O motivo é que eles terão uma sessão de logon único válida com a plataforma de identidade da Microsoft.

Para saber mais, consulte a seção [Enviar uma solicitação de saída](v2-protocols-oidc.md#send-a-sign-out-request) na plataforma de [identidade da Microsoft e a documentação do protocolo OpenID Connect](v2-protocols-oidc.md) .

### <a name="application-registration"></a>Registro de aplicativo

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Durante o registro do aplicativo, você registra uma URL de logout de front-Channel. Em nosso tutorial, você registrou `https://localhost:44321/signout-oidc` no campo **URL de logoff de front-Channel** na página de **autenticação** . Para obter detalhes, consulte [registrar o aplicativo webApp](scenario-web-app-sign-user-app-registration.md#register-an-app-by-using-the-azure-portal).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Durante o registro do aplicativo, você não precisa registrar uma URL extra de logoff de front-Channel. O aplicativo será chamado de volta em sua URL principal. 

# <a name="java"></a>[Java](#tab/java)

Nenhuma URL de logoff de front-Channel é necessária no registro do aplicativo.

# <a name="python"></a>[Python](#tab/python)

Durante o registro do aplicativo, você não precisa registrar uma URL extra de logoff de front-Channel. O aplicativo será chamado de volta em sua URL principal.

---

### <a name="sign-out-button"></a>Botão de saída

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

No ASP.NET, a seleção do botão **sair** no aplicativo Web dispara a `SignOut` ação no `AccountController` controlador (veja abaixo)

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

No ASP.NET MVC, o botão de saída é exposto no `Views\Shared\_LoginPartial.cshtml` . Ele é exibido somente quando há uma conta autenticada. Ou seja, ele é exibido quando o usuário entrou anteriormente.

```html
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="java"></a>[Java](#tab/java)

Em nosso início rápido do Java, o botão de saída está localizado no arquivo main/resources/templates/auth_page.html.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="python"></a>[Python](#tab/python)

No início rápido do Python, o botão de saída está localizado no arquivo [templates/index.html # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) .

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python web app</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`SignOut` ação do controlador

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Nas versões anteriores dos modelos do ASP.NET Core, o `Account` controlador foi inserido com o aplicativo Web. Esse não é mais o caso porque o controlador agora faz parte do pacote NuGet **Microsoft. Identity. Web. UI** . Consulte [AccountController.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) para obter detalhes.

- Define um URI de redirecionamento de OpenID para para `/Account/SignedOut` que o controlador seja chamado de volta quando o Azure ad tiver concluído a saída.
- Chamadas `Signout()` , que permitem ao middleware OpenID Connect contatar o ponto de extremidade da plataforma Microsoft Identity `logout` . Em seguida, o ponto de extremidade:

  - Limpa o cookie de sessão do navegador.
  - Chama novamente o URI de redirecionamento pós-logout. Por padrão, o URI de redirecionamento pós-logout exibe a página de exibição desconectada [SignedOut.cshtml.cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Pages/Account/SignedOut.cshtml.cs). Essa página também é fornecida como parte do Microsoft. Identity. Web.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

No ASP.NET, sair é disparado do `SignOut()` método em um controlador (por exemplo, [AccountController. cs # L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Esse método não faz parte da estrutura ASP.NET, ao contrário do que acontece em ASP.NET Core. Fosse

- Envia um desafio de desconexão de OpenID.
- Limpa o cache.
- Redireciona para a página que deseja.

```csharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 Response.Redirect("/");
}
```

# <a name="java"></a>[Java](#tab/java)

Em Java, a saída é tratada chamando o ponto de extremidade da plataforma Microsoft Identity `logout` diretamente e fornecendo o `post_logout_redirect_uri` valor. Para obter detalhes, consulte [AuthPageController. java # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60).

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="python"></a>[Python](#tab/python)

O código que desconecta o usuário está em [app. py # L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48).

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>Interceptando a chamada para o `logout` ponto de extremidade

O URI de pós-logout permite que os aplicativos participem da saída global.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

O middleware ASP.NET Core OpenID Connect permite que seu aplicativo intercepte a chamada para o ponto de extremidade da plataforma Microsoft Identity `logout` fornecendo um evento OpenID Connect chamado `OnRedirectToIdentityProviderForSignOut` . Isso é manipulado automaticamente pelo Microsoft. Identity. Web (que limpa contas no caso em que seu aplicativo Web chama APIs Web)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

No ASP.NET, você delega ao middleware para executar a saída, limpando o cookie de sessão:

```csharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="java"></a>[Java](#tab/java)

No início rápido do Java, o URI de redirecionamento pós-Logout apenas exibe a página index.html.

# <a name="python"></a>[Python](#tab/python)

No início rápido do Python, o URI de redirecionamento pós-Logout apenas exibe a página index.html.

---

## <a name="protocol"></a>Protocolo

Se você quiser saber mais sobre a saída, leia a documentação do protocolo disponível em [Open ID Connect](./v2-protocols-oidc.md).

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo neste cenário, [vá para produção](scenario-web-app-sign-user-production.md).