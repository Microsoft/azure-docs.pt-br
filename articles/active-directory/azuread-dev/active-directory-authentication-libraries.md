---
title: Bibliotecas de Autenticação do Azure Active Directory | Microsoft Docs
description: A Biblioteca (ADAL) de Autenticação do AD do Azure permite que os desenvolvedores de aplicativo cliente autentiquem facilmente os usuários no Active Directory (AD) local ou em nuvem e daí obtenham tokens de acesso para proteger chamadas de API.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: reference
ms.workload: identity
ms.date: 12/01/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 7b6ffd885e1be2dd59cea87cacd6e5fd5e0f8f49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861163"
---
# <a name="azure-active-directory-authentication-libraries"></a>Bibliotecas de Autenticação do Active Directory do Azure

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

A Biblioteca de Autenticação do Azure Active Directory (ADAL) v1.0 permite que os desenvolvedores de aplicativo cliente autentiquem facilmente os usuários no Active Directory (AD) local ou em nuvem e obtenham tokens para proteger as chamadas à API. A ADAL facilita a autenticação para os desenvolvedores por meio de recursos, como:

- Um cache de token configurável que armazena tokens de acesso e tokens de atualização
- Atualização de token automática quando um token de acesso expira e um token de atualização está disponível
- Suporte para chamadas de método assíncronas

> [!NOTE]
> Procurando as bibliotecas do Azure AD v 2.0 (MSAL)? Confira a [guia de biblioteca MSAL](../develop/reference-v2-libraries.md).
>
>

## <a name="microsoft-supported-client-libraries"></a>Bibliotecas de cliente com suporte da Microsoft

| Plataforma | Biblioteca | Baixar | Código-fonte | Amostra | Referência
| --- | --- | --- | --- | --- | --- |
| Cliente .NET, Windows Store, UWP, Xamarin iOS e Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Aplicativo da área de trabalho](../develop/quickstart-v2-windows-desktop.md) |[Referência](/dotnet/api/microsoft.identitymodel.clients.activedirectory) |
| JavaScript |ADAL.js |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Aplicativo de página única](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[Aplicativo iOS](../develop/quickstart-v2-ios.md) | [Referência](http://cocoadocs.org/docsets/ADAL/2.5.1/)|
| Android |ADAL |[Maven](https://search.maven.org/search?q=g:com.microsoft.aad+AND+a:adal&core=gav) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Aplicativo Android](../develop/quickstart-v2-android.md) | [JavaDocs](https://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | [Aplicativo Web Node.js](https://github.com/Azure-Samples/active-directory-node-webapp-openidconnect)|[Referência](/javascript/api/overview/azure/activedirectory) |
| Java |ADAL4J |[Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3Aadal4j%20g%3Acom.microsoft.azure) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Aplicativo Web Java](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect) |[Referência](https://javadoc.io/doc/com.microsoft.azure/adal4j) |
| Python |ADAL |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[GitHub](https://github.com/AzureAD/azure-activedirectory-library-for-python) |[Aplicativo Web do Python](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |[Referência](https://adal-python.readthedocs.io/) |

## <a name="microsoft-supported-server-libraries"></a>Bibliotecas de servidor com suporte da Microsoft

| Plataforma | Biblioteca | Baixar | Código-fonte | Amostra | Referência
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN para AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.ActiveDirectory) |[Aplicativo MVC](../develop/quickstart-v2-aspnet-webapp.md) | |
| .NET |OWIN para OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.OpenIdConnect) |[Aplicativo Web](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| .NET |OWIN para WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[GitHub](https://github.com/aspnet/AspNetKatana/tree/dev/src/Microsoft.Owin.Security.WsFederation) |[Aplicativo Web MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Extensões do protocolo de identidade para .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Manipulador JWT para .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [API da Web](../develop/authentication-flows-app-scenarios.md)| |

## <a name="scenarios"></a>Cenários

Aqui estão os três cenários comuns para usar o ADAL em um cliente que acessa um recurso remoto:

### <a name="authenticating-users-of-a-native-client-application-running-on-a-device"></a>Autenticação de usuários de um aplicativo cliente nativo em execução em um dispositivo

Nesse cenário, um desenvolvedor tem um aplicativo cliente móvel ou desktop que precisa acessar um recurso remoto, como uma API da Web. A API da Web não permite chamadas anônimas e deve ser chamada no contexto de um usuário autenticado. A API da Web é pré-configurado para confiar em tokens de acesso emitidos por um determinado locatário do Microsoft Azure AD. O Microsoft Azure Active Directory é pré-configurado para emitir tokens de acesso para esse recurso. Para chamar a API da Web do cliente, o desenvolvedor usa ADAL para facilitar a autenticação com o Microsoft Azure Active Directory. É a maneira mais segura para usar o ADAL para que ele renderize a interface do usuário para coletar credenciais de usuário (renderizadas como a janela do navegador).

O ADAL facilita a autenticação do usuário, obtém um token de acesso e um token de atualização do Microsoft Azure AD, e em seguida, usa o token de acesso para fazer solicitações para a API da Web.

Para um exemplo de código que demonstra esse cenário usando a autenticação do AD do Azure, consulte [Aplicativo WPF de cliente nativo para API Web](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-confidential-client-application-running-on-a-web-server"></a>Autenticar um aplicativo cliente confidencial em execução em um servidor Web

Nesse cenário, um desenvolvedor tem um aplicativo em execução num servidor que precisa acessar um recurso remoto, como uma API da Web. A API da Web não permite chamadas anônimas, portanto deve ser chamada de um serviço autorizado. A API da Web é pré-configurado para confiar em tokens de acesso emitidos por um determinado locatário do Microsoft Azure AD. O Microsoft Azure Active Directory é pré-configurado para emitir tokens de acesso para esse recurso para um serviço com as credenciais do cliente (ID e segredo do cliente). O ADAL facilita a autenticação do serviço com o Microsoft Azure Active Directory, retornando um token de acesso que pode ser usado para chamar a API da Web. A ADAL também controla o gerenciamento da vida útil do token de acesso armazenando em cache e renovando-o conforme necessário. Para um exemplo de código que demonstre este cenário, consulte [Aplicativo de Console daemon para API Web](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-confidential-client-application-running-on-a-server-on-behalf-of-a-user"></a>Autenticar um aplicativo cliente confidencial em execução em um servidor, em nome de um usuário

Nesse cenário, um desenvolvedor tem um aplicativo Web em execução num servidor que precisa acessar um recurso remoto, como uma API da Web. A API da Web não permite chamadas anônimas, portanto deve ser chamada de um serviço autorizado em nome de um usuário autenticado. A API da Web é pré-configurado para confiar em tokens de acesso emitidos por um locatário específico do Microsoft Azure Active Directory e o Microsoft Azure Active Directory é pré-configurado para emitir tokens de acesso para esse recurso para um serviço com as credenciais do cliente. Quando o usuário é autenticado no aplicativo Web, o aplicativo pode obter um código de autorização para o usuário do AD do Azure. O aplicativo Web pode então usar a ADAL para obter um token de acesso e atualizar o token em nome de um usuário usando o código de autorização e as credenciais de cliente associadas ao aplicativo do AD do Azure. Depois que o aplicativo Web estiver em posse do token de acesso, ele pode chamar a API da Web até o token expirar. Quando o token expirar, o aplicativo Web pode usar a ADAL para obter um novo token de acesso usando o token de atualização recebido anteriormente. Para um exemplo de código que demonstre este cenário, consulte [Cliente nativo para API Web](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="see-also"></a>Consulte Também

- [Guia do desenvolvedor do Active Directory do Azure](v1-overview.md)
- [Cenários de autenticação do Active Directory do Azure](v1-authentication-scenarios.md)
- [Exemplos de código do Active Directory do Azure](sample-v1-code.md)
