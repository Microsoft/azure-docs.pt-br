---
title: 'Tutorial: Conceder a um grupo acesso aos recursos do Azure usando o Azure PowerShell – RBAC do Azure'
description: Saiba como conceder a um grupo acesso aos recursos do Azure usando o Azure PowerShell e o RBAC do Azure (controle de acesso baseado em função do Azure) neste tutorial.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.openlocfilehash: e25db6213c99afaa46732831534956b01adba0b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555219"
---
# <a name="tutorial-grant-a-group-access-to-azure-resources-using-azure-powershell"></a>Tutorial: Conceder a um grupo acesso aos recursos do Azure usando o Azure PowerShell

O [Azure RBAC (controle de acesso baseado em função do Azure)](overview.md) é a maneira usada para gerenciar o acesso aos recursos no Azure. Neste tutorial, você concede acesso a um grupo para exibir tudo em uma assinatura e gerenciar tudo em um grupo de recursos usando o Azure PowerShell.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Conceder acesso a um grupo em escopos diferentes
> * Relacionar acesso
> * Remover acesso

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Neste tutorial, você precisará de:

- Permissões para criar grupos no Azure Active Directory (ou ter um grupo existente)
- [Azure Cloud Shell](../cloud-shell/quickstart-powershell.md)

## <a name="role-assignments"></a>Atribuições de função

No RBAC do Azure, para conceder acesso, você cria uma atribuição de função. Uma atribuição de função consiste em três elementos: entidade de segurança, definição de função e escopo. Aqui estão as duas atribuições de função que você executará neste tutorial:

| Entidade de segurança | Definição de função | Escopo |
| --- | --- | --- |
| Agrupar<br>(Grupo de Tutorial de RBAC) | [Leitor](built-in-roles.md#reader) | Subscription |
| Agrupar<br>(Grupo de Tutorial de RBAC)| [Colaborador](built-in-roles.md#contributor) | Resource group<br>(rbac-tutorial-resource-group) |

   ![Atribuições de função de um grupo](./media/tutorial-role-assignments-group-powershell/rbac-role-assignments.png)

## <a name="create-a-group"></a>Criar um grupo

Para atribuir uma função, você precisa de um usuário, grupo ou entidade de serviço. Se você ainda não tiver um grupo, crie um.

- No Azure Cloud Shell, crie um novo grupo usando o comando [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup).

   ```azurepowershell
   New-AzureADGroup -DisplayName "RBAC Tutorial Group" `
     -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
   ```

   ```Example
   ObjectId                             DisplayName         Description
   --------                             -----------         -----------
   11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
   ```

Se você não tiver permissões para criar grupos, poderá experimentar o [Tutorial: Conceder a um usuário acesso aos recursos do Azure usando o Azure PowerShell](tutorial-role-assignments-user-powershell.md) em vez disso.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Você usa um grupo de recursos para mostrar como atribuir uma função em um escopo do grupo de recursos.

1. Obtenha uma lista de locais de região usando o comando [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

   ```azurepowershell
   Get-AzLocation | select Location
   ```

1. Selecione um local perto de você e atribua-o a uma variável.

   ```azurepowershell
   $location = "westus"
   ```

1. Crie um novo grupo de recursos usando o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

   ```azurepowershell
   New-AzResourceGroup -Name "rbac-tutorial-resource-group" -Location $location
   ```

   ```Example
   ResourceGroupName : rbac-tutorial-resource-group
   Location          : westus
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
   ```

## <a name="grant-access"></a>Conceder acesso

Para conceder acesso ao grupo, você usa o comando [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) para atribuir uma função. Você deve especificar a entidade de segurança, a definição de função e o escopo.

1. Obtenha a ID de objeto do grupo usando o comando [Get-AzureADGroup](/powershell/module/azuread/new-azureadgroup).

    ```azurepowershell
    Get-AzureADGroup -SearchString "RBAC Tutorial Group"
    ```

    ```Example
    ObjectId                             DisplayName         Description
    --------                             -----------         -----------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
    ```

1. Salve a ID de objeto do grupo em uma variável.

    ```azurepowershell
    $groupId = "11111111-1111-1111-1111-111111111111"
    ```

1. Obtenha a ID de sua assinatura usando o comando [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

    ```azurepowershell
    Get-AzSubscription
    ```

    ```Example
    Name     : Pay-As-You-Go
    Id       : 00000000-0000-0000-0000-000000000000
    TenantId : 22222222-2222-2222-2222-222222222222
    State    : Enabled
    ```

1. Salve o escopo da assinatura em uma variável.

    ```azurepowershell
    $subScope = "/subscriptions/00000000-0000-0000-0000-000000000000"
    ```

1. Atribua a função [Leitor](built-in-roles.md#reader) ao grupo no escopo da assinatura.

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

1. Atribuir a função [Colaborador](built-in-roles.md#contributor) ao grupo no escopo do grupo de recursos.

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

## <a name="list-access"></a>Relacionar acesso

1. Para verificar o acesso para a assinatura, use o comando [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) para listar as atribuições de função.

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId $groupId -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    Na saída, você pode ver que a função de Leitor foi atribuída ao Grupo do Tutorial de RBAC no escopo da assinatura.

1. Para verificar o acesso ao grupo de recursos, use o comando [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) para listar as atribuições de função.

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId $groupId -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    Na saída, você pode ver que a função de Leitor e Colaborador foram atribuídas ao Grupo do Tutorial de RBAC. A função de Colaborador está no escopo rbac-tutorial-resource-group e a função de Leitor é herdada no escopo da assinatura.

## <a name="optional-list-access-using-the-azure-portal"></a>(Opcional) Acesso à lista usando o Portal do Azure

1. Para ver como as atribuições de função são exibidas no portal do Azure, veja a folha **Controle de acesso (IAM)** na assinatura.

    ![Atribuições de função para um grupo no escopo da assinatura](./media/tutorial-role-assignments-group-powershell/role-assignments-subscription.png)

1. Veja a folha **Controle de acesso (IAM)** no grupo de recursos.

    ![Atribuições de função para um grupo no grupo de recursos](./media/tutorial-role-assignments-group-powershell/role-assignments-resource-group.png)

## <a name="remove-access"></a>Remover acesso

Para remover o acesso para usuários, grupos e aplicativos, use [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) para remover a atribuição de função.

1. Use o comando a seguir para remover a atribuição de função de Colaborador do grupo no escopo do grupo de recursos.

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. Use o comando a seguir para remover a atribuição de função de Leitor do grupo no escopo da assinatura.

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar os recursos criados por este tutorial, exclua o grupo de recursos e o grupo.

1. Exclua o grupo de recursos usando o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

    ```azurepowershell
    Remove-AzResourceGroup -Name "rbac-tutorial-resource-group"
    ```

    ```Example
    Confirm
    Are you sure you want to remove resource group 'rbac-tutorial-resource-group'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```
    
1. Quando solicitado a confirmar, digite **Y**. Levará alguns segundos para excluir.

1. Exclua o grupo usando o comando [Remove-AzureADGroup](/powershell/module/azuread/remove-azureadgroup).

    ```azurepowershell
    Remove-AzureADGroup -ObjectId $groupId
    ```
    
    Se você receber um erro ao tentar excluir o grupo, também poderá excluir o grupo no portal.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Atribuir funções do Azure usando o Azure PowerShell](role-assignments-powershell.md)