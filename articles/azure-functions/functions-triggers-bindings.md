---
title: "Trabalhar com gatilhos e associações no Azure Functions | Microsoft Docs"
description: "Saiba como usar gatilhos e associações no Azure Functions para conectar a execução de seu código a eventos online e a serviços baseados em nuvem."
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
tags: 
keywords: "azure functions, functions, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"
ms.assetid: cbc7460a-4d8a-423f-a63e-1cd33fef7252
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/30/2017
ms.author: donnam
ms.openlocfilehash: ab438f804c28d5528901c405311424e0344e00fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Conceitos de gatilhos e de associações do Azure Functions
O Azure Functions permite escrever código em resposta a eventos no Azure e outros serviços, por meio de *gatilhos* e *associações*. Este artigo é uma visão geral conceitual dos gatilhos e associações para todas as linguagens de programação com suporte. Recursos que são comuns a todas as associações são descritos aqui.

## <a name="overview"></a>Visão geral

Gatilhos e associações são uma forma declarativa de definir como uma função é invocada e com que dados ela trabalha. Um *gatilho* define como uma função é invocada. Uma função deve ter exatamente um gatilho. Gatilhos têm dados associados, que geralmente é o conteúdo que disparou a função. 

*Associações* de entrada e saída fornecem uma maneira declarativa de se conectar aos dados de dentro do seu código. Semelhante aos gatilhos, as cadeias de conexão e outras propriedades são especificadas na configuração da função. Associações são opcionais e uma função pode ter várias associações de entrada e saída. 

Usando gatilhos e associações, você pode escrever código que é mais genérico e não codificar os detalhes dos serviços com os quais ele interage. Dados provenientes de serviços simplesmente tornam-se valores de entrada para o código de função. Para exibir os dados de saída para outro serviço (tal como criar uma nova linha no Armazenamento de Tabelas do Azure), use o valor retornado do método. Ou então, se você precisar gerar vários valores de saída, use um objeto auxiliar. Gatilhos e associações têm uma propriedade **name**, que é um identificador usado no código para acessar a associação.

Você pode configurar os gatilhos e associações na guia **Integrar** no Portal do Azure Functions. Nos bastidores, a interface do usuário modifica um arquivo chamado *function.json* arquivo no diretório da função. Você pode editar esse arquivo mudando para o **Editor avançado**.

A tabela a seguir mostra os gatilhos e as associações com suporte no Azure Functions. 

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

### <a name="example-queue-trigger-and-table-output-binding"></a>Exemplo: gatilho de fila e associação de saída de tabela

Suponha que você deseja gravar uma nova linha no Armazenamento de Tabelas do Azure sempre que uma nova mensagem aparece no Armazenamento de Filas do Azure. Esse cenário pode ser implementado usando um gatilho da Fila do Azure e uma associação de saída do Armazenamento de Tabelas do Azure. 

Um gatilho do Armazenamento de Filas do Azure requer as seguintes informações na guia **Integrar**:

* O nome da configuração do aplicativo que contém a cadeia de conexão da conta de Armazenamento do Azure para o Armazenamento de Filas do Azure
* O nome da fila
* O identificador em seu código para ler o conteúdo da mensagem da fila, como `order`.

Para gravar no Armazenamento de Tabelas do Azure, use uma associação de saída com os seguintes detalhes:

* O nome da configuração do aplicativo que contém a cadeia de conexão da conta de Armazenamento do Azure para o Armazenamento de Tabelas do Azure
* O nome da tabela
* O identificador em seu código para criar itens de saída, ou o valor retornado da função.

As associações usam valores de cadeias de conexão armazenados nas configurações do aplicativo para impor a melhor prática de que *function.json* não contém segredos de serviço e, em vez disso, contém simplesmente os nomes das configurações do aplicativo.

Em seguida, use os identificadores que você forneceu para integrar o Armazenamento do Azure em seu código.

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, TraceWriter log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

Este é o *function.json* que corresponde ao código anterior. Observe que a mesma configuração pode ser usada, independentemente da linguagem de implementação da função.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "name": "$return",
      "type": "table",
      "direction": "out",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```
Para exibir e editar o conteúdo de *function.json* no Portal do Azure, clique na opção **Editor avançado** na guia **Integrar** da sua função.

Para ver exemplos de código e detalhes sobre a integração com o Armazenamento do Azure, consulte [Gatilhos e associações do Azure Functions para o Armazenamento do Azure](functions-bindings-storage.md).

### <a name="binding-direction"></a>Direção de associação

Todos os disparadores e associações têm uma propriedade `direction`:

- Para gatilhos, a direção sempre é `in`
- Associações de entrada e saída usam `in` e `out`
- Algumas associações dão suporte a uma direção especial `inout`. Se você usar `inout`, somente o **Editor avançado** estará disponível na guia **Integrar**.

## <a name="using-the-function-return-type-to-return-a-single-output"></a>Usando o tipo de retorno da função para retornar um único resultado

O exemplo anterior mostra como usar o valor retornado da função para fornecer saída para uma associação, que é obtida usando o parâmetro de nome especial `$return`. (Isso tem suporte em linguagens que têm um valor retornado, como C#, JavaScript e F#.) Se uma função tiver várias associações de saída, use `$return` apenas para uma delas. 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Os exemplos a seguir mostram como tipos de retorno são usados com associações de saída em C#, JavaScript e F#.

```cs
// C# example: use method return value for output binding
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
// C# example: async method, using return value for output binding
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

```javascript
// JavaScript: return a value in the second parameter to context.done
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

```fsharp
// F# example: use return value for output binding
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

## <a name="binding-datatype-property"></a>Associação da propriedade dataType

No .NET, use os tipos para definir o tipo de dados para os dados de entrada. Por exemplo, use `string` para associar ao texto de um gatilho de fila, uma matriz de bytes para ler como binário e um tipo personalizado para desserializar para um objeto POCO.

Para idiomas que são digitados dinamicamente como JavaScript, use a propriedade `dataType` na definição da associação. Por exemplo, para ler o conteúdo de uma solicitação HTTP em formato binário, use o tipo `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Outras opções para `dataType` são `stream` e `string`.

## <a name="resolving-app-settings"></a>Resolvendo configurações de aplicativo
Como prática recomendada, os segredos e cadeias de conexão devem ser gerenciados usando configurações do aplicativo, em vez de arquivos de configuração. Isso limita o acesso a esses segredos e torna seguro armazenar *function.json* em um repositório de controle do código-fonte público.

Configurações do aplicativo também são úteis sempre que você desejar alterar a configuração com base no ambiente. Por exemplo, em um ambiente de teste, pode ser útil monitorar um contêiner de armazenamento de filas ou de blobs diferente.

Configurações do aplicativo são resolvidas sempre que um valor está entre sinais de porcentagem, como `%MyAppSetting%`. Observe que a propriedade `connection` dos gatilhos e associações é um caso especial e resolve automaticamente os valores de configurações do aplicativo. 

O exemplo a seguir é um gatilho do Armazenamento de Filas do Azure que usa uma configuração de aplicativo `%input-queue-name%` para definir a fila em que o gatilho é disparado.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

## <a name="trigger-metadata-properties"></a>Propriedades de metadados de gatilho

Além do conteúdo dos dados fornecido por um gatilho (como a mensagem da fila que disparou uma função), vários gatilhos fornecem valores de metadados adicionais. Esses valores podem ser usados como parâmetros de entrada em C# e F# ou propriedades no objeto `context.bindings` em JavaScript. 

Por exemplo, um gatilho da Fila do Armazenamento do Azure dá suporte às seguintes propriedades:

* QueueTrigger – disparar o conteúdo da mensagem em caso de uma cadeia de caracteres válida
* DequeueCount
* ExpirationTime
* ID
* InsertionTime
* NextVisibleTime
* PopReceipt

Detalhes de propriedades de metadados para cada gatilho são descritos no tópico de referência correspondente. A documentação também está disponível na guia **Integrar** do portal, na seção **Documentação** abaixo da área de configuração de associação.  

Por exemplo, como gatilhos de blobs apresentam alguns atrasos, você pode usar um gatilho de fila para executar sua função (consulte [Gatilho do Armazenamento de Blobs](functions-bindings-storage-blob.md#storage-blob-trigger)). A mensagem da fila conteria o nome do arquivo a ser disparado no blob. Usando a propriedade de metadados `queueTrigger`, é possível especificar esse comportamento completo na sua configuração, em vez do código.

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

Propriedades de metadados de um gatilho também podem ser usadas em uma *expressão de associação* para outra associação, conforme descrito na seção a seguir.

## <a name="binding-expressions-and-patterns"></a>Padrões e expressões de associação

Um dos recursos mais poderosos de gatilhos e associações são as *expressões de associação*. Dentro da associação, é possível definir expressões padrão que podem ser usadas em outras associações ou no seu código. Metadados de gatilho também podem ser usado em expressões de associação, como mostrado no exemplo na seção anterior.

Por exemplo, suponha que você deseja redimensionar imagens no contêiner de armazenamento de blobs específico, semelhante ao modelo **Redimensionador de Imagem** na página **Nova Função**. Acesse **Nova Função** -> Linguagem **C#** -> Cenário **Exemplos** -> **ImageResizer-CSharp**. 

Essa é a definição *function.json*:

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

Observe que o parâmetro `filename` é usado na definição do gatilho de blobs e na associação de saída de blobs. Esse parâmetro também pode ser usado no código de função.

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, TraceWriter log)  
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->


### <a name="random-guids"></a>GUIDs aleatórios
O Azure Functions fornece uma sintaxe conveniente para gerar GUIDs em suas associações por meio da expressão de associação `{rand-guid}`. O exemplo a seguir usa isso para gerar um nome exclusivo de blob: 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="current-time"></a>Hora atual

Você pode usar a expressão de associação `DateTime`, que resolve para `DateTime.UtcNow`.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="bind-to-custom-input-properties-in-a-binding-expression"></a>Associe as propriedades personalizadas de entrada em uma expressão de associação

Expressões de associação também podem fazer referência a propriedades que são definidas no próprio conteúdo de gatilho. Por exemplo, pode ser útil associar dinamicamente um arquivos de armazenamento de blobs de um nome de arquivo fornecido em um webhook.

Por exemplo, o *function.json* a seguir usa uma propriedade chamada `BlobName` do conteúdo do gatilho:

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Para fazer isso em C# e F#, você deve definir um POCO que define os campos que serão desserializados no conteúdo do gatilho.

```csharp
using System.Net;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

No JavaScript, a desserialização JSON é executada automaticamente e você pode usar as propriedades diretamente.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

## <a name="configuring-binding-data-at-runtime"></a>Configuração de associação de dados em tempo de execução

No C# e em outras linguagens .NET, você pode usar um padrão de associação obrigatório, em vez de associações declarativas em *function.json*. A associação obrigatória é útil quando os parâmetros de associação precisam ser calculado no tempo de execução, em vez do tempo de design. Para obter mais informações, consulte [Associação em tempo de execução por meio de associações obrigatórias](functions-reference-csharp.md#imperative-bindings) na referência do desenvolvedor do C#.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre uma associação específica, consulte os artigos a seguir:

- [HTTP e webhooks](functions-bindings-http-webhook.md)
- [Timer](functions-bindings-timer.md)
- [Armazenamento de filas](functions-bindings-storage-queue.md)
- [Armazenamento de Blobs](functions-bindings-storage-blob.md)
- [Armazenamento de tabelas](functions-bindings-storage-table.md)
- [Hub de Evento](functions-bindings-event-hubs.md)
- [Barramento de Serviço](functions-bindings-service-bus.md)
- [Banco de dados do Azure Cosmos](functions-bindings-documentdb.md)
- [Microsoft Graph](functions-bindings-microsoft-graph.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [Hubs de Notificação](functions-bindings-notification-hubs.md)
- [Aplicativos Móveis](functions-bindings-mobile-apps.md)
- [Arquivo externo](functions-bindings-external-file.md)
