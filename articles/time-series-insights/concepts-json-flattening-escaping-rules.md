---
title: Regras de saída e mesclagem JSON-Azure Time Series Insights Gen2 | Microsoft Docs
description: Saiba mais sobre o nivelamento JSON, a saída e a manipulação de matrizes no Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.openlocfilehash: 9f768982e69f785c146f026040a91f7a63eef64c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98673444"
---
# <a name="json-flattening-escaping-and-array-handling"></a>Nivelamento, escape e matriz de JSON

Seu ambiente de Azure Time Series Insights Gen2 criará dinamicamente as colunas de suas lojas quentes e frias, seguindo um determinado conjunto de convenções de nomenclatura. Quando um evento é ingerido, um conjunto de regras é aplicado à carga JSON e aos nomes de propriedade. Isso inclui a saída de determinados caracteres especiais e a mesclagem de objetos JSON aninhados. É importante conhecer essas regras para que você entenda como a forma do JSON influenciará como seus eventos são armazenados e consultados. Consulte a tabela abaixo para obter a lista completa de regras. Os exemplos A & B também demonstram como você é capaz de fazer lote com eficiência várias séries temporais em uma matriz.

> [!IMPORTANT]
>
> * Examine as regras abaixo antes de selecionar uma [Propriedade ID da série temporal](./how-to-select-tsid.md) e/ou o carimbo de data/hora de origem do evento [(s) apropriado](concepts-streaming-ingestion-event-sources.md#event-source-timestamp). Se a ID do TS ou o carimbo de data/hora estiver dentro de um objeto aninhado ou se tiver um ou mais dos caracteres especiais abaixo, é importante garantir que o nome da propriedade que você fornece corresponda ao nome da coluna *depois* que as regras de ingestão tiverem sido aplicadas. Consulte o exemplo [B](concepts-json-flattening-escaping-rules.md#example-b) abaixo.

| Regra | JSON de exemplo | [Sintaxe de expressão de série temporal](/rest/api/time-series-insights/reference-time-series-expression-syntax) | Nome da coluna de propriedade em parquet
|---|---|---|---|
| O tipo de dados Azure Time Series Insights Gen2 é acrescentado ao final do nome da coluna como "_ \<dataType\> " | ```"type": "Accumulated Heat"``` | `$event.type.String` |`type_string` |
| A propriedade de [carimbo de data/hora](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) de origem do evento será salva em Azure Time Series insights Gen2 como "timestamp" no armazenamento e o valor armazenado em UTC. Você pode personalizar sua propriedade de carimbo de data/hora de origem do evento para atender às necessidades da sua solução, mas o nome da coluna em armazenamento quente e frio é "timestamp". Outras propriedades JSON de DateTime que não são o carimbo de data/hora da origem do evento serão salvas com "_datetime" no nome da coluna, conforme mencionado na regra acima.  | ```"ts": "2020-03-19 14:40:38.318"``` |  `$event.$ts` | `timestamp` |
| Nomes de propriedades JSON que incluem os caracteres especiais. [\ e ' têm escape usando [' e ']  |  ```"id.wasp": "6A3090FD337DE6B"``` |  `$event['id.wasp'].String` | `['id.wasp']_string` |
| Em [' e '] há escape adicional de aspas simples e barras invertidas. Uma aspa simples será escrita como \ ' e uma barra invertida será escrita como \\\ | ```"Foo's Law Value": "17.139999389648"``` | `$event['Foo\'s Law Value'].Double` | `['Foo\'s Law Value']_double` |
| Objetos JSON aninhados são achatados com um ponto como separador. Há suporte para o aninhamento de até 10 níveis. |  ```"series": {"value" : 316 }``` | `$event.series.value.Long`, `$event['series']['value'].Long` ou `$event.series['value'].Long` |  `series.value_long` |
| Matrizes de tipos primitivos são armazenadas como o tipo dinâmico |  ```"values": [154, 149, 147]``` | Tipos dinâmicos só podem ser recuperados por meio da API [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) | `values_dynamic` |
| As matrizes que contêm objetos têm dois comportamentos, dependendo do conteúdo do objeto: se a (s) Propriedade (ões) ou (es) do carimbo de data/hora estiver dentro dos objetos em uma matriz, a matriz será desregistrada de forma que o conteúdo JSON inicial produza vários eventos. Isso permite que você lote vários eventos em uma estrutura JSON. Todas as propriedades de nível superior que são pares da matriz serão salvas com cada objeto não registrado. Se suas IDs de TS e carimbo de data/hora *não* estiverem dentro da matriz, ele será salvo como o tipo dinâmico. | Veja os exemplos [A](concepts-json-flattening-escaping-rules.md#example-a), [B](concepts-json-flattening-escaping-rules.md#example-b)e [C](concepts-json-flattening-escaping-rules.md#example-c) abaixo
| Matrizes contendo elementos mistos não são achatadas. |  ```"values": ["foo", {"bar" : 149}, 147]``` | Tipos dinâmicos só podem ser recuperados por meio da API [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) | `values_dynamic` |
| 512 caracteres é o limite de nome da propriedade JSON. Se o nome exceder 512 caracteres, ele será truncado para 512 e ' _< ' hashCode ' > ' será acrescentado. **Observe** que isso também se aplica a nomes de propriedade que foram concatenados do objeto achatado, denotando um caminho de objeto aninhado. |``"data.items.datapoints.values.telemetry<...continuing to over 512 chars>" : 12.3440495`` |`"$event.data.items.datapoints.values.telemetry<...continuing to include all chars>.Double"` | `data.items.datapoints.values.telemetry<...continuing to 512 chars>_912ec803b2ce49e4a541068d495ab570_double` |

## <a name="understanding-the-dual-behavior-for-arrays"></a>Compreendendo o comportamento dual para matrizes

As matrizes de objetos serão armazenadas inteiras ou divididas em vários eventos, dependendo de como você modelar seus dados. Isso permite que você use uma matriz para eventos em lote e evite repetir as propriedades de telemetria definidas no nível do objeto raiz. O envio em lote pode ser vantajoso, pois resulta em menos hubs de eventos ou mensagens do Hub IoT enviadas.

No entanto, em alguns casos, as matrizes que contêm objetos só são significativas no contexto de outros valores. A criação de vários eventos renderiza os dados sem significado. Para garantir que uma matriz de objetos seja armazenada no estado em que se encontra como um tipo dinâmico, siga as diretrizes de modelagem de dados abaixo e dê uma olhada no [exemplo C](concepts-json-flattening-escaping-rules.md#example-c)

### <a name="how-to-know-if-my-array-of-objects-will-produce-multiple-events"></a>Como saber se minha matriz de objetos produzirá vários eventos

Se uma ou mais de sua ID de série temporal estiver aninhada dentro de objetos em uma matriz *ou* se a propriedade de carimbo de data/hora de origem do evento estiver aninhada, o mecanismo de ingestão irá dividi-lo para criar vários eventos. Os nomes de propriedade que você forneceu para suas IDs de TS e/ou carimbo de data/hora devem seguir as regras de mesclagem acima e, portanto, indicarão a forma do JSON. Consulte os exemplos abaixo e confira o guia sobre como [selecionar uma propriedade ID da série temporal.](./how-to-select-tsid.md)

### <a name="example-a"></a>Exemplo A

ID da série temporal na raiz do objeto e no carimbo de data/hora aninhado \
**ID da série temporal do ambiente:**`"id"`\
**Carimbo de hora da origem do evento:**`"values.time"`\
**Carga JSON:**

```JSON
[
    {
        "id": "caaae533-1d6c-4f58-9b75-da102bcc2c8c",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 25.6073
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 43.9077
            }
        ]
    },
    {
        "id": "1ac87b74-0865-4a07-b512-56602a3a576f",
        "values": [
            {
                "time": "2020-05-01T00:59:59.000Z",
                "value": 0.337288
            },
            {
                "time": "2020-05-01T01:00:29.000Z",
                "value": 4.76562
            }
        ]
    }
]
```

**Resultado no arquivo parquet:**\
A configuração e a carga acima produzirão três colunas e quatro eventos

|  timestamp  | id_string | values.value_double
| ---- | ---- | ---- |
| `2020-05-01T00:59:59.000Z` | `caaae533-1d6c-4f58-9b75-da102bcc2c8c`| ``25.6073`` |
| `2020-05-01T01:00:29.000Z` |`caaae533-1d6c-4f58-9b75-da102bcc2c8c` | ``43.9077`` |
| `2020-05-01T00:59:59.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``0.337288`` |
| `2020-05-01T01:00:29.000Z` | `1ac87b74-0865-4a07-b512-56602a3a576f` | ``4.76562`` |

### <a name="example-b"></a>Exemplo B

ID da série temporal composta com uma propriedade aninhada \
**ID da série temporal do ambiente:** `"plantId"` e `"telemetry.tagId"`\
**Carimbo de hora da origem do evento:**`"timestamp"`\
**Carga JSON:**

```JSON
[
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:38:09Z",
        "telemetry": [
            {
                "tagId": "100231-A-A6",
                "tagValue": -31.149018
            },
            {
                "tagId": "100231-A-A1",
                "tagValue": 20.560796
            },
            {
                "tagId": "100231-A-A9",
                "tagValue": 177
            },
            {
                "tagId": "100231-A-A8",
                "tagValue": 420
            },
        ]
    },
    {
        "plantId": "9336971",
        "timestamp": "2020-01-22T16:42:14Z",
        "telemetry": [
            {
                "tagId": "103585-A-A7",
                "value": -30.9918
            },
            {
                "tagId": "103585-A-A4",
                "value": 19.960796
            }
        ]
    }
]
```

**Resultado no arquivo parquet:**\
A configuração e a carga acima produzirão quatro colunas e seis eventos

|  timestamp  | plantId_string | telemetry.tagId_string | telemetry.value_double
| ---- | ---- | ---- | ---- |
| `2020-01-22T16:38:09Z` | `9336971`| ``100231-A-A6`` |  -31,149018 |
| `2020-01-22T16:38:09Z` |`9336971` | ``100231-A-A1`` | 20,560796 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A9`` | 177 |
| `2020-01-22T16:38:09Z` | `9336971` | ``100231-A-A8`` | 420 |
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A7`` | -30,9918 |  
| `2020-01-22T16:42:14Z` | `9336971` | ``100231-A-A4`` | 19,960796 |

### <a name="example-c"></a>Exemplo C

A ID da série temporal e o carimbo de data/hora estão na raiz do objeto \
**ID da série temporal do ambiente:**`"id"`\
**Carimbo de hora da origem do evento:**`"timestamp"`\
**Carga JSON:**

```JSON
{
    "id": "800500054755",
    "timestamp": "2020-11-01T10:00:00.000Z",
    "datapoints": [{
            "value": 120
        },
        {
            "value": 124
        }
    ]
}
```

**Resultado no arquivo parquet:**\
A configuração e a carga acima produzirão três colunas e um evento

|  timestamp  | id_string | datapoints_dynamic  
| ---- | ---- | ---- |
| `2020-11-01T10:00:00.000Z` | `800500054755`| ``[{"value": 120},{"value":124}]`` |

## <a name="next-steps"></a>Próximas etapas

* Entenda as limitações de [taxa de transferência](./concepts-streaming-ingress-throughput-limits.md) do seu ambiente
