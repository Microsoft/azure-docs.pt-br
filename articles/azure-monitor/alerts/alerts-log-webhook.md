---
title: Ações de webhook para alertas de log nos alertas do Azure
description: Descreve como configurar um envio de alertas de log com a ação de webhook e as personalizações disponíveis
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 228193066c45421c4dddee1802aba1feed59e9c8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042667"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Ações de webhook para regras de alerta do log

O [alerta de log](alerts-log.md) dá suporte à configuração de grupos de ação de [webhook](./action-groups.md#webhook). Neste artigo, descreveremos quais propriedades estão disponíveis e como configurar um webhook JSON personalizado.

> [!NOTE]
> Atualmente, não há suporte para webhook personalizado com base em JSON na versão da API `2020-05-01-preview`

> [!NOTE]
> É recomendável que você use o [esquema de alerta comum](../alerts/alerts-common-schema.md) para suas integrações de webhook. O esquema de alerta comum fornece a vantagem de ter uma única carga de alerta extensível e unificada em todos os serviços de alerta no Azure Monitor. Para regras de alertas de log que têm uma carga JSON personalizada definida, habilitar o esquema comum reverte o esquema de carga para o descrito [aqui](../alerts/alerts-common-schema-definitions.md#log-alerts). Os alertas com o esquema comum habilitado têm um limite de tamanho superior de 256 KB por alerta, um alerta maior não incluirá os resultados da pesquisa. Quando os resultados da pesquisa não são incluídos, você deve usar o `LinkToFilteredSearchResultsAPI` ou o `LinkToSearchResultsAPI` para acessar os resultados da consulta por meio da API log Analytics.

## <a name="webhook-payload-properties"></a>Propriedades do conteúdo do webhook

Ações de webhook permitem invocar uma única solicitação HTTP POST. O serviço chamado deve dar suporte a WebHooks e saber como usar a carga que recebe.

Propriedades de ação de webhook padrão e seus nomes de parâmetro JSON personalizados:

| Parâmetro | Variável | Descrição |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |Nome da regra de alerta. |
| *Gravidade* |#severity |Severidade definida para o alerta do log disparado. |
| *AlertThresholdOperator* |#thresholdoperator |O operador de limite da regra de alerta. |
| *AlertThresholdValue* |#thresholdvalue |O valor de limite para a regra de alerta. |
| *LinkToSearchResults* |#linktosearchresults |Link para o portal de análise que retorna os registros da consulta que criou o alerta. |
| *LinkToSearchResultsAPI* |#linktosearchresultsapi |Link para a API de análise que retorna os registros da consulta que criou o alerta. |
| *LinkToFilteredSearchResultsUI* |#linktofilteredsearchresultsui |Link para o portal de análise que retorna os registros da consulta filtrada por combinações de valor de dimensões que criaram o alerta. |
| *LinkToFilteredSearchResultsAPI* |#linktofilteredsearchresultsapi |Link para a API de análise que retorna os registros da consulta filtrada por combinações de valores de dimensões que criaram o alerta. |
| *ResultCount* |#searchresultcount |Número de registros nos resultados da pesquisa. |
| *Hora de término do intervalo de pesquisa* |#searchintervalendtimeutc |Hora de término da consulta em UTC, com o formato mm/dd/aaaa HH: mm: ss AM/PM. |
| *Intervalo de pesquisa* |#searchinterval |Janela de tempo para a regra de alerta, com o formato HH: mm: SS. |
| *Hora de início do intervalo de pesquisa* |#searchintervalstarttimeutc |Hora de início da consulta em UTC, com o formato mm/dd/aaaa HH: mm: ss AM/PM. 
| *SearchQuery* |#searchquery |A consulta da pesquisa de log usada pela regra de alerta. |
| *SearchResults* |"IncludeSearchResults": true|Registros retornados pela consulta como uma tabela JSON, limitados aos primeiros 1.000 registros. "IncludeSearchResults": true é adicionado em uma definição personalizada de webhook JSON como uma propriedade de nível superior. |
| *Dimensões* |"IncludeDimensions": verdadeiro|Combinações de valores de dimensões que dispararam esse alerta como uma seção JSON. "IncludeDimensions": true é adicionado em uma definição personalizada de webhook JSON como uma propriedade de nível superior. |
| *Tipo de alerta*| #alerttype | O tipo de regra de alerta de log configurada como [medida métrica ou número de resultados](./alerts-unified-log.md#measure).|
| *WorkspaceID* |#workspaceid |ID do seu espaço de trabalho do Log Analytics. |
| *ID do Aplicativo* |#applicationid |ID do seu aplicativo Application Insights. |
| *ID da assinatura* |#subscriptionid |ID da sua assinatura do Azure usada. |

## <a name="custom-webhook-payload-definition"></a>Definição de conteúdo do webhook personalizado

Você pode usar o **conteúdo JSON personalizado de inclusão para webhook** para obter uma carga JSON personalizada usando os parâmetros acima. Você também pode gerar propriedades adicionais.
Por exemplo, você pode especificar a seguinte carga personalizada que inclui um único parâmetro chamado *text*. O serviço que este webhook chama espera esse parâmetro:

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Esse conteúdo de exemplo é resolvido para algo semelhante ao seguinte quando é enviado para o webhook:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
As variáveis em um webhook personalizado devem ser especificadas em um compartimento JSON. Por exemplo, fazer referência a "#searchresultcount" no exemplo de webhook acima produzirá com base nos resultados do alerta.

Para incluir os resultados da pesquisa, adicione **IncludeSearchResults** como uma propriedade de nível superior no JSON personalizado. Os resultados da pesquisa são incluídos como uma estrutura JSON, de modo que os resultados não podem ser referenciados em campos definidos personalizados. 

> [!NOTE]
> O botão **Exibir webhook** ao lado da opção **incluir conteúdo JSON personalizado para webhook** exibe a visualização do que foi fornecido. Ele não contém dados reais, mas representa o esquema JSON que será usado. 

## <a name="sample-payloads"></a>Cargas de exemplo
Esta seção mostra as cargas de exemplo para WebHooks para alertas de log. Os conteúdos de exemplo incluem exemplos quando a carga é padrão e quando é personalizada.

### <a name="log-alert-for-log-analytics"></a>Alerta de log para Log Analytics
A seguinte carga de exemplo é para uma ação de webhook padrão que é usada para alertas com base em Log Analytics:

> [!NOTE]
> O valor do campo "Severity" será alterado se você tiver [alternado para a API scheduledQueryRules atual](../alerts/alerts-log-api-switch.md) da [API de alerta do log Analytics herdado](./api-alerts.md).

```json
{
    "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
    "AlertRuleName": "AcmeRule",
    "SearchQuery": "Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToFilteredSearchResultsUI": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "LinkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "Description": "log alert rule",
    "Severity": "Warning",
    "AffectedConfigurationItems": [
        "INC-Gen2Alert"
    ],
    "Dimensions": [
        {
            "name": "Computer",
            "value": "INC-Gen2Alert"
        }
    ],
    "SearchResult": {
        "tables": [
            {
                "name": "PrimaryResult",
                "columns": [
                    {
                        "name": "$table",
                        "type": "string"
                    },
                    {
                        "name": "Computer",
                        "type": "string"
                    },
                    {
                        "name": "TimeGenerated",
                        "type": "datetime"
                    }
                ],
                "rows": [
                    [
                        "Fabrikam",
                        "33446677a",
                        "2018-02-02T15:03:12.18Z"
                    ],
                    [
                        "Contoso",
                        "33445566b",
                        "2018-02-02T15:16:53.932Z"
                    ]
                ]
            }
        ]
    },
    "WorkspaceId": "12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
}
```

### <a name="log-alert-for-application-insights"></a>Alerta de log para Application Insights
A seguinte carga de exemplo é para um webhook padrão quando ele é usado para alertas de log com base em recursos de Application Insights:
    
```json
{
    "schemaId": "Microsoft.Insights/LogAlert",
    "data": {
        "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
        "AlertRuleName": "AcmeRule",
        "SearchQuery": "requests | where resultCode == \"500\" | summarize AggregatedValue = Count by bin(Timestamp, 5m), IP",
        "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
        "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
        "AlertThresholdOperator": "Greater Than",
        "AlertThresholdValue": 0,
        "ResultCount": 2,
        "SearchIntervalInSeconds": 3600,
        "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToFilteredSearchResultsUI": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "LinkToFilteredSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "Description": null,
        "Severity": "3",
        "Dimensions": [
            {
                "name": "IP",
                "value": "1.1.1.1"
            }
        ],
        "SearchResult": {
            "tables": [
                {
                    "name": "PrimaryResult",
                    "columns": [
                        {
                            "name": "$table",
                            "type": "string"
                        },
                        {
                            "name": "Id",
                            "type": "string"
                        },
                        {
                            "name": "Timestamp",
                            "type": "datetime"
                        }
                    ],
                    "rows": [
                        [
                            "Fabrikam",
                            "33446677a",
                            "2018-02-02T15:03:12.18Z"
                        ],
                        [
                            "Contoso",
                            "33445566b",
                            "2018-02-02T15:16:53.932Z"
                        ]
                    ]
                }
            ]
        },
        "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
        "AlertType": "Metric measurement"
    }
}
```

### <a name="log-alert-for-other-resources-logs-from-api-version-2020-05-01-preview"></a>Alerta de log para outros logs de recursos (da versão da API `2020-05-01-preview` )

> [!NOTE]
> No momento, não há encargos adicionais para a versão da API `2020-05-01-preview` e alertas de log centrados no recurso.  Os preços para os recursos que estão na versão prévia serão anunciados no futuro e um aviso fornecido antes do início da cobrança. Se você optar por continuar usando a nova versão da API e alertas de log centrados no recurso após o período de aviso, você será cobrado na taxa aplicável.

O seguinte conteúdo de exemplo é para um webhook padrão quando é usado para alertas de log com base em outros logs de recursos (exceto espaços de trabalho e Application Insights):

```json
{
    "schemaId": "azureMonitorCommonAlertSchema",
    "data": {
        "essentials": {
            "alertId": "/subscriptions/12345a-1234b-123c-123d-12345678e/providers/Microsoft.AlertsManagement/alerts/12345a-1234b-123c-123d-12345678e",
            "alertRule": "AcmeRule",
            "severity": "Sev4",
            "signalType": "Log",
            "monitorCondition": "Fired",
            "monitoringService": "Log Alerts V2",
            "alertTargetIDs": [
                "/subscriptions/12345a-1234b-123c-123d-12345678e/resourcegroups/ai-engineering/providers/microsoft.compute/virtualmachines/testvm"
            ],
            "originAlertId": "123c123d-1a23-1bf3-ba1d-dd1234ff5a67",
            "firedDateTime": "2020-07-09T14:04:49.99645Z",
            "description": "log alert rule V2",
            "essentialsVersion": "1.0",
            "alertContextVersion": "1.0"
        },
        "alertContext": {
            "properties": null,
            "conditionType": "LogQueryCriteria",
            "condition": {
                "windowSize": "PT10M",
                "allOf": [
                    {
                        "searchQuery": "Heartbeat",
                        "metricMeasure": null,
                        "targetResourceTypes": "['Microsoft.Compute/virtualMachines']",
                        "operator": "LowerThan",
                        "threshold": "1",
                        "timeAggregation": "Count",
                        "dimensions": [
                            {
                                "name": "ResourceId",
                                "value": "/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm"
                            }
                        ],
                        "metricValue": 0.0,
                        "failingPeriods": {
                            "numberOfEvaluationPeriods": 1,
                            "minFailingPeriodsToAlert": 1
                        },
                        "linkToSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z"
                    }
                ],
                "windowStartTime": "2020-07-07T13:54:34Z",
                "windowEndTime": "2020-07-09T13:54:34Z"
            }
        }
    }
}
```

### <a name="log-alert-with-a-custom-json-payload"></a>Alerta de log com uma carga JSON personalizada
Por exemplo, para criar uma carga personalizada que inclua apenas o nome do alerta e os resultados da pesquisa, use esta configuração: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

A seguinte carga de exemplo é para uma ação personalizada de webhook para qualquer alerta de log:
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre os [alertas de log nos alertas do Azure](./alerts-unified-log.md).
- Entenda como [Gerenciar alertas de log no Azure](alerts-log.md).
- Crie e gerencie [grupos de ações no Azure](./action-groups.md).
- Saiba mais sobre o [Application Insights](../logs/log-query-overview.md).
- Saiba mais sobre [consultas de log](../logs/log-query-overview.md).