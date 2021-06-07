---
title: Registrar e usar procedimentos armazenados, gatilhos e funções definidas pelo usuário em SDKs de Azure Cosmos DB
description: Saiba como registrar e chamar procedimentos armazenados, gatilhos e funções definidas pelo usuário usando SDKs do Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tisande
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 022a45199cfc2d467b1d0d408e86cb5d621070d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339835"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Como registrar e usar procedimentos armazenados, gatilhos e funções definidas pelo usuário no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A API do SQL no Azure Cosmos DB dá suporte ao registro e invocação de procedimentos armazenados, gatilhos e UDFs (funções definidas pelo usuário) escritos em JavaScript. Você pode usar os SDKs [.net](sql-api-sdk-dotnet.md), [.NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript](sql-api-sdk-node.md), [Node.js](sql-api-sdk-node.md)ou [Python](sql-api-sdk-python.md) do API do SQL para registrar e invocar os procedimentos armazenados. Depois de definir um ou mais procedimentos armazenados, gatilhos e funções definidas pelo usuário, você pode carregá-los e exibi-los no [portal do Azure](https://portal.azure.com/) usando data Explorer.

## <a name="how-to-run-stored-procedures"></a><a id="stored-procedures"></a>Como executar procedimentos armazenados

Os procedimentos armazenados são escritos usando JavaScript. Eles podem criar, atualizar, ler, consultar e excluir itens dentro de um contêiner do Azure Cosmos. Para saber mais sobre como escrever procedimentos armazenados no Azure Cosmos DB, confira o artigo [Como escrever procedimentos armazenados no Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures).

Os exemplos a seguir mostram como registrar e chamar um procedimento armazenado usando os SDKs do Azure Cosmos DB. Veja [Criar um documento](how-to-write-stored-procedures-triggers-udfs.md#create-an-item) como a fonte, pois esse procedimento armazenado é salvo como `spCreateToDoItem.js`.

> [!NOTE]
> Para contêineres particionados, ao executar um procedimento armazenado, um valor de chave de partição deve ser fornecido nas opções de solicitação. O escopo dos procedimentos armazenados sempre é uma chave de partição. Itens que têm um valor de chave de partição diferente não estarão visíveis para o procedimento armazenado. Isso também aplica-se a gatilhos.

### <a name="stored-procedures---net-sdk-v2"></a>Procedimentos armazenados-SDK do .NET v2

O exemplo a seguir mostra como registrar um procedimento armazenado usando o SDK do .NET v2:

```csharp
string storedProcedureId = "spCreateToDoItems";
StoredProcedure newStoredProcedure = new StoredProcedure
   {
       Id = storedProcedureId,
       Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
   };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var response = await client.CreateStoredProcedureAsync(containerUri, newStoredProcedure);
StoredProcedure createdStoredProcedure = response.Resource;
```

O código a seguir mostra como chamar um procedimento armazenado usando o SDK do .NET v2:

```csharp
dynamic[] newItems = new dynamic[]
{
    new {
        category = "Personal",
        name = "Groceries",
        description = "Pick up strawberries",
        isComplete = false
    },
    new {
        category = "Personal",
        name = "Doctor",
        description = "Make appointment for check up",
        isComplete = false
    }
};

Uri uri = UriFactory.CreateStoredProcedureUri("myDatabase", "myContainer", "spCreateToDoItem");
RequestOptions options = new RequestOptions { PartitionKey = new PartitionKey("Personal") };
var result = await client.ExecuteStoredProcedureAsync<string>(uri, options, new[] { newItems });
```

### <a name="stored-procedures---net-sdk-v3"></a>Procedimentos armazenados-SDK do .NET v3

O exemplo a seguir mostra como registrar um procedimento armazenado usando o SDK do .NET V3:

```csharp
string storedProcedureId = "spCreateToDoItems";
StoredProcedureResponse storedProcedureResponse = await client.GetContainer("myDatabase", "myContainer").Scripts.CreateStoredProcedureAsync(new StoredProcedureProperties
{
    Id = storedProcedureId,
    Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
});
```

O código a seguir mostra como chamar um procedimento armazenado usando o SDK do .NET V3:

```csharp
dynamic[] newItems = new dynamic[]
{
    new {
        category = "Personal",
        name = "Groceries",
        description = "Pick up strawberries",
        isComplete = false
    },
    new {
        category = "Personal",
        name = "Doctor",
        description = "Make appointment for check up",
        isComplete = false
    }
};

var result = await client.GetContainer("database", "container").Scripts.ExecuteStoredProcedureAsync<string>("spCreateToDoItem", new PartitionKey("Personal"), new[] { newItems });
```

### <a name="stored-procedures---java-sdk"></a>Procedimentos armazenados – SDK do Java

O exemplo a seguir mostra como registrar um procedimento armazenado usando o SDK do Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
StoredProcedure newStoredProcedure = new StoredProcedure(
    "{" +
        "  'id':'spCreateToDoItems'," +
        "  'body':" + new String(Files.readAllBytes(Paths.get("..\\js\\spCreateToDoItems.js"))) +
    "}");
//toBlocking() blocks the thread until the operation is complete and is used only for demo.  
StoredProcedure createdStoredProcedure = asyncClient.createStoredProcedure(containerLink, newStoredProcedure, null)
    .toBlocking().single().getResource();
```

O código a seguir mostra como chamar um procedimento armazenado usando o SDK do Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String sprocLink = String.format("%s/sprocs/%s", containerLink, "spCreateToDoItems");
final CountDownLatch successfulCompletionLatch = new CountDownLatch(1);

List<ToDoItem> ToDoItems = new ArrayList<ToDoItem>();

class ToDoItem {
    public String category;
    public String name;
    public String description;
    public boolean isComplete;
}

ToDoItem newItem = new ToDoItem();
newItem.category = "Personal";
newItem.name = "Groceries";
newItem.description = "Pick up strawberries";
newItem.isComplete = false;

ToDoItems.add(newItem)

newItem.category = "Personal";
newItem.name = "Doctor";
newItem.description = "Make appointment for check up";
newItem.isComplete = false;

ToDoItems.add(newItem)

RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("Personal"));

Object[] storedProcedureArgs = new Object[] { ToDoItems };
asyncClient.executeStoredProcedure(sprocLink, requestOptions, storedProcedureArgs)
    .subscribe(storedProcedureResponse -> {
        String storedProcResultAsString = storedProcedureResponse.getResponseAsString();
        successfulCompletionLatch.countDown();
        System.out.println(storedProcedureResponse.getActivityId());
    }, error -> {
        successfulCompletionLatch.countDown();
        System.err.println("an error occurred while executing the stored procedure: actual cause: "
                + error.getMessage());
    });

successfulCompletionLatch.await();
```

### <a name="stored-procedures---javascript-sdk"></a>Procedimentos armazenados – SDK do JavaScript

O exemplo a seguir mostra como registrar um procedimento armazenado usando o SDK do JavaScript

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItems";
await container.scripts.storedProcedures.create({
    id: sprocId,
    body: require(`../js/${sprocId}`)
});
```

O código a seguir mostra como chamar um procedimento armazenado usando o SDK do JavaScript:

```javascript
const newItem = [{
    category: "Personal",
    name: "Groceries",
    description: "Pick up strawberries",
    isComplete: false
}];
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItems";
const {body: result} = await container.scripts.storedProcedure(sprocId).execute(newItem, {partitionKey: newItem[0].category});
```

### <a name="stored-procedures---python-sdk"></a>Procedimentos armazenados – SDK do Python

O exemplo a seguir mostra como registrar um procedimento armazenado usando o SDK do Python:

```python
import azure.cosmos.cosmos_client as cosmos_client

url = "your_cosmos_db_account_URI"
key = "your_cosmos_db_account_key"
database_name = 'your_cosmos_db_database_name'
container_name = 'your_cosmos_db_container_name'

with open('../js/spCreateToDoItems.js') as file:
    file_contents = file.read()

sproc = {
    'id': 'spCreateToDoItem',
    'serverScript': file_contents,
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
created_sproc = container.scripts.create_stored_procedure(body=sproc) 
```

O código a seguir mostra como chamar um procedimento armazenado usando o SDK do Python:

```python
import uuid

new_id= str(uuid.uuid4())

# Creating a document for a container with "id" as a partition key.

new_item =   {
      "id": new_id, 
      "category":"Personal",
      "name":"Groceries",
      "description":"Pick up strawberries",
      "isComplete":False
   }
result = container.scripts.execute_stored_procedure(sproc=created_sproc,params=[[new_item]], partition_key=new_id) 
```

## <a name="how-to-run-pre-triggers"></a><a id="pre-triggers"></a>Como executar pré-gatilhos

Os exemplos a seguir mostram como registrar e chamar um pré-gatilho usando os SDKs do Azure Cosmos DB. Veja o [Exemplo de pré-gatilho](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers) como a origem, pois esse pré-gatilho é salvo como `trgPreValidateToDoItemTimestamp.js`.

Ao executar, pré-gatilhos são transmitidos para o objeto RequestOptions especificando `PreTriggerInclude` e, em seguida, transmitindo o nome do gatilho em um objeto List.

> [!NOTE]
> Mesmo que o nome do gatilho seja transmitido como uma lista, ainda é possível executar apenas um gatilho por operação.

### <a name="pre-triggers---net-sdk-v2"></a>Pré-gatilhos-SDK do .NET v2

O código a seguir mostra como registrar um pré-gatilho usando o SDK do .NET v2:

```csharp
string triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger
{
    Id =  triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

O código a seguir mostra como chamar um pré-gatilho usando o SDK do .NET v2:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
RequestOptions requestOptions = new RequestOptions { PreTriggerInclude = new List<string> { "trgPreValidateToDoItemTimestamp" } };
await client.CreateDocumentAsync(containerUri, newItem, requestOptions);
```

### <a name="pre-triggers---net-sdk-v3"></a>Pré-gatilhos-SDK do .NET v3

O código a seguir mostra como registrar um pré-gatilho usando o SDK do .NET V3:

```csharp
await client.GetContainer("database", "container").Scripts.CreateTriggerAsync(new TriggerProperties
{
    Id = "trgPreValidateToDoItemTimestamp",
    Body = File.ReadAllText("@..\js\trgPreValidateToDoItemTimestamp.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
});
```

O código a seguir mostra como chamar um pré-gatilho usando o SDK do .NET V3:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

await client.GetContainer("database", "container").CreateItemAsync(newItem, null, new ItemRequestOptions { PreTriggers = new List<string> { "trgPreValidateToDoItemTimestamp" } });
```

### <a name="pre-triggers---java-sdk"></a>Pré-gatilhos – SDK do Java

O código a seguir mostra como registrar um pré-gatilho usando o SDK do Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Pre);
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

O código a seguir mostra como chamar um pré-gatilho usando o SDK do Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
    Document item = new Document("{ "
            + "\"category\": \"Personal\", "
            + "\"name\": \"Groceries\", "
            + "\"description\": \"Pick up strawberries\", "
            + "\"isComplete\": false, "
            + "}"
            );
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPreTriggerInclude(Arrays.asList("trgPreValidateToDoItemTimestamp"));
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="pre-triggers---javascript-sdk"></a>Pré-gatilhos – SDK do JavaScript

O código a seguir mostra como registrar um pré-gatilho usando o SDK do JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "pre"
});
```

O código a seguir mostra como chamar um pré-gatilho usando o SDK do JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.items.create({
    category: "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
}, {preTriggerInclude: [triggerId]});
```

### <a name="pre-triggers---python-sdk"></a>Pré-gatilhos – SDK do Python

O código a seguir mostra como registrar um pré-gatilho usando o SDK do Python:

```python
import azure.cosmos.cosmos_client as cosmos_client

url = "your_cosmos_db_account_URI"
key = "your_cosmos_db_account_key"
database_name = 'your_cosmos_db_database_name'
container_name = 'your_cosmos_db_container_name'

with open('../js/trgPreValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()

trigger_definition = {
    'id': 'trgPreValidateToDoItemTimestamp',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Pre,
    'triggerOperation': documents.TriggerOperation.All
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
trigger = container.scripts.create_trigger(trigger_definition)
```

O código a seguir mostra como chamar um pré-gatilho usando o SDK do Python:

```python
item = {'category': 'Personal', 'name': 'Groceries',
        'description': 'Pick up strawberries', 'isComplete': False}
container.create_item(item, {'pre_trigger_include': 'trgPreValidateToDoItemTimestamp'})
```

## <a name="how-to-run-post-triggers"></a><a id="post-triggers"></a>Como executar pós-gatilhos

Os exemplos a seguir mostram como registrar um pós-gatilho usando os SDKs do Azure Cosmos DB. Veja o [Exemplo de pós-gatilho](how-to-write-stored-procedures-triggers-udfs.md#post-triggers) como a origem, pois esse pós-gatilho é salvo como `trgPostUpdateMetadata.js`.

### <a name="post-triggers---net-sdk-v2"></a>Pós-gatilhos-SDK do .NET v2

O código a seguir mostra como registrar um post-Trigger usando o SDK do .NET v2:

```csharp
string triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger
{
    Id = triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

O código a seguir mostra como chamar um post-Trigger usando o SDK do .NET v2:

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

RequestOptions options = new RequestOptions { PostTriggerInclude = new List<string> { "trgPostUpdateMetadata" } };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.createDocumentAsync(containerUri, newItem, options);
```

### <a name="post-triggers---net-sdk-v3"></a>Pós-gatilhos-SDK do .NET v3

O código a seguir mostra como registrar um post-Trigger usando o SDK do .NET V3:

```csharp
await client.GetContainer("database", "container").Scripts.CreateTriggerAsync(new TriggerProperties
{
    Id = "trgPostUpdateMetadata",
    Body = File.ReadAllText(@"..\js\trgPostUpdateMetadata.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
});
```

O código a seguir mostra como chamar um post-Trigger usando o SDK do .NET V3:

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

await client.GetContainer("database", "container").CreateItemAsync(newItem, null, new ItemRequestOptions { PostTriggers = new List<string> { "trgPostUpdateMetadata" } });
```

### <a name="post-triggers---java-sdk"></a>Pós-gatilhos – SDK do Java

O código a seguir mostra como registrar um pós-gatilho usando o SDK do Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)))));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Post);
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

O código a seguir mostra como chamar um pós-gatilho usando o SDK do Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Document item = new Document(String.format("{ "
    + "\"name\": \"artist_profile_1023\", "
    + "\"artist\": \"The Band\", "
    + "\"albums\": [\"Hellujah\", \"Rotators\", \"Spinning Top\"]"
    + "}"
));
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPostTriggerInclude(Arrays.asList("trgPostUpdateMetadata"));
//toBlocking() blocks the thread until the operation is complete, and is used only for demo.
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="post-triggers---javascript-sdk"></a>Pós-gatilhos – SDK do JavaScript

O código a seguir mostra como registrar um pós-gatilho usando o SDK do JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "post"
});
```

O código a seguir mostra como chamar um pós-gatilho usando o SDK do JavaScript:

```javascript
const item = {
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.items.create(item, {postTriggerInclude: [triggerId]});
```

### <a name="post-triggers---python-sdk"></a>Pós-gatilhos – SDK do Python

O código a seguir mostra como registrar um pós-gatilho usando o SDK do Python:

```python
import azure.cosmos.cosmos_client as cosmos_client

url = "your_cosmos_db_account_URI"
key = "your_cosmos_db_account_key"
database_name = 'your_cosmos_db_database_name'
container_name = 'your_cosmos_db_container_name'

with open('../js/trgPostValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()

trigger_definition = {
    'id': 'trgPostValidateToDoItemTimestamp',
    'serverScript': file_contents,
    'triggerType': documents.TriggerType.Post,
    'triggerOperation': documents.TriggerOperation.All
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
trigger = container.scripts.create_trigger(trigger_definition)
```

O código a seguir mostra como chamar um pós-gatilho usando o SDK do Python:

```python
item = {'category': 'Personal', 'name': 'Groceries',
        'description': 'Pick up strawberries', 'isComplete': False}
container.create_item(item, {'post_trigger_include': 'trgPreValidateToDoItemTimestamp'})
```

## <a name="how-to-work-with-user-defined-functions"></a><a id="udfs"></a>Como trabalhar com funções definidas pelo usuário

Os exemplos a seguir mostram como registrar uma função definida pelo usuário usando os SDKs do Azure Cosmos DB. Veja o [Exemplo de função definida pelo usuário](how-to-write-stored-procedures-triggers-udfs.md#udfs) como a origem, pois esse pós-gatilho é salvo como `udfTax.js`.

### <a name="user-defined-functions---net-sdk-v2"></a>Funções definidas pelo usuário – SDK do .NET v2

O código a seguir mostra como registrar uma função definida pelo usuário usando o SDK do .NET v2:

```csharp
string udfId = "Tax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js")
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

O código a seguir mostra como chamar uma função definida pelo usuário usando o SDK do .NET v2:

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---net-sdk-v3"></a>Funções definidas pelo usuário – SDK do .NET v3

O código a seguir mostra como registrar uma função definida pelo usuário usando o SDK do .NET V3:

```csharp
await client.GetContainer("database", "container").Scripts.CreateUserDefinedFunctionAsync(new UserDefinedFunctionProperties
{
    Id = "Tax",
    Body = File.ReadAllText(@"..\js\Tax.js")
});
```

O código a seguir mostra como chamar uma função definida pelo usuário usando o SDK do .NET V3:

```csharp
var iterator = client.GetContainer("database", "container").GetItemQueryIterator<dynamic>("SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000");
while (iterator.HasMoreResults)
{
    var results = await iterator.ReadNextAsync();
    foreach (var result in results)
    {
        //iterate over results
    }
}
```

### <a name="user-defined-functions---java-sdk"></a>Funções definidas pelo usuário – SDK do Java

O código a seguir mostra como registrar uma função definida pelo usuário usando o SDK do Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String udfId = "Tax";
UserDefinedFunction udf = new UserDefinedFunction();
udf.setId(udfId);
udf.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", udfId)))));
//toBlocking() blocks the thread until the operation is complete and is used only for demo.
UserDefinedFunction createdUDF = client.createUserDefinedFunction(containerLink, udf, new RequestOptions()).toBlocking().single().getResource();
```

O código a seguir mostra como chamar uma função definida pelo usuário usando o SDK do Java:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(containerLink, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000", new FeedOptions());
final CountDownLatch completionLatch = new CountDownLatch(1);
queryObservable.subscribe(
        queryResultPage -> {
            System.out.println("Got a page of query result with " +
                    queryResultPage.getResults().size());
        },
        // terminal error signal
        e -> {
            e.printStackTrace();
            completionLatch.countDown();
        },

        // terminal completion signal
        () -> {
            completionLatch.countDown();
        });
completionLatch.await();
```

### <a name="user-defined-functions---javascript-sdk"></a>Funções definidas pelo usuário – SDK do JavaScript

O código a seguir mostra como registrar uma função definida pelo usuário usando o SDK do JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const udfId = "Tax";
await container.userDefinedFunctions.create({
    id: udfId,
    body: require(`../js/${udfId}`)
```

O código a seguir mostra como chamar uma função definida pelo usuário usando o SDK do JavaScript:

```javascript
const container = client.database("myDatabase").container("myContainer");
const sql = "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000";
const {result} = await container.items.query(sql).toArray();
```

### <a name="user-defined-functions---python-sdk"></a>Funções definidas pelo usuário – SDK do Python

O código a seguir mostra como registrar uma função definida pelo usuário usando o SDK do Python:

```python
import azure.cosmos.cosmos_client as cosmos_client

url = "your_cosmos_db_account_URI"
key = "your_cosmos_db_account_key"
database_name = 'your_cosmos_db_database_name'
container_name = 'your_cosmos_db_container_name'

with open('../js/udfTax.js') as file:
    file_contents = file.read()
udf_definition = {
    'id': 'Tax',
    'serverScript': file_contents,
}
client = cosmos_client.CosmosClient(url, key)
database = client.get_database_client(database_name)
container = database.get_container_client(container_name)
udf = container.scripts.create_user_defined_function(udf_definition)
```

O código a seguir mostra como chamar uma função definida pelo usuário usando o SDK do Python:

```python
results = list(container.query_items(
    'query': 'SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000'))
```

## <a name="next-steps"></a>Próximas etapas

Aprenda mais conceitos e como escrever ou usar procedimentos armazenados, gatilhos e funções definidas pelo usuário no Azure Cosmos DB:

- [Como trabalhar com procedimentos armazenados, gatilhos e funções definidas pelo usuário do Azure Cosmos DB no Azure Cosmos DB](stored-procedures-triggers-udfs.md)
- [Como trabalhar com a API de consulta integrada à linguagem do JavaScript no Azure Cosmos DB](javascript-query-api.md)
- [Como escrever procedimentos armazenados, gatilhos e funções definidas pelo usuário no Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md)
- [Como escrever procedimentos armazenados e gatilhos usando a API de Consulta do JavaScript no Azure Cosmos DB](how-to-write-javascript-query-api.md)
