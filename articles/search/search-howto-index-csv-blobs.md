---
title: Indexar blobs CSV com o indexador de blobs do Azure Search – Azure Search
description: Rastrear blobs CSV no armazenamento de blobs do Azure para pesquisa de texto completo usando um índice do Azure Search. Os indexadores automatizam a ingestão de dados para fontes de dados selecionadas, como o armazenamento de blobs do Azure.
ms.date: 03/01/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 0bbb131b5fb155443c8c3dc340185f3a6fa950a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871256"
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Indexando blobs CSV com o indexador de blobs do Azure Search
Por padrão, o [indexador de blobs do Azure Search](search-howto-indexing-azure-blob-storage.md) analisa blobs de texto delimitado como um único bloco de texto. No entanto, com blobs contendo dados CSV, o ideal é tratar cada linha no blob como um documento separado. Por exemplo, considerando o seguinte texto delimitado, você pode querer analisá-lo em dois documentos, cada um contendo os campos "id", "datePublished" e "tags": 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

Neste artigo, você aprenderá como analisar blobs CSV com um indexador de blob do Azure Search. 

> [!NOTE]
> Siga as recomendações de configuração do indexador no [indexação de um-para-muitos](search-howto-index-one-to-many-blobs.md) para a saída de vários documentos de pesquisa de um blob do Azure.

## <a name="setting-up-csv-indexing"></a>Configurando a indexação de CSV
Para indexar blobs CSV, criar ou atualizar uma definição de indexador com o modo de análise `delimitedText` :  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

Para saber mais sobre a API Criar Indexador, veja [Criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

`firstLineContainsHeaders` indica que a primeira linha (não vazia) de cada blob contém cabeçalhos.
Se os blobs não contêm uma linha de cabeçalho inicial, os cabeçalhos devem ser especificados na configuração do indexador: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

É possível personalizar o caractere delimitador usando a configuração `delimitedTextDelimiter`. Por exemplo: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Atualmente, apenas a codificação UTF-8 tem suporte. Se você precisar de suporte para outras codificações, vote nele em [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Quando você usa o modo de análise de texto delimitado, o Azure Search pressupõe que todos os blobs em sua fonte de dados serão CSV. Se você precisar dar suporte a uma combinação de blobs CSV e não CSV na mesma fonte de dados, vote nela em [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Exemplos de solicitação
Reunindo tudo isso, estes são os exemplos de carga completa. 

Fonte de dados: 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Ajude-nos a aprimorar o Azure Search
Caso você tenha solicitações de recursos ou ideias para melhorias, dê sua opinião em [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

