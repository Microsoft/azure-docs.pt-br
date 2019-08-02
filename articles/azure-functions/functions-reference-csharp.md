---
title: Referência do desenvolvedor de scripts C# do Azure Functions
description: Entenda como desenvolver Azure Functions usando script C#.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, functions, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 12/12/2017
ms.author: glenga
ms.openlocfilehash: 3ac9d8d64e4f16a4d6268606e723b14e32d8c16e
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261777"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Referência do desenvolvedor de scripts C# (.csx) do Azure Functions

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Este artigo é uma introdução ao desenvolvimento do Azure Functions usando o script C# ( *.csx*).

O Azure Functions oferece suporte às linguagens de programação C# e script C#. Se você estiver procurando diretrizes sobre [usar C# em um projeto de biblioteca de classes do Visual Studio](functions-develop-vs.md), consulte [Referência do desenvolvedor C#](functions-dotnet-class-library.md).

Este artigo pressupõe que você já tenha lido o [Guia de desenvolvedores do Azure Functions](functions-reference.md).

## <a name="how-csx-works"></a>Como o .csx funciona

A experiência de scripts C# do Azure Functions baseia-se no [SDK do Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction). Fluxos de dados na sua função C# por meio de argumentos de método. Os nomes de argumentos são especificados em um arquivo `function.json`, e há nomes predefinidos para acessar itens como a função logger e os tokens de cancelamento.

O formato *.csx* permite escrever menos “texto clichê” e se concentrar apenas em escrever uma função C#. Em vez de encapsular tudo em um namespace e uma classe, basta definir um método `Run`. Inclua todas as referências de assembly e todos os namespaces no início do arquivo, como de costume.

Arquivos *.csx* do aplicativo de função são compilados quando uma instância é inicializada. Nessa etapa de compilação, coisas como a inicialização a frio podem levar mais tempo para funções de script C# em comparação a bibliotecas de classes do C#. Essa etapa de compilação também mostra porque funções do script C# são editáveis no portal do Azure enquanto objetos visuais do C# não são.

## <a name="folder-structure"></a>Estrutura de pastas

A estrutura de pastas para um projeto de script C# é semelhante à seguinte:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Há um arquivo [host.json](functions-host-json.md) compartilhado que pode ser usado para configurar o aplicativo de funções. Cada função possui seu próprio arquivo de código (.csx) e arquivo de configuração de associação (function.json).

As extensões de associação necessárias na [versão 2.x](functions-versions.md) do tempo de execução do Functions são definidas no arquivo `extensions.csproj`, com os arquivos de biblioteca reais na pasta `bin`. Ao desenvolver localmente, você precisa [registrar as extensões de associação](./functions-bindings-register.md#extension-bundles). Ao desenvolver funções no portal do Azure, esse registro é feito para você.

## <a name="binding-to-arguments"></a>Binding para argumentos

Dados de entrada ou de saída são associados a um parâmetro de função de script C# por meio da propriedade `name` no arquivo de configuração *function.json*. O exemplo a seguir mostra um arquivo *function.json* e arquivo *run.csx* para uma função disparada por fila. O parâmetro que recebe dados da mensagem da fila é chamado de `myQueueItem` porque esse é o valor de propriedade `name`.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}");
}
```

A instrução `#r` será explicada [mais adiante neste artigo](#referencing-external-assemblies).

## <a name="supported-types-for-bindings"></a>Tipos com suporte para associações

Cada associação tem seus próprios tipos com suporte. Por exemplo, um gatilho de blob pode ser usado com um parâmetro de cadeia de caracteres, um parâmetro POCO, um parâmetro `CloudBlockBlob` ou qualquer um dos vários outros tipos com suporte. O [artigo de referência de associação para associações de blob](functions-bindings-storage-blob.md#trigger---usage) lista todos os tipos de parâmetro com suporte para gatilhos de blob. Para obter mais informações, consulte [Gatilhos e associações](functions-triggers-bindings.md) e os [documentos de referência de associação para cada tipo de associação](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Referenciando classes personalizadas

Se precisa usar uma classe de objeto CRL básico (POCO) personalizada, você pode incluir a definição de classe dentro do mesmo arquivo ou colocá-la em um arquivo separado.

O exemplo a seguir mostra um *run.csx* que inclui uma definição de classe POCO.

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

Uma classe POCO deve ter getter e setter definidos para cada propriedade.

## <a name="reusing-csx-code"></a>Reutilização de código .csx

Você pode usar classes e métodos definidos em outros arquivos *.csx* no seu arquivo *run.csx*. Para fazer isso, use diretivas `#load` no arquivo *run.csx*. No exemplo a seguir, uma rotina de log chamada `MyLogger` é compartilhada em *myLogger.csx* e carregada em *run.csx* usando a diretiva `#load`:

Exemplo de *run.csx*:

```csharp
#load "mylogger.csx"

using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Exemplo de *mylogger.csx*:

```csharp
public static void MyLogger(ILogger log, string logtext)
{
    log.LogInformation(logtext);
}
```

Usar um arquivo *.csx* compartilhado é um padrão comum quando você deseja tipar fortemente os argumentos transmitidos entre as funções usando um objeto POCO. No seguinte exemplo simplificado, um gatilho HTTP e um gatilho de fila compartilham um objeto POCO chamado `Order` para tipar fortemente os dados do pedido:

Exemplo *run.csx* para o gatilho HTTP:

```cs
#load "..\shared\order.csx"

using System.Net;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function received an order.");
    log.LogInformation(req.ToString());
    log.LogInformation("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

Exemplo *run.csx* para o gatilho da fila:

```cs
#load "..\shared\order.csx"

using System;
using Microsoft.Extensions.Logging;

public static void Run(Order myQueueItem, out Order outputQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed order...");
    log.LogInformation(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

Exemplo *order.csx*:

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId +
                  "\n\tcustName : " + custName +             
                  "\n\tcustAddress : " + custAddress +             
                  "\n\tcustEmail : " + custEmail +             
                  "\n\tcartId : " + cartId + "\n}";             
    }
}
```

Você pode usar um caminho relativo com a diretiva `#load` :

* `#load "mylogger.csx"` carrega um arquivo localizado na pasta de função.
* `#load "loadedfiles\mylogger.csx"` carrega um arquivo localizado em uma pasta na pasta de função.
* `#load "..\shared\mylogger.csx"` carrega um arquivo localizado em uma pasta no mesmo nível que a pasta de função, ou seja, diretamente em *wwwroot*.

A diretiva `#load` só funciona com arquivos *.csx*, não com arquivos *.cs*.

## <a name="binding-to-method-return-value"></a>Associando ao valor de retorno do método

Use um valor retornado de um método em uma associação de saída, usando o nome `$return` em *function.json*. Para obter exemplos, consulte [Gatilhos e associações](./functions-bindings-return-value.md).

Use o valor retornado apenas se uma execução de função com êxito sempre resultar em um valor retornado a ser passado para a associação de saída. Caso contrário, use `ICollector` ou `IAsyncCollector`, conforme mostrado na seção a seguir.

## <a name="writing-multiple-output-values"></a>Gravando vários valores de saída

Para gravar vários valores em uma associação de saída ou se uma invocação de função com êxito não resultar em nada a ser passado para a associação de saída, use os tipos [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs). Esses tipos são coleções somente gravação que são gravadas na associação de saída quando o método é concluído.

Este exemplo grava várias mensagens de fila na mesma fila usando `ICollector`:

```csharp
public static void Run(ICollector<string> myQueue, ILogger log)
{
    myQueue.Add("Hello");
    myQueue.Add("World!");
}
```

## <a name="logging"></a>Registrando em log

A saída de log para logs de streaming em C#, inclue um argumento do tipo [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Recomendamos nomeá-lo como `log`. Evite usar `Console.Write` no Azure Functions.

```csharp
public static void Run(string myBlob, ILogger log)
{
    log.LogInformation($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> Para obter informações sobre uma estrutura de log mais recente que pode ser usada no lugar de `TraceWriter`, consulte [Gravar logs nas funções C#](functions-monitoring.md#write-logs-in-c-functions) no artigo **Monitorar o Azure Functions**.

## <a name="async"></a>Async

Para tornar uma função [assíncrona](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), use a palavra-chave `async` e retorne um objeto `Task`.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

Não é possível usar parâmetros `out` em funções assíncronas. Para associações de saída, use o [valor de retorno de função](#binding-to-method-return-value) ou um [objeto coletor](#writing-multiple-output-values).

## <a name="cancellation-tokens"></a>Tokens de cancelamento

Uma função pode aceitar um parâmetro [CancellationToken](/dotnet/api/system.threading.cancellationtoken) que permite ao sistema operacional notificar seu código quando a função está prestes a ser encerrada. Você pode usar essa notificação para certificar-se de que a função não finalize inesperadamente de uma maneira que os dados fiquem em um estado inconsistente.

O exemplo a seguir mostra como verificar o encerramento da função iminente.

```csharp
using System;
using System.IO;
using System.Threading;

public static void Run(
    string inputText,
    TextWriter logger,
    CancellationToken token)
{
    for (int i = 0; i < 100; i++)
    {
        if (token.IsCancellationRequested)
        {
            logger.WriteLine("Function was cancelled at iteration {0}", i);
            break;
        }
        Thread.Sleep(5000);
        logger.WriteLine("Normal processing for queue message={0}", inputText);
    }
}
```

## <a name="importing-namespaces"></a>Importando namespaces

Se precisar importar namespaces, você poderá fazer como geralmente faz, com a cláusula `using` .

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Os seguintes namespaces são automaticamente importados e, portanto, são opcionais:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Referenciando assemblies externos

Para assemblies da estrutura, adicione referências usando a diretiva `#r "AssemblyName"` .

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Os seguintes assemblies são adicionados automaticamente pelo ambiente de hospedagem do Azure Functions:

* `mscorlib`
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`

Os seguintes assemblies podem ser referenciados por um nome simples (por exemplo, `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Referenciando assemblies personalizados

Para referenciar um assembly personalizado, use um assembly *compartilhado* ou *particular*:

* Assemblies compartilhados são compartilhados entre todas as funções em um aplicativo de funções. Para fazer referência a um assembly personalizado, carregue o assembly em uma pasta chamada `bin` em sua [pasta raiz do aplicativo de funções](functions-reference.md#folder-structure) (wwwroot).

* Assemblies particulares fazem parte do contexto de determinada função e dão suporte ao sideload de versões diferentes. Os assemblies particulares devem ser carregados em uma pasta `bin` do diretório da função. Referencie os assemblies usando o nome de arquivo, como `#r "MyAssembly.dll"`.

Para obter informações sobre como carregar arquivos na pasta da função, consulte a seção sobre [gerenciamento de pacotes](#using-nuget-packages).

### <a name="watched-directories"></a>Diretórios inspecionados

O diretório que contém o arquivo de script da função é inspecionado automaticamente quanto às alterações nos assemblies. Para inspecionar alterações de assembly em outros diretórios, adicione-os à lista `watchDirectories` em [host.json](functions-host-json.md).

## <a name="using-nuget-packages"></a>Usando pacotes NuGet
Para usar os pacotes NuGet em uma função 2 C# . x, carregue um arquivo *Function. proj* para a pasta da função no sistema de arquivos do aplicativo de funções. Este é um arquivo *function.proj* de exemplo que adiciona uma referência a *Microsoft.ProjectOxford.Face* versão *1.1.0*:

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    
    <ItemGroup>
        <PackageReference Include="Microsoft.ProjectOxford.Face" Version="1.1.0" />
    </ItemGroup>
</Project>
```

Para usar um feed do NuGet personalizado, especifique o feed em um arquivo *Nuget.Config* na raiz do Aplicativo de Funções. Para obter mais informações, consulte [Configurando o comportamento do NuGet](/nuget/consume-packages/configuring-nuget-behavior). 

> [!NOTE]
> Em funções 1. C# x, os pacotes NuGet são referenciados com um arquivo *Project. JSON* em vez de um arquivo *Function. proj* .

Para funções 1. x, use um arquivo *Project. JSON* em vez disso. Aqui está um exemplo de arquivo *Project. JSON* : 

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

### <a name="using-a-functionproj-file"></a>Usando um arquivo function. proj

1. Abra a função no portal do Azure. A guia logs exibe o resultado da instalação do pacote.
2. Para carregar um arquivo *Function. proj* , use um dos métodos descritos em [como atualizar os arquivos do aplicativo de funções](functions-reference.md#fileupdate) no tópico Azure Functions referência do desenvolvedor.
3. Depois que o arquivo *Function. proj* for carregado, você verá uma saída semelhante ao exemplo a seguir no log de streaming da função:

```
2018-12-14T22:00:48.658 [Information] Restoring packages.
2018-12-14T22:00:48.681 [Information] Starting packages restore
2018-12-14T22:00:57.064 [Information] Restoring packages for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj...
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\function.proj...
2018-12-14T22:01:00.844 [Information] Installing Newtonsoft.Json 10.0.2.
2018-12-14T22:01:01.041 [Information] Installing Microsoft.ProjectOxford.Common.DotNetStandard 1.0.0.
2018-12-14T22:01:01.140 [Information] Installing Microsoft.ProjectOxford.Face.DotNetStandard 1.0.0.
2018-12-14T22:01:09.799 [Information] Restore completed in 5.79 sec for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj.
2018-12-14T22:01:10.905 [Information] Packages restored.
```

## <a name="environment-variables"></a>Variáveis de ambiente

Para obter uma variável de ambiente ou um valor de configuração do aplicativo, use `System.Environment.GetEnvironmentVariable`, conforme mostrado no exemplo de código a seguir:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " +
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

<a name="imperative-bindings"></a> 

## <a name="binding-at-runtime"></a>Associando no tempo de execução

No C#, e em outras linguagens .NET, você pode usar um padrão de associação [obrigatório](https://en.wikipedia.org/wiki/Imperative_programming), em vez de associações [*declarativas*](https://en.wikipedia.org/wiki/Declarative_programming) em *function.json*. A associação obrigatória é útil quando os parâmetros de associação precisam ser calculado no tempo de execução, em vez do tempo de design. Com esse padrão, é possível se vincular a associações de entrada e saída com suporte instantaneamente no código da função.

Defina uma associação obrigatória da seguinte maneira:

- **Não** inclua uma entrada em *function.json* para as associações obrigatórias desejadas.
- Passe um parâmetro de entrada [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) ou [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Use o padrão de C# a seguir para realizar a associação de dados.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute` é o atributo do .NET que define a associação, e `T` é um tipo de entrada ou saída com suporte nesse tipo de associação. `T` não pode ser um tipo de parâmetro `out` (como `out JObject`). Por exemplo, a associação de saída de tabela de aplicativos móveis dá suporte a [seis tipos de saída](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), mas você só pode usar [\<ICollector t >](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) ou [IAsyncCollector\<t >](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) para. `T`

### <a name="single-attribute-example"></a>Exemplo de atributo único

O código de exemplo a seguir cria uma [associação de saída do Armazenamento de Blobs](functions-bindings-storage-blob.md#output) com o caminho do blob definido em tempo de execução e grava uma cadeia de caracteres no blob.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) define a associação de entrada ou saída do [Armazenamento de Blobs](functions-bindings-storage-blob.md) e [TextWriter](/dotnet/api/system.io.textwriter) é um tipo de associação de saída com suporte.

### <a name="multiple-attribute-example"></a>Exemplo de atributo múltiplo

O exemplo anterior obtém a configuração do aplicativo para a cadeia de conexão da conta de armazenamento principal do aplicativo de funções (que é `AzureWebJobsStorage`). É possível especificar uma configuração de aplicativo personalizada a ser usada para a conta de armazenamento adicionando [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) e passando a matriz de atributos para `BindAsync<T>()`. Use um parâmetro `Binder`, não `IBinder`.  Por exemplo:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {    
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

A tabela a seguir lista os atributos .NET para cada tipo de associação e os pacotes no qual eles são definidos.

> [!div class="mx-codeBreakAll"]
> | Associação | Atributo | Adicionar referência |
> |------|------|------|
> | Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
> | Hubs de Eventos | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
> | Aplicativos Móveis | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
> | Hubs de Notificação | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
> | Barramento de Serviço | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
> | Fila de armazenamento | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Armazenamento de blobs | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Tabela de armazenamento | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre gatilhos e associações](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre práticas recomendadas do Azure Functions](functions-best-practices.md)
