---
title: Usar o dimensionamento automático do Azure com métricas de convidado em um modelo de conjunto de dimensionamento do Linux | Microsoft Docs
description: Saiba como fazer dimensionamento automático usando métricas de convidado em um modelo de Conjunto de Dimensionamento de Máquinas Virtuais do Linux
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: na
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: manayar
ms.openlocfilehash: deddcc8623803f9d003f3fafcef5252ebd34b813
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60803344"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Dimensionamento automático usando métricas de convidado em um modelo de conjunto de dimensionamento do Linux

Há dois tipos de métricas no Azure que são coletadas de VMs e conjuntos de dimensionamento: alguns vêm da VM de host e outros, da VM convidada. Em um nível alto, se você estiver usando métricas de rede, disco e CPU padrão, as métricas de host provavelmente serão uma boa opção. Mas se você precisar de uma seleção maior de métricas, as métricas de convidado provavelmente serão mais adequadas. Vamos analisar as diferenças entre ambas:

As métricas de host são mais simples e confiáveis. Elas não exigem configuração adicional porque são coletadas pela VM host, enquanto as métricas da convidada exigem a instalação da [extensão Diagnóstico do Microsoft Azure](../virtual-machines/windows/extensions-diagnostics-template.md) ou da [extensão de Diagnóstico do Azure do Linux](../virtual-machines/linux/diagnostic-extension.md) na VM convidada. Um motivo comum para usar métricas de convidado, em vez de métricas de host, é que as métricas de convidado fornecem uma seleção maior de métricas do que as métricas de host. Um exemplo disso são as métricas de consumo de memória, que só estão disponíveis por meio de métricas de convidado. As métricas de host com suporte estão listadas [aqui](../azure-monitor/platform/metrics-supported.md) e métricas de convidado comumente usadas estão listadas [aqui](../azure-monitor/platform/autoscale-common-metrics.md). Este artigo mostra como modificar o [modelo de conjunto de dimensionamento viável mínimo](./virtual-machine-scale-sets-mvss-start.md) para usar regras de dimensionamento automático com base nas métricas de convidado para conjuntos de dimensionamento do Linux.

## <a name="change-the-template-definition"></a>Alterar a definição do modelo

O modelo de conjunto de dimensionamento mínimo viável pode ser visto [aqui](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json) e o modelo para implantar o conjunto de dimensionamento Linux com dimensionamento automático baseado em convidado pode ser visto [aqui](https://raw.githubusercontent.com/gatneil/mvss/guest-based-autoscale-linux/azuredeploy.json). Vamos examinar a comparação usada para criar esse modelo (`git diff minimum-viable-scale-set existing-vnet`), parte por parte:

Primeiro, adicione parâmetros para `storageAccountName` e `storageAccountSasToken`. O agente de diagnóstico armazena dados de métrica em uma [tabela](../cosmos-db/table-storage-how-to-use-dotnet.md) nesta conta de armazenamento. Do Agente de Diagnóstico do Linux versão 3.0 em diante, não há mais suporte para usar uma chave de acesso de armazenamento. Nesse caso, use um [Token SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

Em seguida, modifique o conjunto de dimensionamento `extensionProfile` para incluir a extensão de diagnóstico. Nessa configuração, especifique a ID do recurso do conjunto de dimensionamento cujas métricas serão coletadas, bem como a conta de armazenamento e o token SAS a serem usados para armazenar as métricas. Especifique com que frequência as métricas são agregadas (neste caso, a cada minuto) e quais métricas devem ser acompanhadas (neste caso, o percentual de memória usada). Para obter informações mais detalhadas sobre essa configuração e métricas diferentes da porcentagem de memória usada, consulte [esta documentação](../virtual-machines/linux/diagnostic-extension.md).

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

Finalmente, adicione um recurso `autoscaleSettings` para configurar o dimensionamento automático com base nessas métricas. Esse recurso tem uma cláusula `dependsOn` que faz referência a conjunto de dimensionamento para garantir que o conjunto de dimensionamento exista antes de tentar realizar seu dimensionamento automático. Se você escolher uma métrica diferente para fazer o dimensionamento automático, use o `counterSpecifier` da configuração de extensão de diagnóstico, como o `metricName` na configuração de dimensionamento automático. Para obter mais informações sobre a configuração de dimensionamento automático, consulte as [práticas recomendadas de dimensionamento automático](..//azure-monitor/platform/autoscale-best-practices.md) e a [documentação de referência da API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931928.aspx).

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
