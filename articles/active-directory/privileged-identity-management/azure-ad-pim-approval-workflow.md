---
title: Aprovar ou negar solicitações para funções do Azure AD no PIM – Azure AD | Microsoft Docs
description: Saiba como aprovar ou negar solicitações para funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d74a243ab10d0fffca960dc5149999560a7b54cd
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94980579"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Aprovar ou negar solicitações para funções do Azure AD no Privileged Identity Management

Com o Azure Active Directory (Azure AD) Privileged Identity Management (PIM), você pode configurar funções para exigir aprovação para ativação e escolher um ou vários usuários ou grupos como aprovadores delegados. Os aprovadores representantes têm 24 horas para aprovar as solicitações. Se a solicitação não for aprovada dentro de 24 horas, o usuário qualificado deverá enviar outra. A janela de tempo de aprovação de 24 horas não é configurável.

## <a name="determine-your-version-of-pim"></a>Determinar sua versão do PIM

A partir de novembro de 2019, a parte das funções do Azure AD da Privileged Identity Management está sendo atualizada para uma nova versão que corresponde às experiências para funções do Azure. Isso cria recursos adicionais, bem como [as alterações na API existente](azure-ad-roles-features.md#api-changes). Enquanto a nova versão está sendo distribuída, os procedimentos que você seguir neste artigo dependem da versão do Privileged Identity Management que você tem atualmente. Siga as etapas nesta seção para determinar qual versão do Privileged Identity Management você tem. Depois de saber sua versão do Privileged Identity Management, você pode selecionar os procedimentos neste artigo que correspondem a essa versão.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que esteja na função de [administrador de função com privilégios](../roles/permissions-reference.md#privileged-role-administrator) .
1. Abra **Azure ad Privileged Identity Management**. Se você tiver uma faixa na parte superior da página Visão geral, siga as instruções na **nova guia versão** deste artigo. Caso contrário, siga as instruções na guia **versão anterior** .

    [![Selecione Azure AD > Privileged Identity Management.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Siga as etapas neste artigo para aprovar ou negar solicitações para funções do Azure AD.

## <a name="new-version"></a>[Nova versão](#tab/new)

### <a name="view-pending-requests"></a>Exibir solicitações pendentes

Como um Aprovador delegado, você receberá uma notificação por email quando uma solicitação de função do Azure AD estiver aguardando sua aprovação. Você pode exibir essas solicitações pendentes no Privileged Identity Management.

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **aprovar solicitações**.

    ![Aprovar solicitações-página mostrando solicitação para examinar as funções do Azure AD](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    Na seção **Solicitações para ativações de função** você verá uma lista de solicitações aguardando a aprovação.

### <a name="approve-requests"></a>Aprovar solicitações

1. Localize e selecione a solicitação que você deseja aprovar. Uma página aprovar ou negar é exibida.

    ![Captura de tela que mostra a página "aprovar solicitações-funções do Azure AD".](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. Na caixa de **justificação** , insira a justificativa comercial.

1. Selecione **Aprovar**. Você receberá uma notificação do Azure de sua aprovação.

    ![Aprovar notificação mostrando que a solicitação foi aprovada](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

### <a name="deny-requests"></a>Negar solicitações

1. Localize e selecione a solicitação que você deseja negar. Uma página aprovar ou negar é exibida.

    ![Aprovar solicitações – painel aprovar ou negar com detalhes e caixa de justificação](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Na caixa de **justificação** , insira a justificativa comercial.

1. Selecione **negar**. Uma notificação é exibida com a negação.

### <a name="workflow-notifications"></a>Notificações de fluxo de trabalho

Veja algumas informações sobre notificações de fluxo de trabalho:

- Os aprovadores são notificados por email quando uma solicitação de uma função está aguardando sua revisão. As notificações por email incluem um link direto para a solicitação no qual o aprovador pode aprovar ou negar.
- As solicitações são resolvidas pelo primeiro aprovador que aprova ou nega.
- Quando um Aprovador responde à solicitação, todos os aprovadores são notificados sobre a ação.
- Administradores globais e administradores de função privilegiada são notificados quando um usuário aprovado se torna ativo em sua função.

>[!NOTE]
>Um administrador global ou administrador de função com privilégios que acredita que um usuário aprovado não deve estar ativo pode remover a atribuição de função ativa em Privileged Identity Management. Embora os administradores não sejam notificados sobre solicitações pendentes, a menos que sejam um aprovador, eles podem exibir e cancelar quaisquer solicitações pendentes para todos os usuários exibindo solicitações pendentes no Privileged Identity Management.

## <a name="previous-version"></a>[Versão anterior](#tab/previous)

### <a name="view-pending-requests"></a>Exibir solicitações pendentes

Como um Aprovador delegado, você receberá uma notificação por email quando uma solicitação de função do Azure AD estiver aguardando sua aprovação. Você pode exibir essas solicitações pendentes no Privileged Identity Management.

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Abra **Azure ad Privileged Identity Management**.

1. Clique em **funções do Microsoft Azure Active Directory**.

1. Clique em **aprovar solicitações**.

    ![Funções do Azure AD – aprovar solicitações](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    Você verá uma lista de solicitações aguardando sua aprovação.

### <a name="approve-requests"></a>Aprovar solicitações

1. Selecione as solicitações que você deseja aprovar e, em seguida, clique em **aprovar** abrir a aprovar selecionado no painel de solicitações.

    ![Aprovar a lista de solicitações com a opção aprovar realçada](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. No **razão da aprovação** , digite um motivo.

    ![Aprovar o painel de solicitações selecionadas com um motivo de aprovação](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Clique em **aprovar**.

    O símbolo de status será atualizado com sua aprovação.

    ![Aprovar o painel de solicitações selecionadas após o botão aprovar clicado](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

### <a name="deny-requests"></a>Negar solicitações

1. Selecione as solicitações que você deseja negar e clique em **Negar** para abrir o painel Negar solicitações selecionadas.

    ![Aprovar a lista de solicitações com a opção negar realçada](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Na caixa **Negar motivo**, digite um motivo.

    ![Negar o painel de solicitações selecionadas com um motivo de negação](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Selecione **negar**.

    Símbolo de Status será atualizado com a negação.

---

## <a name="next-steps"></a>Próximas etapas

- [Notificações por email no Privileged Identity Management](pim-email-notifications.md)
- [Aprovar ou negar solicitações para funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-approval-workflow.md)
