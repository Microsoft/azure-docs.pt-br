---
title: Pesquisar em BLOBs CSV
titleSuffix: Azure Cognitive Search
description: Extraia e importe CSV do armazenamento de BLOBs do Azure usando o modo de análise delimitedText.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: d9633031ca8358ab0498c2e806b22e6c4ddd3eab
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430472"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Como indexar BLOBs CSV usando o modo de análise de delimitedText e indexadores de blob no Azure Pesquisa Cognitiva

O [indexador de blob](search-howto-indexing-azure-blob-storage.md) pesquisa cognitiva do Azure fornece um `delimitedText` modo de análise para arquivos CSV que trata cada linha no CSV como um documento de pesquisa separado. Por exemplo, considerando o seguinte texto delimitado por vírgula, `delimitedText` resultaria em dois documentos no índice de pesquisa: 

```text
id, datePublished, tags
1, 2016-01-12, "azure-search,azure,cloud"
2, 2016-07-07, "cloud,mobile"
```

Sem o `delimitedText` modo de análise, todo o conteúdo do arquivo CSV seria tratado como um documento de pesquisa.

Sempre que você estiver criando vários documentos de pesquisa de um único BLOB, certifique-se de examinar [blobs de indexação para produzir vários documentos de pesquisa](search-howto-index-one-to-many-blobs.md) para entender como funcionam as atribuições de chave de documento. O indexador de blob é capaz de localizar ou gerar valores que definem exclusivamente cada novo documento. Especificamente, ele pode criar um tráfego `AzureSearch_DocumentKey` que gerou quando um blob é analisado em partes menores, em que o valor é usado como a chave do documento de pesquisa no índice.

## <a name="setting-up-csv-indexing"></a>Configurando a indexação de CSV

Para indexar BLOBs CSV, crie ou atualize uma definição de indexador com o `delimitedText` modo de análise em uma solicitação [criar indexador](/rest/api/searchservice/create-indexer) :

```http
{
  "name" : "my-csv-indexer",
  ... other indexer properties
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
}
```

`firstLineContainsHeaders` indica que a primeira linha (não vazia) de cada blob contém cabeçalhos.
Se os blobs não contêm uma linha de cabeçalho inicial, os cabeçalhos devem ser especificados na configuração do indexador: 

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 
```

É possível personalizar o caractere delimitador usando a configuração `delimitedTextDelimiter`. Por exemplo:

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }
```

> [!NOTE]
> Atualmente, apenas a codificação UTF-8 tem suporte. Se você precisar de suporte para outras codificações, vote nele em [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Quando você usa o modo de análise de texto delimitado, o Azure Pesquisa Cognitiva pressupõe que todos os BLOBs em sua fonte de dados serão CSV. Se você precisar dar suporte a uma combinação de blobs CSV e não CSV na mesma fonte de dados, vote nela em [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
>

## <a name="request-examples"></a>Exemplos de solicitação

Reunindo tudo isso, estes são os exemplos de carga completa. 

Fonte de dados: 

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
}   
```

Indexador:

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-csv-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```


