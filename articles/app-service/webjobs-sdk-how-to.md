---
title: Como usar o SDK WebJobs - Azure
description: Saiba mais sobre como escrever código para o WebJobs SDK. Crie trabalhos de processamento em segundo plano controlados por eventos que acessam dados nos serviços do Azure e serviços de terceiros.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: jeconnoc
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 3ba8a8e5922c012b93ab19a5859aab5c31d35b2b
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424151"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Como usar o SDK do Azure WebJobs para o processamento em segundo plano controlado por evento

Este artigo fornece orientação sobre como trabalhar com o SDK do Azure WebJobs. Para começar a usar os trabalhos Web imediatamente, consulte Introdução ao [SDK do Azure WebJobs para processamento em segundo plano orientado a eventos](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>Versões do SDK do WebJobs

Essas são as principais diferenças entre a versão 3. *x* e versão 2. *x* do SDK de trabalhos Web:

* Versão 3. *x* adiciona suporte para .NET Core.
* Na versão 3. *x*, você precisa instalar explicitamente a extensão de associação de armazenamento exigida pelo SDK de trabalhos Web. Na versão 2. *x*, as associações de armazenamento foram incluídas no SDK.
* Ferramentas do Visual Studio para .NET Core (3. *x*) os projetos diferem das ferramentas para .NET Framework (2. *x*) projetos. Para saber mais, confira [desenvolver e implantar trabalhos Web usando o Visual Studio-Azure app Service](webjobs-dotnet-deploy-vs.md).

Quando possível, são fornecidos exemplos para a versão 3. *x* e versão 2. *x*.

> [!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md) é criado no SDK de trabalhos Web, e este artigo fornece links para Azure Functions documentação de alguns tópicos. Observe essas diferenças entre as funções e o SDK de trabalhos Web:
> * Azure Functions versão 2. *x* corresponde à versão 3 do SDK de trabalhos Web. *x*e Azure Functions 1. *x* corresponde ao SDK do webjobs 2. *x*. Os repositórios de código-fonte usam a numeração do SDK de trabalhos Web.
> * O código de exemplo C# para bibliotecas de classes de Azure Functions é como o código do SDK de trabalhos `FunctionName` Web, exceto que você não precisa de um atributo em um projeto do SDK de trabalhos Web.
> * Alguns tipos de ligação têm suporte apenas em funções, como HTTP (WebHooks) e na grade de eventos (que é baseada em HTTP).
>
> Para obter mais informações, consulte [Comparar o WebJobs SDK e o Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>Host do WebJobs

O host é um contêiner de tempo de execução para funções.  Ele escuta gatilhos e chamadas de funções. Na versão 3. *x*, o host é uma implementação de `IHost`. Na versão 2. *x*, você usa o `JobHost` objeto. Você cria uma instância do host em seu código e escreve um código para personalizar seu comportamento.

Essa é uma diferença importante entre usar o SDK de trabalhos Web diretamente e usá-lo indiretamente por meio de Azure Functions. No Azure Functions, o serviço controla o host e você não pode personalizar o host escrevendo código. Azure Functions permite que você personalize o comportamento do host por meio de configurações no arquivo host. JSON. Essas configurações são cadeias de caracteres, não código, e isso limita os tipos de personalizações que você pode fazer.

### <a name="host-connection-strings"></a>Cadeias de conexão do host

O SDK de trabalhos Web procura as cadeias de conexão do armazenamento do Azure e do barramento de serviço do Azure no arquivo local. Settings. JSON quando você executa localmente ou no ambiente do WebJob quando você executa no Azure. Por padrão, uma configuração de cadeia de conexão `AzureWebJobsStorage` de armazenamento chamada é necessária.  

Versão 2. o *x* do SDK permite que você use seus próprios nomes para essas cadeias de conexão ou armazene-os em outro lugar. Você pode definir nomes no código usando o [`JobHostConfiguration`], conforme mostrado aqui:

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    //// Dashboard logging is deprecated; use Application Insights.
    //var _dashboardConn = ConfigurationManager
    //    .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    //config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Porque a versão 3. *x* usa as APIs de configuração padrão do .NET Core, não há API para alterar os nomes da cadeia de conexão.

### <a name="host-development-settings"></a>Configurações de desenvolvimento do host

Você pode executar o host no modo de desenvolvimento para tornar o desenvolvimento local mais eficiente. Aqui estão algumas das configurações que são alteradas quando você executa no modo de desenvolvimento:

| Propriedade | Configuração de desenvolvimento |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` para maximizar a saída de log. |
| `Queues.MaxPollingInterval`  | Um valor baixo para garantir que os métodos de fila sejam disparados imediatamente.  |
| `Singleton.ListenerLockPeriod` | Quinze segundos para ajudar no desenvolvimento iterativo rápido. |

O processo para habilitar o modo de desenvolvimento depende da versão do SDK. 

#### <a name="version-3x"></a>Versão 3. *x*

Versão 3. *x* usa as APIs de ASP.NET Core padrão. Chame o [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) método [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) na instância. Passe uma cadeia de `development`caracteres chamada, como neste exemplo:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment("development");
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

#### <a name="version-2x"></a>Versão 2. *x*

A classe `JobHostConfiguration` tem um método `UseDevelopmentSettings` que habilita o modo de desenvolvimento.  O exemplo a seguir mostra como usar as configurações de desempenho. Para fazer `config.IsDevelopment` com `true` que o retorno seja executado localmente, defina uma variável de `AzureWebJobsEnv` ambiente local chamada `Development`com o valor.

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-servicepointmanager-settings"></a>Gerenciando conexões simultâneas (versão 2. *x*)

Na versão 3. *x*, o limite de conexão assume como padrão conexões infinitas. Se, por alguma razão, você precisar alterar esse limite, poderá usar a [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) propriedade [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) da classe.

Na versão 2. *x*, você controla o número de conexões simultâneas com um host usando a API [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) . Em 2. *x*, você deve aumentar esse valor a partir do padrão 2 antes de iniciar o host de trabalhos Web.

Todas as solicitações HTTP de saída feitas de uma função usando `HttpClient` o `ServicePointManager`Flow. Depois que você alcançar o valor definido `DefaultConnectionLimit`em `ServicePointManager` , o iniciará as solicitações de enfileiramento antes de enviá-las. Suponha que seu `DefaultConnectionLimit` seja definido como 2 e seu código faça 1.000 solicitações HTTP. Inicialmente, apenas duas solicitações são permitidas por meio do sistema operacional. As outras 998 são colocadas na fila até que haja espaço para elas. Isso significa que `HttpClient` seu tempo limite pode ser atingido porque parece ter feito a solicitação, mas a solicitação nunca foi enviada pelo sistema operacional para o servidor de destino. Para que você possa ver o comportamento que não faz sentido: o `HttpClient` local está demorando 10 segundos para concluir uma solicitação, mas o serviço está retornando cada solicitação em 200 ms. 

O valor padrão para aplicativos ASP.net é `Int32.MaxValue`, e isso provavelmente funciona bem para trabalhos Web em execução em um plano de serviço de aplicativo básico ou superior. Os trabalhos Web normalmente precisam da configuração de Always On, e há suporte apenas para planos de serviço de aplicativo básicos e superiores.

Caso o WebJob esteja em execução em um Plano do Serviço de Aplicativo Gratuito ou Compartilhado, seu aplicativo ficará restrito à área restrita do Serviço de Aplicativo, que atualmente tem um [limite de conexão de 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Com um limite de conexão não associado `ServicePointManager`no, é mais provável que o limite de conexão da área restrita seja atingido e o site seja desligado. Nesse caso, a configuração de `DefaultConnectionLimit` para algo inferior, como 50 ou 100, pode evitar que isso aconteça e ainda permitir uma taxa de transferência suficiente.

A configuração deve ser definida antes de todas as solicitações HTTP serem feitas. Por esse motivo, o host de trabalhos Web não deve ajustar a configuração automaticamente. Pode haver solicitações HTTP que ocorrem antes do início do host, o que pode levar a um comportamento inesperado. A melhor abordagem é definir o valor imediatamente no `Main` método antes de inicializar `JobHost`, conforme mostrado aqui:

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it's used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Gatilhos

As funções devem ser métodos públicos e devem ter um atributo Trigger ou [`NoAutomaticTrigger`](#manual-triggers) o atributo.

### <a name="automatic-triggers"></a>Gatilhos automáticos

Os disparadores automáticos chamam uma função em resposta a um evento. Considere este exemplo de uma função que é disparada por uma mensagem adicionada ao armazenamento de filas do Azure. Ele responde lendo um blob do armazenamento de BLOBs do Azure:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

O `QueueTrigger` atributo informa ao tempo de execução para chamar a função sempre que uma mensagem de `myqueue-items` fila é exibida na fila. O `Blob` atributo informa ao tempo de execução para usar a mensagem da fila para ler um blob no contêiner *Sample-WorkItems* . O conteúdo da mensagem da fila, passado para a função no `myQueueItem` parâmetro, é o nome do blob.

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>Gatilhos manuais

Para disparar uma função manualmente, use o `NoAutomaticTrigger` atributo, como mostrado aqui:

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
ILogger logger,
string value,
[Queue("outputqueue")] out string message)
{
    message = value;
    logger.LogInformation("Creating queue message: ", message);
}
```

O processo para disparar a função manualmente depende da versão do SDK.

#### <a name="version-3x"></a>Versão 3. *x*

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    var host = builder.Build();
    using (host)
    {
        var jobHost = host.Services.GetService(typeof(IJobHost)) as JobHost;
        var inputs = new Dictionary<string, object>
        {
            { "value", "Hello world!" }
        };

        await host.StartAsync();
        await jobHost.CallAsync("CreateQueueMessage", inputs);
        await host.StopAsync();
    }
}
```

#### <a name="version-2x"></a>Versão 2. *x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Associações de entrada e saída

As associações de entrada e saída fornecem uma maneira declarativa para criar dados a partir do Azure ou de serviços externos disponíveis para seu código. As associações de saída fornecem uma maneira de atualizar os dados. O [artigo](webjobs-sdk-get-started.md) de introdução mostra um exemplo de cada um.

Você pode usar um valor de retorno de método para uma associação de saída aplicando o atributo ao valor de retorno do método. Consulte o exemplo em [usando o valor de retorno da função do Azure](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Tipos de associação

O processo de instalação e gerenciamento de tipos de associação depende se você está usando a versão 3. *x* ou versão 2. *x* do SDK. Você pode encontrar o pacote a ser instalado para um tipo de associação específico na seção "pacotes" do [artigo de referência](#binding-reference-information)de Azure Functions do tipo de associação. Uma exceção é o gatilho de arquivos e a associação (para o sistema de arquivos local), que não tem suporte pelo Azure Functions.

#### <a name="version-3x"></a>Versão 3. *x*

Na versão 3. *x*, as associações de armazenamento são incluídas no `Microsoft.Azure.WebJobs.Extensions.Storage` pacote. Chame o `AddAzureStorage` método de extensão `ConfigureWebJobs` no método, conforme mostrado aqui:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

Para usar outros tipos de gatilho e associação, instale o pacote do NuGet que os contém e chame o método de extensão `Add<binding>` implementado na extensão. Por exemplo, se você quiser usar uma associação Azure Cosmos DB, instale `Microsoft.Azure.WebJobs.Extensions.CosmosDB` e chame `AddCosmosDB`, da seguinte maneira:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddCosmosDB();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

Para usar o gatilho de Temporizador ou a associação de Arquivos, que fazem parte dos serviços principais, chame os métodos de extensão `AddTimers` ou `AddFiles`, respectivamente.

#### <a name="version-2x"></a>Versão 2. *x*

Esses tipos de gatilho e de associação estão incluídos na versão 2. *x* do `Microsoft.Azure.WebJobs` pacote:

* Armazenamento de Blob
* Armazenamento de filas
* Armazenamento de tabelas

Para usar outros tipos de associação e gatilho, instale o pacote do NuGet que os contém e chame um método `Use<binding>` no objeto `JobHostConfiguration`. Por exemplo, se você quiser usar um gatilho de temporizador, `Microsoft.Azure.WebJobs.Extensions` instale e `UseTimers` chame no `Main` método, conforme mostrado aqui:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Para usar a associação de Arquivos, instale `Microsoft.Azure.WebJobs.Extensions` e chame `UseFiles`.

### <a name="executioncontext"></a>ExecutionContext

O WebJobs permite que você se associe a um [`ExecutionContext`]. Com essa associação, você pode acessar o [`ExecutionContext`] como um parâmetro em sua assinatura de função. Por exemplo, o código a seguir usa o objeto de contexto para acessar a ID de invocação, que você pode usar para correlacionar todos os logs produzidos por uma determinada chamada de função.  

```cs
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

O processo de ligação para o [`ExecutionContext`] depende de sua versão do SDK.

#### <a name="version-3x"></a>Versão 3. *x*

Chame o `AddExecutionContextBinding` método de extensão `ConfigureWebJobs` no método, conforme mostrado aqui:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddExecutionContextBinding();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

#### <a name="version-2x"></a>Versão 2. *x*

O pacote `Microsoft.Azure.WebJobs.Extensions` mencionado anteriormente também fornece um tipo especial de associação que você pode registrar chamando o método `UseCore`. Essa associação permite que você defina [`ExecutionContext`] um parâmetro em sua assinatura de função, que é habilitado da seguinte maneira:

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="binding-configuration"></a>Configuração de associação

Você pode configurar o comportamento de alguns gatilhos e associações. O processo para configurá-los depende da versão do SDK.

* **Versão 3. *x*:** Defina a configuração quando `Add<Binding>` o método for chamado `ConfigureWebJobs`em.
* **Versão 2. *x*:** Defina a configuração definindo as propriedades em um objeto de configuração que você passa `JobHost`para o.

Essas configurações específicas de associação são equivalentes às configurações no [arquivo de projeto host. JSON](../azure-functions/functions-host-json.md) em Azure functions.

Você pode configurar as seguintes associações:

* [Gatilho CosmosDB do Azure](#azure-cosmosdb-trigger-configuration-version-3x)
* [Gatilho de hubs de eventos](#event-hubs-trigger-configuration-version-3x)
* [Gatilho de armazenamento de filas](#queue-storage-trigger-configuration)
* [Associação de SendGrid](#sendgrid-binding-configuration-version-3x)
* [Gatilho do barramento de serviço](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Configuração do gatilho CosmosDB do Azure (versão 3. *x*)

Este exemplo mostra como configurar o gatilho de Azure Cosmos DB:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddCosmosDB(a =>
        {
            a.ConnectionMode = ConnectionMode.Gateway;
            a.Protocol = Protocol.Https;
            a.LeaseOptions.LeasePrefix = "prefix1";

        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Para obter mais detalhes, consulte o artigo [Associação de CosmosDB do Azure](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings) .

### <a name="event-hubs-trigger-configuration-version-3x"></a>Configuração do gatilho de hubs de eventos (versão 3. *x*)

Este exemplo mostra como configurar o gatilho de hubs de eventos:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddEventHubs(a =>
        {
            a.BatchCheckpointFrequency = 5;
            a.EventProcessorOptions.MaxBatchSize = 256;
            a.EventProcessorOptions.PrefetchCount = 512;
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Para obter mais detalhes, consulte o artigo [Associação de hubs de eventos](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) .

### <a name="queue-storage-trigger-configuration"></a>Configuração do gatilho de armazenamento de filas

Estes exemplos mostram como configurar o gatilho de armazenamento de filas:

#### <a name="version-3x"></a>Versão 3. *x*

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage(a => {
            a.BatchSize = 8;
            a.NewBatchThreshold = 4;
            a.MaxDequeueCount = 4;
            a.MaxPollingInterval = TimeSpan.FromSeconds(15);
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Para obter mais detalhes, consulte o artigo [Associação de armazenamento de filas](../azure-functions/functions-bindings-storage-queue.md#hostjson-settings) .

#### <a name="version-2x"></a>Versão 2. *x*

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Para obter mais detalhes, consulte a [referência de host. JSON v1. x](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>Configuração de associação SendGrid (versão 3. *x*)

Este exemplo mostra como configurar a associação de saída SendGrid:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddSendGrid(a =>
        {
            a.FromAddress.Email = "samples@functions.com";
            a.FromAddress.Name = "Azure Functions";
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Para obter mais detalhes, consulte o artigo [Associação de SendGrid](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) .

### <a name="service-bus-trigger-configuration-version-3x"></a>Configuração do gatilho do barramento de serviço (versão 3. *x*)

Este exemplo mostra como configurar o gatilho do barramento de serviço:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddServiceBus(sbOptions =>
        {
            sbOptions.MessageHandlerOptions.AutoComplete = true;
            sbOptions.MessageHandlerOptions.MaxConcurrentCalls = 16;
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Para obter mais detalhes, consulte o artigo [associação do barramento de serviço](../azure-functions/functions-bindings-service-bus.md#hostjson-settings) .

### <a name="configuration-for-other-bindings"></a>Configuração de outras associações

Alguns tipos de gatilho e de associação definem seus próprios tipos de configuração personalizados. Por exemplo, o gatilho de arquivo permite especificar o caminho raiz a ser monitorado, como nestes exemplos:

#### <a name="version-3x"></a>Versão 3. *x*

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddFiles(a => a.RootPath = @"c:\data\import");
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

#### <a name="version-2x"></a>Versão 2. *x*

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>Expressões de associação

Nos parâmetros do construtor de atributo, você pode usar expressões que são resolvidas para valores de várias fontes. Por exemplo, no código a seguir, o caminho para o atributo `BlobTrigger` cria uma expressão nomeada `filename`. Quando usado para a associação de saída, `filename` é resolvido para o nome do blob de disparo.

```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

Para obter mais informações sobre expressões de associação, consulte [Padrões e expressões de associação](../azure-functions/functions-bindings-expressions-patterns.md) na documentação do Azure Functions.

### <a name="custom-binding-expressions"></a>Expressões de associação personalizadas

Às vezes, você deseja especificar um nome de fila, um nome de BLOB ou um contêiner ou um nome de tabela no código, em vez de codificá-lo embutidamente. Por exemplo, você talvez queira especificar o nome da fila para o atributo `QueueTrigger` em um arquivo de configuração ou uma variável de ambiente.

Você pode fazer isso passando um `NameResolver` objeto para o `JobHostConfiguration` objeto. Você inclui espaços reservados no gatilho ou parâmetros do construtor de atributo de associação, e seu código `NameResolver` fornece os valores reais a serem usados no lugar desses espaços reservados. Você identifica espaços reservados ao redor deles com percentual (%) os sinais, como mostrado aqui:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Este código lhe permite usar uma fila denominada `logqueuetest` no ambiente de teste e uma denominada `logqueueprod` na produção. Em vez de um nome de fila embutido em código, você especifica o nome de uma entrada na coleção `appSettings`.

Há um padrão `NameResolver` que entra em vigor se você não fornecer um personalizado. O padrão obtém valores de configurações de aplicativo ou variáveis de ambiente.

Sua `NameResolver` classe obtém o nome da `appSettings`fila, conforme mostrado aqui:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>Versão 3. *x*

Você configura o resolvedor usando injeção de dependência. Esses exemplos exigem a seguinte instrução `using`:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Você adiciona o resolvedor chamando o [`ConfigureServices`] método de extensão [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)em, como neste exemplo:

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    var resolver = new CustomNameResolver();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureServices(s => s.AddSingleton<INameResolver>(resolver));
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versão 2. *x*

Transmita `NameResolver` sua classe para o `JobHost` objeto, conforme mostrado aqui:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

O Azure Functions implementa `INameResolver` para obter valores de configurações do aplicativo, conforme mostrado no exemplo. Quando usa o WebJobs SDK diretamente, você pode escrever uma implementação personalizada que obtém valores de substituição de espaço reservado de qualquer fonte preferida.

## <a name="binding-at-runtime"></a>Associando no tempo de execução

Se você precisar fazer algum trabalho em sua função antes de usar um atributo de associação como `Queue`, `Blob`ou `Table`, você pode usar a `IBinder` interface.

O exemplo a seguir usa uma mensagem da fila de entrada e cria uma nova mensagem com o mesmo conteúdo em uma fila de saída. O nome da fila de saída é definido pelo código no corpo da função.

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessageAsync(new CloudQueueMessage(queueMessage));
}
```

Para obter mais informações, consulte [Associação no tempo de execução](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) na documentação do Azure Functions.

## <a name="binding-reference-information"></a>Informações de referência de associação

A documentação Azure Functions fornece informações de referência sobre cada tipo de associação. Você encontrará as seguintes informações em cada artigo de referência de associação. (Este exemplo é baseado na fila de armazenamento.)

* [Pacotes](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x). O pacote que você precisa instalar para incluir suporte para a associação em um projeto do SDK de trabalhos Web.
* [Exemplos](../azure-functions/functions-bindings-storage-queue.md#trigger---example). Exemplos de código. O C# exemplo de biblioteca de classes se aplica ao SDK de trabalhos Web. Basta omitir `FunctionName` o atributo.
* [Atributos](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes). Os atributos a serem usados para o tipo de associação.
* [Configuração](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration). Explicações das propriedades do atributo e dos parâmetros do construtor.
* [Uso](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Os tipos aos quais você pode associar e informações sobre como a associação funciona. Por exemplo: algoritmo de sondagem, processamento de fila de mensagens suspeita.
  
Para obter uma lista de artigos de referência de associação, consulte "associações com suporte" no artigo [gatilhos e associações](../azure-functions/functions-triggers-bindings.md#supported-bindings) para Azure functions. Nessa lista, as associações de grade de eventos, HTTP e WebHooks têm suporte apenas pelo Azure Functions, não pelo SDK de trabalhos Web.

## <a name="disable-attribute"></a>Atributo Desabilitar 

O [`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) atributo permite que você controle se uma função pode ser disparada. 

No exemplo a seguir, se a configuração `Disable_TestJob` do aplicativo tiver um valor de ou `True` (não diferencia maiúsculas de `1` minúsculas), a função não será executada. Nesse caso, o tempo de execução cria uma mensagem de log *A função 'Functions.TestJob' está desabilitada*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Quando você altera os valores de configuração do aplicativo no portal do Azure, o WebJob é reiniciado para selecionar a nova configuração.

O atributo pode ser declarado no nível de classe, método ou parâmetro. O nome da configuração pode também conter expressões de associação.

## <a name="timeout-attribute"></a>Atributo de tempo limite

O [`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) atributo faz com que uma função seja cancelada se não for concluída dentro de um período de tempo especificado. No exemplo a seguir, a função seria executada por um dia sem o atributo timeout. Timeout faz com que a função seja cancelada após 15 segundos.

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

Você pode aplicar o atributo timeout no nível de classe ou método e pode especificar um tempo limite global usando `JobHostConfiguration.FunctionTimeout`. Tempos limite de nível de classe ou de nível de método substituem tempos limite globais.

## <a name="singleton-attribute"></a>Atributo Singleton

O [`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) atributo garante que apenas uma instância de uma função seja executada, mesmo quando há várias instâncias do aplicativo Web host. Ele faz isso usando o [bloqueio distribuído](#viewing-lease-blobs).

Neste exemplo, apenas uma única instância da `ProcessImage` função é executada em um determinado momento:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Alguns gatilhos têm suporte interno para gerenciamento de simultaneidade:

* **QueueTrigger**. Defina `JobHostConfiguration.Queues.BatchSize` como `1`.
* **ServiceBusTrigger**. Defina `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` como `1`.
* **FileTrigger**. Defina `FileProcessor.MaxDegreeOfParallelism` como `1`.

Você pode usar essas configurações para garantir que sua função seja executada como um singleton em uma única instância. Para garantir que apenas uma única instância da função esteja em execução quando o aplicativo Web é dimensionado para várias instâncias, aplique um bloqueio singleton no nível do ouvinte na função`[Singleton(Mode = SingletonMode.Listener)]`(). Os bloqueios de ouvinte são adquiridos quando o JobHost é iniciado. Se três instâncias expandidas forem iniciadas ao mesmo tempo, somente uma das instâncias adquirirá o bloqueio e somente um ouvinte será iniciado.

### <a name="scope-values"></a>Valores de escopo

Você pode especificar uma *expressão/valor de escopo* em um singleton. A expressão/valor garante que todas as execuções da função em um escopo específico serão serializadas. Implementar um bloqueio mais granular dessa maneira pode permitir algum nível de paralelismo para sua função ao serializar outras invocações conforme determinado pelos seus requisitos. Por exemplo, no código a seguir, a expressão de escopo é vinculada `Region` ao valor da mensagem de entrada. Quando a fila contém três mensagens nas regiões leste, leste e oeste, respectivamente, as mensagens que têm a região leste são executadas em série enquanto a mensagem com a região oeste é executada em paralelo com aquelas no leste.

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item.
}

public class WorkItem
{
     public int ID { get; set; }
     public string Region { get; set; }
     public int Category { get; set; }
     public string Description { get; set; }
}
```

### <a name="singletonscopehost"></a>SingletonScope.Host

O escopo padrão de um bloqueio é `SingletonScope.Function`, ou seja, o escopo de bloqueio (o caminho de concessão de BLOB) está vinculado ao nome de função totalmente qualificado. Para bloquear entre funções, especifique `SingletonScope.Host` e use um nome de ID de escopo que seja o mesmo em todas as funções que você não deseja executar simultaneamente. No exemplo a seguir, apenas uma instância de `AddItem` ou `RemoveItem` é executada por vez:

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation.
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation.
}
```

### <a name="viewing-lease-blobs"></a>Exibição de blobs de concessão

O WebJobs SDK usa [concessões de blob do Azure](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) nos bastidores para implementar o bloqueio distribuído. Os blobs de concessão usados pelo singleton podem ser encontrados no `azure-webjobs-host` contêiner `AzureWebJobsStorage` na conta de armazenamento no caminho "bloqueios". Por exemplo, o caminho de blob de concessão para o primeiro exemplo `ProcessImage` mostrado anteriormente pode ser `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Todos os caminhos incluem a ID de JobHost, 061851c758f04938a4426aa9ab3869c0 neste caso.

## <a name="async-functions"></a>Funções assíncronas

Para obter informações sobre como codificar funções assíncronas, consulte a [documentação do Azure Functions](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Tokens de cancelamento

Para obter informações sobre como lidar com tokens de cancelamento, consulte a documentação do Azure Functions nos [tokens de cancelamento e desligamento normal](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Várias instâncias

Se o seu aplicativo Web for executado em várias instâncias, um WebJob contínuo será executado em cada instância, ouvindo os gatilhos e chamando funções. As várias associações de gatilho são projetadas para compartilhar com eficiência o trabalho de forma colaborativa entre instâncias, para que a expansão para mais instâncias permita que você manipule mais carga.

Os gatilhos de fila e de blob impedem automaticamente que uma função processe uma mensagem de fila ou BLOB mais de uma vez; as funções não precisam ser idempotentes.

O gatilho de timer garante que apenas uma instância do timer seja executada, portanto você não terá mais de uma instância de função em execução em um determinado horário agendado.

Se você quiser garantir que apenas uma instância de uma função seja executada mesmo quando houver várias instâncias do aplicativo Web host, você poderá usar o [`Singleton`](#singleton-attribute) atributo.

## <a name="filters"></a>Filtros

Filtros de função (visualização) fornecem uma maneira de personalizar o pipeline de execução de WebJobs com sua própria lógica. Filtros são semelhantes a [filtros de ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Você pode implementá-los como atributos declarativos que são aplicados às suas funções ou classes. Para obter mais informações, consulte [Filtros de Função](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Log e monitoramento

Recomendamos a estrutura de registro em log desenvolvida para ASP.NET. O [artigo](webjobs-sdk-get-started.md) de introdução mostra como usá-lo. 

### <a name="log-filtering"></a>Filtragem de linha

Cada log criado por uma instância de `ILogger` possui um `Category` e `Level` associados. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)é uma enumeração, e o código inteiro indica importância relativa:

|LogLevel    |Código|
|------------|---|
|Rastreamento       | 0 |
|Depurar       | 1 |
|Information | 2 |
|Aviso     | 3 |
|Erro       | 4 |
|Crítica    | 5 |
|Nenhum        | 6 |

Você pode filtrar de forma independente cada categoria em [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel)um determinado. Por exemplo, você talvez queira ver todos os logs para o processamento de gatilho de blob, mas apenas `Error` e superiores para todo o resto.

#### <a name="version-3x"></a>Versão 3. *x*

Versão 3. *x* do SDK depende da filtragem interna do .NET Core. A classe `LogCategories` permite que você defina categorias para funções, gatilhos e usuários específicos. Ele também define filtros para Estados de host específicos, `Startup` como `Results`e. Isso permite que você ajuste a saída de log. Se nenhuma correspondência for encontrada nas categorias definidas, o filtro reverterá para o valor `Default` ao decidir se deseja filtrar a mensagem.

`LogCategories` requer a seguinte declaração de uso:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

O exemplo a seguir constrói um filtro que, por padrão, filtra todos os logs no `Warning` nível. As `Function` categorias `results` e (equivalente a `Host.Results` na versão 2. *x*) são filtrados no `Error` nível. O filtro compara a categoria atual com todos os níveis registrados na instância `LogCategories` e escolhe a maior correspondência. Isso significa que o `Debug` nível registrado para `Host.Triggers` corresponde `Host.Triggers.Queue` a `Host.Triggers.Blob`ou. Isso permite que você controle categorias mais amplas sem a necessidade de adicionar cada uma delas.

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging(logging =>
            {
                logging.SetMinimumLevel(LogLevel.Warning);
                logging.AddFilter("Function", LogLevel.Error);
                logging.AddFilter(LogCategories.CreateFunctionCategory("MySpecificFunctionName"),
                    LogLevel.Debug);
                logging.AddFilter(LogCategories.Results, LogLevel.Error);
                logging.AddFilter("Host.Triggers", LogLevel.Debug);
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versão 2. *x*

Na versão 2. *x* do SDK, você usa a classe `LogCategoryFilter` para controlar a filtragem. O `LogCategoryFilter` tem uma `Default` Propriedade com um valor inicial de `Information`, o que `Error`significa que todas as `Information`mensagens `Warning`nos níveis, `Critical` , ou são registradas, mas todas as `Debug` mensagens no ou `Trace` os níveis são filtrados fora.

Assim como `LogCategories` acontece com a versão 3. *x*, a `CategoryLevels` propriedade permite que você especifique os níveis de log para categorias específicas, para que você possa ajustar a saída de log. Se nenhuma correspondência for encontrada no dicionário `CategoryLevels`, o filtro reverterá para o valor `Default` ao decidir se deseja filtrar a mensagem.

O exemplo a seguir constrói um filtro que, por padrão, filtra todos os logs no nível de `Warning`. As `Function` `Host.Results`categoriase são filtradas no nível.`Error` O `LogCategoryFilter` compara a categoria atual com todos os `CategoryLevels` registrados e escolhe a maior correspondência. Portanto, `Debug` o nível registrado `Host.Triggers` para será `Host.Triggers.Queue` correspondente `Host.Triggers.Blob`ou. Isso permite que você controle categorias mais amplas sem a necessidade de adicionar cada uma delas.

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Telemetria personalizada do Application Insights​

O processo de implementação de telemetria personalizada para [Application insights](../azure-monitor/app/app-insights-overview.md) depende da versão do SDK. Para saber como configurar o Application Insights, confira [Adicionar registro em log do Application Insights](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Versão 3. *x*

Porque a versão 3. *x* do SDK de trabalhos Web depende do host genérico do .NET Core, uma fábrica de telemetria personalizada não é mais fornecida. Mas você pode adicionar telemetria personalizada ao pipeline usando injeção de dependência. Os exemplos nesta seção exigem as seguintes declarações `using`:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

A seguinte implementação personalizada de [`ITelemetryInitializer`] permite que você adicione seu próprio [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) ao padrão [`TelemetryConfiguration`].

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Chame [`ConfigureServices`] no construtor para adicionar seu item personalizado [`ITelemetryInitializer`] ao pipeline.

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging((context, b) =>
    {
        // Add logging providers.
        b.AddConsole();

        // If this key exists in any config, use it to enable Application Insights.
        string appInsightsKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
        if (!string.IsNullOrEmpty(appInsightsKey))
        {
            // This uses the options callback to explicitly set the instrumentation key.
            b.AddApplicationInsights(o => o.InstrumentationKey = appInsightsKey);
        }
    });
    builder.ConfigureServices(services =>
        {
            services.AddSingleton<ITelemetryInitializer, CustomTelemetryInitializer>();
        });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Quando o [`TelemetryConfiguration`] é construído, todos os tipos registrados de [`ITelemetryInitializer`] são incluídos. Para saber mais, consulte [API de Application insights para métricas e eventos personalizados](../azure-monitor/app/api-custom-events-metrics.md).

Na versão 3. *x*, você não precisa mais liberar o [`TelemetryClient`] quando o host parar. O sistema de injeção de dependência do .NET Core automaticamente descarta o `ApplicationInsightsLoggerProvider` registrado que libera o [`TelemetryClient`].

#### <a name="version-2x"></a>Versão 2. *x*

Na versão 2. *x*, o [`TelemetryClient`] criado internamente pelo provedor de Application insights para o SDK de trabalhos Web [`ServerTelemetryChannel`](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)usa. Quando o ponto de extremidade do Application Insights está indisponível ou limitando solicitações de entrada, este canal [salva as solicitações no sistema de arquivos do aplicativo Web e reenvia-as depois](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

O [`TelemetryClient`] é criado por uma classe que implementa `ITelemetryClientFactory`. Por padrão, é [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Se quiser modificar qualquer parte do pipeline do Application Insights, você poderá fornecer seu próprio `ITelemetryClientFactory` e o host usará sua classe para construir um [`TelemetryClient`]. Por exemplo, esse código substitui `DefaultTelemetryClientFactory` para modificar uma propriedade de `ServerTelemetryChannel`:

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // Change the default from 30 seconds to 15 seconds.
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

O `SamplingPercentageEstimatorSettings` objeto configura a [amostragem adaptável](https://docs.microsoft.com/azure/application-insights/app-insights-sampling). Isso significa que em determinados cenários de alto volume, o Application insights envia um subconjunto selecionado de dados de telemetria para o servidor.

Depois de criar a fábrica de telemetria, passe-a para o provedor de log de Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> Próximas etapas

Este artigo fornece trechos de código que mostram como lidar com cenários comuns para trabalhar com o SDK de trabalhos Web. Para obter exemplos completos, consulte [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk-samples).

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[`JobHostConfiguration`]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
