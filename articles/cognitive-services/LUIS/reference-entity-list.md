---
title: Tipo de entidade de lista-LUIS
titleSuffix: Azure Cognitive Services
description: As entidades de lista representam um conjunto fixo e fechado de palavras relacionadas, juntamente com seus sinônimos. O LUIS não descobre valores adicionais para entidades de lista. Use o recurso recomendado para ver sugestões para novas palavras com base na lista atual.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: ca9f8b570ee28b1913c8ec81c66a5b70827c04d6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559966"
---
# <a name="list-entity"></a>Entidade de lista 

As entidades de lista representam um conjunto fixo e fechado de palavras relacionadas, juntamente com seus sinônimos. O LUIS não descobre valores adicionais para entidades de lista. Use o recurso **Recomendado** para consultar sugestões de novas palavras com base na lista atual. Se houver mais de uma entidade de lista com o mesmo valor, cada entidade será retornada na consulta de ponto de extremidade. 

Uma entidade de lista não é aprendida por máquina. É uma correspondência exata do texto. O LUIS marca qualquer correspondência a um item em qualquer lista como uma entidade na resposta. 

**A entidade é uma boa opção quando os dados de texto:**

* São um conjunto conhecido.
* Não é alterado com frequência. Se você precisar alterar a lista com frequência ou desejar que a lista se expanda automaticamente, uma entidade simples aumentada com uma lista de frases é uma opção melhor. 
* O conjunto não excede os [limites](luis-boundaries.md) máximos do LUIS para esse tipo de entidade.
* O texto no enunciado corresponde exatamente a um sinônimo ou ao nome canônico. LUIS não usa a lista além das correspondências de texto exatas. Correspondência difusa, não diferencia maiúsculas de minúsculas, lematização, plurals e outras variações não são resolvidas com uma entidade de lista. Para gerenciar variações, considere usar um [padrão](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) com a sintaxe de texto opcional.

![entidade de lista](./media/luis-concept-entities/list-entity.png)

## <a name="example-json"></a>JSON de exemplo

Suponha que o aplicativo tem uma lista, chamada `Cities`, que permite variações de nomes de cidade que incluem a cidade do aeroporto (SEA), o código do aeroporto (SEA), o CEP (98101) e o código da área de telefone (206).

|Item de lista|Sinônimos do item|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

Na declaração anterior, a palavra `paris` é mapeada para o item paris como parte da entidade de lista `Cities`. A entidade de lista corresponde ao nome normalizado do item, assim como aos sinônimos do item.

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 22,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

Outra declaração de exemplo, que usa um sinônimo para Paris:

`book 2 tickets to roissy`

```JSON
"entities": [
  {
    "entity": "roissy",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 23,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

|Objeto de dados|Nome da entidade|Valor|
|--|--|--|
|Entidade simples|`Customer`|`bob jones`|

## <a name="next-steps"></a>Próximas etapas

Neste [tutorial](luis-quickstart-intent-and-list-entity.md), saiba como usar uma entidade de **lista** para extrair correspondências exatas de texto de uma lista de itens conhecidos. 
