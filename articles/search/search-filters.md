---
title: Filtros de escopo de resultados da pesquisa em um índice – Azure Search
description: Filtre por identidade de segurança do usuário, idioma, localização geográfica ou valores numéricos para reduzir os resultados da pesquisa em consultas no Azure Search, um serviço de pesquisa de nuvem hospedado no Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 4b5d198506473c598f058c881f781a06e191df88
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653434"
---
# <a name="filters-in-azure-search"></a>Filtros no Azure Search 

Um *filtro* fornece critérios para selecionar documentos usados em uma consulta do Azure Search. A pesquisa não filtrada inclui todos os documentos no índice. Um filtro restringe uma consulta de pesquisa a um subconjunto de documentos. Por exemplo, um filtro poderia restringir a pesquisa de texto completo a apenas os produtos com uma marca ou cor específica, com preços acima de um certo limite.

Algumas experiências de pesquisa impõem requisitos de filtro como parte da implementação, mas você pode usar filtros sempre que desejar restringir a pesquisa com critérios *baseados em valor* (restringindo a pesquisa ao tipo de produto "livros" para a categoria "não ficção" publicados por "Simon & Schuster").

Se sua meta é a pesquisa direcionada para *estruturas* de dados específicas (restringir a pesquisa a um campo de revisões de cliente), há métodos alternativos descritos abaixo.

## <a name="when-to-use-a-filter"></a>Quando usar um filtro

Os filtros são fundamentais para as várias experiências de pesquisa, incluindo "Localizar próximo a mim", navegação facetada e filtros de segurança que mostram somente os documentos que um usuário tem permissão para ver. Se você implementar qualquer uma dessas experiências, um filtro será necessário. É o filtro anexado à consulta de pesquisa que fornece as coordenadas de localização geográfica, a categoria de faceta selecionada pelo usuário ou a identificação de segurança do solicitante.

Os cenários de exemplo incluem o seguinte:

1. Use um filtro para dividir o índice com base nos valores de dados no índice. Com um esquema com cidade tipo de residência e comodidades, você pode criar um filtro para selecionar explicitamente os documentos que atendem aos critérios (em Seattle, condomínios, orla marítima). 

   A pesquisa de texto completo com as mesmas entradas geralmente produz resultados semelhantes, mas um filtro é mais preciso porque requer uma correspondência exata do termo do filtro em relação ao conteúdo no índice. 

2. Use um filtro se a experiência de pesquisa vier com um requisito de filtro:

   * [A navegação facetada](search-faceted-navigation.md) usa um filtro para devolver a categoria de faceta selecionada pelo usuário.
   * A pesquisa de área geográfica usa um filtro para passar as coordenadas do local atual em aplicativos "Localizar próximo a mim". 
   * Filtros de segurança passam identificadores de segurança como critérios de filtro, em que uma correspondência no índice serve como um proxy para direitos de acesso ao documento.

3. Use um filtro se desejar critérios de pesquisa em um campo numérico. 

   Campos numéricos são recuperáveis no documento e podem aparecer nos resultados da pesquisa, mas não são pesquisáveis (sujeito à pesquisa de texto completo) individualmente. Se precisar de critérios de seleção com base em dados numéricos, use um filtro.

### <a name="alternative-methods-for-reducing-scope"></a>Métodos alternativos para reduzir o escopo

Se desejar um efeito de estreitamento nos resultados da pesquisa, os filtros não serão sua única opção. Estas alternativas podem ser mais adequadas, dependendo de seu objetivo:

 + O parâmetro de consulta `searchFields` marca a pesquisa a campos específicos. Por exemplo, se o índice fornece campos separados para descrições em inglês e espanhol, você pode usar searchFields para direcionar quais campos serão usados para pesquisa de texto completo. 

+ O parâmetro `$select` é usado para especificar quais campos serão incluídos em um conjunto de resultados, reduzindo efetivamente a resposta antes de enviá-la para o aplicativo de chamada. Esse parâmetro não refinar a consulta ou reduzir o conjunto de documentos, mas se uma resposta menor for o seu objetivo, este parâmetro é uma opção a ser considerada. 

Para obter mais informações sobre um parâmetro, consulte [Pesquisar Documentos > Solicitar > Parâmetros de consulta](https://docs.microsoft.com/rest/api/searchservice/search-documents#request).


## <a name="how-filters-are-executed"></a>Como os filtros são executados

No momento da consulta, um analisador de filtro aceita critérios como entrada, converte a expressão em expressões Boolianas atômicas representadas como uma árvore e, em seguida, avalia a árvore de filtro em relação aos campos que podem ser filtrados em um índice.

A filtragem ocorre em tandem com a pesquisa, qualificando quais documentos devem ser incluídos no processamento de downstream para recuperação de documentos e pontuação de relevância. Quando combinado com uma cadeia de caracteres de pesquisa, o filtro efetivamente reduz o conjunto de cancelamento da operação de pesquisa subsequentes. Quando usado sozinho (por exemplo, quando a cadeia de caracteres de consulta estiver vazio em `search=*`), os critérios de filtro são a única entrada. 

## <a name="defining-filters"></a>Definir filtros

Os filtros são expressões de OData, articuladas usando um [subconjunto da sintaxe do OData V4 com suporte no Azure Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Você pode especificar um filtro para cada **pesquisa** operação, mas o próprio filtro pode incluir vários campos, vários critérios e, se você usar um **ismatch** função, várias expressões de pesquisa de texto completo. Em uma expressão de filtro de várias partes, você pode especificar predicados em qualquer ordem (sujeito às regras de precedência de operador). Não há nenhum ganho significativo no desempenho se você tentar reorganizar predicados em uma determinada sequência.

Um dos limites de uma expressão de filtro é o limite de tamanho máximo da solicitação. Toda a solicitação, incluindo o filtro, pode ter um máximo de 16 MB para POST ou 8 KB para GET. Também há um limite no número de cláusulas na sua expressão de filtro. Uma boa regra prática é que, se você tiver centenas de cláusulas, você correrá o risco de atingir o limite. É recomendável criar seu aplicativo de forma que ele não gere filtros de tamanho ilimitado.

Os exemplos a seguir representam as definições de filtro de um modelo em várias APIs.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2019-05-06

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "baseRate lt 150",
            Select = new[] { "hotelId", "description" }
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Padrões de uso de filtro

Os exemplos a seguir ilustram vários padrões de uso para cenários de filtro. Para obter mais ideias, consulte [Sintaxe de expressão do OData > Exemplos](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples).

+ **$filter** autônomo, sem uma cadeia de consulta, útil quando a expressão de filtro puder qualificar totalmente documentos de interesse. Sem uma cadeia de caracteres de consulta, não há nenhuma análise linguística ou lexical, nenhuma pontuação e nenhuma classificação. Observe que a cadeia de caracteres de pesquisa é apenas um asterisco, o que significa "corresponder a todos os documentos".

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ A combinação de cadeia de caracteres de consulta e **$filter**, em que o filtro cria o subconjunto e a cadeia de caracteres de consulta fornece as entradas do termo de pesquisa de texto completo no subconjunto filtrado. Usar um filtro com uma cadeia de caracteres de consulta é o padrão de uso mais comum.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ Consultas composta, separadas por "or", cada uma com seus próprios critérios de filtro (por exemplo, “beagles" em "dog" ou "siamese" em "cat"). Expressões são combinadas com `or` forem avaliadas individualmente, com a união de documentos que correspondem a cada expressão enviada de volta na resposta. Esse padrão de uso é obtido por meio de `search.ismatchscoring` função. Você também pode usar a versão sem pontuação, `search.ismatch`.

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

  Também é possível combinar a pesquisa de texto completo por meio `search.ismatchscoring` com filtros usando `and` em vez de `or`, mas isso é funcionalmente equivalente a usar os `search` e `$filter` parâmetros em uma solicitação de pesquisa. Por exemplo, duas consultas a seguir produzem o mesmo resultado:

  ```
  $filter=search.ismatchscoring('pool') and rating ge 4

  search=pool&$filter=rating ge 4
  ```

Confira estes artigos para obter orientação abrangente sobre casos de uso específicos:

+ [Filtros da faceta](search-filters-facets.md)
+ [Filtros da linguagem](search-filters-language.md)
+ [Restrições de segurança](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Requisitos de campo para filtragem

Na API REST, filtrável está *em* por padrão para campos simples. Campos que podem ser filtrados aumentam o tamanho do índice. Certifique-se de definir `"filterable": false` para os campos que não planeja usar em um filtro. Para obter mais informações sobre como configurar definições de campo, consulte [Criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index).

No SDK .NET, filtrável está *desabilitado* por padrão. Você pode tornar um campo que podem ser filtradas, definindo o [propriedade IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) de correspondente [campo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) objeto `true`. Você também pode fazer isso declarativamente usando o [IsFilterable atributo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). No exemplo a seguir, o atributo é definido no `BaseRate` propriedade de uma classe de modelo que é mapeado para a definição de índice.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Tornando um campo existente que podem ser filtradas

Você não pode modificar campos existentes para torná-las filtrável. Em vez disso, você precisa adicionar um novo campo ou recompilar o índice. Para obter mais informações sobre como recompilar um índice ou repopulação campos, consulte [como recriar um índice de Azure Search](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Conceitos básicos do filtro de texto

Filtros de texto correspondem a campos de cadeia de caracteres em cadeias de caracteres literais que você fornece no filtro. Ao contrário de pesquisa de texto completo, não há nenhuma separação de palavras para filtros de texto, ou análise lexical para que as comparações são apenas para correspondências exatas. Por exemplo, suponha que um campo *f* contenha "dia ensolarado" `$filter=f eq 'Sunny'` não corresponder, mas `$filter=f eq 'sunny day'` será. 

Cadeias de caracteres de texto diferenciam minúsculas e maiúsculas. Não há nenhum letras maiusculas e minúsculas de palavras com letras maiusculas: `$filter=f eq 'Sunny day'` não localizará "dia ensolarado".

### <a name="approaches-for-filtering-on-text"></a>Abordagens para filtragem no texto

| Abordagem | DESCRIÇÃO | Quando usar |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | Uma função que corresponde a um campo em relação a uma lista delimitada de cadeias de caracteres. | Recomendado para [filtros de segurança](search-security-trimming-for-azure-search.md) e para todos os filtros em que precisam de muitos valores de texto bruto a ser correspondido com um campo de cadeia de caracteres. O **search.in** função é projetada para velocidade e muito mais rápido que comparar explicitamente o campo em relação a cada cadeia de caracteres usando `eq` e `or`. | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Uma função que permite a combinação de operações de pesquisa de texto completo com operações de filtro estritamente booliano na mesma expressão de filtro. | Use **IsMatch** (ou seu equivalente de pontuação **ismatchscoring**) quando desejar que várias combinações de filtro de pesquisa em uma solicitação. Você também pode usá-la para um filtro de *contém* para filtrar em uma cadeia de caracteres parcial dentro de uma cadeia de caracteres maior. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Uma expressão definida pelo usuário é composta por campos, operadores e valores. | Use esta opção quando você deseja localizar correspondências exatas entre um campo de cadeia de caracteres e um valor de cadeia de caracteres. |

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

Para trabalhar com mais exemplos, consulte [Sintaxe de expressão de filtro OData > Exemplos](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples).

## <a name="see-also"></a>Consulte também

+ [Como funciona a pesquisa de texto completo no Azure Search](search-lucene-query-architecture.md)
+ [API REST para pesquisar documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Sintaxe de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Tipos de dados com suporte](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
