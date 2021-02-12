---
title: Usar sintaxe de consulta Lucene simples
titleSuffix: Azure Cognitive Search
description: Aprenda por exemplo executando consultas com base na sintaxe simples para pesquisa de texto completo, pesquisa de filtro, pesquisa geográfica, pesquisa facetada em um índice de Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: ff9495e37a499b5502d8f8ced79b69608fa9552a
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401739"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Usar a sintaxe de pesquisa "simples" no Azure Pesquisa Cognitiva

No Azure Pesquisa Cognitiva, a [sintaxe de consulta simples](query-simple-syntax.md) invoca o analisador de consulta padrão para pesquisa de texto completo. O analisador é rápido e manipula cenários comuns, incluindo pesquisa de texto completo, pesquisa filtrada e facetada e pesquisa de prefixo. Este artigo usa exemplos para ilustrar o uso de sintaxe simples em uma solicitação de [documentos de pesquisa (API REST)](/rest/api/searchservice/search-documents) .

> [!NOTE]
> Uma sintaxe de consulta alternativa é o [Lucene completo](query-lucene-syntax.md), dando suporte a estruturas de consulta mais complexas, como pesquisa difusa e curinga. Para obter mais informações e exemplos, consulte [usar a sintaxe Lucene completa](search-query-lucene-examples.md).

## <a name="nyc-jobs-examples"></a>Exemplos de trabalhos do NYC

Os exemplos a seguir aproveitam o [índice de pesquisa de trabalhos do NYC](https://azjobsdemo.azurewebsites.net/) que consiste em trabalhos disponíveis com base em um conjunto de uma [das cidades da iniciativa de OpenData de Nova York](https://nycopendata.socrata.com/). Esses dados não devem ser considerados atuais ou completos. O índice está em um serviço de área restrita fornecido pela Microsoft, o que significa que você não precisa de uma assinatura do Azure ou Pesquisa Cognitiva do Azure para tentar essas consultas.

O que você precisa é um postmaster ou uma ferramenta equivalente para emitir a solicitação HTTP em GET ou POST. Se você não estiver familiarizado com essas ferramentas, consulte [início rápido: explorar a API REST do Azure pesquisa cognitiva](search-get-started-rest.md).

## <a name="set-up-the-request"></a>Configurar a solicitação

1. Os cabeçalhos de solicitação devem ter os seguintes valores:

   | Chave | Valor |
   |-----|-------|
   | Tipo de conteúdo | `application/json`|
   | chave de API  | `252044BE3886FE4A8E3BAA4F595114BB` </br> (essa é a chave de API de consulta real para o serviço de pesquisa de área restrita que hospeda o índice de trabalhos NYC) |

1. Defina o verbo como **`GET`** .

1. Defina a URL como **`https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=*&$count=true`** . 

   + A coleção de documentos no índice contém todo o conteúdo pesquisável. A chave de API de consulta fornecida no cabeçalho de solicitação só funciona para operações de leitura direcionando a coleção de documentos.

   + **`$count=true`** Retorna uma contagem dos documentos que correspondem aos critérios de pesquisa. Em uma cadeia de caracteres de pesquisa vazia, a contagem será todos os documentos no índice (cerca de 2558 no caso de trabalhos do NYC).

   + **`search=*`** é uma consulta não especificada, equivalente a nulo ou pesquisa vazia. Ele não é especialmente útil, mas é a pesquisa mais simples que você pode fazer e mostra todos os campos recuperáveis no índice, com todos os valores.

1. Como uma etapa de verificação, cole a solicitação a seguir no GET e clique em **Enviar**. Os resultados são retornados como documentos JSON detalhados.

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-simple-query-parsing"></a>Como invocar a análise de consulta simples

Para consultas interativas, não é necessário especificar nada: simples é o padrão. No código, se você tiver chamado anteriormente **`queryType=full`** , poderá redefinir o padrão com **`queryType=simple`** .

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "queryType": "simple"
}
```

## <a name="example-1-full-text-search-on-specific-fields"></a>Exemplo 1: pesquisa de texto completo em campos específicos

Este primeiro exemplo não é específico do analisador, mas nós o conduzimos para introduzir o primeiro conceito de consulta fundamental: contenção. Este exemplo limita a execução da consulta e a resposta a apenas alguns campos específicos. Saber como estruturar uma resposta JSON legível é importante quando sua ferramenta é Postman ou Search Explorer. 

Essa consulta destina-se somente a *business_title* no **`searchFields`** , especificando por meio do **`select`** parâmetro o mesmo campo na resposta.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

A resposta para essa consulta deve ser semelhante à captura de tela a seguir.

  :::image type="content" source="media/search-query-lucene-examples/postman-sample-results.png" alt-text="Resposta de exemplo do Postman" border="false":::

Você deve ter notado a pontuação de pesquisa na resposta. Pontuações uniformes de **1** ocorrem quando não há nenhuma classificação, porque a pesquisa não era uma pesquisa de texto completo ou porque nenhum critério foi fornecido. Para uma pesquisa vazia, as linhas retornam em ordem arbitrária. Quando você incluir critérios reais, verá as pontuações da pesquisa evoluir para valores significativos.

## <a name="example-2-look-up-by-id"></a>Exemplo 2: pesquisar por ID

Quando você retorna resultados da pesquisa em uma consulta, uma etapa lógica subsequente é fornecer uma página de detalhes que inclui mais campos do documento. Este exemplo mostra como retornar um único documento usando uma operação de [pesquisa](/rest/api/searchservice/lookup-document) para passar a ID do documento.

Todos os documentos possuem um identificador exclusivo. Para experimentar a sintaxe de uma consulta, primeiro retorne uma lista de IDs do documento para que seja possível localizar uma a ser utilizada. Para Trabalhos de Nova Iorque, os identificadores são armazenados no campo `id`.

```http
GET /indexes/nycjobs/docs?api-version=2020-06-30&search=*&$select=id&$count=true
```

Em seguida, recupere um documento da coleção com base em `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", que apareceu primeiro na resposta anterior. A consulta a seguir retorna todos os campos recuperáveis para o documento inteiro.

```http
GET /indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2020-06-30
```

## <a name="example-3-filter-queries"></a>Exemplo 3: consultas de filtro

A [sintaxe de filtro](./search-query-odata-filter.md) é uma expressão OData que você pode usar sozinho ou com **`search`** . Um filtro independente, sem um parâmetro de pesquisa, é útil quando a expressão de filtro é capaz de qualificar totalmente os documentos de interesse. Sem uma cadeia de caracteres de consulta, não há análise léxica ou linguística, nenhuma pontuação (todas as pontuações são 1) e nenhuma classificação. Observe que a cadeia de caracteres de pesquisa está vazia.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

Usado em conjunto, o filtro é aplicado primeiro ao índice inteiro e, em seguida, a pesquisa é executada nos resultados do filtro. Os filtros, portanto, podem ser uma técnica útil para melhorar o desempenho da consulta, uma vez que reduzem o conjunto de documentos que a consulta de pesquisa precisa processar.

  :::image type="content" source="media/search-query-simple-examples/filtered-query.png" alt-text="Filtrar resposta da consulta" border="false":::

Outra maneira eficiente de combinar filtro e pesquisa é por meio **`search.ismatch*()`** de uma expressão de filtro, na qual você pode usar uma consulta de pesquisa dentro do filtro. Essa expressão de filtro usa um curinga no *plano* para selecionar business_title, incluindo o termo plano, planejador, planejamento, e assim por diante.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "search.ismatch('plan*', 'business_title', 'full', 'any')",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

Para obter mais informações sobre a função, consulte [search.ismatch em "Exemplos de filtros"](./search-query-odata-full-text-search-functions.md#examples).

## <a name="example-4-range-filters"></a>Exemplo 4: filtros de intervalo

A filtragem de intervalo é suportada por meio **`$filter`** de expressões para qualquer tipo de dados. Os exemplos a seguir pesquisam campos numéricos e de cadeia de caracteres. 

Os tipos de dados são importantes em filtros de intervalo e funcionam melhor quando os dados numéricos estão em campos numéricos e dados de cadeia de caracteres em campos de cadeia de caracteres. Dados numéricos em campos de cadeia de caracteres não são adequados para intervalos porque cadeias numéricas não são comparáveis no Pesquisa Cognitiva do Azure.

A consulta a seguir é um intervalo numérico:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency"
    }
```
A resposta para essa consulta deve ser semelhante à captura de tela a seguir.

  :::image type="content" source="media/search-query-simple-examples/rangefilternumeric.png" alt-text="Filtro de intervalo para intervalos numéricos" border="false":::

Nessa consulta, o intervalo é sobre um campo de cadeia de caracteres (business_title):

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title"
    }
```

A resposta para essa consulta deve ser semelhante à captura de tela a seguir.

  :::image type="content" source="media/search-query-simple-examples/rangefiltertext.png" alt-text="Filtro de intervalo para intervalos de texto" border="false":::

> [!NOTE]
> A facetagem em intervalos de valores é um requisito comum de aplicativo de pesquisa. Para obter mais informações e exemplos, consulte [como criar um filtro de faceta](search-filters-facets.md).

## <a name="example-5-geo-search"></a>Exemplo 5: pesquisa geográfica

O índice de exemplo inclui um campo geo_location com coordenadas de latitude e longitude. Este exemplo usa a [função geo.distance](search-query-odata-geo-spatial-functions.md#examples) que filtra os documentos dentro da circunferência de um ponto inicial, até uma distância arbitrária (em quilômetros) que você fornece. É possível ajustar o último valor na consulta (4) para reduzir ou ampliar a área da superfície da consulta.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "business_title, work_location"
    }
```

Para obter resultados mais legíveis, os resultados da pesquisa são cortados para incluir o cargo e o local de trabalho. As coordenadas iniciais foram obtidas de um documento aleatório no índice (nesse caso, para um local de trabalho em Staten Island).

  :::image type="content" source="media/search-query-simple-examples/geo-search.png" alt-text="Mapa da ilha Staten" border="false":::

## <a name="example-6-search-precision"></a>Exemplo 6: precisão de pesquisa

Consultas de termo são termos únicos, talvez muitos deles, avaliados independentemente. Consultas de frase são colocadas entre aspas e avaliadas como uma cadeia de caracteres textual. A precisão da correspondência é controlada pelos operadores e pelo searchMode.

Exemplo 1: `search=fire`  corresponde a 140 resultados, onde todas as correspondências contêm a palavra Fire em algum lugar no documento.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire"
    }
```

Exemplo 2: `search=fire department` retorna 2002 resultados. As correspondências são retornadas para documentos que contenham fogo ou bombeiros.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire department"
    }
```

Exemplo 3: `search="fire department"` retorna 77 resultados. Colocar a cadeia de caracteres entre aspas cria uma pesquisa de frase que consiste em ambos os termos e as correspondências são encontradas em termos de token no índice que consiste nos termos combinados. Isso explica por que uma pesquisa como `search=+fire +department` não é equivalente. Ambos os termos são obrigatórios, mas são verificados independentemente. 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
    "count": true,
    "search": "\"fire department\""
    }
```

> [!Note]
> Como uma consulta de frase é especificada entre aspas, este exemplo adiciona um caractere de escape ( `\` ) para preservar a sintaxe.

## <a name="example-7-booleans-with-searchmode"></a>Exemplo 7: boolianos com searchMode

A sintaxe simples dá suporte a operadores boolianos na forma de caracteres (`+, -, |`). O parâmetro searchmode informa as compensações entre Precision e RECALL, com **`searchMode=any`** favorecer a RECALL (a correspondência em qualquer critério qualifica um documento para o conjunto de resultados) e **`searchMode=all`** favorecer a precisão (todos os critérios devem ser correspondidos). 

O padrão é **`searchMode=any`** , que pode ser confuso se você estiver empilhando uma consulta com vários operadores e obtendo resultados mais amplos em vez de mais estreitos. Isso é particularmente verdade com NÃO, onde os resultados incluem todos os documentos "não contendo" um termo específico.

Usando o searchmode padrão (any), 2800 documentos são retornados: aqueles que contêm a frase "departamento de incêndio", além de todos os documentos que não têm a frase "MetroTech Center".

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "any"
    }
```

A resposta para essa consulta deve ser semelhante à captura de tela a seguir.

  :::image type="content" source="media/search-query-simple-examples/searchmodeany.png" alt-text="modo de pesquisa qualquer" border="false":::

Alterar para **`searchMode=all`** impõe um efeito cumulativo em critérios e retorna um conjunto de resultados menores-21 documentos – consistindo em documentos contendo a frase inteira "departamento de incêndio", menos esses trabalhos no endereço do centro do MetroTech.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "all"
    }
```

  :::image type="content" source="media/search-query-simple-examples/searchmodeall.png" alt-text="modo de pesquisa todos" border="false":::

## <a name="example-8-structuring-results"></a>Exemplo 8: resultados de estruturação

Vários parâmetros controlam quais campos estão nos resultados da pesquisa, o número de documentos retornados em cada lote e a ordem de classificação. Este exemplo retona alguns dos exemplos anteriores, limitando os resultados a campos específicos usando a **`$select`** instrução e os critérios de pesquisa textual, retornando 82 correspondências.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description"
    }
```

Anexado ao exemplo anterior, é possível classificar por título. Esse tipo funciona porque civil_service_title é *classificável* no índice.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title"
    }
```

Os resultados da paginação são implementados usando o **`$top`** parâmetro, neste caso, retornando os 5 principais documentos:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5"
    }
```

Para obter os próximos 5, ignore o primeiro lote:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5",
      "skip": "5"
    }
```

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