---
title: Exemplos de código para a plataforma de identidade da Microsoft
description: Fornece um índice de exemplos de código disponíveis da plataforma de identidade da Microsoft organizado por cenário.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 11/04/2020
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a19ca14a37bd24e499fdf0681b3510e9a4a8ea3f
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075025"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Exemplos de código da plataforma de identidade da Microsoft (ponto de extremidade v2.0)

Você pode usar a plataforma de identidade da Microsoft para:

- Adicionar autenticação e autorização para aplicativos Web e APIs da Web.
- Solicitar um token de acesso para acessar uma API da Web protegida.

Este artigo descreve brevemente e fornece links para exemplos da plataforma de identidade da Microsoft. Esses exemplos mostram como isso é feito e também fornecem snippets de código que podem ser usados em seus aplicativos. Na página de exemplo de código, você encontrará tópicos de leitura detalhados que ajudam nos requisitos, instalação e configuração. Comentários no código ajudam você a entender as seções críticas.

Para entender o cenário básico para cada tipo de exemplo, confira [Tipos de aplicativo para a plataforma de identidade da Microsoft](v2-app-types.md).

Você também pode contribuir com os exemplos no GitHub. Para saber como, consulte [Exemplos e documentação do Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Aplicativos de página única

Estes exemplos mostram como escrever um aplicativo de página única protegidos com a plataforma de identidade da Microsoft. Os exemplos usam um dos sabores do MSAL.js.

| Plataforma | Descrição | Link |
| -------- | --------------------- | -------- |
| ![Esta imagem mostra o logotipo do JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA chama o Microsoft Graph |[javascript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Esta imagem mostra o logotipo do JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA chama o Microsoft Graph usando o Fluxo de Código de Autenticação c/PKCE |[javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) |
| ![Esta imagem mostra o logotipo do JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA chama B2C |[b2c-javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Esta imagem mostra o logotipo do JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | O SPA chama o B2C usando o Fluxo de Código de Autenticação com PKCE |[b2c-javascript-spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) |
| ![Esta imagem mostra o logotipo do JavaScript](media/sample-v2-code/logo_js.png) [JavaScript (MSAL.js 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | O SPA chama a API Web personalizada que, por sua vez, chama o Microsoft Graph  | [ms-identity-javascript-tutorial-chapter4-obo](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/1-call-api-graph) |
| ![Esta imagem mostra o logotipo do Angular](media/sample-v2-code/logo_angular.png) [Angular (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA chama o Microsoft Graph  | [active-directory-javascript-singlepageapp-angular](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![Esta imagem mostra o logotipo do Angular](media/sample-v2-code/logo_angular.png) [Angular (MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA chama o Microsoft Graph usando o Fluxo de Código de Autenticação c/PKCE | [ms-identity-javascript-angular-spa](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa) |
| ![Esta imagem mostra o logotipo do Angular](media/sample-v2-code/logo_angular.png) [Angular (MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA chama API Web personalizada | [ms-identity-javascript-angular-spa-aspnetcore-webapi](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi) |
| ![Esta imagem mostra o logotipo do Angular](media/sample-v2-code/logo_angular.png) [Angular (MSAL Angular)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA chama B2C |[active-directory-b2c-javascript-angular-spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |
| ![Esta imagem mostra o logotipo do Angular](media/sample-v2-code/logo_angular.png) [Angular (MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA chama a API Web personalizada com Funções de Aplicativo e Grupos de Segurança |[ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups) |
| ![Esta imagem mostra o logotipo do React](media/sample-v2-code/logo_react.png) [React (MSAL React)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)| SPA chama o Microsoft Graph usando o Fluxo de Código de Autenticação c/PKCE | [ms-identity-javascript-react-spa](https://github.com/Azure-Samples/ms-identity-javascript-react-spa) |
| ![Esta imagem mostra o logotipo do React](media/sample-v2-code/logo_react.png) [React (MSAL React)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)| O SPA chama a API Web personalizada | [ms-identity-javascript-react-tutorial](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/1-call-api) |
| ![Esta imagem mostra o logotipo do React](media/sample-v2-code/logo_react.png) [React (MSAL.js 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)| SPA chama a API Web personalizada que, por sua vez, chama o Microsoft Graph  | [ms-identity-javascript-react-spa-dotnetcore-webapi-obo](https://github.com/Azure-Samples/ms-identity-javascript-react-spa-dotnetcore-webapi-obo) |
| ![Esta imagem mostra o logotipo do Blazor](media/sample-v2-code/logo-blazor.png) [Blazor WebAssembly (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | Tutorial do Blazor WebAssembly para conectar usuários e chamar APIs com o Azure Active Directory |[ms-identity-blazor-wasm](https://github.com/Azure-Samples/ms-identity-blazor-wasm) |

## <a name="web-applications"></a>Aplicativos Web

Os exemplos a seguir ilustram os aplicativos Web que iniciam sessão de usuários. Alguns exemplos também demonstram o aplicativo chamando o Microsoft Graph ou sua própria API Web com a identidade do usuário.

| Plataforma | Conecta apenas usuários | Conecta usuários e chama o Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Esta imagem mostra o logotipo do ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [Tutorial WebApp do ASP.NET Core conecta usuários](https://aka.ms/aspnetcore-webapp-sign-in) | Mesmo exemplo na fase [aplicativo Web do ASP.NET Core chama Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph)</p>Exemplo avançado [Acessar o cache de tokens do usuário conectado em aplicativos, APIs e serviços em segundo plano](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache) |
| ![Esta imagem mostra o logotipo do ASP.NET Framework](media/sample-v2-code/logo_NETframework.png)</p>ASP.NET Core | [Guia estratégico para desenvolvedores para migração de aplicativos do AD FS para o Azure AD](https://github.com/Azure-Samples/ms-identity-dotnet-adfs-to-aad) para saber como migrar com segurança seus aplicativos integrados ao Serviços de Federação do Active Directory (AD FS) para o Azure AD (Azure Active Directory) | |
| ![Esta imagem mostra o logotipo do ASP.NET Framework](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [Início rápido do ASP.NET](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [dotnet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> [msgraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp) |
| ![Esta imagem mostra o logotipo do Java](media/sample-v2-code/logo_java.png)  |[Tutorial do servlet Java – Capítulo 1.1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Entrar com o AAD| |
| ![Esta imagem mostra o logotipo do Java](media/sample-v2-code/logo_java.png)  |[Tutorial do servlet Java – Capítulo 1.2](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Entrar com o B2C |
| ![Esta imagem mostra o logotipo do Java](media/sample-v2-code/logo_java.png)  | | [Tutorial do servlet Java – Capítulo 2.1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Entrar com o AAD e o grafo de chamadas|
| ![Esta imagem mostra o logotipo do Java](media/sample-v2-code/logo_java.png)  |[Tutorial do servlet Java – Capítulo 3.1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Entrar com o AAD e controlar o acesso com a declaração Funções| |
| ![Esta imagem mostra o logotipo do Java](media/sample-v2-code/logo_java.png)  | | [Tutorial do servlet Java – Capítulo 3.2](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Entrar com o AAD e controlar o acesso com a declaração Grupos|
| ![Esta imagem mostra o logotipo do Java](media/sample-v2-code/logo_java.png) | |[Tutorial do servlet Java – Capítulo 4.1](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Implantar no Serviço de Aplicativo do Azure|
| ![Esta imagem mostra o logotipo do Java](media/sample-v2-code/logo_java.png)  | | [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Esta imagem mostra o logotipo do Java](media/sample-v2-code/logo_java.png)  | [ms-identity-b2c-java-servlet-webapp-authentication](https://github.com/Azure-Samples/ms-identity-b2c-java-servlet-webapp-authentication)|  |
| ![Esta imagem mostra o logotipo do Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL Node) | [Tutorial – Conectar usuários em um aplicativo Web Express](https://github.com/Azure-Samples/ms-identity-node) | |
| ![Esta imagem mostra o logotipo do Python](media/sample-v2-code/logo_python.png)  | [Tutorial do Python Flask – Capítulo 1.1](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Entrar com o AAD  |  |
| ![Esta imagem mostra o logotipo do Python](media/sample-v2-code/logo_python.png)  | [Tutorial do Python Flask – Capítulo 1.2](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Entrar com o B2C                    |  |
| ![Esta imagem mostra o logotipo do Python](media/sample-v2-code/logo_python.png)  | | [Tutorial do Python Flask – Capítulo 2.1](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Entrar com o AAD e o grafo de chamadas |
| ![Esta imagem mostra o logotipo do Python](media/sample-v2-code/logo_python.png)  | |[Tutorial do Python Flask – Capítulo 3.1](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Implantar no Serviço de Aplicativo do Azure  |
| ![Esta imagem mostra o logotipo do Python](media/sample-v2-code/logo_python.png)  | [Tutorial do Python Django – Capítulo 1.1](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) Entrar com o AAD  | |
| ![Esta imagem mostra o logotipo do Python](media/sample-v2-code/logo_python.png)  | [Tutorial do Python Django – Capítulo 1.2](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) Entrar com o B2C                    |  |
| ![Esta imagem mostra o logotipo do Python](media/sample-v2-code/logo_python.png)  | | [Tutorial do Python Django – Capítulo 2.1](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) Entrar com o AAD e o grafo de chamadas|
| ![Esta imagem mostra o logotipo do Python](media/sample-v2-code/logo_python.png)  | | [Tutorial do Python Django – Capítulo 3.1](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) Implantar no Serviço de Aplicativo do Azure                    |
| ![Esta imagem mostra o logotipo do Python](media/sample-v2-code/logo_python.png)  | | [Aplicativo Web Python Flask](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Esta imagem mostra o logotipo do Ruby](media/sample-v2-code/logo_ruby.png) |                   | [msgraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |
| ![Esta imagem mostra o logotipo do Blazor](media/sample-v2-code/logo-blazor.png)</p>Servidor Blazor | [Tutorial – Conexão de usuários no aplicativo Blazor Server](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-OIDC) | [Chamadas ao Microsoft Graph do aplicativo Blazor Server](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-graph-user/Call-MSGraph)</p>Tutorial em capítulos: [Aplicativo Blazor Server para conectar usuários e chamar APIs com o Azure Active Directory](https://github.com/Azure-Samples/ms-identity-blazor-server) |

## <a name="desktop-and-mobile-public-client-apps"></a>Aplicativos cliente públicos de desktop e móveis

Os exemplos a seguir mostram aplicativos clientes públicos (aplicativos de desktop ou móveis) que acessam a API do Microsoft Graph ou sua API Web no nome de um usuário. Além da amostra *Desktop (Console) com WAM*, todos esses aplicativos cliente usam a MSAL (Biblioteca de Autenticação da Microsoft).

| Aplicativo cliente | Plataforma | Fluxo/concessão | Chama o Microsoft Graph | Chama uma API Web do ASP.NET Core |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Tutorial da área de trabalho (.NET Core) – opcionalmente usando:</p>– o cache de token de plataforma cruzada</p>– interface do usuário da Web personalizada | ![Esta imagem mostra o logotipo do .NET/C#](media/sample-v2-code/logo_NETcore.png) | [Código de autorização](msal-authentication-flows.md#authorization-code)| [ms-identity-dotnet-desktop-tutorial](https://github.com/azure-samples/ms-identity-dotnet-desktop-tutorial) | |
| Desktop (WPF)      | ![Esta imagem mostra o logotipo do .NET desktop/C#](media/sample-v2-code/logo_NET.png) | [Código de autorização](msal-authentication-flows.md#authorization-code)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Área de trabalho (Console)   | ![Imagem que mostra o logotipo do .NET/C# (Desktop)](media/sample-v2-code/logo_NET.png) | [Autenticação Integrada do Windows](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Área de trabalho (Console)   | ![Esta imagem mostra o logotipo do Java](media/sample-v2-code/logo_java.png) | [Autenticação Integrada do Windows](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Área de trabalho (Console)   | ![Este é o logotipo do .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | [Nome de usuário/senha](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Desktop (console) com WAM  | ![Este é o logotipo do .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | Interativo com o [Gerenciador de Contas da Web](/windows/uwp/security/web-account-manager) (WAM) |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Área de trabalho (Console)   | ![Esta imagem mostra o logotipo do Java](media/sample-v2-code/logo_java.png) | [Nome de usuário/senha](msal-authentication-flows.md#usernamepassword) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Área de trabalho (Console)   | ![Esta imagem mostra o logotipo do Python](media/sample-v2-code/logo_python.png) | [Nome de usuário/senha](msal-authentication-flows.md#usernamepassword) |[ms-identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Área de trabalho (Electron)   | ![Esta imagem mostra o logotipo do Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL Node) | [Código de autorização (PKCE)](msal-authentication-flows.md#authorization-code) |[ms-identity-javascript-nodejs-desktop](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-desktop) |  |
| Móveis (Android, iOS, UWP)   | ![Esta imagem mostra o logotipo do .NET/C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | [Código de autorização](msal-authentication-flows.md#authorization-code) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobile (iOS)       | ![Esta imagem mostra o iOS/Objective-C ou o Swift](media/sample-v2-code/logo_iOS.png) | [Código de autorização](msal-authentication-flows.md#authorization-code) |[ios-swift-objc-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Desktop (macOS)       | macOS | [Código de autorização](msal-authentication-flows.md#authorization-code) |[macOS-swift-objc-native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Móvel (Android-Java)   | ![Esta imagem mostra o logotipo do Android](media/sample-v2-code/logo_Android.png) | [Código de autorização](msal-authentication-flows.md#authorization-code) |  [android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Móvel (Android-Kotlin)   | ![Esta imagem mostra o logotipo do Android](media/sample-v2-code/logo_Android.png) | [Código de autorização](msal-authentication-flows.md#authorization-code) |  [android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Aplicativos de daemon

Os exemplos a seguir mostram um aplicativo que acessa a API do Microsoft Graph com sua própria identidade (sem usuários).

| Aplicativo cliente | Plataforma | Fluxo/Concessão | Chama o Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Console | ![Esta imagem mostra o logotipo do .NET Core](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Credenciais do Cliente](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Aplicativo Web | ![Captura de tela que mostra o logotipo do ASP.NET.](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Credenciais do Cliente](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Console | ![Esta imagem mostra o logotipo do Java](media/sample-v2-code/logo_java.png) | [Credenciais do Cliente](msal-authentication-flows.md#client-credentials) | [ms-identity-java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Console | ![Esta imagem mostra o logotipo do Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL Node)| [Credenciais do Cliente](msal-authentication-flows.md#client-credentials) | [ms-identity-javascript-nodejs-console](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console) |
| Console | ![Esta imagem mostra o logotipo do Python](media/sample-v2-code/logo_python.png) | [Credenciais do Cliente](msal-authentication-flows.md#client-credentials) | [ms-identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Aplicativos sem periféricos

O exemplo a seguir mostra um aplicativo cliente público em execução em um dispositivo sem um navegador da Web. O aplicativo pode ser uma ferramenta de linha de comando, um aplicativo em execução no Linux ou no Mac ou no aplicativo IoT. O exemplo apresenta um aplicativo que acessa a API do Microsoft Graph em nome de um usuário que faz entra interativamente em outro dispositivo (por exemplo, um celular). Esse aplicativo cliente usa MSAL (Biblioteca de Autenticação da Microsoft).

| Aplicativo cliente | Plataforma | Fluxo/Concessão | Chama o Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Área de trabalho (Console)   | ![Esta imagem mostra o logotipo do .NET/C# (Desktop)](media/sample-v2-code/logo_NETcore.png) | [Fluxo de código do dispositivo](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Área de trabalho (Console)   | ![Esta imagem mostra o logotipo do Java](media/sample-v2-code/logo_java.png) | [Fluxo de código do dispositivo](msal-authentication-flows.md#device-code) |[ms-identity-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Área de trabalho (Console)   | ![Esta imagem mostra o logotipo do Python](media/sample-v2-code/logo_python.png) | [Fluxo de código do dispositivo](msal-authentication-flows.md#device-code) |[ms-identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="multi-tenant-saas-applications"></a>Aplicativos SaaS multilocatários

Os exemplos a seguir mostram como configurar o seu aplicativo para aceitar credenciais de qualquer locatário do Azure AD (Azure Active Directory). A configuração do seu aplicativo para ser *multilocatário* significa que você pode oferecer um aplicativo **SaaS** (software como serviço) para muitas organizações, permitindo que os usuários delas possam entrar no seu aplicativo depois de fornecerem o consentimento.

| Plataforma | Descrição | Link |
| -------- | --------------------- | -------- |
| ![Esta imagem mostra o logotipo do Angular](media/sample-v2-code/logo_angular.png) [Angular (MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | O SPA de multilocatário chama a API do Graph |[ms-identity-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter1) |
| ![Esta imagem mostra o logotipo do Angular](media/sample-v2-code/logo_angular.png) [Angular (MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA multilocatário chama API Web personalizada multilocatário |[ms-identity-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter2) |
| ![Esta imagem mostra o logotipo do ASP.NET Core](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | O aplicativo Web ASP.NET Core MVC chama a API do Graph |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) |
| ![Esta imagem mostra o logotipo do ASP.NET Core](media/sample-v2-code/logo_NETcore.png) [.NET Core (MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | O aplicativo Web ASP.NET Core MVC chama a API Web do ASP.NET Core |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) |

## <a name="web-apis"></a>APIs da Web

Os exemplos a seguir mostram como proteger uma API Web com a plataforma de identidade da Microsoft e como chamar uma API downstream da API Web.

| Plataforma | Amostra |
| -------- | ------------------- |
| ![Esta imagem mostra o logotipo do ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | API Web do ASP.NET Core (serviço) de [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Esta imagem mostra o logotipo do ASP.NET](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | API Web (serviço) de [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![Esta imagem mostra o logotipo do Java](media/sample-v2-code/logo_java.png) | API Web (serviço) de [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) |
| ![Esta imagem mostra o logotipo do Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| API Web (serviço) de [active-directory-javascript-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) |
| ![Esta imagem mostra o logotipo do Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| API Web de B2C (serviço) de [active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) |

## <a name="azure-functions-as-web-apis"></a>Azure Functions como APIs Web

Os exemplos a seguir mostram como proteger uma função do Azure usando HttpTrigger e expor uma API Web com a plataforma de identidade da Microsoft e como chamar uma API downstream da API Web.

| Plataforma | Amostra |
| -------- | ------------------- |
| ![Esta imagem mostra o logotipo do ASP.NET Core](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | Função do Azure da API Web do ASP.NET Core (serviço) de [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Esta imagem mostra o logotipo do Python](media/sample-v2-code/logo_python.png)</p>Python | API Web (serviço) de [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![Esta imagem mostra o logotipo do Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| API Web (serviço) de [Node.js e passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Esta imagem mostra o logotipo do Node.js](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| API Web (serviço) do [Node.js e passport-azure-ad usando em nome de](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Outros exemplos do Microsoft Graph

Para saber mais sobre [exemplos](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) e tutoriais que demonstram os diferentes padrões de uso para a API do Microsoft Graph, incluindo autenticação com o Microsoft Azure AD, consulte [Exemplos e tutoriais da Comunidade do Microsoft Graph](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Confira também

[Conceito e referência da API do Microsoft Graph](/graph/use-the-api?context=graph%2fapi%2fbeta&view=graph-rest-beta&preserve-view=true)
