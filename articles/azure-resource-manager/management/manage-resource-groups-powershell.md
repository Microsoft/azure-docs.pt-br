---
title: Gerenciar grupos de recursos-Azure PowerShell
description: Use Azure PowerShell para gerenciar seus grupos de recursos por meio de Azure Resource Manager. Mostra como criar, listar e excluir grupos de recursos.
author: mumian
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: jgao
ms.openlocfilehash: fb8cd45842e2f9b64048aa939f7b6adc9b0e6e0a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98703675"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Gerenciar Azure Resource Manager grupos de recursos usando Azure PowerShell

Saiba como usar Azure PowerShell com [Azure Resource Manager](overview.md) para gerenciar seus grupos de recursos do Azure. Para gerenciar recursos do Azure, consulte [gerenciar recursos do Azure usando Azure PowerShell](manage-resources-powershell.md).

Outros artigos sobre como gerenciar grupos de recursos:

- [Gerenciar grupos de recursos do Azure usando o portal do Azure](manage-resources-portal.md)
- [Gerenciar grupos de recursos do Azure usando o CLI do Azure](manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>O que é um grupo de recursos

Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure. O grupo de recursos pode incluir todos os recursos para a solução ou apenas os recursos que você deseja gerenciar como um grupo. Você decide como deseja alocar recursos para grupos de recursos com base no que faz mais sentido para sua organização. Em geral, adicione recursos que compartilham o mesmo ciclo de vida no mesmo grupo de recursos, para que você possa implantar, atualizar e excluí-los como um grupo facilmente.

O grupo de recursos armazena metadados sobre os recursos. Portanto, quando você especifica uma localização para o grupo de recursos, você está especificando o local em que os metadados são armazenados. Por motivos de conformidade, talvez você precise garantir que os dados sejam armazenados em determinada região.

O grupo de recursos armazena metadados sobre os recursos. Quando você especifica uma localização para o grupo de recursos, você especifica onde os metadados são armazenados.

## <a name="create-resource-groups"></a>Criar grupos de recursos

O script do PowerShell a seguir cria um grupo de recursos.

```azurepowershell-interactive
New-AzResourceGroup -Name demoResourceGroup -Location westus
```

## <a name="list-resource-groups"></a>Listar os grupos de recursos

O script do PowerShell a seguir lista os grupos de recursos em sua assinatura.

```azurepowershell-interactive
Get-AzResourceGroup
```

Para obter um grupo de recursos:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Excluir grupos de recursos

O seguinte script do PowerShell exclui um grupo de recursos:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Para obter mais informações sobre como Azure Resource Manager ordena a exclusão de recursos, consulte [Azure Resource Manager exclusão de grupo de recursos](delete-resource-group.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Implantar recursos em um grupo de recursos existente

Consulte [implantar recursos em um grupo de recursos existente](manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

Para validar uma implantação de grupo de recursos, consulte [Test-AzResourceGroupDeployment](/powershell/module/Az.Resources/Test-AzResourceGroupDeployment).

## <a name="deploy-a-resource-group-and-resources"></a>Implantar um grupo de recursos e recursos

Você pode criar um grupo de recursos e implantar recursos no grupo usando um modelo do Resource Manager. Para saber mais, confira [Create resource group and deploy resources](../templates/deploy-to-subscription.md#resource-groups) (Criar grupo de recursos e implantar recursos).

## <a name="redeploy-when-deployment-fails"></a>Reimplantar quando ocorrer falha na implantação

Esse recurso também é conhecido como *reversão em caso de erro*. Para obter mais informações, consulte [reimplantar quando a implantação falhar](../templates/rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Mover para outro grupo de recursos ou assinatura

Você pode mover os recursos do grupo para outro grupo de recursos. Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Bloquear grupos de recursos

O bloqueio impede que outros usuários em sua organização excluam ou modifiquem acidentalmente recursos críticos, como assinatura do Azure, grupo de recursos ou recurso. 

O script a seguir bloqueia um grupo de recursos para que o grupo de recursos não possa ser excluído.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

O script a seguir obtém todos os bloqueios para um grupo de recursos:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

Para obter mais informações, consulte [Bloquear recursos com Azure Resource Manager](lock-resources.md).

## <a name="tag-resource-groups"></a>Marcar grupos de recursos

Você pode aplicar marcas a recursos e grupos de recursos para organizar seus ativos de modo lógico. Para obter informações, consulte [usando marcas para organizar os recursos do Azure](tag-resources.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Exportar grupos de recursos para modelos

Depois de configurar seu grupo de recursos, você pode exibir um modelo do Resource Manager para o grupo de recursos. A exportação do modelo oferece dois benefícios:

- Automatize implantações futuras da solução porque o modelo contém a infraestrutura completa.
- Aprenda a sintaxe do modelo examinando o JavaScript Object Notation (JSON) que representa sua solução.

Para exportar todos os recursos em um grupo de recursos, use o cmdlet [Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) e forneça o nome do grupo de recursos.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Ele salva o modelo como um arquivo local.

Em vez de exportar todos os recursos no grupo de recursos, você pode selecionar quais recursos exportar.

Para exportar um recurso, passe essa ID de recurso.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Para exportar mais de um recurso, passe as IDs de recurso em uma matriz.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

Ao exportar o modelo, você pode especificar se os parâmetros são usados no modelo. Por padrão, os parâmetros para nomes de recursos são incluídos, mas não têm um valor padrão. Você deve passar esse valor de parâmetro durante a implantação.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": null,
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": null,
    "type": "String"
  }
}
```

No recurso, o parâmetro é usado para o nome.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

Se você usar o `-IncludeParameterDefaultValue` parâmetro ao exportar o modelo, o parâmetro de modelo incluirá um valor padrão definido para o valor atual. Você pode usar esse valor padrão ou substituir o valor padrão passando um valor diferente.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

Se você usar o `-SkipResourceNameParameterization` parâmetro ao exportar o modelo, os parâmetros para nomes de recursos não serão incluídos no modelo. Em vez disso, o nome do recurso é definido diretamente no recurso para seu valor atual. Não é possível personalizar o nome durante a implantação.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

O recurso exportar modelo não dá suporte à exportação de Azure Data Factory recursos. Para saber mais sobre como você pode exportar Data Factory recursos, consulte [copiar ou clonar um data Factory em Azure data Factory](../../data-factory/copy-clone-data-factory.md).

Para exportar recursos criados por meio do modelo de implantação clássico, você deve [migrá-los para o modelo de implantação do Gerenciador de recursos](../../virtual-machines/migration-classic-resource-manager-overview.md).

Para obter mais informações, consulte [exportação única e de vários recursos para o modelo no portal do Azure](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Gerenciar o acesso a grupos de recursos

O Azure [RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md) do Azure é a maneira como você gerencia o acesso a recursos no Azure. Para obter mais informações, consulte [Adicionar ou remover atribuições de função do Azure usando Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Próximas etapas

- Para saber Azure Resource Manager, consulte [Azure Resource Manager visão geral](overview.md).
- Para saber mais sobre a sintaxe do modelo do Resource Manager, consulte [entender a estrutura e a sintaxe dos modelos de Azure Resource Manager](../templates/template-syntax.md).
- Para saber como desenvolver modelos, consulte os tutoriais passo a [passo](../index.yml).
- Para exibir os esquemas de modelo de Azure Resource Manager, consulte [referência de modelo](/azure/templates/).