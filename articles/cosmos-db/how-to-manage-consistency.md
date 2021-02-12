---
title: Gerenciar a consistência no Azure Cosmos DB
description: Saiba como configurar e gerenciar níveis de consistência no Azure Cosmos DB usando portal do Azure, SDK do .NET, SDK do Java e vários outros SDKs
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/10/2020
ms.author: anfeldma
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: b0c03c2f5313605fbdf288a9262df0852e066efd
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333453"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Gerenciar os níveis de coerência no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo explica como gerenciar os níveis de consistência no Azure Cosmos DB. Você aprende como configurar o nível de consistência padrão, substituir a consistência padrão, gerenciar manualmente os tokens de sessão e compreender a métrica PBS (Desatualização Limitada Probabilística).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Configurar o nível de consistência padrão

O [nível de consistência padrão](consistency-levels.md) é o nível de coerência que os clientes usam por padrão.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Para exibir ou modificar o nível de consistência padrão, entre no portal do Azure. Localize a conta do Azure Cosmos e abra o painel **Consistência padrão**. Escolha o nível de consistência que você gostaria de ter como o novo padrão e escolha **Salvar**. O portal do Azure também fornece uma visualização dos diferentes níveis de consistência com notas musicais. 

:::image type="content" source="./media/how-to-manage-consistency/consistency-settings.png" alt-text="Menu de consistência no portal do Azure":::

# <a name="cli"></a>[CLI](#tab/cli)

Crie uma conta do cosmos com consistência de sessão e, em seguida, atualize a consistência padrão.

```azurecli
# Create a new account with Session consistency
az cosmosdb create --name $accountName --resource-group $resourceGroupName --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name $accountName --resource-group $resourceGroupName --default-consistency-level Strong
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Crie uma conta do cosmos com consistência de sessão e, em seguida, atualize a consistência padrão.

```azurepowershell-interactive
# Create a new account with Session consistency
New-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Location $locations -Name $accountName -DefaultConsistencyLevel "Session"

# Update an existing account's default consistency
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Name $accountName -DefaultConsistencyLevel "Strong"
```

---

## <a name="override-the-default-consistency-level"></a>Substituir o nível de consistência padrão

Os clientes podem substituir o nível de consistência padrão que é definido pelo serviço. O nível de consistência pode ser definido por solicitação, o que substitui o nível de consistência padrão definido no nível da conta.

> [!TIP]
> A consistência só pode ser **reduzida** no nível da solicitação. Para mudar de uma consistência mais fraca para a mais forte, atualize a consistência padrão para a conta Cosmos.

### <a name="net-sdk"></a><a id="override-default-consistency-dotnet"></a>SDK .NET

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
// Override consistency at the request level via request options
ItemRequestOptions requestOptions = new ItemRequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.GetContainer(databaseName, containerName)
    .CreateItemAsync(
        item,
        new PartitionKey(itemPartitionKey),
        requestOptions);
```
---

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> SDK do Java v4

# <a name="async"></a>[Async](#tab/api-async)

   API assíncrona do SDK do Java V4 (Maven com.azure::azure-cosmos)

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencyAsync)]

# <a name="sync"></a>[Sincronizar](#tab/api-sync)

   API síncrona do SDK do Java V4 (Maven com.azure::azure-cosmos)

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySync)]

--- 

### <a name="java-v2-sdks"></a><a id="override-default-consistency-javav2"></a> SDKs do Java v2

# <a name="async"></a>[Async](#tab/api-async)

SDK do Java v2 assíncrono (Maven com. Microsoft. Azure:: Azure-cosmosdb)

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

# <a name="sync"></a>[Sincronizar](#tab/api-sync)

Sincronizar o SDK do Java v2 (Maven com. Microsoft. Azure:: Azure-documentdb)

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Eventual);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="override-default-consistency-javascript"></a>SDK de Node.js/JavaScript/TypeScript

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Eventual
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a name="python-sdk"></a><a id="override-default-consistency-python"></a>SDK do Python

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>Utilizar tokens de sessão

Um dos níveis de consistência no Azure Cosmos DB é a consistência de *Sessão*. Esse é o nível padrão aplicado a contas do Cosmos por padrão. Ao trabalhar com a consistência de *Sessão* , o cliente usará um token de sessão internamente com cada solicitação de leitura/consulta para garantir que o nível de consistência definido seja mantido.

Para gerenciar os tokens de sessão manualmente, obtenha o token de sessão na resposta e configure-os por solicitação. Se não for necessário gerenciar manualmente os tokens de sessão, você não precisa usar esses exemplos. O SDK controla os tokens de sessão automaticamente. Se você não definir o token de sessão manualmente, por padrão, o SDK usará o token de sessão mais recente.

### <a name="net-sdk"></a><a id="utilize-session-tokens-dotnet"></a>SDK .NET

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = client.GetContainer(databaseName, collectionName);
ItemResponse<SalesOrder> response = await container.CreateItemAsync<SalesOrder>(salesOrder);
string sessionToken = response.Headers.Session;

ItemRequestOptions options = new ItemRequestOptions();
options.SessionToken = sessionToken;
ItemResponse<SalesOrder> response = await container.ReadItemAsync<SalesOrder>(salesOrder.Id, new PartitionKey(salesOrder.PartitionKey), options);
```
---

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> SDK do Java v4

# <a name="async"></a>[Async](#tab/api-async)

   API assíncrona do SDK do Java V4 (Maven com.azure::azure-cosmos)

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencySessionAsync)]

# <a name="sync"></a>[Sincronizar](#tab/api-sync)

   API síncrona do SDK do Java V4 (Maven com.azure::azure-cosmos)

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySessionSync)]

--- 

### <a name="java-v2-sdks"></a><a id="utilize-session-tokens-javav2"></a>SDKs do Java v2

# <a name="async"></a>[Async](#tab/api-async)

SDK do Java v2 assíncrono (Maven com. Microsoft. Azure:: Azure-cosmosdb)

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

# <a name="sync"></a>[Sincronizar](#tab/api-sync)

Sincronizar o SDK do Java v2 (Maven com. Microsoft. Azure:: Azure-documentdb)

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="utilize-session-tokens-javascript"></a>SDK de Node.js/JavaScript/TypeScript

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a name="python-sdk"></a><a id="utilize-session-tokens-python"></a>SDK do Python

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>Monitorar métrica PBS (Desatualização Limitada Probabilística)

Quão eventual é a consistência eventual? Para o caso médio, podemos oferecer limites de desatualização com relação ao histórico de versão e à hora. A métrica [**PBS (desatualização limitada probabilística)**](https://pbs.cs.berkeley.edu/) tenta quantificar a probabilidade de desatualização e mostra-a como uma métrica. Para exibir a métrica PBS, vá para a conta do Azure Cosmos no portal do Azure. Abra o painel **métricas** e selecione a guia **consistência** . Examine o grafo chamado **probabilidade de leituras altamente consistentes com base em sua carga de trabalho (consulte PBS)**.

:::image type="content" source="./media/how-to-manage-consistency/pbs-metric.png" alt-text="Gráfico PBS no portal do Azure":::

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como gerenciar conflitos de dados ou passar para o próximo conceito fundamental no Azure Cosmos DB. Confira os seguintes artigos:

* [Níveis de consistência no Azure Cosmos DB](consistency-levels.md)
* [Particionamento e distribuição de dados](./partitioning-overview.md)
* [Gerenciar conflitos entre regiões](how-to-manage-conflicts.md)
* [Particionamento e distribuição de dados](partitioning-overview.md)
* [Consistency tradeoffs in modern distributed database systems design](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k) (Compensações de coerência no projeto de sistemas de bancos de dados modernos distribuídos)
* [Alta disponibilidade](high-availability.md)
* [SLA do Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)