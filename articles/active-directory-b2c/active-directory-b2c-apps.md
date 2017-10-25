---
title: Tipos de aplicativos - Azure AD B2C | Microsoft Docs
description: "Os tipos de aplicativos que você pode compilar no Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: bb9d4abe-0db7-4bd9-b0c4-2f43b2c9cf33
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/06/2016
ms.author: dastrock
ms.openlocfilehash: 51001feb17ae99d3bd391a9f980d514e07f97099
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-types-of-applications"></a>Azure Active Directory B2C: tipos de aplicativos
O Active Directory B2C do Azure (AD do Azure) oferece suporte à autenticação para várias arquiteturas de aplicativos modernos. Todas elas se baseiam nos protocolos padrão da indústria, [OAuth 2.0](active-directory-b2c-reference-protocols.md) ou [OpenID Connect](active-directory-b2c-reference-protocols.md). Este documento descreve brevemente os tipos de aplicativos que você pode compilar, independentemente da linguagem ou plataforma preferida. Ele também ajuda você a entender os cenários de alto nível antes de [começar a compilar aplicativos](active-directory-b2c-overview.md#get-started).

## <a name="the-basics"></a>Noções básicas
Todos os aplicativos que usam o Azure AD B2C devem ser registrados em seu [diretório B2C](active-directory-b2c-get-started.md) por meio do [Portal do Azure](https://portal.azure.com/). O processo de registro do aplicativo coleta e atribui alguns valores ao seu aplicativo:

* Uma **ID de aplicativo** que identifica exclusivamente o aplicativo.
* Um **URI de Redirecionamento** que pode ser usado para direcionar as respostas de volta ao aplicativo.
* Quaisquer outros valores específicos ao cenário. Para obter mais detalhes, saiba como [registrar um aplicativo](active-directory-b2c-app-registration.md).

Depois de registrado, o aplicativo se comunica com o AD do Azure enviando solicitações ao ponto de extremidade v2.0 do AD do Azure:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Cada solicitação enviada para o AD B2C do Azure especifica uma **política**. Uma política controla o comportamento do AD do Azure. Você também pode usar esses pontos de extremidade para criar um conjunto altamente personalizável de experiências do usuário. Algumas políticas comuns incluem inscrição, entrada e edição de perfil. Se você não estiver familiarizado com as políticas, leia sobre a [estrutura de política extensível](active-directory-b2c-reference-policies.md) do Azure AD B2C antes de continuar.

A interação de cada aplicativo com o ponto de extremidade v2.0 segue um padrão semelhante de alto nível:

1. O aplicativo direciona o usuário ao ponto de extremidade v2.0 para executar uma [política](active-directory-b2c-reference-policies.md).
2. O usuário conclui a política de acordo com a definição de política.
3. O aplicativo recebe algum tipo de token de segurança do ponto de extremidade v2.0.
4. O aplicativo usa o token de segurança para acessar informações protegidas ou um recurso protegido.
5. O servidor de recurso valida o token de segurança para verificar se o acesso pode ser concedido.
6. O aplicativo atualiza periodicamente o token de segurança.

<!-- TODO: Need a page for libraries to link to -->
Essas etapas podem variar um pouco com base no tipo de aplicativo que você está compilando. Bibliotecas de software livre aberto podem resolver os detalhes para você.

## <a name="web-apps"></a>Aplicativos Web
Para os aplicativos Web (incluindo .NET, PHP, Java, Ruby, Python e Node.js) hospedados em um servidor e acessados por meio de um navegador, o Azure AD B2C dá suporte ao [OpenID Connect](active-directory-b2c-reference-protocols.md) para todas as experiências de usuário. Isso inclui gerenciamento de entrada, de inscrição e de perfil. Na implementação do OpenID Connect do AD B2C do Azure, seu aplicativo Web inicia essas experiências de usuário emitindo solicitações de autenticação para AD do Azure. O resultado da solicitação é um `id_token`. Esse token de segurança representa a identidade do usuário. Ele também fornece informações sobre o usuário na forma de declarações:

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Saiba mais sobre os tipos de tokens e declarações disponíveis para um aplicativo na [referência de token do B2C](active-directory-b2c-reference-tokens.md).

Em aplicativos Web, cada execução de uma [política](active-directory-b2c-reference-policies.md) usa estas etapas de alto nível:

![Imagem de raias do aplicativo Web](./media/active-directory-b2c-apps/webapp.png)

Validação do `id_token` usando uma chave de assinatura pública recebida do Azure AD é suficiente para verificar a identidade do usuário. Isso também define um cookie de sessão que pode ser usado para identificar o usuário nas solicitações de página subsequentes.

Para ver esse cenário em ação, experimente um destes exemplos de código de entrada de aplicativo Web em nossa seção [Introdução](active-directory-b2c-overview.md#get-started).

Além de facilitar a entrada simples, um aplicativo de servidor Web talvez precise acessar algum serviço Web de back-end. Nesse caso, o aplicativo Web pode executar o [fluxo do OpenID Connect](active-directory-b2c-reference-oidc.md) um pouco diferente, e adquirir tokens usando códigos de autorização e tokens de atualização. Este cenário é descrito na [seção de APIs Web](#web-apis)a seguir.

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## <a name="web-apis"></a>APIs da Web
Você pode usar o AD B2C do Azure para proteger serviços Web, tais como a API Web RESTful do aplicativo. APIs Web podem usar o OAuth 2.0 para proteger seus dados, autenticando solicitações HTTP recebidas usando tokens. O chamador de uma API Web acrescenta um token no cabeçalho de autorização de uma solicitação HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A API Web pode usar o token para verificar a identidade do chamador da API e extrair informações sobre o chamador por meio de declarações codificadas no token. Saiba mais sobre os tipos de tokens e declarações disponíveis para um aplicativo na [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md).

> [!NOTE]
> Atualmente, o Azure AD B2C dá suporte apenas a APIs Web acessadas por seus próprios clientes conhecidos. Por exemplo, seu aplicativo completo pode incluir um aplicativo iOS e aplicativo do Android e um API Web de back-end. Essa arquitetura tem total suporte. Atualmente, não há suporte para permitir que um cliente parceiro, como outro aplicativo iOS, acesse também a mesma API Web. Todos os componentes do aplicativo completo devem compartilhar uma única ID do aplicativo.
>
>

Uma API Web pode receber tokens de muitos tipos de clientes, incluindo aplicativos Web, aplicativos móveis e de área de trabalho, aplicativos de página única, daemons do lado do servidor e até outras APIs Web. Veja um exemplo do fluxo completo de um aplicativo Web que chama uma API Web:

![Imagem de raias da API da Web do aplicativo Web](./media/active-directory-b2c-apps/webapi.png)

Para saber mais sobre códigos de atualização, tokens de atualização e etapas para a obtenção de tokens, leia sobre o [Protocolo OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Para saber como proteger uma API Web com o Azure AD B2C, confira os tutoriais da API Web em nossa [seção Introdução](active-directory-b2c-overview.md#get-started).

## <a name="mobile-and-native-apps"></a>Aplicativos móveis e nativos
Os aplicativos instalados em dispositivos, como aplicativos móveis e de área de trabalho, geralmente precisam acessar serviços de back-end ou APIs Web em nome de usuários. Você pode adicionar experiências de gerenciamento de identidade personalizadas a seus aplicativos nativos e chamar com segurança serviços de back-end usando o Azure AD B2C e o [fluxo de código de autorização do OAuth 2.0](active-directory-b2c-reference-oauth-code.md).  

Nesse fluxo, o aplicativo executa [políticas](active-directory-b2c-reference-policies.md) e recebe um `authorization_code` do Azure AD depois que o usuário conclui a política. O `authorization_code` representa a permissão do aplicativo para chamar serviços de back-end em nome do usuário conectado no momento. O aplicativo pode trocar o `authorization_code` em segundo plano por um `id_token` e um `refresh_token`.  O aplicativo pode usar o `id_token` para autenticar em uma API Web de back-end em solicitações HTTP. Ele também pode usar o `refresh_token` para obter um novo `id_token` quando o antigo expira.

> [!NOTE]
> Atualmente, o Azure AD B2C só dá suporte a tokens que são usados para acessar um serviço Web de back-end do próprio aplicativo. Por exemplo, seu aplicativo completo pode incluir um aplicativo iOS e aplicativo do Android e um API Web de back-end. Essa arquitetura tem total suporte. Não há suporte atualmente para permitir que o aplicativo iOS acesse uma API Web de parceiro usando tokens de acesso do OAuth 2.0. Todos os componentes do aplicativo completo devem compartilhar uma única ID do aplicativo.
>
>

![Imagem de raias do aplicativo Nativo](./media/active-directory-b2c-apps/native.png)

## <a name="current-limitations"></a>Limitações atuais
Atualmente, o Azure AD B2C não suporta os tipos de aplicativos a seguir, mas há intenção futura de implementação. 

### <a name="daemonsserver-side-apps"></a>Aplicativos daemons/do lado do servidor
Os aplicativos que contêm processos de longa duração ou que operem sem a presença de um usuário também precisam de uma maneira de acessar recursos protegidos, como APIs Web. Esses aplicativos podem se autenticar e obter tokens usando a identidade do aplicativo (em vez de a identidade delegada de um usuário) e usando o fluxo de credenciais do cliente OAuth 2.0.

Atualmente, esse fluxo não tem o suporte do AD B2C do Azure. Esses aplicativos podem obter tokens somente após a ocorrência de um fluxo de usuário interativo.

### <a name="web-api-chains-on-behalf-of-flow"></a>Cadeias de API Web (fluxo Em nome de)
Muitas arquiteturas incluem uma API Web que precisa chamar outra API Web downstream, ambas protegidas pelo AD B2C do Azure. Esse cenário é comum em clientes nativos que têm um back-end de API Web. Isso chama um serviço online da Microsoft como a API do Graph do AD do Azure.

Esse cenário de API Web encadeado pode ter suporte usando a concessão credencial de portador JWT do OAuth 2.0, também conhecida como fluxo Em nome de.  No entanto, o fluxo em nome de não está implementado atualmente no Azure AD B2C.
