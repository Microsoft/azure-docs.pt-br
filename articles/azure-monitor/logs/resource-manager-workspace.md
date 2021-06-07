---
title: Amostras de modelo do Resource Manager para workspaces do Log Analytics
description: Realize amostragem de modelos do Azure Resource Manager para implantar workspaces do Log Analytics e configurar fontes de dados co no Azure Monitor.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 05/18/2020
ms.openlocfilehash: 5c1ef7d8de32564e2b1d3b1578fcd72cefde0327
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047138"
---
# <a name="resource-manager-template-samples-for-log-analytics-workspaces-in-azure-monitor"></a>Amostras de modelo do Resource Manager para workspaces do Log Analytics no Azure Monitor
Este artigo inclui [modelos do Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) de amostra para criar e configurar workspaces do Log Analytics no Azure Monitor. Cada amostra inclui um arquivo de modelo e um arquivo de parâmetros com valores de amostra para fornecer ao modelo.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="template-references"></a>Referências de modelo

- [Microsoft.OperationalInsights workspaces](/azure/templates/microsoft.operationalinsights/2020-03-01-preview/workspaces
) 
- [Microsoft.OperationalInsights workspaces/dataSources](/azure/templates/microsoft.operationalinsights/2020-03-01-preview/workspaces/datasources)

## <a name="create-a-log-analytics-workspace"></a>Criar um espaço de trabalho do Log Analytics
A amostra a seguir cria um workspace do Log Analytics vazio.

### <a name="notes"></a>Observações

- Se você especificar um tipo de preço **Gratuito**, remova o elemento **retentionInDays**.

### <a name="template-file"></a>Arquivo de modelo

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the workspace."
          }
      },
      "sku": {
          "type": "string",
          "allowedValues": [
            "pergb2018",
            "Free",
            "Standalone",
            "PerNode",
            "Standard",
            "Premium"
            ],
          "defaultValue": "pergb2018",
          "metadata": {
          "description": "Pricing tier: PerGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
          }
        },
        "location": {
          "type": "string",
          "allowedValues": [
          "australiacentral", 
          "australiaeast", 
          "australiasoutheast", 
          "brazilsouth",
          "canadacentral", 
          "centralindia", 
          "centralus", 
          "eastasia", 
          "eastus", 
          "eastus2", 
          "francecentral", 
          "japaneast", 
          "koreacentral", 
          "northcentralus", 
          "northeurope", 
          "southafricanorth", 
          "southcentralus", 
          "southeastasia",
          "switzerlandnorth",
          "switzerlandwest",
          "uksouth", 
          "ukwest", 
          "westcentralus", 
          "westeurope", 
          "westus", 
          "westus2" 
          ],
          "metadata": {
              "description": "Specifies the location for the workspace."
              }
        },
        "retentionInDays": {
          "type": "int",
          "defaultValue": 120,
          "metadata": {
            "description": "Number of days to retain data."
          }
        },
        "resourcePermissions": {
          "type": "bool",
          "metadata": {
            "description": "true to use resource or workspace permissions. false to require workspace permissions."
          }
      }

      },
      "resources": [
      {
          "type": "Microsoft.OperationalInsights/workspaces",
          "name": "[parameters('workspaceName')]",
          "apiVersion": "2020-08-01",
          "location": "[parameters('location')]",
          "properties": {
              "sku": {
                  "name": "[parameters('sku')]"
              },
              "retentionInDays": "[parameters('retentionInDays')]",
              "features": {
                  "searchVersion": 1,
                  "legacy": 0,
                  "enableLogAccessUsingOnlyResourcePermissions": "[parameters('resourcePermissions')]"
              }
          }
      }
  ]
}
```

### <a name="parameter-file"></a>Arquivo de parâmetro.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "sku": {
      "value": "pergb2018"
    },
    "location": {
      "value": "eastus"
    },
    "resourcePermissions": {
      "value": true
    }
  }
}
```

## <a name="collect-windows-events"></a>Coletar eventos do Windows
A amostra a seguir adiciona a coleção de [eventos do Windows](../agents/data-sources-windows-events.md) a um workspace existente.

### <a name="notes"></a>Observações

- Adicione um elemento **datasources** para cada log de eventos a ser coletado. Você pode especificar um conjunto diferente de tipos de evento para cada log.

### <a name="template-file"></a>Arquivo de modelo

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string"
      },
      "location": {
        "type": "string"
      }
  },
  "resources": [
  {
      "type": "Microsoft.OperationalInsights/workspaces",
      "apiVersion": "2020-08-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
          "type": "datasources",
          "apiVersion": "2020-08-01",
          "name": "WindowsEventsSystem",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsEvent",
          "properties": {
            "eventLogName": "System",
            "eventTypes": [
              {
                "eventType": "Error"
              },
              {
                "eventType": "Warning"
              }
            ]
          }
        },
        {
          "type": "datasources",
          "apiVersion": "2020-08-01",
          "name": "WindowsEventsApplication",
          "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
          ],
          "kind": "WindowsEvent",
          "properties": {
            "eventLogName": "Application",
            "eventTypes": [
              {
                "eventType": "Error"
              },
              {
                "eventType": "Warning"
              },
              {
                "eventType": "Information"
              }
            ]
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
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```

## <a name="collect-syslog"></a>Coletar syslog
A amostra a seguir adiciona a coleção de [eventos do syslog](../agents/data-sources-syslog.md) a um workspace existente.

### <a name="notes"></a>Observações

- Adicione um elemento **datasources** para cada recurso a ser coletado. Você pode especificar um conjunto diferente de severidades para cada recurso.

### <a name="template-file"></a>Arquivo de modelo

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the workspace."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
    },
    "resources": [
    {
        "apiVersion": "2020-08-01",
        "type": "Microsoft.OperationalInsights/workspaces",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "resources": [
            {
                "type": "datasources",
                "apiVersion": "2020-08-01",
                "name": "SyslogKern",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxSyslog",
                "properties": {
                    "syslogName": "kern",
                    "syslogSeverities": [
                        {
                            "severity": "emerg"
                        },
                        {
                            "severity": "alert"
                        },
                        {
                            "severity": "crit"
                        },
                        {
                            "severity": "err"
                        },
                        {
                            "severity": "warning"
                        },
                        {
                            "severity": "notice"
                        },
                        {
                            "severity": "info"
                        },
                        {
                            "severity": "debug"
                        }
                    ]
                }
            },
            {
                "type": "datasources",
                "apiVersion": "2020-08-01",
                "name": "SyslogDaemon",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxSyslog",
                "properties": {
                    "syslogName": "daemon",
                    "syslogSeverities": [
                        {
                            "severity": "emerg"
                        },
                        {
                            "severity": "alert"
                        },
                        {
                            "severity": "crit"
                        },
                        {
                            "severity": "err"
                        },
                        {
                            "severity": "warning"
                        }
                    ]
                }
            },
            {
                "apiVersion": "2020-08-01",
                "type": "datasources",
                "name": "SyslogCollection",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxSyslogCollection",
                "properties": {
                    "state": "Enabled"
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
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```

## <a name="collect-windows-performance-counters"></a>Coletar contadores de desempenho do Windows
A amostra a seguir adiciona a coleção de [contadores de desempenho do Windows](../agents/data-sources-performance-counters.md) a um workspace existente.

### <a name="notes"></a>Observações

- Adicione um elemento **datasources** para cada contador e instância a serem coletados. Você pode especificar uma taxa de coleta diferente para cada combinação de contador e instância.
  
### <a name="template-file"></a>Arquivo de modelo

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the workspace."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
              "description": "Location of the workspace."
            }
        }
    },
    "resources": [
    {
        "apiVersion": "2020-08-01",
        "type": "Microsoft.OperationalInsights/workspaces",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "resources": [
          {
            "apiVersion": "2020-08-01",
            "type": "datasources",
            "name": "WindowsPerfMemoryAvailableBytes",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "kind": "WindowsPerformanceCounter",
            "properties": {
              "objectName": "Memory",
              "instanceName": "*",
              "intervalSeconds": 10,
              "counterName": "Available MBytes "
            }
          },
          {
            "apiVersion": "2020-08-01",
            "type": "datasources",
            "name": "WindowsPerfMemoryPercentageBytes",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "kind": "WindowsPerformanceCounter",
            "properties": {
              "objectName": "Memory",
              "instanceName": "*",
              "intervalSeconds": 10,
              "counterName": "% Committed Bytes in Use"
            }
          },
          {
            "apiVersion": "2020-08-01",
            "type": "datasources",
            "name": "WindowsPerfProcessorPercentage",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
            ],
            "kind": "WindowsPerformanceCounter",
            "properties": {
              "objectName": "Processor",
              "instanceName": "_Total",
              "intervalSeconds": 10,
              "counterName": "% Processor Time"
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
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```


## <a name="collect-linux-performance-counters"></a>Coletar contadores de desempenho do Linux
A amostra a seguir adiciona a coleção de [contadores de desempenho do Linux](../agents/data-sources-performance-counters.md) a um workspace existente.

### <a name="notes"></a>Observações

- Adicione um elemento **datasources** para cada objeto e instância a serem coletados. Você pode especificar um conjunto diferente de contadores para cada combinação de objeto e instância, mas só pode especificar uma taxa para todos os contadores.
  
### <a name="template-file"></a>Arquivo de modelo

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
              "description": "Name of the workspace."
            }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Specifies the location in which to create the workspace."
          }
        }
    },
    "resources": [
    {
        "apiVersion": "2020-08-01",
        "type": "Microsoft.OperationalInsights/workspaces",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "resources": [
            {
                "apiVersion": "2020-08-01",
                "type": "datasources",
                "name": "LinuxPerformanceLogicalDisk",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxPerformanceObject",
                "properties": {
                    "objectName": "Logical Disk",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "performanceCounters": [
                        {
                            "counterName": "% Used Inodes"
                        },
                        {
                            "counterName": "Free Megabytes"
                        },
                        {
                            "counterName": "% Used Space"
                        },
                        {
                            "counterName": "Disk Transfers/sec"
                        },
                        {
                            "counterName": "Disk Reads/sec"
                        },
                        {
                            "counterName": "Disk Writes/sec"
                        }
                    ]
                }
            },
            {
                "apiVersion": "2020-08-01",
                "type": "datasources",
                "name": "LinuxPerformanceProcessor",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "LinuxPerformanceObject",
                "properties": {
                    "objectName": "Processor",
                    "instanceName": "*",
                    "intervalSeconds": 10,
                    "performanceCounters": [
                        {
                            "counterName": "% Processor Time"
                        },
                        {
                            "counterName": "% Privileged Time"
                        }
                    ]
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
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```


## <a name="collect-custom-logs"></a>Coletar logs personalizados
A amostra a seguir adiciona a coleção de [logs personalizados](../agents/data-sources-custom-logs.md) a um workspace existente.

### <a name="notes"></a>Observações

- A configuração de delimitadores e extrações pode ser complexa. Para obter ajuda, você pode definir um log personalizado usando o portal do Azure e recuperar sua configuração usando [Get-AzOperationalInsightsDataSource](/powershell/module/az.operationalinsights/get-azoperationalinsightsdatasource) com **-Kind** definido como **CustomLog**.

  
### <a name="template-file"></a>Arquivo de modelo

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "workspaceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the workspace."
          }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Specifies the location in which to create the workspace."
        }
      }
  },
  "resources": [
  {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.OperationalInsights/workspaces",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
            "apiVersion": "2020-08-01",
            "type": "dataSources",
            "name": "[concat(parameters('workspaceName'), 'armlog_timedelimited')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', '/', parameters('workspaceName'))]"
            ],
            "kind": "CustomLog",
            "properties": {
                "customLogName": "arm_log_timedelimited",
                "description": "this is a description",
                "inputs": [
                  {
                      "location": {
                        "fileSystemLocations": {
                            "linuxFileTypeLogPaths": [ "/var/logs" ],
                            "windowsFileTypeLogPaths": ["c:\\Windows\\Logs\\*.txt"]
                        }
                      },
                      "recordDelimiter": {
                        "regexDelimiter": {
                          "matchIndex": 0,
                          "numberdGroup": null,
                          "pattern": "(^.*((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9].*$)"
                        }
                      }
                  }
                ],
                "extractions": [
                {
                    "extractionName": "TimeGenerated",
                    "extractionProperties": {
                    "dateTimeExtraction": {
                        "regex": [
                          {
                              "matchIndex": 0,
                              "numberdGroup": null,
                              "pattern": "((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]"
                          }
                        ]
                    }
                    },
                    "extractionType": "DateTime"
                }
                ]
            }
        },
        {
          "apiVersion": "2020-08-01",
          "type": "dataSources",
          "name": "[concat(parameters('workspaceName'), 'armlog_newline')]",
          "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', '/', parameters('workspaceName'))]"
          ],
          "kind": "CustomLog",
          "properties": {
              "customLogName": "armlog_newline",
              "description": "this is a description",
              "inputs": [
                {
                    "location": {
                      "fileSystemLocations": {
                          "linuxFileTypeLogPaths": [ "/var/logs" ],
                          "windowsFileTypeLogPaths": ["c:\\Windows\\Logs\\*.txt"]
                      }
                    },
                    "recordDelimiter": {
                      "regexDelimiter": {
                        "pattern": "\\n",
                        "matchIndex": 0,
                        "numberdGroup": null
                      }
                    }
                }
              ],
              "extractions": [
                {
                  "extractionName": "TimeGenerated",
                  "extractionType": "DateTime",
                  "extractionProperties": {
                    "dateTimeExtraction": {
                        "regex": null,
                        "joinStringRegex": null
                    }
                  }
                }
              ]
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
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```


## <a name="collect-iis-log"></a>Coletar log do IIS
A amostra a seguir adiciona a coleção de [logs do IIS](../agents/data-sources-iis-logs.md) a um workspace existente.

### <a name="template-file"></a>Arquivo de modelo

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
              "description": "Name of the workspace."
            }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Specifies the location in which to create the workspace."
          }
        }
    },
    "resources": [
    {
        "type": "Microsoft.OperationalInsights/workspaces",
        "apiVersion": "2020-08-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "resources": [
            {
                "apiVersion": "2020-08-01",
                "type": "datasources",
                "name": "IISLog",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "kind": "IISLogs",
                "properties": {
                    "state": "OnPremiseEnabled"
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
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "value": "MyWorkspace"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```



## <a name="next-steps"></a>Próximas etapas

* [Obtenha outros modelos de amostra do Azure Monitor](../resource-manager-samples.md).
* [Saiba mais sobre os workspaces do Log Analytics](./quick-create-workspace.md).
* [Saiba mais sobre as fontes de dados do agente](../agents/agent-data-sources.md).