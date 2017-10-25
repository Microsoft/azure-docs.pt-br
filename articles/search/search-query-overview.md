---
title: "Consultar seu Índice de Azure Search | Microsoft Docs"
description: "Crie uma consulta de pesquisa na Pesquisa do Azure e use parâmetros de pesquisa para filtrar e classificar os resultados da pesquisa."
services: search
manager: jhubbard
documentationcenter: 
author: ashmaka
ms.assetid: 69205d7a-363f-4b92-a53f-6ca818a3d2c7
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 04/26/2017
ms.author: ashmaka
ms.openlocfilehash: a22b82829df4659681940267e64c98d345453958
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="query-your-azure-search-index"></a>Consultar seu índice de Pesquisa do Azure
> [!div class="op_single_selector"]
> * [Visão geral](search-query-overview.md)
> * [Portal](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Ao enviar solicitações de pesquisa à Pesquisa do Azure, há vários parâmetros que podem ser especificados junto com as palavras reais digitadas na caixa de pesquisa de seu aplicativo. Esses parâmetros de consulta permitem conseguir maior controle do comando [full-text search experience](search-lucene-query-architecture.md).

Abaixo está uma lista que explica resumidamente os usos comuns dos parâmetros de consulta na Pesquisa do Azure. Para ver uma cobertura completa dos parâmetros de consulta e seu comportamento, consulte as páginas detalhadas da [API REST](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) e do [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters#microsoft_azure_search_models_searchparameters#properties_summary).

## <a name="types-of-queries"></a>Tipos de consultas
a Pesquisa do Azure oferece várias opções para a criação de consultas extremamente poderosas. Os dois tipos de consulta principais que você usará são `search` e `filter`. Uma consulta `search` procura um ou mais termos em todos os campos *pesquisáveis* no índice e funciona como você esperaria que um mecanismo de pesquisa, como o Bing ou o Google, funcionasse. Uma consulta `filter` avalia uma expressão booliana em todos os campos *filtráveis* em um índice. Diferentemente das consultas `search`, as consultas `filter` fazem a correspondência exata do conteúdo de um campo, o que significa que elas diferenciam letras maiúsculas de minúsculas para os campos de cadeia de caracteres.

Você pode usar pesquisas e filtros juntos ou separados. Se você usá-los juntos, o filtro será aplicado primeiro ao índice inteiro e, em seguida, a pesquisa será realizada nos resultados do filtro. Os filtros, portanto, podem ser uma técnica útil para melhorar o desempenho da consulta, uma vez que reduzem o conjunto de documentos que a consulta de pesquisa precisa processar.

A sintaxe das expressões de filtro é um subconjunto da [linguagem de filtro OData](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search). Para as consultas de pesquisa, você pode usar a [sintaxe simplificada](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) ou a [sintaxe de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search), que são analisadas abaixo.

### <a name="simple-query-syntax"></a>Sintaxe de consulta simples
A [sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) é a linguagem de consulta padrão usada na Pesquisa do Azure. A sintaxe de consulta simples dá suporte a vários operadores de pesquisa comuns, incluindo o AND, OR, NOT, frase, sufixo e operadores de precedência.

### <a name="lucene-query-syntax"></a>sintaxe de consulta Lucene
A [sintaxe de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search) permite que você use a linguagem de consulta amplamente adotada e expressiva desenvolvida como parte do [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Usar a sintaxe de consulta permite que você consiga facilmente os seguintes recursos: [Consultas com escopo de campo](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields), [pesquisa difusa](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy), [pesquisa por proximidade](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity), [aumento de termos](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost), [pesquisa de expressão regular](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex), [pesquisa de curinga](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard), [conceitos básicos da sintaxe](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax) e [consultas usando operadores boolianos](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_boolean).

## <a name="ordering-results"></a>Ordenando resultados
Ao receber os resultados de uma consulta de pesquisa, você pode solicitar que a Pesquisa do Azure apresente os resultados ordenados por valores em um campo específico. Por padrão, a Pesquisa do Azure ordena os resultados da pesquisa com base na classificação de pontuação da pesquisa de cada documento, que é derivada de [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Se você quiser que a Pesquisa do Azure retorne os resultados ordenados por um valor diferente da pontuação da pesquisa, poderá usar o `orderby` parâmetro de pesquisa. Você pode especificar o valor do parâmetro `orderby` para incluir os nomes de campo e as chamadas para a função [`geo.distance()` ](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) para obter os valores geoespaciais. Cada expressão pode ser seguida por `asc` para indicar que os resultados são solicitados na ordem ascendente e `desc` para indicar que os resultados são solicitados na ordem descendente. Ordem ascendente da classificação padrão.

## <a name="paging"></a>Paginamento
A Pesquisa do Azure facilita implementar a paginação dos resultados da pesquisa. Usando os parâmetros `top` e `skip`, você pode emitir sem problemas as solicitações de pesquisa que permitem receber o conjunto total de resultados da pesquisa em subconjuntos gerenciáveis e ordenados, que permitem facilmente as boas práticas da IU de pesquisa. Ao receber esses subconjuntos menores de resultados, você também pode receber a contagem de documentos no conjunto total de resultados da pesquisa.

Você pode aprender mais sobre a paginação dos resultados da pesquisa no artigo [Como paginar os resultados da pesquisa na Pesquisa do Azure](search-pagination-page-layout.md).

## <a name="hit-highlighting"></a>Realce de ocorrência
Na Pesquisa do Azure, enfatizar a parte exata dos resultados da pesquisa que correspondem à consulta de pesquisa é mais fácil usando os parâmetros `highlight`, `highlightPreTag` e `highlightPostTag`. Você pode especificar quais campos *pesquisáveis* devem ter o texto correspondido enfatizado, bem como especificar as marcas da cadeia de caracteres exatas para anexar ao início e ao término do texto correspondente que a Azure Search retorna.

## <a name="try-out-query-syntax"></a>Experimente a sintaxe de consulta

A melhor maneira de entender as diferenças de sintaxe é por meio do envio de consultas e do exame dos resultados.

+ Use o [Search Explorer](search-explorer.md) no portal do Azure. Ao implantar [o índice de exemplo](search-get-started-portal.md), você poderá consultar o índice em minutos usando as ferramentas no portal.

+ Use o [Fiddler](search-fiddler.md) ou o Chrome Postman para enviar consultas para um índice que você carregou no serviço de pesquisa. Ambas as ferramentas oferecem suporte a chamadas REST para um ponto de extremidade HTTP. 