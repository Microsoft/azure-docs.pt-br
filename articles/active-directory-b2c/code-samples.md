---
title: Exemplos de código do Azure Active Directory B2C | Microsoft Docs
description: Exemplos de código para os aplicativos móveis, de desktop, Web e de página única no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/02/2020
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b09587d90024a8c376be8b0d93f7ef7b6cc51a1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008477"
---
# <a name="azure-active-directory-b2c-code-samples"></a>Exemplos de código do Azure Active Directory B2C

As tabelas a seguir fornecem links para exemplos de aplicativos, incluindo iOS, Android, .NET e Node. js.

## <a name="mobile-and-desktop-apps"></a>Aplicativos de desktop e móveis

| Amostra | Descrição |
|--------| ----------- |
| [ios-swift-native-msal](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal) | Um exemplo de iOS no Swift que autentica os usuários do Azure AD B2C e chama uma API usando OAuth 2.0 |
| [android-native-msal](https://github.com/Azure-Samples/ms-identity-android-java#b2cmodefragment-class) | Um aplicativo simples para Android mostrando como usar MSAL para autenticar os usuários por meio do Azure Active Directory B2C e acessar uma API Web com os tokens resultantes. |
| [ios-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-ios-native-appauth) | Um exemplo que mostra como você pode usar uma biblioteca de terceiros para criar um aplicativo iOS em Objective-C que autentica usuários da identidade da Microsoft ao nosso serviço de identidade do Azure AD B2C. |
| [android-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-android-native-appauth) | Um exemplo que mostra como você pode usar uma biblioteca de terceiros para criar um aplicativo Android que autentica usuários da identidade da Microsoft ao nosso serviço de identidade B2C e chama uma API Web usando tokens de acesso OAuth 2.0. |
| [dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) | Um exemplo que mostra como um aplicativo .NET de área de trabalho do Windows (WPF) pode entrar em um usuário usando o Azure AD B2C, obter um token de acesso usando MSAL.NET e chamar uma API. |
| [xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Um aplicativo Xamarin Forms simples para Android mostrando como usar MSAL para autenticar os usuários por meio do Azure Active Directory B2C e acessar uma API Web com os tokens resultantes. |

## <a name="web-apps-and-apis"></a>APIs e aplicativos Web

| Amostra | Descrição |
|--------| ----------- |
| [dotnet-webapp-and-webapi](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) | Um exemplo combinado para um aplicativo Web .NET que chama uma API Web .NET, ambos protegidos usando o Azure AD B2C. |
| [dotnetcore-webapp-openidconnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C) | Um aplicativo Web ASP.NET Core que usa o OpenID Connect para conectar usuários no Azure AD B2C. |
| [dotnetcore-webapp-msal-api](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C) | Um aplicativo Web ASP.NET Core que pode entrar em um usuário usando o Azure AD B2C, obter um token de acesso usando MSAL.NET e chamar uma API. |
| [openidconnect-nodejs](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS) | Um aplicativo Node.js que fornece uma maneira rápida e fácil de configurar um aplicativo Web com o Express usando o OpenID Connect. |
| [javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | Uma pequena API WEB Node.js do Azure AD B2C que mostra como proteger sua API Web e aceita tokens de acesso B2C usando passport.js. |
| [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/README_B2C.md) | Demonstre como integrar o B2C da plataforma de identidade da Microsoft a um aplicativo Web Python.  |

## <a name="single-page-apps"></a>Aplicativos de página única

| Amostra | Descrição |
|--------| ----------- |
| [ms-identity-javascript-react-tutorial](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/2-call-api-b2c) | Um SPA (aplicativo de página única) que chama uma API Web. A autenticação é feita com o Azure AD B2C usando o React da MSAL. Este exemplo usa o fluxo de código de autorização com PKCE. |
| [ms-identity-b2c-javascript-spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) | Um SPA (aplicativo de página única) que chama uma API Web. A autenticação é feita com o Azure AD B2C usando o MSAL.js. Este exemplo usa o fluxo de código de autorização com PKCE. |
| [javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) | Um SPA (aplicativo de página única) que chama uma API Web. A autenticação é feita com o Azure AD B2C usando o MSAL.js. Este exemplo usa o fluxo implícito.|
| [javascript-nodejs-management](https://github.com/Azure-Samples/ms-identity-b2c-javascript-nodejs-management/tree/main/Chapter1) | Um SPA (aplicativo de página única) que chama o Microsoft Graph para gerenciar usuários em um diretório B2C. A autenticação é feita com o Azure AD B2C usando o MSAL.js. Este exemplo usa o fluxo de código de autorização com PKCE.|

## <a name="consoledaemon-apps"></a>Aplicativos de console/daemon

| Amostra | Descrição |
|--------| ----------- |
| [javascript-nodejs-management](https://github.com/Azure-Samples/ms-identity-b2c-javascript-nodejs-management/tree/main/Chapter2) | Um aplicativo de daemon de console Node.js e Express que chama o Microsoft Graph com sua identidade para gerenciar usuários em um diretório B2C. A autenticação é feita com o Azure AD B2C usando o Nó da MSAL. Este exemplo usa o fluxo de código de autorização.|
| [dotnetcore-b2c-account-management](https://github.com/Azure-Samples/ms-identity-dotnetcore-b2c-account-management) | Um aplicativo de console .NET Core que chama o Microsoft Graph com sua identidade para gerenciar usuários em um diretório B2C. A autenticação é feita com o Azure AD B2C usando MSAL.NET. Este exemplo usa o fluxo de código de autorização.|

## <a name="saml-test-application"></a>Aplicativo de teste do SAML

| Amostra | Descrição |
|--------| ----------- |
| [saml-sp-tester](https://github.com/azure-ad-b2c/saml-sp-tester/tree/master/source-code) | Aplicativo de teste do SAML para testar o Azure AD B2C configurado para atuar como provedor de identidade do SAML. |

## <a name="api-connectors"></a>Conectores de API

As tabelas a seguir fornecem links para exemplos de código para aproveitar as APIs Web em seus fluxos dos usuários usando [conectores de API](api-connectors-overview.md).

### <a name="azure-function-quickstarts"></a>Guias de início rápido da Função do Azure

| Amostra                                                                                                                          | Descrição                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Este exemplo de Função do Azure do .NET Core demonstra como limitar as inscrições a domínios de email específicos e validar as informações fornecidas pelo usuário. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Este exemplo de Função do Azure do Node.js demonstra como limitar as inscrições a domínios de email específicos e validar as informações fornecidas pelo usuário.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Este exemplo de Função do Azure do Python demonstra como limitar os inscrições a domínios de email específicos e validar as informações fornecidas pelo usuário.    |


### <a name="automated-fraud-protection-services--captcha"></a>Serviços automatizados de proteção contra fraudes e CAPTCHA
| Amostra                                                                                                            | Descrição                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Proteção contra fraude e abuso da Arkose Labs](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-arkose) | Este exemplo mostra como proteger as inscrições de seus usuários usando o serviço de proteção contra fraude e abuso da Arkose Labs. |
| [reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) | Este exemplo mostra como proteger as inscrições de seus usuários usando um desafio reCAPTCHA para evitar abuso automatizado. |


### <a name="identity-verification"></a>Verificação de identidade

| Amostra                                                                                                            | Descrição                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | Esse exemplo mostra como verificar uma identidade de usuário como parte dos seus fluxos de inscrição usando um conector de API para fazer a integração ao IDology. |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | Este exemplo mostra como verificar uma identidade de usuário como parte dos seus fluxos de inscrição usando um conector de API para fazer a integração ao Experian. |


### <a name="other"></a>Outro

| Amostra                                                                                                            | Descrição                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [Código de convite](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-invitation-code) | Este exemplo demonstra como limitar a inscrição para públicos-alvo específicos usando códigos de convite.|
| [Exemplos da comunidade do conector de API](https://github.com/azure-ad-b2c/api-connector-samples) | Este repositório tem exemplos de cenários mantidos pela comunidade habilitados por conectores de API.|
