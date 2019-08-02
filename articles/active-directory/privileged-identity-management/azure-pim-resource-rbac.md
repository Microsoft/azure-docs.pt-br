---
title: Exibir histórico de atividade e auditoria para funções de recurso do Azure no PIM - Azure Active Directory | Microsoft Docs
description: Exiba a atividade e o histórico de auditoria para funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84bd491d992ed15df288d9226b58bfe832e0692a
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476507"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-pim"></a>Exibir a atividade e o histórico de auditoria para funções de recurso do Azure no PIM

Com o Azure AD (Azure Active Directory) PIM (Privileged Identity Management), você pode exibir a atividade, as ativações e o histórico de auditoria para funções de recursos do Azure em sua organização. Isso inclui assinaturas, grupos de recursos e até mesmo máquinas virtuais. Todos os recursos do portal do Azure que utilizam a funcionalidade RBAC (controle de acesso baseado em função) do Azure podem aproveitar todas as funcionalidades de segurança e gerenciamento do ciclo de vida do PIM.

## <a name="view-activity-and-activations"></a>Exibir a atividade e as ativações

Para ver as ações que um usuário específico realizou em vários recursos, você pode exibir a atividade do recurso do Azure associada a um período de ativação especificado.

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **Recursos do Azure**.

1. Clique no recurso para o qual deseja exibir a atividade e as ativações.

1. Clique em **Funções** ou **Membros**.

1. Clique em um usuário.

    Você verá uma exibição gráfica das ações do usuário em recursos do Azure por data. Ele também mostra as ativações de função recentes nesse mesmo período.

    ![Detalhes do usuário com ativações de função e o resumo de atividade de recurso](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Clique em uma ativação de função específica para ver detalhes e a atividade de recurso do Azure correspondente que ocorreu enquanto o usuário estava ativo.

    ![Ativação da função selecionada e os detalhes da atividade exibidos por data](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="export-role-assignments-with-children"></a>Exportar as atribuições de função com filhos

Talvez você tenha um requisito de conformidade no qual precisa fornecer uma lista completa de atribuições de função para auditores. O PIM permite que você consulte as atribuições de função em um recurso específico, que inclui as atribuições de função para todos os recursos filho. Anteriormente, era difícil para os administradores obter uma lista completa das atribuições de função para uma assinatura e eles precisavam exportar as atribuições de função para cada recurso específico. Usando o PIM, você pode consultar todas as atribuições de função ativas e qualificadas em uma assinatura, incluindo as atribuições de função para todos os grupos de recursos e recursos.

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **Recursos do Azure**.

1. Clique no recurso para o qual deseja exportar as atribuições de função, como uma assinatura.

1. Clique em **Membros**.

1. Clique em **Exportar** para abrir o painel Exportar associação.

    ![Painel de associação de exportação para exportar todos os membros](media/azure-pim-resource-rbac/export-membership.png)

1. Clique em **Exportar todos os membros** para exportar todas as atribuições de função em um arquivo CSV.

    ![Exportado atribuições de função no CSV fil conforme exibido no Excel](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Exibir o histórico de auditoria de recursos

A auditoria de recursos fornece uma exibição de todas as atividades de função para um recurso.

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **Recursos do Azure**.

1. Clique no recurso para o qual deseja exibir o histórico de auditoria.

1. Clique em **Auditoria de recursos**.

1. Filtre o histórico usando uma data predefinida ou um intervalo personalizado.

    ![Lista de auditoria de recurso com filtros](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. Em **Tipo de auditoria**, selecione **Ativar (Atribuído + Ativado)** .

    ![Lista de auditoria de recursos que é filtrada pelo tipo de auditoria de ativar](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. Em **Ação**, clique em **(atividade)** de um usuário para ver os detalhes da atividade desse usuário em recursos do Azure.

    ![Detalhes da atividade de usuário para uma determinada ação](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Exibir minha auditoria

A opção Minha auditoria permite que você exiba sua atividade de função pessoal.

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **Recursos do Azure**.

1. Clique no recurso para o qual deseja exibir o histórico de auditoria.

1. Clique em **Minha auditoria**.

1. Filtre o histórico usando uma data predefinida ou um intervalo personalizado.

    ![Lista de auditoria para o usuário atual](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções de recurso do Azure no PIM](pim-resource-roles-assign-roles.md)
- [Aprovar ou negar as solicitações para funções de recurso do Azure no PIM](pim-resource-roles-approval-workflow.md)
- [Exibir o histórico de auditoria para funções do Azure AD no PIM](pim-how-to-use-audit-log.md)
