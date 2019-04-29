---
title: Usar um modelo do Azure Resource Manager para criar um espaço de trabalho
titleSuffix: Azure Machine Learning service
description: Saiba como usar um modelo do Azure Resource Manager para criar um novo workspace de Serviço do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 04/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: 7349998325e56d5ebb78de5ca30c0127f09102aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819539"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning-service"></a>Usar um modelo do Azure Resource Manager para criar um espaço de trabalho para o serviço de Azure Machine Learning

Neste artigo, você aprenderá várias maneiras de criar um workspace de Serviço do Azure Machine Learning usando modelos do Azure Resource Manager. Um modelo do Resource Manager facilita a criação de recursos como uma operação única e coordenada. Um modelo é um documento JSON que define os recursos necessários para uma implantação. Além disso, pode especificar os parâmetros de implantação. Os parâmetros são usados para fornecer valores de entrada ao usar o modelo.

Para saber mais, confira [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma **assinatura do Azure**. Se você não tiver uma, experimente a [versão paga ou gratuita do Serviço do Azure Machine Learning](https://aka.ms/AMLFree).

* Para usar um modelo a partir de uma CLI, você precisará do [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) ou da [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Modelo do Resource Manager

O modelo do Resource Manager a seguir pode ser usado para criar um espaço de trabalho do serviço de Azure Machine Learning e os recursos do Azure associados:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "The name of the Azure Machine Learning service workspace."
            }
        },
        "location": {
            "type": "string",
            "allowedValues": [
                "eastus",
                "eastus2",
                "southcentralus",
                "southeastasia",
                "westcentralus",
                "westeurope",
                "westus2"
            ],
            "metadata": {
                "description": "The location where the workspace will be created."
            }
        }
    },
    "variables": {
        "storageAccount": {
            "name": "[concat('sa',uniqueString(resourceGroup().id))]",
            "type": "Standard_LRS"
        },
        "keyVault": {
            "name": "[concat('kv',uniqueString(resourceGroup().id))]",
            "tenantId": "[subscription().tenantId]"
        },
        "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
        "containerRegistryName": "[concat('cr',uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "name": "[variables('storageAccount').name]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2018-07-01",
            "sku": {
                "name": "[variables('storageAccount').type]"
            },
            "kind": "StorageV2",
            "properties": {
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "supportHttpsTrafficOnly": true
            }
        },
        {
            "name": "[variables('keyVault').name]",
            "type": "Microsoft.KeyVault/vaults",
            "apiVersion": "2018-02-14",
            "location": "[parameters('location')]",
            "properties": {
                "tenantId": "[variables('keyVault').tenantId]",
                "sku": {
                    "name": "standard",
                    "family": "A"
                },
                "accessPolicies": []
            }
        },
        {
            "name": "[variables('applicationInsightsName')]",
            "type": "Microsoft.Insights/components",
            "apiVersion": "2015-05-01",
            "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
            "kind": "web",
            "properties": {
                "Application_Type": "web"
            }
        },
        {
            "name": "[variables('containerRegistryName')]",
            "type": "Microsoft.ContainerRegistry/registries",
            "apiVersion": "2017-10-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard"
            },
            "properties": {
                "adminUserEnabled": true
            }
        },
        {
            "name": "[parameters('workspaceName')]",
            "type": "Microsoft.MachineLearningServices/workspaces",
            "apiVersion": "2018-11-19",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[variables('storageAccount').name]",
                "[variables('keyVault').name]",
                "[variables('applicationInsightsName')]",
                "[variables('containerRegistryName')]"
            ],
            "identity": {
                "type": "systemAssigned"
            },
            "properties": {
                "friendlyName": "[parameters('workspaceName')]",
                "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVault').name)]",
                "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
                "containerRegistry": "[resourceId('Microsoft.ContainerRegistry/registries',variables('containerRegistryName'))]",
                "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccount').name)]"
            }
        }
    ]
}
```

Esse modelo cria os seguintes serviços do Azure:

* Grupo de recursos do Azure
* Conta de Armazenamento do Azure
* Cofre da Chave do Azure
* Azure Application Insights
* Registro de Contêiner do Azure
* Workspace do Azure Machine Learning

O grupo de recursos é o contêiner que retém os serviços. Os diversos serviços são exigidos pelo workspace do Azure Machine Learning.

O modelo de exemplo tem dois parâmetros:

* A **localização** onde o grupo de recursos e serviços serão criados.

    O modelo usará a localização selecionada para a maioria dos recursos. A exceção é o serviço do Application Insights que não está disponível em todos os locais de disponibilidade dos outros serviços. Se você selecionar uma localização onde não esteja disponível, o serviço será criado na localização Centro-Sul dos EUA.

* O **nome do workspace** é o nome amigável do workspace do Azure Machine Learning.

    Os nomes dos outros serviços são gerados aleatoriamente.

Para obter mais informações sobre modelos, consulte os artigos a seguir:

* [Criar modelos do Gerenciador de Recursos do Azure](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Implantar um aplicativo com o modelo do Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md)
* [Tipos de recursos do Microsoft.MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

1. Siga as etapas em [Implantar recursos do modelo personalizado](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Ao acessar a tela __Editar modelo__, cole o modelo deste documento.
1. Selecione __Salvar__ para usar o modelo. Forneça as informações a seguir e concorde com os termos e condições listados:

   * Assinatura: Selecione a assinatura do Azure a ser usada para esses recursos.
   * Grupo de recursos: Selecione ou crie um grupo de recursos para conter os serviços.
   * Nome do workspace: O nome a ser usado para o workspace do Azure Machine Learning que será criado. O nome do workspace deverá ter entre 3 e 33 caracteres. E o nome poderá conter apenas caracteres alfanuméricos e '-'.
   * Localização: Selecione a localização onde os recursos serão criados.

     ![Os parâmetros do modelo no portal do Azure](media/how-to-create-workspace-template/template-parameters.png)

Para obter mais informações, consulte [Implantar recursos de modelo personalizado](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Usar PowerShell do Azure

Este exemplo assume que você salvou o modelo em um arquivo nomeado `azuredeploy.json` no diretório atual:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

Para obter mais informações, consulte [Implantar recursos com modelos do Resource Manager e do Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md) e [implantar modelo do Resource Manager privado com o token SAS e o Azure PowerShell](../../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="use-azure-cli"></a>Usar a CLI do Azure

Este exemplo assume que você salvou o modelo em um arquivo nomeado `azuredeploy.json` no diretório atual:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace
```

Para obter mais informações, consulte [Implantar recursos com modelos do Resource Manager e da CLI do Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md) e [implantar modelo do Resource Manager privado com o token SAS e a CLI do Azure](../../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="next-steps"></a>Próximas etapas

* [Implantar recursos com modelos do Resource Manager e API REST do Resource Manager](../../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Criar e implantar grupos de recursos do Azure por meio do Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
