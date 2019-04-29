---
title: Melhores práticas para formatar JSON nas consultas do Azure Time Series Insights | Microsoft Docs
description: Saiba como melhorar a eficiência de consulta do Azure Time Series Insights.
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 05/24/2018
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: 2d42b7ebdee291e7c71351fa2c3a5583a121b79e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63760670"
---
# <a name="how-to-shape-json-to-maximize-query-performance"></a>Como formatar JSON para maximizar o desempenho da consulta 

Este artigo fornece orientação para a formatação JSON, para maximizar a eficiência de suas consultas do Azure Time Series Insights (TSI).

## <a name="video"></a>Vídeo: 

### <a name="in-this-video-we-cover-best-practices-around-shaping-json-to-meet-your-storage-needsbr"></a>Neste vídeo, abordaremos as práticas recomendadas sobre a formatação JSON para atender às suas necessidades de armazenamento.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Práticas recomendadas

É importante considerar como enviar eventos para o Time Series Insights. Ou seja, você sempre deve:

1. envie dados pela rede de maneira mais eficiente possível.
2. Certifique-se de que seus dados sejam armazenados de forma que permitam executar agregações adequadas para seu cenário.
3. certifique-se de não atingir os limites máximos de propriedade do TSI de
   - 600 propriedades (colunas) para ambientes S1.
   - 800 propriedades (colunas) para ambientes S2.

As diretrizes a seguir ajudam a garantir o melhor desempenho de consulta possível:

1. Não use as propriedades dinâmicas, como uma ID de marca, como nome de propriedade, pois ela contribui para atingir o limite máximo de propriedades.
2. Não envie propriedades desnecessárias. Se uma propriedade de consulta não for necessária, será melhor não enviá-lo e evitar limitações de armazenamento.
3. Use [dados de referência](time-series-insights-add-reference-data-set.md) para evitar o envio de dados estáticos na rede.
4. Compartilhe propriedades de dimensão entre vários eventos para enviar dados pela rede com mais eficiência.
5. Não use o aninhamento profundo de matriz. O TSI dá suporte a até dois níveis de matrizes aninhadas que contêm objetos. O TSI mescla matrizes nas mensagens com vários eventos com pares de valor de propriedade.
6. Se apenas algumas medidas existirem para a maioria ou todos os eventos, será melhor enviar essas medidas como propriedades separadas dentro do mesmo objeto. Enviá-los separadamente reduz o número de eventos e pode tornar consultas mais funcionais, pois menos eventos precisam ser processados. Quando houver várias medidas, enviá-las como valores em uma única propriedade minimiza a possibilidade de atingir o limite máximo de propriedade.

## <a name="examples"></a>Exemplos

Os exemplos a seguir demonstram como enviar eventos para destacar as recomendações anteriores. Após cada exemplo, você pode ver como as recomendações foram aplicadas.

Os exemplos são baseados em um cenário onde vários dispositivos enviam sinais ou medidas. As medidas ou sinais podem ser a Taxa de Fluxo, Pressão de Óleo do Motor, Temperatura e Umidade. No primeiro exemplo, há algumas medidas em todos os dispositivos. No segundo exemplo, há vários dispositivos, e cada um envia muitas medidas exclusivas.

### <a name="scenario-only-a-few-measurementssignals-exist"></a>Cenário: existem apenas algumas medidas/sinais

**Recomendação:** envie cada medida como uma propriedade/coluna separada.

No exemplo a seguir, há uma única mensagem do Hub IoT, onde a matriz externa contém uma seção compartilhada comuns dos valores de dimensão. A matriz externa usa dados de referência para aumentar a eficiência da mensagem. Os dados de referência contêm metadados do dispositivo, que não são alterados com cada evento, mas fornecem propriedades úteis para análise de dados. Os valores comuns de dimensão de envio em lote e a utilização de dados de referência, salva em bytes enviados pela rede, tornando a mensagem mais eficiente.

Carga JSON de exemplo:

```json
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 1.0172575712203979,
                "Engine Oil Pressure psi ": 34.7
            },
            {
                "Flow Rate ft3/s": 2.445906400680542,
                "Engine Oil Pressure psi ": 49.2
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "Flow Rate psi": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

Tabela de dados de referência (a propriedade de chave é deviceId):

| deviceId | messageId | deviceLocation |
| --- | --- | --- |
| FXXX | DADOS\_LINHA | UE |
| FAAA | DADOS\_LINHA | EUA |

Tabela de eventos do Time Series Insights (após a mesclagem):

| deviceId | messageId | deviceLocation |  timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
| --- | --- | --- | --- | --- | --- |
| FXXX | DADOS\_LINHA | UE | 2018-01-17T01:17:00Z | 1,0172575712203979 | 34,7 |
| FXXX | DADOS\_LINHA | UE | 2018-01-17T01:17:00Z | 2,445906400680542 | 49,2 |
| FAAA | DADOS\_LINHA | EUA | 2018-01-17T01:18:00Z | 0,58015072345733643 | 22,2 |

Observe o seguinte no exemplo anterior:

- A coluna **deviceId** serve como o cabeçalho de coluna para vários dispositivos em uma frota. Tentar tornar o valor de deviceId seu próprio nome de propriedade limitaria o total de dispositivos a 595 (ambientes S1) ou 795 (ambientes S2), com as outras cinco colunas.

- As propriedades desnecessárias são anuladas, por exemplo, informações de marca e modelo etc. Uma vez que eles não serão consultados no futuro, eliminá-los permite mais eficiência de rede e de armazenamento.

- Os dados de referência são usados para reduzir o número de bytes transferidos pela rede. Dois atributos, **messageId** e **deviceLocation**, são unidos usando a propriedade chave **deviceId**. Esses dados são unidos aos dados de telemetria em tempo de ingresso e armazenados em TSI para consulta.

- Duas camadas de aninhamento são usadas, que é a quantidade máxima de aninhamento com suporte do TSI. É essencial para evitar matrizes profundamente aninhadas.

- As medidas são enviadas como propriedades separadas no mesmo objeto, já que há algumas medidas. Aqui, **series.Flow Rate psi** e **series.Engine Oil Pressure ft3/s** são colunas exclusivas.

### <a name="scenario-several-measures-exist"></a>Cenário: existem várias medidas

**Recomendação:** envie medidas como as tuplas "tipo", "unidade", "valor".

Carga JSON de exemplo:

```json
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 1.0172575712203979
            },
            {
                "tagId": "oilPressure",
                "value": 49.2
            },
            {
                "tagId": "pumpRate",
                "value": 2.445906400680542
            },
            {
                "tagId": "oilPressure",
                "value": 34.7
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 0.58015072345733643
            },
            {
                "tagId": "oilPressure",
                "value": 22.2
            }
        ]
    }
]
```

Dados de referência (as propriedades chave são deviceId e series.tagId):

| deviceId | series.tagId | messageId | deviceLocation | tipo | unit |
| --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | DADOS\_LINHA | UE | Taxa de Fluxo | ft3/s |
| FXXX | oilPressure | DADOS\_LINHA | UE | Pressão de óleo do motor | psi |
| FAAA | pumpRate | DADOS\_LINHA | EUA | Taxa de Fluxo | ft3/s |
| FAAA | oilPressure | DADOS\_LINHA | EUA | Pressão de óleo do motor | psi |

Tabela de eventos do Time Series Insights (após a mesclagem):

| deviceId | series.tagId | messageId | deviceLocation | Tipo | unit |  timestamp | series.value |
| --- | --- | --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | DADOS\_LINHA | UE | Taxa de Fluxo | ft3/s | 2018-01-17T01:17:00Z | 1,0172575712203979 |
| FXXX | oilPressure | DADOS\_LINHA | UE | Pressão de óleo do motor | psi | 2018-01-17T01:17:00Z | 34,7 |
| FXXX | pumpRate | DADOS\_LINHA | UE | Taxa de Fluxo | ft3/s | 2018-01-17T01:17:00Z | 2,445906400680542 |
| FXXX | oilPressure | DADOS\_LINHA | UE | Pressão de óleo do motor | Psi | 2018-01-17T01:17:00Z | 49,2 |
| FAAA | pumpRate | DADOS\_LINHA | EUA | Taxa de Fluxo | ft3/s | 2018-01-17T01:18:00Z | 0,58015072345733643 |
| FAAA | oilPressure | DADOS\_LINHA | EUA | Pressão de óleo do motor | psi | 2018-01-17T01:18:00Z | 22,2 |

Observe o seguinte no exemplo anterior e, assim como no primeiro exemplo:

- colunas **deviceId** e **series.tagId** servem como cabeçalhos de coluna para diversos dispositivos e marcas em uma frota. Usar cada um deles como seu próprio atributo limitaria a consultas um total de 594 (ambientes S1) ou 794 (ambientes S2) dispositivos com as outras seis colunas.

- propriedades desnecessárias foram anuladas pela razão citada no primeiro exemplo.

- os dados de referência são usados para reduzir o número de bytes transferidos pela rede introduzindo **deviceId** para um par exclusivo de **messageId** e **deviceLocation**. Uma chave composta é usada, **series.tagId**, para o par exclusivo de **tipo** e **unidade**. A chave composta permite que o par **deviceId** e **series.tagId** seja usado para fazer referência a quatro valores: **messageId, deviceLocation, type** e **unit**. Esses dados são unidos aos dados de telemetria em tempo de ingresso e armazenados em TSI para consulta.

- duas camadas de aninhamento são usadas peça razão citada no primeiro exemplo.

### <a name="for-both-scenarios"></a>Para ambos os cenários

Se você tiver uma propriedade com um grande número de valores possíveis, é melhor enviar como valores distintos em uma única coluna em vez de criar uma nova coluna para cada valor. Dos dois exemplos anteriores:
  - No primeiro exemplo, há algumas propriedades que têm vários valores, portanto é melhor criar cada propriedade em separado. 
  - No entanto, no segundo exemplo, você pode ver que as medidas não são especificadas como propriedades individuais, mas em vez disso, uma matriz de valores/medidas em uma propriedade comum da série. Uma nova chave é enviada, **tagId** , que cria uma nova coluna, **series.tagId**, na tabela plana. Novas propriedades são criadas, **type** e **unit**, usando dados de referência, evitando que o limite de propriedade seja atingido.

## <a name="next-steps"></a>Próximas etapas

Para colocar essas diretrizes em prática, veja [Sintaxe de consulta do Azure Time Series Insights](/rest/api/time-series-insights/ga-query-syntax) para saber mais sobre a sintaxe de consulta para os dados do TSI acessarem a API REST.