---
title: Aplicativo Web que chama APIs da Web (configuração de código) – plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo Web que chama APIs da Web (configuração de código do aplicativo)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ad995908ff20d123a77b511d127652aa17c4634
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494520"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Aplicativo Web que chama as APIs da Web-configuração de código

Como visto no cenário de usuários de entrada de [aplicativo Web](scenario-web-app-sign-user-overview.md), Considerando que o logon do usuário é delegado ao middleware do Open ID Connect (OIDC), você deseja conectar-se no processo OIDC. A maneira de fazer isso é diferente dependendo da estrutura usada (aqui ASP.NET e ASP.NET Core), mas, no final, você assinará os eventos do middleware OIDC. O princípio é que:

- Você permitirá que ASP.NET ou ASP.NET Core solicite um código de autorização. Ao fazer isso, o ASP.NET/ASP.NET Core permitirá que o usuário entre e consentisse,
- Você assinará a recepção do código de autorização pelo aplicativo Web.
- Quando o código de autenticação for recebido, você usará bibliotecas MSAL para resgatar o código e os tokens de acesso resultantes e o armazenamento de tokens de atualização no cache de token. A partir daí, o cache pode ser usado em outras partes do aplicativo para adquirir outros tokens silenciosamente.

> [!NOTE]
> Os trechos de código deste artigo são extraídos dos seguintes exemplos no GitHub, que são totalmente funcionais:
>
> - [Tutorial incremental do aplicativo Web ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)
> - [Exemplo de aplicativo Web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)

## <a name="libraries-supporting-web-app-scenarios"></a>Bibliotecas que dão suporte a cenários de aplicativos Web

As bibliotecas que dão suporte ao fluxo de código de autorização para aplicativos Web são:

| Biblioteca MSAL | Descrição |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | As plataformas com suporte são .NET Framework e plataformas .NET Core (não UWP, Xamarin. iOS e Xamarin. Android, pois essas plataformas são usadas para criar aplicativos cliente públicos) |
| ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Desenvolvimento em andamento-em visualização pública |
| ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Desenvolvimento em andamento-em visualização pública |

## <a name="aspnet-core-configuration"></a>Configuração de ASP.NET Core

No ASP.NET Core, as coisas acontecem no `Startup.cs` arquivo. Você desejará assinar o `OnAuthorizationCodeReceived` evento Open ID Connect e, a partir desse evento, chame MSAL. O método `AcquireTokenFromAuthorizationCode` da rede que tem o efeito de armazenar no cache de token, o token de acesso para `scopes`o solicitado e um token de atualização que será usado para atualizar o token de acesso quando ele estiver próximo da expiração ou para obter um token em nome do mesmo usuário , mas para um recurso diferente.

```CSharp
string[] scopes = new string[]{ "user.read" };
string[] scopesRequestedByMsalNet = new string[]{ "openid", "profile", "offline_access" };
```

Os comentários no código a seguir ajudarão você a entender alguns aspectos complicados de combinando MSAL.NET e ASP.NET Core. Os detalhes completos são fornecidos no [tutorial incremental do aplicativo Web ASP.NET Core, capítulo 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)

```CSharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Response type. We ask ASP.NET to request an Auth Code, and an IDToken
   options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

   // This "offline_access" scope is needed to get a refresh token when users sign in with
   // their Microsoft personal accounts
   // (it's required by MSAL.NET and automatically provided by Azure AD when users
   // sign in with work or school accounts, but not with their Microsoft personal accounts)
   options.Scope.Add("offline_access");
   options.Scope.Add("user.read"); // for instance

   // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
   // where it will be usable from Controllers later (through the TokenAcquisition service)
   var handler = options.Events.OnAuthorizationCodeReceived;
   options.Events.OnAuthorizationCodeReceived = async context =>
   {
    // As AcquireTokenByAuthorizationCode is asynchronous we want to tell ASP.NET core
    // that we are handing the code even if it's not done yet, so that it does 
    // not concurrently call the Token endpoint.
    context.HandleCodeRedemption();

    // Call MSAL.NET AcquireTokenByAuthorizationCode
    var application = BuildConfidentialClientApplication(context.HttpContext,
                                                         context.Principal);
    var result = await application.AcquireTokenByAuthorizationCode(scopes.Except(scopesRequestedByMsalNet), context.ProtocolMessage.Code)
                                  .ExecuteAsync();

    // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it
    // and will not send the OAuth 2.0 request in case a further call to
    // AcquireTokenByAuthorizationCodeAsync in the future for incremental consent 
    // (getting a code requesting more scopes)
    // Share the ID Token so that the identity of the user is known in the application (in 
    // HttpContext.User)
    context.HandleCodeRedemption(null, result.IdToken);

    // Call the previous handler if any
    await handler(context);
   };
```

No ASP.NET Core, a criação do aplicativo cliente confidencial usa informações contidas no HttpContext. Isso `HttpContext` sabe sobre a URL para o aplicativo Web e o usuário conectado (em um `ClaimsPrincipal`). 

Ele também usa a configuração de ASP.NET Core, que tem uma seção "AzureAD" e que está associada a:

- a `_applicationOptions` estrutura de dados do tipo [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet)
- a `azureAdOptions` instância do tipo [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs) definida em ASP.NET Core `Authentication.AzureAD.UI`. Por fim, o aplicativo precisa manter os caches de token.

```CSharp
/// <summary>
/// Creates an MSAL Confidential client application
/// </summary>
/// <param name="httpContext">HttpContext associated with the OIDC response</param>
/// <param name="claimsPrincipal">Identity for the signed-in user</param>
/// <returns></returns>
private IConfidentialClientApplication BuildConfidentialClientApplication(HttpContext httpContext, ClaimsPrincipal claimsPrincipal)
{
 var request = httpContext.Request;

 // Find the URI of the application)
 string currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _applicationOptions.CallbackPath ?? string.Empty);

 // Updates the authority from the instance (including national clouds) and the tenant
 string authority = $"{azureAdOptions.Instance}{azureAdOptions.TenantId}/";

 // Instantiates the application based on the application options (including the client secret)
 var app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
               .WithRedirectUri(currentUri)
               .WithAuthority(authority)
               .Build();

 // Initialize token cache providers. In the case of Web applications, there must be one
 // token cache per user (here the key of the token cache is in the claimsPrincipal which
 // contains the identity of the signed-in user)
 if (UserTokenCacheProvider != null)
 {
  UserTokenCacheProvider.Initialize(app.UserTokenCache, httpContext, claimsPrincipal);
 }
 if (AppTokenCacheProvider != null)
 {
  AppTokenCacheProvider.Initialize(app.AppTokenCache, httpContext);
 }
 return app;
}
```

Para obter detalhes sobre os provedores de cache de token, consulte os [tutoriais do aplicativo Web ASP.NET Core | Caches de token](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/455d32f09f4f6647b066ebee583f1a708376b12f/2-WebApp-graph-user/2-2-TokenCache)

> [!NOTE]
> `AcquireTokenByAuthorizationCode`realmente resgata o código de autorização solicitado por ASP.NET e obtém os tokens que são adicionados ao cache do token do usuário MSAL.NET. A partir daí, eles são usados, nos controladores de ASP.NET Core.

## <a name="aspnet-configuration"></a>Configuração do ASP.NET

A maneira como o ASP.net lida com coisas é semelhante, exceto que a configuração de OpenIdConnect e a `OnAuthorizationCodeReceived` assinatura para o evento `App_Start\Startup.Auth.cs` ocorre no arquivo. Você encontrará conceitos semelhantes, exceto que aqui você precisará especificar o RedirectUri no arquivo de configuração, que é um pouco menos robusto:

```CSharp
private void ConfigureAuth(IAppBuilder app)
{
 app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

 app.UseCookieAuthentication(new CookieAuthenticationOptions { });

 app.UseOpenIdConnectAuthentication(
 new OpenIdConnectAuthenticationOptions
 {
  Authority = Globals.Authority,
  ClientId = Globals.ClientId,
  RedirectUri = Globals.RedirectUri,
  PostLogoutRedirectUri = Globals.RedirectUri,
  Scope = Globals.BasicSignInScopes, // a basic set of permissions for user sign in & profile access
  TokenValidationParameters = new TokenValidationParameters
  {
   NameClaimType = "name",
  },
  Notifications = new OpenIdConnectAuthenticationNotifications()
  {
   AuthorizationCodeReceived = OnAuthorizationCodeReceived,
  }
 });
}
```

```CSharp
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
{
 // Upon successful sign in, get & cache a token using MSAL
 string userId = context.AuthenticationTicket.Identity.FindFirst(ClaimTypes.NameIdentifier).Value;
 IConfidentialClientApplication clientapp;
 clientApp = ConfidentialClientApplicationBuilder.Create(Globals.ClientId)
                  .WithClientSecret(Globals.ClientSecret)
                  .WithRedirectUri(Globals.RedirectUri)
                  .WithAuthority(new Uri(Globals.Authority))
                  .Build();

  EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);

 AuthenticationResult result = await clientapp.AcquireTokenByAuthorizationCode(scopes, context.Code)
                                              .ExecuteAsync();
}
```

Finalmente, em vez de um segredo do cliente, o aplicativo cliente confidencial também pode provar sua identidade usando um certificado de cliente ou uma asserção de cliente.
Usar declarações de cliente é um cenário avançado, detalhado em [asserções de cliente](msal-net-client-assertions.md)

### <a name="msalnet-token-cache-for-a-aspnet-core-web-app"></a>Cache de token MSAL.NET para um aplicativo Web ASP.NET (Core)

Em aplicativos Web (ou APIs Web como uma questão de fato), a implementação de cache de token é diferente das implementações de cache de token de aplicativos de área de trabalho (que geralmente são [baseadas em arquivo](scenario-desktop-acquire-token.md#file-based-token-cache). Ele pode usar a sessão do ASP.NET/ASP.NET Core ou um cache Redis, ou um banco de dados, ou até mesmo o armazenamento de BLOBs do Azure. No trecho de código acima, esse é o objeto da `EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);` chamada de método, que associa um serviço de cache. Os detalhes do que acontece aqui estão além do escopo deste guia de cenário, mas os links são fornecidos abaixo.

> [!IMPORTANT]
> Uma coisa muito importante a ser observada é que, para aplicativos Web e APIs Web, deve haver um cache de token por usuário (por conta). É necessário serializar o cache de token para cada conta.

Exemplos de como usar caches de token para aplicativos Web e APIs Web estão disponíveis no [tutorial ASP.NET Core aplicativo Web](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) no [cache de token](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)da fase 2-2. Para implementações, examine a seguinte pasta [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) na biblioteca [microsoft-authentication-extensions-for-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) na pasta [ Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web).

## <a name="next-steps"></a>Próximas etapas

Neste ponto, quando o usuário entra em um token, ele é armazenado no cache de token. Vamos ver como ele é usado em outras partes do aplicativo Web.

> [!div class="nextstepaction"]
> [Entrar no aplicativo Web](scenario-web-app-call-api-sign-in.md)
