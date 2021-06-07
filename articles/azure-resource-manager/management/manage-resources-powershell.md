---
title: Gerenciar recursos-Azure PowerShell
description: Use Azure PowerShell e Azure Resource Manager para gerenciar seus recursos. Mostra como implantar e excluir recursos.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: ce3b1d9f8d01e722e4ec5078d92ef4148072da25
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96185735"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>Gerenciar recursos do Azure usando o Azure PowerShell

Saiba como usar Azure PowerShell com [Azure Resource Manager](overview.md) para gerenciar os recursos do Azure. Para gerenciar grupos de recursos, consulte [gerenciar grupos de recursos do Azure usando Azure PowerShell](manage-resource-groups-powershell.md).

Outros artigos sobre o gerenciamento de recursos:

- [Gerenciar recursos do Azure usando o portal do Azure](manage-resources-portal.md)
- [Gerenciar recursos do Azure usando o CLI do Azure](manage-resources-cli.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Implantar recursos em um grupo de recursos existente

Você pode implantar recursos do Azure diretamente usando Azure PowerShell ou implantar um modelo do Resource Manager para criar recursos do Azure.

### <a name="deploy-a-resource"></a>Implantar um recurso

O script a seguir cria uma conta de armazenamento.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName "Standard_LRS"

# Retrieve the context.
$ctx = $storageAccount.Context
```

### <a name="deploy-a-template"></a>Implantar um modelo

O script a seguir cria uma implantação de um modelo de início rápido para criar uma conta de armazenamento. Para obter mais informações, consulte [início rápido: criar modelos de Azure Resource Manager usando Visual Studio Code](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

Para obter mais informações, veja [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../templates/deploy-powershell.md).

## <a name="deploy-a-resource-group-and-resources"></a>Implantar um grupo de recursos e recursos

Você pode criar um grupo de recursos e implantar recursos no grupo. Para saber mais, confira [Create resource group and deploy resources](../templates/deploy-to-subscription.md#resource-groups) (Criar grupo de recursos e implantar recursos).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Implantar recursos em várias assinaturas ou grupos de recursos

Normalmente, você deve implantar todos os recursos em seu modelo em um único grupo de recursos. No entanto, há cenários em que você deseja implantar um conjunto de recursos de uma vez, mas colocá-los em diferentes grupos de recursos ou assinaturas. Para obter mais informações, consulte [implantar recursos do Azure em várias assinaturas ou grupos de recursos](../templates/deploy-to-resource-group.md).

## <a name="delete-resources"></a>Excluir recursos

O script a seguir mostra como excluir uma conta de armazenamento.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

Para obter mais informações sobre como Azure Resource Manager ordena a exclusão de recursos, consulte [Azure Resource Manager exclusão de grupo de recursos](delete-resource-group.md).

## <a name="move-resources"></a>Mover recursos

O script a seguir mostra como remover uma conta de armazenamento de um grupo de recursos para outro grupo de recursos.

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Bloquear recursos

O bloqueio impede que outros usuários em sua organização excluam ou modifiquem acidentalmente recursos críticos, como assinatura do Azure, grupo de recursos ou recurso. 

O script a seguir bloqueia uma conta de armazenamento para que a conta não possa ser excluída.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

O script a seguir obtém todos os bloqueios para uma conta de armazenamento:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

O script a seguir exclui um bloqueio de uma conta de armazenamento:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

Para obter mais informações, consulte [Bloquear recursos com Azure Resource Manager](lock-resources.md).

## <a name="tag-resources"></a>Recursos de marca

A marcação ajuda a organizar o grupo de recursos e os recursos logicamente. Para obter informações, consulte [usando marcas para organizar os recursos do Azure](tag-resources.md#powershell).

## <a name="manage-access-to-resources"></a>Gerenciar o acesso aos recursos

O Azure [RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md) do Azure é a maneira como você gerencia o acesso a recursos no Azure. Para obter mais informações, consulte [Adicionar ou remover atribuições de função do Azure usando Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Próximas etapas

- Para saber Azure Resource Manager, consulte [Azure Resource Manager visão geral](overview.md).
- Para saber mais sobre a sintaxe do modelo do Resource Manager, consulte [entender a estrutura e a sintaxe dos modelos de Azure Resource Manager](../templates/template-syntax.md).
- Para saber como desenvolver modelos, consulte os tutoriais passo a [passo](../index.yml).
- Para exibir os esquemas de modelo de Azure Resource Manager, consulte [referência de modelo](/azure/templates/).