---
title: Exibir relatório de auditoria para atribuições de grupo de acesso privilegiado no Privileged Identity Management (PIM) – Azure AD | Microsoft Docs
description: Exiba a atividade e o histórico de auditoria para atribuições de grupo de acesso privilegiado no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94dd5c2579eb7ce96dea70c6354df8ec84125bd9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88141662"
---
# <a name="audit-activity-history-for-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Histórico de atividades de auditoria para atribuições de grupo de acesso privilegiado (versão prévia) no Privileged Identity Management

Com o PIM (Privileged Identity Management), você pode exibir a atividade, as ativações e o histórico de auditoria para membros e proprietários do grupo de acesso privilegiado do Azure em sua organização do Azure Active Directory (Azure AD).

> [!NOTE]
> Se sua organização tiver funções de gerenciamento terceirizadas para um provedor de serviços que usa o [Gerenciamento de recursos delegado do Azure](../../lighthouse/concepts/azure-delegated-resource-management.md), as atribuições de função autorizadas por esse provedor de serviços não serão mostradas aqui.

Siga estas etapas para exibir o histórico de auditoria para grupos de acesso privilegiado.

## <a name="view-resource-audit-history"></a>Exibir o histórico de auditoria de recursos

A **auditoria de recursos** fornece uma exibição de todas as atividades associadas aos seus grupos de acesso privilegiado.

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **grupos de acesso privilegiado (versão prévia)**.

1. Selecione o grupo de acesso privilegiado para o qual você deseja exibir o histórico de auditoria.

1. Em **atividade**, selecione **recurso auditoria**.

1. Filtre o histórico usando uma data predefinida ou um intervalo personalizado.

    ![Lista de auditoria de recursos com filtros](media/groups-audit/groups-resource-audit.png)

## <a name="view-my-audit"></a>Exibir minha auditoria

**Minha auditoria** permite que você exiba sua atividade de função pessoal para um grupo de acesso privilegiado.

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **grupos de acesso privilegiado (versão prévia)**.

1. Selecione o grupo de acesso privilegiado para o qual você deseja exibir o histórico de auditoria.

1. Em **atividade**, selecione **minha auditoria**.

1. Filtre o histórico usando uma data predefinida ou um intervalo personalizado.

    ![Lista de auditoria para o usuário atual](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Próximas etapas

- [Atribuir Associação de grupo e propriedade no Privileged Identity Management](groups-assign-member-owner.md)
- [Aprovar ou negar solicitações de grupos de acesso privilegiado no PIM](groups-approval-workflow.md)
- [Exibir histórico de auditoria para funções do Azure AD no PIM](groups-audit.md)
