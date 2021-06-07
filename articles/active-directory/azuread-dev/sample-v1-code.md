---
title: Exemplos de código para o Azure Active Directory v1.0 | Microsoft Docs
description: Fornece um índice de exemplos de código do Azure Active Directory (ponto de extremidade v1.0), organizado por cenário.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: sample
ms.workload: identity
ms.date: 07/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 4d2b1a60856877c24d61379a688ea630e506a848
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075229"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Exemplos de código do Azure Active Directory (ponto de extremidade v1.0)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Você pode usar o Active Directory do Microsoft Azure (AD do Azure) para adicionar autenticação e autorização a aplicativos da Web e APIs da Web.

Esta seção fornece links para exemplos que você pode usar para aprender mais sobre o ponto de extremidade do Azure AD v1.0. Esses exemplos mostram como isso é feito, junt com snippets de código que podem ser usados em seus aplicativos. Na página de exemplo de código, você encontrará tópicos de leitura detalhados que ajudam nos requisitos, instalação e configuração. E o código é comentado para ajudá-lo a entender as seções críticas.

> [!NOTE]
> Se você estiver interessado nos exemplos de código do Azure AD V2, consulte [v 2.0 exemplos de código por cenário](../develop/sample-v2-code.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

Para entender o cenário básico para cada tipo de exemplo, consulte [Cenários de Autenticação do Azure AD](v1-authentication-scenarios.md).

Você também pode contribuir com nossas amostras no GitHub. Para saber como, consulte [Exemplos e documentação do Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Aplicativos de página única

Este exemplo mostra como escrever um aplicativo de página única protegido com o Azure AD.

| Plataforma | Chama sua própria API | Chama outra API da Web |
|--|--|--|
| ![Esta imagem mostra o logotipo do JavaScript](media/sample-v2-code/logo-js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
| ![Esta imagem mostra o logotipo do Angular JS](media/sample-v2-code/logo-angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi) |

## <a name="web-applications"></a>Aplicativos Web

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Aplicativos da Web, os usuários de assinatura, chamada Microsoft Graph, ou uma API da Web com a identidade do usuário

Os exemplos a seguir ilustram os usuários assinantes de aplicativos Web. Alguns desses aplicativos também chamam o Microsoft Graph, ou sua própria API Web, no nome do usuário conectado.

| Plataforma | Conecta apenas usuários | Chama o Microsoft Graph | Chama outra API da Web ASP.NET ou ASP.NET Core 2.0 |
|--|--|--|--|
| ![Esta imagem mostra o logotipo do ASP.NET Core](media/sample-v2-code/logo-netcore.png)</p>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) </p>(AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore) |
| ![Esta imagem mostra o logotipo do ASP.NET Framework](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | </p> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) </p> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)</p> (AAD Graph) |
| ![Esta imagem mostra o logotipo do Python](media/sample-v2-code/logo-python.png) |  | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi) |
| ![Esta imagem mostra o log do Java](media/sample-v2-code/logo-java.png) |  | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect) |
| ![Esta imagem mostra o logotipo do PHP](media/sample-v2-code/logo-php.png) |  | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web) |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Aplicativos da Web demonstrando o controle de acesso baseado em função (autorização)

Os exemplos a seguir mostram como implementar o RBAC (controle de acesso baseado em função). O RBAC é usado para restringir as permissões de determinados recursos em um aplicativo Web para determinados usuários. Os usuários estão autorizados dependendo se pertencerem a um **grupo do Azure AD** ou se têm uma determinada **função** do aplicativo.

| Plataforma | Amostra | Descrição |
|--|--|--|
| ![Esta imagem mostra o logotipo do ASP.NET Framework](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) </p>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Um aplicativo web .NET 4.5 MVC que usa Azure AD **papéis** de autorização |

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Aplicativos de desktop e mobile cliente público chamar Microsoft Graph ou uma API da Web

Os exemplos a seguir mostram aplicativos cliente públicos (aplicativos da área de trabalho/móvel) que acessam o Microsoft Graph ou uma API Web no nome de um usuário. Dependendo dos dispositivos e plataformas, os aplicativos podem conectar usuários de diferentes maneiras (fluxos/concessões):

- Interativamente
- Silenciosamente (com Autenticação Integrada do Windows no Windows ou nome de usuário/senha)
- Delegando uma conexão interativa a outro dispositivo (o fluxo de código do dispositivo usado em dispositivos que não fornecem controles da Web)

Aplicativo cliente | Plataforma | Fluxo/Concessão | Chama o Microsoft Graph | Chama uma API Web ASP.NET ou ASP.NET Core 2.x
------------------ | -------- | ---------- | -------------------- | -------------------------
Desktop (WPF)           | ![Esta imagem mostra o logotipo do .NET/C#](media/sample-v2-code/logo-net.png)  | Interativo | Parte do [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Móvel (UWP)            | .![Esta imagem mostra o .NET/C#/UWP](media/sample-v2-code/logo-windows.png)   | Interativo | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> Este exemplo usa [WAM](/windows/uwp/security/web-account-manager), e não [ADAL.NET](https://aka.ms/adalnet)|  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (aplicativo da UWP usando ADAL.NET para chamar uma API Web de locatário único) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (aplicativo da UWP usando ADAL.NET para chamar uma API Web de locatário múltiplo)|
Móveis (Android, iOS, UWP)   | ![Esta imagem mostra o .NET/C# (Xamarin)](media/sample-v2-code/logo-xamarin.png) | Interativo | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Mobile (Android)           | ![Esta imagem mostra o logotipo do Android](media/sample-v2-code/logo-android.png) | Interativo |   [android](https://github.com/Azure-Samples/active-directory-android) |
Mobile (iOS)           | ![Esta imagem mostra o iOS/Objective C ou Swift](media/sample-v2-code/logo-ios.png) | Interativo |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Área de trabalho (Console)          | ![Esta imagem mostra o logotipo do .NET/C#](media/sample-v2-code/logo-net.png) | Nome de usuário + senha </p>  Autenticação Integrada do Windows | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Área de trabalho (Console)          | ![Esta imagem mostra o logotipo do Java](media/sample-v2-code/logo-java.png) | Nome de usuário + senha | | [java-native-headless](https://github.com/Azure-Samples/active-directory-java-native-headless)
Área de trabalho (Console)           | ![Esta imagem mostra o logotipo do .NET Core/C#](media/sample-v2-code/logo-netcore.png) | Fluxo de código do dispositivo | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Aplicativos daemon (acessando APIs da Web com a identidade do aplicativo)

Os exemplos a seguir mostram aplicativos desktop ou Web que acessam o Microsoft Graph ou uma API da Web com a identidade do aplicativo (sem usuário).

Aplicativo cliente | Plataforma | Fluxo/Concessão | Chama uma API da Web ASP.NET ou ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- 
Aplicativo de daemon (Console)          | ![Esta imagem mostra o logotipo do .NET Framework](media/sample-v2-code/logo-netframework.png) | Credenciais do cliente com o segredo do aplicativo ou certificado | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Aplicativo de daemon (Console)         | ![Esta imagem mostra o logotipo do .NET Core](media/sample-v2-code/logo-netcore.png) | Credenciais do cliente com certificado| [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Aplicativo Web ASP.NET  | ![Esta imagem mostra o logotipo do .NET Framework](media/sample-v2-code/logo-netframework.png) | Credenciais do cliente | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>APIs da Web

### <a name="web-api-protected-by-azure-active-directory"></a>API da Web protegida pelo Active Directory do Azure

A amostra a seguir mostra como proteger uma API da Web node.js com o Azure AD.

Nas seções anteriores deste artigo, você também pode encontrar outros exemplos que ilustram um aplicativo cliente **chamando** uma **API Web** ASP.NET ou ASP.NET Core. Esses exemplos não são mencionados novamente nesta seção, mas você encontrará na última coluna das tabelas acima ou abaixo

| Plataforma | Amostra |
|--------|-------------------|
| ![Esta imagem mostra o logotipo do Node.js](media/sample-v2-code/logo-nodejs.png)  | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi) |

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>API da Web chamando o Microsoft Graph ou outra API da Web

Os exemplos a seguir demonstram uma API da web que chama outra API da web. O segundo exemplo mostra como tratar o Acesso Condicional.

| Plataforma |  Chama o Microsoft Graph | Chama outra API da Web ASP.NET ou ASP.NET Core 2.0 |
| -------- |  --------------------- | ------------------------- |
| ![Esta imagem mostra o logotipo do ASP.NET Framework](media/sample-v2-code/logo-netframework.png)</p> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) </p> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |

## <a name="other-microsoft-graph-samples"></a>Outros exemplos do Microsoft Graph

Para amostras e tutoriais que demonstram padrões de uso diferentes para a API do Microsoft Graph, incluindo a autenticação com o Azure AD, consulte [ Exemplos e tutoriais da comunidade do Microsoft Graph ](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Confira também

- [Guia do desenvolvedor do Active Directory do Azure](v1-overview.md)
- [Bibliotecas de autenticação do Azure Active Directory](active-directory-authentication-libraries.md)
- [Conceito e referência da API do Microsoft Graph](/graph/use-the-api)
