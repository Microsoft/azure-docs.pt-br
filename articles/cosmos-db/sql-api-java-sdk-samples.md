---
title: 'API do SQL do Azure Cosmos DB: Exemplos do SDK do Java v4'
description: Encontre exemplos de Java no GitHub para tarefas comuns usando a API do SQL do Azure Cosmos DB, incluindo as operações do CRUD.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/23/2020
ms.custom: devx-track-java
ms.author: anfeldma
ms.openlocfilehash: 0b0bdd204b2aa322adeb7373911b90e742c71999
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018982"
---
# <a name="azure-cosmos-db-sql-api-java-sdk-v4-examples"></a>API do SQL do Azure Cosmos DB: Exemplos do SDK do Java v4
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

> [!IMPORTANT]  
> Para saber mais sobre o SDK do Java v4, consulte as [Notas sobre a versão](sql-api-sdk-java-v4.md) do SDK do Java v4 do Azure Cosmos DB, o [repositório do Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), as [dicas de desempenho](performance-tips-java-sdk-v4-sql.md) do SDK do Java v4 do Azure Cosmos DB e o [guia de solução de problemas](troubleshoot-java-sdk-v4-sql.md) do SDK do Java v4 do Azure Cosmos DB. Se você estiver usando uma versão mais antiga do que a v4, confira o guia [Migrar para o SDK do Java v4 do Azure Cosmos DB](migrate-java-v4-sdk.md) para obter ajuda na atualização para a v4.
>

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- Você pode [ativar benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Todos os meses, sua Assinatura do Visual Studio lhe oferece créditos que podem ser usados para serviços pagos do Azure.
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

Os aplicativos de exemplo mais recentes que executam operações CRUD e outras operações comuns nos recursos do Azure Cosmos DB estão incluídos no repositório GitHub [azure-cosmos-java-sql-api-samples](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples). Esse artigo fornece:

* Links para as tarefas em cada um dos arquivos de exemplo do projeto Java. 
* Links para o conteúdo de referência da API relacionada.

**Pré-requisitos**

Para executar esse aplicativo de exemplo, serão necessários:

* Java Development Kit 8
* SDK do Java v4 do Azure Cosmos DB

Opcionalmente, é possível usar o Maven para obter os últimos binários do SDK do Java v4 do Azure Cosmos DB para usar em seu projeto. O Maven adiciona automaticamente todas as dependências necessárias. Caso contrário, você poderá baixar diretamente as dependências listadas no arquivo pom.xml e adicioná-las ao seu caminho de compilação.

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**Executar aplicativos de exemplo**

Clone o repositório do exemplo:
```bash
$ git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples.git

$ cd azure-cosmos-java-sql-api-samples
```

Você pode executar os exemplos usando um IDE (Eclipse, IntelliJ ou VSCODE) ou na linha de comando usando o Maven.

Essas variáveis de ambiente devem ser definidas

```
ACCOUNT_HOST=your account hostname;ACCOUNT_KEY=your account primary key
```

para dar aos exemplos acesso de leitura/gravação à sua conta.

Para executar um exemplo, especifique sua Classe principal 

```
com.azure.cosmos.examples.sample.synchronicity.MainClass
```

em que *sample.synchronicity.MainClass* pode ser
* crudquickstart.sync.SampleCRUDQuickstart
* crudquickstart.async.SampleCRUDQuickstartAsync
* indexmanagement.sync.SampleIndexManagement
* indexmanagement.async.SampleIndexManagementAsync
* storedprocedure.sync.SampleStoredProcedure
* storedprocedure.async.SampleStoredProcedureAsync
* changefeed.SampleChangeFeedProcessor *(Changefeed tem apenas um exemplo assíncrono, sem nenhum exemplo síncrono.)* ...etc...

> [!NOTE]
> Cada exemplo é autossuficiente, eles se configuram e fazem sua limpeza sozinhos. Os exemplos emitem várias chamadas para criar um `CosmosContainer`. Cada vez que isso é feito, sua assinatura é cobrada por 1 hora de uso por nível de desempenho da coleção criada. 
> 
> 

## <a name="database-examples"></a>Exemplos de banco de dados
O arquivo [Exemplos de CRUD do banco de dados](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java) mostra como executar as tarefas a seguir. Para saber mais sobre os bancos de dados Azure Cosmos antes de executar os exemplos a seguir, veja o artigo conceitual [Trabalhando com bancos de dados, contêineres e itens](account-databases-containers-items.md). 

| Tarefa | Referência de API |
| --- | --- |
| [Criar um banco de dados](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L77-L85) | CosmosClient.createDatabaseIfNotExists |
| [Ler um banco de dados por ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L88-L95) | CosmosClient.getDatabase |
| [Ler todos os bancos de dados](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L98-L112) | CosmosClient.readAllDatabases |
| [Excluir um banco de dados](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/databasecrud/sync/DatabaseCRUDQuickstart.java#L115-L123) | CosmosDatabase.delete |

## <a name="collection-examples"></a>Exemplos de coleção
O arquivo [Exemplos de CRUD da coleção](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) mostra como executar as tarefas a seguir. Para saber mais sobre as coleções do Azure Cosmos antes de executar os exemplos a seguir, veja o artigo conceitual [Trabalhando com bancos de dados, contêineres e itens](account-databases-containers-items.md).

| Tarefa | Referência de API |
| --- | --- |
| [Criar uma coleção](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L97-L112) | CosmosDatabase.createContainerIfNotExists |
| [Obter desempenho configurado de uma coleção](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L115-L123) | CosmosContainer.replaceProvisionedThroughput |
| [Obter uma coleção por ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L126-L133) | CosmosDatabase.getContainer |
| [Ler todas as coleções em um banco de dados](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L136-L150) | CosmosDatabase.readAllContainers |
| [Excluir uma coleção](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/containercrud/sync/ContainerCRUDQuickstart.java#L153-L161) | CosmosContainer.delete |

## <a name="autoscale-collection-examples"></a>Exemplos de coleção de dimensionamento automático

Para saber mais sobre o dimensionamento automático antes de executar esses exemplos, confira estas instruções para habilitar o dimensionamento automático em sua [conta](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-autoscale/) e em seus [bancos de dados e contêineres](./provision-throughput-autoscale.md).

O arquivo [Exemplos de CRUD do banco de dados de dimensionamento automático](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java) mostra como executar as tarefas a seguir.

| Tarefa | Referência de API |
| --- | --- |
| [Criar um banco de dados com a taxa de transferência máxima de dimensionamento automático especificada](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscaledatabasecrud/sync/AutoscaleDatabaseCRUDQuickstart.java#L78-L89) | CosmosClient.createDatabase<br>ThroughputProperties.createAutoscaledThroughput |

O arquivo [Exemplos de CRUD da coleção de dimensionamento automático](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java) mostra como executar as tarefas a seguir. 

| Tarefa | Referência de API |
| --- | --- |
| [Criar uma coleção com a taxa de transferência máxima de dimensionamento automático especificada](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L97-L110) | CosmosDatabase.createContainerIfNotExists |
| [Alterar a taxa de transferência máxima de dimensionamento automático configurada de uma coleção](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L113-L120) | CosmosContainer.replaceThroughput |
| [Ler a configuração de taxa de transferência de dimensionamento automático de uma coleção](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/autoscalecontainercrud/sync/AutoscaleContainerCRUDQuickstart.java#L122-L133) | CosmosContainer.readThroughput |

## <a name="analytical-storage-collection-examples"></a>Exemplos de coleta de armazenamento analítico

O arquivo [Exemplos de CRUD da coleção de armazenamento analítico](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java) mostra como executar as tarefas a seguir. Para saber mais sobre as coleções do Azure Cosmos antes de executar os seguintes exemplos, leia sobre o Synapse e o Armazenamento Analítico do Azure Cosmos DB.

| Tarefa | Referência de API |
| --- | --- |
| [Criar uma coleção](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/analyticalcontainercrud/sync/AnalyticalContainerCRUDQuickstart.java#L93-L108) | CosmosDatabase.createContainerIfNotExists |

## <a name="document-examples"></a>Exemplos de documento
O arquivo [Exemplos de CRUD do documento](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/DocumentCrudSamples.java) mostra como executar as tarefas a seguir. Para saber mais sobre os documentos do Azure Cosmos antes de executar os exemplos a seguir, veja o artigo conceitual [Trabalhando com bancos de dados, contêineres e itens](account-databases-containers-items.md).

| Tarefa | Referência de API |
| --- | --- |
| [Criar um documento](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L133-L147) | CosmosContainer.createItem |
| [Ler um documento por ID](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L179-L193) | CosmosContainer.readItem |
| [Consulta de documentos](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176) | CosmosContainer.queryItems |
| [Substituir um documento](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L195-L210) | CosmosContainer.replaceItem |
| [Inserir um documento](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L212-L2225) | CosmosContainer.upsertItem |
| [Excluir um documento](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L303-L310) | CosmosContainer.deleteItem |
| [Substituir um documento com a verificação de Etag condicional](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L227-L264) | AccessCondition.setType<br>AccessCondition.setCondition |
| [Ler apenas o documento se ele tiver sido alterado](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L266-L300) | AccessCondition.setType<br>AccessCondition.setCondition |

## <a name="indexing-examples"></a>Exemplos de indexação
O arquivo [Exemplos de CRUD da coleção](https://github.com/Azure/azure-documentdb-java/blob/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples/CollectionCrudSamples.java) mostra como executar as tarefas a seguir. Para saber mais sobre a indexação no Azure Cosmos DB antes de executar os exemplos a seguir, consulte os artigos conceituais [políticas de indexação](index-policy.md), [tipos de indexação](index-overview.md#index-types) e [caminhos de indexação](index-policy.md#include-exclude-paths). 

| Tarefa | Referência de API |
| --- | --- |
| Excluir um documento do índice | ExcludedIndex<br>IndexingPolicy |
| Usar indexação lenta | IndexingPolicy.IndexingMode |
| [Incluir caminhos dos documentos especificados no índice](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L145-L148) | IndexingPolicy.IncludedPaths |
| [Excluir caminhos dos documentos especificados do índice](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L150-L153) | IndexingPolicy.ExcludedPaths |
| [Criar um índice composto](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L171-L186) | IndexingPolicy.setCompositeIndexes<br>CompositePath |
| Forçar uma operação de verificação de intervalo em um caminho indexado por hash | FeedOptions.EnableScanInQuery |
| Usar índices de intervalo em cadeias de caracteres | IndexingPolicy.IncludedPaths<br>RangeIndex |
| Executar uma transformação de índice | - |
| [Criar um índice geoespacial](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/indexmanagement/sync/SampleIndexManagement.java#L157-L166) | IndexingPolicy.setSpatialIndexes<br>SpatialSpec<br>SpatialType |

Para obter mais informações sobre indexação, consulte [Políticas de indexação do Azure Cosmos DB](index-policy.md).

## <a name="query-examples"></a>Exemplos de consulta
O arquivo [Exemplos de consulta](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java) mostra como realizar as tarefas a seguir usando a gramática de consulta SQL. Para saber mais sobre a referência de consulta SQL no Azure Cosmos DB antes de executar as amostras a seguir, confira [Exemplos de consulta SQL do Azure Cosmos DB](./sql-query-getting-started.md). 

| Tarefa | Referência de API |
| --- | --- |
| [Consulta de todos os documentos](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L210-L214) | CosmosContainer.queryItems |
| [Consulta de igualdade usando = =](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L291-L295) | CosmosContainer.queryItems |
| [Consulta de desigualdade usando != e NOT](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L297-L305) | CosmosContainer.queryItems |
| [Consulta usando operadores de intervalo como >, <, >=, <=](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L307-L312) | CosmosContainer.queryItems |
| [Consulta usando operadores de intervalo em cadeias de caracteres](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L314-L319) | CosmosContainer.queryItems |
| [Consultar com ORDER BY](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L321-L326) | CosmosContainer.queryItems |
| [Consultar com DISTINCT](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L328-L333) | CosmosContainer.queryItems |
| [Consultar com funções de agregação](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L335-L343) | CosmosContainer.queryItems |
| [Trabalhar com subdocumentos](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L345-L353) | CosmosContainer.queryItems |
| [Consulta com junções dentro do documento](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L355-L377) | CosmosContainer.queryItems |
| [Consulta com cadeia de caracteres, operadores matemáticos e de matriz](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L379-L390) | CosmosContainer.queryItems |
| [Consulta com SQL parametrizada usando SqlQuerySpec](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421) |CosmosContainer.queryItems |
| [Consultar com paginação explícita](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216-L266) | CosmosContainer.queryItems |
| [Consultar coleções particionadas paralelamente](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L268-L289) | CosmosContainer.queryItems |
| Consultar com ORDER BY para coleções particionadas | CosmosContainer.queryItems |

## <a name="change-feed-examples"></a>Exemplos de feed de alteração 
O arquivo [Exemplo do processador do feed de alterações](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java) mostra como realizar as tarefas a seguir. Para saber mais sobre o feed de alterações no Azure Cosmos DB antes de executar as amostras a seguir, confira [Ler o feed de alterações do Azure Cosmos DB](read-change-feed.md) e [Processador do feed de alterações](change-feed-processor.md).

| Tarefa | Referência de API |
| --- | --- |
| [Funcionalidade básica do feed de alterações](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L124-L154) |ChangeFeedProcessor.changeFeedProcessorBuilder |
| Ler feed de alterações em um horário específico | ChangeFeedProcessor.changeFeedProcessorBuilder |
| [Ler feed de alterações do início](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/changefeed/SampleChangeFeedProcessor.java#L124-L154) | - |

## <a name="server-side-programming-examples"></a>Exemplos de programação do lado do servidor

O arquivo [Exemplo de procedimento armazenado](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java) mostra como realizar as tarefas a seguir. Para saber mais sobre a programação do servidor no Azure Cosmos DB antes de executar as amostras a seguir, confira [Procedimentos armazenados, gatilhos e funções definidas pelo usuário](stored-procedures-triggers-udfs.md).

| Tarefa | Referência de API |
| --- | --- |
| [Criar um procedimento armazenado](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L132-L151) | CosmosScripts.createStoredProcedure |
| [Executar um procedimento armazenado](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L167-L181) | CosmosStoredProcedure.execute |
| [Excluir um procedimento armazenado](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/storedprocedure/sync/SampleStoredProcedure.java#L183-L193) | CosmosStoredProcedure.delete |

## <a name="user-management-examples"></a>Exemplos de gerenciamento de usuário
O arquivo Exemplo de gerenciamento do usuário mostra como realizar as tarefas a seguir:

| Tarefa | Referência de API |
| --- | --- |
| Criar um usuário | - |
| Definir permissões em uma coleção ou um documento | - |
| Obter uma lista de permissões do usuário |- |
