---
title: Associações de armazenamento de tabelas do Azure Functions
description: Entenda como usar as associações de armazenamento da Tabela do Azure no Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: 5c3049b5f9f7607b9b75f3bee48b6ccd44601b15
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68254749"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Associações de armazenamento de tabelas do Azure Functions

Este artigo explica como trabalhar com associações de armazenamento de Tabela do Azure no Azure Functions. O Azure Functions dá suporte a associações de entrada e saída para armazenamento de Tabelas do Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - Functions 1. x

As associações de armazenamento de Tabelas são fornecidas no pacote NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs), versão 2.x. O código-fonte do pacote está no repositório GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Pacotes - Functions 2. x

As associações de Armazenamento de Tabelas são fornecidas no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage), versão 3.x. O código-fonte do pacote está no repositório GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Entrada

Use a associação de entrada de armazenamento de Tabela do Azure para ler uma tabela em uma conta de Armazenamento do Azure.

## <a name="input---example"></a>Entrada - exemplo

Consulte o exemplo específico a um idioma:

* [Uma entidade de leitura de C#](#input---c-example---one-entity)
* [Associação de C# para IQueryable](#input---c-example---iqueryable)
* [Associação de C# para CloudTable](#input---c-example---cloudtable)
* [Script de C# ler uma entidade](#input---c-script-example---one-entity)
* [Associação de script de C# para IQueryable](#input---c-script-example---iqueryable)
* [Associação de script de C# para CloudTable](#input---c-script-example---cloudtable)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)
* [Java](#input---java-example)

### <a name="input---c-example---one-entity"></a>Entrada - exemplo de C# - uma entidade

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que lê uma linha da tabela. 

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

### <a name="input---c-example---iqueryable"></a>Entrada - exemplo de C# - IQueryable

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que lê várias linhas da tabela. Observe que a `MyPoco` classe deriva de `TableEntity`.

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

### <a name="input---c-example---cloudtable"></a>Entrada - exemplo de C# - CloudTable

`IQueryable` não tem suporte no [tempo de execução do Functions v2](functions-versions.md). Uma alternativa é usar um `CloudTable` parâmetro do método para ler a tabela usando o SDK de Armazenamento do Azure. Veja um exemplo de uma função 2.x que consulta uma tabela de log do Azure Functions:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Table;
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

Para obter mais informações sobre como usar o CloudTable, consulte [Introdução ao Armazenamento de Tabelas do Azure](../cosmos-db/table-storage-how-to-use-dotnet.md).

Se você tentar associar `CloudTable` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](#azure-storage-sdk-version-in-functions-1x).

### <a name="input---c-script-example---one-entity"></a>Entrada - exemplo de script de C# - uma entidade

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

A seção [configuração](#input---configuration) explica essas propriedades.

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

### <a name="input---c-script-example---iqueryable"></a>Entrada - exemplo de script de C# - IQueryable

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

A seção [configuração](#input---configuration) explica essas propriedades.

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

### <a name="input---c-script-example---cloudtable"></a>Entrada - exemplo de script de C# - CloudTable

`IQueryable` não tem suporte no [tempo de execução do Functions v2](functions-versions.md). Uma alternativa é usar um `CloudTable` parâmetro do método para ler a tabela usando o SDK de Armazenamento do Azure. Veja um exemplo de uma função 2.x que consulta uma tabela de log do Azure Functions:

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

Para obter mais informações sobre como usar o CloudTable, consulte [Introdução ao Armazenamento de Tabelas do Azure](../cosmos-db/table-storage-how-to-use-dotnet.md).

Se você tentar associar `CloudTable` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](#azure-storage-sdk-version-in-functions-1x).

### <a name="input---f-example"></a>Entrada - exemplo #F

O exemplo a seguir mostra uma associação de entrada de tabela em um arquivo *function.json* e código [script F#](functions-reference-fsharp.md) que usa a associação. A função usa um gatilho de fila para ler uma linha da tabela. 

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

A seção [configuração](#input---configuration) explica essas propriedades.

O código F# é o seguinte:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.LogInformation(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.LogInformation(sprintf "Name in Person entity: %s" personEntity.Name)
```

### <a name="input---javascript-example"></a>Entrada - exemplo de JavaScript

O exemplo a seguir mostra uma associação de entrada de tabela em um arquivo*function.json* e o código [JavaScript](functions-reference-node.md) que usa a associação. A função usa um gatilho de fila para ler uma linha da tabela. 

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

A seção [configuração](#input---configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

### <a name="input---java-example"></a>Entrada - exemplo de Java

O exemplo a seguir mostra uma função disparada por HTTP que retorna a contagem total de itens em uma partição especificada no Armazenamento de Tabelas.

```java
@FunctionName("getallcount")
public int run(
   @HttpTrigger(name = "req",
                 methods = {HttpMethod.GET},
                 authLevel = AuthorizationLevel.ANONYMOUS) Object dummyShouldNotBeUsed,
   @TableInput(name = "items",
                tableName = "mytablename",  partitionKey = "myparkey",
                connection = "myconnvarname") MyItem[] items
) {
    return items.length;
}
```


## <a name="input---attributes"></a>Entrada – atributos
 
Em [bibliotecas de classes C#](functions-dotnet-class-library.md), use os seguintes atributos para configurar uma associação de entrada da tabela:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  O construtor do atributo usa o nome da tabela, a chave de partição e a chave de linha. Ele pode ser usado em um parâmetro de saída ou no valor de retorno da função, conforme mostrado no exemplo a seguir:

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

  Para ver um exemplo completo, confira Entrada - exemplo de C#.

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

## <a name="input---java-annotations"></a>Entrada - anotações de Java

Na [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime), use a anotação `@TableInput` nos parâmetros cujo valor possa ser proveniente do Armazenamento de Tabelas.  Esta anotação pode ser usada com tipos Java nativos, POJOs ou valores anuláveis usando T\<> opcional. 

## <a name="input---configuration"></a>Entrada - configuração

A tabela a seguir explica as propriedades de configuração de associação que você definir no arquivo *function.json* e o `Table` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**type** | N/D | Deve ser definido como `table`. Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure.|
|**direction** | N/D | Deve ser definido como `in`. Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure. |
|**name** | N/D | O nome da variável que representa a tabela ou entidade no código de função. | 
|**tableName** | **TableName** | O nome da tabela.| 
|**partitionKey** | **PartitionKey** |Opcional. Chave de partição da entidade de tabela para leitura. Consulte a seção de [uso](#input---usage) para obter orientação sobre como usar essa propriedade.| 
|**rowKey** |**RowKey** | Opcional. Chave de linha da entidade de tabela para leitura. Consulte a seção de [uso](#input---usage) para obter orientação sobre como usar essa propriedade.| 
|**take** |**Take** | Opcional. O número máximo de entidades para ler em JavaScript. Consulte a seção de [uso](#input---usage) para obter orientação sobre como usar essa propriedade.| 
|**filter** |**Filtrar** | Opcional. Uma expressão de filtro OData para a entrada de tabela em JavaScript. Consulte a seção de [uso](#input---usage) para obter orientação sobre como usar essa propriedade.| 
|**conexão** |**Conexão** | O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui. Por exemplo, se você configurar `connection` para “MyStorage”, o tempo de execução do Functions procura por uma configuração de aplicativo que esteja nomeada “AzureWebJobsMyStorage." Se você deixar `connection` vazio, o tempo de execução de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Entrada - uso

A associação de entrada da Tabela de Armazenamento dá suporte aos seguintes cenários:

* **Ler uma linha em C# ou o script do C#**

  Definir `partitionKey` e `rowKey`. Acessar os dados da tabela usando um parâmetro de método `T <paramName>`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. `T` geralmente é um tipo que implementa `ITableEntity` ou deriva de `TableEntity`. As propriedades `filter` e `take` não são usadas neste cenário. 

* **Ler uma ou mais linhas em C# ou o script do C#**

  Acessar os dados da tabela usando um parâmetro de método `IQueryable<T> <paramName>`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. `T` geralmente é um tipo que implementa `ITableEntity` ou deriva de `TableEntity`. Você pode usar `IQueryable` métodos para fazer a filtragem necessária. As propriedades `partitionKey`, `rowKey`, `filter` e `take` não são usadas neste cenário.  

  > [!NOTE]
  > `IQueryable` não tem suporte no [tempo de execução do Functions v2](functions-versions.md). Uma alternativa é [usar um parâmetro do método paramName de CloudTable](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) para ler a tabela usando o SDK de Armazenamento do Azure. Se você tentar associar `CloudTable` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](#azure-storage-sdk-version-in-functions-1x).

* **Ler uma ou mais linhas em JavaScript**

  Definir as propriedades `filter` e `take`. Não definir `partitionKey` ou `rowKey`. Acesse a entidade (ou entidades) de tabela de entrada usando `context.bindings.<name>`. Os objetos desserializados têm propriedades `RowKey` e `PartitionKey`.

## <a name="output"></a>Saída

Use uma associação de saída de armazenamento de Tabela do Azure para gravar entidades para uma tabela em uma conta de Armazenamento do Azure.

> [!NOTE]
> Essa associação de saída não dá suporte para atualização de entidades existentes. Use a `TableOperation.Replace` operação [do SDK do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/cosmos-db/tutorial-develop-table-dotnet#delete-an-entity) para atualizar uma entidade existente.   

## <a name="output---example"></a>Saída - exemplo

Consulte o exemplo específico a um idioma:

* [C#](#output---c-example)
* [Script do C# (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Saída - exemplo C#

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que usa um gatilho HTTP para gravar uma única linha de tabela. 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

### <a name="output---c-script-example"></a>Saída - exemplo de script C#

O exemplo a seguir mostra uma associação de saída de tabela em um arquivo *function.json* e código [script C#](functions-reference-csharp.md) que usa a associação. A função escreve múltiplas entidades de tabela.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A seção [configuração](#output---configuration) explica essas propriedades.

Aqui está o código de script do C#:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

### <a name="output---f-example"></a>Saída - Exemplo #F

O exemplo a seguir mostra uma associação de saída de tabela em um arquivo *function.json* e código [script C#](functions-reference-fsharp.md) que usa a associação. A função escreve múltiplas entidades de tabela.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A seção [configuração](#output---configuration) explica essas propriedades.

O código F# é o seguinte:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: ILogger) =
    for i = 1 to 10 do
        log.LogInformation(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

### <a name="output---javascript-example"></a>Saída - exemplo JavaScript

O exemplo a seguir mostra uma associação de saída de tabela em um arquivo *function.json* e código [script C#](functions-reference-node.md) que usa a associação. A função escreve múltiplas entidades de tabela.

Aqui está o arquivo *function.json*:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A seção [configuração](#output---configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

## <a name="output---attributes"></a>Saída - atributos

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), use o [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

O construtor do atributo usa o nome da tabela. Ele pode ser usado em um `out` parâmetro ou no valor de retorno da função, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Você pode definir a `Connection` propriedade para especificar a conta de armazenamento para usar, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Para ver um exemplo completo, consulte [Saída – exemplo de C#](#output---c-example).

Você pode usar o `StorageAccount` atributo para especificar a conta de armazenamento no nível de classe, método ou parâmetro. Para obter mais informações, consulte [Entrada - atributos](#input---attributes).

## <a name="output---configuration"></a>Saída - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no `Table` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**type** | N/D | Deve ser definido como `table`. Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure.|
|**direction** | N/D | Deve ser definido como `out`. Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure. |
|**name** | N/D | O nome da variável usada no código da função que representa a tabela ou entidade. Definido como `$return` para referenciar o valor de retorno da função.| 
|**tableName** |**TableName** | O nome da tabela.| 
|**partitionKey** |**PartitionKey** | Chave de partição da entidade de tabela para gravar. Consulte a [seção de uso](#output---usage) para obter orientação sobre como usar essa propriedade.| 
|**rowKey** |**RowKey** | A chave de linha da entidade de tabela para gravar. Consulte a [seção de uso](#output---usage) para obter orientação sobre como usar essa propriedade.| 
|**conexão** |**Conexão** | O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui. Por exemplo, se você configurar `connection` para “MyStorage”, o tempo de execução do Functions procura por uma configuração de aplicativo que esteja nomeada “AzureWebJobsMyStorage." Se você deixar `connection` vazio, o tempo de execução de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - uso

A associação de entrada de Armazenamento da Tabela dá suporte aos seguintes cenários:

* **Gravar uma linha em qualquer idioma**

  Em C# e o script C#, acesse a entidade de tabela de saída usando um parâmetro de método como `out T paramName` ou valor de retorno da função. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. `T`pode ser qualquer tipo serializável, se a chave de partição e a chave de linha forem fornecidos pelo arquivo *function.json* ou o `Table` atributo. Caso contrário, `T` deve ser um tipo que inclua `PartitionKey` e `RowKey` propriedades. Nesse cenário, `T` normalmente implementa `ITableEntity` ou deriva de `TableEntity`, mas ele não precisa.

* **Gravar uma ou mais linhas em C# ou no script do C#**

  Em C# e o script C#, acesse a entidade de tabela de saída usando um parâmetro de método como `ICollector<T> paramName` ou `IAsyncCollector<T> paramName`. No script do C#, `paramName` é o valor especificado na propriedade `name` de *function.json*. `T` especifica o esquema das entidades que você deseja adicionar. Geralmente, `T` deriva de `TableEntity` ou implementa `ITableEntity`, mas isso não é obrigatório. A chave de partição e a linha valores de chave em *function.json* ou o `Table` construtor de atributo não são usados neste cenário.

  Uma alternativa é usar um `CloudTable` parâmetro do método para gravar a tabela usando o SDK de Armazenamento do Azure. Se você tentar associar `CloudTable` e receber uma mensagem de erro, certifique-se de ter uma referência para [a versão correta do SDK do Armazenamento](#azure-storage-sdk-version-in-functions-1x). Para um exemplo de código que associa a `CloudTable`, consulte os exemplos de associação de entrada para [C#](#input---c-example---cloudtable) ou [script de C#](#input---c-script-example---cloudtable) anteriormente neste artigo.

* **Gravar uma ou mais linhas em JavaScript**

  Em funções do JavaScript, acesse a tabela de saída usando `context.bindings.<name>`.

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Associação | Referência |
|---|---|
| Tabela | [Códigos de erro de tabelas](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Blob, tabela, fila | [Códigos de erro de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, tabela, fila | [Solução de problemas](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
