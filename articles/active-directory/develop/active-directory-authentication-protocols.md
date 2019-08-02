---
title: Protocolos de autenticação do Azure Active Directory | Microsoft Docs
description: Uma visão geral dos protocolos de autenticação suportados pelo Azure Active Directory (AD)
documentationcenter: dev-center-name
author: rwike77
services: active-directory
manager: CelesteDG
editor: ''
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: f964c5882432ae0637039e32ca961008e8223b6b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612580"
---
# <a name="azure-active-directory-authentication-protocols"></a>Protocolos de autenticação do Azure Active Directory
O Azure Active Directory (AD do Azure) oferece suporte a vários dos protocolos de autenticação e autorização mais usados. Os tópicos nesta seção descrevem os protocolos suportados e sua implementação no Azure AD. Os tópicos incluem uma revisão dos tipos de declarações com suporte, uma introdução ao uso de metadados de federação, documentação detalhada de referência dos protocolos OAuth 2.0. e SAML 2.0 e uma seção de solução de problemas.

## <a name="authentication-protocols-articles-and-reference"></a>Artigos e referência dos protocolos de autenticação
* [Informações importantes sobre substituição de chave de assinatura no Azure AD](active-directory-signing-key-rollover.md) – Saiba mais sobre a cadência de substituição de chave de assinatura no Azure AD, as alterações que você pode fazer para atualizar a chave automaticamente e discussões sobre como atualizar os cenários mais comuns do aplicativo.
* [Token Suportado e Tipos de Declaração](v1-id-and-access-tokens.md) - Saiba mais sobre as declarações nos tokens que o Azure AD emite.
* [Metadados de federação](azure-ad-federation-metadata.md) - Saiba como localizar e interpretar os documentos de metadados que o Azure AD gera.
* [OAuth 2.0 no Azure AD](v1-protocols-oauth-code.md) - Saiba mais sobre a implementação do OAuth 2.0 no Azure AD.
* [OpenID Connect 1.0](v1-protocols-openid-connect-code.md) - Saiba como usar o OAuth 2.0, um protocolo de autorização para autenticação.
* [Serviço para Chamadas de Serviço com Credenciais do Cliente](v1-oauth2-client-creds-grant-flow.md) – Saiba como utilizar o fluxo de concessão de credenciais de cliente OAuth 2.0 para chamadas de serviços.
* [Serviço para Chamadas de Serviço com Fluxo Em Nome De](v1-oauth2-on-behalf-of-flow.md) – Saiba como utilizar o fluxo de Em Nome De OAuth 2.0 para chamadas de serviços.
* [Referência de protocolo SAML](active-directory-saml-protocol-reference.md) - Saiba mais sobre os perfis de logon único e logout único SAML do Azure AD.

## <a name="see-also"></a>Consulte também
[Guia do desenvolvedor do Active Directory do Azure](v1-overview.md)

[Exemplos de código do Active Directory](sample-v1-code.md)
