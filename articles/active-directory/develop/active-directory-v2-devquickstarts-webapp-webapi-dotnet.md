---
title: "Introdução à API de chamada do aplicativo Web .NET v2.0 do Azure AD | Microsoft Docs"
description: "Como criar um aplicativo Web do MVC .NET que chama serviços Web usando contas pessoais da Microsoft e contas corporativas ou de estudante para conexão."
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 56be906e-71de-469d-9a5c-9fc08aae4223
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: dc3162ae8e6ce622139125c2e78fa45d2e90d534
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="calling-a-web-api-from-a-net-web-app"></a>Chamar uma API Web em um aplicativo Web .NET
Com o ponto de extremidade v2.0, você pode adicionar autenticação rapidamente a seus aplicativos Web e APIs Web com suporte a contas pessoais da Microsoft e contas corporativas ou de estudante.  Compilaremos aqui um aplicativo Web MVC que faz logon dos usuários usando o OpenID Connect, com alguma ajuda do middleware OWIN da Microsoft.  O aplicativo Web obterá tokens de acesso OAuth 2.0 para uma API Web protegida pelo OAuth 2.0 que permite criar, ler e excluir itens de uma determinado “Lista de Tarefas Pendentes” do usuário.

Este tutorial se concentrará principalmente em usar ADAL para adquirir e usar tokens de acesso em um aplicativo Web, descrito por completo [aqui](active-directory-v2-flows.md#web-apps).  Como pré-requisitos, talvez você queira aprender primeiro a [adicionar conexão básica a um aplicativo Web](active-directory-v2-devquickstarts-dotnet-web.md) ou a [proteger corretamente uma API Web](active-directory-v2-devquickstarts-dotnet-api.md).

> [!NOTE]
> Nem todos os recursos e cenários do Azure Active Directory têm suporte no ponto de extremidade v2.0.  Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="download-sample-code"></a>Baixar código de exemplo
O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet).  Para acompanhar, você pode [baixar o esqueleto do aplicativo como um .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip) ou clonar o esqueleto:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

Como alternativa, você pode [baixar o aplicativo concluído como. zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) ou clonar o aplicativo concluído:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## <a name="register-an-app"></a>Registrar um aplicativo
Crie um novo aplicativo em [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) ou siga estas [etapas detalhadas](active-directory-v2-app-registration.md).  Não se esqueça de:

* Copiar a **ID do Aplicativo** designada ao seu aplicativo, você precisará dela logo.
* Crie um **Segredo de Aplicativo** do tipo **Senha** e anote seu valor para uso posterior
* Adicionar a plataforma **Web** para seu aplicativo.
* Inserir o **URI de Redirecionamento**correto. O URI de redirecionamento indica ao Azure AD para onde as respostas de autenticação devem ser direcionadas — o padrão para este tutorial é `https://localhost:44326/`.

## <a name="install-owin"></a>Instalar a OWIN
Agora adicione os pacotes NuGet do middleware OWIN ao projeto `TodoList-WebApp` usando o Console de Gerenciador de Pacotes.  O OWIN será usado para emitir solicitações de entrada e saída, gerenciar a sessão do usuário e obter informações sobre o usuário, entre outras coisas.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

## <a name="sign-the-user-in"></a>Conectar o usuário
Configuraremos aqui o middleware OWIN para usar o [protocolo de autenticação do OpenID Connect](active-directory-v2-protocols.md).  

* Abra o arquivo `web.config` na raiz do projeto `TodoList-WebApp` e insira os valores de configuração do aplicativo na seção `<appSettings>`.
  * `ida:ClientId` é a **ID do Aplicativo** atribuída ao seu aplicativo no portal de registro.
  * O `ida:ClientSecret` é o **Segredo de Aplicativo** criado no portal de registro.
  * `ida:RedirectUri` é o **URI de Redirecionamento** inserido no portal.
* Abra o arquivo `web.config` na raiz do projeto `TodoList-Service` e substitua `ida:Audience` pela mesma **Id de Aplicativo**, como acima.
* Abra o arquivo `App_Start\Startup.Auth.cs` e adicione instruções `using` para as bibliotecas acima.
* No mesmo arquivo, implemente o método `ConfigureAuth(...)` .  Os parâmetros que você fornece em `OpenIDConnectAuthenticationOptions` servirão como coordenadas para seu aplicativo para se comunicar com o AD do Azure.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {

                    // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
                    // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                    // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

                    ClientId = clientId,
                    Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0 "),
                    Scope = "openid email profile offline_access",
                    RedirectUri = redirectUri,
                    PostLogoutRedirectUri = redirectUri,
                    TokenValidationParameters = new TokenValidationParameters
                    {
                        ValidateIssuer = false,
                    },

                    // The `AuthorizationCodeReceived` notification is used to capture and redeem the authorization_code that the v2.0 endpoint returns to your app.

                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed,
                        AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    }

        });
}
// ...
```

## <a name="use-msal-to-get-access-tokens"></a>Usar a MSAL para obter acesso a tokens
Na notificação `AuthorizationCodeReceived`, queremos usar [OAuth 2.0 em conjunto com o OpenID Connect](active-directory-v2-protocols.md) para resgatar o authorization_code de um token de acesso para o Serviço Lista de Tarefas Pendentes.  A MSAL pode facilitar esse processo para você:

* Primeiramente, instale a versão de visualização da MSAL:

```PM> Install-Package Microsoft.Identity.Client -ProjectName TodoList-WebApp -IncludePrerelease```

* E adicione outra instrução `using` ao arquivo `App_Start\Startup.Auth.cs`da MSAL.
* Agora, adicione um novo método, o manipulador de eventos `OnAuthorizationCodeReceived`.  Esse manipulador usará a MSAL para adquirir um token de acesso para a API Lista de Tarefas Pendentes e armazenará o token no cache do token da MSAL para depois:

```C#
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
        string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
        string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty);
        ClientCredential cred = new ClientCredential(clientId, clientSecret);

        // Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
        app = new ConfidentialClientApplication(Startup.clientId, redirectUri, cred, new NaiveSessionCache(userObjectId, notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase)) {};
        var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { clientId }, notification.Code);
}
// ...
```

* Em aplicativos Web, a MSAL tem um cache de token extensível que pode ser usado para armazenar tokens.  Este exemplo implementa o `NaiveSessionCache` , que usa o armazenamento de sessão http para tokens de cache.

<!-- TODO: Token Cache article -->


## <a name="call-the-web-api"></a>Chamar a API Web
Agora é hora de usar de fato o access_token que você acabou de adquirir na etapa 3.  Abra o arquivo `Controllers\TodoListController.cs` do aplicativo Web, que faz todas as solicitações CRUD à API da Lista de Tarefas Pendentes.

* Aqui, você pode usar a MSAL novamente para buscar access_tokens no cache da MSAL.  Primeiramente, adicione uma instrução `using` para MSAL a este arquivo.
  
    `using Microsoft.Identity.Client;`
* Na ação `Index`, use o método `AcquireTokenSilentAsync` da MSAL para obter um access_token que possa ser usado para ler dados no serviço Lista de Tarefas Pendentes:

```C#
// ...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
app = new ConfidentialClientApplication(Startup.clientId, redirectUri, credential, new NaiveSessionCache(userObjectID, this.HttpContext)){};
result = await app.AcquireTokenSilentAsync(new string[] { Startup.clientId });
// ...
```

* O exemplo adiciona o token resultante à solicitação HTTP GET, como o cabeçalho `Authorization`, que o serviço Lista de Tarefas Pendentes usa para autenticar a solicitação.
* Se o serviço Lista de Tarefas Pendentes retornar uma resposta `401 Unauthorized`, access_tokens na MSAL se tornarão inválidos por algum motivo.  Nesse caso, você deve descartar todos os access_token do cache da MSAL e mostrar ao usuário uma mensagem que ele pode precisar para entrar novamente, que reiniciará o fluxo de aquisição de token.

```C#
// ...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
        app.AppTokenCache.Clear(Startup.clientId);

        return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
// ...
```

* Da mesma forma, se a MSAL não puder retornar um access_token por algum motivo, você deverá orientar o usuário a entrar novamente.  Isso é tão simples quanto capturar qualquer `MSALException`:

```C#
// ...
catch (MsalException ee)
{
        // If MSAL could not get a token silently, show the user an error indicating they might need to sign in again.
        return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
// ...
```

* A mesma chamada `AcquireTokenSilentAsync` é implementada nas ações `Create` e `Delete`.  Em aplicativos Web, você pode usar esse método MSAL para obter access_tokens sempre que precisar deles em seu aplicativo.  A MSAL se encarregará da aquisição, do caching e da atualização de tokens.

Por fim, compile e execute seu aplicativo!  Entre com uma conta da Microsoft ou uma Conta do AD do Azure e observe como a identidade do usuário é refletida na barra de navegação superior.  Adicione e exclua alguns itens na Lista de Tarefas do usuário para ver as chamadas à API protegidas pelo OAuth 2.0 em ação.  Agora você tem um aplicativo Web e uma API Web, ambos protegidos por protocolos padrão do setor, que podem autenticar usuários com as respectivas contas pessoais e corporativas/de estudante.

Para referência, o exemplo concluído (sem seus valores de configuração) [é fornecido aqui](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip).  

## <a name="next-steps"></a>Próximas etapas
Para obter recursos adicionais, consulte:

* [Guia do desenvolvedor do v2.0 >>](active-directory-appmodel-v2-overview.md)
* [Marca “azure-active-directory” do StackOverflow >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Obter atualizações de segurança para nossos produtos
Recomendamos que você obtenha notificações sobre a ocorrência de incidentes de segurança visitando [esta página](https://technet.microsoft.com/security/dd252948) e assinando os alertas do Security Advisory.

