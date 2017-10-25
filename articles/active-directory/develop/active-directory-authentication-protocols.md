---
title: "Protocolos de Autenticação do Azure Active Directory | Microsoft Docs"
description: "Uma visão geral dos protocolos de autenticação suportados pelo Azure Active Directory (AD)"
documentationcenter: dev-center-name
author: priyamohanram
services: active-directory
manager: mbaldwin
editor: 
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: priyamo
ms.custom: aaddev
ms.openlocfilehash: e106a9bdf28243dd829b6a014b73c148809c1bde
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# Protocolos de autenticação do Azure Active Directory
O Azure Active Directory (AD do Azure) oferece suporte a vários dos protocolos de autenticação e autorização mais usados. Os tópicos nesta seção descrevem os protocolos suportados e sua implementação no Azure AD. Os tópicos incluem uma revisão dos tipos de declarações com suporte, uma introdução ao uso de metadados de federação, documentação detalhada de referência dos protocolos OAuth 2.0. e SAML 2.0 e uma seção de solução de problemas.

## Artigos e referência dos protocolos de autenticação
* [Informações importantes sobre substituição de chave de assinatura no Azure AD](active-directory-signing-key-rollover.md) – Saiba mais sobre a cadência de substituição de chave de assinatura no Azure AD, as alterações que você pode fazer para atualizar a chave automaticamente e discussões sobre como atualizar os cenários mais comuns do aplicativo.
* [Token Suportado e Tipos de Declaração](active-directory-token-and-claims.md) - Saiba mais sobre as declarações nos tokens que o Azure AD emite.
* [Metadados de federação](active-directory-federation-metadata.md) - Saiba como localizar e interpretar os documentos de metadados que o Azure AD gera.
* [OAuth 2.0 no Azure AD](active-directory-protocols-oauth-code.md) - Saiba mais sobre a implementação do OAuth 2.0 no Azure AD.
* [OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) - Saiba como usar o OAuth 2.0, um protocolo de autorização para autenticação.
* [Serviço para Chamadas de Serviço com Credenciais do Cliente](active-directory-protocols-oauth-service-to-service.md) – Saiba como utilizar o fluxo de concessão de credenciais de cliente OAuth 2.0 para chamadas de serviços.
* [Serviço para Chamadas de Serviço com Fluxo Em Nome De](active-directory-protocols-oauth-on-behalf-of.md) – Saiba como utilizar o fluxo de Em Nome De OAuth 2.0 para chamadas de serviços.
* [Referência de protocolo SAML](active-directory-saml-protocol-reference.md) - Saiba mais sobre os perfis de logon único e logout único SAML do Azure AD.

## Consulte também
[Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md)

[Exemplos de código do Active Directory](active-directory-code-samples.md)
