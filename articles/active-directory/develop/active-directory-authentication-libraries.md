---
title: "Bibliotecas de Autenticação do Azure Active Directory | Microsoft Docs"
description: "A Biblioteca (ADAL) de Autenticação do AD do Azure permite que os desenvolvedores de aplicativo cliente autentiquem facilmente os usuários no Active Directory (AD) local ou em nuvem e daí obtenham tokens de acesso para proteger chamadas de API."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: mbaldwin
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/25/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: 1b79fb5b280b0cb4e087c2acde07796fd51e81fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-authentication-libraries"></a>Bibliotecas de Autenticação do Active Directory do Azure
A Biblioteca de Autenticação do Azure Active Directory (ADAL) permite que os desenvolvedores de aplicativo cliente autentiquem facilmente os usuários no Active Directory (AD) local ou em nuvem e obtenham tokens para proteger as chamadas à API. A ADAL facilita a autenticação para os desenvolvedores por meio de recursos, como:
 - Um cache de token configurável que armazena tokens de acesso e tokens de atualização
 - Atualização de token automática quando um token de acesso expira e um token de atualização está disponível
 - Suporte para chamadas de método assíncronas
 - E mais

> [!NOTE]
> Procurando as bibliotecas do Azure AD v 2.0 (MSAL)? Confira a [guia de biblioteca MSAL](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-libraries). 
> 
> 

### <a name="client-libraries"></a>Bibliotecas de cliente

| Plataforma | Biblioteca | Baixar | Código-fonte | Amostra | Referência
| --- | --- | --- | --- | --- | --- |
| Cliente .NET, Windows Store, UWP, Xamarin iOS e Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Aplicativo da área de trabalho](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-dotnet) |[Referência](https://docs.microsoft.com/dotnet/api/?view=identitymodelclientsad-3.13.9) | 
| Cliente .NET, Windows Store, Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.4) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.4) | [Aplicativo da área de trabalho](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) | | 
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Aplicativo de Página Única](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[Aplicativo iOS](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-ios) | [Referência](https://cocoapods.org/pods/ADAL)|
| Android |ADAL |[O Repositório Central](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Aplicativo Android](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | | |
| Java |ADAL4J |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Aplicativo Web Java](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-java) | |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) | | |

### <a name="server-libraries"></a>Bibliotecas do servidor 

| Plataforma | Biblioteca | Baixar | Código-fonte | Amostra | Referência
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN para AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[Aplicativo MVC](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN para OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Aplicativo Web](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [API da Web](https://docs.microsoft.com/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |
| .NET |OWIN para WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[Aplicativo Web MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Extensões do protocolo de identidade para .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Manipulador JWT para .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |

### <a name="v20-client-libraries-msal"></a>Bibliotecas de cliente v2.0 (MSAL)

O [ponto de extremidade do Azure AD v2.0](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-compare) combina Azure AD e contas Microsoft em um único ponto de extremidade. Para acessar esse ponto de extremidade, os desenvolvedores podem usar as [bibliotecas MSAL de visualização com suporte para produção](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-libraries) em vez da ADAL.

| Plataforma | Biblioteca | Baixar | Código-fonte | Amostra | Referência
| --- | --- | --- | --- | --- | --- |
| Cliente .NET, Windows Store, UWP, Xamarin iOS e Android |MSAL para .NET (versão prévia) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client/1.1.0-preview) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Aplicativo da área de trabalho](~/articles/active-directory/develop/guidedsetups/active-directory-windesktop.md) |[Referência](https://docs.microsoft.com/dotnet/api/?view=identityclient-1.1.0-preview) | 
| JavaScript |MSAL para JavaScript (versão prévia) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) | [Aplicativo de Página Única](~/articles/active-directory/develop/GuidedSetups/active-directory-javascriptspa.md) | [Referência](https://htmlpreview.github.io/?https://raw.githubusercontent.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/docs/classes/_useragentapplication_.msal.useragentapplication.html) | 
| iOS |MSAL para iOS (versão prévia) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [Aplicativo iOS](~/articles/active-directory/develop/GuidedSetups/active-directory-ios.md) | [Referência](https://azuread.github.io/docs/objc/) |
| Android |MSAL para Android (versão prévia) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Aplicativo Android](~/articles/active-directory/develop/GuidedSetups/active-directory-android.md) | [Referência](http://javadoc.io/doc/com.microsoft.identity.client/msal/0.1.1) |

## <a name="scenarios"></a>Cenários

Aqui estão os três cenários comuns nos quais a ADAL pode ser usada para autenticar um cliente que acesse um recurso remoto:  

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Autenticação de usuários de um aplicativo cliente nativo em execução em um dispositivo 

Nesse cenário, um desenvolvedor tem um aplicativo cliente do WPF, que precisa acessar um recurso remoto protegido pelo Azure AD, como uma API Web. Ele tem uma assinatura do Azure, sabe como chamar a API da Web downstream e conhece o locatário do AD do Azure que usa a API da Web. Como resultado, ele pode usar a ADAL para facilitar a autenticação com o AD do Azure, delegando completamente a experiência de autenticação para a ADAL ou tratando explicitamente as credenciais do usuário. A ADAL facilita autenticar o usuário, obter um token de acesso e um token de atualização do AD do Azure, e então, usar o token de acesso para fazer solicitações para a API da Web.

Para um exemplo de código que demonstra esse cenário usando a autenticação do AD do Azure, consulte [Aplicativo WPF de cliente nativo para API Web](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Autenticar um aplicativo cliente confidencial em execução em um servidor Web

Nesse cenário, um desenvolvedor tem um aplicativo em execução num servidor que precisa acessar um recurso remoto protegido pelo AD do Azure, como uma API da Web. Ele tem uma assinatura do Azure, sabe como chamar o serviço downstream e conhece o locatário do AD do Azure que a API da Web usa. Como resultado, ele pode usar a ADAL para facilitar a autenticação com o AD do Azure tratando explicitamente as credenciais do aplicativo. A ADAL facilita recuperar um token do AD do Azure usando a credencial do cliente do aplicativo e, em seguida, usar esse token para fazer solicitações para a API da Web. A ADAL também controla o gerenciamento da vida útil do token de acesso armazenando em cache e renovando-o conforme necessário. Para um exemplo de código que demonstre este cenário, consulte [Aplicativo de Console daemon para API Web](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Autenticar um aplicativo cliente confidencial em execução em um servidor, em nome de um usuário 

Nesse cenário, um desenvolvedor tem um aplicativo em execução num servidor que precisa acessar um recurso remoto protegido pelo AD do Azure, como uma API da Web. A solicitação também precisa ser feita em nome de um usuário do Azure AD. Ele tem uma assinatura do Azure, sabe como chamar a API da Web downstream e conhece o locatário do AD do Azure que o serviço usa. Quando o usuário é autenticado para o aplicativo Web, o aplicativo pode obter um código de autorização para o usuário do AD do Azure. O aplicativo Web pode então usar a ADAL para obter um token de acesso e atualizar o token em nome de um usuário usando o código de autorização e as credenciais de cliente associadas ao aplicativo do AD do Azure. Depois que o aplicativo Web estiver em posse do token de acesso, ele pode chamar a API da Web até o token expirar. Quando o token expirar, o aplicativo Web pode usar a ADAL para obter um novo token de acesso usando o token de atualização recebido anteriormente. Para um exemplo de código que demonstre este cenário, consulte [Cliente nativo para API Web](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Consulte também

- [Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md)
- [Cenários de autenticação do Active Directory do Azure](active-directory-authentication-scenarios.md)
- [Exemplos de código do Active Directory do Azure](active-directory-code-samples.md)
