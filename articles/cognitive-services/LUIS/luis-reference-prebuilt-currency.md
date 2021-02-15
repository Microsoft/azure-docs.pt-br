---
title: Entidade predefinida de moeda-LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações sobre a entidade predefinida de moeda de LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: c89de0ba74d04c510f3d5ba537f3a6dcc4819169
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91534329"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Entidade de moeda predefinida para um aplicativo LUIS
A entidade de moeda predefinida detecta a moeda em muitas indicações e países/regiões, independentemente da cultura do aplicativo LUIS. Uma vez que essa entidade já está treinada, não é necessário adicionar enunciados de exemplo contendo moeda às intenções do aplicativo. A entidade de moeda é compatível com [muitas culturas](luis-reference-prebuilt-entities.md).

## <a name="types-of-currency"></a>Tipos de moeda
A moeda é gerenciada por meio do repositório do GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26)

## <a name="resolution-for-currency-entity"></a>Resolução de entidades de moeda

#### <a name="v3-response"></a>[Resposta v3](#tab/V3)

O JSON a seguir é com o `verbose` parâmetro definido como `false` :

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "units": "Dollar"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3, resposta detalhada](#tab/V3-verbose)
O JSON a seguir é com o `verbose` parâmetro definido como `true` :

```json
"entities": {
    "money": [
        {
            "number": 10.99,
            "unit": "Dollar"
        }
    ],
    "$instance": {
        "money": [
            {
                "type": "builtin.currency",
                "text": "$10.99",
                "startIndex": 23,
                "length": 6,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor"
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[Resposta v2](#tab/V2)

O exemplo a seguir mostra a resolução da entidade **builtin.currency**.

```json
"entities": [
    {
        "entity": "$10.99",
        "type": "builtin.currency",
        "startIndex": 23,
        "endIndex": 28,
        "resolution": {
        "unit": "Dollar",
        "value": "10.99"
        }
    }
]
```
* * *

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [ponto de extremidade de previsão V3](luis-migration-api-v3.md).

Saiba mais sobre as entidades [datetimeV2](luis-reference-prebuilt-datetimev2.md), [dimensão](luis-reference-prebuilt-dimension.md) e [email](luis-reference-prebuilt-email.md).
