---
title: Associações de armazenamento do Blob do Azure para o Azure Functions
description: Entenda como usar gatilhos e associações do Armazenamento de blob do Azure em Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/15/2018
ms.author: cshoe
ms.openlocfilehash: 3e67737e26edfee94a5a4d740d6c575817c66ff0
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766181"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Associações de armazenamento do Blob do Azure para o Azure Functions

Este artigo explica como trabalhar com associações de armazenamento de blob do Azure no Azure Functions. O Azure Functions dá suporte a associações de saída, gatilho e entrada para blobs. O artigo inclui uma seção para cada associação:

* [Gatilho de blob](#trigger)
* [Associação de entrada de blob](#input)
* [Associação de saída de blob](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Use o gatilho de Grade de Eventos em vez do disparador do armazenamento de Blob apenas para contas de armazenamento de blob, para alta escala ou para reduzir a latência. Para saber mais, veja a seção [Gatilho](#trigger) a seguir.

## <a name="packages---functions-1x"></a>Pacotes - Functions 1. x

As associações de armazenamento de Blob são fornecidas no [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) pacote NuGet, versão 2. x. O código-fonte do pacote está no repositório GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob).

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Pacotes - Functions 2. x

As associações de armazenamento de Blobs são fornecidas no pacote NuGet [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage), versão 3.x. O código-fonte do pacote está no repositório GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Gatilho

O gatilho de armazenamento de Blob inicia uma função quando é detectado um blob novo ou atualizado. O conteúdo do blob é fornecido como entrada para a função.

O [gatilho de Grade de Eventos](functions-bindings-event-grid.md) tem suporte interno para [eventos de blob](../storage/blobs/storage-blob-event-overview.md) e também pode ser usado para iniciar uma função quando um blob novo ou atualizado é detectado. Para obter um exemplo, consulte o tutorial [Redimensionamento de imagem com Grade de Eventos](../event-grid/resize-images-on-storage-blob-upload-event.md).

Use a Grade de Eventos em vez do disparador de armazenamento de Blobs para os seguintes cenários:

* Contas de armazenamento de Blobs
* Alta escala
* Minimizando a latência

### <a name="blob-storage-accounts"></a>Contas de armazenamento de Blobs

As [contas de Armazenamento de Blobs](../storage/common/storage-account-overview.md#types-of-storage-accounts) são compatíveis para associações de entrada e saída de blobs, mas não para gatilhos de blob. Os gatilhos de armazenamento de Blobs requerem uma conta de armazenamento de uso geral.

### <a name="high-scale"></a>Alta escala

A alta escala pode ser definida vagamente como contêineres que possuem mais de 100.000 blobs ou contas de armazenamento que têm mais de 100 atualizações de blobs por segundo.

### <a name="latency-issues"></a>Problemas de latência

Se seu aplicativo de funções está no plano de Consumo, pode haver um atraso de até 10 minutos no processamento de novos blobs se um aplicativo de funções ficar ocioso. Para evitar essa latência, você pode alternar para um plano do serviço de aplicativo com o Always On habilitado. Você também pode usar um [gatilho da Grade de Eventos](functions-bindings-event-grid.md) com sua conta de armazenamento de Blob. Para obter um exemplo, confira o [tutorial da Grade de Eventos](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json). 

### <a name="queue-storage-trigger"></a>Gatilho de armazenamento de filas

Além da Grade de Eventos, outra alternativa para blobs de processamento é o gatilho de armazenamento de Fila, mas ele não tem suporte interno para eventos de blob. Você precisaria criar mensagens de fila ao criar ou atualizar blobs. Suponto que você fez isso, consulte o [exemplo de associação de entrada de blob mais adiante neste artigo](#input---example).

## <a name="trigger---example"></a>Gatilho - exemplo

Consulte o exemplo específico a um idioma:

* [C#](#trigger---c-example)
* [Script do C# (.csx)](#trigger---c-script-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Gatilho - exemplo C#

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que grava um log quando um blob é adicionado ou atualizado no contêiner `samples-workitems`.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

A cadeia de caracteres `{name}` no caminho do disparador de blob `samples-workitems/{name}` cria uma [expressão de associação](./functions-bindings-expressions-patterns.md) que você pode usar no código de função para acessar o nome de arquivo do blob disparando. Para obter mais informações, consulte [Padrões de nome do blob](#trigger---blob-name-patterns) a seguir neste artigo.

Para obter mais informações sobre o atributo `BlobTrigger`, consulte [Gatilho - atributos](#trigger---attributes).

### <a name="trigger---c-script-example"></a>Gatilho - exemplo de script C#

O exemplo a seguir mostra uma associação de disparo de blob em um arquivo *function.json* e [código Python](functions-reference-python.md) que usa a associação. A função grava um log quando um blob é adicionado ou atualizado no `samples-workitems` [contêiner](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

A cadeia de caracteres `{name}` no caminho do disparador de blob `samples-workitems/{name}` cria uma [expressão de associação](./functions-bindings-expressions-patterns.md) que você pode usar no código de função para acessar o nome de arquivo do blob disparando. Para obter mais informações, consulte [Padrões de nome do blob](#trigger---blob-name-patterns) a seguir neste artigo.

Para obter mais informações sobre as propriedades do arquivo *function.json*, consulte a seção [Configuração](#trigger---configuration) que explica essas propriedades.

Aqui está o código script C# que associa a um `Stream`:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Aqui está o código script C# que associa a um `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>Gatilho - exemplo de JavaScript

O exemplo a seguir mostra uma associação de gatilho de blob em um arquivo *function.json* e [código JavaScript](functions-reference-node.md) que usa a associação. A função grava um log quando um blob é adicionado ou atualizado no `samples-workitems` contêiner.

Aqui está o arquivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

A cadeia de caracteres `{name}` no caminho do disparador de blob `samples-workitems/{name}` cria uma [expressão de associação](./functions-bindings-expressions-patterns.md) que você pode usar no código de função para acessar o nome de arquivo do blob disparando. Para obter mais informações, consulte [Padrões de nome do blob](#trigger---blob-name-patterns) a seguir neste artigo.

Para obter mais informações sobre as propriedades do arquivo *function.json*, consulte a seção [Configuração](#trigger---configuration) que explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

### <a name="trigger---python-example"></a>Gatilho – exemplo do Python

O exemplo a seguir mostra uma associação de disparo de blob em um arquivo *function.json* e [código Python](functions-reference-python.md) que usa a associação. A função grava um log quando um blob é adicionado ou atualizado no `samples-workitems` [contêiner](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Aqui está o arquivo *function.json*:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

A cadeia de caracteres `{name}` no caminho do disparador de blob `samples-workitems/{name}` cria uma [expressão de associação](./functions-bindings-expressions-patterns.md) que você pode usar no código de função para acessar o nome de arquivo do blob disparando. Para obter mais informações, consulte [Padrões de nome do blob](#trigger---blob-name-patterns) a seguir neste artigo.

Para obter mais informações sobre as propriedades do arquivo *function.json*, consulte a seção [Configuração](#trigger---configuration) que explica essas propriedades.

Aqui está o código Python:

```python
import logging
import azure.functions as func

def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

### <a name="trigger---java-example"></a>Gatilho - exemplo Java

O exemplo a seguir mostra uma ligação de disparo de blob em um arquivo *function.json* e [código Java](functions-reference-java.md) que usa a ligação. A função grava um log quando um blob é adicionado ou atualizado no `myblob` contêiner.

Aqui está o arquivo *function.json*:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "file",
            "type": "blobTrigger",
            "direction": "in",
            "path": "myblob/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Aqui está o código Java:

```java
@FunctionName("blobprocessor")
public void run(
  @BlobTrigger(name = "file",
               dataType = "binary",
               path = "myblob/{name}",
               connection = "MyStorageAccountAppSetting") byte[] content,
  @BindingName("name") String filename,
  final ExecutionContext context
) {
  context.getLogger().info("Name: " + filename + " Size: " + content.length + " bytes");
}
```


## <a name="trigger---attributes"></a>Gatilho – atributos

Em [bibliotecas de classe C#](functions-dotnet-class-library.md), use os seguintes atributos para configurar um gatilho de blob:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs)

  O construtor do atributo usa uma cadeia de caracteres de caminho que indica o contêiner para inspecionar e, opcionalmente, um [padrão de nome de blob](#trigger---blob-name-patterns). Aqui está um exemplo:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Você pode definir a `Connection` propriedade para especificar a conta de armazenamento para usar, conforme mostrado no exemplo a seguir:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Para ver um exemplo completo, consulte [Gatilho – exemplo de C#](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Oferece uma maneira de especificar a conta de armazenamento para usar. O construtor toma o nome de uma configuração de aplicativo que contenha uma cadeia de conexão de armazenamento. O atributo pode ser aplicado no nível de classe, método ou parâmetro. O exemplo a seguir mostra o nível de classe e método:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

A conta de armazenamento a ser usada é determinada na seguinte ordem:

* A propriedade `BlobTrigger` do atributo`Connection`.
* O `StorageAccount` atributo aplicado ao mesmo parâmetro do `BlobTrigger` atributo.
* O `StorageAccount` atributo aplicado à função.
* O `StorageAccount` atributo aplicado à classe.
* A conta de armazenamento padrão para a função de aplicativo (configuração de aplicativo "AzureWebJobsStorage").

## <a name="trigger---configuration"></a>Gatilho – configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `BlobTrigger`.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como `blobTrigger`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | n/d | Deve ser definido como `in`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. As exceções são mencionadas na seção [uso](#trigger---usage). |
|**name** | n/d | O nome da variável que representa o blob no código de função. |
|**path** | **BlobPath** |O [contêiner](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) para monitorar.  Pode ser um [padrão de nome de blob](#trigger---blob-name-patterns). |
|**conexão** | **Conexão** | O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui. Por exemplo, se você configurar `connection` para “MyStorage”, o tempo de execução do Functions procura por uma configuração de aplicativo que esteja nomeada “AzureWebJobsMyStorage." Se você deixar `connection` vazio, o tempo de execução de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.<br><br>A cadeia de conexão deve ser uma conta de armazenamento para uso geral e não uma [conta de Armazenamento de Blobs](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Gatilho - uso

Em C# e script C#, você pode usar os tipos de parâmetros a seguir para o blob disparando:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* Um POCO serializado como JSON
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> Requer associação "inout" `direction` em *function.json* ou `FileAccess.ReadWrite` em uma biblioteca de classes C#.

Se você tentar associar a um dos tipos de SDK de armazenamento e obter uma mensagem de erro, certifique-se de que você tem uma referência a [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x).

Associação para `string`, `Byte[]`, ou POCO só é recomendada se o tamanho do blob for pequeno, pois o conteúdo inteiro do blob é carregado na memória. Geralmente, é preferível usar um tipo `Stream` ou `CloudBlockBlob`. Para obter mais informações, consulte [Concorrência e uso de memória](#trigger---concurrency-and-memory-usage) mais adiante neste artigo.

Em JavaScript, acesse os dados do blob de entrada usando `context.bindings.<name from function.json>`.

## <a name="trigger---blob-name-patterns"></a>Gatilho - padrões de nome de blob

Você pode especificar um padrão de nome de blob na `path` propriedade em *function.json* ou no `BlobTrigger` construtor de atributo. O nome padrão pode ser uma [expressão de associação ou filtro](./functions-bindings-expressions-patterns.md). As seções a seguir fornecem exemplos.

### <a name="get-file-name-and-extension"></a>Obtenha o nome de arquivo e extensão

O exemplo a seguir mostra como associar ao nome do arquivo de blob e extensão separadamente:

```json
"path": "input/{blobname}.{blobextension}",
```
Se um blob é nomeado *original-Blob1.txt* o valor das variáveis `blobname` e `blobextension` no código de função é *original-Blob1* e *txt*.

### <a name="filter-on-blob-name"></a>Filtre por nome de blob

O exemplo a seguir gatilha apenas em blobs no `input` contêiner que iniciam com a cadeia de caracteres "original-":

```json
"path": "input/original-{name}",
```

Se o nome do blob for *original-Blob1.txt*, o valor da `name` variável no código da função é `Blob1`.

### <a name="filter-on-file-type"></a>Filtre por tipo de arquivo

O exemplo a seguir é disparado apenas em arquivos *.png*:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filtre em chaves em nomes de arquivos

Para procurar as chaves em nomes de arquivos, escape as chaves usando duas chaves. O exemplo a seguir filtra por blobs que têm chaves no nome:

```json
"path": "images/{{20140101}}-{name}",
```

Se o blob é nomeado *{20140101}soundfile.mp3*, o valor da variável `name` no código da função é *soundfile.mp3*.

## <a name="trigger---metadata"></a>Gatilho - metadados

O gatilho de blob fornece várias propriedades de metadados. Essas propriedades podem ser usadas como parte de expressões de associação em outras associações ou como parâmetros em seu código. Esses valores têm a mesma semântica que o tipo [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet).

|Propriedade  |Type  |DESCRIÇÃO  |
|---------|---------|---------|
|`BlobTrigger`|`string`|O caminho do blob de gatilho.|
|`Uri`|`System.Uri`|A URI do blob para o local principal.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|As propriedades do sistema do blob. |
|`Metadata` |`IDictionary<string,string>`|Os metadados definidos pelo usuário para o blob.|

Por exemplo, o script C# e exemplos de JavaScript a seguir registram o caminho para o blob disparando, incluindo o contêiner:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

## <a name="trigger---blob-receipts"></a>Gatilho - recebimentos de blob

O tempo de execução do Azure Functions garante que nenhuma função de gatilho de blob seja chamada mais de uma vez para o mesmo blob novo ou atualizado. Para determinar se uma versão de determinado blob foi processada, ele mantém os *recebimentos de blob*.

O Azure Functions armazena recibos do blob em um contêiner denominado *azure-webjobs-hosts* na conta de armazenamento do Azure do seu aplicativo de funções (definido na configuração do aplicativo `AzureWebJobsStorage`). Um recebimento de blob tem as seguintes informações:

* A função disparada ("*&lt;nome do aplicativo de funções>*.Functions.*&lt;nome da função>*", por exemplo: "MyFunctionApp.Functions.CopyBlob")
* O nome do contêiner
* O tipo de blob ("BlockBlob" ou "PageBlob")
* O nome do blob
* O ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

Para forçar o reprocessamento de um blob, exclua manualmente o recebimento desse blob do contêiner *azure-webjobs-hosts*. Enquanto o reprocessamento pode não ocorrer imediatamente, é garantida para ocorrer em um momento posterior no tempo.

## <a name="trigger---poison-blobs"></a>Gatilho - mensagens suspeitas

Quando uma função de gatilho de blob falhar para um determinado blob, o Azure Functions repete essa função até cinco vezes por padrão.

Se todas as cinco tentativas falharem, o Azure Functions adiciona uma mensagem para uma fila de armazenamento denominada *webjobs-blobtrigger-poison*. A mensagem da fila para blobs suspeitos é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no formato *&lt;nome do aplicativo de funções>*.Functions.*&lt;nome da função>*)
* BlobType ("BlockBlob" ou "PageBlob")
* ContainerName
* BlobName
* ETag (um identificador de versão de blob, por exemplo: "0x8D1DC6E70A277EF")

## <a name="trigger---concurrency-and-memory-usage"></a>Gatilho - concorrência e uso de memória

O gatilho de blob usa uma fila internamente, portanto, o número máximo de invocações de função concorrente é controlado pela [configuração de filas em host.json](functions-host-json.md#queues). As configurações padrão limitam a concorrência a 24 invocações. Esse limite aplica-se separadamente a cada função que usa um gatilho de blob.

[O plano de consumo](functions-scale.md#how-the-consumption-and-premium-plans-work) limita um aplicativo de funções em uma VM (máquina virtual) a 1,5 GB de memória. A memória é usada por cada instância de execução de execução simultânea e pelo próprio tempo de execução de Funções. Se uma função disparada por blob carregar todo o blob na memória, a memória máxima usada por essa função apenas para blobs será tamanho máximo de blob 24 *. Por exemplo, um aplicativo de funções com três funções disparadas por blob e as configurações padrão teriam uma concorrência máxima por VM de 3*24 = 72 invocações de função.

As funções de JavaScript e Java carregam todo o blob na memória, e as funções C# fazem isso se você associar a `string`, `Byte[]` ou POCO.

## <a name="trigger---polling"></a>Disparar - sondagem

Se o contêiner de blob que está sendo monitorado contiver mais de 10.000 blobs (em todos os contêineres), as verificações de tempo de execução do Functions varrerão os arquivos para observar blobs novos ou alterados de log. Esse processo pode resultar em atrasos. Uma função não poderá ser disparada até que se passem vários minutos ou mais tempo depois da criação do blob.

> [!WARNING]
> Além disso, [logs de armazenamento são criados da "melhor forma dentro do possível"](/rest/api/storageservices/About-Storage-Analytics-Logging). Não há nenhuma garantia de que todos os eventos são capturados. Sob algumas condições, logs poderão ser perdidos.
> 
> Se você precisar de um processamento de blob mais rápido ou confiável, crie uma [mensagem de fila](../storage/queues/storage-dotnet-how-to-use-queues.md) ao criar o blob. Em seguida, use um [gatilho de fila](functions-bindings-storage-queue.md) em vez de um gatilho de blob para processar o blob. Outra opção é usar a Grade de Eventos; consulte o tutorial [Automatize redimensionamento de imagens carregadas usando a Grade de Eventos](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="input"></a>Entrada

Use uma associação de entrada de Armazenamento de Blobs para ler blobs.

## <a name="input---example"></a>Entrada - exemplo

Consulte o exemplo específico a um idioma:

* [C#](#input---c-example)
* [Script do C# (.csx)](#input---c-script-example)
* [Java](#input---java-examples)
* [JavaScript](#input---javascript-example)
* [Python](#input---python-example)

### <a name="input---c-example"></a>Entrada – exemplo de C#

O exemplo a seguir é uma [função C#](functions-dotnet-class-library.md) que usa um gatilho de fila e uma associação de blob de entrada. A mensagem da fila contém o nome do blob e a função registra o tamanho do blob.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

### <a name="input---c-script-example"></a>Entrada - exemplo de script C#

<!--Same example for input and output. -->

O exemplo a seguir mostra uma associação de entrada e de saída de blob em um arquivo *function.json* e um código [script C# (.csx)](functions-reference-csharp.md) que usa as associações. A função faz uma cópia de um blob de texto. A função é disparada por uma mensagem da fila que contém o nome do blob para copiar. O novo blob é nomeado *{originalblobname}-Copy*.

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

A seção [configuração](#input---configuration) explica essas propriedades.

Aqui está o código de script do C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input---javascript-example"></a>Entrada - exemplo de JavaScript

<!--Same example for input and output. -->

O exemplo a seguir mostra uma associação de entrada e saída de blob em um arquivo *function.json* e [código JavaScript](functions-reference-node.md) que usa as associações. A função faz uma cópia de um blob. A função é disparada por uma mensagem da fila que contém o nome do blob para copiar. O novo blob é nomeado *{originalblobname}-Copy*.

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

A seção [configuração](#input---configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

### <a name="input---python-example"></a>Entrada – exemplo do Python

<!--Same example for input and output. -->

O exemplo a seguir mostra uma associação de entrada e de saída de blob em um arquivo *function.json* e um [código Python](functions-reference-python.md) que usa as associações. A função faz uma cópia de um blob. A função é disparada por uma mensagem da fila que contém o nome do blob para copiar. O novo blob é nomeado *{originalblobname}-Copy*.

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
      "name": "$return",
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

A seção [configuração](#input---configuration) explica essas propriedades.

Aqui está o código Python:

```python
import logging
import azure.functions as func

def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

### <a name="input---java-examples"></a>Entrada - Exemplos Java

Esta seção contém os seguintes exemplos:

* [Gatilho HTTP, o nome do blob da cadeia de caracteres de consulta](#http-trigger-look-up-blob-name-from-query-string-java)
* [Gatilho de fila, receber o nome do blob da mensagem da fila](#queue-trigger-receive-blob-name-from-queue-message-java)

#### <a name="http-trigger-look-up-blob-name-from-query-string-java"></a>Gatilho HTTP, o nome do blob da cadeia de caracteres de consulta (Java)

 O exemplo a seguir mostra uma função de Java que usa a anotação ```HttpTrigger``` para receber um parâmetro que contém o nome de um arquivo em um contêiner de armazenamento de blob. Em seguida, a anotação ```BlobInput``` lê o arquivo e passa seu conteúdo para a função como um ```byte[]```.

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message-java"></a>Gatilho de fila, receber o nome do blob da mensagem da fila (Java)

 O exemplo a seguir mostra uma função de Java que usa a anotação ```QueueTrigger``` para receber uma mensagem que contém o nome de um arquivo em um contêiner de armazenamento de blob. Em seguida, a anotação ```BlobInput``` lê o arquivo e passa seu conteúdo para a função como um ```byte[]```.

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

Na biblioteca de tempo de execução de funções [Java](/java/api/overview/azure/functions/runtime), use a anotação `@BlobInput` nos parâmetros cujo valor viria de um blob.  Essa anotação pode ser usada com tipos nativos do Java, POJOs ou valores que permitem valor nulos usando `Optional<T>`.

## <a name="input---attributes"></a>Entrada – atributos

Em [bibliotecas de classes do C#](functions-dotnet-class-library.md), use o [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

O construtor do atributo usa o caminho para o blob e um parâmetro `FileAccess` que indica a leitura ou gravação, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

Você pode definir a `Connection` propriedade para especificar a conta de armazenamento para usar, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Você pode usar o `StorageAccount` atributo para especificar a conta de armazenamento no nível de classe, método ou parâmetro. Para obter mais informações, consulte [Gatilho - atributos](#trigger---attributes).

## <a name="input---configuration"></a>Entrada - configuração

A tabela a seguir explica as propriedades de configuração de associação que você definir no arquivo *function.json* e o `Blob` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como `blob`. |
|**direction** | n/d | Deve ser definido como `in`. As exceções são mencionadas na seção [uso](#input---usage). |
|**name** | n/d | O nome da variável que representa o blob no código de função.|
|**path** |**BlobPath** | O caminho para o blob. |
|**conexão** |**Conexão**| O nome de uma configuração de aplicativo que contém uma cadeia de conexão de [Armazenamento para usar para essa associação](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-azure-storage-account). Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui. Por exemplo, se você configurar `connection` para “MyStorage”, o tempo de execução do Functions procura por uma configuração de aplicativo que esteja nomeada “AzureWebJobsMyStorage." Se você deixar `connection` vazio, o tempo de execução de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.<br><br>A cadeia de conexão deve ser uma conta de armazenamento de finalidade geral e não uma [conta de armazenamento de blobs](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|n/d | **Access** | Indica se você será leitura ou gravação. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Entrada - uso

Em C# e script C#, você pode usar os tipos de parâmetros a seguir para a associação de entrada de blob:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> Requer associação "inout" `direction` em *function.json* ou `FileAccess.ReadWrite` em uma biblioteca de classes C#.

Se você tentar associar a um dos tipos de SDK de armazenamento e obter uma mensagem de erro, certifique-se de que você tem uma referência a [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x).

Associação para `string` ou `Byte[]` só é recomendada se o tamanho do blob for pequeno, pois o conteúdo inteiro do blob é carregado na memória. Geralmente, é preferível usar um tipo `Stream` ou `CloudBlockBlob`. Para obter mais informações, consulte [Concorrência e uso de memória](#trigger---concurrency-and-memory-usage) mais adiante neste artigo.

Em JavaScript, acesse os dados do blob usando `context.bindings.<name from function.json>`.

## <a name="output"></a>Saída

Usar as associações de saída do Armazenamento de Blobs para gravar os blobs.

## <a name="output---example"></a>Saída - exemplo

Consulte o exemplo específico a um idioma:

* [C#](#output---c-example)
* [Script do C# (.csx)](#output---c-script-example)
* [Java](#output---java-examples)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Saída - exemplo C#

O exemplo a seguir é uma [função C#](functions-dotnet-class-library.md) que usa um gatilho de blob e duas associações de blob de saída. A função é gatilhada pela criação de um blob de imagem no contêiner *imagens de amostra*. Isso cria cópias de pequeno e médio tamanho de blob de imagem.

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```

### <a name="output---c-script-example"></a>Saída - exemplo de script C#

<!--Same example for input and output. -->

O exemplo a seguir mostra uma associação de entrada e de saída de blob em um arquivo *function.json* e um código [script C# (.csx)](functions-reference-csharp.md) que usa as associações. A função faz uma cópia de um blob de texto. A função é disparada por uma mensagem da fila que contém o nome do blob para copiar. O novo blob é nomeado *{originalblobname}-Copy*.

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

A seção [configuração](#output---configuration) explica essas propriedades.

Aqui está o código de script do C#:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="output---javascript-example"></a>Saída - exemplo JavaScript

<!--Same example for input and output. -->

O exemplo a seguir mostra uma associação de entrada e saída de blob em um arquivo *function.json* e [código JavaScript](functions-reference-node.md) que usa as associações. A função faz uma cópia de um blob. A função é disparada por uma mensagem da fila que contém o nome do blob para copiar. O novo blob é nomeado *{originalblobname}-Copy*.

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

A seção [configuração](#output---configuration) explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

### <a name="output---python-example"></a>Saída – exemplo do Python

<!--Same example for input and output. -->

O exemplo a seguir mostra uma associação de entrada e de saída de blob em um arquivo *function.json* e um [código Python](functions-reference-python.md) que usa as associações. A função faz uma cópia de um blob. A função é disparada por uma mensagem da fila que contém o nome do blob para copiar. O novo blob é nomeado *{originalblobname}-Copy*.

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

A seção [configuração](#output---configuration) explica essas propriedades.

Aqui está o código Python:

```python
import logging
import azure.functions as func

def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

### <a name="output---java-examples"></a>Saída - Exemplo Java

Esta seção contém os seguintes exemplos:

* [Gatilho HTTP, usando OutputBinding](#http-trigger-using-outputbinding-java)
* [Gatilho de fila, usando o valor de retorno de função](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>Gatilho HTTP, usando OutputBinding (Java)

 O exemplo a seguir mostra uma função de Java que usa a anotação ```HttpTrigger``` para receber um parâmetro que contém o nome de um arquivo em um contêiner de armazenamento de blob. Em seguida, a anotação ```BlobInput``` lê o arquivo e passa seu conteúdo para a função como um ```byte[]```. A anotação ```BlobOutput``` associa-se a ```OutputBinding outputItem```, que é usado pela função para gravar o conteúdo do blob de entrada para o contêiner de armazenamento configurado.

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

 O exemplo a seguir mostra uma função de Java que usa a anotação ```QueueTrigger``` para receber uma mensagem que contém o nome de um arquivo em um contêiner de armazenamento de blob. Em seguida, a anotação ```BlobInput``` lê o arquivo e passa seu conteúdo para a função como um ```byte[]```. A anotação ```BlobOutput``` associa-se ao valor de retorno da função, que é usado pelo tempo de execução para gravar o conteúdo do blob de entrada para o contêiner de armazenamento configurado.

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

 Na [biblioteca de tempo de execução das funções Java](/java/api/overview/azure/functions/runtime) , use a anotação `@BlobOutput` nos parâmetros da função cujo valor seria gravado em um objeto no armazenamento de blobs.  O tipo de parâmetro deve ser `OutputBinding<T>`, onde T é qualquer tipo Java nativo ou um POJO.

## <a name="output---attributes"></a>Saída - atributos

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

Para ver um exemplo completo, consulte [Saída – exemplo de C#](#output---c-example).

Você pode usar o `StorageAccount` atributo para especificar a conta de armazenamento no nível de classe, método ou parâmetro. Para obter mais informações, consulte [Gatilho - atributos](#trigger---attributes).

## <a name="output---configuration"></a>Saída - configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no `Blob` atributo.

|Propriedade function.json | Propriedade de atributo |DESCRIÇÃO|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como `blob`. |
|**direction** | n/d | Deve ser definido como `out` para uma associação de saída. As exceções são mencionadas na seção [uso](#output---usage). |
|**name** | n/d | O nome da variável que representa o blob no código de função.  Definido como `$return` para referenciar o valor de retorno da função.|
|**path** |**BlobPath** | O caminho para o blobco. |
|**conexão** |**Conexão**| O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui. Por exemplo, se você configurar `connection` para “MyStorage”, o tempo de execução do Functions procura por uma configuração de aplicativo que esteja nomeada “AzureWebJobsMyStorage." Se você deixar `connection` vazio, o tempo de execução de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.<br><br>A cadeia de conexão deve ser uma conta de armazenamento de finalidade geral e não uma [conta de armazenamento de blobs](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|n/d | **Access** | Indica se você será leitura ou gravação. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - uso

Em script C# e C#, é possível associar os tipos a seguir para gravar blobs:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> Requer associação "in" `direction` em *function.json* ou `FileAccess.Read` em uma biblioteca de classes C#. No entanto, você pode usar o objeto de contêiner que o tempo de execução fornece para operações de gravação, como carregar blobs no contêiner.

<sup>2</sup> Requer associação "inout" `direction` em *function.json* ou `FileAccess.ReadWrite` em uma biblioteca de classes C#.

Se você tentar associar a um dos tipos de SDK de armazenamento e obter uma mensagem de erro, certifique-se de que você tem uma referência a [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x).

Em funções assíncronas, use o valor de retorno ou `IAsyncCollector` em vez de um parâmetro `out`.

Associação para `string` ou `Byte[]` só é recomendada se o tamanho do blob for pequeno, pois o conteúdo inteiro do blob é carregado na memória. Geralmente, é preferível usar um tipo `Stream` ou `CloudBlockBlob`. Para obter mais informações, consulte [Concorrência e uso de memória](#trigger---concurrency-and-memory-usage) mais adiante neste artigo.


Em JavaScript, acesse os dados do blob usando `context.bindings.<name from function.json>`.

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Associação |  Referência |
|---|---|
| Blob | [Códigos de erro de Blob](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob, tabela, fila |  [Códigos de erro de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, tabela, fila |  [Solução de problemas](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Próximas etapas

* [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Blob storage trigger](functions-create-storage-blob-triggered-function.md)
--->
