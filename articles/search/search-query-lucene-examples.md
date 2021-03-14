---
title: Usar sintaxe de consulta Lucene completa
titleSuffix: Azure Cognitive Search
description: Exemplos de consulta que demonstram a sintaxe de consulta Lucene para pesquisa difusa, pesquisa de proximidade, aumento de termo, pesquisa de expressão regular e pesquisas de curinga em um índice de Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 6213efb6ba14052c6f957a6d999f48f55f65186c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693553"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Use a sintaxe de pesquisa "completa" do Lucene (consultas avançadas no Pesquisa Cognitiva do Azure)

Ao construir consultas para Pesquisa Cognitiva do Azure, você pode substituir o [analisador de consulta simples](query-simple-syntax.md) padrão pelo [analisador de consulta Lucene](query-lucene-syntax.md) mais potente para formular expressões de consulta especializadas e avançadas.

O analisador Lucene dá suporte a formatos de consulta complexos, como consultas com escopo de campo, pesquisa difusa, pesquisa de curingas e de sufixo, pesquisa de proximidade, aumento de termo e pesquisa de expressão regular. A energia adicional vem com requisitos adicionais de processamento, portanto, você deve esperar um tempo de execução um pouco mais longo. Neste artigo, você pode percorrer exemplos demonstrando operações de consulta com base na sintaxe completa.

> [!Note]
> Muitas construções de consulta especializadas habilitadas por meio da sintaxe de consulta Lucene completa não são [texto analisado](search-lucene-query-architecture.md#stage-2-lexical-analysis), o que pode ser surpreendente caso espera-se a lematização. A análise lexical é realizada somente em termos completos (uma consulta de termo ou de frase). Tipos de consulta com termos incompletos (consulta de prefixo, consulta de curinga, consulta de regex, consulta difusa) são adicionados diretamente à árvore de consulta, ignorando o estágio de análise. A única transformação executada em termos de consulta parcial está em letras minúsculas. 
>

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
    "queryType": "full",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ "Search" definido como `*` é uma consulta não especificada, equivalente a NULL ou a uma pesquisa vazia. Ele não é especialmente útil, mas é a pesquisa mais simples que você pode fazer e mostra todos os campos recuperáveis no índice, com todos os valores.

+ "QueryType" definido como "Full" invoca o analisador de consulta Lucene completo e é necessário para essa sintaxe.

+ "Select" definido como uma lista delimitada por vírgulas de campos é usado para composição de resultado de pesquisa, incluindo apenas os campos que são úteis no contexto dos resultados da pesquisa.

+ "Count" retorna o número de documentos correspondentes aos critérios de pesquisa. Em uma cadeia de caracteres de pesquisa vazia, a contagem será todos os documentos no índice (50 no caso de hotéis-Sample-index).

## <a name="example-1-fielded-search"></a>Exemplo 1: pesquisa em campo

Escopo de pesquisa de campo individual, expressões de pesquisa inseridas em um campo específico. Este exemplo pesquisa nomes de Hotel com o termo "Hotel", mas não "Motel". Você pode especificar vários campos usando e. 

Ao usar essa sintaxe de consulta, você pode omitir o parâmetro "searchFields" quando os campos que você deseja consultar estão na própria expressão de pesquisa. Se você incluir "searchFields" na pesquisa de campo, o `fieldName:searchExpression` sempre terá precedência sobre "searchFields".

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:(hotel NOT motel) AND Category:'Resort and Spa'",
    "queryType": "full",
    "select": "HotelName, Category",
    "count": true
}
```

A resposta para essa consulta deve ser semelhante ao exemplo a seguir, filtrada em "Resort e Spa", retornando hotéis que incluem "Hotel" ou "Motel" no nome.

```json
"@odata.count": 4,
"value": [
    {
        "@search.score": 4.481559,
        "HotelName": "Nova Hotel & Spa",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.4524608,
        "HotelName": "King's Palace Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.3970203,
        "HotelName": "Triple Landscape Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.2953436,
        "HotelName": "Peaceful Market Hotel & Spa",
        "Category": "Resort and Spa"
    }
]
```

A expressão de pesquisa pode ser um único termo ou uma frase, ou uma expressão mais complexa entre parênteses, opcionalmente com operadores boolianos. Alguns exemplos incluem o seguinte:

+ `HotelName:(hotel NOT motel)`
+ `Address/StateProvince:("WA" OR "CA")`
+ `Tags:("free wifi" NOT "free parking") AND "coffee in lobby"`

Lembre-se de colocar uma frase entre aspas se desejar que ambas as cadeias de caracteres sejam avaliadas como uma única entidade, como nesse caso procurando por dois locais distintos no campo endereço/StateProvince. Dependendo do cliente, talvez seja necessário escapar ( `\` ) as aspas.

O campo especificado em `fieldName:searchExpression` deve ser um campo pesquisável. Consulte [criar índice (API REST)](/rest/api/searchservice/create-index) para obter detalhes sobre como as definições de campo são atribuídas.

## <a name="example-2-fuzzy-search"></a>Exemplo 2: pesquisa difusa

Correspondências de pesquisa difusa em termos semelhantes, incluindo palavras incorretas. Para fazer uma pesquisa difusa, acrescente o símbolo til `~` ao final de uma única palavra com um parâmetro opcional, um valor entre 0 e 2, que especifica a distância de edição. Por exemplo, `blue~` ou `blue~1` retornariam mar, amar e maré.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Tags:conserge~",
    "queryType": "full",
    "select": "HotelName, Category, Tags",
    "searchFields": "HotelName, Category, Tags",
    "count": true
}
```

A resposta para esta consulta é resolvida como "concierge" nos documentos correspondentes, cortada por brevidade:

```json
"@odata.count": 12,
"value": [
    {
        "@search.score": 1.1832147,
        "HotelName": "Secret Point Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "air conditioning",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1819803,
        "HotelName": "Twin Dome Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "free wifi",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1773309,
        "HotelName": "Smile Hotel",
        "Category": "Suite",
        "Tags": [
            "view",
            "concierge",
            "laundry service"
        ]
    },
```

Não há suporte diretamente para frases, mas você pode especificar uma correspondência difusa em cada termo de uma frase de várias partes, como `search=Tags:landy~ AND sevic~` .  Esta expressão de consulta localiza 15 correspondências no "serviço lavanderia".

> [!Note]
> As consultas difusas não são [analisadas](search-lucene-query-architecture.md#stage-2-lexical-analysis). Tipos de consulta com termos incompletos (consulta de prefixo, consulta de curinga, consulta de regex, consulta difusa) são adicionados diretamente à árvore de consulta, ignorando o estágio de análise. A única transformação executada em termos de consulta parcial é de letras minúsculas.
>

## <a name="example-3-proximity-search"></a>Exemplo 3: pesquisa de proximidade

A pesquisa de proximidade localiza os termos que estão próximos um do outro em um documento. Insira um símbolo til "~" no final de uma frase seguida pelo número de palavras que criam o limite de proximidade.

Essa consulta pesquisa os termos "Hotel" e "aeroporto" em 5 palavras uns dos outros em um documento. As aspas são de escape ( `\"` ) para preservar a frase:

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Description: \"hotel airport\"~5",
    "queryType": "full",
    "select": "HotelName, Description",
    "searchFields": "HotelName, Description",
    "count": true
}
```

A resposta para essa consulta deve ser semelhante ao exemplo a seguir:

```json
"@odata.count": 2,
"value": [
    {
        "@search.score": 0.6331726,
        "HotelName": "Trails End Motel",
        "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
    },
    {
        "@search.score": 0.43032226,
        "HotelName": "Catfish Creek Fishing Cabins",
        "Description": "Brand new mattresses and pillows.  Free airport shuttle. Great hotel for your business needs. Comp WIFI, atrium lounge & restaurant, 1 mile from light rail."
    }
]
```

## <a name="example-4-term-boosting"></a>Exemplo 4: aumento de termo

O aumento de termos refere-se ao aumento da classificação de um documento caso ele contenha o termo aumentado, em relação aos documentos que não contêm o termo. Para aumentar um termo, use o sinal de interpolação, o `^` símbolo com um fator de aumento (um número) no final do termo que você está pesquisando. O padrão do fator de aumento é 1 e, embora ele deva ser positivo, ele pode ser menor que 1 (por exemplo, 0,2). O aumento de termos difere dos perfis de pontuação, em que os perfis de pontuação aumentam determinados campos, em vez de termos específicos.

Nesta consulta "Before", procure "acesso à praia" e observe que há sete documentos que correspondem em um ou ambos os termos.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "beach access",
    "queryType": "full",
    "select": "HotelName, Description, Tags",
    "searchFields": "HotelName, Description, Tags",
    "count": true
}
```

Na verdade, há apenas um documento que corresponde ao "acesso" e, por ser a única correspondência, o posicionamento é alto (segunda posição), mesmo que o documento esteja sem o termo "praia".

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.2723424,
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown."
    },
    {
        "@search.score": 1.5507699,
        "HotelName": "Old Carrabelle Hotel",
        "Description": "Spacious rooms, glamorous suites and residences, rooftop pool, walking access to shopping, dining, entertainment and the city center."
    },
    {
        "@search.score": 1.5358944,
        "HotelName": "Whitefish Lodge & Suites",
        "Description": "Located on in the heart of the forest. Enjoy Warm Weather, Beach Club Services, Natural Hot Springs, Airport Shuttle."
    },
    {
        "@search.score": 1.3433652,
        "HotelName": "Ocean Air Motel",
        "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away."
    },
```

Na consulta "After", repita a pesquisa, desta vez aumentando os resultados com o termo "praia" ao longo do termo "Access". É uma versão legível da consulta `search=Description:beach^2 access` . Dependendo do cliente, talvez seja necessário expressar `^2` como `%5E2` .

Depois de aumentar o termo "praia", a correspondência no velho Carrabelle Hotel se move para o sexto lugar.

<!-- Consider a scoring profile that boosts matches in a certain field, such as "genre" in a music app. Term boosting could be used to further boost certain search terms higher than others. For example, "rock^2 electronic" will boost documents that contain the search terms in the "genre" field higher than other searchable fields in the index. Furthermore, documents that contain the search term "rock" will be ranked higher than the other search term "electronic" as a result of the term boost value (2). -->

## <a name="example-5-regex"></a>Exemplo 5: Regex

Uma pesquisa de expressão regular encontra uma correspondência com base no conteúdo entre as barras "/", como documentado na [classe RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:/(Mo|Ho)tel/",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

A resposta para essa consulta deve ser semelhante ao exemplo a seguir:

```json
    "@odata.count": 22,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Days Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Triple Landscape Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Smile Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Pelham Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Sublime Cliff Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Twin Dome Motel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Nova Hotel & Spa"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
```

> [!Note]
> Consultas Regex não são [analisadas](./search-lucene-query-architecture.md#stage-2-lexical-analysis). A única transformação executada em termos de consulta parcial é de letras minúsculas.
>

## <a name="example-6-wildcard-search"></a>Exemplo 6: pesquisa de curinga

Você pode usar a sintaxe geralmente reconhecida para várias `*` pesquisas de curinga de caractere () ou única ( `?` ). Observe que o analisador de consulta Lucene oferece suporte ao uso desses símbolos com um único termo e não uma frase.

Nesta consulta, pesquise nomes de Hotel que contenham o prefixo ' SC '. Você não pode usar `*` um `?` símbolo ou como o primeiro caractere de uma pesquisa.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:sc*",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

A resposta para essa consulta deve ser semelhante ao exemplo a seguir:

```json
    "@odata.count": 2,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scottish Inn"
        }
    ]
```

> [!Note]
> Consultas com curingas não são [analisadas](./search-lucene-query-architecture.md#stage-2-lexical-analysis). A única transformação executada em termos de consulta parcial é de letras minúsculas.
>

## <a name="next-steps"></a>Próximas etapas

Tente especificar consultas no código. Os links a seguir explicam como configurar consultas de pesquisa usando os SDKs do Azure.

+ [Consultar seu índice usando o SDK do .NET](search-get-started-dotnet.md)
+ [Consultar seu índice usando o SDK do Python](search-get-started-python.md)
+ [Consultar seu índice usando o SDK do JavaScript](search-get-started-javascript.md)

Referência de sintaxe adicional, arquitetura de consulta e exemplos podem ser encontrados nos links a seguir:

+ [Exemplos de consulta de sintaxe Lucene para criar consultas avançadas](search-query-lucene-examples.md)
+ [Como funciona a pesquisa de texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Sintaxe de consulta simples](query-simple-syntax.md)
+ [Sintaxe de consulta Lucene completa](query-lucene-syntax.md)
+ [Sintaxe de filtro](search-query-odata-filter.md)