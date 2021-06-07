---
title: Aprovar solicitações de ativação para membros e proprietários do grupo no Privileged Identity Management-Azure AD
description: Saiba como aprovar ou negar solicitações para grupos de função atribuíveis no Azure AD Privileged Identity Management (PIM).
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
ms.date: 07/27/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a3b6dad33ce678478a201dc8b7b612f4b8507c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91536980"
---
# <a name="approve-activation-requests-for-privileged-access-group-members-and-owners-preview"></a>Aprovar solicitações de ativação para membros e proprietários do grupo de acesso privilegiado (versão prévia)

Com o Privileged Identity Management (PIM) no Azure Active Directory (Azure AD), você pode configurar membros do grupo de acesso privilegiado e proprietários para exigir aprovação para ativação e escolher usuários ou grupos da sua organização do Azure AD como aprovadores delegados. É recomendável selecionar dois ou mais aprovadores para cada grupo para reduzir a carga de trabalho para o administrador da função com privilégios. Os aprovadores representantes têm 24 horas para aprovar as solicitações. Se a solicitação não for aprovada dentro de 24 horas, o usuário qualificado deverá enviar outra. A janela de tempo de aprovação de 24 horas não é configurável.

Siga as etapas neste artigo para aprovar ou negar solicitações de funções de recursos do Azure.

## <a name="view-pending-requests"></a>Exibir solicitações pendentes

Como um aprovador delegado, você receberá uma notificação por email quando uma solicitação de função de recurso do Azure estiver aguardando a aprovação. Você pode exibir solicitações pendentes no Privileged Identity Management.

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **aprovar solicitações**.

    ![Aprovar solicitações-página de recursos do Azure mostrando solicitação para revisão](./media/groups-approval-workflow/groups-select-request.png)

    Na seção **Solicitações para ativações de função** você verá uma lista de solicitações aguardando a aprovação.

## <a name="approve-requests"></a>Aprovar solicitações

1. Localize e selecione a solicitação que você deseja aprovar e selecione **aprovar**.

    ![Captura de tela que mostra a página "aprovar solicitações" com os botões "aprovar" e "confirmar" realçados.](./media/groups-approval-workflow/groups-confirm-approval.png)

1. Na caixa de **justificação** , insira a justificativa comercial.

1. Selecione **Confirmar**. Uma notificação do Azure é gerada pela sua aprovação.

## <a name="deny-requests"></a>Negar solicitações

1. Localize e selecione a solicitação que você deseja negar e selecione **negar**.

    ![Aprovar solicitações – painel aprovar ou negar com detalhes e caixa de justificação](./media/groups-approval-workflow/groups-confirm-denial.png)

1. Na caixa de **justificação** , insira a justificativa comercial.

1. Selecione **Confirmar**. Uma notificação do Azure é gerada pela negação.

## <a name="workflow-notifications"></a>Notificações de fluxo de trabalho

Veja algumas informações sobre notificações de fluxo de trabalho:

- Os aprovadores são notificados por email quando uma solicitação de uma atribuição de grupo está aguardando sua revisão. As notificações por email incluem um link direto para a solicitação no qual o aprovador pode aprovar ou negar.
- As solicitações são resolvidas pelo primeiro aprovador que aprova ou nega.
- Quando um Aprovador responde à solicitação, todos os aprovadores são notificados sobre a ação.

>[!Note]
>Um administrador que acredita que um usuário aprovado não deve estar ativo pode remover a atribuição de grupo ativo em Privileged Identity Management. Embora os administradores de recursos não sejam notificados sobre solicitações pendentes, a menos que sejam um aprovador, eles podem exibir e cancelar solicitações pendentes para todos os usuários exibindo solicitações pendentes no Privileged Identity Management.

## <a name="next-steps"></a>Próximas etapas

- [Estender ou renovar atribuições de grupo no Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Notificações por email no Privileged Identity Management](pim-email-notifications.md)
- [Aprovar ou negar solicitações para atribuições de grupo no Privileged Identity Management](azure-ad-pim-approval-workflow.md)
