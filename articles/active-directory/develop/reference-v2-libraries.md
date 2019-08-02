---
title: Bibliotecas de autenticação da plataforma de identidade da Microsoft | Microsoft Docs
description: Bibliotecas de cliente compatíveis e bibliotecas de middleware de servidor, juntamente com links de exemplo, de biblioteca e de origem relacionados, para o ponto de extremidade da plataforma Microsoft Identity.
services: active-directory
documentationcenter: ''
author: negoe
manager: CelesteDG
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/25/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 418ad3cc059b86df1c96a62c3979a0c5e5de2a61
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494535"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Bibliotecas de autenticação da plataforma de identidade da Microsoft

O [ponto de extremidade da plataforma de identidade da Microsoft](active-directory-v2-compare.md) dá suporte aos protocolos OAuth 2,0 padrão do setor e OpenID Connect 1,0. A MSAL (biblioteca de autenticação da Microsoft) foi projetada para trabalhar com o ponto de extremidade da plataforma de identidade da Microsoft. Você também pode usar bibliotecas de código-fonte aberto que dão suporte a OAuth 2,0 e a OpenID Connect 1,0.

Recomendamos que você use bibliotecas escritas por especialistas de domínio de protocolo que seguem uma metodologia de SDL (ciclo de vida de desenvolvimento de segurança). Essas bibliotecas incluem [aquela que a Microsoft segue][Microsoft-SDL]. Se você tiver código à mão para os protocolos, deverá seguir uma metodologia como o Microsoft SDL. Preste muita atenção às considerações de segurança nas especificações de padrões para cada protocolo.

> [!NOTE]
> Você está procurando pela ADAL (biblioteca de autenticação Azure Active Directory)? Confira o [guia da biblioteca Adal](active-directory-authentication-libraries.md).

## <a name="types-of-libraries"></a>Tipos de bibliotecas

O ponto de extremidade da plataforma de identidade da Microsoft funciona com dois tipos de bibliotecas:

* **Bibliotecas de cliente**: Clientes e servidores nativos usam bibliotecas de cliente para adquirir tokens de acesso para chamar um recurso, como o Microsoft Graph.
* **Bibliotecas de middleware de servidor**: Os aplicativos Web usam bibliotecas de middleware de servidor para entrada do usuário. As APIs da Web usam bibliotecas de middleware de servidor para validar tokens enviados por clientes nativos ou por outros servidores.

## <a name="library-support"></a>Suporte à biblioteca

As bibliotecas vêm em duas categorias de suporte:

* **Com suporte da Microsoft**: A Microsoft fornece correções para essas bibliotecas e fez a devida diligência do SDL nessas bibliotecas.
* **Compatível**: A Microsoft testou essas bibliotecas em cenários básicos e confirmou que elas funcionam com o ponto de extremidade da plataforma Microsoft Identity. A Microsoft não fornece correções para essas bibliotecas e não fez uma análise dessas bibliotecas. Os problemas e solicitações de recursos devem ser direcionados ao projeto de software livre da biblioteca.

Para obter uma lista de bibliotecas que funcionam com o ponto de extremidade da plataforma Microsoft Identity, consulte as seções a seguir.

## <a name="microsoft-supported-client-libraries"></a>Bibliotecas de cliente com suporte da Microsoft

Use bibliotecas de autenticação de cliente para adquirir um token para chamar uma API Web protegida.

| Plataforma | Biblioteca | Baixar | Código-fonte | Amostra | Referência | Documentação conceitual | Roteiro |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [Aplicativo de página única](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Referência](https://htmlpreview.github.io/?https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core/docs/classes/_useragentapplication_.useragentapplication.html) | [Documentos conceituais](msal-overview.md)| [Roteiro](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
|![Angular JS](media/sample-v2-code/logo_angular.png) | MSAL Angular JS | [NPM](https://www.npmjs.com/package/@azure/msal-angularjs) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
![Angular](media/sample-v2-code/logo_angular.png) | MSAL angular (versão prévia) | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Aplicativo da área de trabalho](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[Documentos conceituais](msal-overview.md) | [Roteiro](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL Python (versão prévia) | [PyPI](https://pypi.org/project/msal) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [Amostras](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [ReadTheDocs](https://msal-python.rtfd.io/) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [Roteiro](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL Java (versão prévia) | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [Amostras](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | [Roteiro](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| ![iOS / Objective C ou swift](media/sample-v2-code/logo_iOS.png) | MSAL obj_c (versão prévia) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [Aplicativo iOS](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki) | |
|![Android / Java](media/sample-v2-code/logo_Android.png) | MSAL (versão prévia) | [Repositório central](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplicativo Android](quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.identity.client/msal) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki) |

## <a name="microsoft-supported-server-middleware-libraries"></a>Bibliotecas de middleware de servidor com suporte da Microsoft

Use bibliotecas de middleware para ajudar a proteger aplicativos Web e APIs Web. Aplicativos Web ou APIs Web escritos com ASP.NET ou ASP.NET Core usam as bibliotecas de middleware.

| Plataforma | Biblioteca | Baixar | Código-fonte | Amostra | Referência
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | Segurança do ASP.NET |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[Aplicativo MVC](quickstart-v2-aspnet-webapp.md) |[Referência da API do ASP.NET](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| Extensões de IdentityModel para .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [Aplicativo MVC](quickstart-v2-aspnet-webapp.md) |[Referência](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Aplicativo Web](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="microsoft-supported-libraries-by-os--language"></a>Bibliotecas com suporte da Microsoft por sistema operacional/idioma

Em termos de sistemas operacionais com suporte vs idiomas, o mapeamento é o seguinte:

|             | ![Windows](media/sample-v2-code/logo_windows.png) Windows    | Linux      | macOS      | ![iOS](media/sample-v2-code/logo_iOS.png)        | ![Android / Java](media/sample-v2-code/logo_Android.png) Android    |
|-------------|------------|------------|------------|------------|------------|
| ![JavaScript](media/sample-v2-code/logo_js.png)  |  MSAL.js | MSAL.js | MSAL.js | MSAL.js |  MSAL.js |
| <img alt="C#" src="../../cognitive-services/speech-service/media/index/logo_csharp.svg" width="64px" height="64px" /> | ASP.NET, ASP.NET Core, MSAL.Net (.NET FW, Core, UWP)| ASP.NET Core, MSAL.Net (.NET Core) | ASP.NET Core, MSAL.Net (MacOS)       | MSAL.Net (Xamarin. iOS) | MSAL.Net (Xamarin. Android)|
| <img alt="Objective C" src="../../cognitive-services/speech-service/media/index/logo_objc.jpg" width="64px" height="64px" /> |            |            |        | MSAL.iOS |            |
| ![Java](media/sample-v2-code/logo_java.png) Java       |        |        |       | | MSAL.Android
| ![Java](media/sample-v2-code/logo_java.png) Java       | msal4j       | msal4j       | msal4j      | |
| ![Python](media/sample-v2-code/logo_python.png) Python     | MSAL Python | MSAL Python | MSAL Python |
| ![Node. js](media/sample-v2-code/logo_nodejs.png) Node.JS     | Passport. Node | Passport. Node | Passport. Node |

Veja também [cenários por plataformas e linguagens com suporte](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="compatible-client-libraries"></a>Bibliotecas de cliente compatíveis

| Plataforma | Nome da biblioteca | Versão testada | Código-fonte | Amostra |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | 1\.13.5 da versão |[Hello.js](https://github.com/MrSwitch/hello.js) |[SPA](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Scribe Java](https://github.com/scribejava/scribejava) | [Versão 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Biblioteca Gluu OpenID Connect](https://github.com/GluuFederation/oxAuth) | [Versão 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Biblioteca Gluu OpenID Connect](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Solicitações-OAuthlib](https://github.com/requests/requests-oauthlib) | [Versão 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Solicitações-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [openid-client](https://github.com/panva/node-openid-client) | [2.4.5 da versão](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [openid-client](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [The PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [Versão 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth: 1.3.1<br />omniauth-oauth2: 1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| ![iOS](media/sample-v2-code/logo_iOS.png) ![Android](media/sample-v2-code/logo_Android.png) | [Reagir à autenticação de aplicativo nativo](https://github.com/FormidableLabs/react-native-app-auth) | [4.2.0 da versão](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [Reagir à autenticação de aplicativo nativo](https://github.com/FormidableLabs/react-native-app-auth) | |

Para qualquer biblioteca compatível com padrões, você pode usar o ponto de extremidade da plataforma de identidade da Microsoft. É importante saber para onde ir para obter suporte:

* Para problemas e solicitações de novos recursos no código de biblioteca, entre em contato com o proprietário da biblioteca.
* Para problemas e solicitações de novos recursos na implementação do protocolo do lado do serviço, entre em contato com a Microsoft.
* [Arquivo uma solicitação de recurso](https://feedback.azure.com/forums/169401-azure-active-directory) para recursos adicionais que você deseja ver no protocolo.
* [Crie uma solicitação de suporte](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) se você encontrar um problema em que o ponto de extremidade da plataforma de identidade da Microsoft não está em conformidade com o OAuth 2,0 ou o OpenID Connect 1,0.

## <a name="related-content"></a>Conteúdo relacionado

Para obter mais informações sobre o ponto de extremidade da plataforma Microsoft Identity, consulte a [visão geral da plataforma Microsoft Identity][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: https://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
