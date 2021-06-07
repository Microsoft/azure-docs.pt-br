---
title: Associação de saída do armazenamento de BLOBs do Azure para Azure Functions
description: Saiba como fornecer dados de associação de saída do armazenamento de BLOBs do Azure para uma função do Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 78cbf94bfc19757a4264c7884c3e47b230de5e46
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044083"
---
# <a name="azure-blob-storage-output-binding-for-azure-functions"></a>Associação de saída do armazenamento de BLOBs do Azure para Azure Functions

A associação de saída permite que você modifique e exclua dados de armazenamento de BLOBs em uma função do Azure.

Para obter informações sobre a instalação e detalhes de configuração, confira a [visão geral](./functions-bindings-storage-blob.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir é uma [função C#](functions-dotnet-class-library.md) que usa um gatilho de blob e duas associações de blob de saída. A função é gatilhada pela criação de um blob de imagem no contêiner *imagens de amostra*. Isso cria cópias de pequeno e médio tamanho de blob de imagem.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

public class ResizeImages
{
    [FunctionName("ResizeImage")]
    public static void Run([BlobTrigger("sample-images/{name}")] Stream image,
        [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
        [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
    {
        IImageFormat format;

        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageSmall, ImageSize.Small, format);
        }

        image.Position = 0;
        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageMedium, ImageSize.Medium, format);
        }
    }

    public static void ResizeImage(Image<Rgba32> input, Stream output, ImageSize size, IImageFormat format)
    {
        var dimensions = imageDimensionsTable[size];

        input.Mutate(x => x.Resize(dimensions.Item1, dimensions.Item2));
        input.Save(output, format);
    }

    public enum ImageSize { ExtraSmall, Small, Medium }

    private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
        { ImageSize.ExtraSmall, (320, 200) },
        { ImageSize.Small,      (640, 400) },
        { ImageSize.Medium,     (800, 600) }
    };

}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

<!--Same example for input and output. -->

O exemplo a seguir mostra uma associação de entrada e de saída de blob em um arquivo *function.json* e um código [script C# (.csx)](functions-reference-csharp.md) que usa as associações. A função faz uma cópia de um blob de texto. A função é disparada por uma mensagem da fila que contém o nome do blob para copiar. O novo BLOB é denominado *{originalblobname}-Copy*.

No arquivo *function.json*, a `queueTrigger` propriedade de metadados é usada para especificar o nome do blob nas propriedades `path`:

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
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código de script do C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="java"></a>[Java](#tab/java)

Esta seção contém os seguintes exemplos:

* [Gatilho HTTP, usando OutputBinding](#http-trigger-using-outputbinding-java)
* [Gatilho de fila, usando o valor de retorno de função](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>Gatilho HTTP, usando OutputBinding (Java)

 O exemplo a seguir mostra uma função de Java que usa a anotação `HttpTrigger` para receber um parâmetro que contém o nome de um arquivo em um contêiner de armazenamento de blob. Em seguida, a anotação `BlobInput` lê o arquivo e passa seu conteúdo para a função como um `byte[]`. A anotação `BlobOutput` associa-se a `OutputBinding outputItem`, que é usado pela função para gravar o conteúdo do blob de entrada para o contêiner de armazenamento configurado.

```java
  @FunctionName("copyBlobHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage copyBlobHttp(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    @BlobOutput(
      name = "target", 
      path = "myblob/{Query.file}-CopyViaHttp")
    OutputBinding<String> outputItem,
    final ExecutionContext context) {
      // Save blob to outputItem
      outputItem.setValue(new String(content, StandardCharsets.UTF_8));

      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-using-function-return-value-java"></a>Gatilho de fila, usando o valor de retorno de função (Java)

 O exemplo a seguir mostra uma função de Java que usa a anotação `QueueTrigger` para receber uma mensagem que contém o nome de um arquivo em um contêiner de armazenamento de blob. Em seguida, a anotação `BlobInput` lê o arquivo e passa seu conteúdo para a função como um `byte[]`. A anotação `BlobOutput` associa-se ao valor de retorno da função, que é usado pelo runtime para gravar o conteúdo do blob de entrada para o contêiner de armazenamento configurado.

```java
  @FunctionName("copyBlobQueueTrigger")
  @StorageAccount("Storage_Account_Connection_String")
  @BlobOutput(
    name = "target", 
    path = "myblob/{queueTrigger}-Copy")
  public String copyBlobQueue(
    @QueueTrigger(
      name = "filename", 
      dataType = "string",
      queueName = "myqueue-items") 
    String filename,
    @BlobInput(
      name = "file", 
      path = "samples-workitems/{queueTrigger}") 
    String content,
    final ExecutionContext context) {
      context.getLogger().info("The content of \"" + filename + "\" is: " + content);
      return content;
  }
```

 Na [biblioteca de runtime das funções Java](/java/api/overview/azure/functions/runtime) , use a anotação `@BlobOutput` nos parâmetros da função cujo valor seria gravado em um objeto no armazenamento de blobs.  O tipo de parâmetro deve ser `OutputBinding<T>`, onde T é qualquer tipo Java nativo ou um POJO.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

O exemplo a seguir mostra uma associação de entrada e saída de blob em um arquivo *function.json* e [código JavaScript](functions-reference-node.md) que usa as associações. A função faz uma cópia de um blob. A função é disparada por uma mensagem da fila que contém o nome do blob para copiar. O novo BLOB é denominado *{originalblobname}-Copy*.

No arquivo *function.json*, a `queueTrigger` propriedade de metadados é usada para especificar o nome do blob nas propriedades `path`:

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
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O exemplo a seguir demonstra como criar uma cópia de um blob de entrada como a saída de uma [função do PowerShell](functions-reference-powershell.md).

No arquivo de configuração da função (*function.jsem*), a `trigger` Propriedade Metadata é usada para especificar o nome do blob de saída nas `path` Propriedades.

> [!NOTE]
> Para evitar loops infinitos, verifique se os caminhos de entrada e saída são diferentes.

```json
{
  "bindings": [
    {
      "name": "myInputBlob",
      "path": "data/{trigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in",
      "type": "blobTrigger"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "data/copy/{trigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Este é o código do PowerShell:

```powershell
# Input bindings are passed in via param block.
param([byte[]] $myInputBlob, $TriggerMetadata)
Write-Host "PowerShell Blob trigger function Processed blob Name: $($TriggerMetadata.Name)"
Push-OutputBinding -Name myOutputBlob -Value $myInputBlob
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

O exemplo a seguir mostra uma associação de entrada e de saída de blob em um arquivo *function.json* e um [código Python](functions-reference-python.md) que usa as associações. A função faz uma cópia de um blob. A função é disparada por uma mensagem da fila que contém o nome do blob para copiar. O novo BLOB é denominado *{originalblobname}-Copy*.

No arquivo *function.json*, a `queueTrigger` propriedade de metadados é usada para especificar o nome do blob nas propriedades `path`:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "outputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

A seção [configuração](#configuration) explica essas propriedades.

Aqui está o código Python:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), use o [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

O construtor do atributo usa o caminho para o blob e um parâmetro `FileAccess` que indica a leitura ou gravação, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Você pode definir a `Connection` propriedade para especificar a conta de armazenamento para usar, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O script C# não dá suporte a atributos.

# <a name="java"></a>[Java](#tab/java)

O `@BlobOutput` atributo fornece acesso ao blob que disparou a função. Se você usar uma matriz de bytes com o atributo, defina `dataType` como `binary` . Consulte o [exemplo de saída](#example) para obter detalhes.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O JavaScript não dá suporte a atributos.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Não há suporte para atributos pelo PowerShell.

# <a name="python"></a>[Python](#tab/python)

O Python não dá suporte a atributos.

---

Para obter um exemplo completo, consulte [exemplo de saída](#example).

Você pode usar o `StorageAccount` atributo para especificar a conta de armazenamento no nível de classe, método ou parâmetro. Para obter mais informações, consulte [Trigger-Attributes](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `Blob`.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como `blob`. |
|**direction** | n/d | Deve ser definido como `out` para uma associação de saída. As exceções são mencionadas na seção [uso](#usage). |
|**name** | n/d | O nome da variável que representa o blob no código de função.  Definido como `$return` para referenciar o valor de retorno da função.|
|**path** |**BlobPath** | O caminho para o contêiner de BLOB. |
|**connection** |**Conexão**| O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui. Por exemplo, se você configurar `connection` para “MyStorage”, o runtime do Functions procura por uma configuração de aplicativo que esteja nomeada “AzureWebJobsMyStorage." Se você deixar `connection` vazio, o runtime de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.<br><br>A cadeia de conexão deve ser uma conta de armazenamento de finalidade geral e não uma [conta de armazenamento de blobs](../storage/common/storage-account-overview.md#types-of-storage-accounts).<br><br>Caso esteja usando a [versão 5.x ou posterior da extensão](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher), em vez de uma cadeia de conexão, será possível fornecer uma referência a uma seção de configuração que definirá a conexão. Confira a opção [Conexões](./functions-reference.md#connections).|
|N/D | **Acesso** | Indica se você será leitura ou gravação. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="java"></a>[Java](#tab/java)

O `@BlobOutput` atributo fornece acesso ao blob que disparou a função. Se você usar uma matriz de bytes com o atributo, defina `dataType` como `binary` . Consulte o [exemplo de saída](#example) para obter detalhes.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Acesse os dados de BLOB usando `context.bindings.<BINDING_NAME>` , onde o nome da associação é definido no _function.jsno_ arquivo.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Acesse os dados de blob por meio de um parâmetro que corresponde ao nome designado pelo parâmetro de nome da associação na _function.jsno_ arquivo.

# <a name="python"></a>[Python](#tab/python)

Você pode declarar parâmetros de função como os seguintes tipos para gravar no armazenamento de BLOBs:

* Cadeias de caracteres como `func.Out[str]`
* Fluxos como `func.Out[func.InputStream]`

Consulte o [exemplo de saída](#example) para obter detalhes.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Associação |  Referência |
|---|---|
| Blob | [Códigos de erro de Blob](/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob, tabela, fila |  [Códigos de erro de armazenamento](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, tabela, fila |  [Solução de problemas](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Próximas etapas

- [Executar uma função quando os dados do armazenamento de BLOBs forem alterados](./functions-bindings-storage-blob-trigger.md)
- [Ler dados do armazenamento de BLOBs quando uma função é executada](./functions-bindings-storage-blob-input.md)
