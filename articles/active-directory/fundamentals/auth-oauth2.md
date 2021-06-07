---
title: Autenticação OAUTH 2,0 com Azure Active Directory
description: Diretrizes arquitetônicas sobre a obtenção de autenticação OAUTH 2,0 com Azure Active Directory.
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
ms.openlocfilehash: c1604d79ce5eb9949028cd677b340bf3d4b09f6c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96172832"
---
# <a name="oauth-20-authentication-with-azure-active-directory"></a>Autenticação OAuth 2,0 com Azure Active Directory

O OAuth 2,0 é o protocolo do setor para autorização. Ele permite que um usuário conceda acesso limitado aos seus recursos protegidos. Projetado para funcionar especificamente com HTTP (Hypertext Transfer Protocol), o OAuth separa a função do cliente do proprietário do recurso. O cliente solicita acesso aos recursos controlados pelo proprietário do recurso e hospedado pelo servidor de recursos. O servidor de recursos emite tokens de acesso com a aprovação do proprietário do recurso. O cliente usa os tokens de acesso para acessar os recursos protegidos hospedados pelo servidor de recursos. 

O OAuth 2,0 está diretamente relacionado ao OpenID Connect (OIDC). Como o OIDC é uma camada de autenticação e autorização criada com base no OAuth 2,0, ele não é compatível com versões anteriores do OAuth 1,0. O Azure Active Directory (AD do Azure) dá suporte a todos os fluxos do OAuth 2,0. 

## <a name="use-when"></a>Use quando:

Para clientes avançados & cenários de aplicativos modernos e acesso à API Web RESTful.

![Diagrama de arquitetura](./media/authentication-patterns/oauth.png)

## <a name="components-of-system"></a>Componentes do sistema

* **Usuário**: solicita um serviço do aplicativo Web (aplicativo). O usuário normalmente é o proprietário do recurso que possui os dados e tem o poder de permitir que os clientes acessem os dados ou recursos. 

* **Navegador da Web**: o navegador da Web com o qual o usuário interage é o cliente OAuth. 

* **Aplicativo Web**: o aplicativo Web ou o servidor de recursos é onde residem o recurso ou os dados. Ele confia no servidor de autorização para autenticar e autorizar o cliente OAuth com segurança. 

* **Azure ad**: o Azure AD é o servidor de autorização, também conhecido como IDP (provedor de identidade). Ele manipula com segurança tudo o que fazer com as informações do usuário, seu acesso e a relação de confiança. É responsável por emitir os tokens que concedem e revogam o acesso aos recursos.

## <a name="implement-oauth-20-with-azure-ad"></a>Implementar o OAuth 2,0 com o Azure AD

* [Integrando aplicativos com o AD do Azure](../saas-apps/tutorial-list.md) 

* [Protocolos OAuth 2,0 e OpenID Connect na plataforma de identidade da Microsoft](../develop/active-directory-v2-protocols.md) 

* [Tipos de aplicativos e OAuth2](../develop/v2-app-types.md) 

