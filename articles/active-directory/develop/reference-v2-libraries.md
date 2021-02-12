---
title: Bibliotecas de autenticação da plataforma de identidade da Microsoft | Azure
description: Lista de bibliotecas de cliente e middleware compatível com a plataforma de identidade da Microsoft. Use essas bibliotecas para adicionar suporte para entrada de usuário (autenticação) e acesso à API Web protegido (autorização) para seus aplicativos.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 01/29/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 9549ebab687400e32bbc68a2c76cf8efc8c106c8
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218258"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Bibliotecas de autenticação da plataforma de identidade da Microsoft

As tabelas a seguir mostram o suporte da biblioteca de autenticação da Microsoft para vários tipos de aplicativos. Eles incluem links para o código-fonte da biblioteca, onde obter o pacote para o projeto do aplicativo e se a biblioteca dá suporte à entrada do usuário (autenticação), acesso a APIs da Web protegidas (autorização) ou ambos.

A plataforma de identidade da Microsoft foi certificada pelo OpenID Foundation como um [provedor de OpenID certificado](https://openid.net/certification/). Se você preferir usar uma biblioteca diferente da MSAL (biblioteca de autenticação da Microsoft) ou outra biblioteca com suporte da Microsoft, escolha uma com uma [implementação certificada do OpenID Connect](https://openid.net/developers/certified/).

Se você optar por codificar manualmente sua própria implementação de nível de protocolo do [OAuth 2,0 ou do OpenID connect 1,0](active-directory-v2-protocols.md), preste atenção às considerações de segurança na especificação de cada padrão e siga uma metodologia de SDL (ciclo de vida de desenvolvimento de software) como o [SDL da Microsoft][Microsoft-SDL].

## <a name="single-page-application-spa"></a>Aplicativo de página única (SPA)

Um aplicativo de página única é executado inteiramente na superfície do navegador e busca dados da página (HTML, CSS e JavaScript) dinamicamente ou em tempo de carregamento do aplicativo. Ele pode chamar APIs Web para interagir com fontes de dados back-end.

Como o código de uma SPA é executado inteiramente no navegador, ele é considerado um *cliente público* que não pode armazenar segredos com segurança.

| Idioma/estrutura | Projeto em<br/>GitHub                                                                                                    | Pacote                                                                      | Introdução<br/>iniciado                             | Conectar usuários                                         | Acessar APIs Web                                                 | Disponibilidade geral (GA) *ou*<br/>Visualização pública<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL angular 2,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)         | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | Versão prévia pública                                               |
| Angular              | [MSAL angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular) | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | [Tutorial](tutorial-v2-angular.md)              | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
| AngularJS            | [AngularJS MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)         | [@azure/msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | Versão prévia pública                                               |
| JavaScript           | [MSAL.js 2,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)              | [@azure/msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [Tutorial](tutorial-v2-javascript-auth-code.md) | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
| React                | [Reagir MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)                 | [@azure/msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | Versão prévia pública                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> os [termos de uso complementares para visualizações de Microsoft Azure][preview-tos] se aplicam a bibliotecas em *Visualização pública*.

## <a name="web-application"></a>Aplicativo Web

Um aplicativo Web executa o código em um servidor que gera e envia HTML, CSS e JavaScript para o navegador da Web de um usuário a ser renderizado. A identidade do usuário é mantida como uma sessão entre o navegador do usuário (o front-end) e o servidor Web (o back-end).

Como o código de um aplicativo Web é executado no servidor Web, ele é considerado um *cliente confidencial* que pode armazenar segredos com segurança.

| Idioma/estrutura | Projeto em<br/>GitHub                                                                                     | Pacote                                                                                                    | Introdução<br/>iniciado                               | Conectar usuários                                            | Acessar APIs Web                                                    | Disponibilidade geral (GA) *ou*<br/>Visualização pública<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|:-------------------------------------------------:|:--------------------------------------------------------:|:------------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)                      | —                                                 | ![A biblioteca não pode solicitar tokens de ID para entrada do usuário.][n] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y]    | GA                                                           |
| ASP.NET Core         | [Segurança do ASP.NET](/aspnet/core/security/)                                                                | [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) | —                                                 | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y]    | ![A biblioteca não pode solicitar tokens de acesso para APIs Web protegidas.][n] | GA                                                           |
| ASP.NET Core         | [Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web)                               | [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web)                            | —                                                 | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y]    | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y]    | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://search.maven.org/artifact/com.microsoft.azure/msal4j)                                     | [Início rápido](quickstart-v2-java-webapp.md)        | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y]    | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y]    | GA                                                           |
| Node.js              | [Node.jsMSAL ](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [MSAL-nó](https://www.npmjs.com/package/@azure/msal-node)                                                | [Início rápido](quickstart-v2-nodejs-webapp-msal.md) | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y]    | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y]    | Versão prévia pública                                               |
| Node.js              | [Azure AD Passport](https://github.com/AzureAD/passport-azure-ad)                                         | [Passport-Azure-AD](https://www.npmjs.com/package/passport-azure-ad)                                       | [Início rápido](quickstart-v2-nodejs-webapp.md)      | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y]    | ![A biblioteca não pode solicitar tokens de acesso para APIs Web protegidas.][n] | GA                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)                     | [MSAL](https://pypi.org/project/msal)                                                                      | [Início rápido](quickstart-v2-python-webapp.md)      | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y]    | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y]    | GA                                                           |
<!--
| Java | [ScribeJava](https://github.com/scribejava/scribejava) | [ScribeJava 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Java | [Gluu oxAuth](https://github.com/GluuFederation/oxAuth) | [oxAuth 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| Node.js | [openid-client](https://github.com/panva/node-openid-client/) | [openid-client 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [oauth2-client 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth) | [omniauth 1.3.1](https://github.com/omniauth/omniauth/releases/tag/v1.3.1)<br/>[omniauth-oauth2 1.4.0](https://github.com/intridea/omniauth-oauth2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> os [termos de uso complementares para visualizações de Microsoft Azure][preview-tos] se aplicam a bibliotecas em *Visualização pública*.

## <a name="desktop-application"></a>Aplicativo da área de trabalho

Um aplicativo de área de trabalho normalmente é um código binário (compilado) que mostra uma interface do usuário e destina-se a ser executado na área de trabalho de um usuário.

Como um aplicativo de área de trabalho é executado na área de trabalho do usuário, ele é considerado um *cliente público* que não pode armazenar segredos com segurança.

| Idioma/estrutura | Projeto em<br/>GitHub                                                                                     | Pacote                                                                               | Introdução<br/>iniciado                        | Conectar usuários                                         | Acessar APIs Web                                                 | Disponibilidade geral (GA) *ou*<br/>Visualização pública<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Digital             | [Node.jsMSAL ](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [@azure/msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | —                                          | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | Versão prévia pública                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
| macOS (Swift/obj-C)  | [MSAL para iOS e macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Tutorial](tutorial-v2-ios.md)             | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
| UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Tutorial](tutorial-v2-windows-uwp.md)     | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Tutorial](tutorial-v2-windows-desktop.md) | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> os [termos de uso complementares para visualizações de Microsoft Azure][preview-tos] se aplicam a bibliotecas em *Visualização pública*.

## <a name="mobile-application"></a>Aplicativo móvel

Um aplicativo móvel normalmente é um código binário (compilado) que mostra uma interface do usuário e destina-se a ser executado em um dispositivo móvel do usuário.

Como um aplicativo móvel é executado no dispositivo móvel do usuário, ele é considerado um *cliente público* que não pode armazenar segredos com segurança.

| Plataforma          | Projeto em<br/>GitHub                                                                          | Pacote                                                                               | Introdução<br/>iniciado                    | Conectar usuários                                         | Acessar APIs Web                                                 | Disponibilidade geral (GA) *ou*<br/>Visualização pública<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android (Java)    | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [Início rápido](quickstart-v2-android.md) | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
| Android (Kotlin)  | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
| iOS (Swift/obj-C) | [MSAL para iOS e macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Tutorial](tutorial-v2-ios.md)         | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
| Xamarin (.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> os [termos de uso complementares para visualizações de Microsoft Azure][preview-tos] se aplicam a bibliotecas em *Visualização pública*.

## <a name="service--daemon"></a>Serviço/daemon

Os serviços e daemons são comumente usados para comunicação de servidor para servidor e outras comunicações autônomas (às vezes chamadas de sem *periféricos*). Como não há nenhum usuário no teclado para inserir as credenciais ou consentir o acesso aos recursos, esses aplicativos são autenticados como eles mesmos, e não um usuário, ao solicitar acesso autorizado aos recursos de uma API Web.

Um serviço ou daemon executado em um servidor é considerado um *cliente confidencial* que pode armazenar seus segredos com segurança.

| Idioma/estrutura | Projeto em<br/>GitHub                                                                 | Pacote                                                                                | Introdução<br/>iniciado                           | Conectar usuários                                            | Acessar APIs Web                                                 | Disponibilidade geral (GA) *ou*<br/>Visualização pública<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [Início rápido](quickstart-v2-netcore-daemon.md) | ![A biblioteca não pode solicitar tokens de ID para entrada do usuário.][n] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![A biblioteca não pode solicitar tokens de ID para entrada do usuário.][n] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [MSAL-Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | [Início rápido](quickstart-v2-python-daemon.md)  | ![A biblioteca não pode solicitar tokens de ID para entrada do usuário.][n] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> os [termos de uso complementares para visualizações de Microsoft Azure][preview-tos] se aplicam a bibliotecas em *Visualização pública*.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a biblioteca de autenticação da Microsoft, consulte a [visão geral da biblioteca de autenticação da Microsoft (MSAL)](msal-overview.md).

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
