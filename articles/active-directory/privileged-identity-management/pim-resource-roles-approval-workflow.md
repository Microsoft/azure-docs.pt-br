---
title: Aprovar ou negar as solicitações para funções de recurso do Azure no PIM - Azure Active Directory | Microsoft Docs
description: Saiba como aprovar ou negar solicitações para funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d2e8b4ae1a01cd299d910c4e88655885c7d00dc
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476381"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>Aprovar ou negar as solicitações para funções de recurso do Azure no PIM

Com o Azure Active Directory (Azure AD) Privileged Identity Management (PIM), você pode configurar funções para solicitar aprovação para ativação e escolher um ou vários usuários ou grupos como aprovadores delegados. Os aprovadores representantes têm 24 horas para aprovar as solicitações. Se a solicitação não for aprovada dentro de 24 horas, o usuário qualificado deverá enviar outra. A janela de tempo de aprovação de 24 horas não é configurável.

Siga as etapas neste artigo para aprovar ou negar solicitações de funções de recursos do Azure.

## <a name="view-pending-requests"></a>Exibir solicitações pendentes

Como um aprovador delegado, você receberá uma notificação por email quando uma solicitação de função de recurso do Azure estiver aguardando a aprovação. Você pode exibir essas solicitações pendentes no PIM.

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **aprovar solicitações**.

    ![Aprovar solicitações - página de recursos do Azure mostrando a solicitação para examinar](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    Na seção **Solicitações para ativações de função** você verá uma lista de solicitações aguardando a aprovação.

## <a name="approve-requests"></a>Aprovar solicitações

1. Localize e clique na solicitação que você quer aprovar. Um aprovar ou negar o painel será exibido.

    ![Aprovar solicitações - aprovar ou Negar painel com detalhes e a caixa de justificativa](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Na caixa **Justificação**, digite um motivo.

1. Clique em **Aprovar**.

    Uma notificação é exibida com a aprovação.

    ![Aprovar notificação mostrando que a solicitação foi aprovada](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Negar solicitações

1. Localize e clique na solicitação que você quer negar. Um aprovar ou negar o painel será exibido.

    ![Aprovar solicitações - aprovar ou Negar painel com detalhes e a caixa de justificativa](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Na caixa **Justificação**, digite um motivo.

1. Clique em **Negar**.

    Uma notificação é exibida com a negação.

## <a name="workflow-notifications"></a>Notificações de fluxo de trabalho

Veja algumas informações sobre notificações de fluxo de trabalho:

- Todos os membros da lista de aprovadores são notificados por email quando uma solicitação para uma função está aguardando revisão. As notificações por email incluem um link direto para a solicitação no qual o aprovador pode aprovar ou negar.
- As solicitações são resolvidas pelo primeiro membro da lista que aprovar ou recusar.
- Quando um aprovador responde à solicitação, todos os membros da lista de aprovadores são notificados da ação.
- Os administradores de recursos são notificados quando um membro aprovado se torna ativo em sua função.

>[!Note]
>Um administrador de recursos que acredite que o membro aprovado não deve estar ativo pode remover a atribuição de função ativa no PIM. Embora os administradores de recursos não sejam notificados de solicitações pendentes a menos que sejam membros da lista de aprovadores, eles podem exibir e cancelar solicitações pendentes de todos os usuários exibindo solicitações pendentes no PIM. 

## <a name="next-steps"></a>Próximas etapas

- [Estender ou renovar funções de recurso do Azure no PIM](pim-resource-roles-renew-extend.md)
- [Notificações por email no PIM](pim-email-notifications.md)
- [Aprovar ou negar as solicitações para funções do Azure AD no PIM](azure-ad-pim-approval-workflow.md)
