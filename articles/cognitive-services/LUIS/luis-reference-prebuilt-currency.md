---
title: Entidade predefinidos de moeda
titleSuffix: Azure
description: Este artigo contém informações sobre a entidade predefinida de moeda de LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 9efaaa6bdd0f2b51efca398464dbf08de56d831d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712725"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Entidade de moeda predefinida para um aplicativo LUIS
A entidade de moeda predefinida detecta moeda em muitas denominações e países, independentemente da cultura do aplicativo de LUIS. Uma vez que essa entidade já está treinada, não é necessário adicionar enunciados de exemplo contendo moeda às intenções do aplicativo. A entidade de moeda é compatível com [muitas culturas](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Tipos de moeda
A moeda é gerenciada por meio do repositório do GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26)

## <a name="resolution-for-currency-entity"></a>Resolução de entidades de moeda
O exemplo a seguir mostra a resolução da entidade **builtin.currency**.

```json
{
  "query": "search for items under $10.99",
  "topScoringIntent": {
    "intent": "SearchForItems",
    "score": 0.926173568
  },
  "intents": [
    {
      "intent": "SearchForItems",
      "score": 0.926173568
    },
    {
      "intent": "None",
      "score": 0.07376878
    }
  ],
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
}
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as entidades [datetimeV2](luis-reference-prebuilt-datetimev2.md), [dimensão](luis-reference-prebuilt-dimension.md) e [email](luis-reference-prebuilt-email.md). 
