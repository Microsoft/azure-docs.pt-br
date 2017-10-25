---
title: "Exemplos de código do Azure Active Directory | Microsoft Docs"
description: "Um índice de exemplos de código do Active Directory do Azure, organizados por cenário."
services: active-directory
documentationcenter: dev-center-name
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/19/2017
ms.author: mbaldwin
ms.custom: aaddev
ms.openlocfilehash: 19d71e65dc2c78663d94c74228dcff038566391b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-code-samples"></a>Exemplos de código do Active Directory do Azure
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Você pode usar o Active Directory do Microsoft Azure (AD do Azure) para adicionar autenticação e autorização a aplicativos da Web e APIs da Web. Esta seção leva você a exemplos que mostram como isso é feito, bem como a trechos de código que podem ser usados em seus aplicativos. Na página de exemplo de código, você encontrará tópicos de leitura detalhados que ajudam nos requisitos, instalação e configuração. E o código é comentado para ajudá-lo a entender as seções críticas.

Para entender o cenário básico para cada tipo de exemplo, consulte Cenários de Autenticação do Azure AD.

Contribua com nossos exemplos no GitHub: [Exemplos e documentação do Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="web-browser-to-web-application"></a>Navegador da Web para o aplicativo Web
Estes exemplos mostram como escrever um aplicativo Web que direciona o navegador do usuário para entrar no AD do Azure.

| Linguagem/plataforma | Amostra | Descrição |
| --- | --- | --- |
| C# / .NET |[WebApp-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) |Use o OpenID Connect (middleware ASP.Net OpenID Connect OWIN) para autenticar usuários de um locatário do Azure AD. |
| C# / .NET |[WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) |Um aplicativo Web .NET MVC multilocatário que usa o OpenID Connect (middleware ASP.Net OpenID Connect OWIN) para autenticar usuários de vários locatários do Azure AD. |
| C# / .NET |[WebApp-WSFederation-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) |Use o WS-Federation (middleware ASP.Net WS-Federation OWIN) para autenticar usuários de um locatário do Azure AD. |

## <a name="single-page-application-spa"></a>SPA (Aplicativo de Página Única)
Este exemplo mostra como escrever um aplicativo de página única protegido com o AD do Azure.  

| Linguagem/plataforma | Amostra | Descrição |
| --- | --- | --- |
| JavaScript, C#/.NET |[SinglePageApp-DotNet](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) |Use o ADAL para JavaScript e o Azure AD para proteger um aplicativo de página única baseado em AngularJS implementado com um back-end da API da Web ASP.NET. |

## <a name="native-application-to-web-api"></a>Aplicativo nativo para API da Web
Estes exemplos de código mostram como compilar aplicativos cliente nativos que chamam APIs da Web protegidas pelo AD do Azure. Eles usam a [biblioteca de autenticação do Azure AD (ADAL)](active-directory-authentication-libraries.md) e o [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Linguagem/plataforma | Amostra | Descrição |
| --- | --- | --- |
| JavaScript |[NativeClient-MultiTarget-Cordova](https://github.com/Azure-Samples/active-directory-cordova-multitarget) |Use o plug-in ADAL para Apache Cordova para compilar um aplicativo Apache Cordova que chama uma API da Web e usa o Azure AD para autenticação. |
| C# / .NET |[NativeClient-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) |Um aplicativo .NET WPF que chama uma API da Web protegida usando o Azure AD. |
| C# / .NET |[NativeClient-WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) |Um aplicativo da Windows Store que chama uma API da Web protegida pelo Azure AD. |
| C# / .NET |[NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) |Um aplicativo da Windows Store que chama uma API da Web de multilocatários protegida pelo Azure AD. |
| C# / .NET |[WebAPI-OnBehalfOf-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof) |Um aplicativo cliente nativo que chama uma API da Web, a qual obtém um token para agir em nome do usuário original, e depois usa esse token para chamar outra API da Web. |
| C# / .NET |[NativeClient-WindowsPhone8.1](https://github.com/Azure-Samples/active-directory-dotnet-windowsphone-8.1) |Um aplicativo da Windows Store para Windows Phone 8.1 que chama uma API da Web protegida pelo Azure AD. |
| ObjC |[NativeClient-iOS](https://github.com/Azure-Samples/active-directory-ios) |Um aplicativo iOS que chama uma API da Web que requer o Azure AD para autenticação. |
| C# / .NET |[WebAPI-ManuallyValidateJwt-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation) |Um aplicativo cliente nativo que inclui lógica para processar um token JWT em uma API da Web, em vez de usar o middleware OWIN. |
| C#/Xamarin |[NativeClient-Xamarin-Android](https://github.com/Azure-Samples/active-directory-xamarin-android) |Uma associação Xamarin para a ADAL (Biblioteca de Autenticação do Azure AD) para a biblioteca Android. |
| C#/Xamarin |[NativeClient-Xamarin-iOS](https://github.com/Azure-Samples/active-directory-xamarin-ios) |Uma associação Xamarin para a ADAL (Biblioteca de Autenticação do Azure AD) nativa para iOS. |
| C#/Xamarin |[NativeClient-MultiTarget-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-multitarget) |Um projeto Xamarin direcionado a cinco plataformas e que chama uma API da Web protegida pelo Azure AD. |
| C# / .NET |[NativeClient-Headless-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-native-headless) |Um aplicativo nativo que realiza a autenticação não interativa e chama uma API da Web protegida pelo Azure AD. |

## <a name="web-application-to-web-api"></a>Aplicativo Web para API da Web
Esses exemplos de código mostram como usar o [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) para compilar aplicativos Web que chamam APIs da Web protegidas pelo Azure AD.

| Linguagem/plataforma | Amostra | Descrição |
| --- | --- | --- |
| C# / .NET |[WebApp-WebAPI-OpenIDConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect) |Chame uma API da Web com as permissões do usuário conectado. |
| C# / .NET |[WebApp-WebAPI-OAuth2-AppIdentity-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity) |Chame uma API da Web com as permissões do aplicativo. |
| C# / .NET |[WebApp-WebAPI-OAuth2-UserIdentity-Dotnet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) |Adicione uma autorização com o [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) a um aplicativo Web existente para que ele possa chamar uma API da Web. |
| JavaScript |[WebAPI-Nodejs](https://github.com/Azure-Samples/active-directory-node-webapi) |Configure um serviço de API REST que é integrado ao Azure AD para proteção da API. Inclui um servidor Node.js com uma API da Web. |
| C# / .NET |[WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-multitenant-openidconnect) |Um aplicativo Web MVC multilocatário que usa o OpenID Connect (middleware ASP.Net OpenID Connect OWIN) para autenticar usuários de um locatário do Azure AD. Usa um código de autorização para invocar a Graph API. |

## <a name="server-or-daemon-application-to-web-api"></a>Servidor ou aplicativo Daemon para API da Web
Esses exemplos de código mostram como compilar um aplicativo de daemon ou para servidores que obtém os recursos de uma API Web usando a [ADAL (Biblioteca de Autenticação do Azure AD)](active-directory-authentication-libraries.md) e o [OAuth 2.0 no Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Linguagem/plataforma | Amostra | Descrição |
| --- | --- | --- |
| C# / .NET |[Daemon-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon) |Um aplicativo de console chama uma API da Web. A credencial do cliente é uma senha. |
| C# / .NET |[Daemon-CertificateCredential-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) |Um aplicativo de console que chama uma API da Web. A credencial do cliente é um certificado. |

## <a name="calling-azure-ad-graph-api"></a>Chamando a Graph API do AD do Azure
Esses exemplos de código mostram como compilar aplicativos que chamam a Graph API do AD do Azure para ler e gravar dados no diretório.

| Linguagem/plataforma | Amostra | Descrição |
| --- | --- | --- |
| Java |[WebApp-GraphAPI-Java](https://github.com/Azure-Samples/active-directory-java-graphapi-web) |Um aplicativo Web que usa a Graph API para acessar os dados do diretório do Azure AD. |
| PHP |[WebApp-GraphAPI-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-web) |Um aplicativo Web que usa a Graph API para acessar os dados do diretório do Azure AD. |
| C# / .NET |[WebApp-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) |Um aplicativo Web que usa a Graph API para acessar os dados do diretório do Azure AD. |
| C# / .NET |[ConsoleApp-GraphAPI-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-console) |Esse aplicativo de console demonstra chamadas comuns de leitura e gravação para a Graph API, e mostra como executar a atribuição de licença de usuário e atualizar uma foto em miniatura do usuário e links. |
| C# / .NET |[ConsoleApp-GraphAPI-DiffQuery-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-diffquery) |Um aplicativo de console que utiliza a consulta diferencial na Graph API para obter alterações periódicas nos objetos de usuário em um locatário do Azure AD. |
| C# / .NET |[WebApp-GraphAPI-DirectoryExtensions-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-directoryextensions-web) |Um aplicativo MVC que usa consultas de Graph API para gerar um organograma comercial simples. |
| PHP |[WebApp-GraphAPI-DirectoryExtensions-PHP](https://github.com/Azure-Samples/active-directory-php-graphapi-directoryextensions-web) |Um aplicativo PHP que chama a Graph API para registrar uma extensão e, em seguida, ler, atualizar e excluir valores no atributo de extensão. |

## <a name="authorization"></a>Autorização
Estes exemplos de código mostram como usar o AD do Azure para autorização.

| Linguagem/plataforma | Amostra | Descrição |
| --- | --- | --- |
| C# / .NET |[WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) |Executa o RBAC (controle de acesso baseado em função) usando declarações de grupo do Azure Active Directory em um aplicativo que é integrado ao Azure AD. |
| C# / .NET |[WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) |Executa o RBAC (controle de acesso baseado em função) usando funções de aplicativo do Azure Active Directory em um aplicativo que é integrado ao Azure AD. |

## <a name="legacy-walkthroughs"></a>Passo a passo herdado
Essas orientações usam tecnologia ligeiramente mais antiga, mas ainda podem ser interessantes.

| Linguagem/plataforma | Amostra | Descrição |
| --- | --- | --- |
| C# / .NET |[Autorização baseada em função e baseada em ACL em um aplicativo do Microsoft Azure AD](http://go.microsoft.com/fwlink/?LinkId=331694) |Execute RBAC (autorização baseada em função) e autorização baseada em ACL em um aplicativo que é integrado ao Azure AD. |
| C# / .NET |[AAL - aplicativo da Windows Store para serviço REST - autenticação](http://go.microsoft.com/fwlink/?LinkId=330605) |Use a [ADAL (Biblioteca de Autenticação do Azure AD)](active-directory-authentication-libraries.md) (antigo AAL) para Windows Store Beta para adicionar recursos de autenticação de usuário para um aplicativo da Windows Store. |
| C# / .NET |[ADAL - aplicativo nativo para serviço REST - autenticação com AAD via caixa de diálogo de navegador](http://go.microsoft.com/fwlink/?LinkId=259814) |Use a [ADAL (Biblioteca de Autenticação do Azure AD)](active-directory-authentication-libraries.md) para adicionar recursos de autenticação de usuário para um cliente WPF. |
| C# / .NET |[ADAL - aplicativo nativo para serviço REST - autenticação com ACS via caixa de diálogo de navegador](http://code.msdn.microsoft.com/AAL-Native-App-to-REST-de57f2cc) |Use a [ADAL (Biblioteca de Autenticação do Azure AD)](active-directory-authentication-libraries.md) e o [ACS (Serviço de Controle de Acesso 2.0)](http://msdn.microsoft.com/library/azure/hh147631.aspx) para adicionar recursos de autenticação de usuário para um cliente WPF. |
| C# / .NET |[ADAL - Autenticação de servidor a servidor](http://go.microsoft.com/fwlink/?LinkId=259816) |Use a [ADAL (Biblioteca de Autenticação do Azure AD)](active-directory-authentication-libraries.md) para proteger chamadas de serviço de um processo do lado do servidor para um serviço REST da API da Web MVC4. |
| C# / .NET |[Adicionando logon ao seu aplicativo Web usando o Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) |Configure um aplicativo .NET para executar logon único da Web em seu diretório comercial do Azure AD. |
| C# / .NET |[Desenvolvendo aplicativos Web multilocatários com o Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect) |Use o Azure AD para adicionar recursos de logon único e de acesso de diretório de um aplicativo .NET para funcionar em várias organizações. |
| Java |[Aplicativo de exemplo Java para Graph API Azure AD](http://go.microsoft.com/fwlink/?LinkId=263969) |Use a Graph API para acessar dados do diretório a partir do Azure AD. |
| PHP |[Aplicativo de exemplo PHP para Graph API do Azure AD](http://code.msdn.microsoft.com/PHP-Sample-App-For-Windows-228c6ddb) |Use a Graph API para acessar dados do diretório a partir do Azure AD. |
| C# / .NET |[Aplicativo de exemplo para Graph API do Azure AD](http://go.microsoft.com/fwlink/?LinkID=262648) |Use a Graph API para acessar dados do diretório a partir do Azure AD. |
| C# / .NET |[Aplicativo de exemplo para consulta diferencial gráfica do Azure AD](http://go.microsoft.com/fwlink/?LinkId=275398) |Use a consulta diferencial na Graph API para obter alterações periódicas nos objetos de usuário em um locatário do Azure AD. |
| C# / .NET |[Aplicativo de exemplo para integrar aplicativos multilocatários em nuvem para o Azure AD](http://go.microsoft.com/fwlink/?LinkId=275397) |Integre um aplicativo multilocatário no Azure AD. |
| C# / .NET |[Proteger um aplicativo da Windows Store e o serviço Web REST usando o Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) |Crie um recurso de API da Web simples e um aplicativo de cliente da Windows Store usando o Azure AD e a [ADAL (Biblioteca de Autenticação do Azure AD)](active-directory-authentication-libraries.md). |
| C# / .NET |[Usando a Graph API para consultar o Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-graphapi-web) |Configure um aplicativo Microsoft .NET para usar a Graph API do Azure AD para acessar dados de um diretório de locatário do Azure AD. |

## <a name="see-also"></a>Confira também
##### <a name="other-resources"></a>Outros recursos
[Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md)

[Conceitos e referência da Graph API do Azure AD](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Biblioteca auxiliar da Graph API do Azure AD](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
