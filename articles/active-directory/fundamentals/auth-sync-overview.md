---
title: Visão geral do protocolo de autenticação e sincronização do Azure Active Directory
description: Diretrizes arquitetônicas sobre a integração do Azure AD com protocolos de autenticação herdados e padrões de sincronização
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
ms.openlocfilehash: ab63bc5bd2819a239741da525eebb2404a47bbf9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92441191"
---
# <a name="azure-active-directory-integrations-with-authentication-and-synchronization-protocols"></a>Azure Active Directory integrações com protocolos de autenticação e sincronização

O Microsoft Azure Active Directory (AD do Azure) permite a integração com muitos protocolos de autenticação e sincronização. As integrações de autenticação permitem que você use o Azure AD e seus recursos de segurança e gerenciamento com pouca ou nenhuma alteração em seus aplicativos que usam métodos de autenticação herdados. As integrações de sincronização permitem que você sincronize os dados do usuário e do grupo com o Azure AD e, em seguida, os recursos de gerenciamento do Azure AD. Alguns padrões de sincronização também habilitam o provisionamento automatizado.

## <a name="legacy-authentication-protocols"></a>Protocolos de autenticação herdados

A tabela a seguir apresenta autenticação de integração do Azure AD com protocolos de autenticação herdados e seus recursos. Selecione o nome de um protocolo de autenticação para ver

* Uma descrição detalhada

* Quando usar isso

* Diagrama de arquitetura

* Explicação dos componentes do sistema

* Links para como implementar a integração

 

| Protocolo de autenticação| Autenticação| Autorização| Autenticação Multifator| Acesso condicional |
| - |- | - | - | - |
| [Autenticação baseada em cabeçalho](auth-header-based.md)|![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png) |
| [Autenticação LDAP](auth-ldap.md)| ![marca de seleção](./media/authentication-patterns/check.png)| | |  |
| [Autenticação OAuth 2.0](auth-oauth2.md)| ![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png) |
| [Autenticação OIDC](auth-oidc.md)| ![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png) |
| [Autenticação SSO baseada em senha](auth-password-based-sso.md )| ![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png) |
| [Autenticação RADIUS]( auth-radius.md)| ![marca de seleção](./media/authentication-patterns/check.png)| | ![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png) |
| [Serviços de gateway Área de Trabalho Remota](auth-remote-desktop-gateway.md)| ![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png) |
| [Secure Shell (SSH)](auth-ssh.md) |  ![marca de seleção](./media/authentication-patterns/check.png)| | ![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png) |
| [Autenticação SAML](auth-saml.md)| ![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png) |
| [Autenticação do Windows-delegação restrita de Kerberos](auth-kcd.md)| ![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png) |


 
## <a name="synchronization-patterns"></a>Padrões de sincronização

A tabela a seguir apresenta a integração do Azure AD com padrões de sincronização e seus recursos. Selecione o nome de um padrão para ver

* Uma descrição detalhada

* Quando usar isso

* Diagrama de arquitetura

* Explicação dos componentes do sistema

* Links para como implementar a integração



| Padrão de sincronização| Sincronização de diretório| Provisionamento do usuário |
| - | - | - |
| [Sincronização de diretório](sync-directory.md)| ![marca de seleção](./media/authentication-patterns/check.png)|  |
| [Sincronização de LDAP](sync-ldap.md)| ![marca de seleção](./media/authentication-patterns/check.png)|  |
| [Sincronização do SCIM](sync-scim.md)| ![marca de seleção](./media/authentication-patterns/check.png)| ![marca de seleção](./media/authentication-patterns/check.png) |

