---
title: Exibir relatório de auditoria para funções de recurso do Azure no Privileged Identity Management (PIM) – Azure AD | Microsoft Docs
description: Exiba a atividade e o histórico de auditoria para funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45144f64789a19390984c3f9f6a660e3c3300215
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002486"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Exibir a atividade e o histórico de auditoria das funções de recurso do Azure no Privileged Identity Management

Com o Azure AD (Azure Active Directory) PIM (Privileged Identity Management), você pode exibir a atividade, as ativações e o histórico de auditoria para funções de recursos do Azure em sua organização. Isso inclui assinaturas, grupos de recursos e até mesmo máquinas virtuais. Qualquer recurso dentro do portal do Azure que aproveita a funcionalidade de controle de acesso baseado em função do Azure pode aproveitar os recursos de segurança e gerenciamento do ciclo de vida no Privileged Identity Management.

> [!NOTE]
> Se sua organização tiver funções de gerenciamento terceirizadas para um provedor de serviços que usa o [Gerenciamento de recursos delegado do Azure](../../lighthouse/concepts/azure-delegated-resource-management.md), as atribuições de função autorizadas por esse provedor de serviços não serão mostradas aqui.

## <a name="view-activity-and-activations"></a>Exibir a atividade e as ativações

Para ver as ações que um usuário específico realizou em vários recursos, você pode exibir a atividade do recurso do Azure associada a um período de ativação especificado.

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **Recursos do Azure**.

1. Selecione o recurso para o qual você deseja exibir a atividade e as ativações.

1. Selecione **funções** ou **Membros**.

1. Selecione um usuário.

    Você verá um resumo das ações do usuário nos recursos do Azure por data. Ele também mostra as ativações de função recentes nesse mesmo período.

    ![Detalhes do usuário com Resumo de atividade de recursos e ativações de função](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Selecione uma ativação de função específica para ver detalhes e a atividade de recurso do Azure correspondente que ocorreu enquanto esse usuário estava ativo.

    [![Ativação de função selecionada e detalhes da atividade](media/azure-pim-resource-rbac/export-membership.png "Ativação de função selecionada e detalhes da atividade")](media/azure-pim-resource-rbac/export-membership.png)

## <a name="export-role-assignments-with-children"></a>Exportar as atribuições de função com filhos

Talvez você tenha um requisito de conformidade no qual precisa fornecer uma lista completa de atribuições de função para auditores. Privileged Identity Management permite consultar atribuições de função em um recurso específico, que inclui atribuições de função para todos os recursos filho. Anteriormente, era difícil para os administradores obter uma lista completa das atribuições de função para uma assinatura e eles precisavam exportar as atribuições de função para cada recurso específico. Usando Privileged Identity Management, você pode consultar todas as atribuições de função ativas e qualificadas em uma assinatura, incluindo atribuições de função para todos os grupos de recursos e recursos.

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **Recursos do Azure**.

1. Selecione o recurso para o qual você deseja exportar atribuições de função, como uma assinatura.

1. Selecione **Membros**.

1. Selecione **Exportar** para abrir o painel Exportar associação.

    [![Exportar o painel de associação para exportar todos os membros](media/azure-pim-resource-rbac/export-membership.png "Exportar página de associação para exportar todos os membros")](media/azure-pim-resource-rbac/export-membership.png)

1. Selecione **exportar todos os membros** para exportar todas as atribuições de função em um arquivo CSV.

    ![Atribuições de função exportadas no arquivo CSV como exibidas no Excel](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Exibir o histórico de auditoria de recursos

A auditoria de recursos fornece uma exibição de todas as atividades de função para um recurso.

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **Recursos do Azure**.

1. Selecione o recurso para o qual você deseja exibir o histórico de auditoria.

1. Selecione **auditoria de recurso**.

1. Filtre o histórico usando uma data predefinida ou um intervalo personalizado.

    [![Lista de auditoria de recursos com filtros](media/azure-pim-resource-rbac/rbac-resource-audit.png "Lista de auditoria de recursos com filtros")](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. Em **Tipo de auditoria**, selecione **Ativar (Atribuído + Ativado)**.

    [![Lista de auditoria de recursos filtrada por ativar tipo](media/azure-pim-resource-rbac/rbac-audit-activity.png "Lista de auditoria de recursos filtrada por ativar")](media/azure-pim-resource-rbac/rbac-audit-activity.png) ![ de auditoria Lista de auditoria de recursos filtrada por ativar tipo de auditoria](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. Em **Ação**, clique em **(atividade)** de um usuário para ver os detalhes da atividade desse usuário em recursos do Azure.

    ![Detalhes da atividade do usuário para uma ação específica](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Exibir minha auditoria

A opção Minha auditoria permite que você exiba sua atividade de função pessoal.

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **Recursos do Azure**.

1. Selecione o recurso para o qual você deseja exibir o histórico de auditoria.

1. Selecione **minha auditoria**.

1. Filtre o histórico usando uma data predefinida ou um intervalo personalizado.

    [![Lista de auditoria para o usuário atual](media/azure-pim-resource-rbac/my-audit-time.png "Lista de auditoria para o usuário atual")](media/azure-pim-resource-rbac/my-audit-time.png)

> [!NOTE]
> O acesso ao histórico de auditoria requer uma função de administrador global ou de administrador de função privilegiada.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Obter motivo, Aprovador e número do tíquete para eventos de aprovação

1. Entre no [portal do Azure](https://aad.portal.azure.com) com permissões de função de administrador de função com privilégios e abra o Azure AD.
1. Selecione **Logs de Auditoria**.
1. Use o filtro de **serviço** para exibir apenas eventos de auditoria para o serviço Privileged Identity Management. Na página **logs de auditoria** , você pode:

    - Consulte o motivo de um evento de auditoria na coluna **razão do status** .
    - Consulte o aprovador na coluna **iniciado por (ator)** do evento "Adicionar membro à solicitação de função aprovada".

    [![Filtrar o log de auditoria para o serviço PIM](media/azure-pim-resource-rbac/filter-audit-logs.png "Filtrar o log de auditoria para o serviço PIM")](media/azure-pim-resource-rbac/filter-audit-logs.png)

1. Selecione um evento de log de auditoria para ver o número do tíquete na guia **atividade** do painel de **detalhes** .
  
    [![Verificar o número do tíquete para o evento de auditoria](media/azure-pim-resource-rbac/audit-event-ticket-number.png "Verificar o número do tíquete do evento de auditoria")](media/azure-pim-resource-rbac/audit-event-ticket-number.png)]

1. Você pode exibir o solicitante (pessoa ativando a função) na guia **destinos** do painel de **detalhes** para um evento de auditoria. Há três tipos de destino para as funções de recurso do Azure:

    - A função (**Type** = Role)
    - O solicitante (**tipo** = outro)
    - O aprovador (**tipo** = usuário)

    [![Verificar o tipo de destino](media/azure-pim-resource-rbac/audit-event-target-type.png "Verificar o tipo de destino")](media/azure-pim-resource-rbac/audit-event-target-type.png)

Normalmente, o evento de log imediatamente acima do evento de aprovação é um evento para "Adicionar membro à função concluído" **, onde o iniciado por (ator)** é o solicitante. Na maioria dos casos, você não precisará localizar o solicitante na solicitação de aprovação de uma perspectiva de auditoria.

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-assign-roles.md)
- [Aprovar ou negar solicitações para funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-approval-workflow.md)
- [Exibir histórico de auditoria para funções do Azure AD no Privileged Identity Management](pim-how-to-use-audit-log.md)
