---
title: "Gerenciar o RBAC (Controle de Acesso Baseado em Função) com o Azure PowerShell | Microsoft Docs"
description: "Como gerenciar o RBAC com o Azure PowerShell, incluindo listagem e atribuição e funções, e exclusão de atribuições de funções."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.openlocfilehash: d7b11df21650b5cb27f9c3dd8306f8d12664185e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-role-based-access-control-with-azure-powershell"></a>Gerenciar o Controle de Acesso baseado em função com o Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [CLI do Azure](role-based-access-control-manage-access-azure-cli.md)
> * [API REST](role-based-access-control-manage-access-rest.md)

Você pode usar o RBAC (Controle de Acesso Baseado em Função) no portal do Azure e na API de Gerenciamento de Recursos do Azure para gerenciar o acesso à sua assinatura em um nível refinado. Com esse recurso, você pode conceder acesso aos usuários, grupos ou entidades de serviço do Active Directory atribuindo algumas funções para eles em um determinado escopo.

Antes de poder usar o PowerShell para gerenciar o RBAC, você precisa dos seguintes pré-requisitos:

* PowerShell do Azure, versão 0.8.8 ou posterior. Para instalar a última versão e associá-la à sua assinatura do Azure, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).
* Cmdlets do Azure Resource Manager. Instale os [cmdlets do Azure Resource Manager](/powershell/azure/overview) no PowerShell.

## <a name="list-roles"></a>Listar funções
### <a name="list-all-available-roles"></a>Relacionar todas as funções disponíveis
Para listar as funções RBAC disponíveis para a atribuição e inspecionar as operações para as quais elas concedem acesso, use `Get-AzureRmRoleDefinition`.

```
Get-AzureRmRoleDefinition | FT Name, Description
```

![RBAC PowerShell - Get-AzureRmRoleDefinition - captura de tela](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### <a name="list-actions-of-a-role"></a>Relacionar ações de uma função
Para listar as ações para uma função específica, use `Get-AzureRmRoleDefinition <role name>`.

```
Get-AzureRmRoleDefinition Contributor | FL Actions, NotActions

(Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions
```

![RBAC PowerShell - Get-AzureRmRoleDefinition para uma função específica - captura de tela](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## <a name="see-who-has-access"></a>Veja quem tem acesso
Para listar as atribuições de acesso do RBAC, use `Get-AzureRmRoleAssignment`.

### <a name="list-role-assignments-at-a-specific-scope"></a>Listar as atribuições de função em um escopo específico
Você pode ver todas as atribuições de acesso para uma assinatura, grupo de recursos ou recurso especificados. Por exemplo, para ver todas as atribuições ativas para um grupo de recursos, use `Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>`.

```
Get-AzureRmRoleAssignment -ResourceGroupName Pharma-Sales-ProjectForcast | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell - Get-AzureRmRoleAssignment para um grupo de recursos - captura de tela](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### <a name="list-roles-assigned-to-a-user"></a>Listar funções atribuídas a um usuário
Para listar todas as funções que são atribuídas a um usuário específico e as funções que são atribuídas aos grupos aos quais o usuário pertence, use `Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups`.

```
Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com | FL DisplayName, RoleDefinitionName, Scope

Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell - Get-AzureRmRoleAssignment para um usuário - captura de tela](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### <a name="list-classic-service-administrator-and-coadmin-role-assignments"></a>Listar atribuições de função de administrador e coadministrador de serviços clássicos
Para listar as atribuições para administrador e coadministradores da assinatura clássica, use:

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## <a name="grant-access"></a>Conceder acesso
### <a name="search-for-object-ids"></a>Pesquisar IDs de objeto
Para atribuir uma função, você precisa identificar o objeto (usuário, grupo ou aplicativo) e o escopo.

Se você não souber a ID da assinatura, poderá encontrá-la na folha **Assinaturas** no portal do Azure. Para saber como consultar a ID da assinatura, consulte [Get-AzureSubscription](/powershell/module/azure/get-azuresubscription?view=azuresmps-3.7.0) no MSDN.

Para obter a ID de objeto para um grupo do Azure AD, use:

    Get-AzureRmADGroup -SearchString <group name in quotes>

Para obter a ID de objeto de uma Entidade de Serviço do Azure AD ou um aplicativo, use:

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Atribuir uma função a um aplicativo no escopo da assinatura
Para conceder acesso a um aplicativo no escopo da assinatura, use:

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>

![RBAC PowerShell - New-AzureRmRoleAssignment - captura de tela](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>Atribuir uma função ao usuário no escopo do grupo de recursos
Para conceder acesso a um usuário no escopo do grupo de recursos, use:

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - captura de tela](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>Atribuir uma função a um grupo no escopo de recursos
Para conceder acesso a um grupo no escopo de recursos, use:

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - captura de tela](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## <a name="remove-access"></a>Remover acesso
Para remover o acesso a usuários, grupos e aplicativos, use:

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

![RBAC PowerShell - Remove-AzureRmRoleAssignment - captura de tela](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## <a name="create-a-custom-role"></a>Criar uma função personalizada
Para criar uma função personalizada, use o comando ```New-AzureRmRoleDefinition``` . Há dois métodos de estruturar a função, usando PSRoleDefinitionObject ou um modelo JSON. 

## <a name="get-actions-for-a-resource-provider"></a>Obter ações para um provedor de recursos
Quando estiver criando funções personalizadas do zero, é importante conhecer todas as possíveis operações dos provedores de recursos.
Use o comando ```Get-AzureRMProviderOperation``` para obter essas informações.
Por exemplo, se desejar verificar todas as operações disponíveis para a máquina virtual, use este comando:

```
Get-AzureRMProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation , Description -AutoSize
```

### <a name="create-role-with-psroledefinitionobject"></a>Criar função com PSRoleDefinitionObject
Ao usar o PowerShell para criar uma função personalizada, é possível começar do zero ou usar uma das [funções internas](role-based-access-built-in-roles.md) como ponto de partida. O exemplo nesta seção começa com uma função interna e, em seguida, a personaliza com mais privilégios. Edite os atributos para adicionar *Actions*, *notActions* ou *scopes* desejados e salve as alterações como uma nova função.

O exemplo a seguir inicia com a função *Colaborador da Máquina Virtual* e utiliza-a para criar uma função personalizada denominada *Operador da Máquina Virtual*. A nova função concede acesso a todas as operações de leitura dos provedores de recursos *Microsoft.Compute*, *Microsoft.Storage* e *Microsoft.Network* e concede acesso para iniciar, reiniciar e monitorar as máquinas virtuais. A função personalizada pode ser usada em duas assinaturas.

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Contributor"
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
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e")
$role.AssignableScopes.Add("/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624")
New-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell - Get-AzureRmRoleDefinition - captura de tela](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

### <a name="create-role-with-json-template"></a>Criar função com modelo JSON
Um modelo JSON pode ser usado como a definição da fonte para a função personalizada. O exemplo a seguir cria uma função personalizada que permite acesso de leitura ao armazenamento e aos recursos de computação, acesso ao suporte e adiciona essa função a duas assinaturas. Crie um novo arquivo `C:\CustomRoles\customrole1.json` com o exemplo a seguir. A ID deve ser definida como `null` na criação de função inicial, pois uma nova ID é gerada automaticamente. 

```
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
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```
Para adicionar a função às assinaturas, execute o seguinte comando do PowerShell:
```
New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="modify-a-custom-role"></a>Modificar uma função personalizada
Assim como na criação de uma função personalizada, você pode modificar uma função personalizada existente usando PSRoleDefinitionObject ou um modelo JSON.

### <a name="modify-role-with-psroledefinitionobject"></a>Modificar função com PSRoleDefinitionObject
Para modificar uma função personalizada, primeiro use o comando `Get-AzureRmRoleDefinition` para recuperar a definição da função. Depois, faça as alterações desejadas na definição da função. Por fim, use o comando `Set-AzureRmRoleDefinition` para salvar a definição da função modificada.

O exemplo a seguir adiciona a operação `Microsoft.Insights/diagnosticSettings/*` à função personalizada *Operador de Máquina Virtual* .

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell - Set-AzureRmRoleDefinition - captura de tela](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

O exemplo a seguir adiciona uma assinatura do Azure para os escopos atribuíveis da função personalizada de *Operador da Máquina Virtual* .

```
Get-AzureRmSubscription - SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/34370e90-ac4a-4bf9-821f-85eeedead1a2")
Set-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell - Set-AzureRmRoleDefinition - captura de tela](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

### <a name="modify-role-with-json-template"></a>Modificar função com modelo JSON
Usando o modelo JSON anterior, você pode facilmente modificar uma função personalizada existente para adicionar ou remover ações. Atualize o modelo JSON e adicione a ação de leitura para rede, conforme mostrado no exemplo a seguir. As definições listadas no modelo não são aplicadas de forma cumulativa a uma definição existente, o que significa que a função aparece exatamente como especificada no modelo. Você também precisa atualizar o campo de ID com a ID da função. Se não tiver certeza de qual é esse valor, você poderá usar o cmdlet `Get-AzureRmRoleDefinition` para obter essa informação.

```
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
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

Para atualizar a função existente, execute o seguinte comando do PowerShell:
```
Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Excluir uma função personalizada
Para excluir uma função personalizada, use o comando `Remove-AzureRmRoleDefinition` .

O exemplo a seguir remove a função personalizada *Operador de Máquina Virtual* .

```
Get-AzureRmRoleDefinition "Virtual Machine Operator"

Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

![RBAC PowerShell - Remove-AzureRmRoleDefinition - captura de tela](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## <a name="list-custom-roles"></a>Listar funções personalizadas
Para listar as funções disponíveis para atribuição em um escopo, use o comando `Get-AzureRmRoleDefinition` .

O exemplo a seguir lista todas as funções disponíveis para atribuição na assinatura selecionada.

```
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

![RBAC PowerShell - Get-AzureRmRoleDefinition - captura de tela](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

No exemplo a seguir, a função personalizada *Operador de Máquina Virtual* não está disponível na assinatura *Production4*, pois essa assinatura não está no **AssignableScopes** da função.

![RBAC PowerShell - Get-AzureRmRoleDefinition - captura de tela](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## <a name="see-also"></a>Consulte também
* [Usando o Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md)
  [!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

