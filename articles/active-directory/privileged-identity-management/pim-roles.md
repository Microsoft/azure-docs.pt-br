---
title: Funções que você não pode gerenciar em Privileged Identity Management Azure Active Directory | Microsoft Docs
description: Descreve as funções que não podem ser gerenciadas no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 05/11/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: c72587c5486ed61215fd20c215a1dd194f4b7bc4
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92372405"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Funções que você não pode gerenciar no Privileged Identity Management

O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) permite que você gerencie todas as [funções do Azure ad](../roles/permissions-reference.md) e todas as [funções do Azure](../../role-based-access-control/built-in-roles.md). As funções do Azure também podem incluir suas funções personalizadas anexadas aos grupos de gerenciamento, assinaturas, grupos de recursos e recursos. No entanto, há algumas poucas funções que não podem ser gerenciadas. Este artigo descreve as funções que você não pode gerenciar no Privileged Identity Management.

## <a name="classic-subscription-administrator-roles"></a>Funções de administrador de assinatura Clássico

Você não pode gerenciar as seguintes funções de administrador de assinatura clássica no Privileged Identity Management:

- Administrador de conta
- Administrador de serviços
- Coadministrador

Para obter mais informações sobre as funções de administrador de assinatura clássica, consulte [funções de administrador de assinatura clássica, funções do Azure e funções de administrador do Azure ad](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-microsoft-365-admin-roles"></a>E quanto Microsoft 365 funções de administrador?

Damos suporte a todas as funções de Microsoft 365 na experiência do portal de funções e administradores do Azure AD, como administrador do Exchange e administrador do SharePoint, mas não damos suporte a funções específicas no RBAC do Exchange ou no RBAC do SharePoint. Para obter mais informações sobre esses serviços de Microsoft 365, consulte [Microsoft 365 funções de administrador](/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Usuários qualificados para a função de administrador do SharePoint, a função de administrador do dispositivo e todas as funções que tentam acessar o centro de conformidade e segurança da Microsoft podem enfrentar atrasos de até algumas horas após a ativação de sua função. Estamos trabalhando com essas equipes para corrigir os problemas.

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções do Azure AD no Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Atribuir funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-assign-roles.md)