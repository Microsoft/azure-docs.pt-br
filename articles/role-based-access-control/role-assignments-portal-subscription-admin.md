---
title: Atribuir um usuário como administrador de uma assinatura do Azure-RBAC do Azure
description: Saiba como tornar um usuário administrador de uma assinatura do Azure usando o portal do Azure e o controle de acesso baseado em função do Azure (RBAC do Azure).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: dec5888127ed1fc291bec244a44cfb71e343e3bb
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556844"
---
# <a name="assign-a-user-as-an-administrator-of-an-azure-subscription"></a>Atribuir a um usuário a função de administrador de uma assinatura do Azure

Para tornar um usuário administrador de uma assinatura do Azure, atribua-o à função [Proprietário](built-in-roles.md#owner) no escopo da assinatura. A função proprietário dá ao usuário acesso completo a todos os recursos na assinatura, incluindo a permissão para conceder acesso a outras pessoas. Essas etapas são as mesmas que as de qualquer outra atribuição de função.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-open-the-subscription"></a>Etapa 1: abrir a assinatura

1. Entre no [portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa na parte superior, pesquise por assinaturas.

    ![portal do Azure Pesquisar o grupo de recursos](./media/shared/sub-portal-search.png)

1. Clique na assinatura que você deseja usar.

    Veja a seguir uma assinatura de exemplo.

    ![Visão geral do grupo de recursos](./media/shared/sub-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>Etapa 2: abrir o painel Adicionar atribuição de função

O **controle de acesso (iam)** é a página que você normalmente usa para atribuir funções para conceder acesso aos recursos do Azure. Ele também é conhecido como IAM (gerenciamento de identidades e acesso) e aparece em vários locais na portal do Azure.

1. Clique em **IAM (Controle de Acesso)**.

    Veja a seguir um exemplo da página de controle de acesso (IAM) para uma assinatura.

    ![Página de controle de acesso (IAM) para um grupo de recursos](./media/shared/sub-access-control.png)

1. Clique na guia **atribuições de função** para exibir as atribuições de função nesse escopo.

1. Clique em **Adicionar**  >  **Adicionar atribuição de função**.
   Se você não tiver permissões para atribuir funções, a opção Adicionar atribuição de função será desativada.

   ![Adicionar menu de atribuição de função](./media/shared/add-role-assignment-menu.png)

    O painel Adicionar atribuição de função é aberto.

   ![Adicionar painel de atribuição de função](./media/shared/add-role-assignment.png)

## <a name="step-3-select-the-owner-role"></a>Etapa 3: selecionar a função de proprietário

A função de [proprietário](built-in-roles.md#owner) concede acesso completo para gerenciar todos os recursos, incluindo a capacidade de atribuir funções no RBAC do Azure. Você deve ter um máximo de 3 proprietários de assinatura para reduzir o potencial de violação por um proprietário comprometido.

- Na lista **função** , selecione a função **proprietário** .

   ![Selecione a função proprietário no painel Adicionar atribuição de função](./media/role-assignments-portal-subscription-admin/add-role-assignment-role-owner.png)

## <a name="step-4-select-who-needs-access"></a>Etapa 4: Selecione quem precisa de acesso

1. Na lista **atribuir acesso a** , selecione **usuário, grupo ou entidade de serviço**.

1. Na seção **selecionar** , procure o usuário inserindo uma cadeia de caracteres ou rolando a lista.

   ![Selecionar usuário em Adicionar atribuição de função](./media/role-assignments-portal-subscription-admin/add-role-assignment-user-admin.png)

1. Depois de localizar o usuário, clique para selecioná-lo.

## <a name="step-5-assign-role"></a>Etapa 5: atribuir função

1. Para atribuir a função, clique em **salvar**.

   Depois de alguns instantes, o usuário recebe a função no escopo selecionado.

1. Na guia **atribuições de função** , verifique se você vê a atribuição de função na lista.

    ![Adicionar atribuição de função salva](./media/role-assignments-portal-subscription-admin/sub-role-assignments-owner.png)

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções do Azure usando o portal do Azure](role-assignments-portal.md)
- [Listar atribuições de função do Azure usando o portal do Azure](role-assignments-list-portal.md)
- [Organizar seus recursos com grupos de gerenciamento do Azure](../governance/management-groups/overview.md)
