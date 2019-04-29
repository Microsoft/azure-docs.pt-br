---
title: Processar regras de limite configurável com base no Azure Stream Analytics
description: Este artigo descreve como usar dados de referência para obter uma solução de alerta com regras de limite configurável com base no Azure Stream Analytics.
services: stream-analytics
author: rockboyfor
ms.author: v-yeche
manager: digimobile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
origin.date: 04/30/2018
ms.date: 08/20/2018
ms.openlocfilehash: ce2cf6ebdfd74549114e94e4c7356e387576d3c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761719"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Processar regras de limite configurável com base no Azure Stream Analytics
Este artigo descreve como usar dados de referência para obter uma solução de alerta que usa regras de limite configurável com base no Azure Stream Analytics.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Cenário: Alertas com base nos limites ajustáveis regra
Talvez seja necessário gerar um alerta como saída quando eventos de fluxo de entrada atingirem um determinado valor, ou quando um valor agregado com base em eventos de fluxo de entrada exceder um certo limite. É simples configurar uma consulta do Stream Analytics, que tem o valor comparado com um limite estático, fixo e predeterminado. Um limite fixo pode ser codificado na sintaxe de consulta de streaming usando comparações numéricas simples (maior que, menor que e de igualdade).

Em alguns casos, os valores de limite precisam ser mais facilmente configuráveis sem a edição da sintaxe de consulta cada vez que um valor de limite é alterado. Em outros casos, talvez você precise de vários dispositivos ou usuários processados na mesma consulta com cada um deles com valores de limite diferentes em cada tipo de dispositivo. 

Esse padrão pode ser usado para configurar dinamicamente os limites, escolha seletivamente a que tipo de dispositivo o limite se aplica ao filtrar os dados de entrada e escolha seletivamente quais campos serão incluídos na saída.

## <a name="recommended-design-pattern"></a>Padrão de design recomendado
Use uma entrada de dados de referência para um trabalho do Stream Analytics, como uma pesquisa dos limites de alerta:
- Armazene os valores do limite nos dados de referência, um valor por chave.
- Una os eventos de entrada de dados de streaming aos dados de referência na coluna de chave.
- Use o valor de chave de dados de referência como o valor do limite.

## <a name="example-data-and-query"></a>Consulta e dados de exemplo
No exemplo, os alertas são gerados quando a agregação de streaming de dados em dispositivos em uma janela de um minuto corresponde aos valores estipulados na regra fornecidos como dados de referência.

Na consulta, para cada deviceId, e cada metricName em deviceId, você pode configurar de 0 a 5 dimensões como GROUP BY. Somente os eventos com os valores de filtro correspondente são agrupados. Quando agrupadas, as agregações em janelas de Mín, Máx, Med são calculadas em uma janela em cascata de 60 segundos. Os filtros nos valores agregados, então, são calculados de acordo com o limite configurado na referência para gerar o evento de alerta de saída.

Por exemplo, suponha que haja um trabalho do Stream Analytics com uma entrada de dados de referência denominada **regras**, e uma entrada de dados de streaming denominada **métricas**. 

## <a name="reference-data"></a>Dados de referência
Estes dados de referência de exemplo mostram como uma regra com base em limite poderia ser representada. Um arquivo JSON contém os dados de referência e é salvo no armazenamento de blobs do Azure, e esse contêiner de armazenamento de blobs é usado como uma entrada de dados de referência denominada **regras**. Você pode substituir esse arquivo JSON e substituir a configuração da regra com o passar do tempo, sem interromper ou iniciar o trabalho de streaming.

- A regra de exemplo é usada para representar um alerta ajustável quando a CPU excede (a média é maior que ou igual a) o valor `90`%. O campo `value` pode ser configurado conforme necessário.
- Observe que a regra tem um campo **operador**, que é interpretado de forma dinâmica na sintaxe da consulta posteriormente em `AVGGREATEROREQUAL`. 
- A regra filtra os dados em uma determinada chave de dimensão `2` com o valor `C1`. Outros campos são de cadeia de caracteres vazia, indicando que não se deve filtrar o fluxo de entrada por esses campos de evento. Você pode configurar regras de CPU adicionais para filtrar outros campos correspondentes conforme o necessário.
- Nem todas as colunas devem ser incluídas no evento de alerta de saída. Nesse caso, a chave de `includedDim` número `2` é ativada `TRUE` para representar que o campo de número 2 de dados de evento no fluxo será incluído nos eventos de saída qualificados. Os outros campos não são incluídos na saída do alerta, mas a lista de campos pode ser ajustada.

```json
{
    "ruleId": 1234, 
    "deviceId" : "978648", 
    "metricName": "CPU", 
    "alertName": "hot node AVG CPU over 90",
    "operator" : "AVGGREATEROREQUAL",
    "value": 90, 
    "includeDim": {
        "0": "FALSE", 
        "1": "FALSE", 
        "2": "TRUE", 
        "3": "FALSE", 
        "4": "FALSE"
    },
    "filter": {
        "0": "", 
        "1": "",
        "2": "C1", 
        "3": "", 
        "4": ""
    }    
}
```

## <a name="example-streaming-query"></a>Exemplo de consulta de streaming
Este exemplo de consulta do Stream Analytics une os dados de referência de **regras** do exemplo acima a um fluxo de entrada de dados chamado **métricas**.

```sql
WITH transformedInput AS
(
    SELECT
        dim0 = CASE rules.includeDim.[0] WHEN 'TRUE' THEN metrics.custom.dimensions.[0].value ELSE NULL END,
        dim1 = CASE rules.includeDim.[1] WHEN 'TRUE' THEN metrics.custom.dimensions.[1].value ELSE NULL END,
        dim2 = CASE rules.includeDim.[2] WHEN 'TRUE' THEN metrics.custom.dimensions.[2].value ELSE NULL END,
        dim3 = CASE rules.includeDim.[3] WHEN 'TRUE' THEN metrics.custom.dimensions.[3].value ELSE NULL END,
        dim4 = CASE rules.includeDim.[4] WHEN 'TRUE' THEN metrics.custom.dimensions.[4].value ELSE NULL END,
        metric = metrics.metric.value,
        metricName = metrics.metric.name,
        deviceId = rules.deviceId, 
        ruleId = rules.ruleId, 
        alertName = rules.alertName,
        ruleOperator = rules.operator, 
        ruleValue = rules.value
    FROM 
        metrics
        timestamp by eventTime
    JOIN 
        rules
        ON metrics.deviceId = rules.deviceId AND metrics.metric.name = rules.metricName
    WHERE
        (rules.filter.[0] = '' OR metrics.custom.filters.[0].value = rules.filter.[0]) AND 
        (rules.filter.[1] = '' OR metrics.custom.filters.[1].value = rules.filter.[1]) AND
        (rules.filter.[2] = '' OR metrics.custom.filters.[2].value = rules.filter.[2]) AND
        (rules.filter.[3] = '' OR metrics.custom.filters.[3].value = rules.filter.[3]) AND
        (rules.filter.[4] = '' OR metrics.custom.filters.[4].value = rules.filter.[4])
)

SELECT
    System.Timestamp as time, 
    transformedInput.deviceId as deviceId,
    transformedInput.ruleId as ruleId,
    transformedInput.metricName as metric,
    transformedInput.alertName as alert,
    AVG(metric) as avg,
    MIN(metric) as min, 
    MAX(metric) as max, 
    dim0, dim1, dim2, dim3, dim4
FROM
    transformedInput
GROUP BY
    transformedInput.deviceId,
    transformedInput.ruleId,
    transformedInput.metricName,
    transformedInput.alertName,
    dim0, dim1, dim2, dim3, dim4,
    ruleOperator, 
    ruleValue, 
    TumblingWindow(second, 60)
HAVING
    (
        (ruleOperator = 'AVGGREATEROREQUAL' AND avg(metric) >= ruleValue) OR
        (ruleOperator = 'AVGEQUALORLESS' AND avg(metric) <= ruleValue) 
    )
```

## <a name="example-streaming-input-event-data"></a>Dados de evento de entrada de streaming de exemplo
Estes dados JSON de exemplo que representam os dados de entrada de **métricas** são usados na consulta de streaming acima. 

- Três eventos de exemplo são listados no período de tempo de um minuto, valor `T14:50`. 
- Todos os três têm o mesmo `deviceId` valor `978648`.
- Os valores de métricas de CPU variam em cada evento, `98`, `95`, `80`, respectivamente. Somente os dois primeiros eventos excederem o alerta de CPU estabelecido na regra.
- O campo includeDim na regra de alerta foi a chave número 2. O campo de chave 2 correspondente nos eventos de exemplo é chamado de `NodeName`. Os três exemplos de eventos têm valores `N024`, `N024` e `N014` respectivamente. Na saída, você verá apenas o nó `N024`, já que ele representa os únicos dados que correspondem aos critérios de alerta de alta utilização da CPU. `N014` não atende ao limite de CPU alta.
- A regra de alerta está configurada com um `filter` somente na chave número 2, que corresponde ao campo `cluster` nos eventos de exemplo. Os três eventos de exemplo têm o valor `C1` e correspondem aos critérios de filtro.

```json
{
    "eventTime": "2018-04-30T14:50:23.1324132Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N1"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N1"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 98,
        "count": 1.0,
        "min": 98,
        "max": 98,
        "stdDev": 0.0
    }
}
{
    "eventTime": "2018-04-30T14:50:24.1324138Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N2"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N2"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 95,
        "count": 1,
        "min": 95,
        "max": 95,
        "stdDev": 0
    }
}
{
    "eventTime": "2018-04-30T14:50:37.1324130Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N3"
            },
            "1": {
                "name": "Cluster",
                "value": "C1 "
            },
            "2": {
                "name": "NodeName",
                "value": "N014"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N3"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 80,
        "count": 1,
        "min": 80,
        "max": 80,
        "stdDev": 0
    }
}
```

## <a name="example-output"></a>Saída de exemplo
Estes dados JSON de saída de exemplo mostram que um único evento de alerta foi gerado com base na regra de limite de CPU definida nos dados de referência. O evento de saída contém o nome do alerta, bem como a agregação (média, mín, máx) dos campos considerados. Os dados de evento de saída incluem o valor `NodeName` de campo de chave 2 `N024` devido à configuração da regra. (O JSON foi alterado para mostrar as quebras de linha para facilitar a leitura).

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```
<!--Update_Description: updat meta properties, wording update-->