---
title: Referência de operador lógico OData
titleSuffix: Azure Cognitive Search
description: Documentação de referência e sintaxe para usar os operadores lógicos OData and, or e not do nas consultas do Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 27d5427d34de591f9cfeab2310d79a2fde217624
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88917866"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Operadores lógicos OData no Azure Cognitive Search – `and`, `or`, `not`

[Expressões de filtro OData](query-odata-filter-orderby-syntax.md) no Azure Cognitive Search são expressões boolianas avaliadas como `true` ou `false`. Você pode escrever um filtro complexo escrevendo uma série de [filtros mais simples](search-query-odata-comparison-operators.md) e compondo-os usando os operadores lógicos da [álgebra booliana](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: um operador binário que será avaliado como `true` se as subexpressões esquerda e direita forem avaliadas como `true`.
- `or`: um operador binário que será avaliado como `true` se uma das subexpressões esquerda ou direita for avaliada como `true`.
- `not`: um operador unário que será avaliado como `true` se a subexpressão for avaliada como `false` e vice-versa.

Em conjunto com os [operadores de coleção `any` e `all`](search-query-odata-collection-operators.md), eles permitem que você construa filtros que podem expressar critérios de pesquisa muito complexos.

## <a name="syntax"></a>Sintaxe

A EBNF ([forma estendida de Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) a seguir define a gramática de uma expressão OData que usa operadores lógicos.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe OData para Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Confira [Referência de sintaxe de expressão OData para Azure Cognitive Search](search-query-odata-syntax-reference.md) para ver a EBNF completa.

Há duas formas de expressões lógicas: binária (`and`/`or`), em que há duas subexpressões, e unária (`not`), em que há apenas uma. As subexpressões podem ser expressões boolianas de qualquer tipo:

- Campos ou variáveis de intervalo do tipo `Edm.Boolean`
- Funções que retornam valores do tipo `Edm.Boolean`, como `geo.intersects` ou `search.ismatch`
- [Expressões de comparação](search-query-odata-comparison-operators.md), como `rating gt 4`
- [Expressões de coleção](search-query-odata-collection-operators.md), como `Rooms/any(room: room/Type eq 'Deluxe Room')`
- Os literais boolianos `true` ou `false`.
- Outras expressões lógicas construídas usando `and`, `or` e `not`.

> [!IMPORTANT]
> Há algumas situações em que nem todos os tipos de subexpressão podem ser usados com `and`/`or`, especialmente dentro de expressões lambda. Confira [Operadores de coleção OData no Azure Cognitive Search](search-query-odata-collection-operators.md#limitations) para obter detalhes.

### <a name="logical-operators-and-null"></a>Operadores lógicos e `null`

A maioria das expressões boolianas, como funções e comparações, não pode produzir valores `null`, e os operadores lógicos não podem ser aplicados diretamente ao literal `null` (por exemplo, `x and null` não é permitido). No entanto, os campos boolianos podem ser `null`. Sendo assim, você precisa estar ciente de como os operadores `and`, `or` e `not` se comportam na presença de null. Isso é resumido na seguinte tabela, em que `b` é um campo do tipo `Edm.Boolean`:

| Expression | Resultado quando `b` é `null` |
| --- | --- |
| `b` | `false` |
| `not b` | `true` |
| `b eq true` | `false` |
| `b eq false` | `false` |
| `b eq null` | `true` |
| `b ne true` | `true` |
| `b ne false` | `true` |
| `b ne null` | `false` |
| `b and true` | `false` |
| `b and false` | `false` |
| `b or true` | `true` |
| `b or false` | `false` |

Quando um campo booliano `b` aparece sozinho em uma expressão de filtro, ele se comporta como se tivesse sido escrito `b eq true`. Portanto, se `b` for `null`, a expressão será avaliada como `false`. Da mesmo modo, `not b` comporta-se como `not (b eq true)`. Portanto, ele é avaliado como `true`. Dessa forma, os campos `null` se comportam da mesma maneira que `false`. Isso é consistente com o modo como eles se comportam quando combinados com outras expressões usando `and` e `or`, conforme mostrado na tabela acima. Apesar disso, uma comparação direta com `false` (`b eq false`) ainda será avaliada como `false`. Em outras palavras, `null` não é igual a `false`, embora ambos se comportem da mesma maneira em expressões boolianas.

## <a name="examples"></a>Exemplos

Corresponder documentos em que o campo `rating` está entre três e cinco, inclusive:

```odata-filter-expr
    rating ge 3 and rating le 5
```

Corresponder documentos em que todos os elementos do campo `ratings` são menores que três ou maiores que cinco:

```odata-filter-expr
    ratings/all(r: r lt 3 or r gt 5)
```

Corresponder documentos em que o campo `location` está dentro do polígono fornecido, e o documento não contém o termo "público".

```odata-filter-expr
    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')
```

Corresponder documentos para hotéis em Vancouver, Canadá, em que há um quarto de luxo com uma taxa base inferior a 160:

```odata-filter-expr
    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)
```

## <a name="next-steps"></a>Próximas etapas  

- [Filtros no Azure Cognitive Search](search-filters.md)
- [Visão geral da linguagem de expressão OData para Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;API REST do Azure Cognitive Search&#41;](/rest/api/searchservice/Search-Documents)