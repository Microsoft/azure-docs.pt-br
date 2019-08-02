---
title: Localizar o encargo de RU (Unidade de Solicitação) no Azure Cosmos DB
description: Saiba como encontrar o preço de RU (Unidade de Solicitação) para qualquer operação executada em um contêiner do Azure Cosmos.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 06/14/2019
ms.author: thweiss
ms.openlocfilehash: 96c36067456a49a5760d6fde488dcb4ad8311a90
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356452"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>Encontrar o preço de Unidade de Solicitação no Azure Cosmos DB

Este artigo apresenta as diferentes maneiras de encontrar o consumo de [RU](request-units.md) (Unidade de Solicitação) para qualquer operação executada em um contêiner do Azure Cosmos DB. Atualmente, você só pode medir esse consumo usando o portal do Azure ou inspecionando a resposta enviada do Azure Cosmos DB por meio de um dos SDKs.

## <a name="sql-core-api"></a>API de SQL (Core)

Se você estiver usando a API do SQL, terá várias opções para encontrar o consumo de RU para uma operação em um contêiner do Azure Cosmos.

### <a name="use-the-azure-portal"></a>Use o Portal do Azure

Atualmente, você pode encontrar o preço de solicitação no portal do Azure somente para uma consulta SQL.

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos](create-sql-api-dotnet.md#create-account) e alimente-a com alguns dados ou selecione uma conta do Azure Cosmos existente que já contenha dados.

1. Acesse o painel do **Data Explorer** e, em seguida, selecione o contêiner no qual deseja trabalhar.

1. Selecione **Nova Consulta SQL**.

1. Insira uma consulta válida e, em seguida, selecione **Executar Consulta**.

1. Selecione **Estatísticas da Consulta** para exibir o preço de solicitação real da solicitação executada.

![Captura de tela do preço de solicitação de uma consulta SQL no portal do Azure](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk"></a>Usar o SDK .NET
### <a name="net-v2-sdk"></a>SDK do .NET V2

Os objetos retornados do [SDK do .NET v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) expõem uma propriedade `RequestCharge`:

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

### <a name="net-v3-sdk"></a>SDK do .NET V3

Os objetos retornados do [SDK do .NET v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) expõem uma propriedade `RequestCharge`:

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

Para saber mais, confira [Início Rápido: Criar um aplicativo Web do .NET usando uma conta da API do SQL no Azure Cosmos DB](create-sql-api-dotnet.md).

### <a name="use-the-java-sdk"></a>Usar o SDK do Java

Os objetos retornados do [SDK do Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) expõem um método `getRequestCharge()`:

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

Para saber mais, confira [Início Rápido: Criar um aplicativo Java usando uma conta da API do SQL do Azure Cosmos DB](create-sql-api-java.md).

### <a name="use-the-nodejs-sdk"></a>Usar o SDK do Node.js

Os objetos retornados do [SDK do Node.js](https://www.npmjs.com/package/@azure/cosmos) expõem um subobjeto `headers` que mapeia todos os cabeçalhos retornados pela API HTTP subjacente. O preço de solicitação está disponível na chave `x-ms-request-charge`:

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

Para saber mais, confira [Início Rápido: Criar um aplicativo do Node.js usando uma conta da API do SQL do Azure Cosmos DB](create-sql-api-nodejs.md). 

### <a name="use-the-python-sdk"></a>Usar o SDK do Python

O objeto `CosmosClient` do [SDK do Python](https://pypi.org/project/azure-cosmos/) expõe um dicionário `last_response_headers` que mapeia todos os cabeçalhos retornados pela API HTTP subjacente para a última operação executada. O preço de solicitação está disponível na chave `x-ms-request-charge`:

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

Para saber mais, confira [Início Rápido: Criar um aplicativo do Python usando uma conta da API do SQL do Azure Cosmos DB](create-sql-api-python.md). 

## <a name="azure-cosmos-db-api-for-mongodb"></a>API do Azure Cosmos DB para MongoDB

O preço de RU é exposto por um [comando de banco de dados](https://docs.mongodb.com/manual/reference/command/) personalizado chamado `getLastRequestStatistics`. O comando retorna um documento que contém o nome da última operação executada, seu preço de solicitação e sua duração. Se você usar a API do Azure Cosmos DB para MongoDB, você terá várias opções para recuperar o preço de RU.

### <a name="use-the-azure-portal"></a>Use o Portal do Azure

Atualmente, você pode encontrar o preço de solicitação no portal do Azure somente para uma consulta.

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) e alimente-a com alguns dados ou selecione uma conta existente que já contenha dados.

1. Acesse o painel do **Data Explorer** e, em seguida, selecione a coleção na qual deseja trabalhar.

1. Selecione **Nova Consulta**.

1. Insira uma consulta válida e, em seguida, selecione **Executar Consulta**.

1. Selecione **Estatísticas da Consulta** para exibir o preço de solicitação real da solicitação executada.

![Captura de tela do preço de solicitação de consulta MongoDB no portal do Azure](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>Usar o driver do .NET do MongoDB

Ao usar o [driver oficial do .NET do MongoDB](https://docs.mongodb.com/ecosystem/drivers/csharp/), você pode executar comandos chamando o método `RunCommand` em um objeto `IMongoDatabase`. Esse método exige uma implementação da classe abstrata `Command<>`:

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

Para saber mais, confira [Início Rápido: Criar um aplicativo Web do .NET usando uma API do Azure Cosmos DB para MongoDB](create-mongodb-dotnet.md).

### <a name="use-the-mongodb-java-driver"></a>Usar o driver do Java do MongoDB


Ao usar o [driver oficial do Java do MongoDB](https://mongodb.github.io/mongo-java-driver/), você pode executar comandos chamando o método `runCommand` em um objeto `MongoDatabase`:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Para saber mais, confira [Início Rápido: Criar um aplicativo Web usando a API do Azure Cosmos DB para MongoDB e o SDK do Java](create-mongodb-java.md).

### <a name="use-the-mongodb-nodejs-driver"></a>Usar o driver do Node.js do MongoDB

Ao usar o [driver oficial do Node.js do MongoDB](https://mongodb.github.io/node-mongodb-native/), você pode executar comandos chamando o método `command` em um objeto `db`:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Para saber mais, confira [Início Rápido: Migrar um aplicativo Web do Node.js do MongoDB existente para o Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="cassandra-api"></a>API Cassandra

Quando você executa operações na API do Cassandra do Azure Cosmos DB, o preço de RU é retornado no conteúdo de entrada como um campo chamado `RequestCharge`. Você tem várias opções para recuperar o preço de RU.

### <a name="use-the-net-sdk"></a>Usar o SDK .NET

Ao usar o [SDK do .NET](https://www.nuget.org/packages/CassandraCSharpDriver/), você pode recuperar o conteúdo de entrada na propriedade `Info` de um objeto `RowSet`:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Para saber mais, confira [Início Rápido: Criar um aplicativo do Cassandra usando o SDK do .NET e o Azure Cosmos DB](create-cassandra-dotnet.md).

### <a name="use-the-java-sdk"></a>Usar o SDK do Java

Ao usar o [SDK do Java](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core), você pode recuperar o conteúdo de entrada chamando o método `getExecutionInfo()` em um objeto `ResultSet`:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Para saber mais, confira [Início Rápido: Criar um aplicativo do Cassandra usando o SDK do Java e o Azure Cosmos DB](create-cassandra-java.md).

## <a name="gremlin-api"></a>API do Gremlin

Ao usar a API do Gremlin, você tem várias opções para encontrar o consumo de RU para uma operação em um contêiner do Azure Cosmos. 

### <a name="use-drivers-and-sdk"></a>Usar drivers e o SDK

Os cabeçalhos retornados pela API do Gremlin são mapeados para atributos de status personalizados, que atualmente são exibidos pelo SDK do Java e do .NET do Gremlin. O encargo de solicitação está disponível na chave `x-ms-request-charge`.

### <a name="use-the-net-sdk"></a>Usar o SDK .NET

Quando você usa o [SDK do .NET do Gremlin](https://www.nuget.org/packages/Gremlin.Net/), os atributos de status ficam disponíveis na propriedade `StatusAttributes` do objeto `ResultSet<>`:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Para saber mais, confira [Início Rápido: Criar um aplicativo do .NET Framework ou do Core usando uma conta da API do Gremlin do Azure Cosmos DB](create-graph-dotnet.md).

### <a name="use-the-java-sdk"></a>Usar o SDK do Java

Quando você usa o [SDK do Java do Gremlin](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver), você pode recuperar os atributos de status chamando o método `statusAttributes()` no objeto `ResultSet`:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Para saber mais, confira [Início Rápido: Criar um banco de dados de grafo no Azure Cosmos DB usando o SDK do Java](create-graph-java.md).

## <a name="table-api"></a>API de Tabela

Atualmente, o único SDK que retorna o preço de RU para operações de tabela é o [SDK do .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). O objeto `TableResult` expõe uma propriedade `RequestCharge` que é populada pelo SDK quando usada na API de Tabela do Azure Cosmos DB:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Para saber mais, confira [Início Rápido: Criar um aplicativo de API de Tabela usando o SDK do .NET e o Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como otimizar o consumo de RU, confira estes artigos:

* [Unidades de solicitação e taxa de transferência no Azure Cosmos DB](request-units.md)
* [Otimizar a taxa de transferência provisionada no Azure Cosmos DB](optimize-cost-throughput.md)
* [Otimizar o custo de consulta no Azure Cosmos DB](optimize-cost-queries.md)
* [Taxa de transferência provisionada para dimensionamento global](scaling-throughput.md)
* [Provisionar a taxa de transferência para contêineres e bancos de dados](set-throughput.md)
* [Provisionar a taxa de transferência para um contêiner](how-to-provision-container-throughput.md)
* [Monitorar e depurar com métricas no Azure Cosmos DB](use-metrics.md)
