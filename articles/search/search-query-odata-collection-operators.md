---
title: Referência de operador de coleção OData
titleSuffix: Azure Cognitive Search
description: Ao criar expressões de filtro no Azure Pesquisa Cognitiva consultas, use os operadores "any" e "All" em expressões lambda quando o filtro estiver em um campo de coleção ou coleção complexa.
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
ms.openlocfilehash: 4c1243d5d9122539466e94b6bbfdd5ced588e69a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88934898"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>Operadores de coleção OData no Azure Pesquisa Cognitiva- `any` e `all`

Ao escrever uma [expressão de filtro OData](query-odata-filter-orderby-syntax.md) para usar com o Azure pesquisa cognitiva, geralmente é útil filtrar nos campos de coleção. Você pode conseguir isso usando os `any` `all` operadores e.

## <a name="syntax"></a>Sintaxe

O seguinte EBNF ([formulário estendido Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática de uma expressão OData que usa `any` ou `all` .

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe do OData para Pesquisa Cognitiva do Azure](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Consulte [referência de sintaxe de expressão OData para pesquisa cognitiva do Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

Há três formas de expressão que filtram coleções.

- As duas primeiras iteram em um campo de coleção, aplicando um predicado fornecido na forma de uma expressão lambda para cada elemento da coleção.
  - Uma expressão `all` que usa retorna `true` se o predicado é verdadeiro para cada elemento da coleção.
  - Uma expressão `any` que usa retorna `true` se o predicado é true para pelo menos um elemento da coleção.
- A terceira forma de filtro de coleção usa `any` sem uma expressão lambda para testar se um campo de coleção está vazio. Se a coleção tiver qualquer elemento, ela retornará `true` . Se a coleção estiver vazia, ela retornará `false` .

Uma **expressão lambda** em um filtro de coleção é como o corpo de um loop em uma linguagem de programação. Ele define uma variável, chamada **variável de intervalo**, que contém o elemento atual da coleção durante a iteração. Ele também define outra expressão booliana que é o critério de filtro a ser aplicado à variável de intervalo para cada elemento da coleção.

## <a name="examples"></a>Exemplos

Corresponder documentos cujo `tags` campo contém exatamente a cadeia de caracteres "WiFi":

```text
tags/any(t: t eq 'wifi')
```

Corresponder documentos em que cada elemento do `ratings` campo cai entre 3 e 5, inclusive:

```text
ratings/all(r: r ge 3 and r le 5)
```

Corresponder documentos em que qualquer uma das coordenadas geográficas no `locations` campo esteja dentro do polígono determinado:

```text
locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))
```

Corresponder documentos onde o `rooms` campo está vazio:

```text
not rooms/any()
```

Corresponder documentos em que para todas as salas, o `rooms/amenities` campo conterá "TV" e `rooms/baseRate` menor que 100:

```text
rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)
```

## <a name="limitations"></a>Limitações

Nem todos os recursos de expressões de filtro estão disponíveis no corpo de uma expressão lambda. As limitações são diferentes dependendo do tipo de dados do campo de coleção que você deseja filtrar. A tabela a seguir resume as limitações.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Para obter mais detalhes sobre essas limitações, bem como exemplos, consulte [solução de problemas de filtros de coleta no Azure pesquisa cognitiva](search-query-troubleshoot-collection-filters.md). Para obter informações mais detalhadas sobre por que essas limitações existem, consulte [noções básicas sobre filtros de coleta no Azure pesquisa cognitiva](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Próximas etapas  

- [Filtros no Azure Pesquisa Cognitiva](search-filters.md)
- [Visão geral da linguagem de expressão OData para Azure Pesquisa Cognitiva](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;API REST do Azure Pesquisa Cognitiva&#41;](/rest/api/searchservice/Search-Documents)