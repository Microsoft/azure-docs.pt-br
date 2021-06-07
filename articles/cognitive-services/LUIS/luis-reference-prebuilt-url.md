---
title: Entidades predefinidas de URL-LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações sobre a entidade predefinida URL de LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/04/2019
ms.openlocfilehash: bc33fd1b90306a016c419f227fb9e73e83e8ea83
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91535246"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>Entidade de URL predefinida para um aplicativo LUIS
A entidade de URL extrai URLs com nomes de domínio ou endereços IP. Uma vez que essa entidade já está treinada, não é necessário adicionar enunciados de exemplo contendo URLs ao aplicativo. A entidade de URL é compatível somente com a cultura `en-us`.

## <a name="types-of-urls"></a>Tipos de URLs
A URL é gerenciada por meio do repositório do GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml)

## <a name="resolution-for-prebuilt-url-entity"></a>Resolução para a entidade URL predefinida

Os seguintes objetos de entidade são retornados para a consulta:

`https://www.luis.ai is a great cognitive services example of artificial intelligence`

#### <a name="v3-response"></a>[Resposta v3](#tab/V3)

O JSON a seguir é com o `verbose` parâmetro definido como `false` :

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3, resposta detalhada](#tab/V3-verbose)

O JSON a seguir é com o `verbose` parâmetro definido como `true` :

```json
"entities": {
    "url": [
        "https://www.luis.ai"
    ],
    "$instance": {
        "url": [
            {
                "type": "builtin.url",
                "text": "https://www.luis.ai",
                "startIndex": 0,
                "length": 17,
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

O exemplo a seguir mostra a resolução do https://www.luis.ai é um excelente exemplo de serviços cognitivas de inteligência artificial

```json
"entities": [
    {
        "entity": "https://www.luis.ai",
        "type": "builtin.url",
        "startIndex": 0,
        "endIndex": 17
    }
]
```

* * *

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [ponto de extremidade de previsão V3](luis-migration-api-v3.md).

Saiba mais sobre as entidades [ordinal](luis-reference-prebuilt-ordinal.md), [número](luis-reference-prebuilt-number.md) e [temperatura](luis-reference-prebuilt-temperature.md).
