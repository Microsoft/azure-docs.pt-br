---
title: Cenário de troca de token da plataforma de identidade da Microsoft com SAML e OIDC/OAuth no Azure Active Directory
description: Saiba mais sobre cenários comuns de troca de tokens ao trabalhar com SAML e OIDC/OAuth no Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.openlocfilehash: 5841af4225f88b36fe727b60a597af208ac830f5
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97827165"
---
# <a name="microsoft-identity-platform-token-exchange-scenarios-with-saml-and-oidcoauth"></a>Cenários de troca de tokens da plataforma Microsoft Identity com SAML e OIDC/OAuth

SAML e OpenID Connect (OIDC)/OAuth são protocolos populares usados para implementar um único Sign-On (SSO). Alguns aplicativos podem implementar apenas SAML e outros podem implementar apenas OIDC/OAuth. Ambos os protocolos usam tokens para comunicar segredos. Para saber mais sobre o SAML, consulte [protocolo SAML de Sign-On único](single-sign-on-saml-protocol.md). Para saber mais sobre o OIDC/OAuth, consulte [protocolos OAuth 2,0 e OpenID Connect na plataforma de identidade da Microsoft](active-directory-v2-protocols.md).

Este artigo descreve um cenário comum em que um aplicativo implementa SAML, mas você precisa chamar o API do Graph, que usa OIDC/OAuth. As diretrizes básicas são fornecidas para pessoas que trabalham com esse cenário.

## <a name="scenario-you-have-a-saml-token-and-want-to-call-the-graph-api"></a>Cenário: você tem um token SAML e deseja chamar o API do Graph
Muitos aplicativos são implementados com o SAML. No entanto, o API do Graph usa os protocolos OIDC/OAuth. É possível, embora não trivial, adicionar a funcionalidade OIDC/OAuth a um aplicativo SAML. Depois que a funcionalidade do OAuth estiver disponível em um aplicativo, a API do Graph poderá ser usada.

A estratégia geral é adicionar a pilha OIDC/OAuth ao seu aplicativo. Com seu aplicativo que implementa os dois padrões, você pode usar um cookie de sessão. Você não está trocando um token explicitamente. Você está registrando um usuário no SAML, que gera um cookie de sessão. Quando o API do Graph invoca um fluxo OAuth, você usa o cookie de sessão para autenticar. Essa estratégia pressupõe que as verificações de acesso condicional são aprovadas e o usuário está autorizado.

> [!NOTE]
> A biblioteca recomendada para adicionar o comportamento OIDC/OAuth é a MSAL (biblioteca de autenticação da Microsoft). Para saber mais sobre o MSAL, consulte [visão geral da biblioteca de autenticação da Microsoft (MSAL)](msal-overview.md). A biblioteca anterior foi chamada de Biblioteca de Autenticação do Active Directory (ADAL), mas não é recomendável, pois MSAL está substituindo-a.

## <a name="next-steps"></a>Próximas etapas
- [Fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md)
