---
title: Implantar recursos com a API REST e o modelo | Microsoft Docs
description: Use o Azure Resource Manager e a REST API do Gerenciador de recursos para implantar recursos no Azure. Os recursos são definidos em um modelo do Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: tomfitz
ms.openlocfilehash: 42f6ce96cf339e90ed0a0dcdbdb3f1b6924430e9
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206405"
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Implantar recursos com modelos do Resource Manager e a API REST do Resource Manager

Este artigo explica como usar a API REST do Resource Manager com modelos do Resource Manager para implantar seus recursos no Azure.  

Você pode incluir seu modelo no corpo da solicitação ou vinculá-lo a um arquivo. Se optar pelo arquivo, ele poderá ser local ou um arquivo externo disponível por meio de um URI. Quando seu modelo estiver em uma conta de armazenamento, você poderá restringir o acesso a ele e fornecer um token de SAS (Assinatura de Acesso Compartilhado) durante a implantação.

## <a name="deployment-scope"></a>Escopo da implantação

Você pode direcionar sua implantação para um grupo de gerenciamento, uma assinatura do Azure ou um grupo de recursos. Na maioria dos casos, você vai direcionar as implantações para um grupo de recursos. Use implantações de assinatura ou grupo de gerenciamento para aplicar políticas e as atribuições de função no escopo especificado. Você também pode usar implantações de assinatura para criar um grupo de recursos e implantar recursos nele. Dependendo do escopo da implantação, você deve usar comandos diferentes.

Para implantar em um **grupo de recursos**, use [implantações - crie](/rest/api/resources/deployments/createorupdate). A solicitação é enviada para:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Para implantar em um **assinatura**, use [implantações - crie no escopo de assinatura](/rest/api/resources/deployments/createorupdateatsubscriptionscope). A solicitação é enviada para:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Para implantar em um **grupo de gerenciamento**, use [implantações - crie no escopo de grupo de gerenciamento](/rest/api/resources/deployments/createorupdateatmanagementgroupscope). A solicitação é enviada para:

```HTTP
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Os exemplos neste artigo usam a implantações do grupo de recursos. Para obter mais informações sobre implantações de assinatura, consulte [criar grupos de recursos e recursos no nível da assinatura](deploy-to-subscription.md).

## <a name="deploy-with-the-rest-api"></a>Implantar com a API REST

1. Definir [Parâmetros e cabeçalhos comuns](/rest/api/azure/), incluindo tokens de autenticação.

1. Se você não tiver um grupo de recursos existente, crie um grupo de recursos. Forneça sua ID de assinatura, o nome do novo grupo de recursos e local que você precisa para sua solução. Para obter mais informações, consulte [Criar um grupo de recursos](/rest/api/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-05-01
   ```

   Com um corpo de solicitação como:

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. Valide sua implantação antes de executá-la usando a operação [Validar uma implantação do modelo](/rest/api/resources/deployments/validate) . Ao testar a implantação, forneça parâmetros exatamente como faria ao executar a implantação (mostrado na próxima etapa).

1. Para implantar um modelo, forneça sua ID de assinatura, o nome do grupo de recursos, o nome da implantação no URI da solicitação. 

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-05-01
   ```

   No corpo da solicitação, forneça um link para seu arquivo de modelo e parâmetro. Observe que **mode** está definido como **Incremental**. Para executar uma implantação completa, defina **mode** como **Complete**. Tenha cuidado ao usar o modo completo, pois você pode excluir acidentalmente recursos que não estão em seu modelo.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental"
    }
   }
   ```

    Se você quiser registrar o conteúdo da resposta, o conteúdo da solicitação ou ambos, inclua **debugSetting** na solicitação.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    Você pode configurar sua conta de armazenamento para usar um token de SAS (Assinatura de Acesso Compartilhado). Para obter mais informações, consulte [Delegando Acesso com uma Assinatura de Acesso Compartilhado](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

1. Em vez de vincular os modelo e parâmetros a um arquivo, você pode incluí-los no corpo da solicitação. O exemplo a seguir mostra o corpo da solicitação com o modelo e parâmetro embutido:

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
              "Standard_LRS",
              "Standard_GRS",
              "Standard_ZRS",
              "Premium_LRS"
            ],
            "metadata": {
              "description": "Storage Account type"
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2018-02-01",
            "location": "[parameters('location')]",
            "sku": {
              "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
          }
        ],
        "outputs": {
          "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
          }
        }
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

1. Para obter o status da implantação do modelo, use [obter implantações -](/rest/api/resources/deployments/get).

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="redeploy-when-deployment-fails"></a>Reimplantar quando ocorrer falha na implantação

Esse recurso também é conhecido como *reversão em erro*. Quando uma implantação falha, é possível reimplantar automaticamente uma implantação anterior bem-sucedida com base em seu histórico de implantações. Para especificar a reimplantação, use a propriedade `onErrorDeployment` no corpo da solicitação. Essa funcionalidade é útil se você tem um bom estado conhecido para sua implantação de infra-estrutura e deseja reverter para esse estado. Há uma série de limitações e restrições:

- A reimplantação é executada exatamente como ele foi executado anteriormente com os mesmos parâmetros. Você não pode alterar os parâmetros.
- A implantação anterior é executada usando o [modo completo](./deployment-modes.md#complete-mode). Todos os recursos não incluídos na implantação anterior são excluídos e quaisquer configurações de recurso são definidas para seu estado anterior. Certifique-se de entender completamente o [modos de implantação](./deployment-modes.md).
- A reimplantação afeta apenas os recursos, as alterações de dados não são afetadas.
- Esse recurso só tem suporte em implantações do grupo de recursos, não implantações de nível da assinatura. Para obter mais informações sobre a implantação de nível de assinatura, consulte [criar grupos de recursos e recursos no nível da assinatura](./deploy-to-subscription.md).

Para usar essa opção, as implantações devem ter nomes exclusivos para que possam ser identificadas no histórico. Se você não tiver nomes exclusivos, a implantação atual com falha pode substituir a implantação bem-sucedida anteriormente no histórico. Você só pode usar essa opção com implantações de nível raiz. Implantações de um modelo aninhado não estão disponíveis para reimplantação.

Para reimplantar a última implantação bem-sucedida se a implantação atual falhar, use:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Para reimplantar uma implantação específica se a implantação atual falhar, use:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

A implantação especificada deve ter êxito.

## <a name="parameter-file"></a>Arquivo de parâmetro.

Se você usar um arquivo de parâmetro para passar os valores de parâmetro durante a implantação, será necessário criar um arquivo JSON com um formato semelhante ao exemplo a seguir:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "webSiteName": {
            "value": "ExampleSite"
        },
        "webSiteHostingPlanName": {
            "value": "DefaultPlan"
        },
        "webSiteLocation": {
            "value": "West US"
        },
        "adminPassword": {
            "reference": {
               "keyVault": {
                  "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
               },
               "secretName": "sqlAdminPassword"
            }
        }
   }
}
```

O tamanho do arquivo de parâmetro não pode ser superior a 64 KB.

Se você precisar fornecer um valor confidencial para um parâmetro (como uma senha), adicione esse valor em um cofre de chaves. Recupere o cofre de chaves durante a implantação, conforme mostrado no exemplo anterior. Para obter mais informações, veja [Transmitir valores seguros durante a implantação](resource-manager-keyvault-parameter.md). 

## <a name="next-steps"></a>Próximas etapas

- Para especificar como lidar com os recursos existentes no grupo de recursos, mas que não estão definidos no modelo, confira [Modos de implantação do Azure Resource Manager](deployment-modes.md).
- Para saber mais sobre como lidar com operações assíncronas de REST, confira [Track asynchronous Azure operations](resource-manager-async-operations.md) (Rastrear operações assíncronas do Azure).
- Para saber mais sobre modelos, consulte [entender a estrutura e sintaxe dos modelos do Azure Resource Manager](resource-group-authoring-templates.md).

