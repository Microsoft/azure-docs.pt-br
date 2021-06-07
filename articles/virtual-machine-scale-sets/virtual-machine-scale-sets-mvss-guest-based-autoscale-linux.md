---
title: Usar o dimensionamento automático do Azure com métricas de convidado em um modelo de conjunto de dimensionamento Linux
description: Saiba como fazer dimensionamento automático usando métricas de convidado em um modelo de Conjunto de Dimensionamento de Máquinas Virtuais do Linux
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 04/26/2019
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 0605780651e1a3c54ae53d13a3f99e1124fa76db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585009"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Dimensionamento automático usando métricas de convidado em um modelo de conjunto de dimensionamento do Linux

Há dois tipos amplos de métricas no Azure que são coletadas de VMs e conjuntos de dimensionamento: métricas de host e métricas de convidado. Em um alto nível, se você quiser usar as métricas de CPU, disco e rede padrão, as métricas de host serão uma boa opção. No entanto, se você precisar de uma seleção maior de métricas, as métricas de convidado devem ser examinadas.

As métricas do host não exigem configuração adicional porque são coletadas pela VM do host, enquanto as métricas de convidado exigem que você instale a [extensão do Windows diagnóstico do Azure](../virtual-machines/extensions/diagnostics-template.md) ou a [extensão de diagnóstico do Azure do Linux](../virtual-machines/extensions/diagnostics-linux.md) na VM convidada. Um motivo comum para usar métricas de convidado, em vez de métricas de host, é que as métricas de convidado fornecem uma seleção maior de métricas do que as métricas de host. Um exemplo disso são as métricas de consumo de memória, que só estão disponíveis por meio de métricas de convidado. As métricas de host com suporte estão listadas [aqui](../azure-monitor/essentials/metrics-supported.md) e métricas de convidado comumente usadas estão listadas [aqui](../azure-monitor/autoscale/autoscale-common-metrics.md). Este artigo mostra como modificar o [modelo básico do conjunto de dimensionamento viável](virtual-machine-scale-sets-mvss-start.md) para usar regras de dimensionamento automático com base em métricas de convidado para conjuntos de dimensionamento do Linux.

## <a name="change-the-template-definition"></a>Alterar a definição do modelo

Em um [artigo anterior](virtual-machine-scale-sets-mvss-start.md), criamos um modelo de conjunto de dimensionamento básico. Agora, usaremos esse modelo anterior e o modificaremos para criar um modelo que implanta um conjunto de dimensionamento do Linux com dimensionamento automático baseado em métrica de convidado.

Primeiro, adicione parâmetros para `storageAccountName` e `storageAccountSasToken`. O agente de diagnóstico armazena dados de métrica em uma [tabela](../cosmos-db/tutorial-develop-table-dotnet.md) nesta conta de armazenamento. Do Agente de Diagnóstico do Linux versão 3.0 em diante, não há mais suporte para usar uma chave de acesso de armazenamento. Nesse caso, use um [Token SAS](../storage/common/storage-sas-overview.md).

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

Em seguida, modifique o conjunto de dimensionamento `extensionProfile` para incluir a extensão de diagnóstico. Nessa configuração, especifique a ID do recurso do conjunto de dimensionamento cujas métricas serão coletadas, bem como a conta de armazenamento e o token SAS a serem usados para armazenar as métricas. Especifique com que frequência as métricas são agregadas (neste caso, a cada minuto) e quais métricas devem ser acompanhadas (neste caso, o percentual de memória usada). Para obter informações mais detalhadas sobre essa configuração e métricas diferentes da porcentagem de memória usada, consulte [esta documentação](../virtual-machines/extensions/diagnostics-linux.md).

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

Finalmente, adicione um recurso `autoscaleSettings` para configurar o dimensionamento automático com base nessas métricas. Esse recurso tem uma cláusula `dependsOn` que faz referência a conjunto de dimensionamento para garantir que o conjunto de dimensionamento exista antes de tentar realizar seu dimensionamento automático. Se você escolher uma métrica diferente para fazer o dimensionamento automático, use o `counterSpecifier` da configuração de extensão de diagnóstico, como o `metricName` na configuração de dimensionamento automático. Para obter mais informações sobre a configuração de dimensionamento automático, consulte as [práticas recomendadas de dimensionamento automático](../azure-monitor/autoscale/autoscale-best-practices.md) e a [documentação de referência da API REST do Azure Monitor](/rest/api/monitor/autoscalesettings).

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
