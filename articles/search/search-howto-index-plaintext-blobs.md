---
title: Pesquisar por blobs de texto sem formatação
titleSuffix: Azure Cognitive Search
description: Configure um indexador de pesquisa para extrair texto sem formatação de BLOBs do Azure para pesquisa de texto completo no Azure Pesquisa Cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 422346430e32ccb8745d5a5d829c5d61089a99c6
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430421"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Como indexar blobs de texto sem formatação no Azure Pesquisa Cognitiva

Ao usar um [indexador de blob](search-howto-indexing-azure-blob-storage.md) para extrair texto pesquisável para pesquisa de texto completo, você pode invocar vários modos de análise para obter resultados de indexação melhores. Por padrão, o indexador analisa o conteúdo do blob como uma única parte do texto. No entanto, se todos os BLOBs contiverem texto sem formatação na mesma codificação, você poderá melhorar significativamente o desempenho de indexação usando o `text` modo de análise.

Você deve usar o `text` modo de análise quando:

+ O tipo de arquivo é. txt
+ Os arquivos são de qualquer tipo, mas o próprio conteúdo é texto (por exemplo, código-fonte do programa, HTML, XML e assim por diante). Para arquivos em um idioma de marcação, qualquer caractere de sintaxe será ativado como texto estático.

Lembre-se de que os indexadores serializam para JSON. O conteúdo do arquivo de texto inteiro será indexado em um campo grande como `"content": "<file-contents>"` . Instruções de nova linha e de retorno são expressas como `\r\n\` .

Se você quiser um resultado mais granular, considere as seguintes soluções:

+ [`delimitedText`](search-howto-index-csv-blobs.md) modo de análise, se a origem for CSV
+ [ `jsonArray` ou `jsonLines` ](search-howto-index-json-blobs.md), se a origem for JSON

Uma terceira opção para dividir o conteúdo em várias partes requer recursos avançados na forma de [enriquecimento de ia](cognitive-search-concept-intro.md). Ele adiciona uma análise que identifica e atribui partes do arquivo a campos de pesquisa diferentes. Você pode encontrar uma solução completa ou parcial por meio [de habilidades internas](cognitive-search-predefined-skills.md), mas uma solução mais provável seria o modelo de aprendizado que compreende o seu conteúdo, articulado no modelo de aprendizado personalizado, encapsulado em uma [habilidade personalizada](cognitive-search-custom-skill-interface.md).

## <a name="set-up-plain-text-indexing"></a>Configurar a indexação de texto sem formatação

Para indexar blobs de texto sem formatação, crie ou atualize uma definição de indexador com a `parsingMode` propriedade de configuração para `text` em uma solicitação [criar indexador](/rest/api/searchservice/create-indexer) :

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text" } }
}
```

Por padrão, a codificação `UTF-8` será assumida. Para especificar uma codificação diferente, use a propriedade de configuração `encoding`: 

```http
{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
}
```

## <a name="request-example"></a>Exemplo de solicitação

Os modos de análise são especificados na definição do indexador.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-plaintext-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```

## <a name="next-steps"></a>Próximas etapas

+ [Indexadores na Pesquisa Cognitiva do Azure](search-indexer-overview.md)
+ [Como configurar um indexador de BLOB](search-howto-indexing-azure-blob-storage.md)
+ [Visão geral da indexação de BLOB](search-blob-storage-integration.md)