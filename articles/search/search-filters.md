---
title: Filtros de escopo de resultados da pesquisa em um índice – Azure Search
description: Filtre por identidade de segurança do usuário, idioma, localização geográfica ou valores numéricos para reduzir os resultados da pesquisa em consultas no Azure Search, um serviço de pesquisa de nuvem hospedado no Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: a9e8d2cbc067fd92208fac778ba17c58bdc7a5e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61289579"
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

+ O parâmetro `$select` é usado para especificar quais campos serão incluídos em um conjunto de resultados, reduzindo efetivamente a resposta antes de enviá-la para o aplicativo de chamada. Esse parâmetro não refina a consulta nem reduzir o conjunto de documentos, mas, se uma resposta granular for sua meta, este parâmetro é uma opção a ser considerada. 

Para obter mais informações sobre um parâmetro, consulte [Pesquisar Documentos > Solicitar > Parâmetros de consulta](https://docs.microsoft.com/rest/api/searchservice/search-documents#request).


## <a name="filters-in-the-query-pipeline"></a>Filtros no pipeline de consulta

No momento da consulta, um analisador de filtro aceita critérios como entrada, converte a expressão em expressões Boolianas atômicas e cria uma árvore de filtro, que é avaliada em relação aos campos que podem ser filtrados em um índice.  

A filtragem ocorre antes da pesquisa, qualificando quais documentos devem ser incluídos no processamento de downstream para recuperação de documentos e a relevância de pontuação de qualificação. Quando combinado com uma cadeia de caracteres de pesquisa, o filtro efetivamente reduz a área da superfície da operação de pesquisa subsequente. Quando usado sozinho (por exemplo, quando a cadeia de caracteres de consulta estiver vazio em `search=*`), os critérios de filtro são a única entrada. 

## <a name="filter-definition"></a>Definição de filtro

Os filtros são expressões de OData, articuladas usando um [subconjunto da sintaxe do OData V4 com suporte no Azure Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Você pode especificar um filtro para cada operação de **pesquisa**, mas o próprio filtro pode incluir vários campos, vários critérios e, se você usar uma função **ismatch**, várias expressões. Em uma expressão de filtro de várias partes, você pode especificar predicados em qualquer ordem. Não há nenhum ganho significativo no desempenho se você tentar reorganizar predicados em uma determinada sequência.

O limite fixo em uma expressão de filtro é o limite máximo para a solicitação. Toda a solicitação, incluindo o filtro, pode ter um máximo de 16 MB para POST ou 8 KB para GET. Os limites flexíveis se correlacionam com o número de cláusulas na sua expressão de filtro. Uma boa regra prática é que, se você tiver centenas de cláusulas, você correrá o risco de atingir o limite. É recomendável criar seu aplicativo de forma que ele não gere filtros de tamanho ilimitado.

Os exemplos a seguir representam as definições de filtro de um modelo em várias APIs.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2017-11-11

# Option 2: Use filter for POST and pass it in the header
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2017-11-11
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

```

## <a name="filter-design-patterns"></a>Padrões de design de filtro

Os exemplos a seguir ilustram vários padrões de design para cenários de filtro. Para obter mais ideias, consulte [Sintaxe de expressão do OData > Exemplos](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples).

+ **$filter** autônomo, sem uma cadeia de consulta, útil quando a expressão de filtro puder qualificar totalmente documentos de interesse. Sem uma cadeia de caracteres de consulta, não há nenhuma análise linguística ou lexical, nenhuma pontuação e nenhuma classificação. Observe que a cadeia de caracteres de pesquisa está vazia.

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ A combinação de cadeia de caracteres de consulta e **$filter**, em que o filtro cria o subconjunto e a cadeia de caracteres de consulta fornece as entradas do termo de pesquisa de texto completo no subconjunto filtrado. Usar um filtro com uma cadeia de caracteres de consulta é o padrão mais comum de código.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ Consultas composta, separadas por "or", cada uma com seus próprios critérios de filtro (por exemplo, “beagles" em "dog" ou "siamese" em "cat"). Expressões OR são avaliadas individualmente, com respostas de cada uma combinadas em uma resposta enviada de volta para o aplicativo de chamada. Esse padrão de design é obtido por meio da função search.ismatch. Você pode usar a versão sem pontuação (search.ismatch) ou a de pontuação (search.ismatchscoring).

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

Confira estes artigos para obter orientação abrangente sobre casos de uso específicos:

+ [Filtros da faceta](search-filters-facets.md)
+ [Filtros da linguagem](search-filters-language.md)
+ [Restrições de segurança](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Requisitos de campo para filtragem

Na API REST, filtrável está *habilitado* por padrão. Campos que podem ser filtrados aumentam o tamanho do índice. Certifique-se de definir `filterable=FALSE` para os campos que não planeja usar em um filtro. Para obter mais informações sobre como configurar definições de campo, consulte [Criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index).

No SDK .NET, filtrável está *desabilitado* por padrão. A API para definir a propriedade filtrável é [IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). No exemplo a seguir, está configurada na definição de campo BaseRate.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="reindexing-requirements"></a>Requisitos de reindexação

Se um campo for não filtrável e você desejar torná-lo filtrável, será preciso adicionar um novo campo ou recompilar o campo existente. Alterar uma definição de campo altera a estrutura física do índice. No Azure Search, todos os caminhos de acesso permitidos são indexados para velocidade de consulta rápida, o que exige uma recompilação das estruturas de dados quando as definições de campo são alteradas. 

Recriar campos individuais pode ser uma operação de baixo impacto, exigindo somente uma operação de mesclagem que envia a chave de documento existente e os valores associados ao índice, deixando o restante de cada documento intacto. Se você encontrar um requisito de recompilação, consulte [ações de indexação (carregar, mesclar, mergeOrUpload, excluir)](search-what-is-data-import.md#indexing-actions) para obter uma lista de opções.


## <a name="text-filter-fundamentals"></a>Conceitos básicos do filtro de texto

Filtros de texto são válidos para os campos de cadeia de caracteres, dos quais você deseja extrair um conjunto arbitrário de documentos com base nos valores no corpo de pesquisa.

Para filtros de texto compostos por cadeias de caracteres, não há nenhuma separação de palavras ou análise lexical, portanto, as comparações são apenas para correspondências exatas. Por exemplo, suponha que um campo *f* contenha "dia ensolarado", `$filter=f eq 'Sunny'`não corresponder, mas `$filter=f eq 'Sunny day'` corresponderá. 

Cadeias de caracteres de texto diferenciam minúsculas e maiúsculas. Não há nenhuma conversão para minúscula de palavras com letras maiúsculas: `$filter=f eq 'Sunny day'` não localizará “dia ensolarado”.


| Abordagem | DESCRIÇÃO | 
|----------|-------------|
| [search.in()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Uma função que fornece uma lista delimitada por vírgulas de cadeias de caracteres para um determinado campo. As cadeias de caracteres compõem os critérios de filtro, que são aplicados a todos os campos no escopo da consulta. <br/><br/>`search.in(f, ‘a, b, c’)` é semanticamente equivalente a `f eq ‘a’ or f eq ‘b’ or f eq ‘c’`, exceto que é executado com mais rapidez quando a lista de valores for grande.<br/><br/>Recomendamos a função **search.in** para [filtros de segurança](search-security-trimming-for-azure-search.md) e para todos os filtros compostos por um texto bruto a ser correspondido nos valores em um determinado campo. Essa abordagem é projetada para velocidade. Você pode esperar o tempo de resposta de subsegundos para centenas ou milhares de valores. Embora não haja nenhum limite explícito no número de itens que você pode passar para a função, a latência aumenta proporcionalmente ao número de cadeias de caracteres que você fornecer. | 
| [search.ismatch()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Uma função que permite a combinação de operações de pesquisa de texto completo com operações de filtro estritamente booliano na mesma expressão de filtro. Ela permite várias combinações de filtro e consulta em uma solicitação. Você também pode usá-la para um filtro de *contém* para filtrar em uma cadeia de caracteres parcial dentro de uma cadeia de caracteres maior. |  
| [$filter=field operator string](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Uma expressão definida pelo usuário é composta por campos, operadores e valores. | 

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

Para trabalhar com mais exemplos, consulte [Sintaxe de expressão de filtro OData > Exemplos](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples).

## <a name="see-also"></a>Consulte também

+ [Como funciona a pesquisa de texto completo no Azure Search](search-lucene-query-architecture.md)
+ [API REST para pesquisar documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Sintaxe de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Tipos de dados com suporte](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
