---
title: Atribuir proprietários e membros qualificados para grupos de acesso privilegiado-Azure Active Directory
description: Saiba como atribuir proprietários qualificados ou membros de um grupo de funções de atribuição de função no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 08/18/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48c4473cfafce1215219251c47bce1d5730645fc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91534413"
---
# <a name="assign-eligibility-for-a-privileged-access-group-preview-in-privileged-identity-management"></a>Atribuir qualificação para um grupo de acesso privilegiado (versão prévia) no Privileged Identity Management

O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) pode ajudá-lo a gerenciar a qualificação e a ativação de atribuições para grupos de acesso privilegiado no Azure AD. Você pode atribuir a qualificação a membros ou proprietários do grupo.

>[!NOTE]
>Cada usuário qualificado para associação ou propriedade de um grupo de acesso privilegiado deve ter uma licença Azure AD Premium P2. Para obter mais informações, consulte [requisitos de licença para usar Privileged Identity Management](subscription-requirements.md).

## <a name="assign-an-owner-or-member-of-a-group"></a>Atribuir um proprietário ou membro de um grupo

Siga estas etapas para tornar um usuário qualificado para ser um membro ou proprietário de um grupo de acesso privilegiado.

1. [Entre no Azure ad](https://aad.portal.azure.com) com permissões de administrador global ou de proprietário do grupo.
1. Selecione **grupos** e, em seguida, selecione o grupo de funções que você deseja gerenciar. Você pode pesquisar ou filtrar a lista.

    ![localizar um grupo de funções que pode ser atribuído para gerenciar no PIM](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. Abra o grupo e selecione **acesso privilegiado (versão prévia)**.

    ![Abrir a experiência de Privileged Identity Management](./media/groups-assign-member-owner/groups-discover-groups.png)

1. Selecione **Adicionar atribuições**.

    ![Novo painel de atribuição](./media/groups-assign-member-owner/groups-add-assignment.png)

1. Selecione os membros ou proprietários que você deseja tornar qualificados para o grupo de acesso privilegiado.

    ![Captura de tela que mostra a página "Adicionar atribuições" com o painel "selecionar um membro ou grupo" aberto e o botão "selecionar" realçado.](./media/groups-assign-member-owner/add-assignments.png)

1. Selecione **Avançar** para definir a duração da associação ou propriedade.

    ![Selecione um painel de membro ou grupo](./media/groups-assign-member-owner/assignment-duration.png)

1. Na lista **Tipo de atribuição**, selecione **Qualificado** ou **Ativo**. Os grupos de acesso privilegiado fornecem dois tipos de atribuição distintos:

    - Atribuições **qualificadas** exigem que o membro da função execute uma ação para usar a função. As ações podem incluir a execução de uma verificação de MDA (Autenticação Multifator), fornecimento de uma justificativa comercial ou solicitação de aprovação dos aprovadores designados.

    - As atribuições **ativas** não exigem que o membro execute qualquer ação para usar a função. Membros atribuídos como ativos sempre possuem privilégios atribuídos pela função.

1. Se a atribuição deve ser permanente (permanentemente qualificada ou permanentemente atribuída), marque a caixa de seleção **permanentemente** . Dependendo das configurações da sua organização, a caixa de seleção pode não aparecer ou pode não ser editável.

1. Quando terminar, selecione **atribuir**.

1. Para criar a nova atribuição de função, selecione **Adicionar**. Uma notificação do status é exibida.

    ![Nova atribuição - Notificação](./media/groups-assign-member-owner/groups-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Atualizar ou remover uma atribuição de função existente

Siga estas etapas para atualizar ou remover uma atribuição de função existente.

1. [Entre no Azure ad](https://aad.portal.azure.com) com permissões de administrador global ou de proprietário do grupo.
1. Selecione **grupos** e, em seguida, selecione o grupo de funções que você deseja gerenciar. Você pode pesquisar ou filtrar a lista.

    ![localizar um grupo de funções que pode ser atribuído para gerenciar no PIM](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. Abra o grupo e selecione **acesso privilegiado (versão prévia)**.

    ![Abrir a experiência de Privileged Identity Management](./media/groups-assign-member-owner/groups-discover-groups.png)

1. Selecione a função que você deseja atualizar ou remover.

1. Localize a atribuição de função nas guias **Funções qualificadas** ou **Funções ativas**.

    ![Atualizar ou remover atribuição de função](./media/groups-assign-member-owner/groups-bring-under-management.png)

1. Selecione **Atualizar** ou **Remover** para atualizar ou remover a atribuição de função.

    Para obter informações sobre como estender uma atribuição de função, consulte [estender ou renovar funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Próximas etapas

- [Estender ou renovar funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Definir configurações de função de recurso do Azure no Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Atribuir funções do Azure AD no Privileged Identity Management](pim-how-to-add-role-to-user.md)
