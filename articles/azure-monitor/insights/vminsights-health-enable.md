---
title: Habilitar a integridade de convidado do Azure Monitor para VMs (versão prévia)
description: Descreve como habilitar Azure Monitor para VMs integridade de convidado em sua assinatura e como carregar VMs.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/16/2020
ms.custom: references_regions
ms.openlocfilehash: 902bcaa97658802b35fb523a1213e6bbd47f357f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684501"
---
# <a name="enable-azure-monitor-for-vms-guest-health-preview"></a>Habilitar a integridade de convidado do Azure Monitor para VMs (versão prévia)
Azure Monitor para VMs integridade de convidado permite que você exiba a integridade de uma máquina virtual conforme definido por um conjunto de medidas de desempenho que são amostradas em intervalos regulares. Este artigo descreve como habilitar esse recurso em sua assinatura e como habilitar o monitoramento de convidado para cada máquina virtual.

## <a name="current-limitations"></a>Limitações atuais
Azure Monitor para VMs integridade de convidado tem as seguintes limitações na visualização pública:

- No momento, somente as máquinas virtuais do Azure têm suporte. No momento, não há suporte para o arco do Azure para servidores.


## <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis
A máquina virtual deve executar um dos seguintes sistemas operacionais: 

  - Ubuntu 16, 4 LTS, Ubuntu 18, 4 LTS
  - Windows Server 2012 ou posterior

## <a name="supported-regions"></a>Regiões com suporte

A máquina virtual deve estar localizada em uma das seguintes regiões:

- Austrália Central
- Leste da Austrália
- Australia Southeast
- Índia Central
- Centro dos EUA
- Leste da Ásia
- Leste dos EUA
- Leste dos EUA 2
- Leste dos EUA 2 EUAP
- Centro-Oeste da Alemanha
- Japan East
- Centro-Norte dos EUA
- Norte da Europa
- Centro-Sul dos Estados Unidos
- Sudeste Asiático
- Sul do Reino Unido
- Centro-Oeste dos EUA
- Europa Ocidental
- Oeste dos EUA
- Oeste dos EUA 2


Log Analytics espaço de trabalho deve estar localizado em uma das seguintes regiões:

- Centro dos EUA
- Leste dos EUA
- Leste dos EUA 2
- Leste dos EUA 2 EUAP
- Norte da Europa
- Sudeste Asiático
- Sul do Reino Unido
- Região de Europa Ocidental
- Oeste dos EUA 2

## <a name="prerequisites"></a>Pré-requisitos

- A máquina virtual deve ser integrada a Azure Monitor para VMs.
- O usuário executando etapas de integração deve ter um nível de colaborador mínimo acesso à assinatura em que a máquina virtual e a regra de coleta de dados estão localizadas.
- Os provedores de recursos do Azure necessários devem ser registrados conforme descrito na seção a seguir.

## <a name="register-required-azure-resource-providers"></a>Registrar os provedores de recursos do Azure necessários
Os provedores de recursos do Azure a seguir serão registrados para sua assinatura para habilitar Azure Monitor para VMs integridade de convidado. 

- Microsoft.WorkloadMonitor
- Microsoft.insights

Você pode usar qualquer um dos métodos disponíveis para registrar um provedor de recursos conforme descrito em [provedores de recursos e tipos do Azure](../../azure-resource-manager/management/resource-providers-and-types.md). Você também pode usar o seguinte comando de exemplo usando armclient, postmaster ou outro método para fazer chamada autenticada para Azure Resource Manager:

```
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.WorkloadMonitor/register?api-version=2019-10-01
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.Insights/register?api-version=2019-10-01
```


## <a name="enable-a-virtual-machine-using-the-azure-portal"></a>Habilitar uma máquina virtual usando o portal do Azure
Quando você habilita a integridade de convidado para uma máquina virtual na portal do Azure, toda a configuração necessária é executada para você. Isso inclui a criação da regra exigir coleta de dados, a instalação da extensão de integridade do convidado na máquina virtual e a criação de uma associação com a regra de coleta de dados.

Na exibição de **introdução** no Azure monitor para VMs, clique no link ao lado da mensagem de atualização de uma máquina virtual e, em seguida, clique no botão **Atualizar** . Você também pode selecionar várias máquinas virtuais para atualizá-las juntas.

![Habilitar o recurso de integridade na máquina virtual](media/vminsights-health-enable/enable-agent.png)


## <a name="enable-a-virtual-machine-using-resource-manager-template"></a>Habilitar uma máquina virtual usando o modelo do Resource Manager
Há três etapas necessárias para habilitar as máquinas virtuais usando Azure Resource Manager.

- Criar regra de coleta de dados.
- Instalar a extensão de integridade do convidado em cada máquina virtual
- Crie uma associação entre a máquina virtual e a regra de coleta de dados.

### <a name="create-data-collection-rule-dcr"></a>Criar regra de coleta de dados (DCR)

> [!NOTE]
> Se você habilitar uma máquina virtual usando a portal do Azure, a regra de coleta de dados descrita aqui será criada para você. Nesse caso, você não precisa executar esta etapa.

A configuração dos monitores no Azure Monitor para VMs integridade do convidado é armazenada em [Data Collection Rules (DCR)](../platform/data-collection-rule-overview.md). Cada máquina virtual com a extensão de integridade do convidado precisará de uma associação com essa regra.

> [!NOTE]
> Você pode criar regras de coleta de dados adicionais para modificar a configuração padrão dos monitores, conforme descrito em [Configurar monitoramento em Azure monitor para VMs integridade de convidado (versão prévia)](vminsights-health-configure.md).

O modelo requer valores para os seguintes parâmetros:

- **defaultHealthDataCollectionRuleName**: Mantenha o nome padrão definido no modelo.
- **destinationWorkspaceResourceId**: ID de recurso do espaço de trabalho log Analytics usado para coleta de dados de máquina virtual.
- **dataCollectionRuleLocation**: região da regra de coleta de dados. Isso deve corresponder à região do espaço de trabalho Log Analytics.


Implante o modelo usando qualquer [método de implantação para modelos do Resource Manager](../../azure-resource-manager/templates/deploy-powershell.md). Os comandos a seguir implantam o modelo e o arquivo de parâmetros usando o PowerShell ou CLI do Azure.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDataCollectionRule -ResourceGroupName my-resource-group -TemplateFile Health.DataCollectionRule.template.json -TemplateParameterFile Health.DataCollectionRule.template.parameters.json
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az deployment group create --name GuestHealthDataCollectionRule --resource-group my-resource-group --template-file Health.DataCollectionRule.template.json --parameters Health.DataCollectionRule.template.parameters.json
```

---

A regra de coleta de dados definida no modelo do Resource Manager abaixo habilita todos os monitores para as máquinas virtuais com a extensão de integridade do convidado. Ele deve incluir fontes de dados para cada um dos contadores de desempenho usados pelos monitores.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "defaultHealthDataCollectionRuleName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule to create."
      },
      "defaultValue": "Microsoft-VMInsights-Health"
    },
    "destinationWorkspaceResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the Azure resource ID of the Log Analytics workspace to use to store virtual machine health data."
      }
    },
    "dataCollectionRuleLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code in which the data collection rule should be deployed. Examples: eastus, westeurope, etc"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/dataCollectionRules",
      "name": "[parameters('defaultHealthDataCollectionRuleName')]",
      "location": "[parameters('dataCollectionRuleLocation')]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Data collection rule for VM Insights health.",
        "dataSources": {
          "performanceCounters": [
              {
                  "name": "VMHealthPerfCounters",
                  "streams": [ "Microsoft-Perf" ],
                  "scheduledTransferPeriod": "PT1M",
                  "samplingFrequencyInSeconds": 60,
                  "counterSpecifiers": [
                      "\\LogicalDisk(*)\\% Free Space",
                      "\\Memory\\Available Bytes",
                      "\\Processor(_Total)\\% Processor Time"
                  ]
              }
          ],
          "extensions": [
            {
              "name": "Microsoft-VMInsights-Health",
              "streams": [
                "Microsoft-HealthStateChange"
              ],
              "extensionName": "HealthExtension",
              "extensionSettings": {
                "schemaVersion": "1.0",
                "contentVersion": "",
                "healthRuleOverrides": [
                  {
                    "scopes": [ "*" ],
                    "monitors": ["root"],
                    "alertConfiguration": {
                      "isEnabled": true
                    }
                  }
                ]
              },
              "inputDataSources": [
                  "VMHealthPerfCounters"
              ]

            }
          ]
        },
        "destinations": {
          "logAnalytics": [
            {
              "workspaceResourceId": "[parameters('destinationWorkspaceResourceId')]",
              "name": "Microsoft-HealthStateChange-Dest"
            }
          ]
        },                  
        "dataFlows": [
          {
            "streams": [
              "Microsoft-HealthStateChange"
            ],
            "destinations": [
              "Microsoft-HealthStateChange-Dest"
            ]
          }
        ]
      }
    }
  ]
}
```

### <a name="sample-parameter-file"></a>Arquivo de parâmetro de exemplo

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "destinationWorkspaceResourceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
      },
      "dataCollectionRuleLocation": {
        "value": "eastus"
      }
  }
}
```



### <a name="install-guest-health-extension-and-associate-with-data-collection-rule"></a>Instalar a extensão de integridade do convidado e associar à regra de coleta de dados
Use o seguinte modelo do Resource Manager para habilitar uma máquina virtual para a integridade do convidado. Isso instala a extensão de integridade do convidado e cria a associação com a regra de coleta de dados. Você pode implantar esse modelo usando qualquer [método de implantação para modelos do Resource Manager](../../azure-resource-manager/templates/deploy-powershell.md).


Por exemplo, use os comandos a seguir para implantar o modelo e o arquivo de parâmetros em um grupo de recursos usando o PowerShell ou a CLI do Azure.


# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az deployment group create --name GuestHealthDeployment --resource-group my-resource-group --template-file Health.VirtualMachine.template.json --parameters Health.VirtualMachine.template.parameters.json
```

---

### <a name="template-file"></a>Arquivo de modelo

``` json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "virtualMachineName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the virtual machine."
      }
    },
    "virtualMachineLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code of the virtual machine region (location). Examples: eastus, westeurope, etc"
      }
    },
    "virtualMachineOsType": {
      "type": "string",
      "metadata": {
        "description": "Specifies operating system type of the target virtual machine."
      },
      "allowedValues": ["windows", "linux"]
    },
    "dataCollectionRuleAssociationName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule association to create."
      },
      "defaultValue": "VM-Health-Dcr-Association"
    },
    "healthDataCollectionRuleResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies resource id of Azure Monitor Data Collection Rule for virtual machine health data."
      }
    }
  },
  "variables": {
    "healthExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthWindowsAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthLinuxAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      }
    },
    "azureMonitorAgentExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorWindowsAgent", 
        "typeHandlerVersion": "1.0", 
        "autoUpgradeMinorVersion": false 
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorLinuxAgent", 
        "typeHandlerVersion": "1.5", 
        "autoUpgradeMinorVersion": false 
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('virtualMachineName')]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2017-03-30",
      "identity": {
        "type": "SystemAssigned"
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
      "name": "[concat(parameters('virtualMachineName'), '/Microsoft.Insights/', parameters('dataCollectionRuleAssociationName'))]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Association of data collection rule for VM Insights Health.",
        "dataCollectionRuleId": "[parameters('healthDataCollectionRuleResourceId')]"
      }
    },
    { 
      "type": "Microsoft.Compute/virtualMachines/extensions", 
      "apiVersion": "2019-12-01", 
      "name": "[concat(parameters('virtualMachineName'), '/', variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ], 
      "properties": "[variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')]]"
    },
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('virtualMachineName'), '/', variables('healthExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2018-06-01",
      "dependsOn": [ 
        "[resourceId('Microsoft.Compute/virtualMachines/extensions', parameters('virtualMachineName'), variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ],    
      "properties": "[variables('healthExtensionProperties')[parameters('virtualMachineOsType')]]"
    }               
  ]
}
```

### <a name="sample-parameter-file"></a>Arquivo de parâmetro de exemplo

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualMachineName": {
        "value": "myvm"
      },
      "virtualMachineLocation": {
        "value": "eastus"
      },
      "virtualMachineOsType": {
        "value": "linux"
      },
      "healthDataCollectionRuleResourceId": {
        "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/dataCollectionRules/Microsoft-VMInsights-Health"
      }
  }
}
```

## <a name="next-steps"></a>Próximas etapas

- [Personalizar monitores habilitados pelo Azure Monitor para VMs](vminsights-health-configure.md)