---
title: "Associações de armazenamento de Blobs do Azure Functions | Microsoft Docs"
description: "Entenda como usar gatilhos e associações do Armazenamento do Azure em Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: aba8976c-6568-4ec7-86f5-410efd6b0fb9
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: b123578dbac48018f674f85ec923e4c6e65fb9f8
ms.contentlocale: pt-br
ms.lasthandoff: 08/29/2017

---
# <a name="azure-functions-blob-storage-bindings"></a>Associações de armazenamento de Blobs do Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e trabalhar com associações do armazenamento de Blobs do Azure no Azure Functions. O Azure Functions dá suporte a associações de entrada, saída e gatilho para o armazenamento de Blobs do Azure. Para os recursos que estão disponíveis em todas as associações, veja [Gatilhos e conceitos de associações do Azure Functions](functions-triggers-bindings.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Não há suporte para [conta de armazenamento somente de blob](../storage/common/storage-create-storage-account.md#blob-storage-accounts). Gatilhos de armazenamento de blobs e associações exigem uma conta de armazenamento de uso geral. 
> 

<a name="trigger"></a>
<a name="storage-blob-trigger"></a>
## <a name="blob-storage-triggers-and-bindings"></a>Gatilhos e associações de armazenamento de blobs

Usando o gatilho do armazenamento de blobs do Azure, seu código de função é chamado quando é detectado um blob novo ou atualizado. O conteúdo do blob é fornecido como entrada para a função.

Defina um gatilho de armazenamento de blobs usando a guia **Integrar** no portal do Functions. O portal cria a seguinte definição na seção **Ligações** de *function.json*:

```json
{
    "name": "<The name used to identify the trigger data in your code>",
    "type": "blobTrigger",
    "direction": "in",
    "path": "<container to monitor, and optionally a blob name pattern - see below>",
    "connection": "<Name of app setting - see below>"
}
```

As associações de entrada e saída de blob são definidas usando `blob` como o tipo de associação:

```json
{
  "name": "<The name used to identify the blob input in your code>",
  "type": "blob",
  "direction": "in", // other supported directions are "inout" and "out"
  "path": "<Path of input blob - see below>",
  "connection":"<Name of app setting - see below>"
},
```

* A propriedade `path` oferece suporte à associação de expressões e parâmetros de filtro. Veja [Padrões de nome](#pattern).
* A propriedade `connection` deve conter o nome de uma configuração de aplicativo que contenha uma cadeia de conexão de armazenamento. No Portal do Azure, o editor padrão na guia **Integrar** define essa configuração de aplicativo para você ao selecionar uma conta de armazenamento.

> [!NOTE]
> Ao usar um gatilho de blob em um plano de Consumo, pode haver um atraso de até 10 minutos no processamento de novos blobs depois que um aplicativo de funções ficar ocioso. Depois que o aplicativo de funções estiver em execução, os blobs serão processados imediatamente. Para evitar esse atraso inicial, considere uma das seguintes opções:
> - Use um plano do Serviço de Aplicativo com a opçao Sempre ativado habilitada.
> - Use outro mecanismo para disparar o processamento de blob, como uma mensagem de fila que contém o nome do blob. Para obter um exemplo, confira [Gatilho de fila com associação de entrada de blob](#input-sample).

<a name="pattern"></a>

### <a name="name-patterns"></a>Padrões de nome
Você pode especificar um padrão de nome de blob na propriedade `path`, que pode ser uma expressão de filtro ou associação. Veja [Padrões e expressões de associação](functions-triggers-bindings.md#binding-expressions-and-patterns).

Por exemplo, para filtrar os blobs que começam com a cadeia de caracteres "original", use a seguinte definição. Esse caminho encontra um blob chamado *original-Blob1.txt* no contêiner *input* e o valor da `name` variável no código de função é `Blob1`.

```json
"path": "input/original-{name}",
```

Para associar ao nome do arquivo de blob e extensão separadamente, use dois padrões. Esse caminho também é um blob chamado *original-Blob1.txt* e o valor das variáveis `blobname` e `blobextension` no código de função é *original-Blob1* e *txt*.

```json
"path": "input/{blobname}.{blobextension}",
```

Você pode restringir o tipo de arquivo de blobs usando um valor fixo para a extensão de arquivo. Por exemplo, para disparar apenas em arquivos .png, use o seguinte padrão:

```json
"path": "samples/{name}.png",
```

As chaves são caracteres especiais nos padrões de nome. Para especificar nomes de blob com chaves, você pode ignorar as chaves usando chaves duplas. O exemplo a seguir encontra um blob denominado *{20140101}-soundfile.mp3* no contêiner *imagens* e o `name` valor da variável no código da função é *soundfile.mp3*. 

```json
"path": "images/{{20140101}}-{name}",
```

### <a name="trigger-metadata"></a>Metadados de gatilho

O gatilho de blob fornece várias propriedades de metadados. Essas propriedades podem ser usadas como parte de expressões de associações em outras associações ou como parâmetros em seu código. Esses valores têm a mesma semântica que [CloudBlob](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet).

- **BlobTrigger**. Digite `string`. O caminho do blob de gatilho
- **Uri**. Digite `System.Uri`. A URI do blob para o local principal.
- **Propriedades**. Digite `Microsoft.WindowsAzure.Storage.Blob.BlobProperties`. As propriedades do sistema do blob.
- **Metadados**. Digite `IDictionary<string,string>`. Os metadados definidos pelo usuário para o blob.

<a name="receipts"></a>

### <a name="blob-receipts"></a>Recebimentos de blob
O tempo de execução do Azure Functions garante que nenhuma função de gatilho de blob seja chamada mais de uma vez para o mesmo blob novo ou atualizado. Para determinar se uma versão de determinado blob foi processada, ele mantém os *recebimentos de blob*.

O Azure Functions armazena recibos do blob em um contêiner denominado *azure-webjobs-hosts* na conta de armazenamento do Azure do seu aplicativo de funções (definido na configuração do aplicativo `AzureWebJobsStorage`). Um recebimento de blob tem as seguintes informações:

* A função disparada ("*&lt;nome do aplicativo de funções>*.Functions.*&lt;nome da função>*", por exemplo: "MyFunctionApp.Functions.CopyBlob")
* O nome do contêiner
* O tipo de blob ("BlockBlob" ou "PageBlob")
* O nome do blob
* O ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

Para forçar o reprocessamento de um blob, exclua manualmente o recebimento desse blob do contêiner *azure-webjobs-hosts*.

<a name="poison"></a>

### <a name="handling-poison-blobs"></a>Manipulação de blobs suspeitos
Quando uma função de gatilho de blob falhar para um determinado blob, o Azure Functions repete essa função até cinco vezes por padrão. 

Se todas as cinco tentativas falharem, o Azure Functions adiciona uma mensagem para uma fila de armazenamento denominada *webjobs-blobtrigger-poison*. A mensagem da fila para blobs suspeitos é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no formato *&lt;nome do aplicativo de funções>*.Functions.*&lt;nome da função>*)
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

### <a name="blob-polling-for-large-containers"></a>Sondagem de blobs para grandes contêineres
Se o contêiner de blob que está sendo monitorado contiver mais de 10.000 blobs, as verificações de tempo de execução do Functions varrerão os arquivos de log em busca de blobs novos ou alterados. Esse processo não ocorre em tempo real. Uma função não poderá ser disparada até que se passem vários minutos ou mais tempo depois da criação do blob. Além disso, [logs de armazenamento são criados da "melhor forma dentro do possível"](/rest/api/storageservices/About-Storage-Analytics-Logging). Não há nenhuma garantia de que todos os eventos são capturados. Sob algumas condições, logs poderão ser perdidos. Se você precisar de um processamento de blob mais rápido ou confiável, crie uma [mensagem de fila](../storage/queues/storage-dotnet-how-to-use-queues.md) ao criar o blob. Em seguida, use um [gatilho de fila](functions-bindings-storage-queue.md) em vez de um gatilho de blob para processar o blob.

<a name="triggerusage"></a>

## <a name="using-a-blob-trigger-and-input-binding"></a>Usando um gatilho de blob e uma associação de entrada
Nas funções do .NET, acesse os dados de blob usando um parâmetro de método, como `Stream paramName`. Aqui, `paramName` é o valor especificado na [configuração do gatilho](#trigger). Em funções do Node.js, acesse os dados do blob de entrada usando `context.bindings.<name>`.

No .NET, você pode associar a qualquer um dos tipos na lista abaixo. Se for usado como uma associação de entrada, alguns desses tipos exigem uma direção de associação `inout` no *function.json*. Não há suporte para essa direção pelo editor padrão. Use o editor avançado.

* `TextReader`
* `Stream`
* `ICloudBlob` (exige a direção de associação "inout")
* `CloudBlockBlob` (exige a direção de associação "inout")
* `CloudPageBlob` (exige a direção de associação "inout")
* `CloudAppendBlob` (exige a direção de associação "inout")

Se blobs de texto forem esperados, você também poderá associar a um tipo `string` de .NET. Isso será recomendado apenas se o tamanho do blob for pequeno, porque o conteúdo inteiro do blob é carregado na memória. Geralmente, é preferível usar um tipo `Stream` ou `CloudBlockBlob`.

## <a name="trigger-sample"></a>Exemplo de gatilho
Suponha que você tem o seguinte function.json, que define um gatilho de armazenamento de blobs:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccount"
        }
    ]
}
```

Veja o exemplo específico por linguagem que registra em log o conteúdo de cada blob adicionado ao contêiner monitorado.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="blob-trigger-examples-in-c"></a>Exemplos de gatilho de blob em C# #

```cs
// Blob trigger sample using a Stream binding
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

```cs
// Blob trigger binding to a CloudBlockBlob
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

<a name="triggernodejs"></a>

### <a name="trigger-example-in-nodejs"></a>Exemplo de gatilho em Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```
<a name="outputusage"></a>
<a name="storage-blob-output-binding"></a>

## <a name="using-a-blob-output-binding"></a>Usando uma associação de saída de blob

Em funções do .NET, você deve usar um parâmetro `out string` em sua assinatura de função ou usar um dos tipos na lista a seguir. Em funções do Node.js, você acessa o blob de saída usando `context.bindings.<name>`.

Em funções do .NET, é possível executar a saída para qualquer um dos seguintes tipos:

* `out string`
* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="input-sample"></a>

## <a name="queue-trigger-with-blob-input-and-output-sample"></a>Gatilho de fila com amostra de entrada e saída de blob
Suponha que você tenha o seguinte function.json, que define um [Gatilho de armazenamento de filas](functions-bindings-storage-queue.md), uma entrada de armazenamento de blobs e uma saída de armazenamento de blobs. Observe o uso da propriedade de metadados `queueTrigger`. Nas propriedades `path` de entrada e saída de blob:

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
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

Consulte o exemplo específico por linguagem que copia o blob de entrada para o blob de saída.

* [C#](#incsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>

### <a name="blob-binding-example-in-c"></a>Exemplo de associação de blob em C# #

```cs
// Copy blob from input to output, based on a queue trigger
public static void Run(string myQueueItem, Stream myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

<a name="innodejs"></a>

### <a name="blob-binding-example-in-nodejs"></a>Exemplo de associação de blob em Node.js

```javascript
// Copy blob from input to output, based on a queue trigger
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


