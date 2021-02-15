---
title: Entidade predefinida keyphrase-LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações da entidade pré-compilada KeyPhrase no LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.openlocfilehash: 02a48c8d106795b970d573bb71f52ba92e0a90ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541978"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>Entidade predefinida keyPhrase para um aplicativo LUIS
A entidade keyPhrase extrai uma variedade de frases-chave de um expressão. Você não precisa adicionar o exemplo declarações que contém keyPhrase ao aplicativo. A entidade keyPhrase tem suporte em [muitas culturas](luis-language-support.md#languages-supported) como parte dos recursos de [análise de texto](../text-analytics/overview.md) .

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Resolução para entidade keyPhrase pré-compilada

Os seguintes objetos de entidade são retornados para a consulta:

`where is the educational requirements form for the development and engineering group`

#### <a name="v3-response"></a>[Resposta v3](#tab/V3)

O JSON a seguir é com o `verbose` parâmetro definido como `false` :

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3, resposta detalhada](#tab/V3-verbose)
O JSON a seguir é com o `verbose` parâmetro definido como `true` :

```json
"entities": {
    "keyPhrase": [
        "educational requirements",
        "development"
    ],
    "$instance": {
        "keyPhrase": [
            {
                "type": "builtin.keyPhrase",
                "text": "educational requirements",
                "startIndex": 13,
                "length": 24,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            },
            {
                "type": "builtin.keyPhrase",
                "text": "development",
                "startIndex": 51,
                "length": 11,
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

O exemplo a seguir mostra a resolução da entidade **builtin.keyPhrase**.

```json
"entities": [
    {
        "entity": "development",
        "type": "builtin.keyPhrase",
        "startIndex": 51,
        "endIndex": 61
    },
    {
        "entity": "educational requirements",
        "type": "builtin.keyPhrase",
        "startIndex": 13,
        "endIndex": 36
    }
]
```
* * *

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [ponto de extremidade de previsão V3](luis-migration-api-v3.md).

Saiba mais sobre as entidades [percentual](luis-reference-prebuilt-percentage.md), [número](luis-reference-prebuilt-number.md) e [idade](luis-reference-prebuilt-age.md).
