---
title: Exemplos de modelo do Resource Manager para regras de coleta de dados
description: Modelos do Azure Resource Manager de exemplo para criar associações entre regras de coleta de dados e máquinas virtuais no Azure Monitor.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: d241cb5d7ece260de42088eecfd669e5d3f40096
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592278"
---
# <a name="resource-manager-template-samples-for-data-collection-rules-in-azure-monitor"></a>Exemplos de modelo do Resource Manager para regras de coleta de dados no Azure Monitor
Este artigo inclui [modelos do Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) de exemplo para criar uma associação entre uma regra de [coleta de dados](data-collection-rule-overview.md) e o [agente do Azure Monitor](./azure-monitor-agent-overview.md). Cada amostra inclui um arquivo de modelo e um arquivo de parâmetros com valores de amostra para fornecer ao modelo.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-association-with-azure-vm"></a>Criar associação com a VM do Azure

O exemplo a seguir cria uma associação entre uma máquina virtual do Azure e uma regra de coleta de dados.

### <a name="template-file"></a>Arquivo de modelo

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('vmName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this virtual machine.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Arquivo de parâmetro.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```

## <a name="create-association-with-azure-arc"></a>Criar associação com o Azure Arc

O exemplo a seguir cria uma associação entre um servidor habilitado para o Azure Arc e uma regra de coleta de dados.

### <a name="template-file"></a>Arquivo de modelo

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.HybridCompute/machines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('machineName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this Arc server.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Arquivo de parâmetro.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```


## <a name="next-steps"></a>Próximas etapas

* [Obtenha outros modelos de amostra do Azure Monitor](../resource-manager-samples.md).
* [Saiba mais sobre o agente do Log Analytics](./log-analytics-agent.md).
* [Saiba mais sobre a extensão de diagnóstico](./diagnostics-extension-overview.md).