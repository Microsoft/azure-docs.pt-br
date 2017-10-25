---
title: Gerenciar o acesso a recursos do Azure com o Azure Active Directory
description: "Conheça as maneiras de gerenciar o acesso aos recursos do Azure usando diferentes recursos do Azure Active Directory."
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: bryanla
ms.assetid: f66abf54-3809-436c-92b6-018e1179780e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2017
ms.author: skwan
ms.openlocfilehash: e42bbed0a9e37a6632b2a71d2b3546fcbc38cdc7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-access-to-azure-resources-with-azure-active-directory"></a>Gerenciar o acesso a recursos do Azure com o Azure Active Directory

O gerenciamento de identidade e de acesso para recursos de nuvem é uma função crítica para qualquer organização que esteja usando a nuvem. O Azure AD (Azure Active Directory) é o sistema de identidade e de acesso para o Microsoft Azure.  

Antes de explorar as áreas de recurso de suporte do Azure AD, confira o vídeo a seguir: "Bloquear o acesso à nuvem do Azure usando SSO, Controle de Acesso Baseado em Funções e Condicional". Nele, você aprenderá:

- Práticas recomendadas para configurar o logon único ao portal do Azure com o Active Directory local.
- Usar o RBAC do Azure para controle de acesso refinado para recursos em assinaturas.
- Impor regras de autenticação de alta segurança usando o Acesso Condicional do Azure AD.
- O conceito de Identidade do Serviço Gerenciado, em que os recursos do Azure podem autenticar-se automaticamente para os serviços do Azure e os desenvolvedores não precisam lidar com chaves nem segredos de API.

> [!VIDEO https://www.youtube.com/embed/FKBoWWKRnvI]

## <a name="feature-areas"></a>Áreas de recursos
O Azure AD fornece os seguintes recursos para gerenciar o acesso aos recursos do Azure:

|||
|---|---|
| [Relação entre locatários e assinaturas do Azure AD](active-directory-understanding-resource-access.md) | Saiba como o Azure AD é a fonte confiável de usuários e grupos para uma assinatura do Azure. |
| [RBAC (Controle de Acesso Baseado em Função)](role-based-access-control-what-is.md) | Ofereça gerenciamento de acesso refinado por meio de funções atribuídas a usuários, grupos ou entidades de serviço. |
| [Privileged Identity Management com RBAC](pim-azure-resource.md) | Controle o acesso altamente privilegiado atribuindo funções com privilégios Just-In-Time. |
| [Acesso condicional para o gerenciamento do Azure](conditional-access-azure-management.md) | Configure políticas de acesso condicional para permitir ou bloquear o acesso a pontos de extremidade de gerenciamento do Azure. |
| [MSI (Identidade do Serviço Gerenciado)](msi-overview.md) | Forneça aos recursos do Azure, como Máquinas Virtuais, a respectiva identidade para que você não precise colocar credenciais em seu código. |

 