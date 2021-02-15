---
title: Implantar uma especificação de modelo como um modelo vinculado
description: Saiba como implantar uma especificação de modelo existente em uma implantação vinculada.
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: b30a99a469ec010dcc2e128bbeb446b493631608
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518865"
---
# <a name="tutorial-deploy-a-template-spec-as-a-linked-template-preview"></a>Tutorial: implantar uma especificação de modelo como um modelo vinculado (versão prévia)

Saiba como implantar uma especificação de [modelo](template-specs.md) existente usando uma [implantação vinculada](linked-templates.md#linked-template). Use as especificações do modelo para compartilhar modelos do ARM com outros usuários em sua organização. Depois de criar uma especificação de modelo, você pode implantar a especificação do modelo usando Azure PowerShell ou CLI do Azure. Você também pode implantar a especificação do modelo como parte de sua solução usando um modelo vinculado.

## <a name="prerequisites"></a>Pré-requisitos

Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> As especificações de modelo estão atualmente em versão prévia. Para usá-lo com Azure PowerShell, você deve instalar a [versão 5.0.0 ou posterior](/powershell/azure/install-az-ps). Para usá-lo com CLI do Azure, use a [versão 2.14.2 ou posterior](/cli/azure/install-azure-cli).

## <a name="create-a-template-spec"></a>Criar uma especificação de modelo

Siga o [início rápido: criar e implantar a especificação do modelo](quickstart-create-template-specs.md) para criar uma especificação de modelo para implantar uma conta de armazenamento. Você precisa do nome do grupo de recursos da especificação do modelo, do nome da especificação do modelo e da versão de especificação do modelo na próxima seção.

## <a name="create-the-main-template"></a>Criar o modelo principal

Para implantar uma especificação de modelo em um modelo do ARM, adicione um [recurso de implantações](/azure/templates/microsoft.resources/deployments) ao seu modelo principal. Na `templateLink` propriedade, especifique a ID de recurso de uma especificação de modelo. Crie um modelo com o JSON a seguir chamado **azuredeploy.js**. Este tutorial pressupõe que você salvou em um caminho **c:\Templates\deployTS\azuredeploy.js** , mas pode usar qualquer caminho.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "tsResourceGroup":{
      "type": "string",
      "metadata": {
        "Description": "Specifies the resource group name of the template spec."
      }
    },
    "tsName": {
      "type": "string",
      "metadata": {
        "Description": "Specifies the name of the template spec."
      }
    },
    "tsVersion": {
      "type": "string",
      "defaultValue": "1.0.0.0",
      "metadata": {
        "Description": "Specifies the version the template spec."
      }
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "metadata": {
        "Description": "Specifies the storage account type required by the template spec."
      }
    }
  },
  "variables": {
    "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2016-09-01",
      "name": "[variables('appServicePlanName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "B1",
        "tier": "Basic",
        "size": "B1",
        "family": "B",
        "capacity": 1
      },
      "kind": "linux",
      "properties": {
        "perSiteScaling": false,
        "reserved": true,
        "targetWorkerCount": 0,
        "targetWorkerSizeId": 0
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "createStorage",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
        },
        "parameters": {
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }
  ],
  "outputs": {
    "templateSpecId": {
      "type": "string",
      "value": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
    }
  }
}
```

A ID de especificação do modelo é gerada usando a [`resourceID()`](template-functions-resource.md#resourceid) função. O argumento do grupo de recursos na função ResourceId () será opcional se o templateSpec estiver no mesmo grupo de recursos da implantação atual.  Você também pode transmitir diretamente a ID do recurso como um parâmetro. Para obter a ID, use:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$id = (Get-AzTemplateSpec -ResourceGroupName $resourceGroupName -Name $templateSpecName -Version $templateSpecVersion).Versions.Id
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli-interactive
id = $(az ts show --name $templateSpecName --resource-group $resourceGroupName --version $templateSpecVersion --query "id")
```

> [!NOTE]
> Há um problema conhecido na obtenção de uma ID de especificação de modelo e na atribuição dela a uma variável no Windows PowerShell.

---

A sintaxe para passar parâmetros para a especificação do modelo é:

```json
"parameters": {
  "storageAccountType": {
    "value": "[parameters('storageAccountType')]"
  }
}
```

> [!NOTE]
> O apiVersion de `Microsoft.Resources/deployments` deve ser 2020-06-01 ou posterior.

## <a name="deploy-the-template"></a>Implantar o modelo

Quando você implanta o modelo vinculado, ele implanta o aplicativo Web e a conta de armazenamento. A implantação é a mesma de implantar outros modelos de ARM.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

New-AzResourceGroupDeployment `
  -ResourceGroupName webRG `
  -TemplateFile "c:\Templates\deployTS\azuredeploy.json" `
  -tsResourceGroup templateSpecRg `
  -tsName storageSpec `
  -tsVersion 1.0
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

az deployment group create \
  --resource-group webRG \
  --template-file "c:\Templates\deployTS\azuredeploy.json" \
  --parameters tsResourceGroup=templateSpecRG tsName=storageSpec tsVersion=1.0
```

---

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como criar uma especificação de modelo que inclua modelos vinculados, confira [Criar uma especificação de modelo com um modelo vinculado](template-specs-create-linked.md).
