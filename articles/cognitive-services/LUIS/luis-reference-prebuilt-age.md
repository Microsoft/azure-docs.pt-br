---
title: Entidade predefinida de idade
titleSuffix: Azure
description: Este artigo contém informações sobre a entidade predefinida e idade em LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: c901a384d7629a187a2a4fbd4295a1a68615abdb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712790"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>Entidade de idade predefinida para um aplicativo LUIS
A entidade de idade predefinida captura o valor de idade tanto numericamente quanto em termos de dias, semanas, meses e anos. Uma vez que essa entidade já está treinada, não é necessário adicionar enunciados contendo email às intenções do aplicativo. A entidade de idade é compatível com [muitas culturas](luis-reference-prebuilt-entities.md). 

## <a name="types-of-age"></a>Tipos de idade
A idade é gerenciada por meio do repositório do GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3)

## <a name="resolution-for-prebuilt-age-entity"></a>Resolução para entidade de idade predefinida
O exemplo a seguir mostra a resolução da entidade **builtin.age**.

```json
{
  "query": "A 90 day old utilities bill is quite late.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8236133
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8236133
    }
  ],
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre as entidades [moeda](luis-reference-prebuilt-currency.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md) e [dimensão](luis-reference-prebuilt-dimension.md). 
