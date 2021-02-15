---
title: Cota de implantação excedida
description: Descreve como resolver o erro de ter mais de 800 implantações no histórico do grupo de recursos.
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.openlocfilehash: 8996d7817eea2f8daf44fbc9b4416c884b05940f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87987045"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Resolver erro quando a contagem de implantação exceder 800

Cada grupo de recursos é limitado a 800 implantações em seu histórico de implantação. Este artigo descreve o erro que você recebe quando uma implantação falha porque ela excederia as implantações permitidas do 800. Para resolver esse erro, exclua as implantações do histórico do grupo de recursos. A exclusão de uma implantação do histórico não afeta nenhum dos recursos que foram implantados.

Azure Resource Manager exclui automaticamente as implantações do seu histórico conforme você próximo ao limite. Você ainda poderá ver esse erro por um dos seguintes motivos:

1. Você tem um bloqueio de CanNotDelete no grupo de recursos que impede as exclusões do histórico de implantação.
1. Você optou por excluir exclusões automáticas.
1. Você tem um grande número de implantações em execução simultaneamente e as exclusões automáticas não são processadas com rapidez suficiente para reduzir o número total.

Para obter informações sobre como remover o bloqueio ou optar por exclusões automáticas, consulte [exclusões automáticas do histórico de implantação](deployment-history-deletions.md).

Este artigo descreve como excluir manualmente implantações do histórico.

## <a name="symptom"></a>Sintoma

Durante a implantação, você receberá um erro informando que a implantação atual excederá a cota de implantações de 800.

## <a name="solution"></a>Solução

### <a name="azure-cli"></a>CLI do Azure

Use o comando [AZ Deployment Group Delete](/cli/azure/group/deployment) para excluir implantações do histórico.

```azurecli-interactive
az deployment group delete --resource-group exampleGroup --name deploymentName
```

Para excluir todas as implantações com mais de cinco dias, use:

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az deployment group list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az deployment group delete --resource-group exampleGroup --name $deployment
done
```

Você pode obter a contagem atual no histórico de implantação com o seguinte comando:

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

Use o comando [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) para excluir implantações do histórico.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

Para excluir todas as implantações com mais de cinco dias, use:

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

Você pode obter a contagem atual no histórico de implantação com o seguinte comando:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>Soluções de terceiros

As soluções externas a seguir abordam cenários específicos:

* [Aplicativos lógicos do Azure e soluções do PowerShell](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [Extensão AzDevOps](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
