---
title: Configurar um aplicativo Web que assina usuários | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar um aplicativo Web que faz logon de usuários (configuração de código)
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
ms.openlocfilehash: 54caea62feed6ae7c082a979901999a5dcb3bd71
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582240"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>Aplicativo Web que assina usuários: configuração de código

Saiba como configurar o código para seu aplicativo Web que faz logon em usuários.

## <a name="libraries-for-protecting-web-apps"></a>Bibliotecas para proteger aplicativos Web

<!-- This section can be in an include for web app and web APIs -->
As bibliotecas que são usadas para proteger um aplicativo Web (e uma API da Web) são:

| Plataforma | Biblioteca | Descrição |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_NET.png) | [Extensões do modelo de identidade para .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Usado diretamente pelo ASP.NET e pelo ASP.NET Core, o Microsoft Identity modelo Extensions for .NET propõe um conjunto de DLLs em execução no .NET Framework e no .NET Core. Em um aplicativo Web ASP.NET ou ASP.NET Core, você pode controlar a validação de token usando a classe **TokenValidationParameters** (em particular, em alguns cenários de parceiros). Na prática, a complexidade é encapsulada na biblioteca [Microsoft. Identity. Web](https://aka.ms/ms-identity-web) |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Suporte para aplicativos Web Java |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Suporte para aplicativos Web do Python |

Selecione a guia que corresponde à plataforma em que você está interessado:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Os trechos de código neste artigo e os seguintes são extraídos do [tutorial de ASP.NET Core incremental do aplicativo Web, capítulo 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Talvez você queira consultar este tutorial para obter detalhes completos de implementação.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Os trechos de código neste artigo e os seguintes são extraídos do [exemplo de aplicativo web ASP.net](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect).

Talvez você queira consultar este exemplo para obter detalhes completos de implementação.

# <a name="java"></a>[Java](#tab/java)

Os trechos de código neste artigo e os seguintes são extraídos do exemplo de [aplicativo Web Java chamando o Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp) no MSAL Java.

Talvez você queira consultar este exemplo para obter detalhes completos de implementação.

# <a name="python"></a>[Python](#tab/python)

Trechos de código neste artigo e os seguintes são extraídos do exemplo de [aplicativo Web Python chamando o Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) no MSAL Python.

Talvez você queira consultar este exemplo para obter detalhes completos de implementação.

---

## <a name="configuration-files"></a>Arquivos de configuração

Os aplicativos Web que conectam usuários usando a plataforma de identidade da Microsoft são configurados por meio de arquivos de configuração. Estes são os valores que você precisa especificar na configuração:

- A instância de nuvem ( `Instance` ) se você quiser que seu aplicativo seja executado em nuvens nacionais, por exemplo
- O público na ID do locatário ( `TenantId` )
- A ID do cliente ( `ClientId` ) para seu aplicativo, conforme copiado do portal do Azure

Você também pode ver referências ao `Authority` . O `Authority` valor é a concatenação dos `Instance` valores e `TenantId` .

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

No ASP.NET Core, essas configurações estão localizadas na [appsettings.jsno](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) arquivo, na seção "AzureAd".

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD audience among:
    // - "TenantId" as a GUID obtained from the Azure portal to sign in users in your organization
    // - "organizations" to sign in users in any work or school account
    // - "common" to sign in users with any work or school account or Microsoft personal account
    // - "consumers" to sign in users with a Microsoft personal account only
    "TenantId": "[Enter the tenantId here]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath": "/signout-oidc"
  }
}
```

No ASP.NET Core, outro arquivo ([properties\launchSettings.jsem](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) contém a URL ( `applicationUrl` ) e a porta TLS/SSL ( `sslPort` ) para seu aplicativo e vários perfis.

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

No portal do Azure, os URIs de redirecionamento que você registra na página de **autenticação** para seu aplicativo precisam corresponder a essas URLs. Para os dois arquivos de configuração anteriores, eles seriam `https://localhost:44321/signin-oidc` . O motivo é que `applicationUrl` é `http://localhost:3110` , mas `sslPort` é especificado (44321). `CallbackPath` é `/signin-oidc` , conforme definido em `appsettings.json` .

Da mesma forma, o URI de saída seria definido como `https://localhost:44321/signout-oidc` .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

No ASP.NET, o aplicativo é configurado por meio do arquivo de [Web.config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) , linhas 12 a 15.

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

No portal do Azure, os URIs de resposta que você registra na página de **autenticação** do seu aplicativo precisam corresponder a essas URLs. Ou seja, eles devem ser `https://localhost:44326/` .

# <a name="java"></a>[Java](#tab/java)

Em Java, a configuração está localizada no arquivo [Application. Properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) localizado em `src/main/resources` .

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

No portal do Azure, os URIs de resposta que você registra na página de **autenticação** do seu aplicativo precisam corresponder às `redirectUri` instâncias que o aplicativo define. Ou seja, eles devem ser `http://localhost:8080/msal4jsample/secure/aad` e `http://localhost:8080/msal4jsample/graph/me` .

# <a name="python"></a>[Python](#tab/python)

Aqui está o arquivo de configuração do Python em [app_config. py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py):

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> Este guia de início rápido propõe armazenar o segredo do cliente no arquivo de configuração para simplificar. Em seu aplicativo de produção, você desejaria usar outras maneiras de armazenar seu segredo, como um cofre de chaves ou uma variável de ambiente, conforme descrito na [documentação do Flask](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables).
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Código de inicialização

O código de inicialização é diferente dependendo da plataforma. Para ASP.NET Core e ASP.NET, os usuários de entrada são delegados para o middleware OpenID Connect. O modelo ASP.NET ou ASP.NET Core gera aplicativos Web para o ponto de extremidade do Azure Active Directory (Azure AD) v 1.0. Algumas configurações são necessárias para adaptá-las à plataforma Microsoft Identity. No caso do Java, ele é tratado pelo Spring com a cooperação do aplicativo.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Em ASP.NET Core aplicativos Web (e APIs Web), o aplicativo é protegido porque você tem um `[Authorize]` atributo nos controladores ou nas ações do controlador. Esse atributo verifica se o usuário está autenticado. O código que está inicializando o aplicativo está no arquivo *Startup.cs* .

Para adicionar autenticação com a plataforma de identidade da Microsoft (anteriormente Azure AD v 2.0), você precisará adicionar o código a seguir. Os comentários no código devem ser auto-explicativos.

> [!NOTE]
> Se você quiser iniciar diretamente com os novos modelos de ASP.NET Core para plataforma de identidade da Microsoft, que aproveitam o Microsoft. Identity. Web, você pode baixar um pacote NuGet de versão prévia que contém modelos de projeto para .NET Core 3,1 e .NET 5,0. Depois, uma vez instalado, você pode instanciar diretamente ASP.NET Core aplicativos Web (MVC ou mais ou mais). Consulte [modelos de projeto de aplicativo Web Microsoft. Identity. Web](https://aka.ms/ms-id-web/webapp-project-templates) para obter detalhes. Essa é a abordagem mais simples, pois fará todas as etapas abaixo para você.
>
> Se preferir iniciar seu projeto com o projeto Web padrão ASP.NET Core atual no Visual Studio ou usando `dotnet new mvc --auth SingleAuth` o ou o `dotnet new webapp --auth SingleAuth` , você verá um código semelhante ao seguinte:
>
>```c#
>  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
>          .AddAzureAD(options => Configuration.Bind("AzureAd", options));
> ```
>
> Esse código usa o pacote NuGet herdado **Microsoft. AspNetCore. Authentication. AzureAD. UI** , que é usado para criar um aplicativo do Azure ad v 1.0. Este artigo explica como criar um aplicativo da plataforma Microsoft Identity (Azure AD v 2.0) que substitui esse código.
>

1. Adicione os pacotes do NuGet [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) e [Microsoft. Identity. Web. UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) ao seu projeto. Remova o pacote NuGet Microsoft. AspNetCore. Authentication. AzureAD. UI se ele estiver presente.

2. Atualize o código no `ConfigureServices` para que ele use os `AddMicrosoftIdentityWebAppAuthentication` `AddMicrosoftIdentityUI` métodos e.

   ```c#
   public class Startup
   {
    ...
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
     services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd");

     services.AddRazorPages().AddMvcOptions(options =>
     {
      var policy = new AuthorizationPolicyBuilder()
                    .RequireAuthenticatedUser()
                    .Build();
      options.Filters.Add(new AuthorizeFilter(policy));
     }).AddMicrosoftIdentityUI();
    ```

3. No `Configure` método no *Startup.cs*, habilite a autenticação com uma chamada para `app.UseAuthentication();`

   ```c#
   // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
    // more code here
    app.UseAuthentication();
    app.UseAuthorization();
    // more code here
   }
   ```

No código acima:
- O `AddMicrosoftIdentityWebAppAuthentication` método de extensão é definido em **Microsoft. Identity. Web**. Fosse
  - Adiciona o serviço de autenticação.
  - Configura opções para ler o arquivo de configuração (aqui na seção "AzureAD")
  - Configura as opções do OpenID Connect para que a autoridade seja a plataforma de identidade da Microsoft.
  - Valida o emissor do token.
  - Garante que as declarações correspondentes ao nome sejam mapeadas a partir da `preferred_username` declaração no token de ID.

- Além do objeto de configuração, você pode especificar o nome da seção de configuração ao chamar `AddMicrosoftIdentityWebAppAuthentication` . Por padrão, é `AzureAd` .

- `AddMicrosoftIdentityWebAppAuthentication` tem outros parâmetros para cenários avançados. Por exemplo, rastrear eventos de middleware do OpenID Connect pode ajudá-lo a solucionar problemas de seu aplicativo Web se a autenticação não funcionar. Definir o parâmetro opcional `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` para `true` mostrará como as informações são processadas pelo conjunto de ASP.NET Core middleware à medida que ele progride da resposta http para a identidade do usuário no `HttpContext.User` .

- O `AddMicrosoftIdentityUI` método de extensão é definido em **Microsoft. Identity. Web. UI**. Ele fornece um controlador padrão para manipular a entrada e saída.

Você pode encontrar mais detalhes sobre como o Microsoft. Identity. Web permite que você crie aplicativos Web no <https://aka.ms/ms-id-web/webapp>

> [!WARNING]
> Atualmente, o Microsoft. Identity. Web não oferece suporte ao cenário de **contas de usuário individuais** (armazenando contas de usuário no aplicativo) ao usar o Azure ad como um provedor de logon externo. Para obter detalhes, consulte: [AzureAD/Microsoft-Identity-Web # 133](https://github.com/AzureAD/microsoft-identity-web/issues/133)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

O código relacionado à autenticação em um aplicativo Web ASP.NET e APIs Web está localizado no arquivo [App_Start/Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) .

```csharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
     // `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/.
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="java"></a>[Java](#tab/java)

O exemplo de Java usa o Spring Framework. O aplicativo é protegido porque você implementa um filtro, que intercepta cada resposta HTTP. No guia de início rápido para aplicativos Web Java, esse filtro está `AuthFilter` em `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java` .

O filtro processa o fluxo do código de autorização OAuth 2,0 e verifica se o usuário é autenticado ( `isAuthenticated()` método). Se o usuário não for autenticado, ele computará a URL dos pontos de extremidade de autorização do Azure AD e redirecionará o navegador para esse URI.

Quando a resposta chega, contendo o código de autorização, ela adquire o token usando MSAL Java. Quando ele finalmente recebe o token do ponto de extremidade do token (no URI de redirecionamento), o usuário é conectado.

Para obter detalhes, consulte o `doFilter()` método em [AuthFilter. java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java).

> [!NOTE]
> O código do `doFilter()` é escrito em uma ordem ligeiramente diferente, mas o fluxo é o descrito.

Para obter detalhes sobre o fluxo de código de autorização que esse método dispara, consulte a [plataforma de identidade da Microsoft e o fluxo de código de autorização do OAuth 2,0](v2-oauth2-auth-code-flow.md).

# <a name="python"></a>[Python](#tab/python)

O exemplo de Python usa Flask. A inicialização de Flask e MSAL Python é feita em [app. py # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>Próximas etapas

No próximo artigo, você aprenderá a disparar o logon e a saída.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Vá para o próximo artigo neste cenário, [entre e saia](./scenario-web-app-sign-user-sign-in.md?tabs=aspnetcore).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Vá para o próximo artigo neste cenário, [entre e saia](./scenario-web-app-sign-user-sign-in.md?tabs=aspnet).

# <a name="java"></a>[Java](#tab/java)

Vá para o próximo artigo neste cenário, [entre e saia](./scenario-web-app-sign-user-sign-in.md?tabs=java).

# <a name="python"></a>[Python](#tab/python)

Vá para o próximo artigo neste cenário, [entre e saia](./scenario-web-app-sign-user-sign-in.md?tabs=python).

---
