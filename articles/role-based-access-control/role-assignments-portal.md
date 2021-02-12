---
title: Adicionar ou remover atribuições de função do Azure usando o portal do Azure-RBAC do Azure
description: Saiba como conceder acesso aos recursos do Azure para usuários, grupos, entidades de serviço ou identidades gerenciadas usando o portal do Azure e o controle de acesso baseado em função do Azure (RBAC do Azure).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: f1753e7bc50fa9ff2c5512696a37dae7578f23b4
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117377"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>Adicionar ou remover atribuições de função do Azure usando o portal do Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Este artigo descreve como atribuir funções usando o portal do Azure.

Se você precisar atribuir funções de administrador no Azure Active Directory, consulte [Exibir e atribuir funções de administrador no Azure Active Directory](../active-directory/roles/manage-roles-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="add-a-role-assignment"></a>Adicionar uma atribuição de função

No RBAC do Azure, para conceder acesso a um recurso do Azure, você adiciona uma atribuição de função. Siga estas etapas para atribuir uma função. Para obter uma visão geral de alto nível das etapas, consulte [etapas para adicionar uma atribuição de função](role-assignments-steps.md).

### <a name="step-1-identify-the-needed-scope"></a>Etapa 1: identificar o escopo necessário

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)]

[!INCLUDE [Scope for Azure RBAC least privilege](../../includes/role-based-access-control/scope-least.md)] Para obter mais informações sobre escopo, consulte [entender o escopo](scope-overview.md).

![Níveis de escopo para o Azure RBAC](../../includes/role-based-access-control/media/scope-levels.png)

1. Entre no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa na parte superior, pesquise o escopo ao qual você deseja conceder acesso. Por exemplo, pesquise **grupos de gerenciamento**, **assinaturas**, grupos de **recursos** ou um recurso específico.

    ![portal do Azure Pesquisar o grupo de recursos](./media/shared/rg-portal-search.png)

1. Clique no recurso específico para esse escopo.

    A seguir é mostrado um exemplo de grupo de recursos.

    ![Visão geral do grupo de recursos](./media/shared/rg-overview.png)

### <a name="step-2-open-the-add-role-assignment-pane"></a>Etapa 2: abrir o painel Adicionar atribuição de função

O **controle de acesso (iam)** é a página que você normalmente usa para atribuir funções para conceder acesso aos recursos do Azure. Ele também é conhecido como IAM (gerenciamento de identidades e acesso) e aparece em vários locais na portal do Azure.

1. Clique em **IAM (Controle de Acesso)**.

    O exemplo a seguir mostra a página Controle de acesso (IAM) para um grupo de recursos.

    ![Página de controle de acesso (IAM) para um grupo de recursos](./media/shared/rg-access-control.png)

1. Clique na guia **atribuições de função** para exibir as atribuições de função nesse escopo.

1. Clique em **Adicionar**  >  **Adicionar atribuição de função**.
   Se você não tiver permissões para atribuir funções, a opção Adicionar atribuição de função será desativada.

   ![Adicionar menu de atribuição de função](./media/shared/add-role-assignment-menu.png)

    O painel Adicionar atribuição de função é aberto.

   ![Adicionar painel de atribuição de função](./media/shared/add-role-assignment.png)

### <a name="step-3-select-the-appropriate-role"></a>Etapa 3: selecione a função apropriada

1. Na lista **função** , pesquise ou role para localizar a função que você deseja atribuir.

    Para ajudá-lo a determinar a função apropriada, você pode passar o mouse sobre o ícone de informações para exibir uma descrição para a função. Para obter informações adicionais, você pode exibir o artigo [funções internas do Azure](built-in-roles.md) .

   ![Selecionar função em Adicionar atribuição de função](./media/role-assignments-portal/add-role-assignment-role.png)

1. Clique para selecionar a função.

### <a name="step-4-select-who-needs-access"></a>Etapa 4: Selecione quem precisa de acesso

1. Na lista **atribuir acesso a** , selecione o tipo de entidade de segurança ao qual atribuir acesso.

    | Tipo | Descrição |
    | --- | --- |
    | **Usuário, grupo ou entidade de serviço** | Se você quiser atribuir a função a um usuário, grupo ou entidade de serviço (aplicativo), selecione este tipo. |
    | **Identidade gerenciada atribuída pelo usuário** | Se você quiser atribuir a função a uma [identidade gerenciada atribuída pelo usuário](../active-directory/managed-identities-azure-resources/overview.md), selecione este tipo. |
    | *Identidade gerenciada atribuída ao sistema* | Se você quiser atribuir a função a uma [identidade gerenciada atribuída pelo sistema](../active-directory/managed-identities-azure-resources/overview.md), selecione a instância de serviço do Azure na qual a identidade gerenciada está localizada. |

   ![Selecionar tipo de entidade de segurança em Adicionar atribuição de função](./media/role-assignments-portal/add-role-assignment-type.png)

1. Se você selecionou uma identidade gerenciada atribuída pelo usuário ou uma identidade gerenciada atribuída pelo sistema, selecione a **assinatura** na qual a identidade gerenciada está localizada.

1. Na seção **selecionar** , pesquise a entidade de segurança inserindo uma cadeia de caracteres ou rolando a lista.

   ![Selecionar usuário em Adicionar atribuição de função](./media/role-assignments-portal/add-role-assignment-user.png)

1. Depois de encontrar a entidade de segurança, clique para selecioná-la.

### <a name="step-5-assign-role"></a>Etapa 5: atribuir função

1. Para atribuir a função, clique em **salvar**.

   Após alguns instantes, a entidade de segurança é atribuída a função no escopo selecionado.

1. Na guia **atribuições de função** , verifique se você vê a atribuição de função na lista.

    ![Adicionar atribuição de função salva](./media/role-assignments-portal/rg-role-assignments.png)

## <a name="remove-a-role-assignment"></a>Excluir uma atribuição de função

No RBAC do Azure, para remover o acesso de um recurso do Azure, você remove uma atribuição de função. Siga estas etapas para remover uma atribuição de função.

1. Abra **Controle de acesso (IAM)** em um escopo, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso, no qual você deseja remover o acesso.

1. Clique na guia **Atribuições de função** para visualizar todas as atribuições de função nesse escopo.

1. Na lista de atribuições de função, marque a caixa de seleção ao lado de objeto com a atribuição de função de segurança que você deseja remover.

   ![Atribuição de função selecionada a ser removida](./media/role-assignments-portal/rg-role-assignments-select.png)

1. Clique em **Remover**.

   ![Remover mensagem de atribuição de função](./media/role-assignments-portal/remove-role-assignment.png)

1. Na mensagem para remover a atribuição de função exibida, clique em **Sim**.

    Se você vir uma mensagem informando que as atribuições de função herdadas não podem ser removidas, você está tentando remover uma atribuição de função em um escopo filho. Você deve abrir o controle de acesso (IAM) no escopo onde a função foi atribuída e tentar novamente. Uma maneira rápida de abrir o controle de acesso (IAM) no escopo correto é examinar a coluna **escopo** e clicar no link ao lado de **(Herdado)**.

   ![Remover mensagem de atribuição de função para atribuições de função herdadas](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Próximas etapas

- [Atribuir a um usuário a função de administrador de uma assinatura do Azure](role-assignments-portal-subscription-admin.md)
- [Adicionar uma atribuição de função para uma identidade gerenciada](role-assignments-portal-managed-identity.md)
- [Solucionar problemas do RBAC do Azure](troubleshooting.md)
