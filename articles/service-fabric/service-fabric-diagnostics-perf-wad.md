---
title: Azure Service Fabric – monitoramento de desempenho com a extensão de Diagnóstico do Windows Azure|Microsoft Docs
description: Use o Diagnóstico do Microsoft Azure para coletar contadores de desempenho para os clusters do Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 20fa8945f01a3431d2fd78d545c43d6215c83f56
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "66110296"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Monitoramento de desempenho com a extensão de Diagnóstico do Microsoft Azure do Windows

Este documento aborda as etapas necessárias para configurar a coleta de contadores de desempenho por meio da extensão WAD (Diagnóstico do Microsoft Azure) para clusters do Windows. Para clusters do Linux, configure o [agente do Log Analytics](service-fabric-diagnostics-oms-agent.md) para coletar contadores de desempenho para seus nós. 

 > [!NOTE]
> A extensão WAD deve ser implantada em seu cluster para que essas etapas funcionem para você. Se não estiver configurado, vá até [Coleta e agregação de eventos utilizando o Diagnóstico do Microsoft Azure do Windows](service-fabric-diagnostics-event-aggregation-wad.md).  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="collect-performance-counters-via-the-wadcfg"></a>Coletar contadores de desempenho por meio do WadCfg

Para coletar os contadores de desempenho por meio do WAD, você precisa modificar a configuração adequadamente no modelo do Resource Manager do cluster. Siga estas etapas para adicionar um contador de desempenho que você deseja coletar ao modelo e executar uma atualização de recurso do Resource Manager.

1. Localize a configuração WAD no modelo do seu cluster – localize `WadCfg`. Você adicionará contadores de desempenho para coletar sob a `DiagnosticMonitorConfiguration`.

2. Defina sua configuração para coletar os contadores de desempenho adicionando a seção a seguir para sua `DiagnosticMonitorConfiguration`. 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    A `scheduledTransferPeriod` define a frequência com que os valores dos contadores que são coletados são transferidos para a tabela de armazenamento do Azure e para qualquer coletor configurado. 

3. Adicione os contadores de desempenho que você deseja coletar à `PerformanceCounterConfiguration` que foi declarada na etapa anterior. Cada contador que você gostaria de coletar é definido com um `counterSpecifier`, `sampleRate`, `unit`, `annotation` e quaisquer `sinks` relevantes.

Aqui está um exemplo de configuração com o contador para o *Tempo Total do Processador* (o período de tempo do CPU em uso para operações de processamento) e *Invocações de Método de Ator do Service Fabric por segundo*, um dos contadores de desempenho personalizado do Service Fabric. Consulte [Contadores de Desempenho de Ator Confiável](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) e [Contadores de Desempenho de Serviço Confiáveis](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) para obter uma lista completa de contadores de desempenho personalizada do Service Fabric.

 ```json
 "WadCfg": {
         "DiagnosticMonitorConfiguration": {
           "overallQuotaInMB": "50000",
           "EtwProviders": {
             "EtwEventSourceProviderConfiguration": [
               {
                 "provider": "Microsoft-ServiceFabric-Actors",
                 "scheduledTransferKeywordFilter": "1",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableActorEventTable"
                 }
               },
               {
                 "provider": "Microsoft-ServiceFabric-Services",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableServiceEventTable"
                 }
               }
             ],
             "EtwManifestProviderConfiguration": [
               {
                 "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                 "scheduledTransferLogLevelFilter": "Information",
                 "scheduledTransferKeywordFilter": "4611686018427387904",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricSystemEventTable"
                 }
               }
             ]
           },
           "PerformanceCounters": {
                 "scheduledTransferPeriod": "PT1M",
                 "PerformanceCounterConfiguration": [
                     {
                         "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                         "sampleRate": "PT1M",
                         "unit": "Percent",
                         "annotation": [
                         ],
                         "sinks": ""
                     },
                     {
                         "counterSpecifier": "\\Service Fabric Actor Method(*)\\Invocations/Sec",
                         "sampleRate": "PT1M",
                     }
                 ]
             }
         }
       },
  ```

 A taxa de amostragem do contador pode ser modificada de acordo com suas necessidades. O formato para ela é `PT<time><unit>`, portanto, se você quiser que o contador seja coletado a cada segundo, você deverá definir a `"sampleRate": "PT15S"`.

 Você também pode usar variáveis em seu modelo do ARM para coletar uma matriz de contadores de desempenho, que pode ser útil quando você coleta contadores de desempenho por processo. No exemplo abaixo, estamos coletando de tempo do processador e tempo do coletor de lixo por processo e, em seguida, 2 de contadores de desempenho sobre os próprios nós usando todas as variáveis. 

 ```json
"variables": {
  "copy": [
      {
        "name": "processorTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\Process([parameters('monitoredProcesses')[copyIndex('processorTimeCounters')]])\\% Processor Time",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('processorTimeCounters')],' Processor Time')]",
              "locale": "en-us"
            }
          ]
        }
      },
      {
        "name": "gcTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\.NET CLR Memory([parameters('monitoredProcesses')[copyIndex('gcTimeCounters')]])\\% Time in GC",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('gcTimeCounters')],' Time in GC')]",
              "locale": "en-us"
            }
          ]
        }
      }
    ],
    "machineCounters": [
      {
        "counterSpecifier": "\\Memory\\Available Bytes",
        "sampleRate": "PT1M",
        "unit": "KB",
        "sinks": "applicationInsights",
        "annotation": [
          {
            "displayName": "Memory Available Kb",
            "locale": "en-us"
          }
        ]
      },
      {
        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
        "sampleRate": "PT15S",
        "unit": "percent",
        "annotation": [
          {
            "displayName": "Memory usage",
            "locale": "en-us"
          }
        ]
      }
    ]
  }
....
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": "50000",
      "Metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', variables('vmNodeTypeApp2Name'))]"
      },
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": "[concat(variables ('processorTimeCounters'), variables('gcTimeCounters'),  variables('machineCounters'))]"
      },
....
```

 >[!NOTE]
 >Embora você possa usar `*` para especificar grupos de contadores de desempenho que são nomeados de maneira similar, enviar todos os contadores por meio de um coletor (para o Application Insights) exige que eles sejam declarados individualmente. 

1. Ao adicionar os contadores de desempenho apropriados que precisam ser coletados, você precisará atualizar o recurso de cluster para que essas alterações sejam refletidas no seu cluster em execução. Salve a `template.json` modificada e abra o PowerShell. Você pode atualizar o cluster usando `New-AzResourceGroupDeployment`. A chamada requer o nome do grupo de recursos, o arquivo de modelo atualizado e o arquivo de parâmetros e solicita que o Resource Manager faça as alterações apropriadas aos recursos que você atualizou. Após você entrar em sua conta e estar na assinatura correta, use o seguinte comando para executar a atualização:

    ```sh
    New-AzResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

1. Quando a atualização terminar de ser executada (leva de 15 a 45 minutos, dependendo da primeira implantação e do tamanho do grupo de recursos), o WAD deve coletar os contadores de desempenho e enviá-los para a tabela denominada WADPerformanceCountersTable na conta de armazenamento associada seu cluster. Consulte seus contadores de desempenho no Application Insights ao [adicionar o coletor de IA ao modelo do Gerenciador de Recursos](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Próximas etapas
* Colete mais contadores de desempenho para o cluster. Consulte [Métricas de desempenho](service-fabric-diagnostics-event-generation-perf.md) para obter uma lista de contadores que você deve coletar.
* [Use monitoramento e diagnóstico com uma VM Windows e modelos do Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md) para fazer outras modificações em seu `WadCfg`, incluindo a configuração de contas de armazenamento adicionais para as quais enviar dados de diagnóstico.
* Visite o [WadCfg Builder](https://azure.github.io/azure-diagnostics-tools/config-builder/) para criar um modelo do zero e verifique se a sintaxe está correta. (https://azure.github.io/azure-diagnostics-tools/config-builder/) para criar um modelo do zero e verificar se a sintaxe está correta.