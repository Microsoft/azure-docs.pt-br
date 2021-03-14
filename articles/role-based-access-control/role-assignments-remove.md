---
title: Remover atribuições de função do Azure-RBAC do Azure
description: Saiba como remover o acesso aos recursos do Azure para usuários, grupos, entidades de serviço ou identidades gerenciadas usando portal do Azure, Azure PowerShell, CLI do Azure ou API REST.
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 7a3e4853d6dffa7eb2c5cf80846f6f1bd6beba03
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561252"
---
# <a name="remove-azure-role-assignments"></a>Remover atribuições de função do Azure

O [RBAC (controle de acesso baseado em função) do Azure](../../articles/role-based-access-control/overview.md) é o sistema de autorização usado para gerenciar o acesso aos recursos no Azure. Para remover o acesso de um recurso do Azure, você remove uma atribuição de função. Este artigo descreve como remover atribuições de funções usando o portal do Azure, Azure PowerShell, CLI do Azure e a API REST.

## <a name="prerequisites"></a>Pré-requisitos

Para remover as atribuições de função, você deve ter:

- `Microsoft.Authorization/roleAssignments/delete` permissões, como [administrador de acesso do usuário](../../articles/role-based-access-control/built-in-roles.md#user-access-administrator) ou [proprietário](../../articles/role-based-access-control/built-in-roles.md#owner)

## <a name="azure-portal"></a>Portal do Azure

1. Abra **Controle de acesso (IAM)** em um escopo, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso, no qual você deseja remover o acesso.

1. Clique na guia **Atribuições de função** para visualizar todas as atribuições de função nesse escopo.

1. Na lista de atribuições de função, marque a caixa de seleção ao lado de objeto com a atribuição de função de segurança que você deseja remover.

   ![Atribuição de função selecionada a ser removida](./media/role-assignments-remove/rg-role-assignments-select.png)

1. Clique em **Remover**.

   ![Remover mensagem de atribuição de função](./media/role-assignments-remove/remove-role-assignment.png)

1. Na mensagem para remover a atribuição de função exibida, clique em **Sim**.

    Se você vir uma mensagem informando que as atribuições de função herdadas não podem ser removidas, você está tentando remover uma atribuição de função em um escopo filho. Você deve abrir o controle de acesso (IAM) no escopo onde a função foi atribuída e tentar novamente. Uma maneira rápida de abrir o controle de acesso (IAM) no escopo correto é examinar a coluna **escopo** e clicar no link ao lado de **(Herdado)**.

   ![Remover mensagem de atribuição de função para atribuições de função herdadas](./media/role-assignments-remove/remove-role-assignment-inherited.png)

## <a name="azure-powershell"></a>Azure PowerShell

No Azure PowerShell, você remove uma atribuição de função usando [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

O exemplo a seguir remove a atribuição de função de [colaborador de máquina virtual](built-in-roles.md#virtual-machine-contributor) do usuário *patlong \@ contoso.com* no grupo de recursos *Pharma-Sales* :

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales
```

Remove a função [leitor](built-in-roles.md#reader) do grupo de *equipe Ana Mack* com ID 22222222-2222-2222-2222-222222222222 em um escopo de assinatura.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -ObjectId 22222222-2222-2222-2222-222222222222 `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

Remove a função de [leitor de cobrança](built-in-roles.md#billing-reader) do usuário *Alain \@ example.com* no escopo do grupo de gerenciamento.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

Se você receber a mensagem de erro: "as informações fornecidas não são mapeadas para uma atribuição de função", certifique-se de especificar também os `-Scope` `-ResourceGroupName` parâmetros ou. Para obter mais informações, consulte [solucionar problemas do RBAC do Azure](troubleshooting.md#role-assignments-with-identity-not-found).

## <a name="azure-cli"></a>CLI do Azure

No CLI do Azure, você remove uma atribuição de função usando [AZ role Assignment Delete](/cli/azure/role/assignment#az_role_assignment_delete).

O exemplo a seguir remove a atribuição de função de [colaborador de máquina virtual](built-in-roles.md#virtual-machine-contributor) do usuário *patlong \@ contoso.com* no grupo de recursos *Pharma-Sales* :

```azurecli
az role assignment delete --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

Remove a função [leitor](built-in-roles.md#reader) do grupo de *equipe Ana Mack* com ID 22222222-2222-2222-2222-222222222222 em um escopo de assinatura.

```azurecli
az role assignment delete --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

Remove a função de [leitor de cobrança](built-in-roles.md#billing-reader) do usuário *Alain \@ example.com* no escopo do grupo de gerenciamento.

```azurecli
az role assignment delete --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="rest-api"></a>API REST

Na API REST, você remove uma atribuição de função usando [atribuições de função – excluir](/rest/api/authorization/roleassignments/delete).

1. Obtenha o identificador da atribuição de função (GUID). Esse identificador retorna quando você cria pela primeira vez a atribuição de função, ou você pode obtê-lo listando as atribuições de função.

1. Comece com a solicitação a seguir:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

1. Dentro do URI, substitua *{scope}* pelo escopo para remoção da atribuição de função.

    > [!div class="mx-tableFixed"]
    > | Escopo | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de gerenciamento |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Recurso |

1. Substitua *{roleAssignmentId}* pelo identificador GUID da atribuição de função.

A solicitação a seguir remove a atribuição de função especificada no escopo da assinatura:

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

O texto a seguir mostra um exemplo da saída:

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:24.5379478Z",
        "updatedOn": "2020-05-06T23:55:24.5379478Z",
        "createdBy": "{createdByObjectId1}",
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="arm-template"></a>Modelo de ARM

Não há uma maneira de remover uma atribuição de função usando um modelo de Azure Resource Manager (modelo ARM). Para remover uma atribuição de função, você deve usar outras ferramentas, como o portal do Azure, Azure PowerShell, CLI do Azure ou a API REST.

## <a name="next-steps"></a>Próximas etapas

- [Listar atribuições de função do Azure usando o portal do Azure](role-assignments-list-portal.md)
- [Listar atribuições de função do Azure usando Azure PowerShell](role-assignments-list-powershell.md)
- [Solucionar problemas do RBAC do Azure](troubleshooting.md)
