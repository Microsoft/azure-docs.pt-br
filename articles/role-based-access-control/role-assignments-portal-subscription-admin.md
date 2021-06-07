---
title: Atribuir a um usuário a função de administrador de uma assinatura do Azure – RBAC do Azure
description: Saiba como tornar um usuário administrador de uma assinatura do Azure usando o portal do Azure e o RBAC do Azure (controle de acesso baseado em função do Azure).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: dec5888127ed1fc291bec244a44cfb71e343e3bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100556844"
---
# <a name="assign-a-user-as-an-administrator-of-an-azure-subscription"></a>Atribuir a um usuário a função de administrador de uma assinatura do Azure

Para tornar um usuário administrador de uma assinatura do Azure, atribua-o à função [Proprietário](built-in-roles.md#owner) no escopo da assinatura. A função Proprietário permite ao usuário acesso completo a todos os recursos na assinatura, inclusive a permissão para conceder acesso a outras pessoas. Essas etapas são as mesmas que as de qualquer outra atribuição de função.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-open-the-subscription"></a>Etapa 1: abrir a assinatura

1. Entre no [portal do Azure](https://portal.azure.com).

1. Na caixa Pesquisar na caixa superior, pesquise assinaturas.

    ![No portal do Azure, pesquise grupos de recursos](./media/shared/sub-portal-search.png)

1. Clique na assinatura que você quer usar.

    A seguir, é mostrado um exemplo da assinatura.

    ![Visão geral do grupo de recursos](./media/shared/sub-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>Etapa 2: abrir o painel Adicionar atribuição de função

O **controle de acesso (IAM)** é a página usada normalmente para atribuir funções para conceder acesso aos recursos do Azure. Isso também é conhecido como IAM (gerenciamento de identidade e de acesso) e aparece em vários locais no portal do Azure.

1. Clique em **IAM (Controle de Acesso)**.

    O exemplo a seguir mostra um exemplo de página Controle de Acesso (IAM) para uma assinatura.

    ![Página Controle de Acesso (IAM) para um grupo de recursos](./media/shared/sub-access-control.png)

1. Clique na guia **Atribuições de função** para ver todas as atribuições de função nesse escopo.

1. Clique em **Adicionar** > **Adicionar atribuição de função**.
   Se você não tiver permissões para atribuir funções, a opção Adicionar atribuição de função será desativada.

   ![Adicionar menu de atribuição de função](./media/shared/add-role-assignment-menu.png)

    O painel Adicionar atribuição de função é aberto.

   ![Adicionar painel de atribuição de função](./media/shared/add-role-assignment.png)

## <a name="step-3-select-the-owner-role"></a>Etapa 3: selecionar a função Proprietário

A função [Proprietário](built-in-roles.md#owner) concede acesso completo para gerenciar todos os recursos, incluindo a capacidade de atribuir funções no RBAC do Azure. Você deve designar no máximo três proprietários de assinaturas para reduzir o potencial de violação por um proprietário comprometido.

- Na lista **Função**, selecione a função **Proprietário**.

   ![Selecionar a função Proprietário no painel Adicionar atribuição de função](./media/role-assignments-portal-subscription-admin/add-role-assignment-role-owner.png)

## <a name="step-4-select-who-needs-access"></a>Etapa 4: selecionar quem precisa de acesso

1. Na lista **Atribuir acesso a**, selecione **Usuário, grupo ou entidade de serviço**.

1. Na seção **Selecionar**, procure o usuário inserindo uma cadeia de caracteres ou rolando a lista.

   ![Selecionar usuário em Adicionar atribuição de função](./media/role-assignments-portal-subscription-admin/add-role-assignment-user-admin.png)

1. Depois de localizar o usuário, clique para selecioná-lo.

## <a name="step-5-assign-role"></a>Etapa 5: atribuir função

1. Para atribuir a função, clique em **Salvar**.

   Após alguns instantes, o usuário é atribuído à função no escopo selecionado.

1. Na guia **Atribuições de função**, verifique se você vê a atribuição de função na lista.

    ![Adicionar de atribuição de função salva](./media/role-assignments-portal-subscription-admin/sub-role-assignments-owner.png)

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções do Azure usando o portal do Azure](role-assignments-portal.md)
- [Listar atribuições de função do Azure usando o portal do Azure](role-assignments-list-portal.md)
- [Organizar seus recursos com grupos de gerenciamento do Azure](../governance/management-groups/overview.md)
