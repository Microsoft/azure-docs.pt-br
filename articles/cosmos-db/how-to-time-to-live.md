---
title: Configurar e gerenciar a vida útil no Azure Cosmos DB
description: Saiba como configurar e gerenciar a vida útil em um contêiner e um item no Azure Cosmos DB
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/11/2020
ms.author: anfeldma
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 2ddba95f9ccc25d536638dbc68c41027d26e71c7
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341001"
---
# <a name="configure-time-to-live-in-azure-cosmos-db"></a>Configurar a vida útil no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

No Azure Cosmos DB, você pode optar por configurar a TTL (vida útil) no nível do contêiner ou substituí-la no nível de um item após a definição do contêiner. Você pode configurar a TTL para um contêiner usando o portal do Azure ou os SDKs específicos da linguagem. As substituições de TTL no nível do item podem ser configuradas usando os SDKs.

> Este conteúdo está relacionado a Azure Cosmos DB TTL de repositório transacional. Se você estiver procurando por TTL da loja Analitycal, que habilita cenários NoETL HTAP por meio [do link Synapse do Azure](./synapse-link.md), clique [aqui](./analytical-store-introduction.md#analytical-ttl).

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Habilitar vida útil em um contêiner usando o portal do Azure

Use as etapas a seguir para habilitar a vida útil de um contêiner sem prazo de expiração. Habilite essa opção para permitir que a TTL seja substituída no nível do item. Você também pode definir a TTL inserindo um valor diferente de zero para os segundos.

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Crie uma nova conta do Cosmos ou selecione uma conta existente.

3. Abra o painel **Data Explorer**.

4. Selecione um contêiner existente, expanda-o e modifique os valores abaixo:

   * Abra a janela **Escala e Configurações**.
   * Em **Configuração** , localize **Vida Útil**.
   * Selecione **Ativado (não há padrão)** ou selecione **Ativado** e defina um valor de TTL
   * Clique em **Salvar** para salvar as alterações.

   :::image type="content" source="./media/how-to-time-to-live/how-to-time-to-live-portal.png" alt-text="Configurar a vida útil no portal do Azure":::

* Quando DefaultTimeToLive é nulo, sua vida útil está desativada
* Quando DefaultTimeToLive é -1, sua configuração de vida útil está ativada (não padrão)
* Quando DefaultTimeToLive tem qualquer outro valor inteiro (exceto 0), a configuração de vida útil está ativada

## <a name="enable-time-to-live-on-a-container-using-azure-cli-or-powershell"></a>Habilitar a vida útil em um contêiner usando CLI do Azure ou o PowerShell

Para criar ou habilitar TTL em um contêiner, consulte

* [Criar um contêiner com TTL usando CLI do Azure](manage-with-cli.md#create-a-container-with-ttl)
* [Criar um contêiner com TTL usando o PowerShell](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>Habilitar vida útil em um contêiner usando o SDK

### <a name="net-sdk"></a><a id="dotnet-enable-noexpiry"></a> SDK DO .NET

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

SDK do .NET v2 (Microsoft.Azure.DocumentDB)

```csharp
// Create a new container with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

SDK do .NET v3 (Microsoft. Azure. Cosmos)

```csharp
// Create a new container with TTL enabled and without any expiration value
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = -1 //(never expire by default)
});
```
---

### <a name="java-sdk"></a><a id="java-enable-noexpiry"></a> SDK do Java

# <a name="java-sdk-v4"></a>[SDK do Java v4](#tab/javav4)

SDK do Java V4 (Maven com.azure::azure-cosmos)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(-1);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

# <a name="java-sdk-v3"></a>[SDK do Java v3](#tab/javav3)

Java SDK v3 (Maven com. Microsoft. Azure:: Azure-Cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(-1);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

## <a name="set-time-to-live-on-a-container-using-sdk"></a>Definir a vida útil em um contêiner usando o SDK

Para definir a vida útil de um contêiner, você precisará fornecer um número positivo diferente de zero que indica o período de tempo em segundos. Com base no valor de TTL configurado, todos os itens no contêiner após a última modificação do carimbo de hora do item `_ts` serão excluídos. Opcionalmente, você pode definir `TimeToLivePropertyPath` , que usará uma propriedade diferente em vez da propriedade gerada pelo sistema `_ts` para determinar quais itens serão excluídos com base na TTL.

### <a name="net-sdk"></a><a id="dotnet-enable-withexpiry"></a> SDK DO .NET

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

SDK do .NET v2 (Microsoft.Azure.DocumentDB)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24 // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition;
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

SDK do .NET v3 (Microsoft. Azure. Cosmos)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = 90 * 60 * 60 * 24 // expire all documents after 90 days
});
```
---

### <a name="java-sdk"></a><a id="java-enable-defaultexpiry"></a> SDK do Java

# <a name="java-sdk-v4"></a>[SDK do Java v4](#tab/javav4)

SDK do Java V4 (Maven com.azure::azure-cosmos)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

# <a name="java-sdk-v3"></a>[SDK do Java v3](#tab/javav3)

Java SDK v3 (Maven com. Microsoft. Azure:: Azure-Cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

### <a name="nodejs-sdk"></a><a id="nodejs-enable-withexpiry"></a>SDK do NodeJS

```javascript
const containerDefinition = {
          id: "sample container1",
        };

async function createcontainerWithTTL(db: Database, containerDefinition: ContainerDefinition, collId: any, defaultTtl: number) {
      containerDefinition.id = collId;
      containerDefinition.defaultTtl = defaultTtl;
      await db.containers.create(containerDefinition);
}
```

## <a name="set-time-to-live-on-an-item"></a>Definir a vida útil de um item

Além de definir uma vida útil padrão de um contêiner, você pode definir a vida útil de um item. A definição da vida útil no nível do item substituirá a TTL padrão do item no contêiner.

* Para definir a TTL em um item, é necessário fornecer um número positivo diferente de zero que indique o período, em segundos, em que o item expira após a modificação de seu último carimbo de data/hora (`_ts`).

* Se o item não tiver um campo TTL, por padrão, a TTL definida para o contêiner será aplicada ao item.

* Se a TTL estiver desabilitada no nível do contêiner, o campo TTL no item será ignorado até que a TTL seja habilitada novamente no contêiner.

### <a name="azure-portal"></a><a id="portal-set-ttl-item"></a>Portal do Azure

Use as etapas a seguir para habilitar a vida útil de um item:

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Crie uma nova conta do Cosmos ou selecione uma conta existente.

3. Abra o painel **Data Explorer**.

4. Selecione um contêiner existente, expanda-o e modifique os valores abaixo:

   * Abra a janela **Escala e Configurações**.
   * Em **Configuração** , localize **Vida Útil**.
   * Selecione **ativado (sem padrão)** ou selecione **ativado** e defina um valor TTL. 
   * Clique em **Salvar** para salvar as alterações.

5. Em seguida, navegue até o item para o qual você deseja definir a vida útil, adicione a propriedade `ttl` e selecione **Atualizar**. 

   ```json
   {
    "id": "1",
    "_rid": "Jic9ANWdO-EFAAAAAAAAAA==",
    "_self": "dbs/Jic9AA==/colls/Jic9ANWdO-E=/docs/Jic9ANWdO-EFAAAAAAAAAA==/",
    "_etag": "\"0d00b23f-0000-0000-0000-5c7712e80000\"",
    "_attachments": "attachments/",
    "ttl": 10,
    "_ts": 1551307496
   }
   ```

### <a name="net-sdk-any"></a><a id="dotnet-set-ttl-item"></a>SDK do .NET (qualquer)

```csharp
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }
    [JsonProperty(PropertyName="cid")]
    public string CustomerId { get; set; }
    // used to set expiration policy
    [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
    public int? ttl { get; set; }

    //...
}
// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder
{
    Id = "SO05",
    CustomerId = "CO18009186470",
    ttl = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days
};
```

### <a name="nodejs-sdk"></a><a id="nodejs-set-ttl-item"></a>SDK do NodeJS

```javascript
const itemDefinition = {
          id: "doc",
          name: "sample Item",
          key: "value",
          ttl: 2
        };
```

### <a name="java-sdk"></a><a id="java-set-ttl-item"></a> SDK do Java

# <a name="java-sdk-v4"></a>[SDK do Java v4](#tab/javav4)

SDK do Java V4 (Maven com.azure::azure-cosmos)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String getId() {return this.id;}
    public void setId(String new_id) {this.id = new_id;}
    public String getCustomerId() {return this.customerId;}
    public void setCustomerId(String new_cid) {this.customerId = new_cid;}
    public Integer getTtl() {return this.ttl;}
    public void setTtl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);

```

# <a name="java-sdk-v3"></a>[SDK do Java v3](#tab/javav3)

Java SDK v3 (Maven com. Microsoft. Azure:: Azure-Cosmos)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public void id(String new_id) {this.id = new_id;}
    public String customerId() {return this.customerId;}
    public void customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public void ttl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);

```
---

## <a name="reset-time-to-live"></a>Redefinir tempo de vida útil

Você pode redefinir a vida útil de um item executando uma operação de gravação ou atualização no item. A operação de gravação ou atualização definirá o `_ts` como a hora atual, e a TTL até que o item expire será iniciada novamente. Se você quiser alterar a TTL de um item, poderá atualizar o campo exatamente como atualizaria qualquer outro campo.

### <a name="net-sdk"></a><a id="dotnet-extend-ttl-item"></a> SDK DO .NET

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

SDK do .NET v2 (Microsoft.Azure.DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = 60 * 30 * 30; // update time to live
response = await client.ReplaceDocumentAsync(readDocument);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

SDK do .NET v3 (Microsoft. Azure. Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = 60 * 30 * 30; // update time to live
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```
---

### <a name="java-sdk"></a><a id="java-enable-modifyitemexpiry"></a> SDK do Java

# <a name="java-sdk-v4"></a>[SDK do Java v4](#tab/javav4)

SDK do Java V4 (Maven com.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
CosmosAsyncItemResponse<SalesOrder> itemResponse = container.readItem("SO05", new PartitionKey("CO18009186470"), SalesOrder.class)
        .flatMap(readResponse -> {
            SalesOrder salesOrder = readResponse.getItem();
            salesOrder.setTtl(60 * 30 * 30);
            return container.createItem(salesOrder);
}).block();
```

# <a name="java-sdk-v3"></a>[SDK do Java v3](#tab/javav3)

SDK v3 (Maven com. Microsoft. Azure:: Azure-Cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
container.getItem("SO05", new PartitionKey("CO18009186470")).read()
        .flatMap(readResponse -> {
            SalesOrder salesOrder = null;
            try {
                salesOrder = readResponse.properties().getObject(SalesOrder.class);
            } catch (Exception err) {

            }
            salesOrder.ttl(60 * 30 * 30);
            return container.createItem(salesOrder);
}).block();
```
---

## <a name="turn-off-time-to-live"></a>Desativar a vida útil

Se a vida útil foi definida em um item e você não deseja mais que esse item expire, acesse o item, remova o campo TTL e substitua o item no servidor. Quando o campo TTL for removido do item, o valor TTL padrão atribuído ao contêiner será aplicado ao item. Defina o valor de TTL como -1 para impedir que um item expire e não herde o valor de TTL do contêiner.

### <a name="net-sdk"></a><a id="dotnet-turn-off-ttl-item"></a> SDK DO .NET

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

SDK do .NET v2 (Microsoft.Azure.DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = null; // inherit the default TTL of the container

response = await client.ReplaceDocumentAsync(readDocument);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

SDK do .NET v3 (Microsoft. Azure. Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = null; // inherit the default TTL of the container
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```
---

### <a name="java-sdk"></a><a id="java-enable-itemdefaultexpiry"></a> SDK do Java

# <a name="java-sdk-v4"></a>[SDK do Java v4](#tab/javav4)

SDK do Java V4 (Maven com.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
CosmosAsyncItemResponse<SalesOrder> itemResponse = container.readItem("SO05", new PartitionKey("CO18009186470"), SalesOrder.class)
        .flatMap(readResponse -> {
            SalesOrder salesOrder = readResponse.getItem();
            salesOrder.setTtl(null);
            return container.createItem(salesOrder);
}).block();
```

# <a name="java-sdk-v3"></a>[SDK do Java v3](#tab/javav3)

Java SDK v3 (Maven com. Microsoft. Azure:: Azure-Cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
container.getItem("SO05", new PartitionKey("CO18009186470")).read()
        .flatMap(readResponse -> {
            SalesOrder salesOrder = null;
            try {
                salesOrder = readResponse.properties().getObject(SalesOrder.class);
            } catch (Exception err) {

            }
            salesOrder.ttl(null);
            return container.createItem(salesOrder);
}).block();
```
---

## <a name="disable-time-to-live"></a>Desabilitar a vida útil

Para desabilitar a vida útil de um contêiner e interromper o processo em segundo plano de verificação de itens expirados, a `DefaultTimeToLive` no contêiner deve ser excluída. A exclusão dessa propriedade é diferente de defini-la como -1. Quando você a define como -1, novos itens adicionados ao contêiner terão vida útil eterna; no entanto, você pode substituir esse valor em itens específicos no contêiner. Quando você remover a propriedade de vida útil do contêiner, os itens nunca expirarão, mesmo se tiverem substituído explicitamente o valor de vida útil padrão anterior.

### <a name="net-sdk"></a><a id="dotnet-disable-ttl"></a> SDK DO .NET

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

SDK do .NET v2 (Microsoft.Azure.DocumentDB)

```csharp
// Get the container, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

SDK do .NET v3 (Microsoft. Azure. Cosmos)

```csharp
// Get the container, update DefaultTimeToLive to null
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Disable TTL
containerResponse.Resource.DefaultTimeToLive = null;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```
---

### <a name="java-sdk"></a><a id="java-enable-disableexpiry"></a> SDK do Java

# <a name="java-sdk-v4"></a>[SDK do Java v4](#tab/javav4)

SDK do Java V4 (Maven com.azure::azure-cosmos)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
// Disable TTL
containerProperties.setDefaultTimeToLiveInSeconds(null);
// Update container settings
container.replace(containerProperties).block();
```

# <a name="java-sdk-v3"></a>[SDK do Java v3](#tab/javav3)

Java SDK v3 (Maven com. Microsoft. Azure:: Azure-Cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
// Disable TTL
containerProperties.defaultTimeToLive(null);
// Update container settings
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre vida útil no seguinte artigo:

* [Vida útil](time-to-live.md)