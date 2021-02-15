---
title: Referência do OData Select
titleSuffix: Azure Cognitive Search
description: Sintaxe e referência de linguagem para a seleção explícita de campos a serem retornados nos resultados da pesquisa das consultas de Pesquisa Cognitiva do Azure.
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
ms.openlocfilehash: 54b6ae227fc4b3b951717799660543c02874dda0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88919651"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>Sintaxe de $select OData no Azure Pesquisa Cognitiva

 Você pode usar o [parâmetro **$Select** OData](query-odata-filter-orderby-syntax.md) para escolher quais campos incluir nos resultados da pesquisa do Azure pesquisa cognitiva. Este artigo descreve a sintaxe de **$Select** em detalhes. Para obter mais informações gerais sobre como usar **$Select** ao apresentar os resultados da pesquisa, consulte [como trabalhar com os resultados da pesquisa no Azure pesquisa cognitiva](search-pagination-page-layout.md).

## <a name="syntax"></a>Sintaxe

O parâmetro **$Select** determina quais campos de cada documento são retornados no conjunto de resultados da consulta. O seguinte EBNF ([formulário estendido Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática para o parâmetro **$Select** :

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe do OData para Pesquisa Cognitiva do Azure](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Consulte [referência de sintaxe de expressão OData para pesquisa cognitiva do Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

O parâmetro **$Select** é fornecido em duas formas:

1. Uma única estrela ( `*` ), indicando que todos os campos recuperáveis devem ser retornados ou
1. Uma lista separada por vírgulas de caminhos de campo, identificando quais campos devem ser retornados.

Ao usar o segundo formulário, você só pode especificar campos recuperáveis na lista.

Se você listar um campo complexo sem especificar os subcampos explicitamente, todos os subcampos recuperáveis serão incluídos no conjunto de resultados da consulta. Por exemplo, suponha que o índice tenha um `Address` campo `Street` com `City` `Country` os subcampos, e que são todos recuperáveis. Se você especificar `Address` em **$Select**, os resultados da consulta incluirão todos os três subcampos.

## <a name="examples"></a>Exemplos

Inclua os `HotelId` `HotelName` campos, e `Rating` de nível superior nos resultados, bem como o `City` subcampo de `Address` :

```odata-filter-expr
    $select=HotelId, HotelName, Rating, Address/City
```

Um resultado de exemplo pode ser assim:

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "City": "New York"
  }
}
```

Inclua o `HotelName` campo de nível superior nos resultados, bem como todos os subcampos de e os subcampos `Address` `Type` e `BaseRate` de cada objeto na `Rooms` coleção:

```odata-filter-expr
    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate
```

Um resultado de exemplo pode ser assim:

```json
{
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY",
    "Country": "USA",
    "PostalCode": "10022"
  },
  "Rooms": [
    {
      "Type": "Budget Room",
      "BaseRate": 9.69
    },
    {
      "Type": "Budget Room",
      "BaseRate": 8.09
    }
  ]
}
```

## <a name="next-steps"></a>Próximas etapas  

- [Como trabalhar com os resultados da pesquisa no Azure Pesquisa Cognitiva](search-pagination-page-layout.md)
- [Visão geral da linguagem de expressão OData para Azure Pesquisa Cognitiva](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;API REST do Azure Pesquisa Cognitiva&#41;](/rest/api/searchservice/Search-Documents)