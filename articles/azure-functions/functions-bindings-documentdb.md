---
title: "Associações do Azure Cosmos DB para Functions | Microsoft Docs"
description: "Entenda como usar gatilhos e associações do Azure Cosmos DB no Azure Functions."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: 3d8497f0-21f3-437d-ba24-5ece8c90ac85
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/19/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ad058929eb888920823fddf549ada4ce2c6d9eee
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---
# <a name="azure-cosmos-db-bindings-for-functions"></a>Associações do Azure Cosmos DB para Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e codificar associações do Banco de Dados Cosmos do Azure no Azure Functions. O Functions dá suporte a associações de entrada, saída e gatilho para o Azure Cosmos DB.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Para obter mais informações sobre computação sem servidor com o Azure Cosmos DB, consulte [Azure Cosmos DB: computação de banco de dados sem servidor usando o Azure Functions](..\cosmos-db\serverless-computing-database.md).

<a id="trigger"></a>
<a id="cosmosdbtrigger"></a>

## <a name="azure-cosmos-db-trigger"></a>Gatilho do Azure Cosmos DB

O gatilho do Azure Cosmos DB usa o [Feed de Alterações do Azure Cosmos DB](../cosmos-db/change-feed.md) para escutar alterações entre partições. O gatilho requer uma segunda coleção que ele usa para armazenar _concessões_ sobre as partições.

Tanto a coleção que está sendo monitorada quanto a coleção que contém as concessões devem estar disponíveis para o gatilho funcionar.

O gatilho do Azure Cosmos DB dá suporte às seguintes propriedades:

|Propriedade  |Descrição  |
|---------|---------|
|**tipo** | Deve ser definido como `cosmosDBTrigger`. |
|**name** | O nome da variável usado no código de função que representa a lista de documentos com alterações. | 
|**direction** | Deve ser definido como `in`. Esse parâmetro é definido automaticamente quando você cria o gatilho no portal do Azure. |
|**connectionStringSetting** | O nome de uma configuração de aplicativo que contém a cadeia de conexão usada para conectar-se à conta do Azure Cosmos DB que está sendo monitorada. |
|**databaseName** | O nome do banco de dados do Azure Cosmos DB com a coleção que está sendo monitorada. |
|**collectionName** | O nome da coleção que está sendo monitorada. |
| **leaseConnectionStringSetting** | (Opcional) O nome de uma configuração de aplicativo que contém a cadeia de conexão para o serviço com a coleção de concessão. Quando não definido, o valor `connectionStringSetting` é usado. Esse parâmetro é definido automaticamente quando a associação é criada no portal. |
| **leaseDatabaseName** | (Opcional) O nome do banco de dados que contém a coleção usada para armazenar as concessões. Quando não definido, o valor da configuração `databaseName` é usado. Esse parâmetro é definido automaticamente quando a associação é criada no portal. |
| **leaseCollectionName** | (Opcional) O nome da coleção usada para armazenar as concessões. Quando não definido, o valor `leases` é usado. |
| **createLeaseCollectionIfNotExists** | (Opcional) Quando definido como `true`, a coleção de concessões é criada automaticamente quando ela ainda não existe. O valor padrão é `false`. |
| **leaseCollectionThroughput** | (Opcional) Define a quantidade de Unidades de Solicitação a atribuir quando a coleção de concessões for criada. Essa configuração é usada apenas quando `createLeaseCollectionIfNotExists` é definido como `true`. Esse parâmetro é definido automaticamente quando a associação é criada usando o portal.

>[!NOTE] 
>A cadeia de conexão usada para conectar-se à coleção concessões deve ter permissões de gravação.

Essas propriedades podem ser definidas na guia de Integração para a função no portal do Azure ou editando o arquivo de projeto `function.json`.

## <a name="using-an-azure-cosmos-db-trigger"></a>Como usar um gatilho do Azure Cosmos DB

Esta seção contém exemplos de como usar o gatilho do Azure Cosmos DB. Os exemplos presumem um gatilho de metadados semelhante ao seguinte:

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
 
Para obter um exemplo de como criar um gatilho do Azure Cosmos DB de um aplicativo de função no portal, consulte [Criar uma função disparada pelo Azure Cosmos DB](functions-create-cosmos-db-triggered-function.md). 

### <a name="trigger-sample-in-c"></a>Exemplo de gatilho em C# #
```cs 
    #r "Microsoft.Azure.Documents.Client"
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using System;
    public static void Run(IReadOnlyList<Document> documents, TraceWriter log)
    {
        log.Verbose("Documents modified " + documents.Count);
        log.Verbose("First document Id " + documents[0].Id);
    }
```


### <a name="trigger-sample-in-javascript"></a>Exemplo de gatilho em JavaScript
```javascript
    module.exports = function (context, documents) {
        context.log('First document Id modified : ', documents[0].id);

        context.done();
    }
```

<a id="docdbinput"></a>

## <a name="documentdb-api-input-binding"></a>Associação de entrada da API do DocumentDB
A associação de entrada de API do DocumentDB recupera um documento do Azure Cosmos DB e o envia ao parâmetro de entrada nomeada da função. A ID do documento pode ser determinada com base no gatilho que invoca a função. 

A associação de entrada da API do DocumentDB tem as seguintes propriedades em *function.json*:

|Propriedade  |Descrição  |
|---------|---------|
|**name**     | Nome do parâmetro de associação que representa o documento na função.  |
|**tipo**     | Deve ser definido como `documentdb`.        |
|**databaseName** | O banco de dados que contém o documento.        |
|**collectionName**  | O nome da coleção que contém o documento. |
|**ID**     | A ID do documento a ser recuperado. Essa propriedade dá suporte a parâmetros de associações. Para saber mais, consulte [Associar propriedades personalizadas de entrada em uma expressão de associação](functions-triggers-bindings.md#bind-to-custom-input-properties-in-a-binding-expression). |
|**sqlQuery**     | Uma consulta SQL do Azure Cosmos DB usada para recuperar vários documentos. A consulta dá suporte a associações de tempo de execução, como no exemplo: `SELECT * FROM c where c.departmentId = {departmentId}`.        |
|**conexão**     |O nome da configuração do aplicativo que contém a cadeia de conexão do Azure Cosmos DB.        |
|**direction**     | Deve ser definido como `in`.         |

Não é possível definir ambas as propriedades **id** e **sqlQuery**. Se nenhuma estiver definida, toda a coleção será recuperada.

## <a name="using-a-documentdb-api-input-binding"></a>Utilizar uma associação de entrada de API do DocumentDB

* Em funções C# e F#, todas as alterações feitas no documento de entrada por parâmetros de entrada nomeados são persistidas automaticamente. 
* Funções de JavaScript, as atualizações não são feitas automaticamente após a saída da função. Em vez disso, use `context.bindings.<documentName>In` e `context.bindings.<documentName>Out` para fazer atualizações. Consulte o [exemplo JavaScript](#injavascript).

<a name="inputsample"></a>

## <a name="input-sample-for-single-document"></a>Exemplo de entrada para um único documento
Suponha que você tenha a seguinte associação de entrada da API do DocumentDB na matriz `bindings` de function.json:

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

Veja o exemplo de idioma específico que usa essa associação de entrada para atualizar o valor de texto do documento.

* [C#](#incsharp)
* [F#](#infsharp)
* [JavaScript](#injavascript)

<a name="incsharp"></a>
### <a name="input-sample-in-c"></a>Exemplo de entrada em C# #

```cs
// Change input document contents using DocumentDB API input binding 
public static void Run(string myQueueItem, dynamic inputDocument)
{   
  inputDocument.text = "This has changed.";
}
```
<a name="infsharp"></a>

### <a name="input-sample-in-f"></a>Exemplo de entrada em F# #

```fsharp
(* Change input document contents using DocumentDB API input binding *)
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

<a name="injavascript"></a>

### <a name="input-sample-in-javascript"></a>Amostra de entrada no JavaScript

```javascript
// Change input document contents using DocumentDB API input binding, using context.bindings.inputDocumentOut
module.exports = function (context) {   
  context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
  context.bindings.inputDocumentOut.text = "This was updated!";
  context.done();
};
```

## <a name="input-sample-with-multiple-documents"></a>Exemplo de entrada com vários documentos

Suponha que você deseja recuperar vários documentos especificados por uma consulta SQL, usando um gatilho de fila para personalizar os parâmetros de consulta. 

Neste exemplo, o gatilho de fila fornece um parâmetro `departmentId`. Uma mensagem da fila de `{ "departmentId" : "Finance" }` retornará todos os registros para o departamento financeiro. Usar o seguinte em *function.json*:

```
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

### <a name="input-sample-with-multiple-documents-in-c"></a>Exemplo de entrada com vários documentos em C#

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

### <a name="input-sample-with-multiple-documents-in-javascript"></a>Exemplo de entrada com vários documentos em JavaScript

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

## <a id="docdboutput"></a>Associação de saída da API do DocumentDB
A associação de saída da API do DocumentDB permite que você escreva um novo documento em um banco de dados do Banco de Dados Cosmos do Azure. O *function.json* especifica as propriedades a seguir:

|Propriedade  |Descrição  |
|---------|---------|
|**name**     | Nome do parâmetro de associação que representa o documento na função.  |
|**tipo**     | Deve ser definido como `documentdb`.        |
|**databaseName** | O banco de dados que contém a coleção na qual o documento será criado.     |
|**collectionName**  | O nome da coleção na qual o documento será criado. |
|**createIfNotExists**     | É um valor booliano para indicar se a coleção será criada quando não existir. O padrão é *false*. Isso ocorre porque as novas coleções são criadas com a taxa de transferência reservada, o que tem implicações de preço. Para obter mais detalhes, visite a [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).  |
|**conexão**     |O nome da configuração do aplicativo que contém a cadeia de conexão do Azure Cosmos DB.        |
|**direction**     | Deve ser definido como `out`.         |

## <a name="using-a-documentdb-api-output-binding"></a>Usar uma associação de saída de API do DocumentDB
Esta seção mostra como usar a associação de saída da API do DocumentDB em seu código da função.

Por padrão, quando você grava no parâmetro de saída em sua função, um documento é criado no banco de dados. Este documento contém um GUID gerado automaticamente como a ID do documento. Você pode especificar a ID do document de saída especificando a propriedade `id` no objeto JSON no parâmetro de saída. 

>[!Note]  
>Ao especificar a ID de um documento existente, ela é substituída pelo novo documento de saída. 

Para obter vários documentos de saída, você também pode associar a `ICollector<T>` ou `IAsyncCollector<T>`, sendo `T` um dos tipos com suporte.

<a name="outputsample"></a>

## <a name="documentdb-api-output-binding-sample"></a>Exemplo de associação de saída da API do DocumentDB
Suponha que você tenha a seguinte associação de saída da API do DocumentDB na matriz `bindings` de function.json:

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

E que você tenha uma associação de entrada de fila para uma fila que recebe o JSON no seguinte formato:

```json
{
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

E você deseja criar documentos do Azure Cosmos DB no formato a seguir para cada registro:

```json
{
  "id": "John Henry-123456",
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

Veja o exemplo de idioma específico que usa essa associação de saída para adicionar documentos ao seu banco de dados.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [JavaScript](#outjavascript)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>Amostra de saída em C# #

```cs
#r "Newtonsoft.Json"

using System;
using Newtonsoft.Json;
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

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>Amostra de saída em F# #

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

<a name="outjavascript"></a>

### <a name="output-sample-in-javascript"></a>Amostra de saída no JavaScript

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

