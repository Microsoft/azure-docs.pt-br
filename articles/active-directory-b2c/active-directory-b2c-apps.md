---
title: Tipos de aplicativos que podem ser usados no Azure Active Directory B2C
description: Saiba mais sobre os tipos de aplicativos que você pode usar com Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 09cdc2fb5dba152e467164fd757225c7a9183264
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68369410"
---
# <a name="application-types-that-can-be-used-in-active-directory-b2c"></a>Tipos de aplicativos que podem ser usados no Active Directory B2C

O Azure AD (Azure Active Directory) B2C dá suporte à autenticação para uma variedade de arquiteturas de aplicativos modernos. Todas elas se baseiam nos protocolos padrão da indústria, [OAuth 2.0](active-directory-b2c-reference-protocols.md) ou [OpenID Connect](active-directory-b2c-reference-protocols.md). Este artigo descreve os tipos de aplicativos que você pode criar, independentemente da linguagem ou da plataforma que preferir. Além disso, ajuda a reconhecer cenários de alto nível antes de começar a compilar aplicativos.

Cada aplicativo que usa o Azure AD B2C deve estar registrado no [locatário do Azure AD B2C](active-directory-b2c-get-started.md), usando o [portal do Azure](https://portal.azure.com/). O processo de registro do aplicativo coleta e atribui valores, como:

* Uma **ID do aplicativo** que identifica exclusivamente o aplicativo.
* Um **URL de resposta** que pode ser usado para direcionar as respostas de volta ao seu aplicativo.

Cada solicitação enviada para Azure AD B2C especifica um fluxo de **usuário** (uma política interna) ou uma **política personalizada** que controla o comportamento de Azure ad B2C. Os dois tipos de política permitem que você crie um conjunto altamente personalizável de experiências de usuário.

A interação de cada aplicativo segue um padrão semelhante de alto nível:

1. O aplicativo direciona o usuário para o ponto de extremidade v2.0 para executar uma [política](active-directory-b2c-reference-policies.md).
2. O usuário conclui a política de acordo com a definição de política.
3. O aplicativo recebe um token de segurança do ponto de extremidade v2.0.
4. O aplicativo usa o token de segurança para acessar informações protegidas ou um recurso protegido.
5. O servidor de recurso valida o token de segurança para verificar se o acesso pode ser concedido.
6. O aplicativo atualiza periodicamente o token de segurança.

Essas etapas podem variar um pouco com base no tipo de aplicativo que você está compilando.

## <a name="web-applications"></a>Aplicativos Web

Para os aplicativos Web (incluindo .NET, PHP, Java, Ruby, Python e Node.js) hospedados em um servidor e acessados por meio de um navegador, o Azure AD B2C dá suporte ao [OpenID Connect](active-directory-b2c-reference-protocols.md) para todas as experiências de usuário. Na implementação do OpenID Connect do Azure AD B2C, o aplicativo Web inicia as experiências de usuário emitindo solicitações de autenticação para o Microsoft Azure Active Directory. O resultado da solicitação é um `id_token`. Esse token de segurança representa a identidade do usuário. Ele também fornece informações sobre o usuário na forma de declarações:

```json
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

Saiba mais sobre os tipos de tokens e declarações disponíveis para um aplicativo na [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md).

Em aplicativos Web, cada execução de uma [política](active-directory-b2c-reference-policies.md) usa estas etapas de alto nível:

1. O usuário navega até o aplicativo web.
2. O aplicativo web redireciona o usuário para o Azure Active Directory B2C que indica a política para executar.
3. O usuário conclui a política.
4. O Azure Active Directory B2C retorna um `id_token` no navegador.
5. O `id_token` é postado para o URI de redirecionamento.
6. O `id_token` é validado e um cookie de sessão é definido.
7. A página de segurança é retornada para o usuário.

Validação do `id_token` usando uma chave de assinatura pública recebida do Azure AD é suficiente para verificar a identidade do usuário. Esse processo também define um cookie de sessão que pode ser usado para identificar o usuário nas solicitações de página subsequentes.

Para ver esse cenário em ação, experimente um destes exemplos de código de entrada de aplicativo Web em nossa seção [Introdução](active-directory-b2c-overview.md).

Além de facilitar a entrada simples, um aplicativo de servidor Web talvez precise acessar algum serviço Web back-end. Nesse caso, o aplicativo Web pode executar o [fluxo do OpenID Connect](active-directory-b2c-reference-oidc.md) um pouco diferente, e adquirir tokens usando códigos de autorização e tokens de atualização. Este cenário é descrito na [seção de APIs Web](#web-apis)a seguir.

## <a name="web-apis"></a>APIs da Web

Você pode usar o Azure AD B2C para proteger serviços Web, tais como a API Web RESTful do aplicativo. APIs Web podem usar o OAuth 2.0 para proteger seus dados, autenticando solicitações HTTP recebidas usando tokens. O chamador de uma API Web acrescenta um token no cabeçalho de autorização de uma solicitação HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A API Web pode usar o token para verificar a identidade do chamador da API e extrair informações sobre o chamador por meio de declarações codificadas no token. Saiba mais sobre os tipos de tokens e declarações disponíveis para um aplicativo na [referência de token do Azure AD B2C](active-directory-b2c-reference-tokens.md).

Uma API Web pode receber tokens de muitos tipos de clientes, incluindo aplicativos Web, aplicativos móveis e da área de trabalho, aplicativos de página única, daemons do lado do servidor e até outras APIs Web. Veja um exemplo do fluxo completo de um aplicativo Web que chama uma API Web:

1. O aplicativo web executa uma política e o usuário conclui a experiência do usuário.
2. O Azure AD B2C retorna um (OpenID Connect) `id_token` e um código de autorização para o navegador.
3. As postagens de navegador a `id_token` e código de autorização ao URI de redirecionamento.
4. O servidor web valida o `id_token` e define um cookie de sessão.
5. O servidor web solicita ao Azure AD B2C um `access_token`, fornecendo a ele o código de autorização, a ID de cliente do aplicativo e as credenciais do cliente.
6. O `access_token` e `refresh_token` são retornados para o servidor web.
7. A API da web é chamada com o `access_token` em um cabeçalho de autorização.
8. A API da Web valida o token.
9. Dados seguros são retornados para o aplicativo Web.

Para saber mais sobre códigos de atualização, tokens de atualização e etapas para a obtenção de tokens, leia sobre o [Protocolo OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Para saber como proteger uma API Web com o Azure AD B2C, confira os tutoriais da API Web em nossa [seção Introdução](active-directory-b2c-overview.md).

## <a name="mobile-and-native-applications"></a>Aplicativos nativos e móveis

Os aplicativos instalados em dispositivos, como aplicativos móveis e da área de trabalho, geralmente precisam acessar serviços de back-end ou APIs Web em nome de usuários. Você pode adicionar experiências de gerenciamento de identidade personalizadas aos aplicativos nativos e chamar com segurança serviços back-end usando o Azure AD B2C e o [fluxo de código de autorização do OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Nesse fluxo, o aplicativo executa [políticas](active-directory-b2c-reference-policies.md) e recebe um `authorization_code` do Azure AD depois que o usuário conclui a política. O `authorization_code` representa a permissão do aplicativo para chamar serviços back-end em nome do usuário conectado no momento. O aplicativo pode trocar o `authorization_code` em segundo plano por um `access_token` e um `refresh_token`.  O aplicativo pode usar o `access_token` para autenticar em uma API Web back-end em solicitações HTTP. Ele também pode usar o `refresh_token` para obter um novo `access_token` quando o antigo expira.

## <a name="current-limitations"></a>Limitações atuais

### <a name="unsupported-application-types"></a>Tipos de aplicativos sem suporte

#### <a name="daemonsserver-side-applications"></a>Aplicativos daemons/do lado do servidor

Os aplicativos que contêm processos de longa duração ou que operam sem a presença de um usuário também precisam encontrar uma maneira de acessar os recursos protegidos, tais como APIs Web. Esses aplicativos podem autenticar e obter tokens usando a identidade do aplicativo (em vez da identidade delegada de um usuário) e usando o fluxo de credenciais do cliente OAuth 2.0. O fluxo de credenciais do cliente não é o mesmo que o fluxo em nome e o fluxo em nome não deve ser usado para autenticação de servidor para servidor.

Embora o fluxo de credenciais do cliente atualmente não tenha suporte pelo Azure AD B2C, é possível configurar o fluxo de credencial do cliente usando o Azure AD. Um locatário do Azure AD B2C compartilha algumas funcionalidades com os locatários corporativos do Azure AD.  O fluxo de credencial do cliente tem suporte usando a funcionalidade do Azure AD do locatário do Azure AD B2C.

Para configurar o fluxo de credencial do cliente, consulte [Azure Active Directory v2.0 e o fluxo de credencial do cliente OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-client-creds). Uma autenticação com êxito resulta no recebimento de um token formatado para poder ser usado pelo Azure AD, conforme descrito na [referência de token do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

#### <a name="web-api-chains-on-behalf-of-flow"></a>Cadeias de API Web (fluxo Em nome de)

Muitas arquiteturas incluem uma API Web que precisa chamar outra API Web downstream, ambas protegidas pelo AD B2C do Azure. Esse cenário é comum em clientes nativos que têm um back-end de API Web que chama um serviço online da Microsoft, como a API do Graph do Azure AD.

Esse cenário de API Web encadeado pode ter suporte usando a concessão credencial de portador JWT do OAuth 2.0, também conhecida como fluxo Em nome de.  No entanto, o fluxo em nome de não está implementado atualmente no Azure AD B2C.

### <a name="faulted-apps"></a>Aplicativos com falha

Não edite aplicativos Azure AD B2C das seguintes maneiras:

- Em outros portais de gerenciamento de aplicativos, como o  [Portal de Registro de Aplicativos](https://apps.dev.microsoft.com/).
- Usando a API do Graph ou o PowerShell.

Se você editar o aplicativo do Azure AD B2C fora do portal do Azure, ele se tornará um aplicativo com falha e não poderá mais ser usado com o Azure AD B2C. Exclua o aplicativo e crie-o novamente.

Para excluir o aplicativo, acesse o [Portal de Registro de Aplicativos](https://apps.dev.microsoft.com/) e exclua o aplicativo lá. Para que o aplicativo fique visível, você precisa ser o proprietário do aplicativo (e não apenas um administrador do locatário).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas internas fornecidas pelos [fluxos de usuário no Azure Active Directory B2C](active-directory-b2c-reference-policies.md).
