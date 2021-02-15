---
title: Tokens de segurança | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre os conceitos básicos dos tokens de segurança na plataforma de identidade da Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/11/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 6d9f5538d377be1414089e591559344bde4f381a
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98795635"
---
# <a name="security-tokens"></a>Tokens de segurança

Um provedor de identidade centralizado é especialmente útil para aplicativos que têm usuários localizados em todo o mundo que não necessariamente se conectam da rede da empresa. A plataforma de identidade da Microsoft autentica os usuários e fornece tokens de segurança, como [tokens de acesso](developer-glossary.md#access-token), [tokens de atualização](developer-glossary.md#refresh-token)e [tokens de ID](developer-glossary.md#id-token). Os tokens de segurança permitem que um [aplicativo cliente](developer-glossary.md#client-application) acesse recursos protegidos em um [servidor de recursos](developer-glossary.md#resource-server).

**Token de acesso**: um token de acesso é um token de segurança que é emitido por um [servidor de autorização](developer-glossary.md#authorization-server) como parte de um fluxo [OAuth 2,0](active-directory-v2-protocols.md) . Ele contém informações sobre o usuário e o recurso para o qual o token se destina. As informações podem ser usadas para acessar APIs da Web e outros recursos protegidos. Os tokens de acesso são validados pelos recursos para conceder acesso a um aplicativo cliente. Para saber mais sobre como a plataforma de identidade da Microsoft emite tokens de acesso, consulte [tokens de acesso](access-tokens.md).

**Token de atualização**: como os tokens de acesso são válidos por apenas um curto período de tempo, os servidores de autorização, às vezes, emitirão um token de atualização ao mesmo tempo em que o token de acesso é emitido. O aplicativo cliente pode então trocar esse token de atualização para um novo token de acesso quando necessário. Para saber mais sobre como a plataforma de identidade da Microsoft usa tokens de atualização para revogar permissões, consulte [revogação de token](access-tokens.md#token-revocation).

**Token de ID**: os tokens de ID são enviados ao aplicativo cliente como parte de um fluxo do [OpenID Connect](v2-protocols-oidc.md) . Eles podem ser enviados junto ou em vez de um token de acesso. Os tokens de ID são usados pelo cliente para autenticar o usuário. Para saber mais sobre como a plataforma de identidade da Microsoft emite tokens de ID, consulte [tokens de ID](id-tokens.md).

> [!NOTE]
> Este artigo discute os tokens de segurança usados pelos protocolos OAuth2 e OpenID Connect. Muitos aplicativos empresariais usam SAML para autenticar usuários. Para obter informações sobre asserções SAML, consulte [Azure Active Directory referência de token SAML](reference-saml-tokens.md).

## <a name="validate-security-tokens"></a>Validar tokens de segurança

Cabe ao aplicativo para o qual o token foi gerado, o aplicativo Web que se conectou ao usuário ou a API Web que está sendo chamada para validar o token. O token é assinado pelo servidor de autorização com uma chave privada. O servidor de autorização publica a chave pública correspondente. Para validar um token, o aplicativo verifica a assinatura usando a chave pública do servidor de autorização para validar que a assinatura foi criada usando a chave privada.

Os tokens são válidos por apenas um período limitado. Normalmente, o servidor de autorização fornece um par de tokens, como:

* Um token de acesso, que acessa o aplicativo ou recurso protegido.
* Um token de atualização, que é usado para atualizar o token de acesso quando o token de acesso está perto de expirar.

Tokens de acesso são passados para uma API da Web como o token de portador no `Authorization` cabeçalho. Um aplicativo pode fornecer um token de atualização para o servidor de autorização. Se o acesso do usuário ao aplicativo não foi revogado, ele receberá um novo token de acesso e um novo token de atualização. É assim que o cenário de alguém que sai da empresa é tratado. Quando o servidor de autorização recebe o token de atualização, ele não emitirá outro token de acesso válido se o usuário não estiver mais autorizado.

## <a name="json-web-tokens-and-claims"></a>Tokens Web JSON e declarações

A plataforma de identidade da Microsoft implementa tokens de segurança como JWTs (tokens Web JSON) que contêm *declarações*. Como JWTs são usados como tokens de segurança, essa forma de autenticação, às vezes, é chamada de *autenticação JWT*.

Uma [declaração](developer-glossary.md#claim) fornece asserções sobre uma entidade, como um aplicativo cliente ou [proprietário do recurso](developer-glossary.md#resource-owner), para outra entidade, como um servidor de recursos. Uma declaração também pode ser referida como uma declaração JWT ou uma declaração de token Web JSON.

As declarações são pares de nome ou valor que retransmitem fatos sobre o assunto do token. Por exemplo, uma declaração pode conter fatos sobre a entidade de segurança que foi autenticada pelo servidor de autorização. As declarações presentes em um token específico dependem de muitas coisas, como o tipo de token, o tipo de credencial usado para autenticar o assunto e a configuração do aplicativo.

Os aplicativos podem usar declarações para várias tarefas, como:

* Valide o token.
* Identifique o [locatário](developer-glossary.md#tenant)da entidade do token.
* Exibir informações do usuário.
* Determine a autorização da entidade.

Uma declaração consiste em pares chave-valor que fornecem informações como:

* Servidor de token de segurança que gerou o token.
* Data em que o token foi gerado.
* Assunto (como o usuário – exceto para daemons).
* Público, que é o aplicativo para o qual o token foi gerado.
* Aplicativo (o cliente) que solicitou o token. No caso de aplicativos Web, esse aplicativo pode ser o mesmo que o público.

Para saber mais sobre como a plataforma de identidade da Microsoft implementa tokens e informações de declaração, consulte [tokens de acesso](access-tokens.md) e [tokens de ID](id-tokens.md).

## <a name="how-each-flow-emits-tokens-and-codes"></a>Como cada fluxo emite tokens e códigos

Dependendo de como o cliente é criado, ele pode usar um (ou vários) dos fluxos de autenticação compatíveis com a plataforma de identidade da Microsoft. Esses fluxos podem produzir vários tokens (tokens de ID, tokens de atualização, tokens de acesso) e códigos de autorização. Eles exigem tokens diferentes para fazê-los funcionar. Esta tabela fornece uma visão geral.

|Flow | Exige | token de ID | Token de acesso | Token de atualização | Código de Autorização |
|-----|----------|----------|--------------|---------------|--------------------|
|[Fluxo do código de autorização](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Fluxo implícito](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Fluxo de OIDC híbrido](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[Resgate de token de atualização](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | Token de atualização | x | x | x| |
|[Fluxo em-nome-de](v2-oauth2-on-behalf-of-flow.md) | Token de acesso| x| x| x| |
|[Credenciais do cliente](v2-oauth2-client-creds-grant-flow.md) | | | x (somente aplicativo)| | |

Tokens emitidos por meio do modo implícito têm uma limitação de comprimento porque são passados de volta para o navegador por meio da URL, onde `response_mode` é `query` ou `fragment` . Alguns navegadores têm um limite no tamanho da URL que pode ser colocado na barra do navegador e falham quando é muito longo. Como resultado, esses tokens não têm `groups` `wids` declarações ou.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre autenticação e autorização na plataforma Microsoft Identity, consulte os seguintes artigos:

* Para saber mais sobre os conceitos básicos de autenticação e autorização, consulte [autenticação vs. autorização](authentication-vs-authorization.md).
* Para saber mais sobre como registrar seu aplicativo para integração, consulte [modelo de aplicativo](application-model.md).
* Para saber mais sobre o fluxo de entrada de aplicativos Web, de área de trabalho e móveis, consulte [fluxo de entrada do aplicativo](app-sign-in-flow.md).
