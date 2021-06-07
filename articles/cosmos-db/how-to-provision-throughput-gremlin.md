---
title: Provisione a taxa de transferência em Azure Cosmos DB recursos da API Gremlin
description: Saiba como provisionar a taxa de transferência de contêiner, banco de dados e dimensionamento automático em Azure Cosmos DB recursos de API do Gremlin. Você usará portal do Azure, CLI, PowerShell e vários outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 3c2af7f33135a8c6621db233451231ffa89c2d64
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93086152"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-gremlin-api-resources"></a>Provisionar produtividade de banco de dados, contêiner ou dimensionamento automático em recursos de API Azure Cosmos DB Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Este artigo explica como provisionar a taxa de transferência em Azure Cosmos DB API Gremlin. Você pode provisionar a taxa de transferência padrão (manual) ou de dimensionamento automático em um contêiner ou um banco de dados e compartilhá-lo entre os contêineres no banco de dados. Você pode provisionar a taxa de transferência usando os SDKs portal do Azure, CLI do Azure ou Azure Cosmos DB.

Se você estiver usando uma API diferente, consulte os artigos [API do SQL](how-to-provision-container-throughput.md), [API do Cassandra](how-to-provision-throughput-cassandra.md), [API para MongoDB](how-to-provision-throughput-mongodb.md) para provisionar a taxa de transferência.

## <a name="azure-portal"></a><a id="portal-gremlin"></a> portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) ou selecione uma existente.

1. Abra o painel de **Data Explorer** e selecione **novo grafo**. Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um banco de dados ou usando um existente. Selecione a opção **provisionar taxa de transferência do banco de dados** se você quiser provisionar a taxa de transferência no nível do banco
   * Insira uma ID do grafo.
   * Insira um valor de chave de partição (por exemplo, `/ItemID`).
   * Insira uma taxa de transferência que você deseja provisionar (por exemplo, 1.000 RUs).
   * Selecione **OK**.

    :::image type="content" source="./media/how-to-provision-throughput-gremlin/provision-database-throughput-portal-gremlin-api.png" alt-text="Captura de tela de Data Explorer ao criar um novo grafo com taxa de transferência no nível do banco de dados":::

## <a name="net-sdk"></a>SDK .NET

> [!Note]
> Use os SDKs do cosmos para a API do SQL para provisionar a taxa de transferência para todas as APIs de Azure Cosmos DB, exceto a API Cassandra e MongoDB.

### <a name="provision-container-level-throughput"></a>Provisionar produtividade no nível do contêiner

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

### <a name="provision-database-level-throughput"></a>Provisionar produtividade no nível do banco

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager modelos podem ser usados para provisionar a taxa de transferência de dimensionamento automático em recursos de banco de dados ou de contêiner para todas as APIs de Azure Cosmos DB Consulte [Azure Resource Manager modelos para Azure Cosmos DB](templates-samples-gremlin.md) para obter exemplos.

## <a name="azure-cli"></a>CLI do Azure

CLI do Azure pode ser usado para provisionar a taxa de transferência de autoescala em um banco de dados ou recursos de nível de contêiner para todas as APIs de Azure Cosmos DB Para obter exemplos, consulte [exemplos de CLI do Azure para Azure Cosmos DB](cli-samples-gremlin.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell pode ser usado para provisionar a taxa de transferência de autoescala em um banco de dados ou recursos de nível de contêiner para todas as APIs de Azure Cosmos DB Para obter exemplos, consulte [exemplos de Azure PowerShell para Azure Cosmos DB](powershell-samples-gremlin.md).

## <a name="next-steps"></a>Próximas etapas

Confira os seguintes artigos para saber mais sobre o provisionamento de taxa de transferência no Azure Cosmos DB:

* [Unidades de solicitação e taxa de transferência no Azure Cosmos DB](request-units.md)