---
title: Atribuir funções do Azure AD no PIM - Azure Active Directory | Microsoft Docs
description: Saiba como atribuir funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1760d0e0bd356a05d84c07eda005e0526da5d13
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476517"
---
# <a name="assign-azure-ad-roles-in-pim"></a>Atribuir funções do Azure AD no PIM

Com o Azure Active Directory (Azure AD), um Administrador Global pode fazer **permanente** atribuições de função de administrador do AD do Azure. Essas atribuições de função podem ser criadas usando o [portal do Azure](../users-groups-roles/directory-assign-admin-roles.md) ou usando [comandos do PowerShell](/powershell/module/azuread#directory_roles).

O serviço do Azure AD Privileged Identity Management (PIM) também permite que os administradores de função com privilégios criar atribuições de função de administrador permanente. Além disso, os administradores de função com privilégios pode fazer os usuários **qualificados** para funções de administrador do AD do Azure. Um administrador qualificado pode ativar a função quando necessário e suas permissões expirarão assim que forem feitas.

## <a name="make-a-user-eligible-for-a-role"></a>Qualificar um usuário para uma função

Siga estas etapas para tornar um usuário qualificado para uma função de administrador do AD do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função [Administrador de Funções com Privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

    Para obter informações sobre como permitir acesso para que outro administrador gerencie o PIM, veja [conceder acesso a outros administradores para gerenciar o PIM](pim-how-to-give-access-to-pim.md).

1. Abra o **Azure AD Privileged Identity Management**.

    Se você ainda não iniciou o PIM no portal do Azure, acesse [Começar a usar o PIM](pim-getting-started.md).

1. Clique em **funções do Microsoft Azure Active Directory**.

1. Clique em **Funções** ou **Membros**.

    ![Funções do Azure AD com funções e membros de opções de menu realçado](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Clique em **Adicionar Membro** para abrir Adicionar membros gerenciados.

1. Clique em **Selecionar uma função**, clique em uma função que você quer gerenciar e, em seguida, clique em **Selecionar**.

    ![Painel selecionar uma função listando as funções do Azure AD](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Clique em **Selecionar membros**, selecione os usuários que você quer atribuir à função e, em seguida, clique em **Selecionar**.

    ![Selecione o painel de membros em que você pode selecionar um usuário](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Em Adicionar membros gerenciados, clique em **OK** para adicionar o usuário à função.

1. Na lista de funções, clique na função que você acabou de atribuir para ver a lista de membros.

     Quando a função for atribuída, o usuário que você selecionou aparecerá na lista de membros como **Qualificado** para a função.

    ![Membros de uma função são listados juntamente com seu estado de ativação](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Agora que o usuário está qualificado para a função, avise-o que ele pode ativá-lo seguindo as instruções em [ativar minhas funções do Azure AD no PIM](pim-how-to-activate-role.md).

    Os administradores qualificados são solicitados a registrar na MFA (Autenticação Multifator do Microsoft Azure) durante a ativação. Se um usuário não puder se registrar na MFA ou estiver usando uma conta da Microsoft (geralmente, @outlook.com), será preciso torná-lo permanente em todas as funções.

## <a name="make-a-role-assignment-permanent"></a>Tornar uma atribuição de função permanente

Por padrão, os novos usuários só são elegíveis para uma função de administrador do AD do Azure. Siga estas etapas se quiser tornar uma atribuição de função permanente.

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **funções do Microsoft Azure Active Directory**.

1. Clique em **Membros**.

    ![Funções do Azure AD - estado de ativação e a função mostrando a lista de membros](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Clique em uma função **Qualificada** que você deseja tornar permanente.

1. Clique em **Mais** e então clique em **Tornar perm**.

    ![Abra o painel listando um usuário que está qualificado para uma função com mais opções de menu](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    A função agora está listada como **permanente**.

    ![Lista de membros que mostra o estado de ativação e a função que agora é permanente](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Remover um usuário de uma função

É possível remover usuários de atribuições de função, mas certifique-se de que sempre haja pelo menos um usuário que seja um Administrador Global permanente. Se não tiver certeza de quais os usuários ainda precisam das suas atribuições de função, você poderá [iniciar uma revisão de acesso para a função](pim-how-to-start-security-review.md).

Siga estas etapas para remover um usuário específico de uma função de administrador do AD do Azure.

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **funções do Microsoft Azure Active Directory**.

1. Clique em **Membros**.

    ![Funções do Azure AD - stat de função e a ativação mostrando a lista de membros](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Clique em uma atribuição de função que você queira remover.

1. Clique em **Mais** e, em seguida, clique em **Remover**.

    ![Abra o painel listando um usuário que tenha uma função permanente com mais opções de menu](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Na mensagem que pede confirmação, clique em **Sim**.

    ![Mensagem perguntando se você deseja remover membro da função](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    A atribuição de função será removida.

## <a name="authorization-error-when-assigning-roles"></a>Erro de autorização ao atribuir funções

Se você habilitou recentemente o PIM para uma assinatura e você receber um erro de autorização ao tentar se tornar um usuário qualificado para uma função de administrador do AD do Azure, ele pode ser porque o princípio de serviço do MS-PIM ainda não tem as permissões apropriadas. A entidade de serviço do MS-PIM precisa ter a função de [Administrador de Acesso do Usuário](../../role-based-access-control/built-in-roles.md#user-access-administrator) para atribuir funções a outros usuários. Em vez de esperar até que o MS-PIM receba a função de Administrador de Acesso do Usuário, atribua-a manualmente.

Siga estas etapas para atribuir a função de Administrador de Acesso do Usuário à entidade de serviço do MS-PIM para uma assinatura.

1. Entre no portal do Azure como Administrador Global.

1. Escolha **Todos os serviços** e **Assinaturas**.

1. Escolha sua assinatura.

1. Clique em **Controle de acesso (IAM)** .

1. Escolha **Atribuições de função** para ver a lista atual de atribuições de função no escopo da assinatura.

   ![Folha IAM (controle) de acesso para uma assinatura](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Verifique se a entidade de serviço **MS-PIM** tem a função **Administrador de Acesso do Usuário**.

1. Caso contrário, escolha **Adicionar atribuição de função** para abrir o painel **Adicionar atribuição de função**.

1. Na lista suspensa **Função**, selecione a função **Administrador de Acesso do Usuário**.

1. Na lista **Selecionar**, localize e selecione a entidade de serviço **MS-PIM**.

   ![Adicionar painel de atribuição de função - adicionar permissões para a entidade de serviço do MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Escolha **Salvar** para atribuir a função.

   Após alguns momentos, verifique se a entidade de serviço do MS-PIM tem a função Administrador de Acesso do Usuário no escopo da assinatura.

   ![Folha IAM (controle) de acesso, mostrando o administrador de acesso do usuário a atribuição de função para o PIM MS](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)


## <a name="next-steps"></a>Próximas etapas

- [Definir configurações de função de administrador do AD do Azure no PIM](pim-how-to-change-default-settings.md)
- [Atribuir funções de recurso do Azure no PIM](pim-resource-roles-assign-roles.md)
