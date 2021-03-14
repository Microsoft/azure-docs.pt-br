---
title: Atribuir funções do Azure AD no PIM-Azure Active Directory | Microsoft Docs
description: Saiba como atribuir funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 02/16/2021
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd4374067fe0070c379a76ef5f59bb6aef5b29fc
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123098"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Atribuir funções do Azure AD no Privileged Identity Management

Com o Azure Active Directory (AD do Azure), um administrador global pode fazer atribuições de função de administrador do Azure AD **permanentes** . Essas atribuições de função podem ser criadas usando o [portal do Azure](../roles/permissions-reference.md) ou usando [comandos do PowerShell](/powershell/module/azuread#directory_roles).

O serviço PIM (Azure AD Privileged Identity Management) também permite que administradores de função com privilégios façam atribuições de função de administrador permanentes. Além disso, os administradores de função com privilégios podem tornar os usuários **qualificados** para as funções de administrador do Azure AD. Um administrador qualificado pode ativar a função quando necessário e suas permissões expirarão assim que forem feitas.

## <a name="determine-your-version-of-pim"></a>Determinar sua versão do PIM

A partir de novembro de 2019, a parte das funções do Azure AD da Privileged Identity Management está sendo atualizada para uma nova versão que corresponde às experiências de funções de recurso do Azure. Isso cria recursos adicionais, bem como [as alterações na API existente](azure-ad-roles-features.md#api-changes). Enquanto a nova versão está sendo distribuída, os procedimentos que você seguir neste artigo dependem da versão do Privileged Identity Management que você tem atualmente. Siga as etapas nesta seção para determinar qual versão do Privileged Identity Management você tem. Depois de saber sua versão do Privileged Identity Management, você pode selecionar os procedimentos neste artigo que correspondem a essa versão.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que esteja na função de [administrador de função com privilégios](../roles/permissions-reference.md#privileged-role-administrator) .
1. Abra **Azure ad Privileged Identity Management**. Se você tiver uma faixa na parte superior da página Visão geral, siga as instruções na **nova guia versão** deste artigo. Caso contrário, siga as instruções na guia **versão anterior** .

  [![Selecione Azure AD > Privileged Identity Management.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nova versão](#tab/new)

## <a name="assign-a-role"></a>Atribuir uma função

Siga estas etapas para tornar um usuário qualificado para uma função de administrador do Azure AD.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função de [administrador de função com privilégios](../roles/permissions-reference.md#privileged-role-administrator) .

    Para obter informações sobre como conceder a outro administrador acesso para gerenciar Privileged Identity Management, consulte [conceder acesso a outros administradores para gerenciar Privileged Identity Management](pim-how-to-give-access-to-pim.md).

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **funções do Azure ad**.

1. Selecione **funções** para ver a lista de funções para permissões do Azure AD.

    ![Captura de tela da página "funções" com a ação "Adicionar atribuições" selecionada.](./media/pim-how-to-add-role-to-user/roles-list.png)

1. Selecione **Adicionar atribuições** para abrir a página **Adicionar atribuições** .

1. Selecione **selecionar uma função** para abrir a página **selecionar uma função** .

    ![Novo painel de atribuição](./media/pim-how-to-add-role-to-user/select-role.png)

1. Selecione uma função que você deseja atribuir, selecione um membro a quem você deseja atribuir à função e, em seguida, selecione **Avançar**.

1. Na lista **tipo de atribuição** , no **painel configurações de associação** , selecione **qualificado** ou **ativo**.

    - Atribuições **qualificadas** exigem que o membro da função execute uma ação para usar a função. As ações podem incluir a execução de uma verificação de MDA (Autenticação Multifator), fornecimento de uma justificativa comercial ou solicitação de aprovação dos aprovadores designados.

    - As atribuições **ativas** não exigem que o membro execute qualquer ação para usar a função. Membros atribuídos como ativos sempre possuem privilégios atribuídos pela função.

1. Para especificar uma duração de atribuição específica, adicione as caixas de data e hora de início e término. Quando terminar, selecione **atribuir** para criar a nova atribuição de função.

    ![Configurações de associação - data e hora](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. Depois que a função é atribuída, uma notificação de status de atribuição é exibida.

    ![Nova atribuição - Notificação](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="assign-a-role-with-restricted-scope"></a>Atribuir uma função com escopo restrito

Para determinadas funções, o escopo das permissões concedidas pode ser restrito a uma única unidade de administração, entidade de serviço ou aplicativo. Esse procedimento é um exemplo de como atribuir uma função que tenha o escopo de uma unidade administrativa. Para obter uma lista de funções que dão suporte a escopo por meio da unidade administrativa, consulte [atribuir funções no escopo a uma unidade administrativa](../roles/admin-units-assign-roles.md). Este recurso está sendo lançado no momento para as organizações do Azure AD.

1. Entre no centro de [Administração do Azure Active Directory](https://aad.portal.azure.com) com permissões de administrador de função com privilégios.

1. Selecione **Azure Active Directory**  >  **funções e administradores**.

1. Selecione o **administrador do usuário**.

    ![O comando adicionar atribuição está disponível quando você abre uma função no portal](./media/pim-how-to-add-role-to-user/add-assignment.png)

1. Selecione **Adicionar atribuições**.

    ![Quando uma função dá suporte a escopo, você pode selecionar um escopo](./media/pim-how-to-add-role-to-user/add-scope.png)

1. Na página **Adicionar atribuições** , você pode:

   - Selecione um usuário ou grupo a ser atribuído à função
   - Selecione o escopo da função (nesse caso, unidades administrativas)
   - Selecionar uma unidade administrativa para o escopo

Para obter mais informações sobre como criar unidades administrativas, consulte [Adicionar e remover unidades administrativas](../roles/admin-units-manage.md).

## <a name="update-or-remove-an-existing-role-assignment"></a>Atualizar ou remover uma atribuição de função existente

Siga estas etapas para atualizar ou remover uma atribuição de função existente. **Somente clientes licenciados do Azure ad P2**: não atribua um grupo como ativo a uma função por meio do Azure AD e do PRIVILEGED Identity Management (PIM). Para obter uma explicação detalhada, consulte [problemas conhecidos](../roles/groups-concept.md#known-issues).

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **funções do Azure ad**.

1. Selecione **funções** para ver a lista de funções do Azure AD.

1. Selecione a função que você deseja atualizar ou remover.

1. Localize a atribuição de função nas guias **Funções qualificadas** ou **Funções ativas**.

    ![Atualizar ou remover atribuição de função](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. Selecione **Atualizar** ou **Remover** para atualizar ou remover a atribuição de função.

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>Qualificar um usuário para uma função

Siga estas etapas para tornar um usuário qualificado para uma função de administrador do Azure AD.

1. Selecione **funções** ou **Membros**.

    ![abrir funções do Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Selecione **Adicionar membro** para abrir **adicionar membros gerenciados**.

1. Selecione **selecionar uma função**, selecione uma função que você deseja gerenciar e selecione **selecionar**.

    ![Selecione uma função](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Selecione **selecionar Membros**, selecione os usuários que você deseja atribuir à função e, em seguida, selecione **selecionar**.

    ![Selecione um usuário ou grupo para atribuir](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Em **adicionar membros gerenciados**, selecione **OK** para adicionar o usuário à função.

1. Na lista de funções, selecione a função que você acabou de atribuir para ver a lista de membros.

     Quando a função for atribuída, o usuário selecionado aparecerá na lista de membros como **qualificado** para a função.

    ![Usuário qualificado para uma função](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Agora que o usuário está qualificado para a função, avise-o de que ele pode ativá-lo de acordo com as instruções em [Ativar minhas funções do Azure AD no Privileged Identity Management](pim-how-to-activate-role.md).

    Os administradores qualificados são solicitados a se registrar para a autenticação multifator do Azure AD durante a ativação. Se um usuário não puder se registrar para MFA ou estiver usando um conta Microsoft (como @outlook.com ), você precisará torná-los permanentes em todas as suas funções.

## <a name="make-a-role-assignment-permanent"></a>Tornar uma atribuição de função permanente

Por padrão, novos usuários só estão *qualificados* para uma função de administrador do Azure AD. Siga estas etapas se quiser tornar uma atribuição de função permanente.

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **funções do Azure ad**.

1. Selecione **Membros**.

    ![Lista de membros](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selecione uma função **qualificada** que você deseja tornar permanente.

1. Selecione **mais** e, em seguida, selecione **tornar Perm**.

    ![Tornar a atribuição de função permanente](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    A função agora está listada como **permanente**.

    ![Lista de membros com alteração permanente](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Remover um usuário de uma função

É possível remover usuários de atribuições de função, mas certifique-se de que sempre haja pelo menos um usuário que seja um Administrador Global permanente. Se não tiver certeza de quais os usuários ainda precisam das suas atribuições de função, você poderá [iniciar uma revisão de acesso para a função](pim-how-to-start-security-review.md).

Siga estas etapas para remover um usuário específico de uma função de administrador do Azure AD.

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **funções do Azure ad**.

1. Selecione **Membros**.

    ![Lista de membros](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selecione uma atribuição de função que você deseja remover.

1. Selecione **mais** e, em seguida, selecione **remover**.

    ![Remover uma função](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Na mensagem que solicita que você confirme, selecione **Sim**.

    ![Confirmar a remoção](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    A atribuição de função será removida.

## <a name="authorization-error-when-assigning-roles"></a>Erro de autorização ao atribuir funções

Se você tiver habilitado recentemente Privileged Identity Management para uma assinatura e receber um erro de autorização ao tentar tornar um usuário qualificado para uma função de administrador do Azure AD, pode ser porque a entidade de serviço MS-PIM ainda não tem as permissões apropriadas. A entidade de serviço do MS-PIM deve ter a função de [administrador de acesso do usuário](../../role-based-access-control/built-in-roles.md#user-access-administrator) para atribuir funções a outras pessoas. Em vez de esperar até que o MS-PIM receba a função de Administrador de Acesso do Usuário, atribua-a manualmente.

Siga estas etapas para atribuir a função de Administrador de Acesso do Usuário à entidade de serviço do MS-PIM para uma assinatura.

1. Entre no portal do Azure como Administrador Global.

1. Escolha **Todos os serviços** e **Assinaturas**.

1. Escolha sua assinatura.

1. Clique em **Controle de acesso (IAM)**.

1. Escolha **Atribuições de função** para ver a lista atual de atribuições de função no escopo da assinatura.

   ![Folha IAM (controle) de acesso para uma assinatura](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Verifique se a entidade de serviço **MS-PIM** tem a função **Administrador de Acesso do Usuário**.

1. Caso contrário, escolha **Adicionar atribuição de função** para abrir o painel **Adicionar atribuição de função**.

1. Na lista suspensa **Função**, selecione a função **Administrador de Acesso do Usuário**.

1. Na lista **Selecionar**, localize e selecione a entidade de serviço **MS-PIM**.

   ![Adicionar painel de atribuição de função-adicionar permissões para entidade de serviço do Microsoft PIM](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Escolha **Salvar** para atribuir a função.

   Após alguns momentos, verifique se a entidade de serviço do MS-PIM tem a função Administrador de Acesso do Usuário no escopo da assinatura.

   ![Página controle de acesso mostrando a atribuição de função de administrador de acesso do usuário para a entidade de serviço MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>Próximas etapas

- [Definir as configurações de função de administrador do Azure AD no Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Atribuir funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-assign-roles.md)