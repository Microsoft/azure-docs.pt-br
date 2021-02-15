---
title: Entidade predefinida Person-LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações sobre a entidade personName pré-criada no LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/07/2019
ms.openlocfilehash: 7b0153d79aaf7b88fea958ab36183e57b41af204
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535416"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>Entidade predefinida PersonName para um aplicativo LUIS
A entidade personName pré-criada detecta nomes de pessoas. Como essa entidade já está treinada, não é necessário adicionar enunciados de exemplo contendo personName nas intenções do aplicativo. A entidade personName tem suporte nas [culturas](luis-reference-prebuilt-entities.md) em inglês e em chinês.

## <a name="resolution-for-personname-entity"></a>Resolução da entidade personName

Os seguintes objetos de entidade são retornados para a consulta:

`Is Jill Jones in Cairo?`


#### <a name="v3-response"></a>[Resposta v3](#tab/V3)


O JSON a seguir é com o `verbose` parâmetro definido como `false` :

```json
"entities": {
    "personName": [
        "Jill Jones"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3, resposta detalhada](#tab/V3-verbose)
O JSON a seguir é com o `verbose` parâmetro definido como `true` :

```json
"entities": {
    "personName": [
        "Jill Jones"
    ],
    "$instance": {
        "personName": [
            {
                "type": "builtin.personName",
                "text": "Jill Jones",
                "startIndex": 3,
                "length": 10,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ],
    }
}
```
#### <a name="v2-response"></a>[Resposta v2](#tab/V2)

O exemplo a seguir mostra a resolução da entidade **builtin.personName**.

```json
"entities": [
{
    "entity": "Jill Jones",
    "type": "builtin.personName",
    "startIndex": 3,
    "endIndex": 12
}
]
```
* * *

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [ponto de extremidade de previsão V3](luis-migration-api-v3.md).

Saiba mais sobre as entidades [email](luis-reference-prebuilt-email.md), [número](luis-reference-prebuilt-number.md) e [ordinal](luis-reference-prebuilt-ordinal.md).
