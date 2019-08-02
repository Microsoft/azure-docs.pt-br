---
title: Saiba como gerenciar conflitos entre regiões no Azure Cosmos DB
description: Saiba como gerenciar conflitos no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 06/25/2019
ms.author: mjbrown
ms.openlocfilehash: 96171d4729187ca03f1e9529551a7fb6a26c6976
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360367"
---
# <a name="manage-conflict-resolution-policies-in-azure-cosmos-db"></a>Gerenciar políticas de resolução de conflitos no Azure Cosmos DB

Com gravações de várias regiões, quando vários clientes gravam no mesmo item, poderão ocorrer conflitos. Quando ocorre um conflito, você pode resolvê-lo usando políticas de resolução de conflitos diferentes. Este artigo descreve como gerenciar políticas de resolução de conflitos.

## <a name="create-a-last-writer-wins-conflict-resolution-policy"></a>Crie uma política de resolução de conflitos do tipo “última gravação ganha”

Estes exemplos mostram como configurar um contêiner com uma política de resolução de conflitos do tipo “última gravação ganha”. O caminho padrão para last-writer-wins é o campo de carimbo de data/hora ou a propriedade `_ts`. Isso também pode ser definido como um caminho definido pelo usuário para um tipo numérico. Em um conflito, o valor mais alto ganha. Se o caminho não estiver definido ou for inválido, o padrão será `_ts`. Conflitos resolvidos com essa política não aparecem no feed de conflito. Essa política pode ser usada por todas as APIs.

### <a id="create-custom-conflict-resolution-policy-lww-dotnet"></a>SDK do .NET V2

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

### <a id="create-custom-conflict-resolution-policy-lww-dotnet-v3"></a>SDK do .NET V3

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

### <a id="create-custom-conflict-resolution-policy-lww-java-async"></a>SDK de Java Assíncrono

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a id="create-custom-conflict-resolution-policy-lww-java-sync"></a>SDK de Java Síncrono

```java
DocumentCollection lwwCollection = new DocumentCollection();
lwwCollection.setId(this.lwwCollectionName);
ConflictResolutionPolicy lwwPolicy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
lwwCollection.setConflictResolutionPolicy(lwwPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, lwwCollection);
```

### <a id="create-custom-conflict-resolution-policy-lww-javascript"></a>SDK de Node.js/JavaScript/TypeScript

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

### <a id="create-custom-conflict-resolution-policy-lww-python"></a>SDK do Python

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
- **existingItem**: o item comprometido no momento. Esse valor é null em uma atualização e nulo para uma inserção ou exclusão.
- **isTombstone**: booliano que indica se o incomingItem está em conflito com um item excluído anteriormente. Quando verdadeiro, existingItem também será null.
- **conflictingItems**: Matriz da versão confirmada de todos os itens no contêiner que estão em conflito com incomingItem na ID ou quaisquer outras propriedades de índice exclusivo.

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

### <a id="create-custom-conflict-resolution-policy-stored-proc-dotnet"></a>SDK do .NET V2

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

### <a id="create-custom-conflict-resolution-policy-stored-proc-dotnet-v3"></a>SDK do .NET V3

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

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-async"></a>SDK de Java Assíncrono

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy("resolver");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

Depois que o contêiner é criado, você deve criar o `resolver` procedimento armazenado.

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-sync"></a>SDK de Java Síncrono

```java
DocumentCollection udpCollection = new DocumentCollection();
udpCollection.setId(this.udpCollectionName);
ConflictResolutionPolicy udpPolicy = ConflictResolutionPolicy.createCustomPolicy(
        String.format("dbs/%s/colls/%s/sprocs/%s", this.databaseName, this.udpCollectionName, "resolver"));
udpCollection.setConflictResolutionPolicy(udpPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, udpCollection);
```

Depois que o contêiner é criado, você deve criar o `resolver` procedimento armazenado.

### <a id="create-custom-conflict-resolution-policy-stored-proc-javascript"></a>SDK de Node.js/JavaScript/TypeScript

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

### <a id="create-custom-conflict-resolution-policy-stored-proc-python"></a>SDK do Python

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

### <a id="create-custom-conflict-resolution-policy-dotnet"></a>SDK do .NET V2

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

### <a id="create-custom-conflict-resolution-policy-dotnet-v3"></a>SDK do .NET V3

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

### <a id="create-custom-conflict-resolution-policy-java-async"></a>SDK de Java Assíncrono

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy();
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a id="create-custom-conflict-resolution-policy-java-sync"></a>SDK de Java Síncrono

```java
DocumentCollection manualCollection = new DocumentCollection();
manualCollection.setId(this.manualCollectionName);
ConflictResolutionPolicy customPolicy = ConflictResolutionPolicy.createCustomPolicy(null);
manualCollection.setConflictResolutionPolicy(customPolicy);
DocumentCollection createdCollection = client.createCollection(database.getSelfLink(), collection, null).getResource();
```

### <a id="create-custom-conflict-resolution-policy-javascript"></a>SDK de Node.js/JavaScript/TypeScript

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

### <a id="create-custom-conflict-resolution-policy-python"></a>SDK do Python

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

### <a id="read-from-conflict-feed-dotnet"></a>SDK do .NET V2

```csharp
FeedResponse<Conflict> conflicts = await delClient.ReadConflictFeedAsync(this.collectionUri);
```

### <a id="read-from-conflict-feed-dotnet-v3"></a>SDK do .NET V3

```csharp
FeedIterator<ConflictProperties> conflictFeed = container.Conflicts.GetConflictIterator();
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

### <a id="read-from-conflict-feed-java-async"></a>SDK de Java Assíncrono

```java
FeedResponse<Conflict> response = client.readConflicts(this.manualCollectionUri, null)
                    .first().toBlocking().single();
for (Conflict conflict : response.getResults()) {
    /* Do something with conflict */
}
```

### <a id="read-from-conflict-feed-java-sync"></a>SDK de Java Síncrono

```java
Iterator<Conflict> conflictsIterator = client.readConflicts(this.collectionLink, null).getQueryIterator();
while (conflictsIterator.hasNext()) {
    Conflict conflict = conflictsIterator.next();
    /* Do something with conflict */
}
```

### <a id="read-from-conflict-feed-javascript"></a>SDK de Node.js/JavaScript/TypeScript

```javascript
const container = client
  .database(this.databaseName)
  .container(this.lwwContainerName);

const { result: conflicts } = await container.conflicts.readAll().toArray();
```

### <a id="read-from-conflict-feed-python"></a>Python

```python
conflicts_iterator = iter(client.ReadConflicts(self.manual_collection_link))
conflict = next(conflicts_iterator, None)
while conflict:
    # Do something with conflict
    conflict = next(conflicts_iterator, None)
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os conceitos do Azure Cosmos DB a seguir:

* [Distribuição global – nos bastidores](global-dist-under-the-hood.md)
* [Como configurar vários mestres em seus aplicativos](how-to-multi-master.md)
* [Configurar clientes para multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Como adicionar/remover regiões da conta do Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Como configurar vários mestres nos aplicativos](how-to-multi-master.md).
* [Particionamento e distribuição de dados](partition-data.md)
* [Indexação no Azure Cosmos DB](indexing-policies.md)