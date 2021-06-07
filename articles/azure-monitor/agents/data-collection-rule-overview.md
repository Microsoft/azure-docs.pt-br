---
title: Regras de coleta de dados no Azure Monitor (versão prévia)
description: Visão geral das regras de coleta de dados (DCRs) em Azure Monitor incluindo seu conteúdo e estrutura e como você pode criar e trabalhar com elas.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2021
ms.openlocfilehash: a52d6dca423565e7b5e4b6ac059bcf21b637c87c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586328"
---
# <a name="data-collection-rules-in-azure-monitor-preview"></a>Regras de coleta de dados no Azure Monitor (versão prévia)
As regras de coleta de dados (DCR) definem os dados que chegam ao Azure Monitor e especificam onde esses dados devem ser enviados ou armazenados. Este artigo fornece uma visão geral das regras de coleta de dados, incluindo seu conteúdo e estrutura, e como você pode criar e trabalhar com elas.

## <a name="input-sources"></a>Fontes de entrada
Atualmente, as regras de coleta de dados dão suporte às seguintes fontes de entrada:

- Máquina virtual do Azure com o agente de Azure Monitor. Consulte [Configurar a coleta de dados para o agente de Azure monitor (versão prévia)](../agents/data-collection-rule-azure-monitor-agent.md).



## <a name="components-of-a-data-collection-rule"></a>Componentes de uma regra de coleta de dados
Uma regra de coleta de dados inclui os componentes a seguir.

| Componente | Descrição |
|:---|:---|
| Fontes de dados | Fonte exclusiva de dados de monitoramento com seu próprio formato e método para expor seus dados. Exemplos de uma fonte de dados incluem log de eventos do Windows, contadores de desempenho e syslog. Cada fonte de dados corresponde a um tipo de fonte de dados específico, conforme descrito abaixo. |
| Fluxos | Identificador exclusivo que descreve um conjunto de fontes de dados que serão transformadas e esquematizados como um tipo. Cada fonte de dados requer um ou mais fluxos, e um fluxo pode ser usado por várias fontes de dados. Todas as fontes de dados em um fluxo compartilham um esquema comum. Use vários fluxos, por exemplo, quando você quiser enviar uma fonte de dados específica para várias tabelas no mesmo espaço de trabalho de Log Analytics. |
| Destinos | Conjunto de destinos onde os dados devem ser enviados. Os exemplos incluem Log Analytics espaço de trabalho, métricas de Azure Monitor e hubs de eventos do Azure. | 
| Fluxos de dados | Definição de quais fluxos devem ser enviados para quais destinos. | 

O diagrama a seguir mostra os componentes de uma regra de coleta de dados e sua relação

[![Diagrama de DCR](media/data-collection-rule-overview/data-collection-rule-components.png)](media/data-collection-rule-overview/data-collection-rule-components.png#lightbox)

### <a name="data-source-types"></a>Tipos de fontes de dados
Cada fonte de dados tem um tipo de fonte de dados. Cada tipo define um conjunto exclusivo de propriedades que devem ser especificadas para cada fonte de dados. Os tipos de fonte de dados disponíveis atualmente são mostrados na tabela a seguir.

| Tipo de fonte de dados | Descrição | 
|:---|:---|
| extensão | Fonte de dados baseada em extensão de VM |
| performanceCounters | Contadores de desempenho para Windows e Linux |
| syslog | Eventos de syslog no Linux |
| windowsEventLogs | Log de eventos do Windows |


## <a name="limits"></a>Limites
Para os limites que se aplicam a cada regra de coleta de dados, consulte [limites de serviço Azure monitor](../service-limits.md#data-collection-rules).


## <a name="create-a-dcr"></a>Criar um DCR
No momento, você pode usar qualquer um dos seguintes métodos para criar um DCR:

- [Use o portal do Azure](../agents/data-collection-rule-azure-monitor-agent.md) para criar uma regra de coleta de dados e fazer com que ela seja associada a uma ou mais máquinas virtuais.
- Edite diretamente a regra de coleta de dados em JSON e [envie usando a API REST](/rest/api/monitor/datacollectionrules).
- Crie DCR e associações com [CLI do Azure](https://github.com/Azure/azure-cli-extensions/blob/master/src/monitor-control-service/README.md).
- Crie DCR e associações com Azure PowerShell.
  - [Get-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Get-AzDataCollectionRule.md)
  - [New-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/New-AzDataCollectionRule.md)
  - [Set-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Set-AzDataCollectionRule.md)
  - [Update-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Update-AzDataCollectionRule.md)
  - [Remove-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Remove-AzDataCollectionRule.md)
  - [Get-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Get-AzDataCollectionRuleAssociation.md)
  - [New-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/New-AzDataCollectionRuleAssociation.md)
  - [Remove-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Remove-AzDataCollectionRuleAssociation.md)

## <a name="sample-data-collection-rule"></a>Regra de coleta de dados de exemplo
A regra de coleta de dados de exemplo abaixo é para máquinas virtuais com o agente de gerenciamento do Azure e tem os seguintes detalhes:

- Dados de desempenho
  - Coleta contadores específicos de processador, memória, disco lógico e disco físico a cada 15 segundos e carrega-os a cada minuto.
  - Coleta contadores de processo específicos a cada 30 segundos e carrega a cada 5 minutos.
- Eventos do Windows
  - Coleta eventos de segurança do Windows e carregamentos a cada minuto.
  - Coleta eventos do sistema e de aplicativos do Windows e carregamentos a cada 5 minutos.
- Syslog
  - Coleta eventos de depuração, críticos e de emergência do recurso cron.
  - Coleta eventos de alerta, crítico e de emergência do recurso de syslog.
- Destinos
  - Envia todos os dados para um espaço de trabalho Log Analytics chamado centralWorkspace.

> [!NOTE]
> Para obter uma explicação de XPaths que são usados para especificar a coleta de eventos em regras de coleta de dados, consulte [limitar a coleta de dados com consultas XPath personalizadas](data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries)


```json
{
    "location": "eastus",
    "properties": {
      "dataSources": {
        "performanceCounters": [
          {
            "name": "cloudTeamCoreCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT1M",
            "samplingFrequencyInSeconds": 15,
            "counterSpecifiers": [
              "\\Processor(_Total)\\% Processor Time",
              "\\Memory\\Committed Bytes",
              "\\LogicalDisk(_Total)\\Free Megabytes",
              "\\PhysicalDisk(_Total)\\Avg. Disk Queue Length"
            ]
          },
          {
            "name": "appTeamExtraCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT5M",
            "samplingFrequencyInSeconds": 30,
            "counterSpecifiers": [
              "\\Process(_Total)\\Thread Count"
            ]
          }
        ],
        "windowsEventLogs": [
          {
            "name": "cloudSecurityTeamEvents",
            "streams": [
              "Microsoft-Event"
            ],
            "scheduledTransferPeriod": "PT1M",
            "xPathQueries": [
              "Security!*"
            ]
          },
          {
            "name": "appTeam1AppEvents",
            "streams": [
              "Microsoft-Event"
            ],
            "scheduledTransferPeriod": "PT5M",
            "xPathQueries": [
              "System!*[System[(Level = 1 or Level = 2 or Level = 3)]]",
              "Application!*[System[(Level = 1 or Level = 2 or Level = 3)]]"
            ]
          }
        ],
        "syslog": [
          {
            "name": "cronSyslog",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "cron"
            ],
            "logLevels": [
              "Debug",
              "Critical",
              "Emergency"
            ]
          },
          {
            "name": "syslogBase",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "syslog"
            ],
            "logLevels": [
              "Alert",
              "Critical",
              "Emergency"
            ]
          }
        ]
      },
      "destinations": {
        "logAnalytics": [
          {
            "workspaceResourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.OperationalInsights/workspaces/my-workspace",
            "name": "centralWorkspace"
          }
        ]
      },
      "dataFlows": [
        {
          "streams": [
            "Microsoft-Perf",
            "Microsoft-Syslog",
            "Microsoft-Event"
          ],
          "destinations": [
            "centralWorkspace"
          ]
        }
      ]
    }
  }
```


## <a name="next-steps"></a>Próximas etapas

- [Crie uma regra de coleta de dados](data-collection-rule-azure-monitor-agent.md) e uma associação a ela a partir de uma máquina virtual usando o agente de Azure monitor.
