---
title: Padrões e expressões de associações de funções do azure
description: Aprenda a criar expressões de associação de funções do Azure diferentes com base em padrões comuns.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
origin.date: 02/18/2019
ms.date: 03/20/2019
ms.author: v-junlch
ms.openlocfilehash: 0c1dbbae5e4be965f195b5ea4fc88b1bc5fb4f87
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437864"
---
# <a name="azure-functions-binding-expression-patterns"></a>Padrões de expressões de associação de funções do Azure

Um dos recursos mais poderosos do [gatilhos e associações](./functions-triggers-bindings.md) é *expressões de associação*. No arquivo *function.json* e em parâmetros de função e de código, você pode usar expressões que são resolvidas para valores de várias fontes.

A maioria das expressões são identificadas, encapsulando-as entre chaves. Por exemplo, em uma função de gatilho de fila, `{queueTrigger}` resolve para o texto de mensagem da fila. Se a propriedade `path` para uma associação de saída de blob é `container/{queueTrigger}` e a função é disparada por uma mensagem da fila `HelloWorld`, um blob denominado `HelloWorld` é criado.

Tipos de expressões de associação

* [Configurações do aplicativo](#binding-expressions---app-settings)
* [Nome do arquivo de gatilho](#trigger-file-name)
* [Gatilho metadados](#trigger-metadata)
* [Cargas JSON](#json-payloads)
* [Novo GUID](#create-guids)
* [Data e hora atuais](#current-time)

## <a name="binding-expressions---app-settings"></a>Expressões de associação - configurações do aplicativo

Como prática recomendada, os segredos e cadeias de conexão devem ser gerenciados usando configurações do aplicativo, em vez de arquivos de configuração. Isso limita o acesso a esses segredos e torna seguro armazenar arquivos como *function.json* em repositórios de controle do código-fonte público.

Configurações do aplicativo também são úteis sempre que você desejar alterar a configuração com base no ambiente. Por exemplo, em um ambiente de teste, pode ser útil monitorar um contêiner de armazenamento de filas ou de blobs diferente.

Expressões de associação de configuração do aplicativo são identificadas diferentemente de outras expressões de associação: elas são dispostas em sinais de porcentagem em vez de chaves. Por exemplo, se o caminho de associação de saída de blob é `%Environment%/newblob.txt` e o `Environment` valor de configuração do aplicativo é `Development`, um blob será criado no contêiner `Development`.

Quando uma função é executada localmente, os valores de configuração do aplicativo são provenientes do arquivo *local.settings.json*.

Observe que a propriedade `connection` dos gatilhos e associações é um caso especial e resolve automaticamente os valores de configurações do aplicativo, sem os sinais de porcentagem. 

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

Você pode usar a mesma abordagem em bibliotecas de classes:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input-queue-name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-file-name"></a>Nome do arquivo de gatilho

O `path` para um gatilho de Blob pode ser um padrão que permite que você se refera ao nome do blob que dispara em outras associações e código de função. O padrão também pode incluir critérios de filtragem que especifique os blobs que podem disparar uma invocação de função.

Por exemplo, na seguinte associação de gatilho de Blob, o `path` padrão é `sample-images/{filename}`, que cria uma expressão de associação denominada `filename`:

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
    ...
```

A expressão `filename` pode ser usada em uma associação de saída para especificar o nome do blob que está sendo criado:

```json
    ...
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

Código de função tem acesso a esse mesmo valor usando `filename` como um nome de parâmetro:

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, ILogger log)  
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

A mesma capacidade de usar padrões e expressões de associação se aplica a atributos em bibliotecas de classes. No exemplo a seguir, os parâmetros do construtor de atributo são os mesmos `path` valores dos exemplos de *function.json* anteriores: 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger log)
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
}

```

Você também pode criar expressões para partes do nome do arquivo, como a extensão. Para obter mais informações sobre como usar padrões e expressões na cadeia de caracteres de caminho de Blob, consulte a [referência de associação de blob de Armazenamento](functions-bindings-storage-blob.md).

## <a name="trigger-metadata"></a>Metadados de gatilho

Além do conteúdo dos dados fornecido por um gatilho (como o conteúdo da mensagem da fila que disparou uma função), vários gatilhos fornecem valores de metadados adicionais. Esses valores podem ser usados como parâmetros de entrada em C# e F# ou propriedades no objeto `context.bindings` em JavaScript. 

Por exemplo, um gatilho do Armazenamento de Filas do Azure é compatível com as seguintes propriedades:

* QueueTrigger – disparar o conteúdo da mensagem em caso de uma cadeia de caracteres válida
* DequeueCount
* ExpirationTime
* ID
* InsertionTime
* NextVisibleTime
* PopReceipt

Esses valores de metadados estão acessíveis nas propriedades do arquivo *function.json*. Por exemplo, suponha que você usa um gatilho de fila e que a mensagem da fila contém o nome de um blob que você deseja ler. No arquivo *function.json*, você pode usar a propriedade de metadados `queueTrigger` na propriedade `path` do blob, conforme mostrado no exemplo a seguir:

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

Detalhes de propriedades de metadados para cada gatilho são descritos no artigo de referência correspondente. Para obter um exemplo, consulte [metadados de gatilho de fila](functions-bindings-storage-queue.md#trigger---message-metadata). A documentação também está disponível na guia **Integrar** do portal, na seção **Documentação** abaixo da área de configuração de associação.  

## <a name="json-payloads"></a>Cargas JSON

Quando uma carga de gatilho for JSON, você pode consultar as propriedades na configuração de outras associações na mesma função e no código de função.

A exemplo a seguir mostra o arquivo *function.json* arquivo para uma função de webhook que recebe um nome de blob em JSON: `{"BlobName":"HelloWorld.txt"}`. Uma associação de entrada do Blob lê o blob e associação de saída HTTP retorna o conteúdo de blob na resposta HTTP. Observe que a associação de entrada do Blob obtém o nome do blob referindo-se diretamente à `BlobName` propriedade (`"path": "strings/{BlobName}"`)

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

Para este trabalho em C# e F#, você precisa de uma classe que define os campos a serem desserializados, como no exemplo a seguir:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents, ILogger log)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    log.LogInformation($"Processing: {info.BlobName}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

No JavaScript, a desserialização JSON é executada automaticamente.

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

### <a name="dot-notation"></a>Notação de ponto

Se algumas das propriedades na sua carga JSON são objetos com propriedades, você pode consultar esses diretamente usando a notação de ponto. Por exemplo, suponha que o JSON tem esta aparência:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

Você pode se referir diretamente a `FileName` como `BlobName.FileName`. Com esse formato JSON, aqui está como se parece a propriedade `path` no exemplo anterior:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

Em C#, você precisaria de duas classes:

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

## <a name="create-guids"></a>Criar GUIDs

A expressão de associação `{rand-guid}` cria um GUID. O seguinte caminho de blob em um `function.json` arquivo cria um blob com um nome como *50710cb5-84b9-4d87-9d 83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

## <a name="current-time"></a>Hora atual

A expressão de associação `DateTime` resolve para `DateTime.UtcNow`. O seguinte caminho de blob em um `function.json` arquivo cria um blob com um nome como *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```
## <a name="binding-at-runtime"></a>Associando no tempo de execução

No C#, e em outras linguagens .NET, você pode usar um padrão de associação obrigatório, em vez de associações declarativas em *function.json* e atributos. A associação obrigatória é útil quando os parâmetros de associação precisam ser calculado no tempo de execução, em vez do tempo de design. Para obter mais informações, consulte a [referência do desenvolvedor C#](functions-dotnet-class-library.md#binding-at-runtime) ou [referência do desenvolvedor de script C#](functions-reference-csharp.md#binding-at-runtime).

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Usando o valor de retorno de função do Azure](./functions-bindings-return-value.md)

<!-- Update_Description: link update -->