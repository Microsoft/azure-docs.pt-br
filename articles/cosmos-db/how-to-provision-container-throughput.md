---
title: Provisionar taxa de transferência de contêiner na API do SQL Azure Cosmos DB
description: Saiba como provisionar a taxa de transferência no nível de contêiner em Azure Cosmos DB API do SQL usando portal do Azure, CLI, PowerShell e vários outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 4caf43cb972b44dd1482b9e6e467e41cae294708
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100092"
---
# <a name="provision-standard-manual-throughput-on-an-azure-cosmos-container---sql-api"></a>Provisionar taxa de transferência padrão (manual) em um contêiner Cosmos do Azure-API do SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo explica como provisionar a taxa de transferência padrão (manual) em um contêiner em Azure Cosmos DB API do SQL. É possível provisionar a taxa de transferência em um único contêiner ou [provisionar a taxa de transferência em um banco de dados](how-to-provision-database-throughput.md) e compartilhá-la entre os contêineres no banco de dados. É possível provisionar a taxa de transferência em um contêiner usando o portal do Azure, a CLI do Azure ou SDKs do Azure Cosmos DB.

Se você estiver usando uma API diferente, confira [API para MongoDB](how-to-provision-throughput-mongodb.md), [API do Cassandra](how-to-provision-throughput-cassandra.md), artigos da [API Gremlin](how-to-provision-throughput-gremlin.md) para provisionar a taxa de transferência.

## <a name="azure-portal"></a>Portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos](create-sql-api-dotnet.md#create-account) ou selecione uma existente.

1. Abra o painel **Data Explorer** e selecione **novo contêiner** . Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um banco de dados ou usando um existente.
   * Insira uma ID de contêiner.
   * Insira um valor de chave de partição (por exemplo, `/ItemID`).
   * Insira uma taxa de transferência que você deseja provisionar (por exemplo, 1.000 RUs).
   * Selecione **OK** .

    :::image type="content" source="./media/how-to-provision-container-throughput/provision-container-throughput-portal-sql-api.png" alt-text="Captura de tela do Data Explorer, com Nova coleção realçado":::

## <a name="azure-cli-or-powershell"></a>CLI do Azure ou PowerShell

Para criar um contêiner com taxa de transferência dedicada confira,

* [Criar um contêiner com a CLI do Azure](manage-with-cli.md#create-a-container)
* [Criar um contêiner com o PowerShell](manage-with-powershell.md#create-container)

## <a name="net-sdk"></a>SDK .NET

> [!Note]
> Use os SDKs do cosmos para a API do SQL para provisionar a taxa de transferência para todas as APIs de Cosmos DB, exceto a API Cassandra e MongoDB.

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

---

## <a name="javascript-sdk"></a>SDK do JavaScript

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "containerId",
throughput: 1000
});

// To update an existing container or databases throughput, you need to user the offers API
// Get all the offers
const { resources: offers } = await client.offers.readAll().fetchAll();

// Find the offer associated with your container or the database
const offer = offers.find((_offer) => _offer.offerResourceId === resource._rid);

// Change the throughput value
offer.content.offerThroughput = 2000;

// Replace the offer.
await client.offer(offer.id).replace(offer);
```

## <a name="next-steps"></a>Próximas etapas

Confira os seguintes artigos para saber mais sobre o provisionamento de taxa de transferência no Azure Cosmos DB:

* [Como provisionar a taxa de transferência padrão (manual) em um banco de dados](how-to-provision-database-throughput.md)
* [Como provisionar a taxa de transferência de dimensionamento automático em um banco de dados](how-to-provision-autoscale-throughput.md)
* [Unidades de solicitação e taxa de transferência no Azure Cosmos DB](request-units.md)
