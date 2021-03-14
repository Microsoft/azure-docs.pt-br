---
title: Migre seu aplicativo para usar o SDK do Java do Azure Cosmos DB v4 (com.azure.cosmos)
description: Saiba como atualizar seu aplicativo Java existente usando os SDKs do Java do Azure Cosmos DB mais antigos para o Java SDK 4.0 mais recente (pacote com.azure.cosmos) para API Core (SQL).
author: anfeldma-ms
ms.custom: devx-track-java
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/11/2020
ms.reviewer: sngun
ms.openlocfilehash: 92a9abec36bd75c594c67843286bf8fa067d7dba
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658524"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-java-sdk-v4"></a>Migre seu aplicativo para usar o SDK do Java do Azure Cosmos DB v4
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]  
> Para obter mais informações sobre este SDK, veja as [Notas de versão](sql-api-sdk-java-v4.md) do SDK do Java do Azure Cosmos DB v4, [repositório do Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), [dicas de desempenho](performance-tips-java-sdk-v4-sql.md) do SDK do Java do Azure Cosmos DB v4 e [guia de solução de problemas](troubleshoot-java-sdk-v4-sql.md) do SDK do Java do Azure Cosmos DB v4.
>

Este artigo explica como atualizar o aplicativo Java existente que está usando um SDK do Java do Azure Cosmos DB mais antigo para o SDK do Java do Azure Cosmos DB 4.0 mais recente para API Core (SQL). O SDK do Java do Azure Cosmos DB v4 corresponde ao pacote `com.azure.cosmos`. Você pode usar as instruções neste documento, se estiver migrando o aplicativo de qualquer um dos seguintes SDKs do Java do Azure Cosmos DB: 

* SDK do Java Síncrono 2.x.x
* SDK do Java Assíncrono 2.x.x
* SDK do Java 3.x.x

## <a name="azure-cosmos-db-java-sdks-and-package-mappings"></a>SDKs do Java e mapeamentos de pacote do Azure Cosmos DB

A tabela a seguir lista diferentes SDKs do Java do Azure Cosmos DB, o nome do pacote e as informações de versão:

| Java SDK| Data de lançamento | Aplicativos agrupados   | Jar do Maven  | Nome do pacote do Java  |Referência de API   | Notas de versão  |
|-------|------|-----------|-----------|--------------|-------------|---------------------------|
| Async 2.x.x  | Junho de 2018    | Async(RxJava)  | `com.microsoft.azure::azure-cosmosdb` | `com.microsoft.azure.cosmosdb.rx` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Notas de Versão](sql-api-sdk-async-java.md) |
| Sync 2.x.x     | Setembro de 2018    | Sincronizar   | `com.microsoft.azure::azure-documentdb` | `com.microsoft.azure.cosmosdb` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [Notas de Versão](sql-api-sdk-java.md)  |
| 3.x.x    | Julho de 2019    | Async(Reactor)/Sync  | `com.microsoft.azure::azure-cosmos`  | `com.azure.data.cosmos` | [API](https://azure.github.io/azure-cosmosdb-java/3.0.0/) | - |
| 4,0   | Junho de 2020   | Async(Reactor)/Sync  | `com.azure::azure-cosmos` | `com.azure.cosmos`   | -  | [API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-cosmos/4.0.1/index.html)  |

## <a name="sdk-level-implementation-changes"></a>Alterações de implementação no nível do SDK

Veja a seguir as principais diferenças de implementação entre SDKs diferentes:

### <a name="rxjava-is-replaced-with-reactor-in-azure-cosmos-db-java-sdk-versions-3xx-and-40"></a>RxJava é substituído pelo reator no SDK do Java do Azure Cosmos DB versões 3.x.x e 4.0

Se você não estiver familiarizado com a programação assíncrona ou programação reativa, confira o [guia de padrão do Reator](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md) para obter uma introdução à programação assíncrona e ao Reator do Projeto. Este guia pode ser útil se você usou a API Síncrona do SDK do Java Síncrono do Azure Cosmos DB 2.x.x ou do SDK do Java do Azure Cosmos DB 3.x.x no passado.

Se você usou o SDK do Java Assíncrono do Azure Cosmos DB 2.x.x e planeja migrar para o SDK 4.0, confira o [Guia de Reator versus RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) para obter orientação sobre a conversão do código RxJava para usar o Reator.

### <a name="azure-cosmos-db-java-sdk-v4-has-direct-connectivity-mode-in-both-async-and-sync-apis"></a>O SDK do Java do Azure Cosmos DB v4 tem o modo de conectividade direta nas APIs Assíncrona e Síncrona

Se você usou o SDK do Java do Azure Cosmos DB 2.x.x, observe que o modo de conexão direta baseado em TCP (ao contrário do HTTP) foi implementado no SDK do Java do Azure Cosmos DB 4.0 para as APIs Assíncrona e Síncrona.

## <a name="api-level-changes"></a>Alterações no nível da API

Veja a seguir as alterações no nível da API no SDK do Java do Azure Cosmos DB 4.x.x, em comparação aos SDKs anteriores (SDK do Java 3.x.x, SDK do Java Assíncrono 2.x.x e SDK do Java Síncrono 2.x.x):

:::image type="content" source="./media/migrate-java-v4-sdk/java-sdk-naming-conventions.png" alt-text="Convenções de nomenclatura do SDK do Java do Azure Cosmos DB":::

* O SDK do Java do Azure Cosmos DB 3.x.x e 4.0 se referem aos recursos do cliente como `Cosmos<resourceName>`. Por exemplo, `CosmosClient`, `CosmosDatabase`, `CosmosContainer`. Enquanto que na versão 2.x.x, os SDKs do Java do Azure Cosmos DB não têm um esquema de nomenclatura uniforme.

* Os SDKs do Java do Azure Cosmos DB 3.x.x e 4.0 oferecem APIs Síncrona e Assíncrona.

  * **SDK do Java 4.0**: Todas as classes pertencem à API Síncrona, a menos que o nome da classe seja anexado com `Async` depois de `Cosmos`.

  * **SDK do Java 3.x.x**: Todas as classes pertencem à API Assíncrona, a menos que o nome da classe seja anexado com `Async` depois de `Cosmos`.

  * **SDK do Java Assíncrono 2.x.x**: Os nomes de classe são semelhantes aos do SDK do Java Síncrono 2.x.x, no entanto, o nome começa com *Assíncrono*.

### <a name="hierarchical-api-structure"></a>Estrutura hierárquica da API

Os SDKs do Java do Azure Cosmos DB 4.0 e 3.x.x introduzem uma estrutura hierárquica da API que organiza os clientes, bancos de dados e contêineres de maneira aninhada, conforme mostrado no seguinte trecho de código do SDK 4.0:

```java
CosmosContainer container = client.getDatabase("MyDatabaseName").getContainer("MyContainerName");

```

Na versão 2.x.x do SDK do Java do Azure Cosmos DB, todas as operações em recursos e documentos são realizadas por meio da instância do cliente.

### <a name="representing-documents"></a>Documentos representantes

No SDK do Java do Azure Cosmos DB 4.0, os POJOs personalizados e `JsonNodes` são as duas opções para ler e gravar os documentos no Azure Cosmos DB.

No SDK do Java do Azure Cosmos DB 3.x.x, o objeto `CosmosItemProperties` é exposto pela API pública e distribuído como representação do documento. Essa classe não é mais exposta publicamente na versão 4.0.

### <a name="imports"></a>Importações

* Os pacotes do SDK do Java do Azure Cosmos DB 4.0 começam com `com.azure.cosmos`
* Os pacotes do SDK do Java do Azure Cosmos DB 3.x.x começam com `com.azure.data.cosmos`
* Azure Cosmos DB pacotes de API de sincronização do SDK do Java 2. x. x começam com `com.microsoft.azure.documentdb`

* O SDK do Java do Azure Cosmos DB 4.0 coloca várias classes em um pacote aninhado `com.azure.cosmos.models`. Alguns desses pacotes incluem:

  * `CosmosContainerResponse`
  * `CosmosDatabaseResponse`
  * `CosmosItemResponse`
  * Os análogos da API assíncrona para todos os pacotes acima
  * `CosmosContainerProperties`
  * `FeedOptions`
  * `PartitionKey`
  * `IndexingPolicy`
  * `IndexingMode` ...etc.

### <a name="accessors"></a>Acessadores

O SDK do Java do Azure Cosmos DB 4.0 expõe os métodos `get` e `set` para acessar os membros da instância. Por exemplo, a instância de `CosmosContainer` tem os métodos `container.getId()` e `container.setId()`.

Isso é diferente do SDK do Java do Azure Cosmos DB 3.x.x, que expõe uma interface fluente. Por exemplo, uma instância `CosmosSyncContainer` tem `container.id()`, que está sobrecarregado para obter ou definir o valor de `id`.

## <a name="code-snippet-comparisons"></a>Comparações de trecho de código

### <a name="create-resources"></a>Criar recursos

O trecho de código a seguir mostra as diferenças em como os recursos são criados entre as APIs de sincronização do 4,0, 3. x. x e duas APIs. x. x:

# <a name="java-sdk-40-async-api"></a>[API Assíncrona do SDK do Java 4.0](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateJavaSDKv4ResourceAsync)]

# <a name="java-sdk-3xx-async-api"></a>[API Assíncrona do SDK do Java 3.x.x](#tab/java-v3-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.defaultPolicy();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.preferredLocations(Lists.newArrayList("Your Account Location"));

//  Create async client
//  <CreateAsyncClient>
client = new CosmosClientBuilder()
        .endpoint("your.hostname")
        .key("yourmasterkey")
        .connectionPolicy(defaultPolicy)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databaseResponse -> {
        database = databaseResponse.database();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContainerProperties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database.createContainerIfNotExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.container();
        return Mono.empty();
}).subscribe();
```

# <a name="java-sdk-2xx-sync-api"></a>[API de sincronização do SDK do Java 2. x. x](#tab/java-v2-sync)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.GetDefault();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.setPreferredLocations(Lists.newArrayList("Your Account Location"));

//  Create document client
//  <CreateDocumentClient>
client = new DocumentClient("your.hostname", "your.masterkey", defaultPolicy, ConsistencyLevel.Eventual)

// Create database with specified name
Database databaseDefinition = new Database();
databaseDefinition.setId("YourDatabaseName");
ResourceResponse<Database> databaseResourceResponse = client.createDatabase(databaseDefinition, new RequestOptions());

// Read database with specified name
String databaseLink = "dbs/YourDatabaseName";
databaseResourceResponse = client.readDatabase(databaseLink, new RequestOptions());
Database database = databaseResourceResponse.getResource();

// Create container with specified name
DocumentCollection documentCollection = new DocumentCollection();
documentCollection.setId("YourContainerName");
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="item-operations"></a>Operações de item

O trecho de código a seguir mostra as diferenças em como as operações de item são executadas entre as APIs de sincronização do 4,0, 3. x. x e 2. x. x:

# <a name="java-sdk-40-async-api"></a>[API Assíncrona do SDK do Java 4.0](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemOpsAsync)]

# <a name="java-sdk-3xx-async-api"></a>[API Assíncrona do SDK do Java 3.x.x](#tab/java-v3-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```

# <a name="java-sdk-2xx-sync-api"></a>[API de sincronização do SDK do Java 2. x. x](#tab/java-v2-sync)

```java
//  Container is created. Generate documents to insert.
Document document = new Document();
document.setId("YourDocumentId");
ResourceResponse<Document> documentResourceResponse = client.createDocument(documentCollection.getSelfLink(), document,
    new RequestOptions(), true);
Document responseDocument = documentResourceResponse.getResource();
```
---

### <a name="indexing"></a>Indexação

O trecho de código a seguir mostra as diferenças em como a indexação é criada entre as APIs de sincronização do 4,0, 3. x. x e 2. x. x:

# <a name="java-sdk-40-async-api"></a>[API Assíncrona do SDK do Java 4.0](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateIndexingAsync)]

# <a name="java-sdk-3xx-async-api"></a>[API Assíncrona do SDK do Java 3.x.x](#tab/java-v3-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.path("/*");
includedPaths.add(includedPath);
indexingPolicy.includedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.path("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.excludedPaths(excludedPaths);

containerProperties.indexingPolicy(indexingPolicy);

CosmosContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                               .block()
                                               .container();
```

# <a name="java-sdk-2xx-sync-api"></a>[API de sincronização do SDK do Java 2. x. x](#tab/java-v2-sync)

```java
// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.Consistent); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Create container with specified name and indexing policy
DocumentCollection documentCollection = new DocumentCollection();
documentCollection.setId("YourContainerName");
documentCollection.setIndexingPolicy(indexingPolicy);
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="stored-procedures"></a>Procedimentos armazenados

O trecho de código a seguir mostra as diferenças em como os procedimentos armazenados são criados entre as APIs de sincronização do 4,0, 3. x. x e 2. x. x:

# <a name="java-sdk-40-async-api"></a>[API Assíncrona do SDK do Java 4.0](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateSprocAsync)]

# <a name="java-sdk-3xx-async-api"></a>[API Assíncrona do SDK do Java 3.x.x](#tab/java-v3-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.partitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.responseAsString(),
                    executeResponse.statusCode(),
                    executeResponse.requestCharge()));
            return Mono.empty();
        }).block();
```

# <a name="java-sdk-2xx-sync-api"></a>[API de sincronização do SDK do Java 2. x. x](#tab/java-v2-sync)

```java
logger.info("Creating stored procedure...\n");

String sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
    "var documentToCreate = {\"id\":\"test_doc\"}\n" +
    "var context = getContext();\n" +
    "var collection = context.getCollection();\n" +
    "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
    "    function (err, documentCreated) {\n" +
    "if (err) throw new Error('Error' + err.message);\n" +
    "context.getResponse().setBody(documentCreated.id)\n" +
    "});\n" +
    "if (!accepted) return;\n" +
    "}";
StoredProcedure storedProcedureDef = new StoredProcedure();
storedProcedureDef.setId(sprocId);
storedProcedureDef.setBody(sprocBody);
StoredProcedure storedProcedure = client.createStoredProcedure(documentCollection.getSelfLink(), storedProcedureDef, new RequestOptions())
                                        .getResource();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey("test_doc"));

StoredProcedureResponse storedProcedureResponse =
    client.executeStoredProcedure(storedProcedure.getSelfLink(), options, null);
logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
    sprocId,
    storedProcedureResponse.getResponseAsString(),
    storedProcedureResponse.getStatusCode(),
    storedProcedureResponse.getRequestCharge()));
```
---

### <a name="change-feed"></a>Feed de alteração

O trecho de código a seguir mostra as diferenças em como as operações do feed de alterações são realizadas entre as APIs Assíncronas 4.0 e 3.x.x:

# <a name="java-sdk-40-async-api"></a>[API Assíncrona do SDK do Java 4.0](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateCFAsync)]

# <a name="java-sdk-3xx-async-api"></a>[API Assíncrona do SDK do Java 3.x.x](#tab/java-v3-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.Builder()
        .hostName(hostName)
        .feedContainer(feedContainer)
        .leaseContainer(leaseContainer)
        .handleChanges((List<CosmosItemProperties> docs) -> {
            logger.info("--->setHandleChanges() START");

            for (CosmosItemProperties document : docs) {
                try {

                    // You are given the document as a CosmosItemProperties instance which you may
                    // cast to the desired type.
                    CustomPOJO pojo_doc = document.getObject(CustomPOJO.class);
                    logger.info("----=>id: " + pojo_doc.id());

                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            logger.info("--->handleChanges() END");

        })
        .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```

# <a name="java-sdk-2xx-sync-api"></a>[API de sincronização do SDK do Java 2. x. x](#tab/java-v2-sync)

* Este recurso não tem suporte a partir da sincronização do Java SDK v2. 
---

### <a name="container-level-time-to-livettl"></a>Vida útil (TTL) no nível de contêiner

O trecho de código a seguir mostra as diferenças em como criar vida útil para dados no contêiner usando as APIs de sincronização do 4,0, 3. x. x e 2. x. x:

# <a name="java-sdk-40-async-api"></a>[API Assíncrona do SDK do Java 4.0](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateContainerTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[API Assíncrona do SDK do Java 3.x.x](#tab/java-v3-async)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```

# <a name="java-sdk-2xx-sync-api"></a>[API de sincronização do SDK do Java 2. x. x](#tab/java-v2-sync)

```java
DocumentCollection documentCollection;

// Create a new container with TTL enabled with default expiration value
documentCollection.setDefaultTimeToLive(90 * 60 * 60 * 24);
documentCollection = client.createCollection(database.getSelfLink(), documentCollection, new RequestOptions()).getResource();
```
---

### <a name="item-level-time-to-livettl"></a>Vida útil (TTL) no nível de item

O trecho de código a seguir mostra as diferenças em como criar vida útil para um item usando as APIs de sincronização do 4,0, 3. x. x e 2. x. x:

# <a name="java-sdk-40-async-api"></a>[API Assíncrona do SDK do Java 4.0](#tab/java-v4-async)

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLClassAsync)]

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateItemTTLAsync)]

# <a name="java-sdk-3xx-async-api"></a>[API Assíncrona do SDK do Java 3.x.x](#tab/java-v3-async)

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
    public SalesOrder id(String new_id) {this.id = new_id; return this;}
    public String customerId() {return this.customerId; return this;}
    public SalesOrder customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public SalesOrder ttl(Integer new_ttl) {this.ttl = new_ttl; return this;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```

# <a name="java-sdk-2xx-sync-api"></a>[API de sincronização do SDK do Java 2. x. x](#tab/java-v2-sync)

```java
Document document = new Document();
document.setId("YourDocumentId");
document.setTimeToLive(60 * 60 * 24 * 30 ); // Expire document in 30 days
ResourceResponse<Document> documentResourceResponse = client.createDocument(documentCollection.getSelfLink(), document,
    new RequestOptions(), true);
Document responseDocument = documentResourceResponse.getResource();
```
---

## <a name="next-steps"></a>Próximas etapas

* [Criar um de aplicativo Java](create-sql-api-java.md) para gerenciar os dados da API do SQL do Azure Cosmos DB, usando o SDK v4
* Saiba mais sobre os [SDKs do Java baseados em reator](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-pattern-guide.md)
* Saiba mais sobre como converter o código assíncrono do RxJava em código assíncrono do Reator com o [Guia de Reator versus RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md)
