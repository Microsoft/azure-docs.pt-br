---
title: Como configurar gravações de várias regiões no Azure Cosmos DB
description: Saiba como configurar gravações de várias regiões para seus aplicativos usando SDKs diferentes no Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/06/2021
ms.author: mjbrown
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, "seo-nov-2020"
ms.openlocfilehash: 08d50b18605fd833e6b0efca987338d0ca1eef8d
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102488504"
---
# <a name="configure-multi-region-writes-in-your-applications-that-use-azure-cosmos-db"></a>Configurar gravações de várias regiões em seus aplicativos que usam Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Depois que uma conta tiver sido criada com várias regiões de gravação habilitadas, você deverá fazer duas alterações em seu aplicativo para o ConnectionPolicy para o cliente Cosmos para habilitar as gravações de várias regiões em Azure Cosmos DB. Dentro do ConnectionPolicy, defina UseMultipleWriteLocations como true e passe o nome da região onde o aplicativo é implantado no ApplicationRegion. Isso preencherá a propriedade PreferredLocations com base na proximidade geográfica do local passado. Se uma nova região posteriormente é adicionada à conta, o aplicativo não precisa ser atualizado ou reimplantado, ele detectará automaticamente a região mais próxima e será automaticamente iniciado em caso de evento regional.

> [!Note]
> As contas do cosmos inicialmente configuradas com uma única região de gravação podem ser configuradas para várias regiões de gravação com tempo de inatividade zero. Para saber mais, consulte [Configurar a gravação de várias regiões](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a name="azure-portal"></a><a id="portal"></a> portal do Azure

Para habilitar gravações de várias regiões de portal do Azure, use as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Navegue até sua conta do Azure Cosmos e, no menu, abra o painel **replicar dados globalmente** .

1. Na opção **gravações de várias regiões** , escolha **habilitar**. Ele adiciona automaticamente as regiões existentes às regiões de leitura e gravação.

1. Você pode adicionar outras regiões selecionando os ícones no mapa ou selecionando o botão **Adicionar região** . Todas as regiões que você adicionar terão a leitura e a gravação habilitadas.

1. Depois de atualizar a lista região, selecione **salvar** para aplicar as alterações.

   :::image type="content" source="./media/how-to-multi-master/enable-multi-region-writes.png" alt-text="Captura de tela para habilitar gravações em várias regiões usando portal do Azure" lightbox="./media/how-to-multi-master/enable-multi-region-writes.png":::

## <a name="net-sdk-v2"></a><a id="netv2"></a>SDK v2 do .NET

Para habilitar as gravações de várias regiões em seu aplicativo, defina `UseMultipleWriteLocations` como `true` . Além disso, defina `SetCurrentLocation` como a região na qual o aplicativo está sendo implantado e em que o Azure Cosmos DB está replicado:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>SDK v3 do .NET

Para habilitar as gravações de várias regiões em seu aplicativo, defina `ApplicationRegion` para a região em que o aplicativo está sendo implantado e onde Cosmos DB é replicado:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

Opcionalmente, você pode usar o `CosmosClientBuilder` e o `WithApplicationRegion` para obter o mesmo resultado:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-v4-sdk"></a><a id="java4-multi-region-writes"></a> SDK do Java v4

Para habilitar as gravações de várias regiões em seu aplicativo, chame `.multipleWriteRegionsEnabled(true)` e `.preferredRegions(preferredRegions)` no construtor de cliente, em que `preferredRegions` é um `List` elemento contendo um, que é a região em que o aplicativo está sendo implantado e onde Cosmos DB é replicado:

# <a name="async"></a>[Async](#tab/api-async)

   API assíncrona do [SDK do Java V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos))

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ConfigureMultimasterAsync)]

# <a name="sync"></a>[Sincronizar](#tab/api-sync)

   API síncrona do [SDK do Java V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos))

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ConfigureMultimasterSync)]

--- 

## <a name="async-java-v2-sdk"></a><a id="java2-multi-region-writes"></a> SDK do Java v2 assíncrono

O SDK do Java v2 usava o Maven [com. Microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb). Para habilitar as gravações de várias regiões em seu aplicativo, defina `policy.setUsingMultipleWriteLocations(true)` e defina `policy.setPreferredLocations` como a região em que o aplicativo está sendo implantado e onde Cosmos DB é replicado:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>SDK do Node.js, do JavaScript e do TypeScript

Para habilitar as gravações de várias regiões em seu aplicativo, defina `connectionPolicy.UseMultipleWriteLocations` como `true` . Além disso, defina `connectionPolicy.PreferredLocations` como a região na qual o aplicativo está sendo implantado e em que o Cosmos DB está replicado:

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a name="python-sdk"></a><a id="python"></a>SDK do Python

Para habilitar as gravações de várias regiões em seu aplicativo, defina `connection_policy.UseMultipleWriteLocations` como `true` . Além disso, defina `connection_policy.PreferredLocations` como a região na qual o aplicativo está sendo implantado e em que o Cosmos DB está replicado.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Próximas etapas

Leia os seguintes artigos:

* [Usar tokens de sessão para gerenciar a consistência no Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Tipos de conflitos e políticas de resolução no Azure Cosmos DB](conflict-resolution-policies.md)
* [Alta disponibilidade no Azure Cosmos DB](high-availability.md)
* [Níveis de consistência no Azure Cosmos DB](consistency-levels.md)
* [Escolher o nível de consistência correto no Azure Cosmos DB](./consistency-levels.md)
* [Compensações de consistência, disponibilidade e desempenho no Azure Cosmos DB](./consistency-levels.md)
* [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](./consistency-levels.md)
* [Taxa de transferência provisionada para dimensionamento global](./request-units.md)
* [Distribuição global: nos bastidores](global-dist-under-the-hood.md)