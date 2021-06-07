---
title: Autenticação vs. autorização | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre os conceitos básicos de autenticação e autorização na plataforma Microsoft Identity.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 5244a8e572abb56c00d87d0bdd7e8d1291af9b9e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99581900"
---
# <a name="authentication-vs-authorization"></a>Autenticação versus autorização

Este artigo define a autenticação e a autorização. Ele também aborda brevemente como você pode usar a plataforma de identidade da Microsoft para autenticar e autorizar usuários em seus aplicativos Web, APIs Web ou aplicativos que chamam APIs da Web protegidas. Se você vir um termo com o qual não está familiarizado, experimente nosso [Glossário](developer-glossary.md) ou nossos [vídeos da plataforma de identidade da Microsoft](identity-videos.md), que abrangem conceitos básicos.

## <a name="authentication"></a>Autenticação

A *autenticação* é o processo de provar que você é quem diz que está. Às vezes, ele é reduzido para *Authn*. A plataforma de identidade da Microsoft usa o protocolo [OpenID Connect](https://openid.net/connect/) para lidar com a autenticação.

## <a name="authorization"></a>Autorização

*Autorização* é o ato de conceder a uma parte autenticada a permissão para fazer algo. Ele especifica quais dados você tem permissão para acessar e o que pode fazer com esses dados. Às vezes, a autorização é reduzida para *AuthZ*. A plataforma de identidade da Microsoft usa o protocolo [OAuth 2,0](https://oauth.net/2/) para lidar com a autorização.

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>Autenticação e autorização usando a plataforma de identidade da Microsoft

A criação de aplicativos que mantêm suas próprias informações de nome de usuário e senha incorre em uma carga administrativa alta ao adicionar ou remover usuários em vários aplicativos. Em vez disso, seus aplicativos podem delegar essa responsabilidade a um provedor de identidade centralizado.

O Azure AD (Azure Active Directory) é um provedor de identidade centralizado na nuvem. A delegação de autenticação e autorização para ela permite cenários como:

- Políticas de acesso condicional que exigem que um usuário esteja em um local específico.
- O uso da [autenticação multifator](../authentication/concept-mfa-howitworks.md), que às vezes é chamado de autenticação de dois fatores ou 2FA.
- Permitir que um usuário entre uma vez e seja automaticamente conectado a todos os aplicativos Web que compartilham o mesmo diretório centralizado. Esse recurso é chamado de *SSO (logon único)*.

A plataforma de identidade da Microsoft simplifica a autorização e a autenticação para desenvolvedores de aplicativos, fornecendo identidade como um serviço. Ele dá suporte a protocolos padrão do setor e bibliotecas de software livre para diferentes plataformas para ajudá-lo a começar a codificar rapidamente. Ele permite aos desenvolvedores criar aplicativos que se conectam a todas as identidades da Microsoft, obter tokens para chamar [Microsoft Graph](https://developer.microsoft.com/graph/), acessar APIs da Microsoft ou acessar outras APIs que os desenvolvedores criaram.

Este vídeo explica a plataforma de identidade da Microsoft e os conceitos básicos da autenticação moderna: 

> [!VIDEO https://www.youtube.com/embed/tkQJSHFsduY]

Aqui está uma comparação dos protocolos que a plataforma Microsoft Identity usa:

* **OAuth versus OpenID Connect**: a plataforma usa OAuth para autorização e OpenID Connect (OIDC) para autenticação. O OpenID Connect é desenvolvido com base no OAuth 2.0, portanto, a terminologia e o fluxo são semelhantes entre os dois. Você pode até mesmo autenticar um usuário (por meio do OpenID Connect) e obter autorização para acessar um recurso protegido que o usuário possui (por meio do OAuth 2,0) em uma solicitação. Para saber mais, confira [Protocolos OAuth 2.0 e OpenID Connect](active-directory-v2-protocols.md) e [Protocolo OpenID Connect](v2-protocols-oidc.md).
* **OAuth versus SAML**: a plataforma usa o OAuth 2,0 para autorização e SAML para autenticação. Para obter mais informações sobre como usar esses protocolos em conjunto para autenticar um usuário e obter autorização para acessar um recurso protegido, consulte [plataforma de identidade da Microsoft e fluxo de declaração de portador SAML 2,0 do OAuth](./scenario-token-exchange-saml-oauth.md).
* **OpenID Connect versus SAML**: a plataforma usa o OpenID Connect e o SAML para autenticar um usuário e habilitar o logon único. A autenticação SAML é comumente usada com provedores de identidade, como Serviços de Federação do Active Directory (AD FS) (AD FS) federado ao Azure AD, para que ele seja usado com frequência em aplicativos empresariais. O OpenID Connect é normalmente usado para aplicativos que são puramente na nuvem, como aplicativos móveis, sites e APIs da Web.

## <a name="next-steps"></a>Próximas etapas

Para outros tópicos que abrangem noções básicas de autenticação e autorização:

* Para saber como tokens de acesso, tokens de atualização e tokens de ID são usados na autorização e autenticação, consulte [tokens de segurança](security-tokens.md).
* Para saber mais sobre o processo de registro de seu aplicativo para que ele possa ser integrado à plataforma de identidade da Microsoft, consulte [modelo de aplicativo](application-model.md).