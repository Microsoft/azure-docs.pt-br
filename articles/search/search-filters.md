---
title: Filtrar nos resultados da pesquisa
titleSuffix: Azure Cognitive Search
description: Filtre por identidade de segurança do usuário, idioma, localização geográfica ou valores numéricos para reduzir os resultados da pesquisa em consultas no Azure Pesquisa Cognitiva, um serviço de pesquisa de nuvem hospedado no Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: a5c8f835d44896a452a945614332dcbc25ca8bb8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694420"
---
# <a name="filters-in-azure-cognitive-search"></a>Filtros no Azure Pesquisa Cognitiva 

Um *filtro* fornece critérios baseados em valor para a seleção de documentos usados em uma consulta. Um filtro pode ser um valor único ou uma [expressão de filtro](search-query-odata-filter.md)OData. Em contraste com a pesquisa de texto completo, um valor ou expressão de filtro só retorna uma correspondência estrita.

Algumas experiências de pesquisa, como [navegação facetada](search-filters-facets.md), dependem de filtros como parte da implementação, mas você pode usar filtros sempre que desejar fazer o escopo de uma consulta para valores específicos. Se, em vez disso, o objetivo é delimitar uma consulta a campos específicos, há métodos alternativos, descritos abaixo.

## <a name="when-to-use-a-filter"></a>Quando usar um filtro

Os filtros são fundamentais para as várias experiências de pesquisa, incluindo "Localizar próximo a mim", navegação facetada e filtros de segurança que mostram somente os documentos que um usuário tem permissão para ver. Se você implementar qualquer uma dessas experiências, um filtro será necessário. É o filtro anexado à consulta de pesquisa que fornece as coordenadas de localização geográfica, a categoria de faceta selecionada pelo usuário ou a identificação de segurança do solicitante.

Os cenários comuns incluem o seguinte:

+ Resultados da pesquisa de fatia com base no conteúdo do índice. Dado um esquema com localização, categorias e comodidades do Hotel, você pode criar um filtro para corresponder explicitamente nos critérios (em Seattle, na água, com uma exibição). 

+ A implementação de uma experiência de pesquisa vem com um requisito de filtro:

  + [A navegação facetada](search-faceted-navigation.md) usa um filtro para devolver a categoria de faceta selecionada pelo usuário.
  + A pesquisa de área geográfica usa um filtro para passar as coordenadas do local atual em aplicativos "Localizar próximo a mim". 
  + Os [filtros de segurança](search-security-trimming-for-azure-search.md) passam identificadores de segurança como critérios de filtro, em que uma correspondência no índice serve como um proxy para direitos de acesso ao documento.

+ Faça uma "pesquisa de números". Os campos numéricos são recuperáveis e podem aparecer nos resultados da pesquisa, mas não são pesquisáveis (sujeitos à pesquisa de texto completo) individualmente. Se precisar de critérios de seleção com base em dados numéricos, use um filtro.

### <a name="alternative-methods-for-reducing-scope"></a>Métodos alternativos para reduzir o escopo

Se desejar um efeito de estreitamento nos resultados da pesquisa, os filtros não serão sua única opção. Estas alternativas podem ser mais adequadas, dependendo de seu objetivo:

+ `searchFields` o parâmetro de consulta restringe a pesquisa a campos específicos. Por exemplo, se o índice fornece campos separados para descrições em inglês e espanhol, você pode usar searchFields para direcionar quais campos serão usados para pesquisa de texto completo. 

+ O parâmetro `$select` é usado para especificar quais campos serão incluídos em um conjunto de resultados, reduzindo efetivamente a resposta antes de enviá-la para o aplicativo de chamada. Esse parâmetro não refina a consulta ou reduz a coleção de documentos, mas se uma resposta menor for sua meta, esse parâmetro será uma opção a ser considerada. 

Para obter mais informações sobre um parâmetro, consulte [Pesquisar Documentos > Solicitar > Parâmetros de consulta](/rest/api/searchservice/search-documents#query-parameters).

## <a name="how-filters-are-executed"></a>Como os filtros são executados

No momento da consulta, um analisador de filtro aceita critérios como entrada, converte a expressão em expressões booleanas atômicas representadas como uma árvore e, em seguida, avalia a árvore de filtro sobre campos filtráveis em um índice.

A filtragem ocorre em conjunto com a pesquisa, qualificando quais documentos incluir no processamento de downstream para a classificação de documentos e a pontuação de relevância. Quando emparelhado com uma cadeia de caracteres de pesquisa, o filtro reduz efetivamente o conjunto de recuperação da operação de pesquisa subsequente. Quando usado sozinho (por exemplo, quando a cadeia de caracteres de consulta estiver vazio em `search=*`), os critérios de filtro são a única entrada. 

## <a name="defining-filters"></a>Definir filtros

Filtros são expressões OData, articuladas na [sintaxe de filtro](search-query-odata-filter.md) com suporte pelo pesquisa cognitiva.

Você pode especificar um filtro para cada operação de **pesquisa** , mas o próprio filtro pode incluir vários campos, vários critérios e, se você usar uma função **IsMatch** , várias expressões de pesquisa de texto completo. Em uma expressão de filtro de várias partes, você pode especificar predicados em qualquer ordem (sujeito às regras de precedência de operador). Não há nenhum ganho significativo no desempenho se você tentar reorganizar predicados em uma determinada sequência.

Um dos limites em uma expressão de filtro é o limite de tamanho máximo da solicitação. Toda a solicitação, incluindo o filtro, pode ter um máximo de 16 MB para POST ou 8 KB para GET. Também há um limite no número de cláusulas em sua expressão de filtro. Uma boa regra prática é que, se você tiver centenas de cláusulas, você correrá o risco de atingir o limite. É recomendável criar seu aplicativo de forma que ele não gere filtros de tamanho ilimitado.

Os exemplos a seguir representam as definições de filtro de um modelo em várias APIs.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2020-06-30&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Padrões de uso de filtro

Os exemplos a seguir ilustram vários padrões de uso para cenários de filtro. Para obter mais ideias, consulte [Sintaxe de expressão do OData > Exemplos](./search-query-odata-filter.md#examples).

+ **$filter** autônomo, sem uma cadeia de consulta, útil quando a expressão de filtro puder qualificar totalmente documentos de interesse. Sem uma cadeia de caracteres de consulta, não há nenhuma análise linguística ou lexical, nenhuma pontuação e nenhuma classificação. Observe que a cadeia de caracteres de pesquisa é apenas um asterisco, o que significa "corresponder todos os documentos".

  ```http
  {
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu"
  }
  ```

+ A combinação de cadeia de caracteres de consulta e **$filter**, em que o filtro cria o subconjunto e a cadeia de caracteres de consulta fornece as entradas do termo de pesquisa de texto completo no subconjunto filtrado. A adição de termos (percorrendo teatros de distância) apresenta as pontuações de pesquisa nos resultados, onde os documentos que melhor correspondam aos termos são classificados como mais altos. O uso de um filtro com uma cadeia de caracteres de consulta é o padrão de uso mais comum.

  ```http
  {
    "search": "walking distance theaters",
    "filter": "Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'"
  }

+ Compound queries, separated by "or", each with its own filter criteria (for example, 'beagles' in 'dog' or 'siamese' in 'cat'). Expressions combined with `or` are evaluated individually, with the union of documents matching each expression sent back in the response. This usage pattern is achieved through the `search.ismatchscoring` function. You can also use the non-scoring version, `search.ismatch`.

   ```
   # <a name="match-on-hostels-rated-higher-than-4-or-5-star-motels"></a>Correspondência em Hostels classificado como maior que 4 ou 5-estrelas motéis.
   $filter = Search. ismatchscoring (' Hostel ') e a classificação GE 4 ou Search. ismatchscoring (' motel ') e a classificação EQ 5

   # <a name="match-on-luxury-or-high-end-in-the-description-field-or-on-category-exactly-equal-to-luxury"></a>Corresponder ao ' luxo ' ou ' high-end ' no campo de descrição ou na categoria exatamente igual a ' luxo '.
   $filter = Search. ismatchscoring (' luxo | high-end ', ' description ') ou category EQ ' luxo ' &$count = true
   ```

  It is also possible to combine full-text search via `search.ismatchscoring` with filters using `and` instead of `or`, but this is functionally equivalent to using the `search` and `$filter` parameters in a search request. For example, the following two queries produce the same result:

  ```
  $filter = Search. ismatchscoring (' Pool ') e a classificação GE 4

  Search = pool&$filter = classificação GE 4
  ```

Follow up with these articles for comprehensive guidance on specific use cases:

+ [Facet filters](search-filters-facets.md)
+ [Language filters](search-filters-language.md)
+ [Security trimming](search-security-trimming-for-azure-search.md) 

## Field requirements for filtering

In the REST API, filterable is *on* by default for simple fields. Filterable fields increase index size; be sure to set `"filterable": false` for fields that you don't plan to actually use in a filter. For more information about settings for field definitions, see [Create Index](/rest/api/searchservice/create-index).

In the .NET SDK, the filterable is *off* by default. You can make a field filterable by setting the [IsFilterable property](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) of the corresponding [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) object to `true`. In the example below, the attribute is set on the `BaseRate` property of a model class that maps to the index definition.

```csharp
[IsFilterable, IsSortable, IsFacetable]
public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Tornando um campo existente filtrável

Você não pode modificar os campos existentes para torná-los filtráveis. Em vez disso, você precisa adicionar um novo campo ou recompilar o índice. Para obter mais informações sobre como recriar um índice ou repopular os campos, consulte [como recriar um índice de pesquisa cognitiva do Azure](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Conceitos básicos do filtro de texto

Os filtros de texto correspondem aos campos de cadeia de caracteres em cadeias literais que você fornece no filtro: `$filter=Category eq 'Resort and Spa'`

Diferentemente da pesquisa de texto completo, não há nenhuma análise lexical ou separação de palavras para filtros de texto, portanto, as comparações são apenas para correspondências exatas. Por exemplo, suponha que um campo *f* contenha "dia ensolarado", `$filter=f eq 'Sunny'` não corresponde, mas `$filter=f eq 'sunny day'` vai. 

Cadeias de caracteres de texto diferenciam minúsculas e maiúsculas. Não há letras minúsculas de palavras com letras maiúsculas: `$filter=f eq 'Sunny day'` não encontrará "dia ensolarado".

### <a name="approaches-for-filtering-on-text"></a>Abordagens para filtragem de texto

| Abordagem | Descrição | Quando usar |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | Uma função que corresponde a um campo em uma lista delimitada de cadeias de caracteres. | Recomendado para [filtros de segurança](search-security-trimming-for-azure-search.md) e para todos os filtros em que muitos valores de texto brutos precisam ser correspondidos com um campo de cadeia de caracteres. A função **Search.in** é projetada para velocidade e é muito mais rápida do que comparar explicitamente o campo com cada cadeia de caracteres usando `eq` e `or` . | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Uma função que permite a combinação de operações de pesquisa de texto completo com operações de filtro estritamente booliano na mesma expressão de filtro. | Use **Search. IsMatch** (ou seu equivalente de pontuação, **Search. ismatchscoring**) quando desejar várias combinações de filtro de pesquisa em uma solicitação. Você também pode usá-la para um filtro de *contém* para filtrar em uma cadeia de caracteres parcial dentro de uma cadeia de caracteres maior. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Uma expressão definida pelo usuário é composta por campos, operadores e valores. | Use isso quando desejar localizar correspondências exatas entre um campo de cadeia de caracteres e um valor de cadeia de caracteres. |

## <a name="numeric-filter-fundamentals"></a>Conceitos básicos do filtro numérico

Campos numéricos não são `searchable` no contexto da pesquisa de texto completo. Somente cadeias de caracteres estão sujeitas à pesquisa de texto completo. Por exemplo, se você inserir 99,99 como um termo de pesquisa, você não receberá itens com preços de $ 99,99. Em vez disso, você verá os itens que têm o número 99 nos campos de cadeia de caracteres do documento. Portanto, se você tiver dados numéricos, a suposição é que você os usará para filtros, incluindo intervalos, facetas, grupos e assim por diante. 

Documentos que contêm campos numéricos (preço, tamanho, SKU, ID) fornecem esses valores nos resultados da pesquisa se o campo estiver marcado como `retrievable`. O ponto aqui é que a pesquisa de texto completo em si não é aplicável a tipos de campos numéricos.

## <a name="next-steps"></a>Próximas etapas

Primeiro, tente o **Search Explorer** no portal para enviar consultas com parâmetros **$filter**. O [índice real-estate-sample](search-get-started-portal.md) fornece resultados interessantes para as seguintes consultas filtradas quando você o cola na barra de pesquisa:

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

Para trabalhar com mais exemplos, consulte [Sintaxe de expressão de filtro OData > Exemplos](./search-query-odata-filter.md#examples).

## <a name="see-also"></a>Confira também

+ [Como funciona a pesquisa de texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
+ [API REST para pesquisar documentos](/rest/api/searchservice/search-documents)
+ [Sintaxe de consulta simples](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Sintaxe de consulta Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Tipos de dados com suporte](/rest/api/searchservice/supported-data-types)