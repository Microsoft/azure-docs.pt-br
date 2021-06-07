---
title: Fluxos de alteração na API do Azure Cosmos DB para MongoDB
description: Saiba como usar os fluxos de alteração na API do Azure Cosmos DB para MongoDB para obter as alterações feitas em seus dados.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: rosouz
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 941fe8929b75fdebf187186ca7078b0ae1dd261c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101658513"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Fluxos de alteração na API do Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

O suporte do [feed de alterações](change-feed.md) na API do Azure Cosmos DB para MongoDB está disponível usando a API de fluxos de alteração. Usando a API de fluxos de alteração, seus aplicativos podem obter as alterações feitas na coleção ou nos itens em um único fragmento. Posteriormente, você pode executar ações adicionais com base nos resultados. As alterações nos itens na coleção são capturadas na ordem de seu tempo de modificação e a ordem de classificação é garantida por chave de fragmentação.

> [!NOTE]
> Para usar os fluxos de alteração, crie a API do Azure Cosmos DB para a conta do MongoDB com a versão do servidor 3,6 ou superior. Se você executar os exemplos de fluxo de alterações em uma versão anterior, poderá ver o *nome do estágio de pipeline não reconhecido: $changeStream* erro.

## <a name="examples"></a>Exemplos

O exemplo a seguir mostra como obter fluxos de alteração em todos os itens na coleção. Este exemplo cria um cursor para observar itens quando eles são inseridos, atualizados ou substituídos. O fase `$match`, a fase `$project` e a opção `fullDocument` são necessários para obter os fluxos de alteração. Não há suporte para a observação de operações de exclusão usando fluxos de alteração no momento. Como alternativa, você pode adicionar um marcador flexível nos itens que estão sendo excluídos. Por exemplo, você pode adicionar um atributo no item chamado "deleted". Quando você quiser excluir o item, poderá definir "deleted" como `true` e definir um TTL no item. Como a atualização de "deleted" para `true` é uma atualização, essa alteração será visível no fluxo de alterações.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
var pipeline = new EmptyPipelineDefinition<ChangeStreamDocument<BsonDocument>>()
    .Match(change => change.OperationType == ChangeStreamOperationType.Insert || change.OperationType == ChangeStreamOperationType.Update || change.OperationType == ChangeStreamOperationType.Replace)
    .AppendStage<ChangeStreamDocument<BsonDocument>, ChangeStreamDocument<BsonDocument>, BsonDocument>(
    "{ $project: { '_id': 1, 'fullDocument': 1, 'ns': 1, 'documentKey': 1 }}");

var options = new ChangeStreamOptions{
        FullDocument = ChangeStreamFullDocumentOption.UpdateLookup
    };

var enumerator = coll.Watch(pipeline, options).ToEnumerable().GetEnumerator();

while (enumerator.MoveNext()){
        Console.WriteLine(enumerator.Current);
    }

enumerator.Dispose();
```

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostra como usar a funcionalidade de alteração de fluxo em Java, para o exemplo completo, consulte este [repositório GitHub](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-changestream/blob/main/mongostream/src/main/java/com/azure/cosmos/mongostream/App.java). Este exemplo também mostra como usar o `resumeAfter` método para procurar todas as alterações da última leitura. 

```java
Bson match = Aggregates.match(Filters.in("operationType", asList("update", "replace", "insert")));

// Pick the field you are most interested in
Bson project = Aggregates.project(fields(include("_id", "ns", "documentKey", "fullDocument")));

// This variable is for second example
BsonDocument resumeToken = null;

// Now time to build the pipeline
List<Bson> pipeline = Arrays.asList(match, project);

//#1 Simple example to seek changes

// Create cursor with update_lookup
MongoChangeStreamCursor<ChangeStreamDocument<org.bson.Document>> cursor = collection.watch(pipeline)
        .fullDocument(FullDocument.UPDATE_LOOKUP).cursor();

Document document = new Document("name", "doc-in-step-1-" + Math.random());
collection.insertOne(document);

while (cursor.hasNext()) {
    // There you go, we got the change document.
    ChangeStreamDocument<Document> csDoc = cursor.next();

    // Let is pick the token which will help us resuming
    // You can save this token in any persistent storage and retrieve it later
    resumeToken = csDoc.getResumeToken();
    //Printing the token
    System.out.println(resumeToken);
    
    //Printing the document.
    System.out.println(csDoc.getFullDocument());
    //This break is intentional but in real project feel free to remove it.
    break;
}

cursor.close();

```
---

## <a name="changes-within-a-single-shard"></a>Alterações em um único fragmento

O exemplo a seguir mostra como obter alterações nos itens em um único fragmento. Este exemplo obtém as alterações de itens que têm a chave de fragmento igual a "a" e o valor da chave de fragmento igual a "1". É possível ter diferentes clientes lendo alterações de fragmentos diferentes em paralelo.

```javascript
var cursor = db.coll.watch(
    [
        { 
            $match: { 
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="current-limitations"></a>Limitações atuais

As seguintes limitações são aplicáveis ao usar fluxos de alteração:

* As propriedades `operationType` e `updateDescription` ainda não têm suporte no documento de saída.
* Atualmente, há suporte para os tipos de operações `insert`, `update` e `replace`. No entanto, a operação de exclusão ou outros eventos ainda não têm suporte.

Devido a essas limitações, as opções de fase $match, fase $project e fullDocument são necessárias, conforme mostrado nos exemplos anteriores.

Ao contrário do feed de alterações na API do SQL do Azure Cosmos DB, não há uma [Biblioteca de Processador do Feed de Alterações](change-feed-processor.md) separada para consumir fluxos de alteração ou uma necessidade de um contêiner de concessões. Atualmente, não há suporte para [gatilhos do Azure Functions](change-feed-functions.md) para processamento de fluxos de alteração.

## <a name="error-handling"></a>Tratamento de erros

Há suporte para os seguintes códigos de erro e mensagens ao usar os fluxo de alterações:

* **Código de erro HTTP 16500** – quando o fluxo de alterações é limitado, ele retorna uma página vazia.

* **NamespaceNotFound (OperationType Invalidate)** – se você executar o fluxo de alterações na coleção que não existe ou se a coleção for solta, um erro `NamespaceNotFound` será retornado. Como a propriedade `operationType` não pode ser retornada no documento de saída, em vez do erro `operationType Invalidate`, o erro `NamespaceNotFound` será retornado.

## <a name="next-steps"></a>Próximas etapas

* [Usar a vida útil para expirar os dados automaticamente na API do Azure Cosmos DB para MongoDB](mongodb-time-to-live.md)
* [Indexação na API do Azure Cosmos DB para MongoDB](mongodb-indexing.md)
