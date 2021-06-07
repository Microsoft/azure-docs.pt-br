---
title: Tipos de aplicativos para a plataforma Microsoft Identity | Azure
description: Os tipos de aplicativos e cenários com suporte na plataforma de identidade da Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev, fasttrack-edit, contperf-fy21q2
ms.openlocfilehash: 7ec309f016e73642262399bd75e7b5146bc5e497
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98752779"
---
# <a name="application-types-for-the-microsoft-identity-platform"></a>Tipos de aplicativos para a plataforma Microsoft Identity

A plataforma de identidade da Microsoft dá suporte à autenticação para uma variedade de arquiteturas de aplicativo modernas, todas elas baseadas em protocolos padrão do setor [OAuth 2,0 ou OpenID Connect](active-directory-v2-protocols.md). Este artigo descreve os tipos de aplicativos que você pode criar usando o Azure AD v2.0, independentemente da sua linguagem ou plataforma preferidas. As informações foram projetadas para ajudá-lo a entender cenários de alto nível antes de começar a trabalhar com o código nos [cenários de aplicativo](authentication-flows-app-scenarios.md#application-scenarios).

## <a name="the-basics"></a>Noções básicas

Você deve registrar cada aplicativo que usa a plataforma de identidade da Microsoft no [Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908)portal do Azure. O processo de registro do aplicativo coleta e atribui estes valores para seu aplicativo:

* Uma **ID (cliente) de aplicativo** que identifica exclusivamente o aplicativo.
* Um **URI de Redirecionamento** que pode ser usado para direcionar as respostas novamente ao aplicativo
* Alguns outros valores específicos de cenário, como tipos de conta com suporte

Para obter detalhes, saiba como [registrar um aplicativo](quickstart-register-app.md).

Depois que o aplicativo é registrado, o aplicativo se comunica com a plataforma de identidade da Microsoft enviando solicitações ao ponto de extremidade. Fornecemos as estruturas e as bibliotecas de software livre que lidam com os detalhes dessas solicitações. Você também tem a opção de implementar a lógica de autenticação ao criar solicitações para esses pontos de extremidade:

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Aplicativos de página única (JavaScript)

Muitos aplicativos modernos têm um aplicativo de página única front-end escrito principalmente em JavaScript, frequentemente com uma estrutura como Angular, React ou Vue. A plataforma de identidade da Microsoft dá suporte a esses aplicativos usando o protocolo [OpenID Connect](v2-protocols-oidc.md) para autenticação e o [fluxo de concessão implícita do OAuth 2,0](v2-oauth2-implicit-grant-flow.md) ou o [código de autorização OAuth 2,0 mais recente + fluxo PKCE](v2-oauth2-auth-code-flow.md) para autorização (veja abaixo).

O diagrama de fluxo abaixo demonstra a concessão de código de autorização OAuth 2,0 (com detalhes sobre o PKCE omitido), em que o aplicativo recebe um código do ponto de extremidade da plataforma Microsoft Identity `authorize` e o resgata para tokens e tokens de atualização usando solicitações da Web entre sites. O token de atualização expira a cada 24 horas e o aplicativo deve solicitar outro código. Além do token de acesso, um `id_token` que representa o usuário conectado ao aplicativo cliente normalmente também é solicitado por meio do mesmo fluxo e/ou uma solicitação separada do OpenID Connect (não mostrada aqui).

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="Diagrama mostrando o fluxo do código de autorização OAuth 2 entre um aplicativo de página única e o ponto de extremidade do serviço de token de segurança." border="false":::

Para ver esse cenário em ação, confira nosso [Tutorial: Conectar usuários e chamar a API do Microsoft Graph de um SPA (aplicativo de página única) JavaScript usando o fluxo de código de autenticação](tutorial-v2-javascript-auth-code.md).

### <a name="authorization-code-flow-vs-implicit-flow"></a>Fluxo de código de autorização versus fluxo implícito

Para a maior parte do histórico do OAuth 2.0, o [fluxo implícito](v2-oauth2-implicit-grant-flow.md) foi a maneira recomendada para criar aplicativos de página única. Com a remoção de [cookies de terceiros](reference-third-party-cookies-spas.md) e [maior atenção](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14) em questões de segurança relacionadas ao fluxo implícito, mudamos para o fluxo do código de autorização para aplicativos de página única.

Para garantir a compatibilidade do seu aplicativo no Safari e outros navegadores preocupados com a privacidade, não recomendamos o uso do fluxo implícito e, em vez disso, recomendamos o fluxo do código de autorização.

## <a name="web-apps"></a>Aplicativos Web

Para os aplicativos Web (.NET, PHP, Java, Ruby, Python, Node) que o usuário acessa por meio de um navegador, você pode usar o [OpenID Connect](active-directory-v2-protocols.md) para entrada do usuário. No OpenID Connect, o aplicativo Web recebe um token de ID. Um token de ID é um token de segurança que verifica a identidade do usuário e fornece informações sobre o usuário na forma de declarações:

```JSON
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

Mais detalhes de tipos diferentes de tokens usados na plataforma de identidade da Microsoft estão disponíveis na referência de [token de acesso](access-tokens.md) e [id_token Reference](id-tokens.md)

Em aplicativos de servidor Web, o fluxo de autenticação de entrada usa estas etapas de alto nível:

![Mostra o fluxo de autenticação do aplicativo Web](./media/v2-app-types/convergence-scenarios-webapp.svg)

Você pode garantir a identidade do usuário Validando o token de ID com uma chave de assinatura pública que é recebida da plataforma de identidade da Microsoft. Um cookie de sessão é definido e pode ser usado para identificar o usuário nas solicitações de página subsequentes.

Para ver esse cenário em ação, experimente os exemplos de código no [aplicativo Web que entra no cenário de usuários](scenario-web-app-sign-user-overview.md).

Além de entrada simples, um aplicativo de servidor Web talvez precise acessar outro serviço Web como uma API REST. Nesse caso, o aplicativo de servidor Web participa de um fluxo do OpenID Connect e do OAuth 2.0 combinados, usando o [fluxo do código de autorização do OAuth 2.0](v2-oauth2-auth-code-flow.md). Para saber mais sobre esse cenário, leia sobre a [Introdução aos aplicativos Web e APIs Web](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIDConnect-DotNet).

## <a name="web-apis"></a>APIs da Web

Você pode usar a plataforma de identidade da Microsoft para proteger serviços Web, como a API Web RESTful do seu aplicativo. As APIs da Web podem ser implementadas em várias plataformas e linguagens. Elas também podem ser implementadas usando gatilhos HTTP no Azure Functions. Em vez de tokens de ID e de cookies de sessão, uma API Web usa um token de acesso OAuth 2.0 para proteger seus dados e para autenticar solicitações de entrada. O chamador de uma API da Web acrescenta um token de acesso no cabeçalho de autorização de uma solicitação HTTP, como esta:

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A API Web usa o token de acesso para verificar a identidade do chamador da API e para extrair informações sobre o chamador a partir de declarações que são codificadas no token de acesso. Mais detalhes de diferentes tipos de tokens usados na plataforma Microsoft Identity estão disponíveis na referência de [token de acesso](access-tokens.md) e referência de [id_token](id-tokens.md) .

Uma API Web pode oferecer aos usuários o poder de aceitar/recusar uma funcionalidade ou dados específicos ao expor permissões, também conhecidas como [escopos](v2-permissions-and-consent.md). Para um aplicativo de chamada obter permissão para um escopo, o usuário deve concordar com o escopo durante um fluxo. A plataforma de identidade da Microsoft solicita permissão ao usuário e, em seguida, registra permissões em todos os tokens de acesso que a API Web recebe. A API Web valida os tokens de acesso que recebe em cada chamada e executa verificações de autorização.

Uma API da Web pode receber tokens de acesso de todos os tipos de aplicativos, incluindo aplicativos de servidor Web, aplicativos móveis e de desktop, aplicativos de página única, daemons do lado do servidor e até outras APIs da Web. O fluxo de alto nível para uma API Web tem esta aparência:

![Mostra o fluxo de autenticação da API Web](./media/v2-app-types/convergence-scenarios-webapi.svg)

Para saber como proteger uma API Web usando tokens de acesso OAuth2, confira os exemplos de código da API Web no [cenário da API Web protegida](scenario-protected-web-api-overview.md).

Em muitos casos, APIs Web também precisam fazer solicitações de saída para outras APIs Web downstream protegidas pela plataforma de identidade da Microsoft. Para fazer isso, as APIs Web podem utilizar o fluxo **On Behalf Of**, que permite que a API Web troque um token de acesso de entrada por outro token de acesso a ser usado em solicitações de saída. Para obter mais informações, consulte a [plataforma de identidade da Microsoft e o fluxo em nome de do OAuth 2,0](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Aplicativos móveis e nativos

Os aplicativos instalados no dispositivo, como os aplicativos móveis e de desktop, geralmente precisam acessar serviços de back-end ou APIs da Web que armazenam dados e executam funções em nome de um usuário. Esses aplicativos podem adicionar credenciais e autorização a serviços de back-end usando o [fluxo de código de autorização do OAuth 2.0](v2-oauth2-auth-code-flow.md).

Nesse fluxo, o aplicativo recebe um código de autorização da plataforma Microsoft Identity quando o usuário faz logon. O código de autorização representa a permissão do aplicativo para chamar serviços de back-end em nome do usuário conectado. O aplicativo pode trocar o código de autorização em segundo plano por um token de acesso e um token de atualização do OAuth 2.0. O aplicativo pode usar o token de acesso para se autenticar em APIs da Web em solicitações HTTP, e usar o token de atualização para obter novos tokens de acesso quando os antigos expirarem.

![Mostra o fluxo de autenticação do aplicativo nativo](./media/v2-app-types/convergence-scenarios-native.svg)

> [!NOTE]
> Se o aplicativo usar o sistema de WebView padrão, verifique as informações sobre a funcionalidade "confirmar minha entrada" e o código de erro AADSTS50199 em [códigos de erro de autorização e autenticação do Azure ad](reference-aadsts-error-codes.md).

## <a name="daemons-and-server-side-apps"></a>Daemons e aplicativos do lado do servidor

Os aplicativos com processos de longa duração ou que operem sem interação com um usuário também precisam de uma maneira de acessar recursos protegidos, como APIs Web. Esses aplicativos podem se autenticar e obter tokens usando a identidade do aplicativo (em vez de a identidade delegada de um usuário) com o fluxo de credenciais do cliente OAuth 2.0. Você pode provar a identidade do aplicativo usando um certificado ou o segredo do cliente. Para obter mais informações, consulte [aplicativo de console do daemon do .NET Core usando a plataforma de identidade da Microsoft](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2).

Nesse fluxo, o aplicativo interage diretamente com o ponto de extremidade `/token` para obter acesso:

![Mostra o fluxo de autenticação de aplicativo de daemon](./media/v2-app-types/convergence-scenarios-daemon.svg)

Para construir um aplicativo daemon, consulte a [ documentação de credenciais do cliente ](v2-oauth2-client-creds-grant-flow.md) ou tente um [ aplicativo de amostra .NET ](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com os tipos de aplicativos com suporte na plataforma de identidade da Microsoft, saiba mais sobre [OAuth 2.0 e OpenID Connect](active-directory-v2-protocols.md) para obter uma compreensão dos componentes de protocolo usados pelos diferentes cenários.
