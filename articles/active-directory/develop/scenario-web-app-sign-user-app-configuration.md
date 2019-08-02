---
title: Aplicativo Web que conecta usuários (configuração de código)-plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo Web que faz logon de usuários (configuração de código)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c962e95b3d213c4089b51f58139cab17a3332cbd
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853062"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Aplicativo Web que faz logon de usuários – configuração de código

Saiba como configurar o código para seu aplicativo Web que faz logon de usuários.

## <a name="libraries-used-to-protect-web-apps"></a>Bibliotecas usadas para proteger aplicativos Web

<!-- This section can be in an include for Web App and Web APIs -->
As bibliotecas usadas para proteger um aplicativo Web (e uma API da Web) são:

| Plataforma | Biblioteca | DESCRIÇÃO |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Extensões do modelo de identidade para .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Usado diretamente por ASP.NET e ASP.NET Core, o Microsoft Identity Extensions for .NET propõe um conjunto de DLLs em execução no .NET Framework e no .NET Core. De um aplicativo Web ASP.NET/ASP.NET Core, você pode controlar a validação de token usando a classe **TokenValidationParameters** (em particular em alguns cenários de ISV) |

## <a name="aspnet-core-configuration"></a>Configuração de ASP.NET Core

Os trechos de código neste artigo e os seguintes são extraídos do [tutorial de ASP.NET Core incremental do aplicativo Web, capítulo 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg). Talvez você queira consultar esse tutorial para obter detalhes completos de implementação.

### <a name="application-configuration-files"></a>Arquivos de configuração do aplicativo

No ASP.NET Core, um aplicativo Web que faz logon em usuários com a plataforma Microsoft Identity é configurado por `appsettings.json` meio do arquivo. As configurações que você precisa preencher são:

- a nuvem `Instance` se você quiser que seu aplicativo seja executado em nuvens nacionais
- o público em`tenantId`
- o `clientId` para seu aplicativo, conforme copiado do portal do Azure.

```JSon
{
  "AzureAd": {
    // Azure Cloud instance among:
    // "https://login.microsoftonline.com/" for Azure Public cloud.
    // "https://login.microsoftonline.us/" for Azure US government.
    // "https://login.microsoftonline.de/" for Azure AD Germany
    // "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD Audience among:
    // - the tenant Id as a GUID obtained from the azure portal to sign-in users in your organization
    // - "organizations" to sign-in users in any work or school accounts
    // - "common" to sign-in users with any work and school account or Microsoft personal account
    // - "consumers" to sign-in users with Microsoft personal account only
    "TenantId": "[Enter the tenantId here]]",

    // Client Id (Application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

Em ASP.NET Core, há outro arquivo que contém a URL (`applicationUrl`) e a porta SSL (`sslPort`) para seu aplicativo, bem como vários perfis.

```JSon
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

No portal do Azure, os URIs de resposta que você precisa registrar na página de **autenticação** do seu aplicativo precisam corresponder a essas URLs; ou seja, para os dois arquivos de configuração acima, eles seriam `https://localhost:44321/signin-oidc` como o applicationUrl é `http://localhost:3110` , mas `sslPort` o é especificado (44321) e o `CallbackPath` é `/signin-oidc` definido no `appsettings.json`.
  
Da mesma forma, o URI de saída seria definido como `https://localhost:44321/signout-callback-oidc`.

### <a name="initialization-code"></a>Código de inicialização

Em ASP.NET Core aplicativos Web (e APIs Web), o código que faz a inicialização do aplicativo está localizado `Startup.cs` no arquivo e, para adicionar autenticação com a plataforma de identidade da Microsoft (anteriormente Azure AD) v 2.0, você precisará adicionar o código a seguir. Os comentários no código devem ser auto-explicativos.

  > [!NOTE]
  > Se você iniciar o projeto com `dotnet new mvc` o projeto Web padrão ASP.NET Core no Visual Studio ou se o método `AddAzureAD` estiver disponível por padrão, pois os pacotes relacionados serão carregados automaticamente. No entanto, se você criar um projeto do zero e estiver tentando usar o código abaixo, sugerimos que você adicione o pacote NuGet **"Microsoft. AspNetCore. Authentication. AzureAD. UI"** ao seu projeto `AddAzureAD` para tornar o método disponível.
  
```CSharp
 services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
         .AddAzureAD(options => configuration.Bind("AzureAd", options));

 services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
 {
  // The ASP.NET core templates are currently using Azure AD v1.0, and compute
  // the authority (as {Instance}/{TenantID}). We want to use the Microsoft Identity Platform v2.0 endpoint
  options.Authority = options.Authority + "/v2.0/";

  // If you want to restrict the users that can sign-in to specific organizations
  // Set the tenant value in the appsettings.json file to 'organizations', and add the
  // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection.
  // Otherwise validate the issuer
  options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ForAadInstance(options.Authority).ValidateAadIssuer;

  // Set the nameClaimType to be preferred_username.
  // This change is needed because certain token claims from Azure AD v1.0 endpoint
  // (on which the original .NET core template is based) are different in Azure AD v2.0 endpoint.
  // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
  // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
  options.TokenValidationParameters.NameClaimType = "preferred_username";
  ...
```

## <a name="aspnet-configuration"></a>Configuração do ASP.NET

No ASP.net, o aplicativo é configurado por meio `Web.Config` do arquivo

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, please visit
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

O código relacionado à autenticação no aplicativo Web/APIs Web do ASP.net está localizado no `App_Start/Startup.Auth.cs` arquivo.

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
     // The `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Entrar e sair](scenario-web-app-sign-user-sign-in.md)
