---
title: Entidade numérica predefinida
titleSuffix: Azure
description: Este artigo contém informações sobre a entidade predefinida de número de LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 83f7cc7c0da2682244fa9c4e0e2b153aff2e2380
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473205"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Entidade numérica predefinida para um aplicativo LUIS
Há muitas maneiras de usar os valores numéricos para quantificar, expressar e descrever informações. Este artigo aborda apenas alguns dos exemplos possíveis. O LUIS interpreta as variações em enunciados do usuário e retorna valores numéricos consistentes. Uma vez que essa entidade já está treinada, não é necessário adicionar enunciados de exemplo contendo número às intenções do aplicativo. 

## <a name="types-of-number"></a>Tipos de número
O número é gerenciado por meio do repositório do GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml)

## <a name="examples-of-number-resolution"></a>Exemplos de resolução de número

| Enunciado        | Entidade   | Resolução |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      | 
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


O LUIS inclui o valor reconhecido de uma entidade **`builtin.number`** no campo `resolution` da resposta JSON que ele retorna.

## <a name="resolution-for-prebuilt-number"></a>Resolução para número predefinido
O exemplo a seguir mostra uma resposta JSON de LUIS, que inclui a resolução do valor de 24, para o enunciado "duas dúzias".

```json
{
  "query": "order two dozen eggs",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.105443209
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.105443209
    },
    {
      "intent": "OrderFood",
      "score": 0.9468431361
    },
    {
      "intent": "Help",
      "score": 0.000399122015
    },
  ],
  "entities": [
    {
      "entity": "two dozen",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 14,
      "resolution": {
        "subtype": "integer",
        "value": "24"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre [moeda](luis-reference-prebuilt-currency.md), [ordinal](luis-reference-prebuilt-ordinal.md) e [percentual](luis-reference-prebuilt-percentage.md). 
