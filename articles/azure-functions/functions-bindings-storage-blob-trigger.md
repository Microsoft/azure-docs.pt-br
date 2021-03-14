---
title: Gatilho do armazenamento de BLOBs do Azure para Azure Functions
description: Saiba como executar uma função do Azure como alterações de dados do armazenamento de BLOBs do Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 1d83a828829d27d85749b3fa7b283cad9683bffc
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455901"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>Gatilho do armazenamento de BLOBs do Azure para Azure Functions

O gatilho de armazenamento de Blob inicia uma função quando é detectado um blob novo ou atualizado. O conteúdo do blob é fornecido como [entrada para a função](./functions-bindings-storage-blob-input.md).

O gatilho do armazenamento de BLOBs do Azure requer uma conta de armazenamento de uso geral. Também há suporte para contas de armazenamento V2 com [namespaces hierárquicos](../storage/blobs/data-lake-storage-namespace.md) . Para usar uma conta somente de BLOB ou se seu aplicativo tiver necessidades especializadas, examine as alternativas para usar esse gatilho.

Para obter informações sobre a instalação e detalhes de configuração, confira a [visão geral](./functions-bindings-storage-blob.md).

## <a name="polling"></a>Sondagem

A sondagem funciona como um híbrido entre a inspeção de logs e a execução de verificações de contêiner periódicas. Os BLOBs são verificados em grupos de 10.000 por vez com um token de continuação usado entre intervalos.

> [!WARNING]
> Além disso, [logs de armazenamento são criados da "melhor forma dentro do possível"](/rest/api/storageservices/About-Storage-Analytics-Logging). Não há nenhuma garantia de que todos os eventos são capturados. Sob algumas condições, logs poderão ser perdidos.
> 
> Se você precisar de um processamento de blob mais rápido ou confiável, crie uma [mensagem de fila](../storage/queues/storage-dotnet-how-to-use-queues.md) ao criar o blob. Em seguida, use um [gatilho de fila](functions-bindings-storage-queue.md) em vez de um gatilho de blob para processar o blob. Outra opção é usar a Grade de Eventos; consulte o tutorial [Automatize redimensionamento de imagens carregadas usando a Grade de Eventos](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="alternatives"></a>Alternativas

### <a name="event-grid-trigger"></a>Gatilho de grade de eventos

O [gatilho de grade de eventos](functions-bindings-event-grid.md) também tem suporte interno para [eventos de blob](../storage/blobs/storage-blob-event-overview.md). Use a Grade de Eventos em vez do disparador de armazenamento de Blobs para os seguintes cenários:

- **Contas de armazenamento somente blob**: [as contas de armazenamento somente blob](../storage/common/storage-account-overview.md#types-of-storage-accounts) têm suporte para associações de entrada e saída de BLOB, mas não para gatilhos de BLOB.

- **Alta escala**: a alta escala pode ser definida livremente como contêineres que têm mais de 100.000 BLOBs ou contas de armazenamento que têm mais de 100 atualizações de blob por segundo.

- **Minimizando a latência**: se seu aplicativo de funções estiver no plano de consumo, poderá haver um atraso de até 10 minutos no processamento de novos BLOBs se um aplicativo de funções tiver ficado ocioso. Para evitar essa latência, você pode alternar para um plano do serviço de aplicativo com o Always On habilitado. Você também pode usar um [gatilho da Grade de Eventos](functions-bindings-event-grid.md) com sua conta de armazenamento de Blob. Para obter um exemplo, confira o [tutorial da Grade de Eventos](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json).

Consulte o tutorial [redimensionar imagem com grade de eventos](../event-grid/resize-images-on-storage-blob-upload-event.md) de um exemplo de grade de eventos.

### <a name="queue-storage-trigger"></a>Gatilho de armazenamento de filas

Outra abordagem para o processamento de BLOBs é gravar mensagens da fila que correspondam aos BLOBs que estão sendo criados ou modificados e, em seguida, usar um [gatilho de armazenamento de fila](./functions-bindings-storage-queue.md) para iniciar o processamento.

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que grava um log quando um blob é adicionado ou atualizado no contêiner `samples-workitems`.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

A cadeia de caracteres `{name}` no caminho do disparador de blob `samples-workitems/{name}` cria uma [expressão de associação](./functions-bindings-expressions-patterns.md) que você pode usar no código de função para acessar o nome de arquivo do blob disparando. Para obter mais informações, consulte [Padrões de nome do blob](#blob-name-patterns) a seguir neste artigo.

Para obter mais informações sobre o `BlobTrigger` atributo, consulte [atributos e anotações](#attributes-and-annotations).

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O exemplo a seguir mostra uma associação de gatilho de BLOB em um *function.jsno* arquivo e código que usa a associação. A função grava um log quando um blob é adicionado ou atualizado no `samples-workitems` [contêiner](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

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

A cadeia de caracteres `{name}` no caminho do disparador de blob `samples-workitems/{name}` cria uma [expressão de associação](./functions-bindings-expressions-patterns.md) que você pode usar no código de função para acessar o nome de arquivo do blob disparando. Para obter mais informações, consulte [Padrões de nome do blob](#blob-name-patterns) a seguir neste artigo.

Para obter mais informações sobre as propriedades do arquivo *function.json*, consulte a seção [Configuração](#configuration) que explica essas propriedades.

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

# <a name="java"></a>[Java](#tab/java)

Essa função grava um log quando um blob é adicionado ou atualizado no `myblob` contêiner.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

A cadeia de caracteres `{name}` no caminho do disparador de blob `samples-workitems/{name}` cria uma [expressão de associação](./functions-bindings-expressions-patterns.md) que você pode usar no código de função para acessar o nome de arquivo do blob disparando. Para obter mais informações, consulte [Padrões de nome do blob](#blob-name-patterns) a seguir neste artigo.

Para obter mais informações sobre as propriedades do arquivo *function.json*, consulte a seção [Configuração](#configuration) que explica essas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O exemplo a seguir demonstra como criar uma função que é executada quando um arquivo é adicionado ao `source` contêiner de armazenamento de BLOBs.

O arquivo de configuração de função (_function.jsem_) inclui uma associação com o `type` de `blobTrigger` e `direction` definido como `in` .

```json
{
  "bindings": [
    {
      "name": "InputBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "source/{name}",
      "connection": "MyStorageAccountConnectionString"
    }
  ]
}
```

Aqui está o código associado para o arquivo de _run.ps1_ .

```powershell
param([byte[]] $InputBlob, $TriggerMetadata)

Write-Host "PowerShell Blob trigger: Name: $($TriggerMetadata.Name) Size: $($InputBlob.Length) bytes"
```

# <a name="python"></a>[Python](#tab/python)

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

A cadeia de caracteres `{name}` no caminho do disparador de blob `samples-workitems/{name}` cria uma [expressão de associação](./functions-bindings-expressions-patterns.md) que você pode usar no código de função para acessar o nome de arquivo do blob disparando. Para obter mais informações, consulte [Padrões de nome do blob](#blob-name-patterns) a seguir neste artigo.

Para obter mais informações sobre as propriedades do arquivo *function.json*, consulte a seção [Configuração](#configuration) que explica essas propriedades.

Aqui está o código Python:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Em [bibliotecas de classe C#](functions-dotnet-class-library.md), use os seguintes atributos para configurar um gatilho de blob:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  O construtor do atributo usa uma cadeia de caracteres de caminho que indica o contêiner para inspecionar e, opcionalmente, um [padrão de nome de blob](#blob-name-patterns). Aqui está um exemplo:

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

  Para obter um exemplo completo, consulte [exemplo de gatilho](#example).

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

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

O script C# não dá suporte a atributos.

# <a name="java"></a>[Java](#tab/java)

O `@BlobTrigger` atributo é usado para fornecer acesso ao blob que disparou a função. Consulte o [exemplo de gatilho](#example) para obter detalhes.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O JavaScript não dá suporte a atributos.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Não há suporte para atributos pelo PowerShell.

# <a name="python"></a>[Python](#tab/python)

O Python não dá suporte a atributos.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `BlobTrigger`.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como `blobTrigger`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | n/d | Deve ser definido como `in`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. As exceções são mencionadas na seção [uso](#usage). |
|**name** | n/d | O nome da variável que representa o blob no código de função. |
|**path** | **BlobPath** |O [contêiner](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) a ser monitorado.  Pode ser um [padrão de nome de blob](#blob-name-patterns). |
|**connection** | **Conexão** | O nome de uma configuração de aplicativo que contém uma cadeia de conexão de Armazenamento para usar para essa associação. Se o nome de configuração do aplicativo começar com "AzureWebJobs", você pode especificar apenas o resto do nome aqui. Por exemplo, se você configurar `connection` para “MyStorage”, o runtime do Functions procura por uma configuração de aplicativo que esteja nomeada “AzureWebJobsMyStorage." Se você deixar `connection` vazio, o runtime de Functions usa a cadeia de caracteres de conexão de Armazenamento padrão na configuração de aplicativo chamada `AzureWebJobsStorage`.<br><br>A cadeia de conexão deve ser uma conta de armazenamento para uso geral e não uma [conta de Armazenamento de Blobs](../storage/common/storage-account-overview.md#types-of-storage-accounts).<br><br>Se você estiver usando [a versão 5. x ou superior da extensão](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher), em vez de uma cadeia de conexão, você pode fornecer uma referência a uma seção de configuração que define a conexão. Consulte [conexões](./functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="java"></a>[Java](#tab/java)

O `@BlobTrigger` atributo é usado para fornecer acesso ao blob que disparou a função. Consulte o [exemplo de gatilho](#example) para obter detalhes.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Acessar dados de BLOB usando `context.bindings.<NAME>` onde `<NAME>` corresponde ao valor definido em *function.jsem*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Acesse os dados de blob por meio de um parâmetro que corresponde ao nome designado pelo parâmetro de nome da associação na _function.jsno_ arquivo.

# <a name="python"></a>[Python](#tab/python)

Acessar dados de blob por meio do parâmetro digitado como [InputStream](/python/api/azure-functions/azure.functions.inputstream). Consulte o [exemplo de gatilho](#example) para obter detalhes.

---

## <a name="blob-name-patterns"></a>Padrões de nome de BLOB

Você pode especificar um padrão de nome de blob na `path` propriedade em *function.json* ou no `BlobTrigger` construtor de atributo. O nome padrão pode ser uma [expressão de associação ou filtro](./functions-bindings-expressions-patterns.md). As seções a seguir fornecem exemplos.

> [!TIP]
> Um nome de contêiner não pode conter um resolvedor no padrão de nome.

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

Se o nome do blob for *original-Blob1.txt*, o valor da `name` variável no código da função é `Blob1.txt`.

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

Se o blob for nomeado *{20140101}-soundfile.mp3*, o `name` valor da variável no código de função será *soundfile.mp3*.

## <a name="metadata"></a>Metadados

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-script"></a>[Script do C#](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="java"></a>[Java](#tab/java)

Os metadados não estão disponíveis em Java.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Os metadados estão disponíveis por meio do `$TriggerMetadata` parâmetro.

# <a name="python"></a>[Python](#tab/python)

Os metadados não estão disponíveis no Python.

---

## <a name="blob-receipts"></a>Recebimentos de blob

O Azure Functions runtime garante que nenhuma função de gatilho de blob seja chamada mais de uma vez para o mesmo blob novo ou atualizado. Para determinar se uma versão de determinado blob foi processada, ele mantém os *recebimentos de blob*.

O Azure Functions armazena recibos do blob em um contêiner denominado *azure-webjobs-hosts* na conta de armazenamento do Azure do seu aplicativo de funções (definido na configuração do aplicativo `AzureWebJobsStorage`). Um recebimento de blob tem as seguintes informações:

* A função disparada ( `<FUNCTION_APP_NAME>.Functions.<FUNCTION_NAME>` , por exemplo: `MyFunctionApp.Functions.CopyBlob` )
* O nome do contêiner
* O tipo de BLOB ( `BlockBlob` ou `PageBlob` )
* O nome do blob
* A ETag (um identificador de versão de BLOB, por exemplo: `0x8D1DC6E70A277EF` )

Para forçar o reprocessamento de um blob, exclua manualmente o recebimento desse blob do contêiner *azure-webjobs-hosts*. Embora o reprocessamento possa não ocorrer imediatamente, é garantido que ocorra em um momento posterior. Para reprocessar imediatamente, o blob *scaninfo* no *Azure-webjobs-hosts/blobscaninfo* pode ser atualizado. Todos os BLOBs com um carimbo de data/hora da última modificação após a `LatestScan` propriedade serão verificados novamente.

## <a name="poison-blobs"></a>BLOBs inviabilizados

Quando uma função de gatilho de blob falhar para um determinado blob, o Azure Functions repete essa função até cinco vezes por padrão.

Se todas as cinco tentativas falharem, o Azure Functions adiciona uma mensagem para uma fila de armazenamento denominada *webjobs-blobtrigger-poison*. O número máximo de novas tentativas é configurável. A mesma MaxDequeueCount é usada para manipular blob suspeitos e manipular mensagens de filas suspeitas. A mensagem da fila para blobs suspeitos é um objeto JSON que contém as seguintes propriedades:

* FunctionID (no formato `<FUNCTION_APP_NAME>.Functions.<FUNCTION_NAME>` )
* BlobType ( `BlockBlob` ou `PageBlob` )
* ContainerName
* BlobName
* ETag (um identificador de versão de BLOB, por exemplo: `0x8D1DC6E70A277EF` )

## <a name="concurrency-and-memory-usage"></a>Uso de memória e simultaneidade

O gatilho de blob usa uma fila internamente, portanto, o número máximo de invocações de função concorrente é controlado pela [configuração de filas em host.json](functions-host-json.md#queues). As configurações padrão limitam a concorrência a 24 invocações. Esse limite aplica-se separadamente a cada função que usa um gatilho de blob.

> [!NOTE]
> Para aplicativos que usam a [versão 5.0.0 ou superior da extensão de armazenamento](functions-bindings-storage-blob.md#storage-extension-5x-and-higher), a configuração de filas no host.jssomente se aplica a gatilhos de fila. Em vez disso, a simultaneidade do gatilho de blob é controlada pela [configuração de BLOBs no host.jsem](functions-host-json.md#blobs).

[O plano de consumo](event-driven-scaling.md) limita um aplicativo de funções em uma VM (máquina virtual) a 1,5 GB de memória. A memória é usada por cada instância de execução de execução simultânea e pelo próprio runtime de Funções. Se uma função disparada por blob carregar todo o blob na memória, a memória máxima usada por essa função apenas para blobs será tamanho máximo de blob 24 *. Por exemplo, um aplicativo de funções com três funções disparadas por blob e as configurações padrão teriam uma concorrência máxima por VM de 3*24 = 72 invocações de função.

As funções JavaScript e Java carregam todo o blob na memória, e as funções C# fazem isso se você associar a `string` , ou `Byte[]` .

## <a name="hostjson-properties"></a>Propriedades de host.json

O [host.jsno](functions-host-json.md#blobs) arquivo contém configurações que controlam o comportamento do gatilho de BLOB. Consulte a [host.jsna seção Configurações](functions-bindings-storage-blob.md#hostjson-settings) para obter detalhes sobre as configurações disponíveis.

## <a name="next-steps"></a>Próximas etapas

- [Ler dados do armazenamento de BLOBs quando uma função é executada](./functions-bindings-storage-blob-input.md)
- [Gravar dados de armazenamento de blobs de uma função](./functions-bindings-storage-blob-output.md)
