---
title: Associações do Azure Cosmos DB para Functions 1.x
description: Entenda como usar gatilhos e associações do Azure Cosmos DB no Azure Functions.
services: functions
author: craigshoemaker
ms.author: cshoe
manager: jeconnoc
keywords: azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.topic: reference
ms.date: 11/21/2017
ms.custom: seodec18
ms.openlocfilehash: 0421ec62d25bbfaba2909d16498cac5afd038a53
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60737271"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-1x"></a>Associações do Azure Cosmos DB para Azure Functions 1.x

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [Versão 1](functions-bindings-cosmosdb.md)
> * [Versão 2](functions-bindings-cosmosdb-v2.md)

Este artigo explica como trabalhar com associações do [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) no Azure Functions. O Azure Functions dá suporte a associações de gatilho, entrada e saída para o Azure Cosmos DB.

> [!NOTE]
> Este artigo serve para o Azure Functions 1.x. Para obter informações sobre como usar essas associações em Functions 2.x, veja [Associações do Azure Cosmos DB para Azure Functions 2.x](functions-bindings-cosmosdb-v2.md).
>
>Essa associação era originalmente denominada DocumentDB. No Functions versão 1.x, apenas o gatilho foi renomeado Cosmos DB; a associação de entrada, a associação de saída e o pacote NuGet mantêm o nome DocumentDB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> As associações do Azure Cosmos DB têm suporte apenas para usar com a API do SQL. Para todas as outras APIs do Azure Cosmos DB, você deve acessar o banco de dados por meio da sua função usando o cliente estático da API, incluindo a [API do Azure Cosmos DB para MongoDB](../cosmos-db/mongodb-introduction.md), a [API do Cassandra](../cosmos-db/cassandra-introduction.md), a [API do Gremlin](../cosmos-db/graph-introduction.md) e a [API de Tabela](../cosmos-db/table-introduction.md).

## <a name="packages---functions-1x"></a>Pacotes - Functions 1. x

As associações do Azure Cosmos DB para Functions versão 1.x são fornecidas no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB), versão 1.x. O código-fonte para a associação está no repositório GitHub [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.DocumentDB).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="trigger"></a>Gatilho

O Gatilho do Azure Cosmos DB usa o [Feed de Alterações do Azure Cosmos DB](../cosmos-db/change-feed.md) para escutar as inserções e atualizações nas partições. O feed de alteração publica inserções e atualizações, não exclusões.

## <a name="trigger---example"></a>Gatilho - exemplo

Consulte o exemplo específico a um idioma:

* [C#](#trigger---c-example)
* [Script do C# (.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

[Ignorar exemplos de gatilho](#trigger---attributes)

### <a name="trigger---c-example"></a>Gatilho - exemplo C#

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que é chamada quando há inserções ou atualizações na coleção e no banco de dados especificados.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;

namespace CosmosDBSamplesV1
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            TraceWriter log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.Info($"Documents modified: {documents.Count}");
                log.Info($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

[Ignorar exemplos de gatilho](#trigger---attributes)

### <a name="trigger---c-script-example"></a>Gatilho - exemplo de script C#

O exemplo a seguir mostra uma associação de gatilho do Cosmos DB em um arquivo *function.json* e uma [função script C#](functions-reference-csharp.md) que usa a associação. A função grava mensagens de log quando registros do Cosmos DB são modificados.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Aqui está o código de script do C#:

```cs
    #r "Microsoft.Azure.Documents.Client"
    
    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    

    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Info("Documents modified " + documents.Count);
        log.Info("First document Id " + documents[0].Id);
    }
```

[Ignorar exemplos de gatilho](#trigger---attributes)

### <a name="trigger---javascript-example"></a>Gatilho - exemplo de JavaScript

O exemplo a seguir mostra uma associação de gatilho do Cosmos DB em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função grava mensagens de log quando registros do Cosmos DB são modificados.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Aqui está o código JavaScript:

```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

## <a name="trigger---attributes"></a>Gatilho – atributos

Em [bibliotecas de classes de C#](functions-dotnet-class-library.md), utilize o atributo [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs).

O construtor do atributo toma o nome do banco de dados e o nome da coleção. Para obter informações sobre essas configurações e outras propriedades que podem ser configuradas, consulte [Gatilho – configurações](#trigger---configuration). Aqui está um exemplo de atributo `CosmosDBTrigger` em uma assinatura de método:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        TraceWriter log)
    {
        ...
    }
```

Para ver um exemplo completo, consulte [Gatilho – exemplo de C#](#trigger---c-example).

## <a name="trigger---configuration"></a>Gatilho – configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `CosmosDBTrigger`.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**tipo** || Deve ser definido como `cosmosDBTrigger`. |
|**direction** || Deve ser definido como `in`. Esse parâmetro é definido automaticamente quando você cria o gatilho no portal do Azure. |
|**name** || O nome da variável usado no código de função que representa a lista de documentos com alterações. |
|**connectionStringSetting**|**ConnectionStringSetting** | O nome de uma configuração de aplicativo que contém a cadeia de conexão usada para conectar-se à conta do Azure Cosmos DB que está sendo monitorada. |
|**databaseName**|**DatabaseName**  | O nome do banco de dados do Azure Cosmos DB com a coleção que está sendo monitorada. |
|**collectionName** |**CollectionName** | O nome da coleção que está sendo monitorada. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (Opcional) O nome de uma configuração de aplicativo que contém a cadeia de conexão para o serviço com a coleção de concessão. Quando não definido, o valor `connectionStringSetting` é usado. Esse parâmetro é definido automaticamente quando a associação é criada no portal. A cadeia de conexão da coleção de concessões deve ter permissões de gravação.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Opcional) O nome do banco de dados que contém a coleção usada para armazenar as concessões. Quando não definido, o valor da configuração `databaseName` é usado. Esse parâmetro é definido automaticamente quando a associação é criada no portal. |
|**leaseCollectionName** | **LeaseCollectionName** | (Opcional) O nome da coleção usada para armazenar as concessões. Quando não definido, o valor `leases` é usado. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Opcional) Quando definido como `true`, a coleção de concessões é criada automaticamente quando ela ainda não existe. O valor padrão é `false`. |
|**leasesCollectionThroughput**| **leasesCollectionThroughput**| (Opcional) Define a quantidade de Unidades de Solicitação a atribuir quando a coleção de concessões for criada. Essa configuração é usada apenas quando `createLeaseCollectionIfNotExists` é definido como `true`. Esse parâmetro é definido automaticamente quando a associação é criada usando o portal.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| (Opcional) Quando definido, ele adiciona um prefixo às concessões criadas na coleção de Concessão para esta Função, permitindo efetivamente duas funções do Azure Functions separadas para compartilhar a mesma coleção de Concessão usando prefixos diferentes.
|**feedPollDelay**| **FeedPollDelay**| (Opcional) Quando definido, ele define, em milissegundos, o atraso entre a sondagem de uma partição quanto a novas alterações no feed, depois que todas as alterações atuais forem descarregadas. O padrão é 5000 (5 segundos).
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Opcional) Quando definido, ele define, em milissegundos, o intervalo para disparar uma tarefa para computar se as partições são distribuídas uniformemente entre as instâncias de host conhecidas. O padrão é 13000 (13 segundos).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Opcional), Quando definido, ele define, em milissegundos, o intervalo para o qual a concessão é tomada em uma concessão que representa uma partição. Se a concessão não for renovada dentro deste intervalo, ela será expirada e a propriedade da partição será movida para outra instância. O padrão é 60000 (60 segundos).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Opcional) Quando definido, ele define, em milissegundos, o intervalo de renovação para todas as concessões para partições atualmente mantidas por uma instância. O padrão é 17000 (17 segundos).
|**checkpointFrequency**| **CheckpointFrequency**| (Opcional) Quando definido, ele define, em milissegundos, o intervalo entre os pontos de verificação de concessão. O padrão é sempre após cada chamada de Função.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| (Opcional) Quando definido, ele personaliza a quantidade máxima de itens recebidos por chamada de Função.
|**startFromBeginning**| **startFromBeginning**| Quando definido, ele informa o gatilho para iniciar a leitura de alterações desde o início do histórico de coleção em vez da hora atual. Isso funciona apenas na primeira vez em que gatilho inicia, como em execuções subsequentes, os pontos de verificação já estão armazenados. Definir isso como `true` quando houver concessões já criadas não tem nenhum efeito.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Gatilho - uso

O gatilho requer uma segunda coleção que ele usa para armazenar _concessões_ sobre as partições. Tanto a coleção que está sendo monitorada quanto a coleção que contém as concessões devem estar disponíveis para o gatilho funcionar.

>[!IMPORTANT]
> Se várias funções estiverem configuradas para usar um gatilho do Cosmos DB para a mesma coleção, cada uma das funções deverá usar uma coleção de concessões dedicada ou especificar uma diferente `LeaseCollectionPrefix` para cada função. Caso contrário, apenas uma das funções será disparada. Para obter informações sobre o prefixo, veja a [seção de Configuração](#trigger---configuration).

O gatilho não indica se um documento foi atualizado ou inserido, ele fornece apenas o documento em si. Se você precisa lidar com inserções e atualizações de forma diferente, você pode fazer isso com a implementação de campos de carimbo de hora de inserção ou atualização.

## <a name="input"></a>Entrada

A associação de dados de entrada do Azure Cosmos DB usa a API de SQL para recuperar um ou mais documentos do Azure Cosmos DB e passá-los para o parâmetro de entrada da função. A ID do documento ou os parâmetros de consulta podem ser determinados com base no gatilho que invoca a função.

## <a name="input---examples"></a>Entrada – exemplos

Veja os exemplos específicos a um idioma que lê um documento especificando um valor de ID:

* [C#](#input---c-examples)
* [Script do C# (.csx)](#input---c-script-examples)
* [JavaScript](#input---javascript-examples)
* [F#](#input---f-examples)

[Ignorar exemplos de entrada](#input---attributes)

### <a name="input---c-examples"></a>Entrada – exemplos em C#

Esta seção contém os seguintes exemplos:

* [Gatilho da fila, pesquisar ID no JSON](#queue-trigger-look-up-id-from-json-c)
* [Gatilho HTTP, pesquisar ID na cadeia de caracteres de consulta](#http-trigger-look-up-id-from-query-string-c)
* [Gatilho HTTP, pesquisar ID nos dados da rota](#http-trigger-look-up-id-from-route-data-c)
* [Gatilho HTTP, pesquisar ID nos dados da rota, usando SqlQuery](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [Gatilho HTTP, obter vários documentos, usando SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [Gatilho HTTP, obter vários documentos, usando DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c)

Os exemplos se referem a um tipo `ToDoItem` simples:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-c"></a>Gatilho da fila, pesquisar ID no JSON (C#)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera um único documento. A função é disparada por uma mensagem da fila que contém um objeto JSON. O gatilho da fila analisa o JSON em um objeto chamado `ToDoItemLookup`, que contém a ID a pesquisar. Essa ID é usada para recuperar um documento `ToDoItem` no banco de dados e na coleção especificados.

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}")]ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId}");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c"></a>Gatilho HTTP, pesquisar ID na cadeia de caracteres de consulta (C#)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera um único documento. A função é disparada por uma solicitação HTTP que usa uma cadeia de caracteres de consulta para especificar a ID a pesquisar. Essa ID é usada para recuperar um documento `ToDoItem` no banco de dados e na coleção especificados.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c"></a>Gatilho HTTP, pesquisar ID nos dados da rota (C#)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera um único documento. A função é disparada por uma solicitação HTTP que usa os dados da rota para especificar a ID a pesquisar. Essa ID é usada para recuperar um documento `ToDoItem` no banco de dados e na coleção especificados.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static HttpResponseMessage Run(
            [HttpTrigger(
                AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}")] ToDoItem toDoItem,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.Info($"ToDo item not found");
            }
            else
            {
                log.Info($"Found ToDo item, Description={toDoItem.Description}");
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>Gatilho HTTP, pesquisar ID nos dados da rota, usando SqlQuery (C#)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera um único documento. A função é disparada por uma solicitação HTTP que usa os dados da rota para especificar a ID a pesquisar. Essa ID é usada para recuperar um documento `ToDoItem` no banco de dados e na coleção especificados.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")] IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c"></a>Gatilho HTTP, obter vários documentos, usando SqlQuery (C#)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera uma lista de documentos. A função é disparada por uma solicitação HTTP. A consulta é especificada na propriedade do atributo `SqlQuery`.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;

namespace CosmosDBSamplesV1
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static HttpResponseMessage Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.Info(toDoItem.Description);
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c"></a>Gatilho HTTP, obter vários documentos, usando DocumentClient (C#)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que recupera uma lista de documentos. A função é disparada por uma solicitação HTTP. O código usa uma instância `DocumentClient` fornecida pela associação do Azure Cosmos DB para ler uma lista de documentos. A instância `DocumentClient` também pode ser usada para as operações de gravação.

```cs
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System;
using System.Linq;
using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
            string searchterm = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
                .Value;

            if (searchterm == null)
            {
                return req.CreateResponse(HttpStatusCode.NotFound);
            }

            log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.Info(result.Description);
                }
            }
            return req.CreateResponse(HttpStatusCode.OK);
        }
    }
}
```

[Ignorar exemplos de entrada](#input---attributes)

### <a name="input---c-script-examples"></a>Entrada – exemplos de script C#

Esta seção contém os seguintes exemplos:

* [Gatilho da fila, pesquisar ID na cadeia de caracteres](#queue-trigger-look-up-id-from-string-c-script)
* [Gatilho da fila, obter vários documentos, usando SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Gatilho HTTP, pesquisar ID na cadeia de caracteres de consulta](#http-trigger-look-up-id-from-query-string-c-script)
* [Gatilho HTTP, pesquisar ID nos dados da rota](#http-trigger-look-up-id-from-route-data-c-script)
* [Gatilho HTTP, obter vários documentos, usando SqlQuery](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [Gatilho HTTP, obter vários documentos, usando DocumentClient](#http-trigger-get-multiple-docs-using-documentclient-c-script)

Os exemplos de gatilho HTTP se referem a um tipo `ToDoItem` simples:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-string-c-script"></a>Gatilho da fila, pesquisar ID na cadeia de caracteres (script C#)

O exemplo a seguir mostra uma associação de entrada do Cosmos DB em um arquivo *function.json* e uma [função script C#](functions-reference-csharp.md) que usa a associação. A função lê um documento único e atualiza o valor de texto do documento.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
}
```
A seção [configuração](#input---configuration) explica essas propriedades.

Aqui está o código de script do C#:

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
        inputDocument.text = "This has changed.";
    }
```

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>Gatilho da fila, obter vários documentos, usando SqlQuery (script C#)

O exemplo a seguir mostra uma associação de dados de entrada do Cosmos DB em um arquivo *function.json* e uma [função script C#](functions-reference-csharp.md) que usa a associação de dados. A função recupera vários documentos especificados por uma consulta SQL usando um gatilho de fila para personalizar os parâmetros de consulta.

O gatilho de consulta fornece um parâmetro `departmentId`. Uma mensagem de consulta de `{ "departmentId" : "Finance" }` retornará todos os registros ao departamento financeiro.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

A seção [configuração](#input---configuration) explica essas propriedades.

Aqui está o código de script do C#:

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {
        foreach (var doc in documents)
        {
            // operate on each document
        }
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-c-script"></a>Gatilho HTTP, pesquisar ID na cadeia de caracteres de consulta (script C#)

O exemplo a seguir mostra uma [função de script C#](functions-reference-csharp.md) que recupera um único documento. A função é disparada por uma solicitação HTTP que usa uma cadeia de caracteres de consulta para especificar a ID a pesquisar. Essa ID é usada para recuperar um documento `ToDoItem` no banco de dados e na coleção especificados.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

Aqui está o código de script do C#:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-c-script"></a>Gatilho HTTP, pesquisar ID nos dados da rota (script C#)

O exemplo a seguir mostra uma [função de script C#](functions-reference-csharp.md) que recupera um único documento. A função é disparada por uma solicitação HTTP que usa os dados da rota para especificar a ID a pesquisar. Essa ID é usada para recuperar um documento `ToDoItem` no banco de dados e na coleção especificados.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Aqui está o código de script do C#:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
        log.Info($"ToDo item not found");
    }
    else
    {
        log.Info($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>Gatilho HTTP, obter vários documentos, usando SqlQuery (script C#)

O exemplo a seguir mostra uma [função de script C#](functions-reference-csharp.md) que recupera uma lista de documentos. A função é disparada por uma solicitação HTTP. A consulta é especificada na propriedade do atributo `SqlQuery`.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

Aqui está o código de script do C#:

```cs
using System.Net;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.Info(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>Gatilho HTTP, obter vários documentos, usando DocumentClient (script C#)

O exemplo a seguir mostra uma [função de script C#](functions-reference-csharp.md) que recupera uma lista de documentos. A função é disparada por uma solicitação HTTP. O código usa uma instância `DocumentClient` fornecida pela associação do Azure Cosmos DB para ler uma lista de documentos. A instância `DocumentClient` também pode ser usada para as operações de gravação.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

Aqui está o código de script do C#:

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.Info($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.Info(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

[Ignorar exemplos de entrada](#input---attributes)

### <a name="input---javascript-examples"></a>Entrada – exemplos de JavaScript

Esta seção contém os seguintes exemplos:

* [Gatilho da fila, pesquisar ID no JSON](#queue-trigger-look-up-id-from-json-javascript)
* [Gatilho HTTP, pesquisar ID na cadeia de caracteres de consulta](#http-trigger-look-up-id-from-query-string-javascript)
* [Gatilho HTTP, pesquisar ID nos dados da rota](#http-trigger-look-up-id-from-route-data-javascript)
* [Gatilho da fila, obter vários documentos, usando SqlQuery](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="queue-trigger-look-up-id-from-json-javascript"></a>Gatilho da fila, pesquisar ID no JSON (JavaScript)

O exemplo a seguir mostra uma associação de entrada do Cosmos DB em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função lê um documento único e atualiza o valor de texto do documento.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "name": "inputDocumentIn",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```
A seção [configuração](#input---configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-look-up-id-from-query-string-javascript"></a>Gatilho HTTP, pesquisar ID na cadeia de caracteres de consulta (JavaScript)

O exemplo a seguir mostra uma [função de script JavaScript](functions-reference-node.md) que recupera um único documento. A função é disparada por uma solicitação HTTP que usa uma cadeia de caracteres de consulta para especificar a ID a pesquisar. Essa ID é usada para recuperar um documento `ToDoItem` no banco de dados e na coleção especificados.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}"
    }
  ],
  "disabled": true
}
```

Aqui está o código JavaScript:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

[Ignorar exemplos de entrada](#input---attributes)

#### <a name="http-trigger-look-up-id-from-route-data-javascript"></a>Gatilho HTTP, pesquisar ID nos dados da rota (JavaScript)

O exemplo a seguir mostra uma [função de script JavaScript](functions-reference-node.md) que recupera um único documento. A função é disparada por uma solicitação HTTP que usa uma cadeia de caracteres de consulta para especificar a ID a pesquisar. Essa ID é usada para recuperar um documento `ToDoItem` no banco de dados e na coleção especificados.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "documentDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}"
    }
  ],
  "disabled": false
}
```

Aqui está o código JavaScript:

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

[Ignorar exemplos de entrada](#input---attributes)



#### <a name="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>Gatilho da fila, obter vários documentos, usando SqlQuery (script javaScript)

O exemplo a seguir mostra uma associação de dados de entrada do Cosmos DB em um arquivo *function.json* e uma [função script C#](functions-reference-node.md) que usa a associação de dados. A função recupera vários documentos especificados por uma consulta SQL usando um gatilho de fila para personalizar os parâmetros de consulta.

O gatilho de consulta fornece um parâmetro `departmentId`. Uma mensagem de consulta de `{ "departmentId" : "Finance" }` retornará todos os registros ao departamento financeiro.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connection": "CosmosDBConnection"
}
```

A seção [configuração](#input---configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
    module.exports = function (context, input) {
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }
        context.done();
    };
```

[Ignorar exemplos de entrada](#input---attributes)

<a name="infsharp"></a>

### <a name="input---f-examples"></a>Entrada – exemplos de F#

O exemplo a seguir mostra uma associação de entrada do Cosmos DB em um arquivo *function.json* e uma [função F#](functions-reference-fsharp.md) que usa a associação. A função lê um documento único e atualiza o valor de texto do documento.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "name": "inputDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "connection": "MyAccount_COSMOSDB",
    "direction": "in"
}
```

A seção [configuração](#input---configuration) explica essas propriedades.

O código F# é o seguinte:

```fsharp
    (* Change input document contents using Azure Cosmos DB input binding *)
    open FSharp.Interop.Dynamic
    let Run(myQueueItem: string, inputDocument: obj) =
    inputDocument?text <- "This has changed."
```

Esse exemplo requer um arquivo `project.json` que especifique as dependências `FSharp.Interop.Dynamic` e `Dynamitey` do NuGet:

```json
{
    "frameworks": {
        "net46": {
            "dependencies": {
                "Dynamitey": "1.0.2",
                "FSharp.Interop.Dynamic": "3.0.0"
            }
        }
    }
}
```

Para adicionar um arquivo do `project.json`, veja [Gerenciamento de pacotes do F#](functions-reference-fsharp.md#package).

## <a name="input---attributes"></a>Entrada – atributos

Em [bibliotecas de classes de C#](functions-dotnet-class-library.md), utilize o atributo [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs).

O construtor do atributo toma o nome do banco de dados e o nome da coleção. Para obter informações sobre essas configurações e outras propriedades que podem ser configuradas, consulte [a seção de configuração a seguir](#input---configuration).

## <a name="input---configuration"></a>Entrada - configuração

A tabela a seguir explica as propriedades de configuração de associação que você definir no arquivo *function.json* e o `DocumentDB` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**tipo**     || Deve ser definido como `documentdb`.        |
|**direction**     || Deve ser definido como `in`.         |
|**name**     || Nome do parâmetro de associação que representa o documento na função.  |
|**databaseName** |**DatabaseName** |O banco de dados que contém o documento.        |
|**collectionName** |**CollectionName** | O nome da coleção que contém o documento. |
|**ID**    | **Id** | A ID do documento a ser recuperado. Essa propriedade dá suporte a [expressões de associação](./functions-bindings-expressions-patterns.md). Não defina ambas as propriedades **id** e **sqlQuery**. Se você não definir uma ou outra, toda a coleção é recuperada. |
|**sqlQuery**  |**SqlQuery**  | Uma consulta SQL do Azure Cosmos DB usada para recuperar vários documentos. A propriedade dá suporte a associações de tempo de execução, como neste exemplo: `SELECT * FROM c where c.departmentId = {departmentId}`. Não defina ambas as propriedades **id** e **sqlQuery**. Se você não definir uma ou outra, toda a coleção é recuperada.|
|**conexão**     |**ConnectionStringSetting**|O nome da configuração do aplicativo que contém a cadeia de conexão do Azure Cosmos DB.        |
|**partitionKey**|**PartitionKey**|Especifica o valor da chave de partição para a pesquisa. Pode incluir parâmetros de associação.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Entrada - uso

Em funções C# e F#, todas as alterações feitas no documento de entrada por parâmetros de entrada nomeados são persistidas automaticamente.

Funções de JavaScript, as atualizações não são feitas automaticamente após a saída da função. Em vez disso, use `context.bindings.<documentName>In` e `context.bindings.<documentName>Out` para fazer atualizações. Consulte o [exemplo JavaScript](#input---javascript-examples).

## <a name="output"></a>Saída

Com a associação de saída do Azure Cosmos DB, você pode gravar um novo documento para um banco de dados do Azure Cosmos DB usando a API de SQL.

## <a name="output---examples"></a>Saída – exemplos

Consulte os exemplos específicos a um idioma:

* [C#](#output---c-examples)
* [Script do C# (.csx)](#output---c-script-examples)
* [JavaScript](#output---javascript-examples)
* [F#](#output---f-examples)

Veja também o [exemplo de entrada](#input---c-examples) que usa `DocumentClient`.

[Ignorar exemplos de saída](#output---attributes)

### <a name="output---c-examples"></a>Saída - exemplos C#

Esta seção contém os seguintes exemplos:

* Gatilho da fila, gravar um documento
* Gatilho da fila, gravar documentos usando IAsyncCollector

Os exemplos se referem a um tipo `ToDoItem` simples:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

[Ignorar exemplos de saída](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c"></a>Gatilho da fila, gravar um documento (C#)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que adiciona um documento a um banco de dados, usando os dados fornecidos na mensagem do armazenamento de fila.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System;

namespace CosmosDBSamplesV1
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            TraceWriter log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.Info($"C# Queue trigger function inserted one row");
            log.Info($"Description={queueMessage}");
        }
    }
}
```

[Ignorar exemplos de saída](#output---attributes)

#### <a name="queue-trigger-write-docs-using-iasynccollector-c"></a>Gatilho da fila, gravar documentos usando IAsyncCollector (C#)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que adiciona uma coleção de documentos a um banco de dados, usando os dados fornecidos em um JSON de mensagem da fila.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;

namespace CosmosDBSamplesV1
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [DocumentDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            TraceWriter log)
        {
            log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.Info($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

[Ignorar exemplos de saída](#output---attributes)

### <a name="output---c-script-examples"></a>Saída – exemplos de script C#

Esta seção contém os seguintes exemplos:

* Gatilho da fila, gravar um documento
* Gatilho da fila, gravar documentos usando IAsyncCollector

[Ignorar exemplos de saída](#output---attributes)

#### <a name="queue-trigger-write-one-doc-c-script"></a>Gatilho da fila, gravar um documento (script C#)

O exemplo a seguir mostra uma associação de saída do Azure Cosmos DB em um arquivo *function.json* e uma [função de script C#](functions-reference-csharp.md) que usa a associação. A função usa uma associação de entrada de fila para uma fila que recebe o JSON no seguinte formato:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A função cria documentos do Azure Cosmos DB no formato a seguir para cada registro:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A seção [configuração](#output---configuration) explica essas propriedades.

Aqui está o código de script do C#:

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");

        dynamic employee = JObject.Parse(myQueueItem);

        employeeDocument = new {
            id = employee.name + "-" + employee.employeeId,
            name = employee.name,
            employeeId = employee.employeeId,
            address = employee.address
        };
    }
```

#### <a name="queue-trigger-write-docs-using-iasynccollector"></a>Gatilho da fila, gravar documentos usando IAsyncCollector

Para criar vários documentos, você também pode associar a `ICollector<T>` ou `IAsyncCollector<T>`, sendo `T` um dos tipos com suporte.

Este exemplo se refere a um tipo `ToDoItem` simples:

```cs
namespace CosmosDBSamplesV1
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

Aqui está o arquivo function.json:

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connection": "AzureWebJobsStorage"
    },
    {
      "type": "documentDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Aqui está o código de script do C#:

```cs
using System;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.Info($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

[Ignorar exemplos de saída](#output---attributes)

### <a name="output---javascript-examples"></a>Saída – exemplos de JavaScript

O exemplo a seguir mostra uma associação de saída do Azure Cosmos DB em um arquivo *function.json* e uma [função de script C#](functions-reference-node.md) que usa a associação. A função usa uma associação de entrada de fila para uma fila que recebe o JSON no seguinte formato:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A função cria documentos do Azure Cosmos DB no formato a seguir para cada registro:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

A seção [configuração](#output---configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

[Ignorar exemplos de saída](#output---attributes)

### <a name="output---f-examples"></a>Saída – exemplos de F#

O exemplo a seguir mostra uma associação de saída do Azure Cosmos DB em um arquivo *function.json* e uma [função de script C#](functions-reference-fsharp.md) que usa a associação. A função usa uma associação de entrada de fila para uma fila que recebe o JSON no seguinte formato:

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

A função cria documentos do Azure Cosmos DB no formato a seguir para cada registro:

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "name": "employeeDocument",
    "type": "documentDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connection": "MyAccount_COSMOSDB",
    "direction": "out"
}
```
A seção [configuração](#output---configuration) explica essas propriedades.

O código F# é o seguinte:

```fsharp
    open FSharp.Interop.Dynamic
    open Newtonsoft.Json

    type Employee = {
      id: string
      name: string
      employeeId: string
      address: string
    }

    let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
      log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
      let employee = JObject.Parse(myQueueItem)
      employeeDocument <-
        { id = sprintf "%s-%s" employee?name employee?employeeId
          name = employee?name
          employeeId = employee?employeeId
          address = employee?address }
```

Esse exemplo requer um arquivo `project.json` que especifique as dependências `FSharp.Interop.Dynamic` e `Dynamitey` do NuGet:

```json
{
    "frameworks": {
        "net46": {
          "dependencies": {
            "Dynamitey": "1.0.2",
            "FSharp.Interop.Dynamic": "3.0.0"
           }
        }
    }
}
```

Para adicionar um arquivo do `project.json`, veja [Gerenciamento de pacotes do F#](functions-reference-fsharp.md#package).

## <a name="output---attributes"></a>Saída - atributos

Em [bibliotecas de classes de C#](functions-dotnet-class-library.md), utilize o atributo [DocumentDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs).

O construtor do atributo toma o nome do banco de dados e o nome da coleção. Para obter informações sobre essas configurações e outras propriedades que podem ser configuradas, consulte [Saída – configurações](#output---configuration). Aqui está um exemplo de atributo `DocumentDB` em uma assinatura de método:

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [DocumentDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

Para ver um exemplo completo, consulte [Saída – exemplo de C#](#output---c-examples).

## <a name="output---configuration"></a>Saída - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no `DocumentDB` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**tipo**     || Deve ser definido como `documentdb`.        |
|**direction**     || Deve ser definido como `out`.         |
|**name**     || Nome do parâmetro de associação que representa o documento na função.  |
|**databaseName** | **DatabaseName**|O banco de dados que contém a coleção na qual o documento será criado.     |
|**collectionName** |**CollectionName**  | O nome da coleção na qual o documento será criado. |
|**createIfNotExists**  |**CreateIfNotExists**    | É um valor booliano para indicar se a coleção será criada quando não existir. O padrão é *false* porque as novas coleções são criadas com a taxa de transferência reservada, o que tem implicações de preço. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**partitionKey**|**PartitionKey** |Quando `CreateIfNotExists` for true, define o caminho da chave de partição para a coleção criada.|
|**CollectionThroughput**|**CollectionThroughput**| Quando `CreateIfNotExists` for true, define a [taxa de transferência](../cosmos-db/set-throughput.md) da coleção criada.|
|**conexão**    |**ConnectionStringSetting** |O nome da configuração do aplicativo que contém a cadeia de conexão do Azure Cosmos DB.        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - uso

Por padrão, quando você grava no parâmetro de saída em sua função, um documento é criado no banco de dados. Este documento contém um GUID gerado automaticamente como a ID do documento. Você pode especificar a ID do documento de saída especificando a propriedade `id` no objeto JSON enviado ao parâmetro de saída.

> [!Note]
> Ao especificar a ID de um documento existente, ela é substituída pelo novo documento de saída.

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Associação | Referência |
|---|---|
| CosmosDB | [Códigos de erro CosmosDB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre a computação de banco de dados sem servidor com o Cosmos DB](../cosmos-db/serverless-computing-database.md)
* [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
