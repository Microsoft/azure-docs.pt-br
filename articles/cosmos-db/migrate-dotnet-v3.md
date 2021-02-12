---
title: Migre seu aplicativo para usar o SDK do .NET do Azure Cosmos DB 3,0 (com. Azure. Cosmos)
description: Saiba como atualizar seu aplicativo .NET existente do SDK v2 para o novo SDK do .NET v3 (pacote com. Azure. Cosmos) para a API básica (SQL).
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: 514a6c6daccfe63865ae0b2b9f5bf29c5cbedc29
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93334014"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v3"></a>Migrar seu aplicativo para usar o SDK do .NET Azure Cosmos DB v3
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Para saber mais sobre o SDK do .NET Azure Cosmos DB v3, consulte as [notas de versão](sql-api-sdk-dotnet-standard.md), o [repositório GitHub do .net](https://github.com/Azure/azure-cosmos-dotnet-v3), as dicas de [desempenho](performance-tips-dotnet-sdk-v3-sql.md)do SDK do .net v3 e o guia de [solução de problemas](troubleshoot-dot-net-sdk.md).
>

Este artigo destaca algumas das considerações de atualização de seu aplicativo .NET existente para a API do .NET SDK V3 para núcleo (SQL) mais recente Azure Cosmos DB. Azure Cosmos DB .NET SDK v3 corresponde ao namespace Microsoft. Azure. Cosmos. Você pode usar as informações fornecidas neste documento se estiver migrando seu aplicativo de qualquer um dos seguintes Azure Cosmos DB SDKs do .NET:

* SDK do Azure Cosmos DB .NET Framework V2 para API do SQL
* Azure Cosmos DB SDK do .NET Core v2 para API do SQL

As instruções neste artigo também ajudam a migrar as seguintes bibliotecas externas que agora fazem parte da API do Azure Cosmos DB .NET SDK V3 para núcleo (SQL):

* Biblioteca do processador do feed de alterações do .NET 2,0
* Biblioteca de executor em massa do .NET 1,1 ou superior

## <a name="whats-new-in-the-net-v3-sdk"></a>O que há de novo no SDK do .NET v3

O SDK do v3 contém muitas melhorias de usabilidade e desempenho, incluindo:

* Nomenclatura de modelo de programação intuitivo
* .NET Standard 2,0 * *
* Aumento do desempenho por meio do suporte à API de fluxo
* Hierarquia fluente que substitui a necessidade de alocador de URI
* Suporte interno para a biblioteca do processador do feed de alterações
* Suporte interno para operações em massa
* APIs Mockabale para um teste de unidade mais fácil
* Lote transacional e suporte mais incrivelmente
* Serializadores conectáveis
* Dimensionar contêineres não particionados e de dimensionamento automático

* * Os destinos do SDK .NET Standard 2,0 que unifica os SDKs existentes do Azure Cosmos DB .NET Framework e do .NET Core em um único SDK do .NET. Você pode usar o SDK do .NET em qualquer plataforma que implemente .NET Standard 2,0, incluindo os aplicativos .NET Framework 4.6.1 + e .NET Core 2.0 +.

A maioria das redes, da lógica de repetição e dos níveis inferiores do SDK permanece muito inalterada.

**O SDK do .NET Azure Cosmos DB v3 agora está em código aberto.** Agradecemos as solicitações de pull e registraremos em log problemas e controlaremos os comentários no [github.](https://github.com/Azure/azure-cosmos-dotnet-v3/) Vamos trabalhar para assumir todos os recursos que irão melhorar a experiência do cliente.

## <a name="why-migrate-to-the-net-v3-sdk"></a>Por que migrar para o SDK do .NET v3

Além das inúmeras melhorias de usabilidade e desempenho, novos investimentos em recursos feitos no SDK mais recente não serão portados de volta para versões mais antigas.

Embora não haja planos imediatos para [desativar o suporte para os sdks 2,0](sql-api-sdk-dotnet.md), os SDKs serão substituídos por versões mais recentes no futuro e o SDK entrará em modo de manutenção. Para obter a melhor experiência de desenvolvimento, recomendamos sempre começar com a versão mais recente com suporte do SDK.

## <a name="major-name-changes-from-v2-sdk-to-v3-sdk"></a>Alterações de nome principal do SDK v2 para o SDK v3

As seguintes alterações de nome foram aplicadas em todo o SDK do .NET 3,0 para se alinhar com as convenções de nomenclatura de API para a API básica (SQL):

* `DocumentClient` foi renomeado para `CosmosClient`
* `Collection` foi renomeado para `Container`
* `Document` foi renomeado para `Item`

Todos os objetos de recurso são renomeados com propriedades adicionais, que incluem o nome do recurso para fins de clareza.

A seguir estão algumas das alterações de nome de classe principal:

| SDK do .NET v2 | SDK do .NET v3 |
|-------------|-------------|
|`Microsoft.Azure.Documents.Client.DocumentClient`|`Microsoft.Azure.CosmosClient`|
|`Microsoft.Azure.Documents.Client.ConnectionPolicy`|`Microsoft.Azure.Cosmos.CosmosClientOptions`|
|`Microsoft.Azure.Documents.Client.DocumentClientException` |`Microsoft.Azure.Cosmos.CosmosException`|
|`Microsoft.Azure.Documents.Client.Database`|`Microsoft.Azure.Cosmos.DatabaseProperties`|
|`Microsoft.Azure.Documents.Client.DocumentCollection`|`Microsoft.Azure.Cosmos.ContainerProperties`|
|`Microsoft.Azure.Documents.Client.RequestOptions`|`Microsoft.Azure.Cosmos.ItemRequestOptions`|
|`Microsoft.Azure.Documents.Client.FeedOptions`|`Microsoft.Azure.Cosmos.QueryRequestOptions`|
|`Microsoft.Azure.Documents.Client.StoredProcedure`|`Microsoft.Azure.Cosmos.StoredProcedureProperties`|
|`Microsoft.Azure.Documents.Client.Trigger`|`Microsoft.Azure.Cosmos.TriggerProperties`|

### <a name="classes-replaced-on-net-v3-sdk"></a>Classes substituídas no SDK do .NET v3

As seguintes classes foram substituídas no SDK 3,0:

* `Microsoft.Azure.Documents.UriFactory`

* `Microsoft.Azure.Documents.Document`

* `Microsoft.Azure.Documents.Resource`

O Microsoft.Azure.Documents. A classe UriFactory foi substituída pelo design fluente. O design fluente cria URLs internamente e permite que um único `Container` objeto seja passado em vez de um `DocumentClient` , `DatabaseName` e `DocumentCollection` .

### <a name="changes-to-item-id-generation"></a>Alterações na geração de ID de item

A ID do item não é mais preenchida automaticamente no SDK do .NET v3. Portanto, a ID do item deve incluir especificamente uma ID gerada. Veja o exemplo a seguir:

```csharp
[JsonProperty(PropertyName = "id")]
public Guid Id { get; set; }
```

### <a name="changed-default-behavior-for-connection-mode"></a>Comportamento padrão alterado para o modo de conexão

O SDK v3 agora usa como padrão os modos de conexão direta + TCP em comparação com o SDK v2 anterior, que assumei como padrão os modos de conexões gateway + HTTPS. Essa alteração fornece desempenho e escalabilidade aprimoradas.

### <a name="changes-to-feedoptions-queryrequestoptions-in-v30-sdk"></a>Alterações no Feedoptions (QueryRequestOptions no SDK do v 3.0)

A `FeedOptions` classe no SDK v2 foi renomeada para `QueryRequestOptions` no SDK v3 e dentro da classe, várias propriedades tiveram alterações no nome e/ou valor padrão ou foram removidas completamente.  

`FeedOptions.MaxDegreeOfParallelism` foi renomeado para `QueryRequestOptions.MaxConcurrency` , e o valor padrão e o comportamento associado permanecem os mesmos, o lado do cliente de execução durante a execução de consulta paralela será executado em série sem paralelismo.

`FeedOptions.EnableCrossPartitionQuery` foi removido e o comportamento padrão no SDK 3,0 é que as consultas entre partições serão executadas sem a necessidade de habilitar a propriedade especificamente.

`FeedOptions.PopulateQueryMetrics` é habilitado por padrão com os resultados presentes na propriedade de diagnóstico da resposta.

`FeedOptions.RequestContinuation` Agora foi promovido para os próprios métodos de consulta.

As seguintes propriedades foram removidas:

* `FeedOptions.DisableRUPerMinuteUsage`

* `FeedOptions.EnableCrossPartitionQuery`

* `FeedOptions.JsonSerializerSettings`

* `FeedOptions.PartitionKeyRangeId`

* `FeedOptions.PopulateQueryMetrics`

### <a name="constructing-a-client"></a>Construindo um cliente

O SDK do .NET v3 fornece uma `CosmosClientBuilder` classe fluente que substitui a necessidade da fábrica de URI v2 do SDK.

O exemplo a seguir cria um novo `CosmosClientBuilder` com um ConsistencyLevel forte e uma lista de locais preferenciais:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder(
    accountEndpoint: "https://testcosmos.documents.azure.com:443/",
    authKeyOrResourceToken: "SuperSecretKey")
.WithConsistencyLevel(ConsistencyLevel.Strong)
.WithApplicationRegion(Regions.EastUS);
CosmosClient client = cosmosClientBuilder.Build();
```

### <a name="using-the-change-feed-processor-apis-directly-from-the-v3-sdk"></a>Usando as APIs do processador do feed de alterações diretamente do SDK v3

O SDK v3 tem suporte interno para as APIs do processador do feed de alterações, permitindo que você use o mesmo SDK para criar seu aplicativo e a implementação do processador do feed de alterações. Anteriormente, era necessário usar uma biblioteca separada do processador do feed de alterações.

Para obter mais informações, consulte [como migrar da biblioteca do processador do feed de alterações para o SDK Azure Cosmos DB .net v3](how-to-migrate-from-change-feed-library.md)

### <a name="using-the-bulk-executor-library-directly-from-the-v3-sdk"></a>Usando a biblioteca de executores em massa diretamente do SDK v3

O SDK v3 tem suporte interno para a biblioteca de executores em massa, permitindo que você use o mesmo SDK para compilar seu aplicativo e executar operações em massa. Anteriormente, era necessário usar uma biblioteca de executores em massa separada.

Para obter mais informações, consulte [como migrar da biblioteca de executor em massa para suporte em massa no SDK Azure Cosmos DB .net v3](how-to-migrate-from-bulk-executor-library.md)

## <a name="code-snippet-comparisons"></a>Comparações de trecho de código

O trecho de código a seguir mostra as diferenças em como os recursos são criados entre os SDKs do .NET V2 e V3:

## <a name="database-operations"></a>Operações de banco de dados

### <a name="create-a-database"></a>Criar um banco de dados

# <a name="net-sdk-v3"></a>[SDK v3 do .NET](#tab/dotnet-v3)

```csharp
// Create database with no shared provisioned throughput
DatabaseResponse databaseResponse = await client.CreateDatabaseIfNotExistsAsync(DatabaseName);
Database database = databaseResponse;
DatabaseProperties databaseProperties = databaseResponse;

// Create a database with a shared manual provisioned throughput
string databaseIdManual = new string(DatabaseName + "_SharedManualThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdManual, ThroughputProperties.CreateManualThroughput(400));

// Create a database with shared autoscale provisioned throughput
string databaseIdAutoscale = new string(DatabaseName + "_SharedAutoscaleThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdAutoscale, ThroughputProperties.CreateAutoscaleThroughput(4000));
```

# <a name="net-sdk-v2"></a>[SDK do .NET v2](#tab/dotnet-v2)

```csharp
// Create database
ResourceResponse<Database> databaseResponse = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = DatabaseName });
Database database = databaseResponse;

// Create a database with shared standard provisioned throughput
database = await client.CreateDatabaseIfNotExistsAsync(new Database{ Id = databaseIdStandard }, new RequestOptions { OfferThroughput = 400 });

// Creating a database with shared autoscale provisioned throughput from v2 SDK is not supported use v3 SDK
```
---

### <a name="read-a-database-by-id"></a>Ler um banco de dados pela ID

# <a name="net-sdk-v3"></a>[SDK v3 do .NET](#tab/dotnet-v3)

```csharp
// Read a database
Console.WriteLine($"{Environment.NewLine} Read database resource: {DatabaseName}");
database = client.GetDatabase(DatabaseName);
Console.WriteLine($"{Environment.NewLine} database { database.Id.ToString()}");

// Read all databases
string findQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(findQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id.ToString()}");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[SDK do .NET v2](#tab/dotnet-v2)

```csharp
// Read a database
database = await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine("\n database {0}", database.Id.ToString());

// Read all databases
Console.WriteLine("\n1.1 Reading all databases resources");
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    Console.WriteLine("\n database {0} \n {1}", _database.Id.ToString(), _database.ToString());
}
```
---

### <a name="delete-a-database"></a>Excluir um banco de dados

# <a name="net-sdk-v3"></a>[SDK v3 do .NET](#tab/dotnet-v3)

```csharp
// Delete a database
await client.GetDatabase(DatabaseName).DeleteAsync();
Console.WriteLine($"{ Environment.NewLine} database {DatabaseName} deleted.");

// Delete all databases in an account
string deleteQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(deleteQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            await client.GetDatabase(_database.Id).DeleteAsync();
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id} deleted");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[SDK do .NET v2](#tab/dotnet-v2)

```csharp
// Delete a database
database = await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine(" database {0} deleted.", DatabaseName);

// Delete all databases in an account
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(_database.Id));
    Console.WriteLine("\n database {0} deleted", _database.Id);
}
```
---

## <a name="container-operations"></a>Operações de contêiner

### <a name="create-a-container-autoscale--time-to-live-with-expiration"></a>Criar um contêiner (autoescala + vida útil com expiração)

# <a name="net-sdk-v3"></a>[SDK v3 do .NET](#tab/dotnet-v3)

```csharp
private static async Task CreateManualThroughputContainer(Database database)
{
    // Set throughput to the minimum value of 400 RU/s manually configured throughput
    string containerIdManual = ContainerName + "_Manual";
    ContainerResponse container = await database.CreateContainerIfNotExistsAsync(
        id: containerIdManual,
        partitionKeyPath: partitionKeyPath,
        throughput: 400);
}

// Create container with autoscale
private static async Task CreateAutoscaleThroughputContainer(Database database)
{
    string autoscaleContainerId = ContainerName + "_Autoscale";
    ContainerProperties containerProperties = new ContainerProperties(autoscaleContainerId, partitionKeyPath);

    Container container = await database.CreateContainerIfNotExistsAsync(
        containerProperties: containerProperties,
        throughputProperties: ThroughputProperties.CreateAutoscaleThroughput(autoscaleMaxThroughput: 4000);
}

// Create a container with TTL Expiration
private static async Task CreateContainerWithTtlExpiration(Database database)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    ContainerProperties properties = new ContainerProperties
        (id: containerIdManualwithTTL,
        partitionKeyPath: partitionKeyPath);

    properties.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day

    ContainerResponse containerResponse = await database.CreateContainerIfNotExistsAsync(containerProperties: properties);
    ContainerProperties returnedProperties = containerResponse;
}
```

# <a name="net-sdk-v2"></a>[SDK do .NET v2](#tab/dotnet-v2)

```csharp
// Create a collection
private static async Task CreateManualThroughputContainer(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";

    // Set throughput to the minimum value of 400 RU/s manually configured throughput

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManual;
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });
}

private static async Task CreateAutoscaleThroughputContainer(DocumentClient client)
{
        // .NET v2 SDK does not support the creation of provisioned autoscale throughput containers
}

 private static async Task CreateContainerWithTtlExpiration(DocumentClient client)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManualwithTTL;
    collectionDefinition.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });

}
```
---

### <a name="read-container-properties"></a>Ler propriedades do contêiner

# <a name="net-sdk-v3"></a>[SDK v3 do .NET](#tab/dotnet-v3)

```csharp
private static async Task ReadContainerProperties(Database database)
{
    string containerIdManual = ContainerName + "_Manual";
    Container container = database.GetContainer(containerIdManual);
    ContainerProperties containerProperties = await container.ReadContainerAsync();
}
```

# <a name="net-sdk-v2"></a>[SDK do .NET v2](#tab/dotnet-v2)

```csharp
private static async Task ReadContainerProperties(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";
    DocumentCollection collection = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));
}
```
---

### <a name="delete-a-container"></a>Excluir um contêiner

# <a name="net-sdk-v3"></a>[SDK v3 do .NET](#tab/dotnet-v3)

```csharp
private static async Task DeleteContainers(Database database)
{
    string containerIdManual = ContainerName + "_Manual";

    // Delete a container
    await database.GetContainer(containerIdManual).DeleteContainerAsync();

    // Delete all CosmosContainer resources for a database
    using (FeedIterator<ContainerProperties> feedIterator = database.GetContainerQueryIterator<ContainerProperties>())
    {
        while (feedIterator.HasMoreResults)
        {
            foreach (ContainerProperties _container in await feedIterator.ReadNextAsync())
            {
                await database.GetContainer(_container.Id).DeleteContainerAsync();
                Console.WriteLine($"{Environment.NewLine}  deleted container {_container.Id}");
            }
        }
    }
}
```

# <a name="net-sdk-v2"></a>[SDK do .NET v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteContainers(DocumentClient client)
{
    // Delete a collection
    string containerIdManual = ContainerName + "_Manual";
    await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));

    // Delete all containers for a database
    foreach (var collection in await client.ReadDocumentCollectionFeedAsync(UriFactory.CreateDatabaseUri(DatabaseName)))
    {
        await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, collection.Id));
    }
}
```
---

## <a name="item-and-query-operations"></a>Operações de item e consulta

### <a name="create-an-item"></a>Criar um item

# <a name="net-sdk-v3"></a>[SDK v3 do .NET](#tab/dotnet-v3)

```csharp
private static async Task CreateItemAsync(Container container)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    ItemResponse<SalesOrder> response = await container.CreateItemAsync(salesOrder1,
        new PartitionKey(salesOrder1.AccountNumber));
}

private static async Task RunBasicOperationsOnDynamicObjects(Container container)
{
    // Dynamic Object
    dynamic salesOrder = new
    {
        id = "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    Console.WriteLine("\nCreating item");
    ItemResponse<dynamic> response = await container.CreateItemAsync<dynamic>(
        salesOrder, new PartitionKey(salesOrder.AccountNumber));
    dynamic createdSalesOrder = response.Resource;
}
```

# <a name="net-sdk-v2"></a>[SDK do .NET v2](#tab/dotnet-v2)

```csharp
private static async Task CreateItemAsync(DocumentClient client)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder1,
        new RequestOptions { PartitionKey = new PartitionKey("Account1")});
}

private static async Task RunBasicOperationsOnDynamicObjects(DocumentClient client)
{
    // Create a dynamic object
    dynamic salesOrder = new
    {
        id= "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    ResourceResponse<Document> response = await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder,
        new RequestOptions { PartitionKey = new PartitionKey(salesOrder.AccountNumber)});

    dynamic createdSalesOrder = response.Resource;
    }
```
---

### <a name="read-all-the-items-in-a-container"></a>Ler todos os itens em um contêiner

# <a name="net-sdk-v3"></a>[SDK v3 do .NET](#tab/dotnet-v3)

```csharp
private static async Task ReadAllItems(Container container)
{
    // Read all items in a container
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition: null,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 5
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder salesOrder = response.First();
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[SDK do .NET v2](#tab/dotnet-v2)

```csharp
private static async Task ReadAllItems(DocumentClient client)
{
    // Read all items in a collection
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    string continuationToken = null;
    do
    {
        var feed = await client.ReadDocumentFeedAsync(
            UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
            new FeedOptions { MaxItemCount = 5, RequestContinuation = continuationToken });
        continuationToken = feed.ResponseContinuation;
        foreach (Document document in feed)
        {
            SalesOrder salesOrder = (SalesOrder)(dynamic)document;
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.Add(salesOrder);

        }
    } while (continuationToken != null);
}
```
---

### <a name="query-items"></a>Itens de consulta

# <a name="net-sdk-v3"></a>[SDK v3 do .NET](#tab/dotnet-v3)

```csharp
private static async Task QueryItems(Container container)
{
    // Query for items by a property other than Id
    QueryDefinition queryDefinition = new QueryDefinition(
        "select * from sales s where s.AccountNumber = @AccountInput")
        .WithParameter("@AccountInput", "Account1");

    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();
    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 1
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder sale = response.First();
            Console.WriteLine($"\n Account Number: {sale.AccountNumber}; Id: {sale.Id};");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[SDK do .NET v2](#tab/dotnet-v2)

```csharp
private static async Task QueryItems(DocumentClient client)
{
    // Query for items by a property other than Id
    SqlQuerySpec querySpec = new SqlQuerySpec()
    {
        QueryText = "select * from sales s where s.AccountNumber = @AccountInput",
        Parameters = new SqlParameterCollection()
            {
                new SqlParameter("@AccountInput", "Account1")
            }
    };
    var query = client.CreateDocumentQuery<SalesOrder>(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        querySpec,
        new FeedOptions {EnableCrossPartitionQuery = true});

    var allSalesForAccount1 = query.ToList();

    Console.WriteLine($"\n1.4.2 Query found {allSalesForAccount1.Count} items.");
}
```
---

### <a name="delete-an-item"></a>Excluir um item

# <a name="net-sdk-v3"></a>[SDK v3 do .NET](#tab/dotnet-v3)

```csharp
private static async Task DeleteItemAsync(Container container)
{
    ItemResponse<SalesOrder> response = await container.DeleteItemAsync<SalesOrder>(
        partitionKey: new PartitionKey("Account1"), id: "SalesOrder3");
}
```

# <a name="net-sdk-v2"></a>[SDK do .NET v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteItemAsync(DocumentClient client)
{
    ResourceResponse<Document> response = await client.DeleteDocumentAsync(
        UriFactory.CreateDocumentUri(DatabaseName, ContainerName, "SalesOrder3"),
        new RequestOptions { PartitionKey = new PartitionKey("Account1") });
}
```
---

## <a name="next-steps"></a>Próximas etapas

* [Criar um aplicativo de console](sql-api-get-started.md) para gerenciar Azure Cosmos DB dados da API do SQL usando o SDK v3
* Saiba mais sobre [o que você pode fazer com o SDK do v3](sql-api-dotnet-v3sdk-samples.md)
