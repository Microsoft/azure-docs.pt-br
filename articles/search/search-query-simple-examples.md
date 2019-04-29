---
title: Exemplos de consulta usando a sintaxe de pesquisa "simples" - Azure Search
description: Exemplos de consulta simples para pesquisa de texto completo, pesquisa de filtro, pesquisa geográfica, pesquisa facetada e outras cadeias de caracteres de consulta usadas para consultar um índice do Azure Search.
author: HeidiSteen
manager: cgronlun
tags: Simple query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 9b7147971bd320a11606a93ab4d988e924cf93b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61296880"
---
# <a name="query-examples-using-the-simple-search-syntax-in-azure-search"></a>Exemplos de consulta usando a sintaxe de pesquisa "simples" no Azure Search

A [sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) invoca o analisador de consulta padrão para executar consultas de pesquisa de texto completo em um índice do Azure Search. O analisador de consultas simples é rápido e lida com cenários comuns no Azure Search, incluindo pesquisa de texto completo, pesquisa filtrada e facetada e pesquisa geográfica. Neste artigo, percorra exemplos demonstrando as operações de consulta disponíveis ao usar a sintaxe simples.

A sintaxe de consulta alternativa é [Lucene Completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), com suporte para estruturas de consulta mais complexas como pesquisa com curinga e difusa, que pode demorar mais tempo para ser processada. Para obter mais informações e exemplos que demonstram a sintaxe completa, consulte [Exemplos de consulta de sintaxe Lucene](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Formular solicitações em Postman

Os exemplos a seguir utilizam um índice de pesquisa de Trabalhos de Nova Iorque que consiste em trabalhos disponíveis com base em um conjunto de dados fornecido pelo [OpenData da cidade de Nova Iorque](https://nycopendata.socrata.com/). Esses dados não devem ser considerados atuais ou completos. O índice está em um serviço de área restrita fornecido pela Microsoft, o que significa que não é necessária uma assinatura do Azure ou Azure Search para experimentar essas consultas.

O que é necessário é o Postman ou uma ferramenta equivalente para emitir solicitação HTTP em GET. Para obter mais informações, consulte [explorar com clientes REST](search-fiddler.md).

### <a name="set-the-request-header"></a>Definir o cabeçalho de solicitação

1. No cabeçalho de solicitação, defina **Content-Type** para `application/json`.

2. Adicione uma**api-key** e defina-a para essa cadeia de caracteres: `252044BE3886FE4A8E3BAA4F595114BB`. Essa é uma chave de consulta para o serviço de pesquisa de área restrita que hospeda o índice Trabalhos de Nova Iorque.

Após especificar o cabeçalho de solicitação, você poderá reutilizá-lo para todas as consultas neste artigo, trocando apenas a cadeia de caracteres **search=**. 

  ![Cabeçalho da solicitação do Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Definir a URL da solicitação

A solicitação é um comando GET emparelhado com uma URL que contém a cadeia de caracteres de pesquisa e o ponto de extremidade do Azure Search.

  ![Cabeçalho da solicitação do Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

A composição de URL possui os elementos a seguir:

+ **`https://azs-playground.search.windows.net/`** é um serviço de pesquisa de área restrita mantido pela equipe de desenvolvimento do Azure Search. 
+ **`indexes/nycjobs/`** é o índice Trabalhos de Nova Iorque na coleção de índices desse serviço. O nome do serviço e índice são obrigatórios na solicitação.
+ **`docs`** é a coleção de documentos que contém todo o conteúdo pesquisável. A chave de API de consulta fornecida no cabeçalho da solicitação somente funciona em operações de leitura direcionando a coleção de documentos.
+ **`api-version=2017-11-11`** define a versão da API, que é um parâmetro obrigatório em todas as solicitações.
+ **`search=*`** é a cadeia de caracteres de consulta, que na consulta inicial é nula, retornando os primeiros 50 resultados (por padrão).

## <a name="send-your-first-query"></a>Enviar a primeira consulta

Como uma etapa de verificação, cole a solicitação a seguir no GET e clique em **Enviar**. Os resultados são retornados como documentos JSON detalhados. Documentos inteiros são retornados, que permite que você veja todos os campos e todos os valores.

Cole essa URL em um cliente REST como uma etapa de validação e exibir a estrutura do documento.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=*
  ```

A cadeia de caracteres de consulta, **`search=*`**, é uma pesquisa não especificada equivalente à pesquisa nula ou vazia. Não é especialmente útil, mas é a pesquisa mais simples que você pode fazer.

Opcionalmente, é possível adicionar **`$count=true`** à URL para retornar uma contagem dos documentos que correspondem aos critérios de pesquisa. Em uma cadeia de pesquisa vazia, esses são todos os documentos no índice (cerca de 2800 no caso de NYC Jobs).

## <a name="how-to-invoke-simple-query-parsing"></a>Como invocar a análise de consulta simples

Para consultas interativas, não é necessário especificar nada: simples é o padrão. No código, se você invocou anteriormente **queryType=full** para a sintaxe de consulta completa, você poderá redefinir o padrão com **queryType=simple**.

## <a name="example-1-field-scoped-query"></a>Exemplo 1: consulta com escopo de campo

Este primeiro exemplo não é específico do analisador, mas nós o conduzimos para introduzir o primeiro conceito de consulta fundamental: contenção. Este exemplo abrange a execução da consulta e a resposta para apenas alguns campos específicos. Saber como estruturar uma resposta JSON legível é importante quando sua ferramenta é Postman ou Search Explorer. 

Por uma questão de brevidade, a consulta direciona apenas o campo *business_title* e especifica que somente os títulos da empresa sejam retornados. A sintaxe é **searchFields** para restringir a execução da consulta apenas ao campo business_title e **selecione** para especificar quais campos são incluídos na resposta.

### <a name="partial-query-string"></a>Cadeia de caracteres de consulta parcial

```http
searchFields=business_title&$select=business_title&search=*
```

Aqui está a mesma consulta com vários campos em uma lista delimitada por vírgulas.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>URL completa

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=business_title&$select=business_title&search=*
```

A resposta para essa consulta deve ser semelhante à captura de tela a seguir.

  ![Resposta de exemplo do Postman](media/search-query-lucene-examples/postman-sample-results.png)

Você deve ter notado a pontuação de pesquisa na resposta. Pontuações uniformes de 1 ocorrem quando não há classificação, seja porque a pesquisa não foi pesquisa de texto completo ou porque nenhum critério foi aplicado. Para pesquisa nula sem critérios, as linhas retornam em ordem arbitrária. Quando você incluir critérios reais, verá as pontuações da pesquisa evoluir para valores significativos.

## <a name="example-2-look-up-by-id"></a>Exemplo 2: pesquisar por ID

Este exemplo é um pouco atípico, mas ao avaliar os comportamentos de pesquisa, você poderá querer inspecionar todo o conteúdo de um documento específico para entender por que ele foi incluído ou excluído dos resultados. Para retornar um único documento em sua totalidade, use uma [Operação de pesquisa](https://docs.microsoft.com/rest/api/searchservice/lookup-document) para passar na ID do documento.

Todos os documentos possuem um identificador exclusivo. Para experimentar a sintaxe de uma consulta, primeiro retorne uma lista de IDs do documento para que seja possível localizar uma a ser utilizada. Para Trabalhos de Nova Iorque, os identificadores são armazenados no campo `id`.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchFields=id&$select=id&search=*
```

O próximo exemplo é uma consulta de pesquisa que retorna um documento específico com base em `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", que apareceu primeiro na resposta anterior. A consulta a seguir retorna o documento inteiro, não apenas os campos selecionados. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2017-11-11&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>Exemplo 3: filtrar consultas

A [sintaxe de filtro](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples) é uma expressão OData que pode ser usada com **pesquisa** ou por si própria. Um filtro independente, sem um parâmetro de pesquisa, é útil quando a expressão de filtro é capaz de qualificar totalmente os documentos de interesse. Sem uma cadeia de caracteres de consulta, não há análise léxica ou linguística, nenhuma pontuação (todas as pontuações são 1) e nenhuma classificação. Observe que a cadeia de caracteres de pesquisa está vazia.

```http
POST /indexes/nycjobs/docs/search?api-version=2017-11-11
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "select=job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Usado em conjunto, o filtro é aplicado primeiro ao índice inteiro e, em seguida, a pesquisa é executada nos resultados do filtro. Os filtros, portanto, podem ser uma técnica útil para melhorar o desempenho da consulta, uma vez que reduzem o conjunto de documentos que a consulta de pesquisa precisa processar.

  ![Filtrar resposta da consulta](media/search-query-simple-examples/filtered-query.png)

Se quiser experimentar isso no Postman usando GET, você pode colar nesta cadeia de caracteres:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

Outra maneira avançada de combinar filtro e pesquisa é através de **`search.ismatch*()`** em uma expressão de filtro, onde é possível usar uma consulta de pesquisa dentro do filtro. Essa expressão de filtro usa um curinga no *plano* para selecionar business_title, incluindo o termo plano, planejador, planejamento, e assim por diante.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

Para obter mais informações sobre a função, consulte [search.ismatch em "Exemplos de filtros"](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples).

## <a name="example-4-range-filters"></a>Exemplo 4: filtros de intervalo

A filtragem de intervalos tem suporte por meio de expressões **`$filter`** para qualquer tipo de dados. Os exemplos a seguir pesquisam campos numéricos e de cadeia de caracteres. 

Os tipos de dados são importantes em filtros de intervalo e funcionam melhor quando os dados numéricos estão em campos numéricos e dados de cadeia de caracteres em campos de cadeia de caracteres. Dados numéricos em campos de cadeia de caracteres não são adequados para intervalos porque as cadeia de caracteres numéricas não são comparáveis no Azure Search. 

Os exemplos a seguir estão no formato POST para legibilidade (intervalo numérico, seguido por intervalo de texto):

```http
POST /indexes/nycjobs/docs/search?api-version=2017-11-11
    {
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  ![Filtro de intervalo para intervalos numéricos](media/search-query-simple-examples/rangefilternumeric.png)


```http
POST /indexes/nycjobs/docs/search?api-version=2017-11-11
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  ![Filtro de intervalo para intervalos de texto](media/search-query-simple-examples/rangefiltertext.png)

Também é possível experimentá-los no Postman usando GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> A facetagem em intervalos de valores é um requisito comum de aplicativo de pesquisa. Para obter mais informações e exemplos sobre compilação de filtros para estruturas de faceted navigation, consulte ["Filtrar com base em um intervalo" em *Como implementar faceted navigation*](search-faceted-navigation.md#filter-based-on-a-range).

## <a name="example-5-geo-search"></a>Exemplo 5: Pesquisa geográfica

O índice de exemplo inclui um campo geo_location com coordenadas de latitude e longitude. Este exemplo usa a [função geo.distance](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples) que filtra os documentos dentro da circunferência de um ponto inicial, até uma distância arbitrária (em quilômetros) que você fornece. É possível ajustar o último valor na consulta (4) para reduzir ou ampliar a área da superfície da consulta.

O exemplo a seguir está no formato POST para legibilidade:

```http
POST /indexes/nycjobs/docs/search?api-version=2017-11-11
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
Para obter resultados mais legíveis, os resultados da pesquisa são cortados para incluir uma ID de trabalho, cargo e o local de trabalho. As coordenadas iniciais foram obtidas de um documento aleatório no índice (nesse caso, para um local de trabalho em Staten Island).

Também é possível experimentar isso em Postman usando GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>Exemplo 6: precisão da pesquisa

Consultas de termo são termos únicos, talvez muitos deles, avaliados independentemente. Consultas de frase são colocadas entre aspas e avaliadas como uma cadeia de caracteres textual. A precisão da correspondência é controlada pelos operadores e pelo searchMode.

Exemplo 1: **`&search=fire`** retorna 150 resultados, em que todas as correspondências contêm a palavra fogo em algum lugar no documento.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire
```

Exemplo 2: **`&search=fire department`** retorna 2002 resultados. As correspondências são retornadas para documentos que contenham fogo ou bombeiros.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search=fire department
```

Exemplo 3: **`&search="fire department"`** retorna 82 resultados. Colocar a cadeia de caracteres entre aspas é uma pesquisa textual em ambos os termos e as correspondências são localizadas nos termos indexados no índice consistindo dos termos combinados. Isso explica por que uma pesquisa como **`search=+fire +department`** não é equivalente. Ambos os termos são obrigatórios, mas são verificados independentemente. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>Exemplo 7: boolianos com searchMode

A sintaxe simples dá suporte a operadores boolianos na forma de caracteres (`+, -, |`). O parâmetro searchMode informa as compensações entre precisão e recall, com `searchMode=any` favorecendo recall (correspondência em qualquer critério qualifica um documento para o conjunto de resultados) e `searchMode=all` favorecendo a precisão (todos os critérios devem ser combinados). O padrão é `searchMode=any`, o que pode ser confuso se você estiver empilhando uma consulta com vários operadores e obtendo resultados mais amplos em vez de mais restritos. Isso é particularmente verdade com NÃO, onde os resultados incluem todos os documentos "não contendo" um termo específico.

Usando o searchMode padrão (qualquer), 2800 documentos são retornados: aqueles que contêm o termo de várias partes "corpo de bombeiros", além de todos os documentos que não possuem o termo "Metrotech Center".

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  ![modo de pesquisa qualquer](media/search-query-simple-examples/searchmodeany.png)

Alterar searchMode para `all` impõe um efeito cumulativo aos critérios e retorna um conjunto de resultados menor - 21 documentos - que consiste em documentos contendo toda a frase "corpo de bombeiros", menos esses trabalhos no endereço do Metrotech Center.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  ![modo de pesquisa todos](media/search-query-simple-examples/searchmodeall.png)

## <a name="example-8-structuring-results"></a>Exemplo 8: resultados de estruturação

Vários parâmetros controlam quais campos estão nos resultados da pesquisa, o número de documentos retornados em cada lote e a ordem de classificação. Esse exemplo ressurge alguns dos exemplos anteriores, limitando os resultados a campos específicos usando a instrução **$select** e o critério de pesquisa textual, retornando 82 correspondências 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Anexado ao exemplo anterior, é possível classificar por título. Esse tipo funciona porque civil_service_title é *classificável* no índice.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Os resultados de paginação são implementados usando o parâmetro **$top**, nesse caso, retornando os 5 principais documentos:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Para obter os próximos 5, ignore o primeiro lote:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2017-11-11&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Próximas etapas
Experimente especificar consultas no código. Os links a seguir explicam como configurar consultas de pesquisa para .NET e API REST usando a sintaxe simples padrão.

* [Consultar seu índice do Azure Search usando o SDK do .NET](search-query-dotnet.md)
* [Consultar seu índice do Azure Search usando a API REST](search-create-index-rest-api.md)

Referência de sintaxe adicional, arquitetura de consulta e exemplos podem ser encontrados nos links a seguir:

+ [Exemplos de consulta de sintaxe Lucene para criar consultas avançadas](search-query-lucene-examples.md)
+ [Como funciona a pesquisa de texto completo no Azure Search](search-lucene-query-architecture.md)
+ [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Consulta Lucene completa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Filtro e sintaxe Orderby](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)
