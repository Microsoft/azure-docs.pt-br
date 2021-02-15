---
title: Provisionar taxa de transferência de autoescala na API do SQL Azure Cosmos DB
description: Saiba como provisionar a taxa de transferência de dimensionamento automático no nível do contêiner e do banco de dados em Azure Cosmos DB API do SQL usando portal do Azure, CLI, PowerShell e vários outros SDKs.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/15/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 52904296df77d9097a6180345388e8e702e2bca0
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357612"
---
# <a name="provision-autoscale-throughput-on-database-or-container-in-azure-cosmos-db---sql-api"></a>Provisionar taxa de transferência de autoescala no banco de dados ou contêiner na API Azure Cosmos DB-SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo explica como provisionar a taxa de transferência de dimensionamento automático em um banco de dados ou contêiner (coleção, gráfico ou tabela) em Azure Cosmos DB API do SQL. Você pode habilitar o dimensionamento automático em um único contêiner ou provisionar taxa de transferência de dimensionamento automático em um banco de dados e compartilhá-la entre todos os contêineres no banco de dados.

Se você estiver usando uma API diferente, confira [API para MongoDB](how-to-provision-throughput-mongodb.md), [API do Cassandra](how-to-provision-throughput-cassandra.md), artigos da [API Gremlin](how-to-provision-throughput-gremlin.md) para provisionar a taxa de transferência.

## <a name="azure-portal"></a>Portal do Azure

### <a name="create-new-database-or-container-with-autoscale"></a>Criar novo banco de dados ou contêiner com dimensionamento automático

1. Entre no [Portal do Azure](https://portal.azure.com) ou no [Azure Cosmos DB Explorer](https://cosmos.azure.com/).

1. Vá até sua conta do Azure Cosmos DB e abra a guia **Data Explorer**.

1. Selecione **Novo contêiner**. Insira um nome para seu banco de dados, contêiner e uma chave de partição. Em **Taxa de transferência**, selecione a opção **dimensionamento automático** e defina a [taxa de transferência máxima (RU/s)](provision-throughput-autoscale.md#how-autoscale-provisioned-throughput-works) para a qual você deseja que o banco de dados ou contêiner seja dimensionado.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/create-new-autoscale-container.png" alt-text="Criando um contêiner e configurando a taxa de transferência provisionada de dimensionamento automático":::

1. Selecione **OK**.

Para provisionar o dimensionamento automático no banco de dados de taxa de transferência compartilhada, selecione a opção **Provisionar taxa de transferência do banco de dados** ao criar um novo banco de dados. 

### <a name="enable-autoscale-on-existing-database-or-container"></a>Habilitar dimensionamento automático no banco de dados ou contêiner existente

> [!IMPORTANT]
> Na versão atual, o portal do Azure é a única maneira de migrar entre o dimensionamento automático e a taxa de transferência provisionada (manual) padrão. 

1. Entre no [Portal do Azure](https://portal.azure.com) ou no [Azure Cosmos DB Explorer](https://cosmos.azure.com/).

1. Vá até sua conta do Azure Cosmos DB e abra a guia **Data Explorer**.

1. Selecione **Escala e configurações** para seu contêiner ou **Escala** para seu banco de dados.

1. Em **Escala**, selecione a opção **Dimensionamento automático** e **Salvar**.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/autoscale-scale-and-settings.png" alt-text="Habilitando o dimensionamento automático em um contêiner existente":::

> [!NOTE]
> Quando você habilita o dimensionamento automático em um banco de dados ou contêiner existente, o valor inicial para o máximo de RU/s é determinado pelo sistema com base nas suas configurações e armazenamento de taxa de transferência provisionada manual atual. Após a operação ser concluída, você poderá alterar o máximo de RU/s, se necessário. [Saiba mais.](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work) 

## <a name="azure-cosmos-db-net-v3-sdk"></a>Azure Cosmos DB SDK do .NET v3

Use a [versão 3.9 ou superior](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) do SDK do .NET do Azure Cosmos DB para a API do SQL para gerenciar recursos de dimensionamento automático. 

> [!IMPORTANT]
> É possível usar o SDK do .NET para criar novos recursos de dimensionamento automático. O SDK não suporta a migração entre o dimensionamento automático e a taxa de transferência padrão (manual). No momento, o cenário de migração é suportado apenas no portal do Azure. 

### <a name="create-database-with-shared-throughput"></a>Criar banco de dados com taxa de transferência compartilhada

```csharp
// Create instance of CosmosClient
CosmosClient cosmosClient = new CosmosClient(Endpoint, PrimaryKey);
 
// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
database = await cosmosClient.CreateDatabaseAsync(DatabaseName, throughputProperties: autoscaleThroughputProperties);
```

### <a name="create-container-with-dedicated-throughput"></a>Criar contêiner com taxa de transferência dedicada

```csharp
// Get reference to database that container will be created in
Database database = await cosmosClient.GetDatabase("DatabaseName");

// Container and autoscale throughput settings
ContainerProperties autoscaleContainerProperties = new ContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
container = await database.CreateContainerAsync(autoscaleContainerProperties, autoscaleThroughputProperties);
```

### <a name="read-the-current-throughput-rus"></a>Ler a taxa de transferência atual (RU/s)

```csharp
// Get a reference to the resource
Container container = cosmosClient.GetDatabase("DatabaseName").GetContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = await container.ReadThroughputAsync(requestOptions: null); 

// The autoscale max throughput (RU/s) of the resource
int? autoscaleMaxThroughput = autoscaleContainerThroughput.AutoscaleMaxThroughput;

// The throughput (RU/s) the resource is currently scaled to
int? currentThroughput = autoscaleContainerThroughput.Throughput;
```

### <a name="change-the-autoscale-max-throughput-rus"></a>Alterar a taxa de transferência máxima do dimensionamento automático (RU/s)

```csharp
// Change the autoscale max throughput (RU/s)
await container.ReplaceThroughputAsync(ThroughputProperties.CreateAutoscaleThroughput(newAutoscaleMaxThroughput));
```

## <a name="azure-cosmos-db-java-v4-sdk"></a>SDK do Azure Cosmos DB Java v4

Você pode usar a [versão 4.0 ou superior](https://mvnrepository.com/artifact/com.azure/azure-cosmos) do SDK do Java do Azure Cosmos DB para a API do SQL para gerenciar recursos de dimensionamento automático.

> [!IMPORTANT]
> Você pode usar o SDK do Java para criar novos recursos de dimensionamento automático. O SDK não suporta a migração entre o dimensionamento automático e a taxa de transferência padrão (manual). No momento, o cenário de migração é suportado apenas no portal do Azure.

### <a name="create-database-with-shared-throughput"></a>Criar banco de dados com taxa de transferência compartilhada

#### <a name="async"></a>[Async](#tab/api-async)

```java
// Create instance of CosmosClient
CosmosAsyncClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(PRIMARYKEY)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildAsyncClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosAsyncDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).block().getDatabase();
```

#### <a name="sync"></a>[Sincronizar](#tab/api-sync)

```java
// Create instance of CosmosClient
CosmosClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(PRIMARYKEY)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).getDatabase();
```

--- 

### <a name="create-container-with-dedicated-throughput"></a>Criar contêiner com taxa de transferência dedicada

#### <a name="async"></a>[Async](#tab/api-async)

```java
// Get reference to database that container will be created in
CosmosAsyncDatabase database = client.createDatabase("DatabaseName").block().getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosAsyncContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .block()
                                .getContainer();
```

#### <a name="sync"></a>[Sincronizar](#tab/api-sync)

```java
// Get reference to database that container will be created in
CosmosDatabase database = client.createDatabase("DatabaseName").getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .getContainer();
```

--- 

### <a name="read-the-current-throughput-rus"></a>Ler a taxa de transferência atual (RU/s)

#### <a name="async"></a>[Async](#tab/api-async)

```java
// Get a reference to the resource
CosmosAsyncContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().block().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

#### <a name="sync"></a>[Sincronizar](#tab/api-sync)

```java
// Get a reference to the resource
CosmosContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

--- 

### <a name="change-the-autoscale-max-throughput-rus"></a>Alterar a taxa de transferência máxima do dimensionamento automático (RU/s)

#### <a name="async"></a>[Async](#tab/api-async)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput)).block();
```

#### <a name="sync"></a>[Sincronizar](#tab/api-sync)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput));
```

---

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager modelos podem ser usados para provisionar a taxa de transferência de dimensionamento automático em recursos de banco de dados ou de contêiner para todas as APIs de Azure Cosmos DB Consulte [Azure Resource Manager modelos para Azure Cosmos DB](./templates-samples-sql.md) para obter exemplos.

## <a name="azure-cli"></a>CLI do Azure

CLI do Azure pode ser usado para provisionar a taxa de transferência de autoescala em um banco de dados ou recursos de nível de contêiner para todas as APIs de Azure Cosmos DB Para obter exemplos, consulte [exemplos de CLI do Azure para Azure Cosmos DB](cli-samples.md).

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell pode ser usado para provisionar a taxa de transferência de autoescala em um banco de dados ou recursos de nível de contêiner para todas as APIs de Azure Cosmos DB Para obter exemplos, consulte [exemplos de Azure PowerShell para Azure Cosmos DB](powershell-samples.md).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre as [benefícios da taxa de transferência provisionada com o dimensionamento automático](provision-throughput-autoscale.md#benefits-of-autoscale).
* Saiba como [escolher entre taxa de transferência manual e de dimensionamento automático](how-to-choose-offer.md).
* Examine as [Perguntas frequentes sobre dimensionamento automático](autoscale-faq.md).
