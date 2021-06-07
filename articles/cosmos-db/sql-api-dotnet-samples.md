---
title: 'Azure Cosmos DB: Exemplos do .NET para a API do SQL'
description: Encontre exemplos de .NET C# no GitHub para tarefas comuns na API SQL do Azure Cosmos DB, incluindo operações CRUD.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/23/2019
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 2e7bfd3735a57ddfa5d6100e18cf7a5676c1b9b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019033"
---
# <a name="azure-cosmos-db-net-examples-for-the-sql-api"></a>Azure Cosmos DB: Exemplos do .NET para a API do SQL
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

O repositório GitHub [azure-cosmos-dotnet-v2](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples) inclui as soluções de amostra mais recentes do .NET para executar operações CRUD e outras operações comuns em recursos do Azure Cosmos DB. Esse artigo fornece:

* Links para as tarefas em cada um dos arquivos de exemplo do projeto C#. 
* Links para o conteúdo de referência da API relacionada.

Para obter exemplos de código do SDK do .NET versão 3.0 (versão prévia), confira as amostras mais recentes no repositório GitHub [azure-cosmos-dotnet-v3](https://github.com/Azure/azure-cosmos-dotnet-v3). 

## <a name="prerequisites"></a>Pré-requisitos

Visual Studio 2019 com o fluxo de trabalho de desenvolvimento do Azure instalado
- Você pode baixar e usar o [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) **gratuito**. Verifique se você habilitou o **desenvolvimento do Azure** durante a instalação do Visual Studio. 

O [pacote NuGet Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) 

Uma assinatura do Azure ou uma conta de avaliação gratuita do Cosmos DB
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
  
- Você pode [ativar benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Todos os meses, sua Assinatura do Visual Studio concede a você créditos que podem ser usados para serviços pagos do Azure.
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> As amostras são autossuficientes e são configuradas e limpas automaticamente com várias chamadas a [CreateDocumentCollectionAsync()](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync). Cada ocorrência cobra a assinatura por uma hora de uso no nível de desempenho da coleção. 
> 

## <a name="database-examples"></a>Exemplos de banco de dados
O método [RunDatabaseDemo](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L75-L91) do projeto *DatabaseManagement* de amostra mostra como executar as tarefas a seguir. Para saber mais sobre bancos de dados do Azure Cosmos antes de executar as amostras a seguir, confira [Trabalhar com bancos de dados, contêineres e itens](account-databases-containers-items.md). 

| Tarefa | Referência de API |
| --- | --- |
| [Criar um banco de dados](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L77) |[DocumentClient.CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync) |
| [Ler um banco de dados por ID](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L79) |[DocumentClient.ReadDatabaseAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdatabaseasync) |
| [Ler todos os bancos de dados](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L83) |[DocumentClient.ReadDatabaseFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdatabasefeedasync) |
| [Excluir um banco de dados](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DatabaseManagement/Program.cs#L89) |[DocumentClient.DeleteDatabaseAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedatabaseasync) |

## <a name="collection-examples"></a>Exemplos de coleção
O método [RunCollectionDemo](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L86-L104) do projeto *CollectionManagement* de amostra mostra como executar as tarefas a seguir. Para saber mais sobre as coleções do Azure Cosmos antes de executar as amostras a seguir, confira [Trabalhar com bancos de dados, contêineres e itens](account-databases-containers-items.md). 

| Tarefa | Referência de API |
| --- | --- |
| [Criar uma coleção](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L109) |[DocumentClient.CreateDocumentCollectionIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync) 
| [Obter desempenho configurado de uma coleção](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L190) |[DocumentClient.ReplaceOfferAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync) |
| [Obter uma coleção por ID](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L202) |[DocumentClient.ReadDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync) |
| [Ler todas as coleções em um banco de dados](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L215) |[DocumentClient.ReadDocumentCollectionFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionfeedasync) |
| [Excluir uma coleção](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/CollectionManagement/Program.cs#L228) |[DocumentClient.DeleteDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedocumentcollectionasync) |

## <a name="document-examples"></a>Exemplos de documento
O método [RunDocumentsDemo](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L109-L118) do projeto *DocumentManagement* de amostra mostra como executar as tarefas a seguir. Para saber mais sobre os documentos do Azure Cosmos antes de executar as amostras a seguir, confira [Trabalhar com bancos de dados, contêineres e itens](account-databases-containers-items.md). 

| Tarefa | Referência de API |
| --- | --- |
| [Criar um documento](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L154) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync) |
| [Ler um documento por ID](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L168) |[DocumentClient.ReadDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentasync) |
| [Ler todos os documentos em uma coleção](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L190) |[DocumentClient.ReadDocumentFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync) |
| [Consulta de documentos](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L18-L222) |[DocumentClient.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Substituir um documento](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L240-L242) |[DocumentClient.ReplaceDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentasync) |
| [Inserir um documento](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L254) |[DocumentClient.UpsertDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.upsertdocumentasync) |
| [Excluir um documento](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L275-L277) |[DocumentClient.DeleteDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedocumentasync) |
| [Trabalhando com objetos dinâmicos do .NET](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L349-L397) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync)<br>[DocumentClient.ReadDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentasync)<br>[DocumentClient.ReplaceDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentasync) |
| [Substituir o documento com a verificação de Etag condicional](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L405-L501) |[DocumentClient.AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition)<br>[Documents.Client.AccessConditionType](/dotnet/api/microsoft.azure.documents.client.accessconditiontype) |
| [Ler apenas o documento se ele tiver sido alterado](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/DocumentManagement/Program.cs#L454-L500) |[DocumentClient.AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition)<br>[Documents.Client.AccessConditionType](/dotnet/api/microsoft.azure.documents.client.accessconditiontype) |

## <a name="indexing-examples"></a>Exemplos de indexação
O método [RunIndexDemo](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L93-L115) do projeto *IndexManagement* de exemplo mostra como executar as tarefas a seguir. Para saber mais sobre a indexação no Azure Cosmos DB antes de executar as amostras a seguir, confira [políticas de índice](index-policy.md), [tipos de índice](index-overview.md#index-types) e [caminhos de índice](index-policy.md#include-exclude-paths). 

| Tarefa | Referência de API |
| --- | --- |
| [Excluir um documento do índice](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L123-L162) |[IndexingDirective.Exclude](/dotnet/api/microsoft.azure.documents.indexingdirective) |
| [Usar indexação lenta](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L174-L192) |[IndexingPolicy.IndexingMode](/dotnet/api/microsoft.azure.documents.indexingpolicy.indexingmode) |
| [Excluir caminhos do documento especificado do índice](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L202-L263) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.documents.indexingpolicy.excludedpaths) |
| [Forçar uma operação de verificação de intervalo em um caminho indexado de hash](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L305-L340) |[FeedOptions.EnableScanInQuery](/dotnet/api/microsoft.azure.documents.client.feedoptions.enablescaninquery) |
| [Usar índices de intervalo em cadeias de caracteres](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L265-L316) |[IndexingPolicy.IncludedPaths](/dotnet/api/microsoft.azure.documents.indexingpolicy.includedpaths)<br>[RangeIndex](/dotnet/api/microsoft.azure.documents.rangeindex) |
| [Executar uma transformação de índice](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/IndexManagement/Program.cs#L318-L370) |[ReplaceDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync) |

## <a name="geospatial-examples"></a>Exemplos geoespaciais
O método [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L94-L139) do projeto *Geospatial* de exemplo mostra como executar as tarefas a seguir.  Para saber mais sobre os dados GeoJSON e geoespaciais antes de executar as amostras a seguir, confira [Usar dados de localização GeoJSON e geoespaciais](./sql-query-geospatial-intro.md). 

| Tarefa | Referência de API |
| --- | --- |
| [Habilitar indexação geoespacial em uma nova coleção](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L48) |[IndexingPolicy](/dotnet/api/microsoft.azure.documents.indexingpolicy) <br> [IndexKind.Spatial](/dotnet/api/microsoft.azure.documents.indexkind) <br>[DataType.Point](/dotnet/api/microsoft.azure.documents.datatype) |
| [Inserir documentos com pontos GeoJSON](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L104-L114) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync) </br> [DataType.Point](/dotnet/api/microsoft.azure.documents.datatype) |
| [Localizar pontos dentro de uma distância especificada](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L158-L199) |[ST_DISTANCE](sql-query-st-distance.md) </br> [GeometryOperationExtensions.Distance](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.distance) |
| [Localizar pontos dentro de um polígono](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L204-L227) |[ST_WITHIN](sql-query-st-within.md) </br> [GeometryOperationExtensions.Within](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.distance) </br>[Polygon](/dotnet/api/microsoft.azure.documents.spatial.polygon) |
| [Habilitar indexação geoespacial em uma coleção existente](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L385-L391) |[DocumentClient.ReplaceDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync)<br>[DocumentCollection.IndexingPolicy](/dotnet/api/microsoft.azure.documents.documentcollection.indexingpolicy) |
| [Validar dados de ponto e polígono](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Geospatial/Program.cs#L290-L326) |[ST_ISVALID](sql-query-st-isvalid.md)<br>[ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md)<br>[GeometryOperationExtensions.IsValid](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.isvalid)<br>[GeometryOperationExtensions.IsValidDetailed](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.isvaliddetailed) |

## <a name="query-examples"></a>Exemplos de consulta
O método [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L64-L129) do projeto *Queries* de exemplo mostra como realizar as tarefas a seguir usando a gramática de consulta SQL, o provedor LINQ com consulta e o lambda. Para saber mais sobre a referência de consulta SQL no Azure Cosmos DB antes de executar as amostras a seguir, confira [Exemplos de consulta SQL do Azure Cosmos DB](./sql-query-getting-started.md). 

| Tarefa | Referência de API |
| --- | --- |
| [Consulta de todos os documentos](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L131-L147) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Consulta de igualdade usando = =](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L186-L198) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Consulta de desigualdade usando != e NOT](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L288-L332) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Consulta usando operadores de intervalo como >, <, >=, <=](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L334-L355) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Consulta usando operadores de intervalo em cadeias de caracteres](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L355-L370) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Consultar com ORDER BY](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L422-L446) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Consultar com funções de agregação](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L448-L496) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Trabalhar com subdocumentos](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L498-L524) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Consulta com junções dentro do documento](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L526-L540) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Consulta com cadeia de caracteres, operadores matemáticos e de matriz](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L636-L673) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Consulta com SQL parametrizada usando SqlQuerySpec](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L149-L184) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100))<br>[SqlQuerySpec](/dotnet/api/microsoft.azure.documents.sqlqueryspec) |
| [Consultar com paginação explícita](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L675-L734) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Consultar coleções particionadas paralelamente](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L736-L807) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [Consultar com ORDER BY para coleções particionadas](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/Queries/Program.cs#L809-L882) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |

## <a name="change-feed-examples"></a>Exemplos de feed de alteração 
O método [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/ChangeFeed/Program.cs#L54-L97) do projeto *ChangeFeed* de exemplo mostra como executar as tarefas a seguir. Para saber mais sobre o feed de alterações no Azure Cosmos DB antes de executar as amostras a seguir, confira [Ler o feed de alterações do Azure Cosmos DB](read-change-feed.md) e [Processador do feed de alterações](change-feed-processor.md). 

| Tarefa | Referência de API |
| --- | --- |
| [Ler feed de alteração](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/ChangeFeed/Program.cs#L132) |[DocumentClient.CreateDocumentChangeFeedQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery) | 
| [Ler intervalos de chaves de partição](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/ChangeFeed/Program.cs#L118) |[DocumentClient.ReadPartitionKeyRangeFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpartitionkeyrangefeedasync) | 

A amostra de processador do feed de alterações, [ChangeFeedMigrationTool](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/ChangeFeedMigrationTool), descreve como usar a biblioteca de processadores do feed de alterações para replicar dados para outro contêiner do Cosmos.   

## <a name="server-side-programming-examples"></a>Exemplos de programação do lado do servidor
O método [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/ServerSideScripts/Program.cs#L58-L91) do projeto *ServerSideScripts* de exemplo mostra como executar as tarefas a seguir. Para saber mais sobre a programação do servidor no Azure Cosmos DB antes de executar as amostras a seguir, confira [Procedimentos armazenados, gatilhos e funções definidas pelo usuário](stored-procedures-triggers-udfs.md). 

| Tarefa | Referência de API |
| --- | --- |
| [Criar um procedimento armazenado](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L110) |[DocumentClient.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createstoredprocedureasync) |
| [Executar um procedimento armazenado](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L125) |[DocumentClient.ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.executestoredprocedureasync) |
| [Ler uma alimentação de documento para um procedimento armazenado](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L198) |[DocumentClient.ReadDocumentFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync) |
| [Criar um procedimento armazenado com ORDER BY](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L223) |[DocumentClient.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createstoredprocedureasync) |
| [Criar um pré-gatilho](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L273) |[DocumentClient.CreateTriggerAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createtriggerasync) |
| [Criar um pós-gatilho](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L341) |[DocumentClient.CreateTriggerAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createtriggerasync) |
| [Criar uma UDF (função definida pelo usuário)](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L421) |[DocumentClient.CreateUserDefinedFunctionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createuserdefinedfunctionasync) |

## <a name="user-management-examples"></a>Exemplos de gerenciamento de usuário
O método [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples/UserManagement/Program.cs#L55-L129) do projeto *UserManagement* de exemplo mostra como executar as tarefas a seguir:

| Tarefa | Referência de API |
| --- | --- |
| [Criar um usuário](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs#L93) |[DocumentClient.CreateUserAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createuserasync) |
| [Definir permissões em uma coleção ou documento](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs#L97) |[DocumentClient.CreatePermissionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createpermissionasync) |
| [Obter uma lista de permissões do usuário](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs#L241) |[DocumentClient.ReadUserAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readuserasync)<br>[DocumentClient.ReadPermissionFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpermissionfeedasync) |
