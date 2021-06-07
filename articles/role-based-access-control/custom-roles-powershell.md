---
title: Criar ou atualizar funções personalizadas do Azure usando o Azure PowerShell-RBAC do Azure
description: Saiba como listar, criar, atualizar ou excluir funções personalizadas usando Azure PowerShell e o controle de acesso baseado em função do Azure (RBAC do Azure).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 799475db567c88f067192d027589e9185ee1782b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97369167"
---
# <a name="create-or-update-azure-custom-roles-using-azure-powershell"></a>Criar ou atualizar funções personalizadas do Azure usando o Azure PowerShell

> [!IMPORTANT]
> A adição de um grupo de gerenciamento a `AssignableScopes` está em versão prévia no momento.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se as [funções internas do Azure](built-in-roles.md) não atenderem às necessidades específicas de sua organização, você poderá criar funções personalizadas próprias. Este artigo descreve como listar, criar, atualizar ou excluir funções personalizadas usando Azure PowerShell.

Para obter um tutorial passo a passo sobre como criar uma função personalizada, consulte [tutorial: criar uma função personalizada do Azure usando Azure PowerShell](tutorial-custom-role-powershell.md).

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para criar funções personalizadas, você precisará:

- Permissões para criar funções personalizadas, como [Proprietário](built-in-roles.md#owner) ou [Administrador de acesso do usuário](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) ou [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-custom-roles"></a>Listar funções personalizadas

Para listar as funções disponíveis para atribuição em um escopo, use o comando [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition). O exemplo a seguir lista todas as funções disponíveis para atribuição na assinatura selecionada.

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom
```

```Example
Name                                              IsCustom
----                                              --------
Virtual Machine Operator                              True
AcrImageSigner                                       False
AcrQuarantineReader                                  False
AcrQuarantineWriter                                  False
API Management Service Contributor                   False
...
```

O exemplo a seguir lista apenas as funções personalizadas disponíveis para atribuição na assinatura selecionada.

```azurepowershell
Get-AzRoleDefinition -Custom | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

Se a assinatura selecionada não estiver no `AssignableScopes` da função, a função personalizada não será listada.

## <a name="list-a-custom-role-definition"></a>Listar uma definição de função personalizada

Para listar uma definição de função personalizada, use [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition). Esse é o mesmo comando que você usa para uma função interna.

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | ConvertTo-Json

{
  "Name": "Virtual Machine Operator",
  "Id": "00000000-0000-0000-0000-000000000000",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

O exemplo a seguir lista apenas as ações da função:

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Operator").Actions

"Microsoft.Storage/*/read",
"Microsoft.Network/*/read",
"Microsoft.Compute/*/read",
"Microsoft.Compute/virtualMachines/start/action",
"Microsoft.Compute/virtualMachines/restart/action",
"Microsoft.Authorization/*/read",
"Microsoft.ResourceHealth/availabilityStatuses/read",
"Microsoft.Resources/subscriptions/resourceGroups/read",
"Microsoft.Insights/alertRules/*",
"Microsoft.Insights/diagnosticSettings/*",
"Microsoft.Support/*"
```

## <a name="create-a-custom-role"></a>Criar uma função personalizada

Para criar uma função personalizada, use o comando [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition). Há dois métodos de estruturar a função, usando o objeto `PSRoleDefinition` ou um modelo JSON. 

### <a name="get-operations-for-a-resource-provider"></a>Obter operações para um provedor de recursos

Quando você cria funções personalizadas, é importante conhecer todas as operações possíveis dos provedores de recursos.
É possível exibir a lista de [operações do provedor de recursos](resource-provider-operations.md) ou usar o comando [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) para obter essas informações.
Por exemplo, se desejar verificar todas as operações disponíveis para máquinas virtuais, use este comando:

```azurepowershell
Get-AzProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-custom-role-with-the-psroledefinition-object"></a>Criar uma função personalizada com o objeto PSRoleDefinition

Ao usar o PowerShell para criar uma função personalizada, é possível usar uma das [funções internas](built-in-roles.md) como ponto de partida ou começar do zero. O primeiro exemplo nesta seção começa com uma função interna e, em seguida, a personaliza com mais permissões. Edite os atributos para adicionar o `Actions`, o `NotActions` ou o `AssignableScopes` desejado e salve as alterações como uma nova função.

O exemplo a seguir inicia com a função interna [Colaborador de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) para criar uma função personalizada denominada *Operador de Máquina Virtual*. A nova função concede acesso a todas as operações de leitura dos provedores de recursos *Microsoft.Compute*, *Microsoft.Storage* e *Microsoft.Network* e concede acesso para iniciar, reiniciar e monitorar as máquinas virtuais. A função personalizada pode ser usada em duas assinaturas.

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.ResourceHealth/availabilityStatuses/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzRoleDefinition -Role $role
```

O exemplo a seguir mostra outra maneira de criar a função *Operador de Máquina Virtual* personalizada. Começa pela criação de um novo `PSRoleDefinition` objeto. As operações de ação são especificadas na variável `perms` e definidas como a propriedade `Actions`. A propriedade `NotActions` é definida lendo o `NotActions` da função interna [Colaborador de Máquina Virtual](built-in-roles.md#virtual-machine-contributor). Como o [Colaborador de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) não tem nenhum `NotActions`, esta linha não é obrigatória, mas mostra como as informações podem ser recuperadas de outra função.

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read'
$perms += 'Microsoft.ResourceHealth/availabilityStatuses/read'
$perms += 'Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzRoleDefinition -Role $role
```

### <a name="create-a-custom-role-with-json-template"></a>Criar função personalizada com o modelo JSON

Um modelo JSON pode ser usado como a definição da fonte para a função personalizada. O exemplo a seguir cria uma função personalizada que permite acesso de leitura ao armazenamento e aos recursos de computação, acesso ao suporte e adiciona essa função a duas assinaturas. Crie um novo arquivo `C:\CustomRoles\customrole1.json` com o exemplo a seguir. A ID deve ser definida como `null` na criação de função inicial, pois uma nova ID é gerada automaticamente. 

```json
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Para adicionar a função às assinaturas, execute o seguinte comando do PowerShell:

```azurepowershell
New-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>Atualizar uma função personalizada

Assim como na criação de uma função personalizada, é possível modificar uma função personalizada existente usando o objeto `PSRoleDefinition` ou um modelo JSON.

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>Atualizar uma função personalizada com o objeto PSRoleDefinition

Para modificar uma função personalizada, primeiro use o comando [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) para recuperar a definição da função. Depois, faça as alterações desejadas na definição da função. Por fim, use o comando [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) para salvar a definição da função modificada.

O exemplo a seguir adiciona a operação `Microsoft.Insights/diagnosticSettings/*` à função personalizada *Operador de Máquina Virtual* .

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}
```

O exemplo a seguir adiciona uma assinatura do Azure para os escopos atribuíveis da função personalizada de *Operador da Máquina Virtual* .

```azurepowershell
Get-AzSubscription -SubscriptionName Production3

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222}
```

O exemplo a seguir adiciona um grupo de gerenciamento para `AssignableScopes` a função personalizada *operador de máquina virtual* . A adição de um grupo de gerenciamento a `AssignableScopes` está em versão prévia no momento.

```azurepowershell
Get-AzManagementGroup

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/{groupId1}")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzManagementGroup

Id          : /providers/Microsoft.Management/managementGroups/marketing-group
Type        : /providers/Microsoft.Management/managementGroups
Name        : marketing-group
TenantId    : 99999999-9999-9999-9999-999999999999
DisplayName : Marketing group

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/marketing-group")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222,
                   /providers/Microsoft.Management/managementGroups/marketing-group}
```

### <a name="update-a-custom-role-with-a-json-template"></a>Atualizar função personalizada com o modelo JSON

Usando o modelo JSON anterior, você pode facilmente modificar uma função personalizada existente para adicionar ou remover ações. Atualize o modelo JSON e adicione a ação de leitura para rede, conforme mostrado no exemplo a seguir. As definições listadas no modelo não são aplicadas de forma cumulativa a uma definição existente, o que significa que a função aparece exatamente como especificada no modelo. Você também precisa atualizar o campo de ID com a ID da função. Se não tiver certeza de qual é esse valor, é possível usar o cmdlet [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) para obter essa informação.

```json
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Para atualizar a função existente, execute o seguinte comando do PowerShell:

```azurepowershell
Set-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Excluir uma função personalizada

Para excluir uma função personalizada, use o comando [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition).

O exemplo a seguir remove a função personalizada *Operador de Máquina Virtual* .

```azurepowershell
Get-AzRoleDefinition "Virtual Machine Operator"
Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Criar uma função personalizada do Azure usando o Azure PowerShell](tutorial-custom-role-powershell.md)
- [Funções personalizadas do Azure](custom-roles.md)
- [Operações do provedor de recursos do Azure](resource-provider-operations.md)
