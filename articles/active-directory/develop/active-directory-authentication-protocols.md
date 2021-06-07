---
title: Protocolos de autenticação da plataforma Microsoft Identity
description: Uma visão geral dos protocolos de autenticação com suporte na plataforma de identidade da Microsoft
author: rwike77
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ROBOTS: NOINDEX
ms.openlocfilehash: 803f52666fb09a836eae3294410584ebbd71a1b6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755888"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Protocolos de autenticação da plataforma Microsoft Identity

A plataforma de identidade da Microsoft dá suporte a vários dos protocolos de autenticação e autorização mais amplamente usados. Os tópicos nesta seção descrevem os protocolos com suporte e sua implementação na plataforma Microsoft Identity. Os tópicos incluem uma revisão dos tipos de declarações com suporte, uma introdução ao uso de metadados de federação, documentação detalhada de referência dos protocolos OAuth 2.0. e SAML 2.0 e uma seção de solução de problemas.

## <a name="authentication-protocols-articles-and-reference"></a>Artigos e referência de protocolos de autenticação

* [Informações importantes sobre a substituição de chave de assinatura na plataforma de identidade da Microsoft](active-directory-signing-key-rollover.md) – saiba mais sobre a cadência da substituição de chave de assinatura da plataforma de identidade da Microsoft, as alterações que você pode fazer para atualizar a chave automaticamente e discussão sobre como atualizar os cenários de aplicativos mais comuns.
* [Tipos de declaração e token com suporte](id-tokens.md) -saiba mais sobre as declarações nos tokens que a plataforma de identidade da Microsoft emite.
* [Oauth 2,0 na plataforma de identidade da Microsoft](v2-oauth2-auth-code-flow.md) -saiba mais sobre a implementação do OAuth 2,0 na plataforma Microsoft Identity.
* [OpenID Connect 1.0](v2-protocols-oidc.md) - Saiba como usar o OAuth 2.0, um protocolo de autorização para autenticação.
* [Serviço para Chamadas de Serviço com Credenciais do Cliente](v2-oauth2-client-creds-grant-flow.md) – Saiba como utilizar o fluxo de concessão de credenciais de cliente OAuth 2.0 para chamadas de serviços.
* [Serviço para Chamadas de Serviço com Fluxo Em Nome De](v2-oauth2-on-behalf-of-flow.md) – Saiba como utilizar o fluxo de Em Nome De OAuth 2.0 para chamadas de serviços.
* [Referência de protocolo SAML](active-directory-saml-protocol-reference.md) -saiba mais sobre o único Sign-On e perfis SAML de logout único da plataforma de identidade da Microsoft.

## <a name="see-also"></a>Veja também

* [Visão geral da plataforma de identidade da Microsoft](v2-overview.md)
* [Exemplos de código do Active Directory](sample-v2-code.md)
