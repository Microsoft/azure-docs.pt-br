---
title: Indexadores para fontes de dados de rastreamento durante a indexação - Azure Search
description: Rastrear um banco de dados SQL do Azure, Azure Cosmos DB ou Armazenamento do Azure para extrair dados pesquisáveis e preencher um índice do Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 6871c75f0af662762ffac6e7346d4999e55eb937
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303434"
---
# <a name="indexers-in-azure-search"></a>Indexadores no Azure Search

Um *indexador* no Azure Search é um rastreador que extrai dados pesquisáveis e metadados de uma fonte de dados externa do Azure e popula um índice com base nos mapeamentos de campo a campo entre o índice e a fonte de dados. Essa abordagem, às vezes é chamada de 'modelo de pull' porque o serviço obtém os dados sem a necessidade de escrever qualquer código que adiciona dados a um índice.

Os indexadores baseiam-se em plataformas com indexadores individuais para o SQL Server no Azure, Cosmos DB, armazenamento de tabelas do Azure e o armazenamento de BLOBs ou tipos de fonte de dados. Indexadores de armazenamento de blob tem propriedades adicionais específicas para tipos de conteúdo do blob.

Você pode usar um indexador como o único meio para ingestão de dados ou usar uma combinação de técnicas que incluam o uso de um indexador para carregar apenas alguns dos campos no índice.

Você pode executar os indexadores sob demanda ou em uma data recorrente agenda de atualização de que é executada a cada cinco minutos. Atualizações mais frequentes exigem um modelo de push que atualiza simultaneamente os dados no Azure Search e na fonte de dados externa.

## <a name="approaches-for-creating-and-managing-indexers"></a>Abordagens para criar e gerenciar indexadores

Você pode criar e gerenciar indexadores usando estas abordagens:

* [Portal > Assistente de dados de importação](search-import-data-portal.md)
* [API REST do Serviço](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Inicialmente, um novo indexador é anunciado como uma versão prévia do recurso. As versões prévias do recurso são introduzidas em APIs (REST e .NET) e então integradas ao portal após a graduação para a disponibilidade geral. Se você estiver avaliando um novo indexador, deverá planejar escrever código.

## <a name="permissions"></a>Permissões

Todas as operações relacionadas a indexadores, incluindo solicitações GET para o status ou definições, exigem um [chave de api de administração](search-security-api-keys.md). 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Fontes de dados com suporte

Armazenamentos de dados de rastreamento de indexadores no Azure.

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
* [Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md) 

> [!Note]
> Não há suporte para o armazenamento de tabela do Azure para [pesquisa cognitiva](cognitive-search-concept-intro.md).
>

## <a name="basic-configuration-steps"></a>Etapas da configuração básica
Os indexadores podem oferecer recursos que são exclusivos da fonte de dados. Nesse sentido, alguns aspectos de configuração da fonte de dados ou do indexador variam de acordo com o tipo de indexador. No entanto, todos os indexadores compartilham a mesma composição básica e os mesmos requisitos. As etapas que são comuns a todos os indexadores são abordadas a seguir.

### <a name="step-1-create-a-data-source"></a>Etapa 1: Criar uma fonte de dados
Um indexador obtém a conexão de fonte de dados de um *fonte de dados* objeto. A definição de fonte de dados fornece uma cadeia de caracteres de conexão e, possivelmente, credenciais. Chame o [criar fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source) API REST ou [classe DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) para criar o recurso.

As fontes de dados são configuradas e gerenciadas independentemente dos indexadores que as utilizam, o que significa que uma fonte de dados pode ser usada por vários indexadores para carregar mais de um índice por vez.

### <a name="step-2-create-an-index"></a>Etapa 2: Crie um índice
Um indexador automatizará algumas tarefas relacionadas à ingestão de dados, mas a criação de um índice não é uma delas. Como pré-requisito, você deve ter um índice predefinido com campos iguais aos da sua fonte de dados externa. Campos precisam corresponder ao nome e tipo de dados. Para saber mais sobre como estruturar um índice, confira [Criar um índice (API REST do Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) ou [classe Índice](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). Para obter ajuda com associações de campo, veja [Mapeamento de campos em indexadores do Azure Search](search-indexer-field-mappings.md).

> [!Tip]
> Embora indexadores não consigam gerar um índice para você, o assistente de **Importar dados** no portal pode ajudar. Na maioria dos casos, o assistente pode inferir um esquema de índice a partir de metadados existentes na fonte, apresentando um esquema de índice preliminar que você pode editar em linha enquanto o assistente estiver ativo. Assim que o índice é criado no serviço, outras edições no portal são, na sua maior parte, limitadas a adicionar novos campos. Leve o assistente em consideração para criar, mas não para revisar um índice. Para o aprendizado prático, percorra o [passo a passo portal](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Etapa 3: Criar e agendar o indexador
A definição do indexador é uma construção que reúne todos os elementos relacionados a ingestão de dados. Elementos necessários incluem uma fonte de dados e um índice. Elementos opcionais incluem uma agenda e campo mapeamentos. Mapeamento de campo só são opcionais se campos de origem e os campos de índice correspondem claramente. Um indexador pode referenciar uma fonte de dados de outro serviço, desde que a fonte de dados seja da mesma assinatura. Para saber maissobre como estruturar um indexador, confira [Criar indexador (API REST do Azure Search)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Executar indexadores sob demanda

Embora seja comum a agenda de indexação, um indexador também pode ser invocado sob demanda usando o [execute o comando](https://docs.microsoft.com/rest/api/searchservice/run-indexer):

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> Quando a API de Execução for retornada com êxito, isso indica que a invocação do indexador foi agendada, mas o processamento real ocorre de forma assíncrona. 

Você pode monitorar o status do indexador no portal ou por meio da API obter Status do indexador. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Obter status do indexador

Você pode recuperar o histórico de execução e status de um indexador por meio de [comando obter Status do indexador](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

A resposta contém o status geral do indexador, a última invocação (ou em andamento) do indexador e o histórico de invocações recentes do indexador.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

O histórico de execução contém até as 50 execuções mais recentes, que são classificadas em ordem cronológica inversa (de modo que a execução mais recente fique em primeiro lugar).

## <a name="next-steps"></a>Próximas etapas
Agora que você tem as noções básicas, a próxima etapa é examinar os requisitos e as tarefas específicas para cada tipo de fonte de dados.

* [Banco de Dados SQL do Azure ou SQL Server em uma máquina virtual do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
* [Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
* [Indexação de blobs CSV usando o indexador de Blobs do Azure Search](search-howto-index-csv-blobs.md)
* [Indexação de blobs JSON com o indexador de blobs do Azure Search](search-howto-index-json-blobs.md)
