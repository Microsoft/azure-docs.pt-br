---
title: Ativar funções de recurso do Azure no PIM – Azure AD | Microsoft Docs
description: Saiba como ativar suas funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a6ddde80ca554aea25d24694aff76e61e47d928
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97672450"
---
# <a name="activate-my-azure-resource-roles-in-privileged-identity-management"></a>Ativar minhas funções de recurso do Azure no Privileged Identity Management

Use o Privileged Identity Management (PIM) para permitir que membros de função qualificados de recursos do Azure agendem a ativação para uma data e hora futuras. Eles também podem selecionar uma duração de ativação específica até o valor máximo (configurado pelos administradores).

Este artigo é para membros que precisam ativar sua função de recurso do Azure no Privileged Identity Management.

## <a name="activate-a-role"></a>Ativar uma função

Quando precisar tomar uma função de recurso do Azure, você poderá solicitar a ativação usando a opção de navegação **minhas funções** no Privileged Identity Management.

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Abra **Azure ad Privileged Identity Management**. Para obter informações sobre como adicionar o bloco Privileged Identity Management ao seu painel, consulte [começar a usar o Privileged Identity Management](pim-getting-started.md).

1. Selecione **minhas funções**.

    ![Minha página de funções mostrando funções que você pode ativar](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Selecione **funções de recurso do Azure** para ver uma lista de suas funções de recurso do Azure qualificadas.

    ![Minhas funções – página funções de recurso do Azure](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png)

1. Na lista **Funções de recursos do Azure**, encontre a função que você deseja ativar.

    ![Funções de recurso do Azure – minha lista de funções qualificadas](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Selecione **Ativar** para abrir a página ativar.

    ![O painel ativar aberto com escopo, hora de início, duração e motivo](./media/pim-resource-roles-activate-your-roles/azure-role-eligible-activate.png)

1. Se sua função exigir autenticação multifator, selecione **Verificar sua identidade antes de prosseguir**. Você só precisa se autenticar uma vez por sessão.

    ![Verificar minha identidade com MFA antes da ativação de função](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Selecione **verificar minha identidade** e siga as instruções para fornecer verificação de segurança adicional.

    ![Tela para fornecer verificação de segurança, como um código PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Se você quiser especificar um escopo reduzido, selecione **escopo** para abrir o painel Filtro de recursos.

    É uma prática recomendada solicitar apenas o acesso aos recursos de que você precisa. No painel de filtro Recursos, você pode especificar os grupos de recursos ou recursos aos quais você precisa acessar.

    ![Ativar o painel de filtro de recursos para especificar o escopo](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Se necessário, especifique uma hora de início de ativação personalizada. O membro seria ativado após o horário selecionado.

1. Na caixa **Motivo**, insira o motivo da solicitação de ativação.

    ![Preenchimento do painel de ativação com escopo, hora de início, duração e motivo](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Selecione **Ativar**.

    Se a [função exigir aprovação](pim-resource-roles-approval-workflow.md) para ser ativada, uma notificação será exibida no canto superior direito do seu navegador informando que a solicitação está com a aprovação pendente.

    ![Notificação de aprovação pendente da solicitação de ativação](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>Exibir o status de suas solicitações

Você pode exibir o status das suas solicitações pendentes a serem ativadas.

1. Abra o Azure AD Privileged Identity Management.

1. Selecione **minhas solicitações** para ver uma lista de suas funções do Azure AD e das solicitações de função de recurso do Azure.

    ![Minhas solicitações – página de recursos do Azure mostrando suas solicitações pendentes](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Role para a direita para exibir o **Status da solicitação** coluna.

## <a name="cancel-a-pending-request"></a>Cancelar uma solicitação pendente

Caso não precise da ativação de uma função que requer aprovação, você pode cancelar uma solicitação pendente a qualquer momento.

1. Abra o Azure AD Privileged Identity Management.

1. Selecione **minhas solicitações**.

1. Para a função que você deseja cancelar, selecione o link **Cancelar** .

    Quando você selecionar cancelar, a solicitação será cancelada. Para ativar a função novamente, você precisará enviar uma nova solicitação de ativação.

   ![Minha lista de solicitações com a ação cancelar realçada](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Solucionar problemas

### <a name="permissions-are-not-granted-after-activating-a-role"></a>As permissões não são concedidas depois de ativar uma função

Quando você ativa uma função no Privileged Identity Management, a ativação pode não ser propagada instantaneamente para todos os portais que exigem a função privilegiada. Às vezes, mesmo quando a alteração é propagada, o cache da web em um portal pode fazer com que a alteração não entre em vigor de imediato. Se a ativação estiver atrasada, aqui está o que você deve fazer.

1. Saia do portal do Azure e entre novamente.
1. Em Privileged Identity Management, verifique se você está listado como o membro da função.

## <a name="next-steps"></a>Próximas etapas

- [Estender ou renovar funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Ativar minhas funções do Azure AD no Privileged Identity Management](pim-how-to-activate-role.md)
