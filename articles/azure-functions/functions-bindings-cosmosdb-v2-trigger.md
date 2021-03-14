---
title: Gatilho de Azure Cosmos DB para o Functions 2. x e superior
description: Aprenda a usar o gatilho de Azure Cosmos DB no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 6f4e43efeb1882f52bd335d83a3660a94040ab8a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729208"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x-and-higher"></a>Gatilho de Azure Cosmos DB para Azure Functions 2. x e superior

O Gatilho do Azure Cosmos DB usa o [Feed de Alterações do Azure Cosmos DB](../cosmos-db/change-feed.md) para escutar as inserções e atualizações nas partições. O feed de alteração publica inserções e atualizações, não exclusões.

Para obter informações sobre a instalação e detalhes de configuração, confira a [visão geral](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que é chamada quando há inserções ou atualizações na coleção e no banco de dados especificados.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
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
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O exemplo a seguir mostra uma associação de gatilho do Cosmos DB em um arquivo *function.json* e uma [função script C#](functions-reference-csharp.md) que usa a associação. A função grava mensagens de log quando Cosmos DB registros são adicionados ou modificados.

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
    #r "Microsoft.Azure.DocumentDB.Core"

    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

# <a name="java"></a>[Java](#tab/java)

Essa função é invocada quando há inserções ou atualizações no banco de dados e na coleção especificados.

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


Na biblioteca de runtime de funções [Java](/java/api/overview/azure/functions/runtime), use a anotação `@CosmosDBTrigger` nos parâmetros cujo valor seria proveniente do Cosmos DB.  Essa anotação pode ser usada com tipos nativos do Java, POJOs ou valores que permitem valor nulos usando `Optional<T>`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de gatilho do Cosmos DB em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função grava mensagens de log quando Cosmos DB registros são adicionados ou modificados.

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O exemplo a seguir mostra como executar uma função como alterações de dados no Cosmos DB.

```json
{
  "type": "cosmosDBTrigger",
  "name": "Documents",
  "direction": "in",
  "leaseCollectionName": "leases",
  "connectionStringSetting": "MyStorageConnectionAppSetting",
  "databaseName": "Tasks",
  "collectionName": "Items",
  "createLeaseCollectionIfNotExists": true
}
```

No arquivo _run.ps1_ , você tem acesso ao documento que dispara a função por meio do `$Documents` parâmetro.

```powershell
param($Documents, $TriggerMetadata) 

Write-Host "First document Id modified : $($Documents[0].id)" 
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra uma associação de gatilho do Cosmos DB em um arquivo *function.json* e uma [função Python](functions-reference-python.md) que usa a associação. A função grava mensagens de log quando registros do Cosmos DB são modificados.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Aqui está o código Python:

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Em [bibliotecas de classes de C#](functions-dotnet-class-library.md), utilize o atributo [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs).

O construtor do atributo toma o nome do banco de dados e o nome da coleção. Para obter informações sobre essas configurações e outras propriedades que podem ser configuradas, consulte [Gatilho – configurações](#configuration). Aqui está um exemplo de atributo `CosmosDBTrigger` em uma assinatura de método:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run([CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
        IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

Para obter um exemplo completo, consulte [gatilho](#example).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O script C# não dá suporte a atributos.

# <a name="java"></a>[Java](#tab/java)

Na [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime), use a `@CosmosDBInput` anotação em parâmetros que lêem dados de Cosmos DB.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O JavaScript não dá suporte a atributos.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Não há suporte para atributos pelo PowerShell.

# <a name="python"></a>[Python](#tab/python)

O Python não dá suporte a atributos.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `CosmosDBTrigger`.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como `cosmosDBTrigger`. |
|**direction** | n/d | Deve ser definido como `in`. Esse parâmetro é definido automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | n/d | O nome da variável usado no código de função que representa a lista de documentos com alterações. |
|**connectionStringSetting**|**ConnectionStringSetting** | O nome de uma configuração de aplicativo que contém a cadeia de conexão usada para conectar-se à conta do Azure Cosmos DB que está sendo monitorada. |
|**databaseName**|**DatabaseName**  | O nome do banco de dados do Azure Cosmos DB com a coleção que está sendo monitorada. |
|**collectionName** |**CollectionName** | O nome da coleção que está sendo monitorada. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Adicional O nome de uma configuração de aplicativo que contém a cadeia de conexão para a conta de Azure Cosmos DB que contém a coleção de concessão. Quando não definido, o valor `connectionStringSetting` é usado. Esse parâmetro é definido automaticamente quando a associação é criada no portal. A cadeia de conexão da coleção de concessões deve ter permissões de gravação.|
|**leaseDatabaseName** |**LeaseDatabaseName** | (Opcional) O nome do banco de dados que contém a coleção usada para armazenar as concessões. Quando não definido, o valor da configuração `databaseName` é usado. Esse parâmetro é definido automaticamente quando a associação é criada no portal. |
|**leaseCollectionName** | **LeaseCollectionName** | (Opcional) O nome da coleção usada para armazenar as concessões. Quando não definido, o valor `leases` é usado. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (Opcional) Quando definido como `true`, a coleção de concessões é criada automaticamente quando ela ainda não existe. O valor padrão é `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| Adicional Define o número de unidades de solicitação a serem atribuídas quando a coleção de concessões é criada. Essa configuração é usada apenas quando o `createLeaseCollectionIfNotExists` é definido como `true` . Esse parâmetro é definido automaticamente quando a associação é criada usando o portal.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| Adicional Quando definido, o valor é adicionado como um prefixo às concessões criadas na coleção de concessão para essa função. O uso de um prefixo permite que duas Azure Functions separadas compartilhem a mesma coleção de concessão usando prefixos diferentes.
|**feedPollDelay**| **FeedPollDelay**| Adicional O tempo (em milissegundos) para o atraso entre a sondagem de uma partição para novas alterações no feed, depois que todas as alterações atuais forem descarregadas. O padrão é 5.000 milissegundos ou 5 segundos.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (Opcional) Quando definido, ele define, em milissegundos, o intervalo para disparar uma tarefa para computar se as partições são distribuídas uniformemente entre as instâncias de host conhecidas. O padrão é 13000 (13 segundos).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (Opcional), Quando definido, ele define, em milissegundos, o intervalo para o qual a concessão é tomada em uma concessão que representa uma partição. Se a concessão não for renovada dentro deste intervalo, ela será expirada e a propriedade da partição será movida para outra instância. O padrão é 60000 (60 segundos).
|**leaseRenewInterval**| **LeaseRenewInterval**| (Opcional) Quando definido, ele define, em milissegundos, o intervalo de renovação para todas as concessões para partições atualmente mantidas por uma instância. O padrão é 17000 (17 segundos).
|**checkpointFrequency**| **CheckpointFrequency**| (Opcional) Quando definido, ele define, em milissegundos, o intervalo entre os pontos de verificação de concessão. O padrão é sempre após cada chamada de Função.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Adicional Quando definido, essa propriedade define o número máximo de itens recebidos por chamada de função. Se as operações na coleção monitorada forem executadas por meio de procedimentos armazenados, o [escopo da transação](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) será preservado durante a leitura de itens do feed de alterações. Como resultado, o número de itens recebidos pode ser maior que o valor especificado para que os itens alterados pela mesma transação sejam retornados como parte de um lote atômico.
|**startFromBeginning**| **StartFromBeginning**| Adicional Essa opção informa o gatilho para ler as alterações desde o início do histórico de alterações da coleção, em vez de iniciar na hora atual. A leitura do início só funciona na primeira vez que o gatilho é iniciado, como nas execuções subsequentes, os pontos de verificação já estão armazenados. Definir essa opção como `true` quando houver concessões já criadas não tem efeito. |
|**preferredLocations**| **PreferredLocations**| Adicional Define os locais preferenciais (regiões) para contas de banco de dados replicadas geograficamente no serviço de Azure Cosmos DB. Os valores devem ser separados por vírgulas. Por exemplo, "leste dos EUA, EUA Central do Sul Europa Setentrional". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

O gatilho requer uma segunda coleção que ele usa para armazenar _concessões_ sobre as partições. Tanto a coleção que está sendo monitorada quanto a coleção que contém as concessões devem estar disponíveis para o gatilho funcionar.

>[!IMPORTANT]
> Se várias funções estiverem configuradas para usar um gatilho do Cosmos DB para a mesma coleção, cada uma das funções deverá usar uma coleção de concessões dedicada ou especificar uma diferente `LeaseCollectionPrefix` para cada função. Caso contrário, apenas uma das funções será disparada. Para obter informações sobre o prefixo, veja a [seção de Configuração](#configuration).

O gatilho não indica se um documento foi atualizado ou inserido, ele fornece apenas o documento em si. Se você precisa lidar com inserções e atualizações de forma diferente, você pode fazer isso com a implementação de campos de carimbo de hora de inserção ou atualização.

## <a name="next-steps"></a>Próximas etapas

- [Ler um documento de Azure Cosmos DB (Associação de entrada)](./functions-bindings-cosmosdb-v2-input.md)
- [Salvar alterações em um documento de Azure Cosmos DB (Associação de saída)](./functions-bindings-cosmosdb-v2-output.md)
