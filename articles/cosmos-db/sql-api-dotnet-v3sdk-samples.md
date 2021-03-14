---
title: 'Azure Cosmos DB: exemplos do .NET (Microsoft.Azure.Cosmos) para a API do SQL'
description: Encontre os exemplos de SDK do .NET C# V3 no GitHub para tarefas comuns usando a API SQL do Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2019
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 30ccd5783b3fa9524072235a58b3c8708962ef1c
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102433974"
---
# <a name="azure-cosmos-dbnet-v3-sdk-microsoftazurecosmos-examples-for-the-sql-api"></a>Exemplos de SDK do .NET V3 do Azure Cosmos DB (Microsoft.Azure.Cosmos) para a API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Exemplos de SDK do .NET V2](sql-api-dotnet-samples.md)
> * [Exemplos de SDK do .NET V3](sql-api-dotnet-v3sdk-samples.md)
> * [Exemplos do SDK do Java V4](sql-api-java-sdk-samples.md)
> * [Exemplos do SDK do Spring Data V3](sql-api-spring-data-sdk-samples.md)
> * [Exemplos do Node.js](sql-api-nodejs-samples.md)
> * [Exemplos do Python](sql-api-python-samples.md)
> * [Galeria de Exemplos de Código do Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
>
>

O repositório GitHub [azure-cosmos-dotnet-v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage) inclui as soluções mais recentes de exemplo do .NET para executar operações CRUD e outras operações comuns em recursos do Azure Cosmos DB. Se você estiver familiarizado com a versão anterior do SDK do .NET, poderá estar acostumado com os termos coleção e documento. Como o Azure Cosmos DB é compatível com vários modelos de API, a versão 3.0 do SDK do .NET usa os termos genéricos “contêiner” e “item”. Um contêiner pode ser uma coleção, um gráfico ou uma tabela. Um item pode ser um documento, borda/vértice ou linha e descreve o conteúdo do contêiner. Esse artigo fornece:

* Links para as tarefas em cada um dos arquivos de exemplo do projeto C#.
* Links para o conteúdo de referência da API relacionada.

## <a name="prerequisites"></a>Pré-requisitos

Visual Studio 2019 com o fluxo de trabalho de desenvolvimento do Azure instalado

- Você pode baixar e usar o [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) **gratuito**. Verifique se você habilitou o **desenvolvimento do Azure** durante a instalação do Visual Studio.

   O [pacote NuGet Microsoft.Azure.cosmos](https://www.nuget.org/packages/Microsoft.Azure.cosmos/)

Uma assinatura do Azure ou uma conta de avaliação gratuita do Cosmos DB

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Você pode [ativar benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Todos os meses, sua Assinatura do Visual Studio concede a você créditos que podem ser usados para serviços pagos do Azure.
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> As amostras são autossuficientes e são configuradas e limpas automaticamente. Cada ocorrência cobra a assinatura por uma hora de uso no nível de desempenho do contêiner.
> 

## <a name="database-examples"></a>Exemplos de banco de dados

O método [RunDatabaseDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L65-L91) do projeto *DatabaseManagement* de amostra mostra como executar as tarefas a seguir. Para saber mais sobre bancos de dados do Azure Cosmos antes de executar as amostras a seguir, confira [Trabalhar com bancos de dados, contêineres e itens](account-databases-containers-items.md).

| Tarefa | Referência de API |
| --- | --- |
| [Criar um banco de dados](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L68) |[CosmosClient.CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync) |
| [Ler um banco de dados por ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L80) |[Database.ReadAsync](/dotnet/api/microsoft.azure.cosmos.database.readasync) |
| [Leia todos os bancos de dados de uma conta](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L96) |[CosmosClient.GetDatabaseQueryIterator](/dotnet/api/microsoft.azure.cosmos.cosmosclient.getdatabasequeryiterator) |
| [Excluir um banco de dados](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L106) |[Database.DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync) |

## <a name="container-examples"></a>Exemplos de contêiner

O método [RunContainerDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L69-L89) do projeto *ContainerManagement* de amostra mostra como executar as tarefas a seguir. Para saber mais sobre os contêineres do Azure Cosmos antes de executar os exemplos a seguir, confira [Trabalhar com bancos de dados, contêineres e itens](account-databases-containers-items.md).

| Tarefa | Referência de API |
| --- | --- |
| [Criar um contêiner](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L97-L107) |[Database.CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync) |
| [Criar um contêiner com a política de índice personalizada](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L161-L178) |[Database.CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync) |
| [Alterar o desempenho configurado de um contêiner](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L198-L223) |[Container.ReplaceThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync) |
| [Obter um contêiner por ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L225-L236) |[Container.ReadContainerAsync](/dotnet/api/microsoft.azure.cosmos.container.readcontainerasync) |
| [Ler todos os contêineres em um banco de dados](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L242-L258) |[Database.GetContainerQueryIterator](/dotnet/api/microsoft.azure.cosmos.database.getcontainerqueryiterator) |
| [Excluir um contêiner](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L264-L270) |[Container.DeleteContainerAsync](/dotnet/api/microsoft.azure.cosmos.container.deletecontainerasync) |

## <a name="item-examples"></a>Exemplos de item

O método [RunItemsDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L119-L130) do projeto *ItemManagement* de exemplo mostra como executar as tarefas a seguir. Para saber mais sobre itens do Azure Cosmos antes de executar as amostras a seguir, confira [Trabalhar com bancos de dados, contêineres e itens](account-databases-containers-items.md).

| Tarefa | Referência de API |
| --- | --- |
| [Criar um item](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L161-L200) |[Container.CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync) |
| [Ler um item por ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L203-L241) |[container.ReadItemAsync](/dotnet/api/microsoft.azure.cosmos.container.readitemasync) |
| [Consultar itens](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L244-L320) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator) |
| [Substituir um item](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L415-L456) |[container.ReplaceItemAsync](/dotnet/api/microsoft.azure.cosmos.container.replaceitemasync) |
| [Fazer upsert de um item](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L458-L509) |[container.UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync) |
| [Excluir um item](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L511-L522) |[container.DeleteItemAsync](/dotnet/api/microsoft.azure.cosmos.container.deleteitemasync) |
| [Substituir um item pela verificação de ETag condicional](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L682-L772) |[RequestOptions.IfMatchEtag](/dotnet/api/microsoft.azure.cosmos.requestoptions.ifmatchetag) |

## <a name="indexing-examples"></a>Exemplos de indexação

O método [RunIndexDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L108-L122) do projeto *IndexManagement* de exemplo mostra como executar as tarefas a seguir. Para saber mais sobre a indexação no Azure Cosmos DB antes de executar as amostras a seguir, confira [políticas de índice](index-policy.md), [tipos de índice](index-overview.md#index-types) e [caminhos de índice](index-policy.md#include-exclude-paths). 

| Tarefa | Referência de API |
| --- | --- |
| [Excluir um item do índice](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L130-L186) |[IndexingDirective.Exclude](/dotnet/api/microsoft.azure.cosmos.indexingdirective) |
| [Usar indexação lenta](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L198-L220) |[IndexingPolicy.IndexingMode](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.indexingmode) |
| [Excluir caminhos de item especificados do índice](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L230-L297) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.excludedpaths) |

## <a name="query-examples"></a>Exemplos de consulta

O método [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L76-L96) do projeto *Queries* de exemplo mostra como realizar as tarefas a seguir usando a gramática de consulta SQL, o provedor LINQ com consulta e o lambda. Para saber mais sobre a referência de consulta SQL no Azure Cosmos DB antes de executar as amostras a seguir, confira [Exemplos de consulta SQL do Azure Cosmos DB](./sql-query-getting-started.md).

| Tarefa | Referência de API |
| --- | --- |
| [Consultar itens de uma partição única](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L154-L186) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator) |
| [Consultar itens de várias partições](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L215-L275) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator) |
| [Consultar usando uma instrução SQL](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L189-L212) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator) |

## <a name="change-feed-examples"></a>Exemplos de feed de alteração

O método [RunBasicChangeFeed](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) do projeto *ChangeFeed* de exemplo mostra como executar as tarefas a seguir. Para saber mais sobre o feed de alterações no Azure Cosmos DB antes de executar as amostras a seguir, confira [Ler o feed de alterações do Azure Cosmos DB](read-change-feed.md) e [Processador do feed de alterações](change-feed-processor.md).

| Tarefa | Referência de API |
| --- | --- |
| [Funcionalidade básica do feed de alterações](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder) |
| [Ler feed de alterações a partir de um horário específico](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L127-L162) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder) |
| [Ler feed de alterações do início](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L170-L198) |[ChangeFeedProcessorBuilder.WithStartTime(DateTime)](/dotnet/api/microsoft.azure.cosmos.changefeedprocessorbuilder.withstarttime) |
| [Migrar do processador do feed de alterações para o feed de alterações no SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L256-L333) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder) |

## <a name="server-side-programming-examples"></a>Exemplos de programação do lado do servidor

O método [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L72-L102) do projeto *ServerSideScripts* de exemplo mostra como executar as tarefas a seguir. Para saber mais sobre a programação do servidor no Azure Cosmos DB antes de executar as amostras a seguir, confira [Procedimentos armazenados, gatilhos e funções definidas pelo usuário](stored-procedures-triggers-udfs.md).

| Tarefa | Referência de API |
| --- | --- |
| [Criar um procedimento armazenado](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L116) |[Scripts.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.createstoredprocedureasync) |
| [Executar um procedimento armazenado](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L135) |[Scripts.ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.executestoredprocedureasync) |
| [Excluir um procedimento armazenado](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L351) |[Scripts.DeleteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.deletestoredprocedureasync) |
