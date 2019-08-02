---
title: Tipos de consulta e composição - Azure Search
description: Noções básicas para criar uma consulta de pesquisa no Azure Search, usando parâmetros para filtrar, selecionar e classificar resultados.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/13/2019
ms.custom: seodec2018
ms.openlocfilehash: 422e9209609eac19b7952557674c99789d9f6088
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485180"
---
# <a name="how-to-compose-a-query-in-azure-search"></a>Como compor uma consulta no Azure Search

Na Pesquisa do Azure, uma consulta é uma especificação completa de uma operação de ida e volta. Os parâmetros na solicitação fornecem os critérios de correspondência para localizar documentos em um índice, instruções de execução para o mecanismo e as diretivas para a resposta de formatação. 

Uma solicitação de consulta é um constructo avançado, que especifica quais campos estão no escopo, como pesquisar, quais campos devem ser retornados, se devem ser classificados ou filtrados e assim por diante. Não especificado, uma consulta é executada em todos os campos pesquisáveis como uma operação de pesquisa de texto completo, retornando um conjunto de resultados não marcado em ordem arbitrária.

## <a name="apis-and-tools-for-testing"></a>APIs e ferramentas para teste

A tabela a seguir lista as APIs e as abordagens baseadas em ferramentas para enviar consultas.

| Metodologia | DESCRIÇÃO |
|-------------|-------------|
| [Gerenciador de pesquisa (portal)](search-explorer.md) | Fornece uma barra de pesquisa e opções para seleções de versão da API e índice. Os resultados são retornados como documentos JSON. <br/>[Saiba mais.](search-get-started-portal.md#query-index) | 
| [Postman ou Fiddler](search-get-started-postman.md) | As ferramentas de teste da Web são uma excelente opção para formular chamadas REST. A API REST dá suporte a todas as operações possíveis no Azure Search. Neste arquivo, aprenda a configurar um cabeçalho e um corpo de solicitação HTTP para enviar consultas ao Azure Search.  |
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Cliente que pode ser usado para consultar um índice do Azure Search.  <br/>[Saiba mais.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Pesquisar documentos (API REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | Métodos GET ou POST em um índice, usando parâmetros de consulta para entrada adicional.  |

## <a name="a-first-look-at-query-requests"></a>Uma introdução às solicitações de consulta

Exemplos são úteis para introduzir novos conceitos. Como uma consulta representativa construído na [API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents), esse exemplo tem como alvo o [índice de demonstração de imóveis](search-get-started-portal.md) e inclui os parâmetros comuns.

```
{
    "queryType": "simple" 
    "search": "seattle townhouse* +\"lake\"",
    "searchFields": "description, city",
    "count": "true",
    "select": "listingId, street, status, daysOnMarket, description",
    "top": "10",
    "orderby": "daysOnMarket"
}
```

+ **`queryType`** Define o analisador, que, no Azure Search pode ser o [padrão do analisador de consulta simples](search-query-simple-examples.md) (ideais para a pesquisa de texto completo), ou o [completa o analisador de consulta Lucene](search-query-lucene-examples.md) usado para compilações de consulta avançada, como expressões regulares , pesquisa por proximidade, difusa e pesquisa de curinga, para citar alguns.

+ **`search`** fornece os critérios de correspondência, geralmente texto, mas geralmente acompanhados por operadores booleanos. Termos de autônomo único são *termo* consultas. Consultas de várias partes com delimitação de cotação são *consultas de frase-chave*. A pesquisa pode ser indefinida, como em **`search=*`** , mas mais provavelmente consiste em termos, expressões e operadores semelhantes ao que aparece no exemplo.

+ **`searchFields`** é opcional, usado para restringir a execução de consultas a campos específicos.

As respostas são moldadas também pelos parâmetros incluídos na consulta. No exemplo, o conjunto de resultados consiste em campos listados na instrução **`select`** . Somente os 10 principais hits são retornados nessa consulta, mas **`count`** informa quantos documentos correspondem em geral. Nesta consulta, as linhas são classificadas pelo daysOnMarket.

Na Pesquisa do Azure, a execução da consulta é sempre em relação a um índice, autenticado usando uma chave de API fornecida na solicitação. No REST, ambas são fornecidas nos cabeçalhos de solicitação.

### <a name="how-to-run-this-query"></a>Como executar essa consulta

Para executar essa consulta, use [pesquisar explorer e o índice de demonstração de imóveis](search-get-started-portal.md). 

Você pode colar essa cadeia de caracteres de consulta na barra de pesquisa do explorer: `search=seattle townhouse +lake&searchFields=description, city&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Como as operações de consulta são ativadas pelo índice

O design de índice e o design de consulta estão fortemente acoplados na Pesquisa do Azure. Um fato essencial a saber de antemão é que o *esquema de índice*, com atributos em cada campo, determina o tipo de consulta que você pode construir. 

Atributos de índice em um campo para definir as operações permitidas - se um campo está *pesquisável* no índice, *recuperáveis* nos resultados *classificável*,  *filtrável*e assim por diante. Na string de consulta de exemplo, `"$orderby": "daysOnMarket"` só funciona porque o campo daysOnMarket está marcado como *classificável* no esquema do índice. 

![Definição de índice para a amostra de imóveis](./media/search-query-overview/realestate-sample-index-definition.png " Definição de índice para a amostra de imóveis")

A captura de tela acima é uma lista parcial de atributos de índice para a amostra de imóveis. Você pode visualizar todo o esquema do índice no portal. Para obter mais informações sobre atributos de índice, consulte [Create Index REST API](https://docs.microsoft.com/rest/api/searchservice/create-index).

> [!Note]
> Algumas funcionalidades de consulta está habilitada, todo o índice em vez de em uma base por campo. Esses recursos incluem: [mapas de sinônimo](search-synonyms.md), [analisadores personalizados](index-add-custom-analyzers.md), [sugestor constrói (para preenchimento automático e as consultas sugeridas)](index-add-suggesters.md), [lógica de pontuação Para classificar os resultados](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>Elementos de uma solicitação de consulta

Consultas são sempre direcionadas em um único índice. Não é possível unir índices ou criar estruturas de dados temporárias ou personalizadas como um destino de consulta. 

Os elementos obrigatórios em uma solicitação de consulta incluem os componentes a seguir:

+ Coleta de documentos de endpoint e índice de serviço, expressa como uma URL contendo componentes fixos e definidos pelo usuário: **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`** (Somente REST) é necessária porque mais de uma versão da API está disponível em todos os momentos. 
+ **`api-key`** , seja uma consulta ou uma chave de administrador, autentica a solicitação em seu serviço.
+ **`queryType`** , seja simples ou completo, que pode ser omitido se você estiver usando a sintaxe simples padrão incorporada.
+ **`search`** ou **`filter`** fornece os critérios de correspondência, que podem não ser especificados se você quiser realizar uma pesquisa vazia. Os dois tipos de consulta são discutidos em termos do analisador simples, mas até mesmo as consultas avançadas exigem o parâmetro de pesquisa para passar expressões de consulta complexas.

Todos os outros parâmetros de pesquisa são opcionais. Para obter a lista completa de atributos, consulte [criar índice (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index). Para examinar mais detalhadamente como os parâmetros são usados durante o processamento, consulte [como funciona a pesquisa de texto completo no Azure Search](search-lucene-query-architecture.md).

## <a name="choose-a-parser-simple--full"></a>Escolha um analisador: simples | completo

O Azure Search funciona no Apache Lucene e fornece-lhe uma opção entre dois analisadores de consulta para tratamento de consultas especializadas e típicas. Solicitações usando o analisador simples são formuladas usando a [sintaxe de consulta simples](query-simple-syntax.md), selecionada como padrão por sua velocidade e eficácia em consultas de texto de forma livre. Essa sintaxe dá suporte a vários operadores de pesquisa comuns, incluindo os operadores de precedência, E, OU, NÃO, frase e sufixo.

A [sintaxe de consulta completa do Lucene](query-Lucene-syntax.md#bkmk_syntax), ativada quando você adiciona `queryType=full` à solicitação, expõe a amplamente adotada e expressiva linguagem de consulta desenvolvida como parte do [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Sintaxe completa estende a sintaxe simple. Qualquer consulta que você escrever para a sintaxe simples será executada no analisador Lucene completo. 

Os exemplos a seguir ilustram o ponto: a mesma consulta, mas com configurações diferentes de queryType, produz resultados diferentes. Na primeira consulta, o `^3` é tratado como parte do termo de pesquisa.

```
queryType=simple&search=mountain beach garden ranch^3&searchFields=description&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket
```

A mesma consulta usando o analisador Lucene completo interpreta o impulso em campo no "ranch", que aumenta a classificação de pesquisa dos resultados que contêm esse termo específico.

```
queryType=full&search=mountain beach garden ranch^3&searchFields=description&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Tipos de consultas

O Azure Search dá suporte a uma ampla gama de tipos de consulta. 

| Tipo de consulta | Uso | Obter mais informações e exemplos |
|------------|--------|-------------------------------|
| Pesquisa de texto de forma livre | Parâmetro de pesquisa e o analisador| A pesquisa de texto completo procura um ou mais termos em todos os *campos pesquisáveis* do seu índice e funciona da maneira esperada para um mecanismo de pesquisa como o Google ou o Bing. O exemplo na introdução é a pesquisa de texto completo.<br/><br/>A pesquisa de texto completo é submetida à análise de texto usando o analisador padrão Lucene (por padrão) para diminuir todos os termos, remover palavras parciais como "o". Você pode substituir o padrão por [analisadores que não estão em inglês](index-add-language-analyzers.md#language-analyzer-list) ou [analisadores especializados independentes de linguagem](index-add-custom-analyzers.md#AnalyzerTable) que modificam a análise de texto. Um exemplo é [palavra-chave](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) que trata todo o conteúdo de um campo como um único token. É útil para dados como códigos postais, IDs e alguns nomes de produtos. | 
| Pesquisa filtrados | [Expressão de filtro OData](query-odata-filter-orderby-syntax.md) e qualquer analisador | As consultas de filtro avaliam uma expressão booleana em todos os campos *filtráveis* em um índice. Ao contrário de pesquisa, uma consulta de filtro corresponde o conteúdo exato de um campo, incluindo diferenciação de maiúsculas e minúsculas em campos de cadeia de caracteres. Outra diferença é que as consultas de filtro são expressas na sintaxe OData. <br/>[Exemplo de expressão de filtro](search-query-simple-examples.md#example-3-filter-queries) |
| Pesquisa geográfica | [Tipo de EDM. geographypoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) no campo expressão de filtro e qualquer analisador | As coordenadas armazenadas em um campo com um Edm.GeographyPoint são usadas para controles de pesquisa "localizar perto de mim" ou baseados em mapa. <br/>[Exemplo de pesquisa geográfica](search-query-simple-examples.md#example-5-geo-search)|
| Pesquisa de intervalo | expressão de filtro e analisador simples | Na Pesquisa do Azure, as consultas de intervalo são criadas usando o parâmetro de filtro. <br/>[Exemplo de filtro de intervalo](search-query-simple-examples.md#example-4-range-filters) | 
| [Pesquisa por campo](query-lucene-syntax.md#bkmk_fields) | Parâmetro de pesquisa e o analisador completo | Crie uma expressão de consulta composta visando um único campo. <br/>[Exemplo de pesquisa por campo](search-query-lucene-examples.md#example-2-fielded-search) |
| [pesquisa difusa](query-lucene-syntax.md#bkmk_fuzzy) | Parâmetro de pesquisa e o analisador completo | Corresponde a termos com construção ou ortografia semelhantes. <br/>[Exemplo de pesquisa difusa](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [pesquisa por proximidade](query-lucene-syntax.md#bkmk_proximity) | Parâmetro de pesquisa e o analisador completo | Descobre condições que estão próximos uns dos outros em um documento. <br/>[Exemplo de pesquisa por proximidade](search-query-lucene-examples.md#example-4-proximity-search) |
| [aumento de termos](query-lucene-syntax.md#bkmk_termboost) | Parâmetro de pesquisa e o analisador completo | Classifica um documento mais alto se ele contiver o termo potencializado, em relação a outros que não contêm. <br/>[Exemplo de aumento de termo](search-query-lucene-examples.md#example-5-term-boosting) |
| [pesquisa com expressão regular](query-lucene-syntax.md#bkmk_regex) | Parâmetro de pesquisa e o analisador completo | Correspondências com base no conteúdo de uma expressão regular. <br/>[Exemplo de expressão regular](search-query-lucene-examples.md#example-6-regex) |
|  [pesquisa de curinga ou prefixo](query-lucene-syntax.md#bkmk_wildcard) | Parâmetro de pesquisa e o analisador completo | Correspondências baseadas em um prefixo e til (`~`) ou caractere único (`?`). <br/>[Exemplo de pesquisa de curinga](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Gerenciar resultados da pesquisa 

Os resultados da consulta são transmitidos como documentos JSON na API REST, embora, se você usar APIs do .NET, a serialização será interna. Você pode formatar resultados definindo parâmetros na consulta, selecionando campos específicos para a resposta.

Os parâmetros na consulta podem ser usados para estruturar o conjunto de resultados das seguintes maneiras:

+ Limitar ou enviar em lote o número de documentos nos resultados (50 por padrão)
+ Selecionar campos para incluir nos resultados
+ Definir uma ordem de classificação
+ Adicionar realces de ocorrência para destacar termos correspondentes no corpo dos resultados da pesquisa

### <a name="tips-for-unexpected-results"></a>Dicas para resultados inesperados

Ocasionalmente, a substância e não a estrutura dos resultados são inesperadas. Quando os resultados da consulta não forem os esperados, você poderá tentar essas modificações de consulta para verificar se os resultados melhoram:

+ Altere **`searchMode=any`** (padrão) para **`searchMode=all`** para exigir correspondências em todos os critérios, em vez de em qualquer um dos critérios. Isso é especialmente verdadeiro quando os operadores boolianos estão incluídos na consulta.

+ Altere a técnica de consulta se o texto ou a análise lexical for necessária, mas o tipo de consulta impede o processamento linguístico. Na pesquisa de texto completo, texto ou análise lexical autocorrects para erros de ortografia, formulários de plural a única palavra e até mesmo irregulares verbos ou substantivos. Para algumas consultas, como pesquisa difusa ou com curinga, a análise de texto não faz parte do pipeline de análise de consulta. Para alguns cenários, expressões regulares foram usadas como um solução alternativa. 

### <a name="paging-results"></a>Resultados da paginação
O Azure Search facilita implementar a paginação dos resultados da pesquisa. Ao usar os parâmetros **`top`** e **`skip`** , você pode emitir solicitações de pesquisa que permitem que você receba o conjunto total de resultados de pesquisa em subconjuntos organizáveis e ordenados que facilitam facilmente uma boa pesquisar práticas de interface do usuário. Ao receber esses subconjuntos menores de resultados, você também pode receber a contagem de documentos no conjunto total de resultados da pesquisa.

Você pode aprender mais sobre a paginação dos resultados da pesquisa no artigo [Como paginar os resultados da pesquisa no Azure Search](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Ordenando resultados
Ao receber os resultados de uma consulta de pesquisa, você pode solicitar que o Azure Search apresente os resultados ordenados por valores em um campo específico. Por padrão, o Azure Search ordena os resultados da pesquisa com base na classificação de pontuação da pesquisa de cada documento, que é derivada de [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Se você quiser que o Azure Search retorne os resultados ordenados por um valor diferente da pontuação da pesquisa, poderá usar o **`orderby`** parâmetro de pesquisa. Você pode especificar o valor do parâmetro **`orderby`** para incluir nomes de campo e chamadas para a [ **`geo.distance()` função**](query-odata-filter-orderby-syntax.md) para valores geoespaciais. Each expression can be followed by `asc` to indicate that results are requested in ascending order, and **`desc`** to indicate that results are requested in descending order. Ordem ascendente da classificação padrão.


### <a name="hit-highlighting"></a>Realce de ocorrência
Na Pesquisa do Azure, enfatizar a parte exata dos resultados de pesquisa que correspondem à consulta de pesquisa é facilitada usando **`highlight`** , **`highlightPreTag`** e **`highlightPostTag`** parâmetros. Você pode especificar quais campos *pesquisáveis* devem ter o texto correspondido enfatizado, bem como especificar as marcas da cadeia de caracteres exatas para anexar ao início e ao término do texto correspondente que o Azure Search retorna.

## <a name="see-also"></a>Consulte também

+ [Como a pesquisa de texto completo funciona na Pesquisa do Azure (arquitetura de análise de consulta)](search-lucene-query-architecture.md)
+ [Gerenciador de pesquisa](search-explorer.md)
+ [Como consultar em .NET](search-query-dotnet.md)
+ [Como consultar em REST](search-create-index-rest-api.md)
