---
title: Localizar cobrança de RU (unidade de solicitação) para uma consulta SQL no Azure Cosmos DB
description: Saiba como encontrar a cobrança de RU (unidade de solicitação) para consultas SQL executadas em um contêiner Cosmos do Azure. Você pode usar as linguagens portal do Azure, .NET, Java, Python e Node.js para encontrar a cobrança de RU.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 9d0694a76bca832887d30601711894b953fe22e1
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078434"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-sql-api"></a>Localizar o encargo de unidade de solicitação para operações executadas em Azure Cosmos DB API do SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O Azure Cosmos DB dá suporte a muitas APIs como SQL, MongoDB, Cassandra, Gremlin e Table. Cada API tem seu próprio conjunto de operações de banco de dados. Essas operações variam de simples leituras e gravações de pontos a consultas complexas. Cada operação de banco de dados consome recursos do sistema com base na complexidade da operação.

O custo de todas as operações do banco de dados é normalizado pelo Azure Cosmos DB e expresso por Unidades de Solicitação (ou RUs, abreviado na sigla em inglês). Você pode considerar o RUs como uma moeda de desempenho abstraindo os recursos do sistema, como CPU, IOPS e memória, necessários para executar as operações de banco de dados com suporte pelo Azure Cosmos DB. Independentemente da API usada para interagir com o contêiner do Azure Cosmos DB, os custos sempre serão medidos por RUs. Se a operação do banco de dados é uma gravação, uma leitura de ponto ou uma consulta, os custos são sempre medidos em RUs. Para saber mais, consulte o artigo [sobre as unidades de solicitação e suas considerações](request-units.md) .

Este artigo apresenta as diferentes maneiras pelas quais você pode encontrar o consumo da ru ( [unidade de solicitação](request-units.md) ) para qualquer operação executada em um contêiner em Azure Cosmos DB API do SQL. Se você estiver usando uma API diferente, consulte [API para MongoDB](find-request-unit-charge-mongodb.md), [API do Cassandra](find-request-unit-charge-cassandra.md), [api Gremlin](find-request-unit-charge-gremlin.md)e artigos de [API de tabela](find-request-unit-charge-table.md) para localizar a cobrança de ru/s.

Atualmente, você só pode medir esse consumo usando o portal do Azure ou inspecionando a resposta enviada do Azure Cosmos DB por meio de um dos SDKs. Se você estiver usando a API do SQL, terá várias opções para encontrar o consumo de RU para uma operação em um contêiner do Azure Cosmos.

## <a name="use-the-azure-portal"></a>Usar o portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos](create-sql-api-dotnet.md#create-account) e alimente-a com alguns dados ou selecione uma conta do Azure Cosmos existente que já contenha dados.

1. Acesse o painel do **Data Explorer** e, em seguida, selecione o contêiner no qual deseja trabalhar.

1. Selecione **Nova Consulta SQL** .

1. Insira uma consulta válida e, em seguida, selecione **Executar Consulta** .

1. Selecione **Estatísticas da Consulta** para exibir o preço de solicitação real da solicitação executada.

:::image type="content" source="./media/find-request-unit-charge/portal-sql-query.png" alt-text="Captura de tela do preço de solicitação de uma consulta SQL no portal do Azure":::

## <a name="use-the-net-sdk"></a>Usar o SDK .NET

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

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

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

Os objetos retornados do [SDK do .NET v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) expõem uma propriedade `RequestCharge`:

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

Para obter mais informações, consulte [início rápido: compilar um aplicativo Web .NET usando uma conta da API do SQL no Azure Cosmos DB](create-sql-api-dotnet.md).

---

## <a name="use-the-java-sdk"></a>Usar o SDK do Java

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

Para obter mais informações, consulte [início rápido: criar um aplicativo Java usando uma conta Azure Cosmos DB API do SQL](create-sql-api-java.md).

## <a name="use-the-nodejs-sdk"></a>Usar o SDK do Node.js

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

Para obter mais informações, consulte [início rápido: criar um aplicativo Node.js usando uma conta da API do SQL Azure Cosmos DB](create-sql-api-nodejs.md). 

## <a name="use-the-python-sdk"></a>Usar o SDK do Python

O objeto `CosmosClient` do [SDK do Python](https://pypi.org/project/azure-cosmos/) expõe um dicionário `last_response_headers` que mapeia todos os cabeçalhos retornados pela API HTTP subjacente para a última operação executada. O preço de solicitação está disponível na chave `x-ms-request-charge`:

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

Para obter mais informações, consulte [início rápido: criar um aplicativo Python usando uma conta Azure Cosmos DB API do SQL](create-sql-api-python.md). 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como otimizar o consumo de RU, confira estes artigos:

* [Unidades de solicitação e taxa de transferência no Azure Cosmos DB](request-units.md)
* [Otimizar a taxa de transferência provisionada no Azure Cosmos DB](optimize-cost-throughput.md)
* [Otimizar o custo de consulta no Azure Cosmos DB](./optimize-cost-reads-writes.md)
* [Taxa de transferência provisionada para dimensionamento global](./request-units.md)
* [Provisionar a taxa de transferência para contêineres e bancos de dados](set-throughput.md)
* [Provisionar a taxa de transferência para um contêiner](how-to-provision-container-throughput.md)
* [Monitorar e depurar com métricas no Azure Cosmos DB](use-metrics.md)