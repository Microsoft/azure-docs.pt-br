---
title: "Habilitar automaticamente as Configurações de Diagnóstico usando um modelo do Resource Manager | Microsoft Docs"
description: "Saiba como usar um modelo do Resource Manager para criar configurações de diagnóstico que ajudarão a transmitir seus logs de diagnóstico para os Hubs de Eventos ou armazená-los em uma conta de armazenamento."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a8a88a8c-4a48-4df6-8f7e-d90634d39c57
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/30/2017
ms.author: johnkem
ms.openlocfilehash: 2f764bc14e882f71957299b833d5bc1a6765622a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Habilitar automaticamente as Configurações de Diagnóstico na criação do recurso usando um modelo do Resource Manager
Neste artigo, mostramos como você pode usar um [Modelo do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) para definir as Configurações de Diagnóstico em um recurso quando ele é criado. Isso permite iniciar automaticamente o streaming de seus Logs de Diagnóstico e métricas para os Hubs de Eventos, arquivando-os em uma Conta de Armazenamento ou enviando-os para o Log Analytics quando um recurso é criado.

O método para habilitar os Logs de Diagnóstico usando um modelo do Resource Manager depende do tipo de recurso.

* **Não Computação** (por exemplo, Grupos de Segurança de Rede, Aplicativos Lógicos, Automação) usam as [Configurações de Diagnóstico descritas neste artigo](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings).
* **De Computação** (baseados no WAD/LAD) usam o [Arquivo de configuração do WAD/LAD descrito neste artigo](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

Neste artigo, descrevemos como configurar o diagnóstico usando um método.

Estas são as etapas básicas:

1. Crie um modelo como um arquivo JSON que descreve como criar o recurso e habilite o diagnóstico.
2. [Implantar o modelo usando qualquer método de implantação](../azure-resource-manager/resource-group-template-deploy.md).

Abaixo, damos um exemplo de arquivo JSON do modelo que você precisa gerar para os recursos de Computação e de Não Computação.

## <a name="non-compute-resource-template"></a>Modelo de recursos de Não Computação
Para os recursos de Não Computação, você precisará fazer duas coisas:

1. Adicione parâmetros ao blob de parâmetros para o nome da conta de armazenamento, ID de regra do barramento de serviço e/ou ID do espaço de trabalho do Log Analytics do OMS (permitindo o arquivamento dos Logs de Diagnóstico em uma conta de armazenamento, streaming de logs para os Hubs de Eventos e/ou envio de logs para o Log Analytics).
   
    ```json
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the Service Bus Rule for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Azure Resource ID of the Log Analytics workspace for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. Na matriz de recursos do recurso para o qual você deseja habilitar os Logs de Diagnóstico, adicione um recurso do tipo `[resource namespace]/providers/diagnosticSettings`.
   
    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/service",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2015-07-01",
        "properties": {
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
          "workspaceId": "[parameters('workspaceId')]",
          "logs": [ 
            {
              "category": "/* log category name */",
              "enabled": true,
              "retentionPolicy": {
                "days": 0,
                "enabled": false
              }
            }
          ],
          "metrics": [
            {
              "timeGrain": "PT1M",
              "enabled": true,
              "retentionPolicy": {
                "enabled": false,
                "days": 0
              }
            }
          ]
        }
      }
    ]
    ```

O blob de propriedades da Configuração de Diagnóstico segue [o formato descrito neste artigo](https://msdn.microsoft.com/library/azure/dn931931.aspx). A adição da propriedade `metrics` permitirá também o envio de métricas de recurso para essas mesmas saídas, desde que [o recurso ofereça suporte para as métricas do Azure Monitor](monitoring-supported-metrics.md).

Aqui temos um exemplo completo que cria um aplicativo lógico e ativa o streaming para Hubs de eventos e o armazenamento em uma conta de armazenamento.

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "logicAppName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Logic App that will be created."
      }
    },
    "testUri": {
      "type": "string",
      "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Logic/workflows",
      "name": "[parameters('logicAppName')]",
      "apiVersion": "2016-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "definition": {
          "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "Microsoft.Insights/service",
          "dependsOn": [
            "[resourceId('Microsoft.Logic/workflows', parameters('logicAppName'))]"
          ],
          "apiVersion": "2015-07-01",
          "properties": {
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "WorkflowRuntime",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ],
            "metrics": [
              {
                "timeGrain": "PT1M",
                "enabled": true,
                "retentionPolicy": {
                  "enabled": false,
                  "days": 0
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```

## <a name="compute-resource-template"></a>Modelo de recursos de computação
Para habilitar o diagnóstico em um recurso de Computação, por exemplo uma Máquina Virtual ou cluster do Service Fabric, você precisa:

1. Adicionar a extensão de Diagnóstico do Azure à definição do recurso da VM.
2. Especificar uma conta de armazenamento e/ou hub de eventos como um parâmetro.
3. Adicionar o conteúdo do arquivo XML WADCfg na propriedade XMLCfg, substituindo todos os caracteres XML corretamente.

> [!WARNING]
> Esta última etapa pode ser difícil de fazer. [Confira este artigo](../virtual-machines/windows/extensions-diagnostics-template.md#diagnostics-configuration-variables) para obter um exemplo que divide o Esquema de Configuração de Diagnóstico em variáveis que são substituídas e formatadas corretamente.
> 
> 

O processo inteiro, incluindo os exemplos, é descrito [neste documento](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre os Logs de Diagnóstico do Azure](monitoring-overview-of-diagnostic-logs.md)
* [Transmitir Logs de Diagnóstico do Azure para os Hubs de Eventos](monitoring-stream-diagnostic-logs-to-event-hubs.md)

