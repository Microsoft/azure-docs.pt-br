---
title: Suporte a AD FS (MSAL para Java)
titleSuffix: Microsoft identity platform
description: Saiba mais sobre o suporte a Serviços de Federação do Active Directory (AD FS) (AD FS) na biblioteca de autenticação da Microsoft para Java (MSAL4j).
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 82e3f3b32c6d96b83ec1d0402f344e7d65788c06
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063698"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>Suporte a Serviços de Federação do Active Directory (AD FS) no MSAL para Java

O Serviços de Federação do Active Directory (AD FS) (AD FS) no Windows Server permite adicionar o OpenID Connect e a autenticação e autorização baseadas no OAuth 2,0 ao aplicativo da biblioteca de autenticação da Microsoft para Java (MSAL para Java). Uma vez integrado, seu aplicativo pode autenticar usuários no AD FS, federado por meio do Azure AD. Para obter mais informações sobre cenários, consulte [cenários de AD FS para desenvolvedores](/windows-server/identity/ad-fs/ad-fs-development).

Um aplicativo que usa MSAL para Java se comunicará com Azure Active Directory (Azure AD), que, em seguida, federará para AD FS.

O MSAL para Java se conecta ao Azure AD, que entra em usuários que são gerenciados no Azure AD (usuários gerenciados) ou usuários gerenciados por outro provedor de identidade, como AD FS (usuários federados). O MSAL para Java não sabe que um usuário é federado. Ele simplesmente se comunica com o Azure AD.

A [autoridade](msal-client-application-configuration.md#authority) que você usa nesse caso é a autoridade usual (nome do host da autoridade + locatário, comum ou organizações).

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>Adquirir um token interativamente para um usuário federado

Quando você chama `ConfidentialClientApplication.AcquireToken()` o `PublicClientApplication.AcquireToken()` ou `AuthorizationCodeParameters` `DeviceCodeParameters` o ou o, a experiência do usuário normalmente é:

1. O usuário insere a ID da conta.
2. O Azure AD exibe brevemente "levando você para a página da sua organização", e o usuário é redirecionado para a página de entrada do provedor de identidade. A página de entrada geralmente é personalizada com o logotipo da organização.

As versões de AD FS com suporte nesse cenário federado são:
- Serviços de Federação do Active Directory (AD FS) FS v2
- Serviços de Federação do Active Directory (AD FS) v3 (Windows Server 2012 R2)
- Serviços de Federação do Active Directory (AD FS) V4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>Adquirir um token por nome de usuário e senha

Quando você adquire um token usando `ConfidentialClientApplication.AcquireToken()` ou `PublicClientApplication.AcquireToken()` com `IntegratedWindowsAuthenticationParameters` ou `UsernamePasswordParameters` , MSAL para Java Obtém o provedor de identidade para entrar em contato com base no nome de usuário. MSAL para Java Obtém um token de [token 1,1 SAML](reference-saml-tokens.md) do provedor de identidade, que ele fornece ao Azure AD, que retorna o JWT (token Web JSON).

## <a name="next-steps"></a>Próximas etapas

Para o caso federado, consulte [Configurar o comportamento de entrada Azure Active Directory para um aplicativo usando uma política de descoberta de realm inicial](../manage-apps/configure-authentication-for-federated-users-portal.md)