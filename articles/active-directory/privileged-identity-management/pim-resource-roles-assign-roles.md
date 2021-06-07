---
title: Atribuir funções de recurso do Azure no Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Saiba como atribuir funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 011b50449f0a02b128bc000535f93e1809fd9777
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92673882"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Atribuir funções de recurso do Azure no Privileged Identity Management

O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) pode gerenciar as funções de recurso internas do Azure, bem como as funções personalizadas, incluindo (mas não se limitando a):

- Proprietário
- Administrador de Acesso do Usuário
- Colaborador
- Administrador de Segurança
- Gerenciador de Segurança

> [!NOTE]
> Os usuários ou membros de um grupo atribuído ao proprietário ou às funções de assinatura do administrador de acesso do usuário e aos administradores globais do Azure AD que habilitam o gerenciamento de assinaturas no Azure AD têm permissões de administrador de recursos por padrão. Esses administradores podem atribuir funções, definir configurações de função e revisar o acesso usando Privileged Identity Management para recursos do Azure. Um usuário não pode gerenciar Privileged Identity Management para recursos sem permissões de administrador de recursos. Exiba a lista de [funções internas do Azure](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Atribuir uma função

Siga estas etapas para tornar um usuário qualificado para uma função de recurso do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/) com permissões de função de administrador de acesso do usuário ou proprietário.

    Para obter informações sobre como conceder a outro administrador acesso para gerenciar Privileged Identity Management, consulte [conceder acesso a outros administradores para gerenciar Privileged Identity Management](pim-how-to-give-access-to-pim.md).

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **recursos do Azure**.

1. Use o filtro de recursos para localizar os recursos gerenciados que você está procurando.

    ![Lista de recursos do Azure para gerenciar](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Selecione o recurso que você deseja gerenciar para abrir a página Visão geral do recurso.

1. Em **gerenciar**, selecione **funções** para ver a lista de funções para recursos do Azure.

    ![Funções dos recursos do Azure](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Selecione **Adicionar atribuições** para abrir o painel **Adicionar atribuições** .

1. Selecione **selecionar uma função** para abrir a página **selecionar uma função** .

    ![Novo painel de atribuição](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Selecione uma função que você deseja atribuir e clique em **Selecionar**.

    O painel **selecionar um membro ou grupo** é aberto.

1. Selecione um membro ou grupo que você deseja atribuir à função e, em seguida, clique em **selecionar**.

    ![Selecione um painel de membro ou grupo](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

1. Na guia **configurações** , na lista **tipo de atribuição** , selecione **qualificado** ou **ativo**.

    ![Painel de configurações das Associações](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Privileged Identity Management para recursos do Azure fornece dois tipos distintos de atribuição:

    - Atribuições **qualificadas** exigem que o membro da função execute uma ação para usar a função. As ações podem incluir a execução de uma verificação de MDA (Autenticação Multifator), fornecimento de uma justificativa comercial ou solicitação de aprovação dos aprovadores designados.

    - As atribuições **ativas** não exigem que o membro execute qualquer ação para usar a função. Membros atribuídos como ativos sempre possuem privilégios atribuídos pela função.

1. Para especificar uma duração de atribuição específica, altere as datas e horários de início e término.

1. Quando terminar, selecione **atribuir**.

1. Depois que a nova atribuição de função é criada, uma notificação de status é exibida.

    ![Nova atribuição - Notificação](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Atualizar ou remover uma atribuição de função existente

Siga estas etapas para atualizar ou remover uma atribuição de função existente.

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **recursos do Azure**.

1. Selecione o recurso que você deseja gerenciar para abrir sua página de visão geral.

1. Em **gerenciar**, selecione **funções** para ver a lista de funções para recursos do Azure.

    ![Funções de recursos do Azure - Selecionar função](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Selecione a função que você deseja atualizar ou remover.

1. Localize a atribuição de função nas guias **Funções qualificadas** ou **Funções ativas**.

    ![Atualizar ou remover atribuição de função](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Selecione **Atualizar** ou **Remover** para atualizar ou remover a atribuição de função.

    Para obter informações sobre como estender uma atribuição de função, consulte [estender ou renovar funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Próximas etapas

- [Estender ou renovar funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Definir configurações de função de recurso do Azure no Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Atribuir funções do Azure AD no Privileged Identity Management](pim-how-to-add-role-to-user.md)
