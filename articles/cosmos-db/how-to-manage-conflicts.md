---
title: Gerenciar conflitos entre regiões no Azure Cosmos DB
description: Saiba como gerenciar conflitos no Azure Cosmos DB criando a política de resolução de conflitos last-writer-WINS ou personalizada
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/11/2020
ms.author: anfeldma
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8f98c2201159350f5774f4d2b05102384f31f3af
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339335"
---
# <a name="manage-conflict-resolution-policies-in-azure-cosmos-db"></a>Gerenciar políticas de resolução de conflitos no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Com gravações de várias regiões, quando vários clientes gravam no mesmo item, poderão ocorrer conflitos. Quando ocorre um conflito, você pode resolvê-lo usando políticas de resolução de conflitos diferentes. Este artigo descreve como gerenciar políticas de resolução de conflitos.

## <a name="create-a-last-writer-wins-conflict-resolution-policy"></a>Crie uma política de resolução de conflitos do tipo “última gravação ganha”

Estes exemplos mostram como configurar um contêiner com uma política de resolução de conflitos do tipo “última gravação ganha”. O caminho padrão para last-writer-wins é o campo de carimbo de data/hora ou a propriedade `_ts`. Para a API do SQL, isso também pode ser definido como um caminho definido pelo usuário com um tipo numérico. Em um conflito, o valor mais alto ganha. Se o caminho não estiver definido ou for inválido, o padrão será `_ts`. Conflitos resolvidos com essa política não aparecem no feed de conflito. Essa política pode ser usada por todas as APIs.

### <a name="net-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-dotnet"></a>SDK .NET

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection lwwCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.lwwCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.LastWriterWins,
          ConflictResolutionPath = "/myCustomId",
      },
  });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.lwwCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.LastWriterWins,
            ResolutionPath = "/myCustomId",
        }
    });
```
---

### <a name="java-v4-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-javav4"></a> SDK do Java v4

# <a name="async"></a>[Async](#tab/api-async)

   API assíncrona do SDK do Java V4 (Maven com.azure::azure-cosmos)

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConflictResolutionLWWAsync)]

# <a name="sync"></a>[Sincronizar](#tab/api-sync)

   API síncrona do SDK do Java V4 (Maven com.azure::azure-cosmos)

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConflictResolutionLWWSync)]

--- 

### <a name="java-v2-sdks"></a><a id="create-custom-conflict-resolution-policy-lww-javav2"></a>SDKs do Java v2

# <a name="async-java-v2-sdk"></a>[SDK do Java v2 assíncrono](#tab/async)

[SDK do Java v2 assíncrono](sql-api-sdk-async-java.md) (Maven [com. Microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb))

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

# <a name="sync-java-v2-sdk"></a>[Sincronizar SDK do Java v2](#tab/sync)

[Sincronizar o SDK do Java v2](sql-api-sdk-java.md) (Maven [com. Microsoft. Azure:: Azure-documentdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb))

```java
DocumentCollection lwwCollection = new DocumentCollection();
lwwCollection.setId(this.lwwCollectionName);
ConflictResolutionPolicy lwwPolicy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
lwwCollection.setConflictResolutionPolicy(lwwPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, lwwCollection);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-javascript"></a>SDK de Node.js/JavaScript/TypeScript

```javascript
const database = client.database(this.databaseName);
const { container: lwwContainer } = await database.containers.createIfNotExists(
  {
    id: this.lwwContainerName,
    conflictResolutionPolicy: {
      mode: "LastWriterWins",
      conflictResolutionPath: "/myCustomId"
    }
  }
);
```

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-lww-python"></a>SDK do Python

```python
udp_collection = {
    'id': self.udp_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'LastWriterWins',
        'conflictResolutionPath': '/myCustomId'
    }
}
udp_collection = self.try_create_document_collection(
    create_client, database, udp_collection)
```

## <a name="create-a-custom-conflict-resolution-policy-using-a-stored-procedure"></a>Criar uma política de resolução de conflitos personalizada usando um procedimento armazenado

Estes exemplos mostram como configurar um contêiner com uma política de resolução de conflitos personalizada com procedimento armazenado para resolver o conflito. Esses conflitos não aparecem no feed de conflitos se não houver um erro em seu procedimento armazenado. Depois que a política é criada com o contêiner, você precisa criar o procedimento armazenado. O exemplo de SDK do .NET a seguir mostra um exemplo. Essa política tem suporte apenas na API Core (SQL).

### <a name="sample-custom-conflict-resolution-stored-procedure"></a>Exemplo de procedimento armazenado de resolução de conflitos personalizado

Procedimentos de armazenados de resolução de conflitos personalizados devem ser implementados usando a assinatura de função mostrada abaixo. O nome da função não precisa corresponder ao nome usado ao registrar o procedimento armazenado com o contêiner, mas simplifica a nomenclatura. Aqui está uma descrição dos parâmetros que devem ser implementados para esse procedimento armazenado.

- **incomingItem**: o item que está sendo inserido ou atualizado na confirmação que está gerando os conflitos. É nulo para operações de exclusão.
- **existingItem**: o item atualmente confirmado. Esse valor é null em uma atualização e nulo para uma inserção ou exclusão.
- isdelete: booliano que indica se o incomingItem está em conflito com um item excluído anteriormente. Quando verdadeiro, existingItem também será null.
- **conflictingItems**: matriz da versão confirmada de todos os itens no contêiner que estão em conflito com INCOMINGITEM na ID ou qualquer outra propriedade de índice exclusivo.

> [!IMPORTANT]
> Assim como com qualquer procedimento armazenado, um procedimento de resolução de conflitos personalizado pode acessar todos os dados com a mesma chave de partição e executar qualquer operação de inserir, atualizar ou excluir para resolver conflitos.

Esse exemplo procedimento armazenado resolve conflitos selecionando o valor mais baixo do caminho de `/myCustomId`.

```javascript
function resolver(incomingItem, existingItem, isTombstone, conflictingItems) {
  var collection = getContext().getCollection();

  if (!incomingItem) {
      if (existingItem) {

          collection.deleteDocument(existingItem._self, {}, function (err, responseOptions) {
              if (err) throw err;
          });
      }
  } else if (isTombstone) {
      // delete always wins.
  } else {
      if (existingItem) {
          if (incomingItem.myCustomId > existingItem.myCustomId) {
              return; // existing item wins
          }
      }

      var i;
      for (i = 0; i < conflictingItems.length; i++) {
          if (incomingItem.myCustomId > conflictingItems[i].myCustomId) {
              return; // existing conflict item wins
          }
      }

      // incoming item wins - clear conflicts and replace existing with incoming.
      tryDelete(conflictingItems, incomingItem, existingItem);
  }

  function tryDelete(documents, incoming, existing) {
      if (documents.length > 0) {
          collection.deleteDocument(documents[0]._self, {}, function (err, responseOptions) {
              if (err) throw err;

              documents.shift();
              tryDelete(documents, incoming, existing);
          });
      } else if (existing) {
          collection.replaceDocument(existing._self, incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      } else {
          collection.createDocument(collection.getSelfLink(), incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      }
  }
}
```

### <a name="net-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-dotnet"></a>SDK .NET

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection udpCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.udpCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
          ConflictResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver"),
      },
  });

//Create the stored procedure
await clients[0].CreateStoredProcedureAsync(
UriFactory.CreateStoredProcedureUri(this.databaseName, this.udpCollectionName, "resolver"), new StoredProcedure
{
    Id = "resolver",
    Body = File.ReadAllText(@"resolver.js")
});
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.udpCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.Custom,
            ResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver")
        }
    });

await container.Scripts.CreateStoredProcedureAsync(
    new StoredProcedureProperties("resolver", File.ReadAllText(@"resolver.js"))
);
```
---

### <a name="java-v4-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-javav4"></a> SDK do Java v4

# <a name="async"></a>[Async](#tab/api-async)

   API assíncrona do SDK do Java V4 (Maven com.azure::azure-cosmos)

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConflictResolutionSprocAsync)]

# <a name="sync"></a>[Sincronizar](#tab/api-sync)

   API síncrona do SDK do Java V4 (Maven com.azure::azure-cosmos)

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConflictResolutionSprocSync)]

--- 

### <a name="java-v2-sdks"></a><a id="create-custom-conflict-resolution-policy-stored-proc-javav2"></a>SDKs do Java v2

# <a name="async-java-v2-sdk"></a>[SDK do Java v2 assíncrono](#tab/async)

[SDK do Java v2 assíncrono](sql-api-sdk-async-java.md) (Maven [com. Microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb))

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy("resolver");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

# <a name="sync-java-v2-sdk"></a>[Sincronizar SDK do Java v2](#tab/sync)

[Sincronizar o SDK do Java v2](sql-api-sdk-java.md) (Maven [com. Microsoft. Azure:: Azure-documentdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb))

```java
DocumentCollection udpCollection = new DocumentCollection();
udpCollection.setId(this.udpCollectionName);
ConflictResolutionPolicy udpPolicy = ConflictResolutionPolicy.createCustomPolicy(
        String.format("dbs/%s/colls/%s/sprocs/%s", this.databaseName, this.udpCollectionName, "resolver"));
udpCollection.setConflictResolutionPolicy(udpPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, udpCollection);
```
---

Depois que o contêiner é criado, você deve criar o `resolver` procedimento armazenado.

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-javascript"></a>SDK de Node.js/JavaScript/TypeScript

```javascript
const database = client.database(this.databaseName);
const { container: udpContainer } = await database.containers.createIfNotExists(
  {
    id: this.udpContainerName,
    conflictResolutionPolicy: {
      mode: "Custom",
      conflictResolutionProcedure: `dbs/${this.databaseName}/colls/${
        this.udpContainerName
      }/sprocs/resolver`
    }
  }
);
```

Depois que o contêiner é criado, você deve criar o `resolver` procedimento armazenado.

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-stored-proc-python"></a>SDK do Python

```python
udp_collection = {
    'id': self.udp_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'Custom',
        'conflictResolutionProcedure': 'dbs/' + self.database_name + "/colls/" + self.udp_collection_name + '/sprocs/resolver'
    }
}
udp_collection = self.try_create_document_collection(
    create_client, database, udp_collection)
```

Depois que o contêiner é criado, você deve criar o `resolver` procedimento armazenado.

## <a name="create-a-custom-conflict-resolution-policy"></a>Criar uma política de resolução de conflitos personalizada

Estes exemplos mostram como configurar um contêiner com uma política de resolução de conflitos personalizada. Esses conflitos aparecem no feed de conflitos.

### <a name="net-sdk"></a><a id="create-custom-conflict-resolution-policy-dotnet"></a>SDK .NET

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
DocumentCollection manualCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.manualCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
      },
  });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.manualCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.Custom
        }
    });
```
---

### <a name="java-v4-sdk"></a><a id="create-custom-conflict-resolution-policy-javav4"></a> SDK do Java v4

# <a name="async"></a>[Async](#tab/api-async)

   API assíncrona do SDK do Java V4 (Maven com.azure::azure-cosmos)

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConflictResolutionCustomAsync)]

# <a name="sync"></a>[Sincronizar](#tab/api-sync)

   API síncrona do SDK do Java V4 (Maven com.azure::azure-cosmos)

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConflictResolutionCustomSync)]

--- 

### <a name="java-v2-sdks"></a><a id="create-custom-conflict-resolution-policy-javav2"></a>SDKs do Java v2

# <a name="async-java-v2-sdk"></a>[SDK do Java v2 assíncrono](#tab/async)

[SDK do Java v2 assíncrono](sql-api-sdk-async-java.md) (Maven [com. Microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb))

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy();
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

# <a name="sync-java-v2-sdk"></a>[Sincronizar SDK do Java v2](#tab/sync)

[Sincronizar o SDK do Java v2](sql-api-sdk-java.md) (Maven [com. Microsoft. Azure:: Azure-documentdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb))

```java
DocumentCollection manualCollection = new DocumentCollection();
manualCollection.setId(this.manualCollectionName);
ConflictResolutionPolicy customPolicy = ConflictResolutionPolicy.createCustomPolicy(null);
manualCollection.setConflictResolutionPolicy(customPolicy);
DocumentCollection createdCollection = client.createCollection(database.getSelfLink(), collection, null).getResource();
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="create-custom-conflict-resolution-policy-javascript"></a>SDK de Node.js/JavaScript/TypeScript

```javascript
const database = client.database(this.databaseName);
const {
  container: manualContainer
} = await database.containers.createIfNotExists({
  id: this.manualContainerName,
  conflictResolutionPolicy: {
    mode: "Custom"
  }
});
```

### <a name="python-sdk"></a><a id="create-custom-conflict-resolution-policy-python"></a>SDK do Python

```python
database = client.ReadDatabase("dbs/" + self.database_name)
manual_collection = {
    'id': self.manual_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'Custom'
    }
}
manual_collection = client.CreateContainer(database['_self'], collection)
```

## <a name="read-from-conflict-feed"></a>Ler o feed de conflitos

Estes exemplos mostram como ler o feed de conflitos do contêiner. Os conflitos aparecerão no feed de conflito somente se não forem resolvidos automaticamente ou se estiverem usando uma política de conflitos personalizada.

### <a name="net-sdk"></a><a id="read-from-conflict-feed-dotnet"></a>SDK .NET

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
FeedResponse<Conflict> conflicts = await delClient.ReadConflictFeedAsync(this.collectionUri);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
FeedIterator<ConflictProperties> conflictFeed = container.Conflicts.GetConflictQueryIterator();
while (conflictFeed.HasMoreResults)
{
    FeedResponse<ConflictProperties> conflicts = await conflictFeed.ReadNextAsync();
    foreach (ConflictProperties conflict in conflicts)
    {
        // Read the conflicted content
        MyClass intendedChanges = container.Conflicts.ReadConflictContent<MyClass>(conflict);
        MyClass currentState = await container.Conflicts.ReadCurrentAsync<MyClass>(conflict, new PartitionKey(intendedChanges.MyPartitionKey));

        // Do manual merge among documents
        await container.ReplaceItemAsync<MyClass>(intendedChanges, intendedChanges.Id, new PartitionKey(intendedChanges.MyPartitionKey));

        // Delete the conflict
        await container.Conflicts.DeleteAsync(conflict, new PartitionKey(intendedChanges.MyPartitionKey));
    }
}
```
---

### <a name="java-v2-sdks"></a><a id="read-from-conflict-feed-javav2"></a>SDKs do Java v2

# <a name="async-java-v2-sdk"></a>[SDK do Java v2 assíncrono](#tab/async)

[SDK do Java v2 assíncrono](sql-api-sdk-async-java.md) (Maven [com. Microsoft. Azure:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb))

```java
FeedResponse<Conflict> response = client.readConflicts(this.manualCollectionUri, null)
                    .first().toBlocking().single();
for (Conflict conflict : response.getResults()) {
    /* Do something with conflict */
}
```
# <a name="sync-java-v2-sdk"></a>[Sincronizar SDK do Java v2](#tab/sync)

[Sincronizar o SDK do Java v2](sql-api-sdk-java.md) (Maven [com. Microsoft. Azure:: Azure-documentdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb))

```java
Iterator<Conflict> conflictsIterator = client.readConflicts(this.collectionLink, null).getQueryIterator();
while (conflictsIterator.hasNext()) {
    Conflict conflict = conflictsIterator.next();
    /* Do something with conflict */
}
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="read-from-conflict-feed-javascript"></a>SDK de Node.js/JavaScript/TypeScript

```javascript
const container = client
  .database(this.databaseName)
  .container(this.lwwContainerName);

const { result: conflicts } = await container.conflicts.readAll().toArray();
```

### <a name="python"></a><a id="read-from-conflict-feed-python"></a>Python

```python
conflicts_iterator = iter(client.ReadConflicts(self.manual_collection_link))
conflict = next(conflicts_iterator, None)
while conflict:
    # Do something with conflict
    conflict = next(conflicts_iterator, None)
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os conceitos do Azure Cosmos DB a seguir:

- [Distribuição global - nos bastidores](global-dist-under-the-hood.md)
- [Como configurar gravações de várias regiões em seus aplicativos](how-to-multi-master.md)
- [Configurar clientes para multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
- [Como adicionar/remover regiões da conta do Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
- [Como configuremulti as gravações em seus aplicativos](how-to-multi-master.md).
- [Particionamento e distribuição de dados](partitioning-overview.md)
- [Indexação no Azure Cosmos DB](index-policy.md)
