---
title: "Usar o dimensionamento automático do Azure com métricas de convidado em um modelo de conjunto de dimensionamento do Linux | Microsoft Docs"
description: "Saiba como fazer dimensionamento automático usando métricas de convidado em um modelo de Conjunto de Dimensionamento de Máquinas Virtuais do Linux"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: na
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: negat
ms.openlocfilehash: 98635ea6695fdb1e55456b5b6a293a3b4ad9d839
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Dimensionamento automático usando métricas de convidado em um modelo de conjunto de dimensionamento do Linux

Há dois tipos de métricas no Azure que são coletadas de VMs e conjuntos de dimensionamento: alguns vêm da VM de host e outros, da VM convidada. Em um nível alto, se você estiver usando métricas de rede, disco e CPU padrão, as métricas de host provavelmente serão uma boa opção. Mas se você precisar de uma seleção maior de métricas, as métricas de convidado provavelmente serão mais adequadas. Vamos analisar as diferenças entre ambas:

As métricas de host são mais simples e confiáveis. Elas não exigem configuração adicional porque são coletadas pela VM de host, enquanto as métricas de convidado exigem instalar a [extensão de Diagnóstico do Microsoft Azure](../virtual-machines/windows/extensions-diagnostics-template.md) ou a [extensão de Diagnóstico do Azure do Linux](../virtual-machines/linux/diagnostic-extension.md) na VM convidada. Um motivo comum para usar métricas de convidado, em vez de métricas de host, é que as métricas de convidado fornecem uma seleção maior de métricas do que as métricas de host. Um exemplo disso são as métricas de consumo de memória, que só estão disponíveis por meio de métricas de convidado. As métricas de host com suporte estão listadas [aqui](../monitoring-and-diagnostics/monitoring-supported-metrics.md) e métricas de convidado comumente usadas estão listadas [aqui](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md). Este artigo mostra como modificar o [modelo de conjunto de dimensionamento viável mínimo](./virtual-machine-scale-sets-mvss-start.md) para usar regras de dimensionamento automático com base nas métricas de convidado para conjuntos de dimensionamento do Linux.

## <a name="change-the-template-definition"></a>Alterar a definição do modelo

Nosso modelo de conjunto de dimensionamento viável mínimo pode ser visto [aqui](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json) e nosso modelo para implantar o conjunto de dimensionamento Linux com dimensionamento automático baseado em convidado pode ser visto [aqui](https://raw.githubusercontent.com/gatneil/mvss/guest-based-autoscale-linux/azuredeploy.json). Vamos examinar a comparação usada para criar esse modelo (`git diff minimum-viable-scale-set existing-vnet`), parte por parte:

Primeiro, adicionamos parâmetros para `storageAccountName` e `storageAccountSasToken`. O agente de diagnóstico armazenará dados de métrica em uma [tabela](../cosmos-db/table-storage-how-to-use-dotnet.md) nessa conta de armazenamento. Do Agente de Diagnóstico do Linux versão 3.0 em diante, não há mais suporte para usar uma chave de acesso de armazenamento. É necessário usar um [Token SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

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

Em seguida, modificamos o conjunto de dimensionamento `extensionProfile` para incluir a extensão de diagnóstico. Nessa configuração, podemos especificar a ID de recurso do conjunto de dimensionamento para coletar métricas, bem como a conta de armazenamento e o token SAS usado para armazenar as métricas. Podemos também especificar com que frequência as métricas são agregadas (neste caso, a cada minuto) e quais métricas rastrear (neste caso, porcentagem de memória usada). Para obter informações mais detalhadas sobre essa configuração e métricas diferentes da porcentagem de memória usada, consulte [esta documentação](../virtual-machines/linux/diagnostic-extension.md).

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

Por fim, adicionamos um recurso `autoscaleSettings` para configurar o dimensionamento automático com base nessas métricas. Esse recurso tem uma cláusula `dependsOn` que faz referência a conjunto de dimensionamento para garantir que o conjunto de dimensionamento exista antes de tentar realizar seu dimensionamento automático. Se, escolhemos uma métrica diferente para a qual fazer o dimensionamento automático, usaríamos o `counterSpecifier` da configuração de extensão de diagnóstico, como o `metricName` na configuração de dimensionamento automático. Para obter mais informações sobre a configuração de dimensionamento automático, consulte as [práticas recomendadas de dimensionamento automático](..//monitoring-and-diagnostics/insights-autoscale-best-practices.md) e a [documentação de referência da API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931928.aspx).

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
