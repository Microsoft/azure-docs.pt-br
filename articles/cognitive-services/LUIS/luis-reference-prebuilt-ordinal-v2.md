---
title: Entidade predefinida do ordinal v2-LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações de entidade predefinidas do ordinal V2 em Reconhecimento vocal (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 3fd80389dfa54cb8386c13e0e05a1e71ac4bd956
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541927"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Entidade predefinida do ordinal v2 para um aplicativo LUIS
O número do ordinal v2 expande o [ordinal](luis-reference-prebuilt-ordinal.md) para fornecer referências relativas, como `next` , `last` e `previous` . Eles não são extraídos usando a entidade predefinida ordinal.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Resolução para a entidade do ordinal v2 predefinida

Os seguintes objetos de entidade são retornados para a consulta:

`what is the second to last choice in the list`

#### <a name="v3-response"></a>[Resposta v3](#tab/V3)

O JSON a seguir é com o `verbose` parâmetro definido como `false` :

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ]
}
```

#### <a name="v3-verbose-response"></a>[V3, resposta detalhada](#tab/V3-verbose)

O JSON a seguir é com o `verbose` parâmetro definido como `true` :

```json
"entities": {
    "ordinalV2": [
        {
            "offset": -1,
            "relativeTo": "end"
        }
    ],
    "$instance": {
        "ordinalV2": [
            {
                "type": "builtin.ordinalV2.relative",
                "text": "the second to last",
                "startIndex": 8,
                "length": 18,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-response"></a>[Resposta v2](#tab/V2)

O exemplo a seguir mostra a resolução da entidade **Builtin. ordinalV2** .

```json
"entities": [
    {
        "entity": "the second to last",
        "type": "builtin.ordinalV2.relative",
        "startIndex": 8,
        "endIndex": 25,
        "resolution": {
            "offset": "-1",
            "relativeTo": "end"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [ponto de extremidade de previsão V3](luis-migration-api-v3.md).

Saiba mais sobre a [porcentagem](luis-reference-prebuilt-percentage.md), o [número de telefone](luis-reference-prebuilt-phonenumber.md)e as entidades de [temperatura](luis-reference-prebuilt-temperature.md) .
