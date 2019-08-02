---
title: Tipo de entidade composta-LUIS
titleSuffix: Azure Cognitive Services
description: Uma entidade composta é composta por outras entidades, como entidades predefinidas, expressão simples, regular e entidades de lista. As entidades separadas formam uma entidade inteira.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: ea258275cf954bc6e06da03324c2ae93de0e7fde
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563241"
---
# <a name="composite-entity"></a>Entidade composta 

Uma entidade composta é composta por outras entidades, como entidades predefinidas, expressão simples, regular e entidades de lista. As entidades separadas formam uma entidade inteira. 

**Essa entidade é uma boa opção quando os dados:**

* Estão relacionados uns aos outros. 
* Estão relacionadas entre si no contexto do enunciado.
* Usam uma variedade de tipos de entidade.
* Precisam ser agrupados e processados pelo aplicativo cliente como uma unidade de informação.
* Tenham uma variedade de enunciados de usuário que exigem aprendizado de máquina.

![entidade composta](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>JSON de exemplo

Considere uma entidade composta de pré-criados `number` e `Location::ToLocation` com os seguintes expressão:

`book 2 tickets to paris`

Observe que `2`, o número e `paris`, o ToLocation tem palavras entre eles que não fazem parte de nenhuma entidade. O sublinhado verde, usado em um enunciado rotulado no site do [LUIS](luis-reference-regions.md), indica uma entidade composta.

![Entidade composta](./media/luis-concept-data-extraction/composite-entity.png)

Entidades compostas são retornadas em uma matriz `compositeEntities` e todas as entidades com a composta também são retornadas na matriz `entities`:

```JSON

"entities": [
    {
    "entity": "2 tickets to cairo",
    "type": "ticketInfo",
    "startIndex": 0,
    "endIndex": 17,
    "score": 0.67200166
    },
    {
    "entity": "2",
    "type": "builtin.number",
    "startIndex": 0,
    "endIndex": 0,
    "resolution": {
        "subtype": "integer",
        "value": "2"
    }
    },
    {
    "entity": "cairo",
    "type": "builtin.geographyV2",
    "startIndex": 13,
    "endIndex": 17
    }
],
"compositeEntities": [
    {
    "parentType": "ticketInfo",
    "value": "2 tickets to cairo",
    "children": [
        {
        "type": "builtin.geographyV2",
        "value": "cairo"
        },
        {
        "type": "builtin.number",
        "value": "2"
        }
    ]
    }
]
```    

|Objeto de dados|Nome da entidade|Valor|
|--|--|--|
|Entidade predefinida – número|"builtin.number"|"2"|
|Entidade predefinida – GeographyV2|"Location::ToLocation"|"paris"|

## <a name="next-steps"></a>Próximas etapas

Neste [tutorial](luis-tutorial-composite-entity.md), adicione uma **entidade composta** para agrupar dados extraídos de vários tipos em uma única entidade contentora. Agrupando os dados, o aplicativo cliente poderá extrair com facilidade dados relacionados em diferentes tipos de dados.
