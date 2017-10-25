---
title: "Associações de tabela de armazenamento do Azure Functions | Microsoft Docs"
description: "Entenda como usar as associações do Armazenamento do Azure no Azure Functions."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: 65b3437e-2571-4d3f-a996-61a74b50a1c2
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/28/2016
ms.author: chrande
ms.openlocfilehash: 486b7c31c914ba7bb2d75e3f83ccf346a09104e8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-storage-table-bindings"></a>Associações de tabela de armazenamento do Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Esse artigo explica como configurar e codificar associações de tabela do Armazenamento do Azure no Azure Functions. O Azure Functions dá suporte a associações de entrada e saída para tabelas do Armazenamento do Azure.

A associação da tabela de Armazenamento dá suporte aos seguintes cenários:

* **Ler uma única linha em uma função do C# ou do Node.js** – Defina `partitionKey` e `rowKey`. As propriedades `filter` e `take` não são usadas neste cenário.
* **Ler várias linhas em uma função do C#** – O tempo de execução do Functions fornece um objeto `IQueryable<T>` associado à tabela. O tipo `T` deve derivar de `TableEntity` ou implementar `ITableEntity`. As propriedades `partitionKey`, `rowKey`, `filter` e `take` não são usadas neste cenário; você pode usar o objeto `IQueryable` para fazer qualquer filtragem necessária. 
* **Ler várias linhas em uma função do Node** – Defina as propriedades `filter` e `take`. Não definir `partitionKey` ou `rowKey`.
* **Escrever uma ou mais linhas em uma função do C#** – O tempo de execução do Functions oferece uma associação `ICollector<T>` ou `IAsyncCollector<T>` à tabela, em que `T` especifica o esquema das entidades que você deseja adicionar. Geralmente, o tipo `T` deriva de `TableEntity` ou implementa `ITableEntity`, mas isso não é obrigatório. As propriedades `partitionKey`, `rowKey`, `filter` e `take` não são usadas neste cenário.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="storage-table-input-binding"></a>Associação de entrada da tabela de armazenamento
A associação de entrada da tabela de Armazenamento do Azure permite usar uma tabela de armazenamento na função. 

A entrada da tabela de Armazenamento de uma função usa os seguintes objetos JSON na matriz `bindings` de function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "in",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to read - see below>",
    "rowKey": "<RowKey of table entity to read - see below>",
    "take": "<Maximum number of entities to read in Node.js - optional>",
    "filter": "<OData filter expression for table input in Node.js - optional>",
    "connection": "<Name of app setting - see below>",
}
```

Observe o seguinte: 

* Use `partitionKey` e `rowKey` juntos para ler uma única entidade. Essas propriedades são opcionais. 
* `connection` deve conter o nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. No portal do Azure, o editor padrão da guia **Integrar** define essa configuração de aplicativo para você quando você cria uma conta de Armazenamento ou seleciona uma conta existente. Você também pode [definir a configuração deste aplicativo manualmente](functions-how-to-use-azure-function-app-settings.md#settings).  

<a name="inputusage"></a>

## <a name="input-usage"></a>Uso de entrada
Em funções do C#, você associa à entidade (ou entidades) de tabela de entrada usando um parâmetro nomeado na assinatura da função, como `<T> <name>`.
Em que `T` é o tipo de dados no qual você deseja desserializar os dados e `paramName` é o nome especificado na [associação de entrada](#input). Em funções do Node.js, você acessa a entidade (ou entidades) de tabela de entrada usando `context.bindings.<name>`.

Os dados de entrada podem ser desserializados em funções do Node.js ou do C#. Os objetos desserializados têm propriedades `RowKey` e `PartitionKey`.

Em funções do C#, você também pode associar a qualquer um dos seguintes tipos, e o tempo de execução do Functions tentará desserializar os dados da tabela usando esse tipo:

* Qualquer tipo que implementa `ITableEntity`
* `IQueryable<T>`

<a name="inputsample"></a>

## <a name="input-sample"></a>Amostra de entrada
Suponha que você tem o function.json a seguir, que usa um gatilho da fila para ler uma única linha da tabela. O JSON especifica `PartitionKey` 
`RowKey`. `"rowKey": "{queueTrigger}"` indica que a chave de linha foi obtida da cadeia de caracteres da mensagem da fila.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
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
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Consulte a amostra específica da linguagem que lê uma única entidade de tabela.

* [C#](#inputcsharp)
* [F#](#inputfsharp)
* [Node.js](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>Exemplo de entrada em C# #
```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

<a name="inputfsharp"></a>

### <a name="input-sample-in-f"></a>Exemplo de entrada em F# #
```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>Amostra de entrada no Node.js
```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

<a name="output"></a>

## <a name="storage-table-output-binding"></a>Associação de saída da tabela de armazenamento
A associação de saída da tabela de Armazenamento do Azure permite gravar entidades em uma tabela de Armazenamento na função. 

A saída da tabela de Armazenamento de uma função usa os seguintes objetos JSON na matriz `bindings` de function.json:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "out",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to write - see below>",
    "rowKey": "<RowKey of table entity to write - see below>",
    "connection": "<Name of app setting - see below>",
}
```

Observe o seguinte: 

* Use `partitionKey` e `rowKey` juntos para gravar uma única entidade. Essas propriedades são opcionais. Também especifique `PartitionKey` e `RowKey` ao criar os objetos de entidade no código da função.
* `connection` deve conter o nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. No portal do Azure, o editor padrão da guia **Integrar** define essa configuração de aplicativo para você quando você cria uma conta de Armazenamento ou seleciona uma conta existente. Você também pode [definir a configuração deste aplicativo manualmente](functions-how-to-use-azure-function-app-settings.md#settings). 

<a name="outputusage"></a>

## <a name="output-usage"></a>Uso de saída
Em funções do C#, você associa à saída de tabela usando o parâmetro `out` nomeado na assinatura da função, como `out <T> <name>`, em que `T` é o tipo de dados no qual você deseja serializar os dados, e `paramName` é o nome especificado na [associação de saída](#output). Em funções do Node.js, você acessa a tabela de saída usando `context.bindings.<name>`.

Serialize objetos em funções do Node.js ou do C#. Em funções do C#, também é possível associar aos seguintes tipos:

* Qualquer tipo que implementa `ITableEntity`
* `ICollector<T>` (para gerar várias entidades. Consulte a [amostra](#outcsharp).)
* `IAsyncCollector<T>` (versão assíncrona de `ICollector<T>`)
* `CloudTable` (usando o SDK do Armazenamento do Azure. Consulte a [amostra](#readmulti).)

<a name="outputsample"></a>

## <a name="output-sample"></a>Amostra de saída
O exemplo de *function.json* e *run.csx* a seguir mostra como gravar várias entidades de tabela.

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
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Consulte a amostra específica da linguagem que cria várias entidades de tabela.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Amostra de saída em C# #
```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
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
<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Amostra de saída em F# #
```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Amostra de saída no Node.js
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

<a name="readmulti"></a>

## <a name="sample-read-multiple-table-entities-in-c"></a>Amostra: ler várias entidades de tabela em C#  #
O exemplo de código *function.json* e C# a seguir lê entidades para uma chave de partição especificada na mensagem de fila.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

O código C# adiciona uma referência ao SDK de Armazenamento do Azure para que o tipo de entidade possa derivar de `TableEntity`.

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

