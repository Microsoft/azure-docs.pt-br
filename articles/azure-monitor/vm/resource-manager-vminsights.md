---
title: Amostras de modelo do Resource Manager para insights da VM
description: Realize amostragem de modelos do Azure Resource Manager para implantar e configurar insights da VM.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 05/18/2020
ms.openlocfilehash: 2ee447b1e5f72ce916808a5832f999eea493994c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046832"
---
# <a name="resource-manager-template-samples-for-vm-insights"></a>Amostras de modelo do Resource Manager para insights da VM
Este artigo inclui [modelos do Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) de amostra para habilitar os insights da VM em máquinas virtuais. Cada amostra inclui um arquivo de modelo e um arquivo de parâmetros com valores de amostra para fornecer ao modelo.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="configure-workspace"></a>Configurar o workspace
A amostra a seguir habilita os insights da VM para um workspace do Log Analytics.


### <a name="template-file"></a>Arquivo de modelo

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "String",
            "metadata": {
                "description": "Resource ID of the workspace."
            }
        },
        "workspaceLocation": {
            "type": "String",
            "metadata": {
                "description": "Location of the workspace."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2017-05-10",
            "name": "VMISolutionDeployment",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceLocation')]",
                            "name": "[concat('VMInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('WorkspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[concat('VMInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[concat('OMSGallery/', 'VMInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                }
            },
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]"
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
        "workspaceResourceId": {
            "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
        },
        "workspaceLocation": {
            "value": "eastus"
        }
    }
}
```


## <a name="onboard-an-azure-virtual-machine"></a>Integrar uma máquina virtual do Azure
A amostra a seguir adiciona uma máquina virtual do Azure aos insights da VM.


### <a name="template-file"></a>Arquivo de modelo

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "VmResourceId": {
            "type": "String",
            "metadata": {
                "description": "VM Resource ID."
            }
        },
        "VmLocation": {
            "type": "String",
            "metadata": {
                "description": "The Virtual Machine Location."
            }
        },
        "osType": {
            "type": "String",
            "metadata": {
                "description": "OS Type, Example: Linux / Windows"
            }
        },
        "WorkspaceResourceId": {
            "type": "String",
            "metadata": {
                "description": "Workspace Resource ID."
            }
        }
    },
    "variables": {
        "VmName": "[split(parameters('VmResourceId'),'/')[8]]",
        "DaExtensionName": "[if(equals(toLower(parameters('osType')), 'windows'), 'DependencyAgentWindows', 'DependencyAgentLinux')]",
        "DaExtensionType": "[if(equals(toLower(parameters('osType')), 'windows'), 'DependencyAgentWindows', 'DependencyAgentLinux')]",
        "DaExtensionVersion": "9.5",
        "MmaExtensionName": "[if(equals(toLower(parameters('osType')), 'windows'), 'MMAExtension', 'OMSExtension')]",
        "MmaExtensionType": "[if(equals(toLower(parameters('osType')), 'windows'), 'MicrosoftMonitoringAgent', 'OmsAgentForLinux')]",
        "MmaExtensionVersion": "[if(equals(toLower(parameters('osType')), 'windows'), '1.0', '1.4')]"
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2018-10-01",
            "name": "[variables('VmName')]",
            "location": "[parameters('VmLocation')]",
            "resources": [
                {
                    "type": "extensions",
                    "apiVersion": "2018-10-01",
                    "name": "[variables('DaExtensionName')]",
                    "location": "[parameters('VmLocation')]",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/VirtualMachines/', variables('VmName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                        "type": "[variables('DaExtensionType')]",
                        "typeHandlerVersion": "[variables('DaExtensionVersion')]",
                        "autoUpgradeMinorVersion": true
                    }
                },
                {
                    "type": "extensions",
                    "apiVersion": "2018-10-01",
                    "name": "[variables('MmaExtensionName')]",
                    "location": "[parameters('VmLocation')]",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/VirtualMachines/', variables('VmName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "[variables('MmaExtensionType')]",
                        "typeHandlerVersion": "[variables('MmaExtensionVersion')]",
                        "autoUpgradeMinorVersion": "true",
                        "settings": {
                            "workspaceId": "[reference(parameters('WorkspaceResourceId'), '2015-03-20').customerId]",
                            "azureResourceId": "[parameters('VmResourceId')]",
                            "stopOnMultipleConnections": "true"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(parameters('WorkspaceResourceId'), '2015-03-20').primarySharedKey]"
                        }
                    }
                }
            ]
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
        "vmResourceId": {
            "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-linux-vm"
        },
        "vmLocation": {
            "value": "westus"
        },
        "osType": {
            "value": "linux"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
        }
    }
}

```


## <a name="onboard-an-azure-virtual-machine-scale-set"></a>Integrar um conjunto de dimensionamento de máquinas virtuais do Azure
A amostra a seguir adiciona um conjunto de dimensionamento de máquinas virtuais do Azure aos insights da VM.


### <a name="template-file"></a>Arquivo de modelo

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "VmssResourceId": {
            "type": "String",
            "metadata": {
                "description": "VM Resource ID."
            }
        },
        "VmssLocation": {
            "type": "String",
            "metadata": {
                "description": "The Virtual Machine Location."
            }
        },
        "osType": {
            "type": "String",
            "metadata": {
                "description": "OS Type, Example: Linux / Windows"
            }
        },
        "WorkspaceResourceId": {
            "type": "String",
            "metadata": {
                "description": "Workspace Resource ID."
            }
        }
    },
    "variables": {
        "VmssName": "[split(parameters('VmssResourceId'),'/')[8]]",
        "DaExtensionName": "[if(equals(toLower(parameters('osType')), 'windows'), 'DependencyAgentWindows', 'DependencyAgentLinux')]",
        "DaExtensionType": "[if(equals(toLower(parameters('osType')), 'windows'), 'DependencyAgentWindows', 'DependencyAgentLinux')]",
        "DaExtensionVersion": "9.5",
        "MmaExtensionName": "[if(equals(toLower(parameters('osType')), 'windows'), 'MMAExtension', 'OMSExtension')]",
        "MmaExtensionType": "[if(equals(toLower(parameters('osType')), 'windows'), 'MicrosoftMonitoringAgent', 'OmsAgentForLinux')]",
        "MmaExtensionVersion": "[if(equals(toLower(parameters('osType')), 'windows'), '1.0', '1.4')]"
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "apiVersion": "2018-10-01",
            "name": "[variables('VmssName')]",
            "location": "[parameters('VmssLocation')]",
            "resources": [
                {
                    "type": "extensions",
                    "apiVersion": "2018-10-01",
                    "name": "[variables('DaExtensionName')]",
                    "location": "[parameters('VmssLocation')]",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/virtualMachineScaleSets/', variables('VmssName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                        "type": "[variables('DaExtensionType')]",
                        "typeHandlerVersion": "[variables('DaExtensionVersion')]",
                        "autoUpgradeMinorVersion": true
                    }
                },
                {
                    "type": "extensions",
                    "apiVersion": "2018-10-01",
                    "name": "[variables('MmaExtensionName')]",
                    "location": "[parameters('VmssLocation')]",
                    "dependsOn": [
                        "[concat('Microsoft.Compute/virtualMachineScaleSets/', variables('VmssName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                        "type": "[variables('MmaExtensionType')]",
                        "typeHandlerVersion": "[variables('MmaExtensionVersion')]",
                        "autoUpgradeMinorVersion": "true",
                        "settings": {
                            "workspaceId": "[reference(parameters('WorkspaceResourceId'), '2015-03-20').customerId]",
                            "azureResourceId": "[parameters('VmssResourceId')]",
                            "stopOnMultipleConnections": "true"
                        },
                        "protectedSettings": {
                            "workspaceKey": "[listKeys(parameters('WorkspaceResourceId'), '2015-03-20').primarySharedKey]"
                        }
                    }
                }
            ]
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
        "VmssResourceId": {
            "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-windows-vmss"
        },
        "VmssLocation": {
            "value": "westus"
        },
        "OsType": {
            "value": "windows"
        },
        "WorkspaceResourceId": {
            "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
        }
    }
}
```


## <a name="next-steps"></a>Próximas etapas

* [Obtenha outros modelos de amostra do Azure Monitor](../resource-manager-samples.md).
* [Saiba mais](vminsights-overview.md) sobre os insights da VM.
