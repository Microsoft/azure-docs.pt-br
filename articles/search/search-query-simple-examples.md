---
title: Usar sintaxe de consulta Lucene simples
titleSuffix: Azure Cognitive Search
description: Exemplos de consulta que demonstram a sintaxe simples para pesquisa de texto completo, pesquisa de filtro e pesquisa geográfica em um índice de Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 2abe19351c92bf9cea85c85dd55f47b5ee6d1625
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694029"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Usar a sintaxe de pesquisa "simples" no Azure Pesquisa Cognitiva

No Azure Pesquisa Cognitiva, a [sintaxe de consulta simples](query-simple-syntax.md) invoca o analisador de consulta padrão para pesquisa de texto completo. O analisador é rápido e manipula cenários comuns, incluindo pesquisa de texto completo, pesquisa filtrada e facetada e pesquisa de prefixo. Este artigo usa exemplos para ilustrar o uso de sintaxe simples em uma solicitação de [documentos de pesquisa (API REST)](/rest/api/searchservice/search-documents) .

> [!NOTE]
> Uma sintaxe de consulta alternativa é o [Lucene completo](query-lucene-syntax.md), dando suporte a estruturas de consulta mais complexas, como pesquisa difusa e curinga. Para obter mais informações e exemplos, consulte [usar a sintaxe Lucene completa](search-query-lucene-examples.md).

## <a name="hotels-sample-index"></a>Exemplo de índice de hotéis

As consultas a seguir são baseadas em hotéis-Sample-index, que você pode criar seguindo as instruções neste guia de [início rápido](search-get-started-portal.md).

Exemplos de consultas são articuladas usando a API REST e solicitações POST. Você pode colá-los e executá-los no [postmaster](search-get-started-rest.md) ou no [Visual Studio Code com a extensão pesquisa cognitiva](search-get-started-vs-code.md).

Os cabeçalhos de solicitação devem ter os seguintes valores:

| Chave | Valor |
|-----|-------|
| Tipo de conteúdo | aplicativo/json|
| chave de API  | `<your-search-service-api-key>`, uma consulta ou chave de administração |

Os parâmetros de URI devem incluir o ponto de extremidade do serviço de pesquisa com o nome do índice, as coleções de docs, o comando de pesquisa e a versão da API, semelhante ao exemplo a seguir:

```http
https://{{service-name}}.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
```

O corpo da solicitação deve ser formado como um JSON válido:

```json
{
    "search": "*",
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ "Search" definido como `*` é uma consulta não especificada, equivalente a NULL ou a uma pesquisa vazia. Ele não é especialmente útil, mas é a pesquisa mais simples que você pode fazer e mostra todos os campos recuperáveis no índice, com todos os valores.

+ "QueryType" definido como "Simple" é o padrão e pode ser omitido, mas está incluído para reforçar ainda mais que os exemplos de consulta neste artigo são expressos na sintaxe simples.

+ "Select" definido como uma lista delimitada por vírgulas de campos é usado para composição de resultado de pesquisa, incluindo apenas os campos que são úteis no contexto dos resultados da pesquisa.

+ "Count" retorna o número de documentos correspondentes aos critérios de pesquisa. Em uma cadeia de caracteres de pesquisa vazia, a contagem será todos os documentos no índice (50 no caso de hotéis-Sample-index).

## <a name="example-1-full-text-search"></a>Exemplo 1: pesquisa de texto completo

A pesquisa de texto completo pode ser qualquer número de termos autônomos ou frases delimitadas por aspas, com ou sem operadores boolianos. 

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "pool spa +airport",
    "searchMode": any,
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
}
```

Uma pesquisa de palavra-chave composta por termos ou frases importantes tendem a funcionar melhor. Os campos de cadeia de caracteres passam por análise de texto durante a indexação e a consulta, descartando palavras não essenciais como "The", "and", "It". Para ver como uma cadeia de caracteres de consulta é indexada no índice, passe a cadeia de caracteres em uma chamada de [análise de texto](/rest/api/searchservice/test-analyzer) para o índice.

O parâmetro "searchmode" controla a precisão e a RECALL. Se você quiser mais recall, use o valor padrão "any", que retorna um resultado se qualquer parte da cadeia de caracteres de consulta for correspondida. Se você favorecer a precisão, em que todas as partes da cadeia de caracteres devem ser correspondidas, altere searchmode para "All". Experimente a consulta acima de duas maneiras para ver como o searchmode altera o resultado.

A resposta para a consulta "pool de spa + aeroporto" deve ser semelhante ao exemplo a seguir, cortada para brevidade.

```json
"@odata.count": 6,
"value": [
    {
        "@search.score": 7.3617697,
        "HotelId": "21",
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown.",
        "Category": "Resort and Spa",
        "Tags": [
            "pool",
            "continental breakfast",
            "free parking"
        ]
    },
    {
        "@search.score": 2.5560288,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Description": "Newly Redesigned Rooms & airport shuttle.  Minutes from the airport, enjoy lakeside amenities, a resort-style pool & stylish new guestrooms with Internet TVs.",
        "Category": "Luxury",
        "Tags": [
            "24-hour front desk service",
            "continental breakfast",
            "free wifi"
        ]
    },
    {
        "@search.score": 2.2988036,
        "HotelId": "35",
        "HotelName": "Suites At Bellevue Square",
        "Description": "Luxury at the mall.  Located across the street from the Light Rail to downtown.  Free shuttle to the mall and airport.",
        "Category": "Resort and Spa",
        "Tags": [
            "continental breakfast",
            "air conditioning",
            "24-hour front desk service"
        ]
    }
```

Observe a pontuação de pesquisa na resposta. Essa é a pontuação de relevância da correspondência. Por padrão, um serviço de pesquisa retornará as principais correspondências 50 com base nessa Pontuação.

Pontuações uniformes de "1,0" ocorrem quando não há nenhuma classificação, porque a pesquisa não era uma pesquisa de texto completo ou porque nenhum critério foi fornecido. Por exemplo, em uma pesquisa vazia (Search = `*` ), as linhas retornam em ordem arbitrária. Quando você incluir critérios reais, verá as pontuações da pesquisa evoluir para valores significativos.

## <a name="example-2-look-up-by-id"></a>Exemplo 2: pesquisar por ID

Quando você retorna resultados da pesquisa em uma consulta, uma próxima etapa lógica é fornecer uma página de detalhes que inclua mais campos do documento. Este exemplo mostra como retornar um único documento usando o [documento de pesquisa](/rest/api/searchservice/lookup-document) passando a ID do documento.

```http
GET /indexes/hotels-sample-index/docs/41?api-version=2020-06-30
```

Todos os documentos possuem um identificador exclusivo. Se você estiver usando o portal, selecione a guia Índice de **índices** e, em seguida, examine as definições de campo para determinar qual campo é a chave. Usando REST, a chamada [obter índice](/rest/api/searchservice/get-index) retorna a definição do índice no corpo da resposta.

A resposta para a consulta acima consiste no documento cuja chave é 41. Qualquer campo marcado como "recuperável" na definição do índice pode ser retornado nos resultados da pesquisa e renderizado em seu aplicativo.

```json
{
    "HotelId": "41",
    "HotelName": "Ocean Air Motel",
    "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away.",
    "Description_fr": "L'hôtel front de mer surplombant la plage dispose de chambres avec balcon privé et 2 piscines intérieures et extérieures. Divers commerces et animations artistiques sont sur la promenade, à quelques pas.",
    "Category": "Budget",
    "Tags": [
        "pool",
        "air conditioning",
        "bar"
    ],
    "ParkingIncluded": true,
    "LastRenovationDate": "1951-05-10T00:00:00Z",
    "Rating": 3.5,
    "Location": {
        "type": "Point",
        "coordinates": [
            -157.846817,
            21.295841
        ],
        "crs": {
            "type": "name",
            "properties": {
                "name": "EPSG:4326"
            }
        }
    },
    "Address": {
        "StreetAddress": "1450 Ala Moana Blvd 2238 Ala Moana Ctr",
        "City": "Honolulu",
        "StateProvince": "HI",
        "PostalCode": "96814",
        "Country": "USA"
    },
```

## <a name="example-3-filter-on-text"></a>Exemplo 3: filtrar em texto

A [sintaxe de filtro](search-query-odata-filter.md) é uma expressão OData que você pode usar sozinho ou com "Search". Usado juntos, "Filter" é aplicado primeiro ao índice inteiro e, em seguida, a pesquisa é executada nos resultados do filtro. Os filtros, portanto, podem ser uma técnica útil para melhorar o desempenho da consulta, uma vez que reduzem o conjunto de documentos que a consulta de pesquisa precisa processar.

Os filtros podem ser definidos em qualquer campo marcado como "filtrável" na definição do índice. No caso de hotéis-Sample-index, os campos filtráveis incluem categoria, marcas, ParkingIncluded, classificação e a maioria dos campos de endereço.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "art tours",
    "queryType": "simple",
    "filter": "Category eq 'Resort and Spa'",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

A resposta para a consulta acima tem como escopo apenas os hotéis categorizados como "relatório e Spa", que incluem os termos "arte" ou "Tours". Nesse caso, há apenas uma correspondência.

```json
{
    "@search.score": 2.8576312,
    "HotelId": "31",
    "HotelName": "Santa Fe Stay",
    "Description": "Nestled on six beautifully landscaped acres, located 2 blocks from the Plaza. Unwind at the spa and indulge in art tours on site.",
    "Category": "Resort and Spa"
}
```

## <a name="example-4-filter-functions"></a>Exemplo 4: funções de filtro

As expressões de filtro podem incluir as [funções "Search. IsMatch" e "Search. ismatchscoring"](search-query-odata-full-text-search-functions.md), permitindo que você crie uma consulta de pesquisa dentro do filtro. Essa expressão de filtro usa um caractere curinga em *livre* para selecionar comodidades incluindo WiFi gratuito, estacionamento gratuito e assim por diante.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
  {
    "search": "",
    "filter": "search.ismatch('free*', 'Tags', 'full', 'any')",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
  }
```

A resposta para a consulta acima corresponde a 19 hotéis que oferecem comodidades livres. Observe que a pontuação de pesquisa é um "1,0" uniforme em todos os resultados. Isso ocorre porque a expressão de pesquisa é nula ou vazia, resultando em correspondências de filtro textual, mas sem pesquisa de texto completo. As pontuações de relevância são retornadas somente na pesquisa de texto completo. Se você estiver usando filtros sem "Pesquisar", verifique se tem campos classificável suficientes para poder controlar a classificação de pesquisa.

```json
"@odata.count": 19,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "31",
        "HotelName": "Santa Fe Stay",
        "Tags": [
            "view",
            "restaurant",
            "free parking"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "27",
        "HotelName": "Super Deluxe Inn & Suites",
        "Tags": [
            "bar",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Tags": [
            "continental breakfast",
            "free parking",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
```

## <a name="example-5-range-filters"></a>Exemplo 5: filtros de intervalo

Há suporte para filtragem de intervalo por meio de expressões de filtros para qualquer tipo de dados. Os exemplos a seguir ilustram intervalos numéricos e de cadeia de caracteres. Os tipos de dados são importantes em filtros de intervalo e funcionam melhor quando os dados numéricos estão em campos numéricos e dados de cadeia de caracteres em campos de cadeia de caracteres. Dados numéricos em campos de cadeia de caracteres não são adequados para intervalos porque cadeias numéricas não são comparáveis.

A consulta a seguir é um intervalo numérico. Em hotéis-Sample-index, o único campo numérico filtrável é classificação.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating ge 2 and Rating lt 4",
    "select": "HotelId, HotelName, Rating",
    "orderby": "Rating desc",
    "count": true
}
```

A resposta para essa consulta deve ser semelhante ao exemplo a seguir, cortada por brevidade.

```json
"@odata.count": 27,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "22",
        "HotelName": "Stone Lion Inn",
        "Rating": 3.9
    },
    {
        "@search.score": 1.0,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Rating": 3.8
    },
    {
        "@search.score": 1.0,
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Rating": 3.6
    }
```

A próxima consulta é um filtro de intervalo sobre um campo de cadeia de caracteres (Address/StateProvince):

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Address/StateProvince ge 'A*' and Address/StateProvince lt 'D*'",
    "select": "HotelId, HotelName, Address/StateProvince",
    "count": true
}
```

A resposta para essa consulta deve ser semelhante ao exemplo abaixo, cortada para fins de brevidade. Neste exemplo, não é possível classificar por StateProvince porque o campo não é atribuído como "classificável" na definição do índice.

```json
"@odata.count": 9,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "9",
        "HotelName": "Smile Hotel",
        "Address": {
            "StateProvince": "CA "
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Address": {
            "StateProvince": "CO"
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "7",
        "HotelName": "Countryside Resort",
        "Address": {
            "StateProvince": "CA "
        }
    },
```

## <a name="example-6-geo-search"></a>Exemplo 6: pesquisa geográfica

O índice de exemplo de hotéis inclui um campo de geo_location com coordenadas de latitude e longitude. Este exemplo usa a [função geo.distance](search-query-odata-geo-spatial-functions.md#examples) que filtra os documentos dentro da circunferência de um ponto inicial, até uma distância arbitrária (em quilômetros) que você fornece. Você pode ajustar o último valor na consulta (10) para reduzir ou aumentar a área da superfície da consulta.

```http
POST /indexes/v/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "geo.distance(Location, geography'POINT(-122.335114 47.612839)') le 10",
    "select": "HotelId, HotelName, Address/City, Address/StateProvince",
    "count": true
}
```

A resposta para essa consulta retorna todos os hotéis dentro de uma distância de 10 Kilometer das coordenadas fornecidas:

```json
{
    "@odata.count": 3,
    "value": [
        {
            "@search.score": 1.0,
            "HotelId": "45",
            "HotelName": "Arcadia Resort & Restaurant",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "24",
            "HotelName": "Gacc Capital",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "16",
            "HotelName": "Double Sanctuary Resort",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        }
    ]
}
```

## <a name="example-7-booleans-with-searchmode"></a>Exemplo 7: boolianos com searchMode

A sintaxe simples dá suporte a operadores boolianos na forma de caracteres ( `+, -, |` ) para dar suporte a and, or, e não à lógica de consulta. A pesquisa booliana se comporta como você pode esperar, com algumas exceções notáveis. 

Nos exemplos anteriores, o parâmetro "searchmode" foi introduzido como um mecanismo para influenciar a precisão e a RECALL, com "searchmode = any" favorecendo a RECALL (um documento que satisfaça qualquer um dos critérios é considerado uma correspondência) e "searchmode = All" favorecendo precisão (todos os critérios devem ser correspondidos em um documento). 

No contexto de uma pesquisa booleana, o padrão "searchmode = any" pode ser confuso se você estiver empilhando uma consulta com vários operadores e obtendo resultados mais amplos em vez de mais estreitos. Isso é particularmente verdadeiro, pois os resultados incluem todos os documentos "não contendo" um termo ou frase específica.

O exemplo a seguir ilustra esse cenário. Executar a consulta a seguir com searchmode (any), 42 documentos são retornados: aqueles que contêm o termo "restaurante", além de todos os documentos que não têm a frase "ar condicionado". 

Observe que não há nenhum espaço entre o operador booliano ( `-` ) e a frase "ar condicionado".

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "restaurant -\"air conditioning\"",
    "searchMode": "any",
    "searchFields": "Tags",
    "select": "HotelId, HotelName, Tags",
    "count": true
}
```

Alterar para "searchmode = All" impõe um efeito cumulativo em critérios e retorna um conjunto de resultados menor (7 Correspondências) que consiste em documentos que contêm o termo "restaurante", menos aqueles que contêm a frase "ar condicionado".

A resposta para essa consulta agora seria semelhante ao exemplo a seguir, cortada por brevidade.

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.5460577,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
    {
        "@search.score": 2.166792,
        "HotelId": "10",
        "HotelName": "Countryside Hotel",
        "Tags": [
            "24-hour front desk service",
            "coffee in lobby",
            "restaurant"
        ]
    },
```

## <a name="example-8-paging-results"></a>Exemplo 8: paginando resultados

Nos exemplos anteriores, você aprendeu sobre parâmetros que afetam a composição dos resultados da pesquisa, incluindo "Select" que determina quais campos estão em um resultado, ordens de classificação e como incluir uma contagem de todas as correspondências. Este exemplo é uma continuação da composição de resultado da pesquisa na forma de parâmetros de paginação que permitem a você colocar em lote o número de resultados que aparecem em qualquer página específica. 

Por padrão, um serviço de pesquisa retorna as principais correspondências 50. Para controlar o número de correspondências em cada página, use "Top" para definir o tamanho do lote e, em seguida, use "Skip" para pegar lotes subsequentes.

O exemplo a seguir usa um filtro e uma ordem de classificação no campo de classificação (a classificação é filtrável e classificável) porque é mais fácil ver os efeitos da paginação nos resultados classificados. Em uma consulta de pesquisa completa regular, as correspondências principais são classificadas e paginadas por " @search.score ".

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "count": true
}
```

A consulta localiza 21 documentos correspondentes, mas como você especificou "Top", a resposta retorna apenas as cinco principais correspondências, com classificações começando em 4,9 e terminando em 4,7 com "senhora do Lago B & B". 

Para obter os próximos 5, ignore o primeiro lote:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "skip": "5",
    "count": true
}
```

A resposta para o segundo lote ignora as cinco primeiras correspondências, retornando as próximas cinco, começando com "Pull'r Estalagem Motel". Para continuar com lotes adicionais, você deve manter "Top" em 5 e, em seguida, incrementar "Skip" por 5 em cada nova solicitação (ignorar = 5, ignorar = 10, ignorar = 15 e assim por diante).

```json
"value": [
    {
        "@search.score": 1.0,
        "HotelName": "Pull'r Inn Motel",
        "Rating": 4.7
    },
    {
        "@search.score": 1.0,
        "HotelName": "Sublime Cliff Hotel",
        "Rating": 4.6
    },
    {
        "@search.score": 1.0,
        "HotelName": "Antiquity Hotel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Nordick's Motel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Winter Panorama Resort",
        "Rating": 4.5
    }
]
```

## <a name="next-steps"></a>Próximas etapas

Agora que você tem alguma prática com a sintaxe de consulta básica, tente especificar consultas no código. Os links a seguir explicam como configurar consultas de pesquisa usando os SDKs do Azure.

+ [Consultar seu índice usando o SDK do .NET](search-get-started-dotnet.md)
+ [Consultar seu índice usando o SDK do Python](search-get-started-python.md)
+ [Consultar seu índice usando o SDK do JavaScript](search-get-started-javascript.md)

Referência de sintaxe adicional, arquitetura de consulta e exemplos podem ser encontrados nos links a seguir:

+ [Exemplos de consulta de sintaxe Lucene para criar consultas avançadas](search-query-lucene-examples.md)
+ [Como funciona a pesquisa de texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Sintaxe de consulta simples](query-simple-syntax.md)
+ [Sintaxe de consulta Lucene completa](query-lucene-syntax.md)
+ [Sintaxe de filtro](search-query-odata-filter.md)