---
title: 'Tutorial: Conceder acesso aos recursos do Azure para um usuário usando o portal do Azure – RBAC do Azure'
description: Neste tutorial, aprenda a conceder a um usuário acesso aos recursos do Azure usando o portal do Azure e o RBAC do Azure (controle de acesso baseado em função do Azure).
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/22/2019
ms.author: rolyon
ms.openlocfilehash: fcba9cad208c2ac170f91cc06a6db22e271f2a70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100559325"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-the-azure-portal"></a>Tutorial: Conceder acesso aos recursos do Azure para um usuário usando o portal do Azure

O [Azure RBAC (controle de acesso baseado em função do Azure)](overview.md) é a maneira usada para gerenciar o acesso aos recursos no Azure. Neste tutorial, você concede acesso a um usuário para criar e gerenciar máquinas virtuais em um grupo de recursos.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Conceder acesso a um usuário em um escopo do grupo de recursos
> * Remover acesso

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

1. Na lista de navegação, escolha **Grupos de recursos**.

1. Escolha **Adicionar** para abrir a folha **Grupo de recursos**.

   ![Adicionar um novo grupo de recursos](./media/quickstart-assign-role-user-portal/resource-group.png)

1. Para o **Nome do grupo de recursos**, insira **rbac-resource-group**.

1. Selecione uma assinatura e localização.

1. Clique em **Criar** para criar o grupo de recursos.

1. Clique em **Atualizar** para atualizar a lista de grupos de recursos.

   O novo grupo de recursos é exibido em sua lista de grupos de recursos.

   ![Lista de grupos de recursos](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Conceder acesso

No RBAC do Azure, para permitir acesso, atribua uma função do Azure.

1. Na lista de **Grupos de recursos**, clique no novo grupo de recursos **rbac-resource-group**.

1. Clique em **Controle de acesso (IAM)** .

1. Clique na guia **Atribuições de função** para ver a lista atual das atribuições de função.

   ![Folha Controle de acesso (IAM) para o grupo de recursos](./media/quickstart-assign-role-user-portal/access-control.png)

1. Clique em **Adicionar** > **Adicionar atribuição de função** para abrir o painel Adicionar atribuição de função.

   Se você não tiver permissões para atribuir funções, a opção Adicionar atribuição de função será desativada.

   ![Adicionar menu de atribuição de função](./media/shared/add-role-assignment-menu.png)

    O painel Adicionar atribuição de função é aberto.

   ![Adicionar painel de atribuição de função](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. Na lista suspensa **Função**, selecione **Colaborador da Máquina Virtual**.

1. Na lista **Selecionar**, selecione você mesmo ou outro usuário.

1. Clique em **Salvar** para atribuir a função.

   Após alguns instantes, a função Colaborador da Máquina Virtual é atribuída ao usuário no escopo do grupo de recursos rbac-resource-group.

   ![Atribuição de função Colaborador da Máquina Virtual](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Remover acesso

No RBAC do Azure, para remover o acesso, você deve remover uma atribuição de função.

1. Na lista de atribuições de função, adicione uma marca de seleção ao lado do usuário com a função de Colaborador da Máquina Virtual.

1. Clique em **Remover**.

   ![Remover mensagem de atribuição de função](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. Na mensagem para remover a atribuição de função exibida, clique em **Sim**.

## <a name="clean-up"></a>Limpar

1. Na lista de navegação, escolha **Grupos de recursos**.

1. Clique em **rbac-resource-group** para abrir o grupo de recursos.

1. Clique em **Excluir grupo de recursos** para excluir o grupo de recursos.

   ![Excluir grupo de recursos](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. Na folha **Tem certeza de que deseja excluir**, digite o nome do grupo de recursos: **rbac-resource-group**.

1. Clique em **Excluir** para excluir o grupo de recursos.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Conceder a um usuário acesso aos recursos do Azure usando o Azure PowerShell](tutorial-role-assignments-user-powershell.md)
