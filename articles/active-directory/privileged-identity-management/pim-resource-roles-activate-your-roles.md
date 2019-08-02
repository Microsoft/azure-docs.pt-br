---
title: Ativar minhas funções de recurso do Azure no PIM - Azure Active Directory | Microsoft Docs
description: Saiba como ativar suas funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
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
ms.date: 06/28/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdce060006f65f2b0fb08023066ee504578bc552
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67501654"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Ativar minhas funções de recurso do Azure no PIM

Usando o Azure Active Directory (Azure AD) PIM Privileged Identity Management (), os membros de função qualificada para recursos do Azure podem agendar a ativação para uma data e hora futura. Eles também podem selecionar uma duração de ativação específica até o valor máximo (configurado pelos administradores).

Este artigo é para membros que precisam ativar sua função de recurso do Azure no PIM.

## <a name="activate-a-role"></a>Ativar uma função

Quando você precisar assumir uma função de recurso do Azure, poderá solicitar a ativação usando a opção de navegação **Minhas funções** no PIM.

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. Abra o **Azure AD Privileged Identity Management**. Para obter informações sobre como adicionar o bloco do PIM ao painel, consulte [Começar a usar o PIM](pim-getting-started.md).

1. Clique em **Minhas funções**.

    ![Página Minhas funções mostrando as funções que você pode ativar](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Clique em **funções de recurso do Azure** para ver uma lista das suas funções qualificadas em recursos do Azure.

   ![Minhas funções - página de funções de recurso do Azure](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. Na lista **Funções de recursos do Azure**, encontre a função que você deseja ativar.

    ![Funções de recurso do Azure - minha lista de funções qualificadas](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Clique em **ativar** para abrir o painel de ativar.

1. Se sua função exigir a MFA (autenticação multifator), clique em **Verificar sua identidade antes de prosseguir**. Você só precisa se autenticar uma vez por sessão.

    ![Verificar minha identidade com MFA antes da ativação de função](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Clique em **Verificar minha identidade** e siga as instruções para fornecer a verificação de segurança adicional.

    ![Tela para fornecer verificação de segurança, como um código PIN](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Se você quiser especificar um escopo reduzido, clique em **Escopo** para abrir o painel de filtro Recursos.

    É uma prática recomendada solicitar apenas o acesso aos recursos de que você precisa. No painel de filtro Recursos, você pode especificar os grupos de recursos ou recursos aos quais você precisa acessar.

    ![Ativar - painel de filtro de recurso para especificar o escopo](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Se necessário, especifique uma hora de início de ativação personalizada. O membro seria ativado após o horário selecionado.

1. Na caixa **Motivo**, insira o motivo da solicitação de ativação.

    ![Painel de ativar concluído com escopo, a hora de início, duração e motivo](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Clique em **Ativar**.

    Se a função não exigir aprovação, ela já estará ativada e a função será exibida na lista de funções ativas. Se você quiser usar a função, siga as etapas na próxima seção.

    Se a [função exigir aprovação](pim-resource-roles-approval-workflow.md) para ser ativada, uma notificação será exibida no canto superior direito do seu navegador informando que a solicitação está com a aprovação pendente.

    ![Solicitação de ativação é a notificação de aprovação pendente](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Usar uma função imediatamente após a ativação

No caso de qualquer atraso após a ativação, siga estas etapas depois de ativar para usar suas funções de recurso do Azure imediatamente.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **minhas funções** para ver uma lista de seus qualificados para funções do Azure AD e funções dos recursos do Azure.

1. Clique em **funções de recurso do Azure**.

1. Clique o **funções ativas** guia.

1. Quando a função está ativa, saia do portal e entre novamente.

    A função agora deve estar disponível para uso.

## <a name="view-the-status-of-your-requests"></a>Exibir o status de suas solicitações

Você pode exibir o status das suas solicitações pendentes a serem ativadas.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **Minhas solicitações** ver uma lista de sua função do Azure AD e a função de recurso do Azure solicita.

    ![Minhas solicitações - página de recursos do Azure mostrando suas solicitações pendentes](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Role para a direita para exibir o **Status da solicitação** coluna.

## <a name="cancel-a-pending-request"></a>Cancelar uma solicitação pendente

Caso não precise da ativação de uma função que requer aprovação, você pode cancelar uma solicitação pendente a qualquer momento.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **Minhas solicitações**.

1. Para a função que você deseja cancelar, clique no link **Cancelar**.

    Quando você clicar em Cancelar, a solicitação será cancelada. Para ativar a função novamente, você precisará enviar uma nova solicitação de ativação.

   ![Minha lista de solicitação com a ação de cancelamento realçada](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Solucionar problemas

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Permissões não são concedidas após a ativação de uma função

Quando você ativa uma função no PIM, a ativação não pode propagar instantaneamente para todos os portais que exigem a função com privilégios. Às vezes, mesmo se a alteração é propagada, cache da web em um portal pode resultar na alteração não entrarem em vigor imediatamente. Se a ativação está atrasada, aqui está o que você deve fazer.

1. Saia do portal do Azure e entre novamente.

    Quando você ativa uma função de recurso do Azure, você verá os estágios de sua ativação. Depois que todas as etapas forem concluídas, você verá uma **sair** link. Você pode usar este link para sair. Isso resolverá a maioria dos casos para o atraso de ativação.

1. No PIM, verifique se que você está listado como o membro da função.

## <a name="next-steps"></a>Próximas etapas

- [Estender ou renovar funções de recurso do Azure no PIM](pim-resource-roles-renew-extend.md)
- [Ativar minhas funções do Azure AD no PIM](pim-how-to-activate-role.md)
