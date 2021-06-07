---
title: Provisionar taxa de transferência na API Azure Cosmos DB para recursos do MongoDB
description: Saiba como provisionar a taxa de transferência de contêiner, banco de dados e dimensionamento automático em Azure Cosmos DB API para recursos do MongoDB. Você usará portal do Azure, CLI, PowerShell e vários outros SDKs.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 25eaa4a66fb4a73f976edbd30e6f82015ce84f6f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93086118"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-api-for-mongodb-resources"></a>Provisionar produtividade de banco de dados, contêiner ou dimensionamento automático na API Azure Cosmos DB para recursos do MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Este artigo explica como provisionar a taxa de transferência na API Azure Cosmos DB para MongoDB. Você pode provisionar a taxa de transferência padrão (manual) ou de dimensionamento automático em um contêiner ou um banco de dados e compartilhá-lo entre os contêineres no banco de dados. Você pode provisionar a taxa de transferência usando os SDKs portal do Azure, CLI do Azure ou Azure Cosmos DB.

Se você estiver usando uma API diferente, confira [API do SQL](how-to-provision-container-throughput.md), [API do Cassandra](how-to-provision-throughput-cassandra.md), artigos da [API do Gremlin](how-to-provision-throughput-gremlin.md) para provisionar a taxa de transferência.

## <a name="azure-portal"></a><a id="portal-mongodb"></a> portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).

1. [Crie uma conta do Azure Cosmos](create-mongodb-dotnet.md#create-a-database-account) ou selecione uma existente.

1. Abra o painel **Data Explorer** e selecione **Nova Coleção**. Em seguida, forneça os seguintes detalhes:

   * Indique se você está criando um banco de dados ou usando um existente. Selecione a opção **provisionar taxa de transferência do banco de dados** se você quiser provisionar a taxa de transferência no nível do banco
   * Insira uma ID de coleção.
   * Insira um valor de chave de partição (por exemplo, `/ItemID`).
   * Insira uma taxa de transferência que você deseja provisionar (por exemplo, 1.000 RUs).
   * Selecione **OK**.

    :::image type="content" source="./media/how-to-provision-throughput-mongodb/provision-database-throughput-portal-mongodb-api.png" alt-text="Captura de tela de Data Explorer ao criar uma nova coleção com taxa de transferência no nível do banco de dados":::

> [!Note]
> Se você estiver provisionando a taxa de transferência em um contêiner em uma conta do Azure Cosmos configurada com a API do Azure Cosmos DB para MongoDB, use `/myShardKey` para o caminho da chave de partição.

## <a name="net-sdk"></a><a id="dotnet-mongodb"></a> SDK DO .NET

```csharp
// refer to MongoDB .NET Driver
// https://docs.mongodb.com/drivers/csharp

// Create a new Client
String mongoConnectionString = "mongodb://DBAccountName:Password@DBAccountName.documents.azure.com:10255/?ssl=true&replicaSet=globaldb";
mongoUrl = new MongoUrl(mongoConnectionString);
mongoClientSettings = MongoClientSettings.FromUrl(mongoUrl);
mongoClient = new MongoClient(mongoClientSettings);

// Change the database name
mongoDatabase = mongoClient.GetDatabase("testdb");

// Change the collection name, throughput value then update via MongoDB extension commands
// https://docs.microsoft.com/en-us/azure/cosmos-db/mongodb-custom-commands#update-collection

var result = mongoDatabase.RunCommand<BsonDocument>(@"{customAction: ""UpdateCollection"", collection: ""testcollection"", offerThroughput: 400}");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager modelos podem ser usados para provisionar a taxa de transferência de dimensionamento automático em recursos de banco de dados ou de contêiner para todas as APIs de Azure Cosmos DB Consulte [Azure Resource Manager modelos para Azure Cosmos DB](templates-samples-mongodb.md) para obter exemplos.

## <a name="azure-cli"></a>CLI do Azure

CLI do Azure pode ser usado para provisionar a taxa de transferência de autoescala em um banco de dados ou recursos de nível de contêiner para todas as APIs de Azure Cosmos DB Para obter exemplos, consulte [exemplos de CLI do Azure para Azure Cosmos DB](cli-samples-mongodb.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell pode ser usado para provisionar a taxa de transferência de autoescala em um banco de dados ou recursos de nível de contêiner para todas as APIs de Azure Cosmos DB Para obter exemplos, consulte [exemplos de Azure PowerShell para Azure Cosmos DB](powershell-samples-mongodb.md).

## <a name="next-steps"></a>Próximas etapas

Confira os seguintes artigos para saber mais sobre o provisionamento de taxa de transferência no Azure Cosmos DB:

* [Unidades de solicitação e taxa de transferência no Azure Cosmos DB](request-units.md)