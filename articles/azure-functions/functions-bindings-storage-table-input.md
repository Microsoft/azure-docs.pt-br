---
title: Associações de entrada de armazenamento de tabela do Azure para Azure Functions
description: Entenda como usar as associações de entrada do armazenamento de tabelas do Azure no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4fc2426189384856d2d2e95887cdabd2f9e9ebea
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98033771"
---
# <a name="azure-table-storage-input-bindings-for-azure-functions"></a>Associações de entrada de armazenamento de tabela do Azure para Azure Functions

Use a associação de entrada de armazenamento de Tabela do Azure para ler uma tabela em uma conta de Armazenamento do Azure.

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>Uma entidade

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que lê uma linha da tabela. Para cada mensagem enviada para a fila, a função será disparada.

O valor de chave de linha “{queueTrigger}” indica que a chave de linha foi obtida da cadeia de caracteres da mensagem da fila.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="cloudtable"></a>CloudTable

`CloudTable` Só tem suporte nas [funções V2 e e em tempos de execução mais altos](functions-versions.md).

Use um `CloudTable` parâmetro de método para ler a tabela usando o SDK de armazenamento do Azure. Veja um exemplo de uma função que consulta uma tabela de log de Azure Functions:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

Para obter mais informações sobre como usar o CloudTable, consulte [Introdução ao Armazenamento de Tabelas do Azure](../cosmos-db/tutorial-develop-table-dotnet.md).

Se você tentar associar `CloudTable` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

### <a name="iqueryable"></a>IQueryable

`IQueryable` Só tem suporte no [tempo de execução do Functions v1](functions-versions.md).

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que lê várias linhas de tabela `MyPoco` das quais a classe deriva `TableEntity` .

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

### <a name="one-entity"></a>Uma entidade

O exemplo a seguir mostra uma associação de entrada de tabela em um arquivo *function.json* e código [script C#](functions-reference-csharp.md) que usa a associação. A função usa um gatilho de fila para ler uma linha da tabela. 

O arquivo *function.json* especifica um `partitionKey` e um `rowKey`. O `rowKey` valor “{queueTrigger}” indica que a chave de linha foi obtida da cadeia de caracteres da mensagem da fila.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código de script do C#:

```csharp
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable` Não tem suporte no tempo de execução do Functions para [as versões 2. x e superior)](functions-versions.md). Uma alternativa é usar um `CloudTable` parâmetro do método para ler a tabela usando o SDK de Armazenamento do Azure. Veja um exemplo de uma função que consulta uma tabela de log de Azure Functions:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

Para obter mais informações sobre como usar o CloudTable, consulte [Introdução ao Armazenamento de Tabelas do Azure](../cosmos-db/tutorial-develop-table-dotnet.md).

Se você tentar associar `CloudTable` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

### <a name="iqueryable"></a>IQueryable

O exemplo a seguir mostra uma associação de entrada de tabela em um arquivo *function.json* e código [script C#](functions-reference-csharp.md) que usa a associação. A função lê entidades para uma chave de partição especificada em uma mensagem da fila.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

A seção [configuração](#configuration) explica essas propriedades.

O código script C# adiciona uma referência ao SDK de Armazenamento do Azure para que o tipo de entidade possa derivar de `TableEntity`:

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostra uma função disparada por HTTP que retorna uma lista de objetos Person que estão em uma partição especificada no armazenamento de tabela. No exemplo, a chave de partição é extraída da rota http e o TableName e a conexão são das configurações de função. 

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() { return this.PartitionKey; }
    public void setPartitionKey(String key) { this.PartitionKey = key; }
    public String getRowKey() { return this.RowKey; }
    public void setRowKey(String key) { this.RowKey = key; }
    public String getName() { return this.Name; }
    public void setName(String name) { this.Name = name; }
}

@FunctionName("getPersonsByPartitionKey")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}") HttpRequestMessage<Optional<String>> request,
        @BindingName("partitionKey") String partitionKey,
        @TableInput(name="persons", partitionKey="{partitionKey}", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with partition key: " + partitionKey);

    return persons;
}
```

A anotação TableInput também pode extrair as associações do corpo JSON da solicitação, como mostra o exemplo a seguir.

```java
@FunctionName("GetPersonsByKeysFromRequest")
public HttpResponseMessage get(
        @HttpTrigger(name = "getPerson", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="query") HttpRequestMessage<Optional<String>> request,
        @TableInput(name="persons", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") Person person,
        final ExecutionContext context) {

    if (person == null) {
        return request.createResponseBuilder(HttpStatus.NOT_FOUND)
                    .body("Person not found.")
                    .build();
    }

    return request.createResponseBuilder(HttpStatus.OK)
                    .header("Content-Type", "application/json")
                    .body(person)
                    .build();
}
```

O exemplo a seguir usa o filtro para consultar pessoas com um nome específico em uma tabela do Azure e limita o número de possíveis correspondências a 10 resultados.

```java
@FunctionName("getPersonsByName")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="filter/{name}") HttpRequestMessage<Optional<String>> request,
        @BindingName("name") String name,
        @TableInput(name="persons", filter="Name eq '{name}'", take = "10", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with name: " + name);

    return persons;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de entrada de tabela em um arquivo *function.json* e o código [JavaScript](functions-reference-node.md) que usa a associação. A função usa um gatilho de fila para ler uma linha da tabela. 

O arquivo *function.json* especifica um `partitionKey` e um `rowKey`. O `rowKey` valor “{queueTrigger}” indica que a chave de linha foi obtida da cadeia de caracteres da mensagem da fila.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A função a seguir usa um gatilho de fila para ler uma única linha de tabela como entrada para uma função.

Neste exemplo, a configuração de associação especifica um valor explícito para a tabela `partitionKey` e usa uma expressão para passar para o `rowKey` . A `rowKey` expressão, `{queueTrigger}` ,, indica que a chave de linha vem da cadeia de caracteres de mensagem da fila.

Configuração de associação no _function.jsem_:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "MyQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "PersonEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Código do PowerShell no _run.ps1_:

```powershell
param($MyQueueItem, $PersonEntity, $TriggerMetadata)
Write-Host "PowerShell queue trigger function processed work item: $MyQueueItem"
Write-Host "Person entity name: $($PersonEntity.Name)"
```

# <a name="python"></a>[Python](#tab/python)

A função a seguir usa um gatilho de fila para ler uma única linha de tabela como entrada para uma função.

Neste exemplo, a configuração de associação especifica um valor explícito para a tabela `partitionKey` e usa uma expressão para passar para o `rowKey` . A `rowKey` expressão `{id}` indica que a chave de linha vem da cadeia de caracteres de mensagem da fila.

Configuração de associação na _function.jsno_ arquivo:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "messageJSON",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "rowKey": "{id}",
      "connection": "AzureWebJobsStorage",
      "direction": "in"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ],
      "route": "messages/{id}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ],
  "disabled": false
}
```

Código Python no arquivo *\_ \_ init \_ \_ . py* :

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

 Em [bibliotecas de classes C#](functions-dotnet-class-library.md), use os seguintes atributos para configurar uma associação de entrada da tabela:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  O construtor do atributo usa o nome da tabela, a chave de partição e a chave de linha. O atributo pode ser usado em um `out` parâmetro ou no valor de retorno da função, conforme mostrado no exemplo a seguir:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Você pode definir a `Connection` propriedade para especificar a conta de armazenamento para usar, conforme mostrado no exemplo a seguir:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Para obter um exemplo completo, consulte o exemplo de C#.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Oferece uma maneira de especificar a conta de armazenamento para usar. O construtor toma o nome de uma configuração de aplicativo que contenha uma cadeia de conexão de armazenamento. O atributo pode ser aplicado no nível de classe, método ou parâmetro. O exemplo a seguir mostra o nível de classe e método:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

A conta de armazenamento a ser usada é determinada na seguinte ordem:

* A propriedade `Table` do atributo`Connection`.
* O `StorageAccount` atributo aplicado ao mesmo parâmetro do `Table` atributo.
* O `StorageAccount` atributo aplicado à função.
* O `StorageAccount` atributo aplicado à classe.
* A conta de armazenamento padrão para a função de aplicativo (configuração de aplicativo "AzureWebJobsStorage").

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O script C# não dá suporte a atributos.

# <a name="java"></a>[Java](#tab/java)

Na [biblioteca de runtime de funções Java](/java/api/overview/azure/functions/runtime), use a anotação `@TableInput` nos parâmetros cujo valor possa ser proveniente do Armazenamento de Tabelas.  Essa anotação pode ser usada com tipos nativos do Java, POJOs ou valores que permitem valor nulos usando `Optional<T>`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O JavaScript não dá suporte a atributos.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Não há suporte para atributos pelo PowerShell.

# <a name="python"></a>[Python](#tab/python)

O Python não dá suporte a atributos.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `Table`.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como `table`. Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure.|
|**direction** | n/d | Deve ser definido como `in`. Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure. |
|**name** | n/d | O nome da variável que representa a tabela ou entidade no código de função. | 
|**tableName** | **TableName** | O nome da tabela.| 
|**partitionKey** | **PartitionKey** |Opcional. Chave de partição da entidade de tabela para leitura. Consulte a seção [uso](#usage) para obter orientação sobre como usar essa propriedade.| 
|**rowKey** |**RowKey** | Opcional. Chave de linha da entidade de tabela para leitura. Consulte a seção [uso](#usage) para obter orientação sobre como usar essa propriedade.| 
|**take** |**Take** | Opcional. O número máximo de entidades para ler em JavaScript. Consulte a seção [uso](#usage) para obter orientação sobre como usar essa propriedade.| 
|**filter** |**Filter** | Opcional. Uma expressão de filtro OData para a entrada de tabela em JavaScript. Consulte a seção [uso](#usage) para obter orientação sobre como usar essa propriedade.| 
|**connection** |**Conexão** | O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. A configuração pode ser o nome de uma configuração do aplicativo prefixado "AzureWebJobs" ou nome da cadeia de conexão. Por exemplo, se o nome da sua configuração for "AzureWebJobsMyStorage", você poderá especificar "mystorage" aqui. O tempo de execução do Functions procurará automaticamente uma configuração de aplicativo denominada "AzureWebJobsMyStorage". Se você deixar `connection` vazio, o runtime de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

* **Ler uma linha em**

  Definir `partitionKey` e `rowKey`. Acessar os dados da tabela usando um parâmetro de método `T <paramName>`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. `T` geralmente é um tipo que implementa `ITableEntity` ou deriva de `TableEntity`. As propriedades `filter` e `take` não são usadas neste cenário.

* **Ler uma ou mais linhas**

  Acessar os dados da tabela usando um parâmetro de método `IQueryable<T> <paramName>`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. `T` geralmente é um tipo que implementa `ITableEntity` ou deriva de `TableEntity`. Você pode usar `IQueryable` métodos para fazer a filtragem necessária. As propriedades `partitionKey`, `rowKey`, `filter` e `take` não são usadas neste cenário.  

  > [!NOTE]
  > `IQueryable` não tem suporte no [runtime do Functions v2](functions-versions.md). Uma alternativa é [usar um parâmetro do método paramName de CloudTable](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) para ler a tabela usando o SDK de Armazenamento do Azure. Se você tentar associar `CloudTable` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

* **Ler uma linha em**

  Definir `partitionKey` e `rowKey`. Acessar os dados da tabela usando um parâmetro de método `T <paramName>`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. `T` geralmente é um tipo que implementa `ITableEntity` ou deriva de `TableEntity`. As propriedades `filter` e `take` não são usadas neste cenário.

* **Ler uma ou mais linhas**

  Acessar os dados da tabela usando um parâmetro de método `IQueryable<T> <paramName>`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. `T` geralmente é um tipo que implementa `ITableEntity` ou deriva de `TableEntity`. Você pode usar `IQueryable` métodos para fazer a filtragem necessária. As propriedades `partitionKey`, `rowKey`, `filter` e `take` não são usadas neste cenário.  

  > [!NOTE]
  > `IQueryable` não tem suporte no [runtime do Functions v2](functions-versions.md). Uma alternativa é [usar um parâmetro do método paramName de CloudTable](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) para ler a tabela usando o SDK de Armazenamento do Azure. Se você tentar associar `CloudTable` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

# <a name="java"></a>[Java](#tab/java)

O atributo [TableInput](/java/api/com.microsoft.azure.functions.annotation.tableinput) fornece acesso à linha da tabela que disparou a função.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Definir as propriedades `filter` e `take`. Não definir `partitionKey` ou `rowKey`. Acesse a entidade (ou entidades) de tabela de entrada usando `context.bindings.<BINDING_NAME>`. Os objetos desserializados têm propriedades `RowKey` e `PartitionKey`.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Os dados são passados para o parâmetro de entrada, conforme especificado pela `name` chave na *function.jsno* arquivo. Especificar o `partitionKey` e `rowKey` permite que você filtre registros específicos. Consulte o [exemplo do PowerShell](#example) para obter mais detalhes.

# <a name="python"></a>[Python](#tab/python)

Os dados da tabela são passados para a função como uma cadeia de caracteres JSON. Desserializar a mensagem chamando `json.loads` conforme mostrado no [exemplo](#example)de entrada.

---

## <a name="next-steps"></a>Próximas etapas

* [Gravar dados de armazenamento de tabela de uma função](./functions-bindings-storage-table-output.md)
