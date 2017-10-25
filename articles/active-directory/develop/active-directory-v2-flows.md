---
title: Tipos de aplicativo para o ponto de extremidade v2.0 do Azure Active Directory | Microsoft Docs
description: "Os tipos de aplicativos e cenários com suporte no ponto de extremidade v2.0 do Azure Active Directory."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 9d59e7f0e8f326c40be86e199d7712f6c565cc13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="app-types-for-the-azure-active-directory-v20-endpoint"></a>Tipos de aplicativo para o ponto de extremidade v2.0 do Azure Active Directory
O ponto de extremidade v2.0 do Azure AD (Azure Active Directory) oferece suporte à autenticação para diversas arquiteturas de aplicativos modernos, que se baseiam nos protocolos padrão da indústria [OAuth 2.0 ou OpenID Connect](active-directory-v2-protocols.md). Este artigo descreve os tipos de aplicativos que você pode criar usando o Azure AD v2.0, independentemente de seu idioma ou plataforma. As informações neste artigo foram projetadas para ajudá-lo a entender os cenários de alto nível antes que você [comece a trabalhar com o código](active-directory-appmodel-v2-overview.md#getting-started).

> [!NOTE]
> O ponto de extremidade v2.0 não dá suporte a todos os cenários e recursos do Azure Active Directory. Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="the-basics"></a>Noções básicas
Você deve registrar cada aplicativo que usa o ponto de extremidade v2.0 no [Portal de Registro de Aplicativo da Microsoft](https://apps.dev.microsoft.com). O processo de registro do aplicativo coleta e atribui estes valores para seu aplicativo:

* Uma **ID de Aplicativo** que identifica exclusivamente o aplicativo
* Um **URI de Redirecionamento** que pode ser usado para direcionar as respostas novamente ao aplicativo
* Alguns outros valores específicos de cenário

Para obter detalhes, saiba como [registrar um aplicativo](active-directory-v2-app-registration.md).

Depois de registrado, o aplicativo se comunica com o Azure AD enviando solicitações ao ponto de extremidade v2.0 do Azure AD. Fornecemos as estruturas e as bibliotecas de software livre que lidam com os detalhes dessas solicitações. Você também tem a opção de implementar a lógica de autenticação ao criar solicitações para esses pontos de extremidade:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

## <a name="web-apps"></a>Aplicativos Web
Para os aplicativos Web (.NET, PHP, Java, Ruby, Python, Node) que o usuário acessa por meio de um navegador, você pode usar o [OpenID Connect](active-directory-v2-protocols.md) para entrada do usuário. No OpenID Connect, o aplicativo Web recebe um token de ID. Um token de ID é um token de segurança que verifica a identidade do usuário e fornece informações sobre o usuário na forma de declarações:

```
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

É possível aprender sobre todos os tipos de tokens e declarações disponíveis para um aplicativo na [referência de tokens v2.0](active-directory-v2-tokens.md).

Em aplicativos de servidor Web, o fluxo de autenticação de entrada usa estas etapas de alto nível:

![Fluxo de autenticação do aplicativo Web](../../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

Você pode verificar a identidade do usuário ao validar o token de ID com uma chave pública de assinatura que é recebida do ponto de extremidade v2.0. Um cookie de sessão é definido e pode ser usado para identificar o usuário nas solicitações de página subsequentes.

Para ver esse cenário em ação, experimente um destes exemplos de código de entrada de aplicativo Web em nossa seção [Introdução](active-directory-appmodel-v2-overview.md#getting-started) da v.2.0.

Além de entrada simples, um aplicativo de servidor Web talvez precise acessar outro serviço Web como uma API REST. Nesse caso, o aplicativo de servidor Web participa de um fluxo do OpenID Connect e do OAuth 2.0 combinados, usando o [fluxo do código de autorização do OAuth 2.0](active-directory-v2-protocols.md). Para saber mais sobre esse cenário, leia sobre a [Introdução aos aplicativos Web e APIs Web](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>APIs da Web
É possível usar o ponto de extremidade v2.0 para proteger serviços Web, como a API Web RESTful do seu aplicativo. Em vez de tokens de ID e de cookies de sessão, uma API Web usa um token de acesso OAuth 2.0 para proteger seus dados e para autenticar solicitações de entrada. O chamador de uma API da Web acrescenta um token de acesso no cabeçalho de autorização de uma solicitação HTTP, como esta:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A API Web usa o token de acesso para verificar a identidade do chamador da API e para extrair informações sobre o chamador a partir de declarações que são codificadas no token de acesso. Para saber mais sobre todos os tipos de tokens e declarações que estão disponíveis para um aplicativo, veja a [referência de tokens v2.0](active-directory-v2-tokens.md).

Uma API Web pode oferecer aos usuários o poder de aceitar/recusar uma funcionalidade ou dados específicos ao expor permissões, também conhecidas como [escopos](active-directory-v2-scopes.md). Para um aplicativo de chamada obter permissão para um escopo, o usuário deve concordar com o escopo durante um fluxo. O ponto de extremidade v2.0 solicita a permissão do usuário e registra as permissões em todos os tokens de acesso recebidos pela API Web. A API Web valida os tokens de acesso que recebe em cada chamada e executa verificações de autorização.

Uma API da Web pode receber tokens de acesso de todos os tipos de aplicativos, incluindo aplicativos de servidor Web, aplicativos móveis e de desktop, aplicativos de página única, daemons do lado do servidor e até outras APIs da Web. O fluxo de alto nível para uma API Web tem esta aparência:

![Fluxo de autenticação da API Web](../../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

Para saber como proteger uma API Web com tokens de acesso do OAuth2, confira os exemplos de código da API Web em nossa seção [Introdução](active-directory-appmodel-v2-overview.md#getting-started).

Em muitos casos, APIs Web também precisam fazer solicitações de saída para outras APIs Web downstream protegidas pelo Azure Active Directory.  Para fazer isso, as APIs Web podem utilizar o fluxo **On Behalf Of** do Azure AD, que permite que a API Web troque um token de acesso de entrada por outro token de acesso a ser usado em solicitações de saída.  O fluxo On Behalf Of do ponto de extremidade v2.0 é descrito em [detalhes aqui](active-directory-v2-protocols-oauth-on-behalf-of.md).

## <a name="mobile-and-native-apps"></a>Aplicativos móveis e nativos
Os aplicativos instalados no dispositivo, como os aplicativos móveis e de desktop, geralmente precisam acessar serviços de back-end ou APIs da Web que armazenam dados e executam funções em nome de um usuário. Esses aplicativos podem adicionar credenciais e autorização a serviços de back-end usando o [fluxo de código de autorização do OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

Nesse fluxo, o aplicativo recebe um código de autorização do ponto de extremidade v2.0, quando o usuário faz logon. O código de autorização representa a permissão do aplicativo para chamar serviços de back-end em nome do usuário conectado. O aplicativo pode trocar o código de autorização em segundo plano por um token de acesso e um token de atualização do OAuth 2.0. O aplicativo pode usar o token de acesso para se autenticar em APIs da Web em solicitações HTTP, e usar o token de atualização para obter novos tokens de acesso quando os antigos expirarem.

![Fluxo de autenticação do aplicativo nativo](../../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="single-page-apps-javascript"></a>Aplicativos de página única (JavaScript)
Muitos aplicativos modernos têm um aplicativo de página única front-end que é escrito principalmente em JavaScript. Geralmente, ele é escrito usando uma estrutura como AngularJS, Ember.js ou Durandal.js. O ponto de extremidade v2.0 do Azure AD dá suporte a esses aplicativos usando o [fluxo implícito do OAuth 2.0](active-directory-v2-protocols-implicit.md).

Nesse fluxo, o aplicativo recebe tokens diretamente do ponto de extremidade de autorização v2.0, sem executar qualquer troca entre servidores de back-end. Todo o manuseio de lógica de autenticação e de sessão ocorra inteiramente no cliente JavaScript, sem redirecionamentos adicionais de página.

![Fluxo de autenticação implícita](../../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

Para ver esse cenário em ação, experimente um destes exemplos de código de aplicativo de página única em nossa seção de [Introdução](active-directory-appmodel-v2-overview.md#getting-started).

## <a name="daemons-and-server-side-apps"></a>Daemons e aplicativos do lado do servidor
Os aplicativos com processos de longa duração ou que operem sem interação com um usuário também precisam de uma maneira de acessar recursos protegidos, como APIs Web. Esses aplicativos podem se autenticar e obter tokens usando a identidade do aplicativo (em vez de a identidade delegada de um usuário) com o fluxo de credenciais do cliente OAuth 2.0.

Nesse fluxo, o aplicativo interage diretamente com o ponto de extremidade do `/token` para obter pontos de extremidade:

![Fluxo de autenticação de aplicativo de daemon](../../media/active-directory-v2-flows/convergence_scenarios_daemon.png)

Para criar um aplicativo daemon, consulte o a documentação sobre credenciais do cliente em nossa seção [Introdução](active-directory-appmodel-v2-overview.md#getting-started) seção ou experimente um [aplicativo de exemplo .NET](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
