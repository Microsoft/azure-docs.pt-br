---
title: Referência da função de pesquisa de texto completo OData
titleSuffix: Azure Cognitive Search
description: Funções de pesquisa de texto completo OData, Search. IsMatch e Search. ismatchscoring, no Azure Pesquisa Cognitiva consultas.
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
ms.openlocfilehash: 78f9e4d8fa80fdf74bdb5cd79f4489d12696fcc2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935782"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>Funções de pesquisa de texto completo OData no Azure Pesquisa Cognitiva- `search.ismatch` e `search.ismatchscoring`

O Azure Pesquisa Cognitiva dá suporte à pesquisa de texto completo no contexto de [expressões de filtro OData](query-odata-filter-orderby-syntax.md) por meio das `search.ismatch` `search.ismatchscoring` funções e. Essas funções permitem combinar a pesquisa de texto completo com filtragem booliana estrita de maneiras que não são possíveis apenas usando o parâmetro de nível superior `search` da API de [pesquisa](/rest/api/searchservice/search-documents).

> [!NOTE]
> As `search.ismatch` `search.ismatchscoring` funções e têm suporte apenas em filtros na [API de pesquisa](/rest/api/searchservice/search-documents). Eles não têm suporte nas APIs [sugerir](/rest/api/searchservice/suggestions) ou [preenchimento automático](/rest/api/searchservice/autocomplete) .

## <a name="syntax"></a>Sintaxe

O seguinte EBNF ([formulário estendido Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define a gramática das `search.ismatch` `search.ismatchscoring` funções e:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe do OData para Pesquisa Cognitiva do Azure](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Consulte [referência de sintaxe de expressão OData para pesquisa cognitiva do Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

### <a name="searchismatch"></a>Pesquisar. IsMatch

A `search.ismatch` função avalia uma consulta de pesquisa de texto completo como parte de uma expressão de filtro. Os documentos que correspondem à consulta da pesquisa serão retornados no conjunto de resultados. As seguintes sobrecargas desta função estão disponíveis:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Os parâmetros são definidos na tabela a seguir:

| Nome do parâmetro | Tipo | Descrição |
| --- | --- | --- |
| `search` | `Edm.String` | A consulta de pesquisa (em sintaxe de consulta de Lucene [simples](query-simple-syntax.md) ou [completa](query-lucene-syntax.md) ). |
| `searchFields` | `Edm.String` | Lista separada por vírgulas de campos pesquisáveis para pesquisa; o padrão é todos os campos pesquisáveis no índice. Ao usar a [pesquisa de campo](query-lucene-syntax.md#bkmk_fields) no `search` parâmetro, os especificadores de campo na consulta Lucene substituem todos os campos especificados nesse parâmetro. |
| `queryType` | `Edm.String` | `'simple'` ou `'full'` ; o padrão é `'simple'` . Especifica qual idioma de consulta foi usado no parâmetro `search`. |
| `searchMode` | `Edm.String` | `'any'` ou `'all'` , usa como padrão `'any'` . Indica se um ou todos os termos de pesquisa no `search` parâmetro devem ser correspondidos a fim de contar o documento como uma correspondência. Ao usar os [operadores boolianos do Lucene](query-lucene-syntax.md#bkmk_boolean) no `search` parâmetro, eles terão precedência sobre esse parâmetro. |

Todos os parâmetros acima são equivalentes aos [parâmetros de solicitação de pesquisa correspondentes na API de pesquisa](/rest/api/searchservice/search-documents).

A `search.ismatch` função retorna um valor do tipo `Edm.Boolean` , que permite que você o redija com outras subexpressãos de filtro usando os [operadores lógicos](search-query-odata-logical-operators.md)boolianos.

> [!NOTE]
> O Azure Pesquisa Cognitiva não dá suporte ao uso `search.ismatch` de `search.ismatchscoring` expressões lambda ou internas. Isso significa que não é possível gravar filtros em coleções de objetos que podem correlacionar correspondências de pesquisa de texto completo com correspondências de filtro estritas no mesmo objeto. Para obter mais detalhes sobre essa limitação, bem como exemplos, consulte [solução de problemas de filtros de coleta no Azure pesquisa cognitiva](search-query-troubleshoot-collection-filters.md). Para obter informações mais detalhadas sobre o motivo da existência dessa limitação, consulte [noções básicas sobre filtros de coleta no Azure pesquisa cognitiva](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>Pesquisar. ismatchscoring

A `search.ismatchscoring` função, como a `search.ismatch` função, retorna `true` para documentos que correspondem à consulta de pesquisa de texto completo passada como um parâmetro. A diferença entre eles é que a pontuação de relevância dos documentos que correspondem à consulta `search.ismatchscoring` também contribui para a pontuação geral do documento, enquanto que no caso de `search.ismatch`, a pontuação de documento não se altera. As seguintes sobrecargas dessa função estão disponíveis com parâmetros idênticos aos de `search.ismatch`:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

As `search.ismatch` funções e `search.ismatchscoring` podem ser usadas na mesma expressão de filtro.

## <a name="examples"></a>Exemplos

Localizar documentos com as palavras "orla marítima". Essa consulta de filtro é idêntica a uma [solicitação de pesquisa](/rest/api/searchservice/search-documents) com `search=waterfront`.

```odata-filter-expr
    search.ismatchscoring('waterfront')
```

Localizar documentos com a palavra "hostel" e classificação maior ou igual a 4, ou documentos com a palavra "motel" e classificação igual a 5. Observe que essa solicitação não pode ser expressa sem a função `search.ismatchscoring`.

```odata-filter-expr
    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5
```

Localizar documentos sem a palavra "luxo".

```odata-filter-expr
    not search.ismatch('luxury')
```

Localizar documentos com a frase "vista para o mar" ou classificação igual a 5. A consulta `search.ismatchscoring` será executada apenas em relação aos campos `HotelName` e `Rooms/Description`.

Os documentos que corresponderem somente à segunda cláusula da disjunção serão retornados também para hotéis com `Rating` igual a 5. Para tornar claro que esses documentos não corresponderam a nenhuma das partes pontuadas da expressão, eles serão retornados com pontuação igual a zero.

```odata-filter-expr
    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5
```

Localize documentos em que os termos "Hotel" e "aeroporto" estejam dentro de 5 palavras entre si na descrição do Hotel, e onde o fumante não é permitido em pelo menos algumas das salas. Essa consulta usa a [linguagem de consulta Lucene completa](query-lucene-syntax.md).

```odata-filter-expr
    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)
```

## <a name="next-steps"></a>Próximas etapas  

- [Filtros no Azure Pesquisa Cognitiva](search-filters.md)
- [Visão geral da linguagem de expressão OData para Azure Pesquisa Cognitiva](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;API REST do Azure Pesquisa Cognitiva&#41;](/rest/api/searchservice/Search-Documents)