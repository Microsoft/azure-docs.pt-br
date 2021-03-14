---
title: Atribuir funções do Azure usando o Azure PowerShell-RBAC do Azure
description: Saiba como conceder acesso aos recursos do Azure para usuários, grupos, entidades de serviço ou identidades gerenciadas usando o Azure PowerShell e o Azure RBAC (controle de acesso baseado em função) do Azure.
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 00f663b90f34f3b557329692f844bbbc1bf3207d
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556799"
---
# <a name="assign-azure-roles-using-azure-powershell"></a>Atribuir funções do Azure usando Azure PowerShell

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Este artigo descreve como atribuir funções usando Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para atribuir funções, você deve ter:

- `Microsoft.Authorization/roleAssignments/write` permissões, como [administrador de acesso do usuário](built-in-roles.md#user-access-administrator) ou [proprietário](built-in-roles.md#owner)
- [PowerShell em Azure cloud Shell](../cloud-shell/overview.md) ou [Azure PowerShell](/powershell/azure/install-az-ps)
- A conta usada para executar o comando do PowerShell deve ter a `Directory.Read.All` permissão Microsoft Graph.

## <a name="steps-to-assign-an-azure-role"></a>Etapas para atribuir uma função do Azure

Atribuir uma função consiste em três elementos: entidade de segurança, definição de função e escopo.

### <a name="step-1-determine-who-needs-access"></a>Etapa 1: determinar quem precisa de acesso

Você pode atribuir uma função a um usuário, grupo, entidade de serviço ou identidade gerenciada. Para atribuir uma função, talvez seja necessário especificar a ID exclusiva do objeto. A ID tem o formato: `11111111-1111-1111-1111-111111111111`. Você pode obter a ID usando o portal do Azure ou Azure PowerShell.

**Usuário**

Para um usuário do Azure AD, obtenha o nome principal do usuário, como *patlong \@ contoso.com* ou a ID de objeto de usuário. Para obter a ID de objeto, você pode usar [Get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <userName>
(Get-AzADUser -DisplayName <userName>).id
```

**Grupo**

Para um grupo do Azure AD, você precisa da ID de objeto de grupo. Para obter a ID de objeto, você pode usar [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <groupName>
(Get-AzADGroup -DisplayName <groupName>).id
```

**Entidade de serviço**

Para uma entidade de serviço do Azure AD (identidade usada por um aplicativo), você precisa da ID de objeto da entidade de serviço. Para obter a ID de objeto, você pode usar [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal). Para uma entidade de serviço, use a ID de objeto, e **não** a ID do aplicativo.

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```

**Identidade gerenciada**

Para uma identidade gerenciada atribuída pelo sistema ou pelo usuário, você precisa da ID de objeto. Para obter a ID de objeto, você pode usar [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```
    
### <a name="step-2-select-the-appropriate-role"></a>Etapa 2: selecionar a função apropriada

As permissões são agrupadas em funções. Você pode selecionar em uma lista de várias [funções internas do Azure](built-in-roles.md) ou pode usar suas próprias funções personalizadas. É uma prática recomendada conceder acesso com o privilégio mínimo necessário, portanto, evite atribuir uma função mais ampla.

Para listar funções e obter a ID de função exclusiva, você pode usar [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom, Id
```

Veja como listar os detalhes de uma função específica.

```azurepowershell
Get-AzRoleDefinition <roleName>
```

Para obter mais informações, consulte [listar definições de função do Azure](role-definitions-list.md#azure-powershell).
 
### <a name="step-3-identify-the-needed-scope"></a>Etapa 3: identificar o escopo necessário

O Azure fornece quatro níveis de escopo: recurso, [grupo de recursos](../azure-resource-manager/management/overview.md#resource-groups), assinatura e grupo de [Gerenciamento](../governance/management-groups/overview.md). É uma prática recomendada conceder acesso com o privilégio mínimo necessário, portanto, evite atribuir uma função em um escopo mais amplo. Para obter mais informações sobre escopo, confira [Noções básicas de escopo](scope-overview.md).

**Escopo do recurso**

Para o escopo de recurso, você precisa da ID de recurso para o recurso. Você pode encontrar a ID do recurso examinando as propriedades do recurso no portal do Azure. Uma ID de recurso tem o formato a seguir.

```
/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

**Escopo do grupo de recursos**

Para o escopo do grupo de recursos, você precisa do nome do grupo de recursos. Você pode encontrar o nome na página **grupos de recursos** na portal do Azure ou pode usar [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell
Get-AzResourceGroup
```

**Escopo da assinatura** 

Para o escopo da assinatura, você precisa da ID da assinatura. Você pode encontrar a ID na página **assinaturas** no portal do Azure ou pode usar [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```azurepowershell
Get-AzSubscription
```

**Escopo do grupo de gerenciamento** 

Para o escopo do grupo de gerenciamento, você precisa do nome do grupo de gerenciamento. Você pode encontrar o nome na página **grupos de gerenciamento** no portal do Azure ou pode usar [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
Get-AzManagementGroup
```
    
### <a name="step-4-assign-role"></a>Etapa 4: atribuir função

Para atribuir uma função, use o comando [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) . Dependendo do escopo, o comando normalmente tem um dos formatos a seguir.

**Escopo do recurso**

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionId <roleId> `
-ResourceName <resourceName> `
-ResourceType <resourceType> `
-ResourceGroupName <resourceGroupName>
```

**Escopo do grupo de recursos**

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

**Escopo da assinatura** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

**Escopo do grupo de gerenciamento** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 
    
## <a name="assign-role-examples"></a>Atribuir exemplos de função

#### <a name="assign-a-role-for-all-blob-containers-in-a-storage-account-resource-scope"></a>Atribuir uma função para todos os contêineres de BLOB em um escopo de recurso da conta de armazenamento

Atribui a função de [colaborador de dados de blob de armazenamento](built-in-roles.md#storage-blob-data-contributor) a uma entidade de serviço com a ID de objeto *55555555-5555-5555-5555-555555555555* em um escopo de recurso para uma conta de armazenamento denominada *storage12345*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/providers/Microsoft.Authorization/roleAssignments/cccccccc-cccc-cccc-cccc-cccccccccccc
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-specific-blob-container-resource-scope"></a>Atribuir uma função a um escopo de recurso de contêiner de blob específico

Atribui a função de [colaborador de dados do blob de armazenamento](built-in-roles.md#storage-blob-data-contributor) a uma entidade de serviço com a ID de objeto *55555555-5555-5555-5555-555555555555* em um escopo de recurso para um contêiner de blob chamado *blob-container-01*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignm
                     ents/dddddddd-dddd-dddd-dddd-dddddddddddd
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-group-in-a-specific-virtual-network-resource-scope"></a>Atribuir uma função para um grupo em um escopo de recurso de rede virtual específico

Atribui a função [colaborador da máquina virtual](built-in-roles.md#virtual-machine-contributor) ao grupo *Pharma Sales admins* com a ID aaaaaaaa-AAAA-AAAA-AAAA-aaaaaaaaaaaa em um escopo de recurso para uma rede virtual chamada *Pharma-Sales-Project-Network*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceName pharma-sales-project-network `
-ResourceType Microsoft.Network/virtualNetworks `
-ResourceGroupName MyVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-at-a-resource-group-scope"></a>Atribuir uma função para um usuário em um escopo de grupo de recursos

Atribui a função [colaborador da máquina virtual](built-in-roles.md#virtual-machine-contributor) ao usuário *patlong \@ contoso.com* no escopo do grupo de recursos *Pharma-Sales* .

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

Como alternativa, você pode especificar o grupo de recursos totalmente qualificado com o `-Scope` parâmetro:

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>Atribuir uma função para um usuário usando a ID de função exclusiva em um escopo de grupo de recursos

Há algumas ocasiões em que um nome de função pode ser alterado, por exemplo:

- Você está usando sua própria função personalizada e decide alterar o nome.
- Você está usando uma função de visualização que tem **(visualização)** no nome. Quando a função é liberada, a função é renomeada.

Mesmo que uma função seja renomeada, a ID da função não será alterada. Se você estiver usando scripts ou automação para criar atribuições de função, é uma prática recomendada usar a ID de função exclusiva em vez do nome da função. Portanto, se uma função for renomeada, os scripts provavelmente funcionarão.

O exemplo a seguir atribui a função [colaborador da máquina virtual](built-in-roles.md#virtual-machine-contributor) ao usuário *patlong \@ contoso.com* no escopo do grupo de recursos *Pharma-Sales* .

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 `
-RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="assign-a-role-for-an-application-at-a-resource-group-scope"></a>Atribuir uma função para um aplicativo em um escopo de grupo de recursos

Atribui a função [colaborador de máquina virtual](built-in-roles.md#virtual-machine-contributor) a um aplicativo com ID de objeto de entidade de serviço 77777777-7777-7777-7777-777777777777 no escopo do grupo de recursos *Pharma-Sales* .

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-at-a-subscription-scope"></a>Atribuir uma função para um usuário em um escopo de assinatura

Atribui a função [leitor](built-in-roles.md#reader) ao usuário *annm \@ example.com* em um escopo de assinatura.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName annm@example.com `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Ann M
SignInName         : annm@example.com
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-at-a-management-group-scope"></a>Atribuir uma função para um usuário em um escopo do grupo de gerenciamento

Atribui a função de [leitor de cobrança](built-in-roles.md#billing-reader) ao usuário *Alain \@ example.com* em um escopo de grupo de gerenciamento.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="next-steps"></a>Próximas etapas

- [Listar atribuições de função do Azure usando Azure PowerShell](role-assignments-list-powershell.md)
- [Tutorial: Conceder a um grupo acesso aos recursos do Azure usando o Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Gerenciar recursos com o Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)
