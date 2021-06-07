---
title: Pesquisar em BLOBs JSON
titleSuffix: Azure Cognitive Search
description: Rastreie BLOBs JSON do Azure para conteúdo de texto usando o indexador de blob Pesquisa Cognitiva do Azure. Os indexadores automatizam a ingestão de dados para fontes de dados selecionadas, como o armazenamento de blobs do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 8156966e9a1c000701a5cc1c68a70c4ee048c738
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259043"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Como indexar BLOBs JSON usando um indexador de blob no Azure Pesquisa Cognitiva

Este artigo mostra como [configurar um indexador de blob](search-howto-indexing-azure-blob-storage.md) para BLOBs que consistem em documentos JSON. Os BLOBs JSON no armazenamento de BLOBs do Azure normalmente pressupõem qualquer um desses formulários:

+ Um único documento JSON
+ Um documento JSON que contém uma matriz de elementos JSON bem formados
+ Um documento JSON que contém várias entidades, separadas por uma nova linha

O indexador de blob fornece um **`parsingMode`** parâmetro para otimizar a saída do documento de pesquisa com base nos modos de análise de estrutura consistem nas seguintes opções:

| parsingMode | Documento JSON | Descrição |
|--------------|-------------|--------------|
| **`json`** | Um por blob | os Analisa os BLOBs JSON como uma única parte do texto. Cada blob JSON se torna um único documento de pesquisa. |
| **`jsonArray`** | Múltiplos por blob | Analisa uma matriz JSON no BLOB, em que cada elemento da matriz se torna um documento de pesquisa separado.  |
| **`jsonLines`** | Múltiplos por blob | Analisa um blob que contém várias entidades JSON (também uma matriz), com elementos individuais separados por uma nova linha. O indexador inicia um novo documento de pesquisa após cada nova linha. |

Para o **`jsonArray`** e o **`jsonLines`** , você deve examinar a [indexação de um blob para produzir muitos documentos de pesquisa](search-howto-index-one-to-many-blobs.md) para entender como o indexador de blob trata a desambiguidade da chave do documento para vários documentos de pesquisa produzidos do mesmo BLOB.

Na definição do indexador, você pode opcionalmente definir [mapeamentos de campo](search-indexer-field-mappings.md) para escolher quais propriedades do documento JSON de origem serão usadas para preencher o índice de pesquisa de destino. Por exemplo, ao usar o **`jsonArray`** modo de análise, se a matriz existir como uma propriedade de nível inferior, você poderá definir uma **`document root`** Propriedade indicando onde a matriz é colocada dentro do blob.

As seções a seguir descrevem cada modo em mais detalhes. Se você não estiver familiarizado com os conceitos e clientes do indexador, consulte [criar um indexador de pesquisa](search-howto-create-indexers.md). Você também deve estar familiarizado com os detalhes da [configuração básica do indexador de blob](search-howto-indexing-azure-blob-storage.md), que não é repetida aqui.

<a name="parsing-single-blobs"></a>

## <a name="index-single-json-documents-one-per-blob"></a>Indexar documentos JSON únicos (um por blob)

Por padrão, os indexadores de blob analisam BLOBs JSON como uma única parte do texto, um documento de pesquisa para cada blob em um contêiner. Se o JSON for estruturado, o documento de pesquisa poderá refletir essa estrutura, com elementos individuais representados como campos individuais. Por exemplo, suponha que você tem o seguinte documento JSON no armazenamento de BLOBs do Azure:

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2020-04-13",
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

O indexador de blob analisa o documento JSON em um único documento de pesquisa, carregando um índice, correspondendo a "text", "datePublished" e "tags" da origem em relação aos campos de índice de destino com nomes idênticos e digitados. Dado um índice com os campos "texto", "data de publicação e "marcas", o indexador blob pode inferir o mapeamento correto sem um campo de mapeamento presente na solicitação.

Embora o comportamento padrão seja um documento de pesquisa por blob JSON, definir o modo de análise ' JSON ' altera os mapeamentos de campo internos para conteúdo, promovendo campos dentro `content` dos campos reais no índice de pesquisa. Uma definição de indexador de exemplo para o **`json`** modo de análise pode ser parecida com esta:

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "json" } }
}
```

> [!NOTE]
> Assim como ocorre com todos os indexadores, se os campos não corresponderem claramente, você deve esperar especificar explicitamente os [mapeamentos de campo](search-indexer-field-mappings.md) individuais, a menos que esteja usando os mapeamentos de campos implícitos disponíveis para conteúdo e metadados de BLOB, conforme descrito em [configuração básica do indexador de blob](search-howto-indexing-azure-blob-storage.md).

### <a name="json-example-single-hotel-json-files"></a>exemplo de JSON (arquivos JSON de um único hotel)

O [conjunto de dados de documentos JSON do Hotel](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotel-json-documents) no GitHub é útil para testar a análise de JSON, em que cada blob representa um arquivo JSON estruturado. Você pode carregar os arquivos de dados no armazenamento de BLOBs e usar o assistente de **importação de dados** para avaliar rapidamente como esse conteúdo é analisado em documentos de pesquisa individuais. 

O conjunto de dados consiste em cinco BLOBs, cada um contendo um documento de Hotel com uma coleção de endereços e uma coleção de salas. O indexador de blob detecta as coleções e reflete a estrutura dos documentos de entrada no esquema de índice.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Analisar matrizes JSON

Como alternativa, você pode usar a opção de matriz JSON. Essa opção é útil quando os BLOBs contêm uma matriz de objetos JSON bem formados e você deseja que cada elemento se torne um documento de pesquisa separado. Usando **`jsonArrays`** o, o blob JSON a seguir produz três documentos separados, cada um com os `"id"` `"text"` campos e.  

```text
[
    { "id" : "1", "text" : "example 1" },
    { "id" : "2", "text" : "example 2" },
    { "id" : "3", "text" : "example 3" }
]
```

A **`parameters`** propriedade no indexador contém valores do modo de análise. Para uma matriz JSON, a definição do indexador deve ser semelhante ao exemplo a seguir.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

### <a name="jsonarrays-example-clinical-trials-sample-data"></a>exemplo de jsonArrays (dados de exemplo de testes clínicos)

O [conjunto de dados JSON de testes clínicos](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-json) no GitHub é útil para testar a análise de matriz JSON. Você pode carregar os arquivos de dados no armazenamento de BLOBs e usar o assistente de **importação de dados** para avaliar rapidamente como esse conteúdo é analisado em documentos de pesquisa individuais. 

O conjunto de dados consiste em oito BLOBs, cada um contendo uma matriz JSON de entidades, para um total de 100 entidades. As entidades variam de acordo com quais campos são populados, mas o resultado final é um documento de pesquisa por entidade, de todas as matrizes, em todos os BLOBs.

<a name="nested-json-arrays"></a>

### <a name="parsing-nested-json-arrays"></a>Analisando matrizes JSON aninhadas

Para matrizes JSON com elementos aninhados, você pode especificar um **`documentRoot`** para indicar uma estrutura de vários níveis. Por exemplo, se o seu blob tiver esta aparência:

```http
{
    "level1" : {
        "level2" : [
            { "id" : "1", "text" : "Use the documentRoot property" },
            { "id" : "2", "text" : "to pluck the array you want to index" },
            { "id" : "3", "text" : "even if it's nested inside the document" }  
        ]
    }
}
```

Use esta configuração para indexar a matriz contida na propriedade `level2`:

```http
{
    "name" : "my-json-array-indexer",
    ... other indexer properties
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
}
```

## <a name="parse-json-entities-separated-by-newlines"></a>Analisar entidades JSON separadas por novas linhas

Se o seu blob contiver várias entidades JSON separadas por uma nova linha e você desejar que cada elemento se torne um documento de pesquisa separado, use **`jsonLines`** .

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

Para linhas JSON, a definição do indexador deve ser semelhante ao exemplo a seguir.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
}
```

### <a name="jsonlines-example-caselaw-sample-data"></a>exemplo de jsonLines (dados de exemplo de caselaw)

O [conjunto de dados JSON caselaw](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) no GitHub é útil para testar a nova análise de linha JSON. Assim como ocorre com outros exemplos, você pode carregar esses dados no armazenamento de BLOBs e usar o assistente de **importação de dados** para avaliar rapidamente o impacto do modo de análise em BLOBs individuais.

O conjunto de dados consiste em um blob que contém 10 entidades JSON separadas por uma nova linha, em que cada entidade descreve um único caso legal. O resultado final é um documento de pesquisa por entidade.

## <a name="map-json-fields-to-search-fields"></a>Mapear campos JSON para campos de pesquisa

Os mapeamentos de campo são usados para associar um campo de origem a um campo de destino em situações em que os nomes e tipos de campo não são idênticos. Mas os mapeamentos de campo também podem ser usados para corresponder a partes de um documento JSON e "refazê-los" em campos de nível superior do documento de pesquisa.

O exemplo a seguir ilustra esse cenário. Para obter mais informações sobre mapeamentos de campo em geral, consulte [mapeamentos de campo](search-indexer-field-mappings.md).

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2016-04-13"
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

Digamos que você tenha um índice de pesquisa com os seguintes campos: `text` do tipo `Edm.String`, `date` do tipo `Edm.DateTimeOffset` e `tags` do tipo `Collection(Edm.String)`. Observe a discrepância entre "data de publicação" na fonte e campo `date` no índice. Para mapear seu JSON para a forma desejada, use os seguintes mapeamentos de campo:

```http
"fieldMappings" : [
    { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
    { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
    { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
    ]
```

Os campos de origem são especificados usando a notação de [ponteiro JSON](https://tools.ietf.org/html/rfc6901) . Comece com uma barra invertida para referir-se à raiz do documento JSON, então selecione a propriedade desejada (em nível arbitrário de aninhamento) usando um caminho separado por barra invertida.

Você também pode se referir a elementos individuais da matriz usando um índice com base em zero. Por exemplo, para selecionar o primeiro elemento da matriz "tags" do exemplo anterior, use um mapeamento de campo como este:

```http
{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> Se se **`sourceFieldName`** referir a uma propriedade que não existe no blob JSON, esse mapeamento será ignorado sem um erro. Esse comportamento permite que a indexação continue para BLOBs JSON que têm um esquema diferente (que é um caso de uso comum). Como não há nenhuma verificação de validação, verifique os mapeamentos com cuidado para erros de forma que você não esteja perdendo documentos por um motivo errado.
>

## <a name="next-steps"></a>Próximas etapas

+ [Configurar indexadores de BLOB](search-howto-indexing-azure-blob-storage.md)
+ [Definir mapeamentos de campo](search-indexer-field-mappings.md)
+ [Visão geral dos indexadores](search-indexer-overview.md)
+ [Como indexar BLOBs CSV com um indexador de BLOB](search-howto-index-csv-blobs.md)
+ [Tutorial: pesquisar dados semiestruturados do armazenamento de BLOBs do Azure](search-semi-structured-data.md)