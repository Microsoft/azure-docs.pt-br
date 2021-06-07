---
title: Tipos de consultas
titleSuffix: Azure Cognitive Search
description: Saiba mais sobre os tipos de consultas com suporte no Pesquisa Cognitiva, incluindo texto livre, filtro, preenchimento automático e sugestões, pesquisa geográfica, consultas de sistema e pesquisa de documentos.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 21012848ba3624df6110eaea182beccc4646d234
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609268"
---
# <a name="querying-in-azure-cognitive-search"></a>Consultando no Azure Pesquisa Cognitiva

O Azure Pesquisa Cognitiva oferece uma linguagem de consulta avançada para dar suporte a uma ampla gama de cenários, da pesquisa de texto livre, a padrões de consulta altamente especificados. Este artigo descreve as solicitações de consulta e os tipos de consultas que você pode criar.

No Pesquisa Cognitiva, uma consulta é uma especificação completa de uma operação de viagem de ida e **`search`** volta, com parâmetros que informam a execução da consulta e moldam a resposta que retorna. Os parâmetros e os analisadores determinam o tipo de solicitação de consulta. O exemplo de consulta a seguir é uma consulta de texto livre com um operador booliano, usando os [documentos de pesquisa (API REST)](/rest/api/searchservice/search-documents), direcionando a coleção de documentos [Hotéis-Sample-index](search-get-started-portal.md) .

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "queryType": "simple",
    "searchMode": "all",
    "search": "restaurant +view",
    "searchFields": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "select": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

Os parâmetros usados durante a execução da consulta incluem:

+ **`queryType`** define o analisador, que é o [analisador de consulta simples padrão](search-query-simple-examples.md) (ideal para pesquisa de texto completo) ou o [analisador de consulta Lucene completo](search-query-lucene-examples.md) usado para constructos de consulta avançada, como expressões regulares, pesquisa de proximidade, pesquisa difusa e de curinga, para citar alguns.

+ **`searchMode`** Especifica se as correspondências se baseiam em critérios "todos" ou em "qualquer" na expressão. O padrão é Any.

+ **`search`** fornece os critérios de correspondência, geralmente os termos ou frases inteiras, com ou sem operadores. Qualquer campo atribuído como *pesquisável* no esquema de índice é um candidato para esse parâmetro.

+ **`searchFields`** restringe a execução da consulta a campos pesquisáveis específicos. Durante o desenvolvimento, é útil usar a mesma lista de campos para Select e Search. Caso contrário, uma correspondência pode ser baseada em valores de campo que você não pode ver nos resultados, criando incertezas sobre por que o documento foi retornado.

Parâmetros usados para formatar a resposta:

+ **`select`** Especifica quais campos retornar na resposta. Somente os campos marcados como *recuperáveis* no índice podem ser usados em uma instrução SELECT.

+ **`top`** Retorna o número especificado de documentos de melhor correspondência. Neste exemplo, apenas 10 ocorrências são retornadas. Você pode usar top e Skip (não mostrado) para paginar os resultados.

+ **`count`** informa quantos documentos em todo o índice correspondem em geral, o que pode ser maior que o retornado. 

+ **`orderby`** será usado se você quiser classificar os resultados por um valor, como uma classificação ou um local. Caso contrário, o padrão é usar a pontuação de relevância para classificar os resultados. Um campo deve ser atribuído como *classificável* para ser um candidato para esse parâmetro.

A lista acima é representativa, mas não exaustiva. Para obter a lista completa de parâmetros em uma solicitação de consulta, consulte [Pesquisar documentos (API REST)](/rest/api/searchservice/search-documents).

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Tipos de consultas

Com algumas exceções notáveis, uma solicitação de consulta itera em índices invertidos que são estruturados para verificações rápidas, em que uma correspondência pode ser encontrada em potencialmente qualquer campo, dentro de qualquer número de documentos de pesquisa. Em Pesquisa Cognitiva, a principal metodologia para localizar correspondências é a pesquisa de texto completo ou filtros, mas você também pode implementar outras experiências de pesquisa conhecidas como preenchimento automático ou pesquisa de localização geográfica. O restante deste artigo resume as consultas no Pesquisa Cognitiva e fornece links para mais informações e exemplos.

## <a name="full-text-search"></a>Pesquisa de texto completo

Se o seu aplicativo de pesquisa incluir uma caixa de pesquisa que coleta entradas de termo, a pesquisa de texto completo provavelmente será a operação de consulta que está fazendo o backup dessa experiência. A pesquisa de texto completo aceita termos ou frases passados em um **`search`** parâmetro em todos os campos *pesquisáveis* no índice. Operadores boolianos opcionais na cadeia de caracteres de consulta podem especificar critérios de inclusão ou exclusão. O analisador simples e o analisador completo dão suporte à pesquisa de texto completo.

No Pesquisa Cognitiva, a pesquisa de texto completo é criada no mecanismo de consulta do Apache Lucene. As cadeias de caracteres de consulta na pesquisa de texto completo passam por análise lexical para tornar as verificações mais eficientes. A análise inclui todos os termos de maiúsculas e minúsculas, removendo palavras de parada como "a" e reduzindo os termos para formulários raiz primitivos. O analisador padrão é o Lucene padrão.

Quando termos correspondentes são encontrados, o mecanismo de consulta reconstitui um documento de pesquisa que contém a correspondência usando a chave ou ID do documento para montar valores de campo, classifica os documentos em ordem de relevância e retorna os 50 principais (por padrão) na resposta ou em um número diferente, se você tiver especificado **`top`** .

Se você estiver implementando a pesquisa de texto completo, entender como o conteúdo é indexado ajudará você a depurar quaisquer anomalias de consulta. As consultas em cadeias de caracteres hifenizadas ou especiais podem precisar usar um analisador diferente do padrão Lucene padrão para garantir que o índice contenha os tokens corretos. Você pode substituir o padrão por [analisadores de linguagem](index-add-language-analyzers.md#language-analyzer-list) ou [analisadores especializados](index-add-custom-analyzers.md#built-in-analyzers) que modificam a análise léxica. Um exemplo é a [palavra-chave](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) que trata todo o conteúdo de um campo como um único token. É útil para dados como códigos postais, IDs e alguns nomes de produtos. Para obter mais informações, consulte [pesquisa de termo parcial e padrões com caracteres especiais](search-query-partial-matching.md).

Se você antecipar o uso intensivo de operadores boolianos, o que é mais provável em índices que contêm blocos de texto Grandes (um campo de conteúdo ou descrições longas), certifique-se de testar as consultas com o **`searchMode=Any|All`** parâmetro para avaliar o impacto dessa configuração na pesquisa booleana.

## <a name="autocomplete-and-suggested-queries"></a>Preenchimento automático e consultas sugeridas

[Os resultados de preenchimento automático ou sugerido](search-add-autocomplete-suggestions.md) são alternativas ao que disparam **`search`** solicitações de consulta sucessivas com base em entradas de cadeias de caracteres parciais (depois de cada caractere) em uma experiência de pesquisa conforme o tipo. Você pode usar **`autocomplete`** **`suggestions`** [o e o](tutorial-csharp-type-ahead-and-suggestions.md)parâmetro juntos ou separadamente, conforme descrito neste tutorial, mas não pode usá-los com o **`search`** . Os termos concluídos e as consultas sugeridas são derivados do conteúdo do índice. O mecanismo nunca retornará uma cadeia de caracteres ou sugestão que não existe em seu índice. Para obter mais informações, consulte [preenchimento automático (API REST)](/rest/api/searchservice/autocomplete) e [sugestões (API REST)](/rest/api/searchservice/suggestions).

## <a name="filter-search"></a>Pesquisa de filtro

Os filtros são amplamente usados em aplicativos que incluem Pesquisa Cognitiva. Em páginas de aplicativo, os filtros são geralmente visualizados como facetas em estruturas de navegação de link para filtragem direcionada pelo usuário. Os filtros também são usados internamente para expor fatias de conteúdo indexado. Por exemplo, você pode inicializar uma página de pesquisa usando um filtro em uma categoria de produto ou um idioma se um índice contiver campos em inglês e em francês.

Você também pode precisar de filtros para invocar um formulário de consulta especializado, conforme descrito na tabela a seguir. Você pode usar um filtro com uma pesquisa não especificada ( **`search=*`** ) ou com uma cadeia de caracteres de consulta que inclua termos, frases, operadores e padrões.

| Cenário de filtro | Descrição |
|-----------------|-------------|
| filtros de intervalo | No Pesquisa Cognitiva do Azure, as consultas de intervalo são criadas usando o parâmetro de filtro. Para obter mais informações e exemplos, consulte [exemplo de filtro de intervalo](search-query-simple-examples.md#example-5-range-filters). |
| Pesquisa de localização geográfica | Se um campo pesquisável for do [tipo EDM. GeographyPoint](/rest/api/searchservice/supported-data-types), você poderá criar uma expressão de filtro para "localizar perto de mim" ou controles de pesquisa baseados em mapa. Os campos que orientam a pesquisa geográfica contêm coordenadas. Para obter mais informações e um exemplo, consulte [exemplo de pesquisa geográfica](search-query-simple-examples.md#example-6-geo-search). |
| Navegação facetada | Uma estrutura de navegação de faceta se torna fundamental na navegação orientada pelo usuário quando você invoca um filtro em resposta a um `onclick` evento em uma faceta. Assim, as facetas e os filtros são disponibilizados em mãos. Se você adicionar navegação de faceta, precisará de filtros para concluir a experiência. Para obter mais informações, consulte [como criar um filtro de faceta](search-filters-facets.md). |

> [!NOTE]
> O texto usado em uma expressão de filtro não é analisado durante o processamento da consulta. Presume-se que a entrada de texto seja um padrão de caracteres textualmente sensível a maiúsculas e minúsculas que seja bem-sucedida ou falhe na correspondência. As expressões de filtro são construídas usando a [sintaxe do OData](query-odata-filter-orderby-syntax.md) e passadas em um **`filter`** parâmetro em todos os campos *filtráveis* no índice. Para obter mais informações, consulte [filtros no Azure pesquisa cognitiva](search-filters.md).

## <a name="document-look-up"></a>Pesquisa de documentos

Em contraste com os formulários de consulta descritos anteriormente, este recupera um único [documento de pesquisa por ID](/rest/api/searchservice/lookup-document), sem nenhuma pesquisa de índice ou verificação correspondente. Apenas um documento é solicitado e retornado. Quando um usuário seleciona um item nos resultados da pesquisa, recuperando o documento e preenchendo uma página de detalhes com campos é uma resposta típica e uma pesquisa de documento é a operação que dá suporte a ela.

## <a name="advanced-search-fuzzy-wildcard-proximity-regex"></a>Pesquisa avançada: difusa, curinga, proximidade, Regex

Um formulário de consulta avançada depende do analisador de Lucene completo e dos operadores que disparam um comportamento de consulta específico.

| Tipo de consulta | Uso | Obter mais informações e exemplos |
|------------|--------|------------------------------|
| [Pesquisa em campo](query-lucene-syntax.md#bkmk_fields) | **`search`**  meter **`queryType=full`**  | Crie uma expressão de consulta composta visando um único campo. <br/>[Exemplo de pesquisa em campo](search-query-lucene-examples.md#example-1-fielded-search) |
| [pesquisa difusa](query-lucene-syntax.md#bkmk_fuzzy) | **`search`** meter **`queryType=full`** | Corresponde a termos com construção ou ortografia semelhantes. <br/>[Exemplo de pesquisa difusa](search-query-lucene-examples.md#example-2-fuzzy-search) |
| [pesquisa por proximidade](query-lucene-syntax.md#bkmk_proximity) | **`search`** meter **`queryType=full`** | Descobre condições que estão próximos uns dos outros em um documento. <br/>[Exemplo de pesquisa por proximidade](search-query-lucene-examples.md#example-3-proximity-search) |
| [aumento de termo](query-lucene-syntax.md#bkmk_termboost) | **`search`** meter **`queryType=full`** | Classifica um documento mais alto se ele contiver o termo potencializado, em relação a outros que não contêm. <br/>[Exemplo de aumento de termo](search-query-lucene-examples.md#example-4-term-boosting) |
| [pesquisa de expressão regular](query-lucene-syntax.md#bkmk_regex) | **`search`** meter **`queryType=full`** | Correspondências com base no conteúdo de uma expressão regular. <br/>[Exemplo de expressão regular](search-query-lucene-examples.md#example-5-regex) |
|  [pesquisa de curinga ou prefixo](query-lucene-syntax.md#bkmk_wildcard) | **`search`** parâmetro com * *_`~`_* ou **`?`** , **`queryType=full`**| Correspondências baseadas em um prefixo e til (`~`) ou caractere único (`?`). <br/>[Exemplo de pesquisa de curinga](search-query-lucene-examples.md#example-6-wildcard-search) |

## <a name="next-steps"></a>Próximas etapas

Para uma análise mais detalhada da implementação da consulta, examine os exemplos de cada sintaxe. Se você for novo na pesquisa de texto completo, uma análise mais detalhada do que o mecanismo de consulta pode ser uma opção igualmente boa.

+ [Exemplos de consulta simples](search-query-simple-examples.md)
+ [Exemplos de consulta de sintaxe Lucene para criar consultas avançadas](search-query-lucene-examples.md)
+ [Como a pesquisa de texto completo funciona no Azure pesquisa cognitiva](search-lucene-query-architecture.md)git