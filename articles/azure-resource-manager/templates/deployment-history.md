---
title: Histórico de implantações
description: Descreve como exibir as operações de implantação do Azure Resource Manager com o portal, o PowerShell, a CLI do Azure e a API REST.
tags: top-support-issue
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 00e0c51244d5c191d4c9f05f689b90ece81ec5a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91284611"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Exibir histórico de implantação com o Azure Resource Manager

Azure Resource Manager permite que você exiba seu histórico de implantação. Você pode examinar operações específicas nas implantações anteriores e ver quais recursos foram implantados. Este histórico contém informações sobre quaisquer erros.

O histórico de implantação de um grupo de recursos é limitado a implantações de 800. À medida que você se aproximar do limite, as implantações serão excluídas automaticamente do histórico. Para obter mais informações, confira [Exclusões automáticas do histórico de implantações](deployment-history-deletions.md).

Para obter ajuda com a resolução de erros de implantação específicos, veja [Resolver erros comuns ao implantar recursos no Azure com o Azure Resource Manager](common-deployment-errors.md).

## <a name="get-deployments-and-correlation-id"></a>Obter as implantações e a ID de correlação

Você pode ver detalhes sobre uma implantação por meio do portal do Azure, do PowerShell, da CLI do Azure ou da API REST. Cada implantação tem uma ID de correlação, que é usada para rastrear eventos relacionados. Se você [criar uma solicitação de suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md), o suporte poderá solicitar a ID de correlação. O suporte usa a ID de correlação para identificar as operações para a implantação com falha.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecione o grupo de recursos que você quer examinar.

1. Selecione o link em **Implantações**.

   ![Selecionar o histórico de implantação](./media/deployment-history/select-deployment-history.png)

1. Selecione uma das implantações do histórico de implantações.

   ![Selecionar a implantação](./media/deployment-history/select-details.png)

1. Um resumo da implantação é exibido, incluindo a ID de correlação.

    ![Resumo da implantação](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para listar todas as implantações de um grupo de recursos, use o comando [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment).

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Para obter uma implantação específica de um grupo de recursos, adicione o parâmetro **DeploymentName**.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

Para obter a ID de correlação, use:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para listar a implantação de um grupo de recursos, use [az deployment group list](/cli/azure/group/deployment#az-deployment-group-list).

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

Para obter uma implantação específica, use [az deployment group show](/cli/azure/group/deployment#az-deployment-group-show).

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

Para obter a ID de correlação, use:

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

Para listar as implantações para um grupo de recursos, use a operação a seguir. Para obter o número de versão da API mais recente a ser usado na solicitação, confira [Implantações – listar por grupo de recursos](/rest/api/resources/deployments/listbyresourcegroup).

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

Para obter uma implantação específica, use a operação a seguir. Para obter o número de versão da API mais recente a ser usado na solicitação, confira [Implantações – obter](/rest/api/resources/deployments/get).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

A resposta inclui a ID de correlação.

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "47ff4228-bf2e-4ee5-a008-0b07da681230",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>Obter mensagens de erro e operações de implantação

Cada implantação pode incluir várias operações. Para ver mais detalhes sobre uma implantação, veja as operações de implantação. Quando uma implantação falha, as operações de implantação incluem uma mensagem de erro.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No resumo para uma implantação, selecione **Detalhes da operação**.

    ![Selecionar detalhes da operação](./media/deployment-history/get-operation-details.png)

1. Você verá os detalhes dessa etapa da implantação. Quando ocorre um erro, os detalhes incluem a mensagem de erro.

    ![Mostrar detalhes da operação](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para exibir as operações de implantação para implantação em um grupo de recursos, use o comando [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azdeploymentoperation).

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

Para exibir as operações com falha, filtre as operações com o estado **Falhou**.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

Para obter a mensagem de status das operações com falha, use o seguinte comando:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para exibir as operações de implantação para implantação em um grupo de recursos, use o comando [az deployment operation group list](/cli/azure/deployment/operation/group#az-deployment-operation-group-list). Você deve ter a CLI do Azure 2.6.0 ou posterior.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeployment
```

Para exibir as operações com falha, filtre as operações com o estado **Falhou**.

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

Para obter a mensagem de status das operações com falha, use o seguinte comando:

```azurecli-interactive
az deployment operation group list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

Para obter operações de implantação, use a seguinte operação. Para obter o número de versão da API mais recente a ser usado na solicitação, confira [Operações de implantação – listar](/rest/api/resources/deploymentoperations/list).

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

A resposta inclui uma mensagem de erro.

```json
{
  "value": [
    {
      "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeploy/operations/13EFD9907103D640",
      "operationId": "13EFD9907103D640",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "9d3cdac4-54f8-486c-94bd-10c20867b8bc",
        "serviceRequestId": "01a9d0fe-896b-4c94-a30f-60b70a8f1ad9",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storageq2czadzfgizc2",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storageq2czadzfgizc2"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>Próximas etapas

* Para obter ajuda com a resolução de erros de implantação específicos, veja [Resolver erros comuns ao implantar recursos no Azure com o Azure Resource Manager](common-deployment-errors.md).
* Para saber mais sobre como as implantações são gerenciadas no histórico, consulte [exclusões automáticas do histórico de implantação](deployment-history-deletions.md).
* Para validar sua implantação antes de executá-la, consulte [Implantar um grupo de recursos com um modelo do Azure Resource Manager](deploy-powershell.md).

