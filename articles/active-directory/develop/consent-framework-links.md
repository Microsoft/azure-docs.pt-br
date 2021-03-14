---
title: Como o consentimento de aplicativo funciona
description: Saiba mais sobre como a estrutura de consentimento do Azure AD funciona para ver como você pode usá-la ao desenvolver aplicativos no Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 9fa910dee2830f6749f0fbd36f065c31dafa6757
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646241"
---
# <a name="how-application-consent-works"></a>Como o consentimento de aplicativo funciona

Este artigo o ajuda a aprender mais sobre como a estrutura de consentimento do Azure AD funciona para que você possa desenvolver aplicativos com mais eficiência.

## <a name="recommended-documents"></a>Documentos recomendados

- Tenha uma compreensão geral de [como o consentimento permite que o proprietário do recurso controle o acesso de um aplicativo a recursos](./developer-glossary.md#consent).
- Tenha uma visão geral passo a passo de [como a estrutura de consentimento do Azure AD implementa o consentimento](./quickstart-register-app.md).
- Para se aprofundar, veja [como um aplicativo multilocatário pode usar a estrutura de consentimento](./howto-convert-app-to-be-multi-tenant.md) para implementar consentimento de "usuário" e "administrador", dando suporte a padrões mais avançados de aplicativos de várias camadas.
- Para se aprofundar, veja [como o consentimento tem suporte na camada de protocolo OAuth 2.0 durante o fluxo de concessão de código de autorização.](../azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code)

## <a name="next-steps"></a>Próximas etapas
[AzureAD Microsoft Q&A](/answers/topics/azure-active-directory.html)