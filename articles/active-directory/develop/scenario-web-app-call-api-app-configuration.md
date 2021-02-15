---
title: Configurar um aplicativo Web que chama APIs da Web | Azure
titleSuffix: Microsoft identity platform
description: Saiba como configurar o código de um aplicativo Web que chama APIs Web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 2109705116c323fd3632b7230a81ccd9158c1a64
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582308"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Um aplicativo Web que chama as APIs Web: Configuração do código

Conforme mostrado no cenário do [aplicativo Web que conecta usuários](scenario-web-app-sign-user-overview.md), o aplicativo Web usa o fluxo de código de autorização [OAuth 2.0](v2-oauth2-auth-code-flow.md) para conectar o usuário. Esse fluxo tem duas etapas:

1. Solicitar um código de autorização. Esta parte delega um diálogo particular com o usuário para a plataforma de identidade da Microsoft. Durante esse diálogo, o usuário se conecta e dá consentimento para o uso das APIs Web. Quando o diálogo particular termina com êxito, o aplicativo Web recebe um código de autorização em seu URI de redirecionamento.
1. Solicite um token de acesso para a API, resgatando o código de autorização.

O [aplicativo Web que conecta os cenários de usuários](scenario-web-app-sign-user-overview.md) abrangia apenas a primeira etapa. Aqui, você aprende como modificar seu aplicativo Web para que ele não apenas conecte os usuários, como também chame as APIs Web.

## <a name="libraries-that-support-web-app-scenarios"></a>Bibliotecas que dão suporte a cenários de aplicativo Web

As bibliotecas a seguir na Biblioteca de Autenticação da Microsoft (MSAL) dão suporte ao fluxo de código de autorização para aplicativos Web:

| Biblioteca MSAL | Descrição |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Suporte para plataformas .NET Framework e .NET Core. Não há suporte para a Plataforma Universal do Windows (UWP), Xamarin. iOS e Xamarin.Android, pois essas plataformas são usadas para criar aplicativos cliente públicos. <br/><br/>Para ASP.NET Core aplicativos Web e APIs Web, o MSAL.NET é encapsulado em uma biblioteca de nível superior chamada [Microsoft. Identity. Web](https://aka.ms/ms-identity-web). |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL para Python | Suporte para aplicativos Web do Python. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL para Java | Suporte para aplicativos Web Java. |

Selecione a guia para a plataforma em que você está interessado:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="client-secrets-or-client-certificates"></a>Segredos do cliente ou certificados de cliente

Considerando que seu aplicativo Web agora chama uma API da Web downstream, forneça um certificado do cliente ou um segredo do cliente na *appsettings.jsno* arquivo. Você também pode adicionar uma seção que especifica:

- A URL da API Web downstream
- Os escopos necessários para chamar a API

No exemplo a seguir, a `GraphBeta` seção especifica essas configurações.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-app-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 },
 "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
    }
}
```

Em vez de um segredo do cliente, você pode fornecer um certificado do cliente. O trecho de código a seguir mostra o uso de um certificado armazenado no Azure Key Vault.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-app-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
   ]
  },
  "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
  }
}
```

*O Microsoft. Identity. Web* fornece várias maneiras de descrever os certificados, tanto por configuração quanto por código. Para obter detalhes, consulte [Microsoft. Identity. Web-usando certificados](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) no github.

## <a name="startupcs"></a>Startup.cs

Seu aplicativo Web precisará adquirir um token para a API downstream. Você o especifica adicionando a `.EnableTokenAcquisitionToCallDownstreamApi()` linha após `.AddMicrosoftIdentityWebApi(Configuration)` . Essa linha expõe o `ITokenAcquisition` serviço que você pode usar nas ações do controlador e da página. No entanto, como você verá nas duas opções a seguir, isso pode ser feito mais simplesmente. Você também precisará escolher uma implementação de cache de token, por exemplo `.AddInMemoryTokenCaches()` , em *Startup.cs*:

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, Configuration.GetSection("AzureAd"))
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

Os escopos passados para `EnableTokenAcquisitionToCallDownstreamApi` são opcionais e permitem que seu aplicativo Web solicite os escopos e o consentimento do usuário para esses escopos ao fazer logon. Se você não especificar os escopos, o *Microsoft. Identity. Web* permitirá uma experiência de consentimento incremental.

Se você não quiser adquirir o token por conta própria, *o Microsoft. Identity. Web* fornece dois mecanismos para chamar uma API Web de um aplicativo Web. A opção escolhida depende se você deseja chamar Microsoft Graph ou outra API.

### <a name="option-1-call-microsoft-graph"></a>Opção 1: chamar Microsoft Graph

Se você quiser chamar Microsoft Graph, o *Microsoft. Identity. Web* permite que você use diretamente o `GraphServiceClient` (exposto pelo SDK do Microsoft Graph) em suas ações de API. Para expor Microsoft Graph:

1. Adicione o pacote NuGet [Microsoft. Identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) ao seu projeto.
1. Adicione `.AddMicrosoftGraph()` depois `.EnableTokenAcquisitionToCallDownstreamApi()` no arquivo *Startup.cs* . `.AddMicrosoftGraph()` tem várias substituições. Usando a substituição que usa uma seção de configuração como um parâmetro, o código se torna:

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, Configuration.GetSection("AzureAd"))
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
                  .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>Opção 2: chamar uma API da Web downstream diferente de Microsoft Graph

Para chamar uma API Web diferente de Microsoft Graph, *o Microsoft. Identity. Web* fornece `.AddDownstreamWebApi()` , que solicita tokens e chama a API da Web downstream.

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, "AzureAd")
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
                  .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

### <a name="summary"></a>Resumo

Assim como acontece com as APIs da Web, você pode escolher várias implementações de cache de token. Para obter detalhes, consulte [Microsoft. Identity. Web-token cache Serialization](https://aka.ms/ms-id-web/token-cache-serialization) no github.

A imagem a seguir mostra as várias possibilidades de *Microsoft. Identity. Web* e seu impacto no arquivo *Startup.cs* :

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="Diagrama de bloco mostrando opções de configuração de serviço no ponto de inicialização C S para chamar uma API Web e especificar uma implementação de cache de token":::

> [!NOTE]
> Para entender totalmente os exemplos de código aqui, familiarize-se com os [conceitos básicos de ASP.NET Core](/aspnet/core/fundamentals)e, em particular, com [Opções](/aspnet/core/fundamentals/configuration/options)e [injeção de dependência](/aspnet/core/fundamentals/dependency-injection) .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Como a entrada do usuário é delegada ao middleware do OpenID Connect (OIDC), você deve interagir com o processo OIDC. A maneira como você interage depende da estrutura usada.

Para o ASP.NET, você se inscreverá em eventos do middleware OIDC:

- Você permitirá que o ASP.NET Core solicite um código de autorização por meio do middleware do Open ID Connect. O ASP.NET ou o ASP.NET Core permitirão que o usuário entre e dê consentimento.
- Você assinará o aplicativo Web para receber o código de autorização. Essa assinatura é feita usando um C# delegado.
- Quando o código de autorização for recebido, você usará bibliotecas MSAL para resgatá-lo. Os tokens de acesso e tokens de atualização resultantes são armazenados no cache do token. O cache pode ser usado em outras partes do aplicativo, como nos controladores, para adquirir outros tokens silenciosamente.

Os exemplos de código neste artigo e o seguinte são extraídos do [exemplo de aplicativo do ASP.NET Web](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Se desejar, consulte esse exemplo para obter detalhes completos sobre a implementação.

# <a name="java"></a>[Java](#tab/java)

Os exemplos de código neste artigo e o seguinte são extraídos do [aplicativo Web Java que chama o Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp), um exemplo de aplicativo Web que usa MSAL para Java.
O exemplo atualmente permite que o MSAL para Java produza a URL de código de autorização e manipula a navegação até o ponto de extremidade de autorização para a plataforma de identidade da Microsoft. Também é possível usar a segurança do Sprint para conectar o usuário. Se desejar, consulte o exemplo para obter detalhes completos sobre a implementação.

# <a name="python"></a>[Python](#tab/python)

Os exemplos de código neste artigo e o seguinte são extraídos do [aplicativo Web Python chamando o Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), um exemplo de aplicativo Web que usa MSAL.Python.
O exemplo atualmente permite que o MSAL.Python produza a URL de código de autorização e manipule a navegação até o ponto de extremidade de autorização para a plataforma de identidade da Microsoft. Se desejar, consulte o exemplo para obter detalhes completos sobre a implementação.

---

## <a name="code-that-redeems-the-authorization-code"></a>O código que resgata o código de autorização

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

O Microsoft. Identity. Web simplifica seu código definindo as configurações corretas do OpenID Connect, inscrevendo-se no evento de código recebido e resgatando o código. Nenhum código adicional é necessário para resgatar o código de autorização. Consulte o [código-fonte Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L140) para obter detalhes sobre como isso funciona.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

O ASP.NET lida com as coisas de forma semelhante ao ASP.NET Core, exceto que a configuração do OpenID Connect e a assinatura para o evento `OnAuthorizationCodeReceived` ocorrem no arquivo [App_Start\Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs). Os conceitos também são semelhantes aos do ASP.NET Core, exceto que, no ASP.NET, você deve especificar o `RedirectUri` em [Web.config#L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Essa configuração é um pouco menos robusta do que a do ASP.NET Core, pois você precisará alterá-la ao implantar seu aplicativo.

Aqui está o código para Startup.Auth.cs:

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the query string (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0.
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // A basic set of permissions for user sign-in and profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application, you would use IssuerValidator for additional checks, such as making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }

  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign-in, get the access token and cache it by using MSAL.
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }

  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="java"></a>[Java](#tab/java)

Veja o [aplicativo Web que conecta os usuários: Codifique a configuração](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) para entender como o exemplo do Java obtém o código de autorização. Depois que o aplicativo recebe o código, o [AuthFilter.java#L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Delega para o método `AuthHelper.processAuthenticationCodeRedirect` em [AuthHelper.java#L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
1. Chama `getAuthResultByAuthCode`.

```Java
class AuthHelper {
  // Code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // Code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // Code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// Code omitted
  }
}
```

The método `getAuthResultByAuthCode` é definido em [AuthHelper.java#L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Ele cria um MSAL `ConfidentialClientApplication` e, em seguida, chama `acquireToken()` com `AuthorizationCodeParameters`, que foi criado a partir do código de autorização.

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="python"></a>[Python](#tab/python)

O fluxo do código de autorização é solicitado, conforme mostrado no [aplicativo Web que conecta os usuários: Configuração do código](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). Em seguida, o código é recebido na função `authorized`, que o Flask roteia a partir da URL `/getAToken`. Consulte o [app.py#L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) para ver o contexto completo deste código:

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD.
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here.
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

Em vez de um segredo do cliente, o aplicativo cliente confidencial também pode provar sua identidade usando um certificado de cliente ou uma asserção de cliente.
O uso de asserções de cliente é um cenário avançado, detalhado na [Declarações de cliente](msal-net-client-assertions.md).

## <a name="token-cache"></a>Cache de token

> [!IMPORTANT]
> A implementação do cache de tokens para aplicativos Web ou APIs Web é diferente da implementação para aplicativos de área de trabalho, que geralmente é [baseada em arquivos](scenario-desktop-acquire-token.md#file-based-token-cache).
> Por motivos de segurança e desempenho, é importante garantir que, para aplicativos Web e APIs Web, haja um cache de token por conta de usuário. Você deve serializar o cache de token para cada conta.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

O tutorial do ASP.NET Core usa injeção de dependência para permitir que você decida a implementação do cache de token no arquivo Startup.cs para seu aplicativo. O Microsoft.Identity.Web vem com serializadores pré-criados de cache de token descritos na [Serialização de cache de token](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application). Uma possibilidade interessante é escolher os [caches de memória distribuída](/aspnet/core/performance/caching/distributed#distributed-memory-cache) do ASP.NET Core:

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi(
                initialScopes: new string[] { "user.read" })
            .AddDistributedTokenCaches();

// Then, choose your implementation.
// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache();

// Or a Redis cache:
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache:
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Para obter detalhes sobre os provedores de cache de token, consulte também o artigo serialização de [cache de token](https://aka.ms/ms-id-web/token-cache-serialization) do Microsoft. Identity. Web, bem como os [tutoriais de ASP.NET Core aplicativo Web | Fase de caches de token](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) do tutorial de aplicativos Web.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A implementação do cache de tokens para aplicativos Web ou APIs Web é diferente da implementação para aplicativos de área de trabalho, que geralmente é [baseada em arquivos](scenario-desktop-acquire-token.md#file-based-token-cache).

A implementação do aplicativo Web pode usar a sessão ASP.NET ou a memória do servidor. Por exemplo, veja como a implementação do cache é conectada após a criação do aplicativo MSAL.NET em [MsalAppBuilder.cs#L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51):

```csharp
public static class MsalAppBuilder
{
 // Omitted code
    public static IConfidentialClientApplication BuildConfidentialClientApplication(ClaimsPrincipal currentUser)
    {
      IConfidentialClientApplication clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation.
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="java"></a>[Java](#tab/java)

O MSAL Java fornece métodos para serializar e desserializar o cache do token. O exemplo de Java manipula a serialização da sessão, conforme mostrado no método `getAuthResultBySilentFlow` em [AuthHelper. java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.Read"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  // Update session with latest token cache.
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

Os detalhes da classe `SessionManagementHelper` são fornecidos no [exemplo MSAL para Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="python"></a>[Python](#tab/python)

No exemplo do Python, é garantido um cache por conta com a recriação de um aplicativo cliente confidencial para cada solicitação e, em seguida, serializando-o no cache da sessão Flask:

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

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
```

---

## <a name="next-steps"></a>Próximas etapas

Neste ponto, quando o usuário faz logon, um token é armazenado no cache de token. Vamos ver como ele é usado em outras partes do aplicativo Web.

[Remover contas do cache na saída global](scenario-web-app-call-api-sign-in.md)
