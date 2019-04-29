---
title: Referência de entidades de email predefinidas de LUIS – Azure | Microsoft Docs
titleSuffix: Azure
description: Este artigo contém informações de entidade predefinida de email de LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 4a48bb4a6e988d4352f957c6435a9c1bf0a3e5fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712722"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Entidade de email predefinida para um aplicativo LUIS
Extração de email inclui todo o endereço de email inteira de um enunciado. Uma vez que essa entidade já está treinada, não é necessário adicionar enunciados de exemplo contendo email às intenções do aplicativo. Há suporte para a entidade de email apenas na cultura `en-us`. 

## <a name="resolution-for-prebuilt-email"></a>Resolução de email predefinido
O exemplo a seguir mostra a resolução da entidade **builtin.email**.

```json
{
  "query": "please send the information to patti.owens@microsoft.com",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.811592042
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.811592042
    }
  ],
  "entities": [
    {
      "entity": "patti.owens@microsoft.com",
      "type": "builtin.email",
      "startIndex": 31,
      "endIndex": 55,
      "resolution": {
        "value": "patti.owens@microsoft.com"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [número](luis-reference-prebuilt-number.md), [ordinal](luis-reference-prebuilt-ordinal.md) e [percentual](luis-reference-prebuilt-percentage.md). 
