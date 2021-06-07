---
title: Atribuir PIM (função personalizada do Azure AD Privileged Identity Management)
description: Como atribuir uma função personalizada do Azure AD para atribuição do PIM (Privileged Identity Management)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75e004980ada23a616b57e3c7eb1afb241e09598
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92371640"
---
# <a name="assign-an-azure-ad-custom-role-in-privileged-identity-management"></a>Atribuir uma função personalizada do Azure AD no PIM (Privileged Identity Management)

Este artigo informa como usar o PIM (Privileged Identity Management) para criar a atribuição Just-In-Time e com limite de tempo a funções personalizadas criadas para gerenciar aplicativos na experiência administrativa do Azure AD (Azure Active Directory).

- Para saber mais sobre como criar funções personalizadas para delegar o gerenciamento de aplicativos no Azure AD, confira [Funções Administrador personalizadas no Azure Active Directory (versão prévia)](../roles/custom-overview.md).
- Se você ainda não usou o Privileged Identity Management, obtenha mais informações em [Começar a usar o Privileged Identity Management](pim-getting-started.md).
- Para obter informações sobre como conceder a outro administrador acesso para gerenciar Privileged Identity Management, consulte [conceder acesso a outros administradores para gerenciar Privileged Identity Management](pim-how-to-give-access-to-pim.md).

> [!NOTE]
> As funções personalizadas do Azure AD não são integradas às funções de diretório internas durante a versão prévia. Depois que a funcionalidade estiver em disponibilidade geral, o gerenciamento de função ocorrerá na experiência de funções internas. Se você vir a seguinte faixa, essas funções deverão ser gerenciadas [na experiência de funções internas](pim-how-to-activate-role.md) e este artigo não se aplicará:
>
> [![Selecione Azure AD > Privileged Identity Management.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="assign-a-role"></a>Atribuir uma função

O Privileged Identity Management pode gerenciar funções personalizadas que você cria no gerenciamento de aplicativos do Azure AD (Active Directory).  As etapas a seguir fazem uma atribuição qualificada para uma função do diretório personalizada.

1. Entre no [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) no portal do Azure com uma conta de usuário atribuída à função de administrador de funções com privilégios.
1. Selecione **funções personalizadas do Azure AD (versão prévia)**.

    ![Selecione a versão prévia das funções personalizadas do Azure AD para ver as atribuições de função elegíveis](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Selecione **Funções** para ver a lista de funções personalizadas para aplicativos do Azure AD.

    ![Selecione Funções para ver a lista e atribuições de função elegíveis](./media/azure-ad-custom-roles-assign/view-roles.png)

1. Selecione **Adicionar membro** para abrir a página de atribuição.
1. Para restringir o escopo da atribuição de função para um único aplicativo, selecione **Escopo** para especificar um escopo de aplicativo.

    ![restringir o escopo de atribuições de função qualificadas no Azure AD](./media/azure-ad-custom-roles-assign/set-scope.png)

1. Selecione **Selecionar uma função** para abrir a lista **Selecionar uma função**.

    ![selecionar a função elegível a ser atribuída a um usuário](./media/azure-ad-custom-roles-assign/select-role.png)

1. Selecione uma função que você deseja atribuir e clique em **Selecionar**. A lista **Selecionar um membro** é aberta.

    ![selecionar o usuário a quem você está atribuindo a função](./media/azure-ad-custom-roles-assign/select-member.png)

1. Selecione um usuário a quem você deseja atribuir a função e, em seguida, clique em **Selecionar**. A lista **Configurações de Associação** é aberta.

    ![Definir o tipo de atribuição de função como qualificado ou ativo](./media/azure-ad-custom-roles-assign/membership-settings.png)

1. Na página **Configurações de associação**, selecione **Qualificada** ou **Ativa**:

    - Atribuições **Qualificadas** exigem que o usuário atribuído à função execute uma ação antes de poder usar a função. As ações podem incluir a passagem de uma verificação de autenticação multifator, o fornecimento de uma justificativa comercial ou a solicitação da aprovação de aprovadores designados.
    - Atribuições **Ativas** não exigem que o usuário atribuído execute nenhuma ação para usar a função. Usuários ativos têm os privilégios atribuídos à função sempre.

1. Se a caixa de seleção **Permanente** estiver presente e disponível (dependendo das configurações de função), você poderá especificar se a atribuição é permanente. Marque a caixa de seleção para tornar a atribuição permanentemente qualificada ou permanentemente atribuída. Desmarque a caixa de seleção para especificar a duração de uma atribuição.
1. Para criar a atribuição de função, clique em **Salvar** e, em seguida, em **Adicionar**. Uma notificação do status do processo da atribuição é exibida.

Para verificar a atribuição de função, em uma função aberta, selecione **atribuições**  >  **atribuir** e verifique se a atribuição de função está corretamente identificada como qualificada ou ativa.

 ![Marque para ver se a atribuição de função está visível como qualificada ou ativa](./media/azure-ad-custom-roles-assign/verify-assignments.png)

## <a name="next-steps"></a>Próximas etapas

- [Ativar uma função personalizada do Azure AD](azure-ad-custom-roles-assign.md)
- [Remover ou atualizar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-update-remove.md)
- [Configurar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-configure.md)
- [Definições de função no Azure AD](../roles/permissions-reference.md)
