---
title: Autenticação do OpenID Connect com o Azure Active Directory
description: Diretrizes arquitetônicas sobre como obter a autenticação do OpenID Connect com o Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e5bf7e51de38d42e64f6737e687c5946a464160
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168654"
---
# <a name="openid-connect-authentication-with-azure-active-directory"></a>Autenticação do OpenID Connect com o Azure Active Directory

O OpenID Connect (OIDC) é um protocolo de autenticação baseado no protocolo OAuth2 (que é usado para autorização). O OIDC usa os fluxos de mensagens padronizados do OAuth2 para fornecer serviços de identidade. 

O objetivo de design do OIDC é "tornar simples as coisas simples e complicadas possíveis". O OIDC permite que os desenvolvedores autentiquem seus usuários entre sites e aplicativos sem ter que possuir e gerenciar arquivos de senha. Isso fornece ao construtor de aplicativos uma maneira segura de verificar a identidade da pessoa que está usando o navegador ou o aplicativo nativo que está conectado ao aplicativo.

A autenticação do usuário deve ocorrer em um provedor de identidade em que a sessão ou as credenciais do usuário serão verificadas. Para fazer isso, você precisa de um agente confiável. Aplicativos nativos geralmente iniciam o navegador do sistema para essa finalidade. Exibições inseridas são consideradas não confiáveis, pois não há nada para impedir que o aplicativo seja interceptado na senha do usuário. 

Além da autenticação, o usuário pode ser solicitado a fornecer consentimento. Consentimento é a permissão explícita do usuário para permitir que um aplicativo acesse recursos protegidos. O consentimento é diferente da autenticação porque o consentimento só precisa ser fornecido uma vez para um recurso. O consentimento permanece válido até que o usuário ou administrador revogue manualmente a concessão. 

## <a name="use-when"></a>Usar quando

Há uma necessidade de consentimento do usuário e de entrada na Web.

![diagrama arquitetônico](./media/authentication-patterns/oidc-auth.png)

## <a name="components-of-system"></a>Componentes do sistema

* **Usuário**: solicita um serviço do aplicativo.

* **Agente confiável**: o componente com o qual o usuário interage. Esse agente confiável geralmente é um navegador da Web.

* **Aplicativo**: o aplicativo, ou servidor de recursos, é onde residem o recurso ou os dados. Ele confia no provedor de identidade para autenticar e autorizar com segurança o agente confiável. 

* **Azure ad**: o provedor OIDC, também conhecido como provedor de identidade, gerencia com segurança tudo o que fazer com as informações do usuário, seu acesso e as relações de confiança entre as partes em um fluxo. Ele autentica a identidade do usuário, concede e revoga o acesso a recursos e emite tokens. 

## <a name="implement-oidc-with-azure-ad"></a>Implementar o OIDC com o Azure AD

* [Integrando aplicativos com o AD do Azure](../saas-apps/tutorial-list.md) 

* [Protocolos OAuth 2,0 e OpenID Connect na plataforma de identidade da Microsoft](../develop/active-directory-v2-protocols.md) 

* [Plataforma de identidade da Microsoft e protocolo OpenID Connect](../develop/v2-protocols-oidc.md) 

* [Entrada na Web com o OpenID Connect no Azure Active Directory B2C](../../active-directory-b2c/openid-connect.md) 

* [Proteja seu aplicativo usando o OpenID Connect e o Azure AD](/learn/modules/secure-app-with-oidc-and-azure-ad/) 

