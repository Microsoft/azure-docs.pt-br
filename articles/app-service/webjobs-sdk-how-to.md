---
title: Como usar o SDK do WebJobs
description: Saiba mais sobre como escrever código para o WebJobs SDK. Crie trabalhos de processamento em segundo plano controlado por evento que acessam dados no Azure e em serviços de terceiros.
author: ggailey777
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 063924dccb7d7b95b962b24ecc1af1870a855194
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617124"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Como usar o SDK do Azure WebJobs para o processamento em segundo plano controlado por evento

Este artigo fornece orientações sobre como trabalhar com o SDK do Azure WebJobs. Para começar a usar o WebJobs imediatamente, consulte [Introdução ao SDK do Azure WebJobs para processamento em segundo plano orientado a eventos](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>Versões do SDK do WebJobs

Essas são as principais diferenças entre a versão 3.*x* e a versão 2.*x* do SDK do WebJobs:

* A versão 3.*x* adiciona suporte para .NET Core.
* Na versão 3.*x*, você precisa instalar explicitamente a extensão de associação de armazenamento exigida pelo SDK do WebJobs. Na versão 2.*x*, as associações de armazenamento foram incluídas no SDK.
* As ferramentas do Visual Studio para projetos do .NET Core (3.*x*) diferem de ferramentas para projetos do .NET Framework (2.*x*). Para saber mais, consulte [Desenvolver e implantar trabalhos do WebJobs usando o Visual Studio — Serviço de Aplicativo do Azure](webjobs-dotnet-deploy-vs.md).

Quando possível, são fornecidos exemplos para a versão 3.*x* e a versão 2.*x*.

> [!NOTE]
> O [Azure Functions](../azure-functions/functions-overview.md) é compilado no SDK do WebJobs, e este artigo fornece links para a documentação do Azure Functions para alguns tópicos. Observe as seguintes diferenças entre o Functions e o SDK do WebJobs:
> * O Azure Functions versão 2.*x* corresponde à versão 3.*x* do SDK do WebJobs, e a versão 1.*x* do Azure Functions corresponde à versão 2.*x* do SDK do WebJobs. Os repositórios do código-fonte usam a numeração do SDK do WebJobs.
> * O código de exemplo para bibliotecas de classe C# do Azure Functions é como o código do SDK do WebJobs, porém, não é necessário um atributo `FunctionName` em um projeto do SDK do WebJobs.
> * Alguns tipos de ligação têm suporte apenas no Functions, como HTTP (WebHooks) e na Grade de Eventos (que é baseada em HTTP).
>
> Para obter mais informações, consulte [Comparar o WebJobs SDK e o Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>Host do WebJobs

O host é um contêiner de runtime para funções.  Ele escuta gatilhos e chamadas de funções. Na versão 3.*x*, o host é uma implementação de `IHost`. Na versão 2.*x*, use o objeto `JobHost`. Você cria uma instância do host em seu código e escreve um código para personalizar seu comportamento.

Essa é uma diferença importante entre usar diretamente o SDK do WebJobs e usá-lo indiretamente pelo Azure Functions. No Azure Functions, o serviço controla o host e não é possível personalizá-lo gravando o código. O Azure Functions permite que você personalize o comportamento de host por meio de configurações no arquivo host.json. Essas configurações são cadeias de caracteres, e não código, que limitam os tipos de personalizações que você pode fazer.

### <a name="host-connection-strings"></a>Cadeias de conexão do host

O SDK do WebJobs procura cadeias de conexão de Armazenamento do Azure e Barramento de Serviço do Azure no arquivo local.settings.json quando você executa localmente ou no ambiente do WebJob quando você executa no Azure. Por padrão, uma configuração de cadeia de conexão de armazenamento denominada `AzureWebJobsStorage` é obrigatória.  

A versão 2.*x* do SDK permite usar seus próprios nomes para essas cadeias de conexão ou armazená-las em outro lugar. Você pode definir nomes no código usando o [`JobHostConfiguration`], conforme mostrado aqui:

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

> [!NOTE]
> Porque a versão 3.*x* usa as APIs de configuração padrão do .NET Core, não há API para alterar os nomes da cadeia de conexão. Consulte [desenvolver e implantar trabalhos Web usando o Visual Studio](webjobs-dotnet-deploy-vs.md)

### <a name="host-development-settings"></a>Configurações de desenvolvimento do host

Você pode executar o host no modo de desenvolvimento para tornar o desenvolvimento local mais eficiente. Veja algumas das configurações que são alteradas quando você executa no modo de desenvolvimento:

| Propriedade | Configuração de desenvolvimento |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` para maximizar a saída de log. |
| `Queues.MaxPollingInterval`  | Um valor baixo para garantir que os métodos de fila sejam disparados imediatamente.  |
| `Singleton.ListenerLockPeriod` | Quinze segundos para ajudar no desenvolvimento iterativo rápido. |

O processo para habilitar o modo de desenvolvimento depende da versão do SDK. 

#### <a name="version-3x"></a>Versão 3.*x*

Versão 3.*x* usa as APIs padrão do ASP.NET Core. Chame o método [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) na instância [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder). Passe uma cadeia de caracteres denominada `development`, conforme neste exemplo:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versão 2.*x*

A classe `JobHostConfiguration` tem um método `UseDevelopmentSettings` que habilita o modo de desenvolvimento.  O exemplo a seguir mostra como usar as configurações de desempenho. Para fazer `config.IsDevelopment` retornar `true` quando ele é executado localmente, defina uma variável de ambiente local chamada `AzureWebJobsEnv` com o valor `Development`.

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

### <a name="managing-concurrent-connections-version-2x"></a><a name="jobhost-servicepointmanager-settings"></a>Gerenciando conexões simultâneas (versão 2.*x*)

Na versão 3.*x*, o limite de conexão assume como padrão conexões infinitas. Se, por alguma razão, você precisar alterar esse limite, poderá usar a propriedade [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) da classe [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler).

Na versão 2.*x*, você controla o número de conexões simultâneas com um host usando a API [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit). Na 2.*x*, é recomendável aumentar esse valor a partir do padrão 2 antes de iniciar o host do WebJobs.

Todas as solicitações HTTP de saída feitas por meio de uma função usando o fluxo de `HttpClient` por meio do `ServicePointManager`. Depois de alcançar o valor definido em `DefaultConnectionLimit`, `ServicePointManager` iniciará as solicitações de enfileiramento antes de enviá-las. Suponha que seu `DefaultConnectionLimit` seja definido como 2 e seu código faça 1.000 solicitações HTTP. Inicialmente, apenas duas solicitações são permitidas por meio do sistema operacional. As outras 998 são colocadas na fila até que haja espaço para elas. Isso significa que seu `HttpClient` poderá atingir o tempo limite, porque ele parece ter feito a solicitação, mas esta nunca foi enviada pelo sistema operacional para o servidor de destino. Para que você possa ver o comportamento que não faz sentido: o `HttpClient` local está demorando 10 segundos para concluir uma solicitação, mas o serviço está retornando cada solicitação em 200 ms. 

O valor padrão para aplicativos ASP.NET é `Int32.MaxValue`, e é provável que funcione bem para WebJobs em execução em um Plano do Serviço de Aplicativo Básico ou superior. Normalmente, os WebJobs precisam da configuração Always On, que é suportada apenas por um Plano do Serviço de Aplicativo Básico ou superior.

Caso o WebJob esteja em execução em um Plano do Serviço de Aplicativo Gratuito ou Compartilhado, seu aplicativo ficará restrito à área restrita do Serviço de Aplicativo, que atualmente tem um [limite de conexão de 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Com um limite de conexão não associado no `ServicePointManager`, é mais provável que o limite de conexão de área restrita seja atingido e o site seja desativado. Nesse caso, a configuração de `DefaultConnectionLimit` para algo inferior, como 50 ou 100, pode evitar que isso aconteça e ainda permitir uma taxa de transferência suficiente.

A configuração deve ser definida antes de todas as solicitações HTTP serem feitas. Por esse motivo, o host do WebJobs não deve ajustar a configuração automaticamente. Poderá haver solicitações HTTP que ocorrem antes do início do host, o que pode levar a um comportamento inesperado. A melhor abordagem é definir o valor imediatamente no método `Main` antes de inicializar o `JobHost`, conforme mostrado aqui:

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

As funções devem ser métodos públicos e devem ter um atributo de gatilho ou o atributo [`NoAutomaticTrigger`](#manual-triggers).

### <a name="automatic-triggers"></a>Gatilhos automáticos

Os disparadores automáticos chamam uma função em resposta a um evento. Considere este exemplo de uma função que é disparada por uma mensagem adicionada ao armazenamento de Filas do Azure. Ele responde lendo um blob do Armazenamento de Blobs do Azure:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

O atributo `QueueTrigger` informa o tempo de execução para chamar a função sempre que uma mensagem de fila aparecer na fila `myqueue-items`. O atributo `Blob` informa o tempo de execução para usar a mensagem da fila para ler um blob no contêiner *sample-workitems*. O nome do item de blob no contêiner `samples-workitems` é obtido diretamente no gatilho de fila como uma expressão de associação (`{queueTrigger}`).

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>Gatilhos manuais

Para disparar uma função manualmente, use o atributo `NoAutomaticTrigger`, conforme mostrado aqui:

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

#### <a name="version-3x"></a>Versão 3.*x*

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

#### <a name="version-2x"></a>Versão 2.*x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Associações de entrada e saída

As associações de entrada e saída fornecem uma maneira declarativa para criar dados a partir do Azure ou de serviços externos disponíveis para seu código. As associações de saída fornecem uma maneira de atualizar os dados. O artigo [Introdução](webjobs-sdk-get-started.md) mostra um exemplo de cada um.

Você pode usar um valor de retorno do método para uma associação de saída, aplicando o atributo ao valor de retorno do método. Consulte o exemplo em [Usando o valor de retorno da Função do Azure](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Tipos de associação

O processo de instalação e gerenciamento de tipos de associação depende se você está usando a versão 3.*x* ou a versão 2.*x* do SDK. Você pode encontrar o pacote a ser instalado para um tipo de associação específico na seção “Pacotes” desse tipo de associação no [artigo de referência](#binding-reference-information) do Azure Functions. Uma exceção é o gatilho e a associação de Arquivos (para o sistema de arquivos local), o que não é suportado pelo Azure Functions.

#### <a name="version-3x"></a>Versão 3.*x*

Na versão 3.*x*, as associações de armazenamento são incluídas no pacote `Microsoft.Azure.WebJobs.Extensions.Storage`. Chame o método de extensão `AddAzureStorage` no método `ConfigureWebJobs`, como mostrado aqui:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Para usar outros tipos de gatilho e associação, instale o pacote do NuGet que os contém e chame o método de extensão `Add<binding>` implementado na extensão. Por exemplo, se você quiser usar uma associação do Azure Cosmos DB, instale `Microsoft.Azure.WebJobs.Extensions.CosmosDB` e chame `AddCosmosDB`, desta forma:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Para usar o gatilho de Temporizador ou a associação de Arquivos, que fazem parte dos serviços principais, chame os métodos de extensão `AddTimers` ou `AddFiles`, respectivamente.

#### <a name="version-2x"></a>Versão 2.*x*

Esses tipos de gatilho e de associação estão incluídos na versão 2.*x* do pacote `Microsoft.Azure.WebJobs`:

* Armazenamento de blob
* Armazenamento de filas
* Armazenamento de tabela

Para usar outros tipos de associação e gatilho, instale o pacote do NuGet que os contém e chame um método `Use<binding>` no objeto `JobHostConfiguration`. Por exemplo, se você quiser usar um gatilho de Temporizador, instale `Microsoft.Azure.WebJobs.Extensions` e chame `UseTimers` no método `Main`, como mostrado aqui:

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

O processo de associação ao [`ExecutionContext`] depende da versão do SDK.

#### <a name="version-3x"></a>Versão 3.*x*

Chame o método de extensão `AddExecutionContextBinding` no método `ConfigureWebJobs`, como mostrado aqui:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versão 2.*x*

O pacote `Microsoft.Azure.WebJobs.Extensions` mencionado anteriormente também fornece um tipo especial de associação que você pode registrar chamando o método `UseCore`. Essa associação permite definir um parâmetro [`ExecutionContext`] em sua assinatura de função, que é habilitada da seguinte maneira:

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

* **Versão 3.*x*:** Defina a configuração quando o método `Add<Binding>` for chamado em `ConfigureWebJobs`.
* **Versão 2.*x*:** Defina a configuração definindo as propriedades em um objeto de configuração que você passa para `JobHost`.

Essas configurações específicas de associação são equivalentes às configurações no [arquivo de projeto host.json](../azure-functions/functions-host-json.md) no Azure Functions.

É possível configurar as seguintes associações:

* [Gatilho de CosmosDB do Azure](#azure-cosmosdb-trigger-configuration-version-3x)
* [Gatilho de Hubs de Eventos](#event-hubs-trigger-configuration-version-3x)
* [Gatilho de armazenamento de filas](#queue-storage-trigger-configuration)
* [Associação de SendGrid](#sendgrid-binding-configuration-version-3x)
* [Gatilho de Barramento de Serviço](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Configuração do gatilho CosmosDB do Azure (versão 3.*x*)

Este exemplo mostra como configurar o gatilho do Azure Cosmos DB:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Para obter mais detalhes, consulte o artigo [Associação do Azure CosmosDB](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings).

### <a name="event-hubs-trigger-configuration-version-3x"></a>Configuração do gatilho dos Hubs de Eventos (versão 3.*x*)

Este exemplo mostra como configurar o gatilho dos Hubs de Eventos:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Para obter mais detalhes, consulte o artigo [Associação dos Hubs de Eventos](../azure-functions/functions-bindings-event-hubs.md#host-json).

### <a name="queue-storage-trigger-configuration"></a>Configuração do gatilho de armazenamento de Filas

Estes exemplos mostram como configurar o gatilho de armazenamento de Filas:

#### <a name="version-3x"></a>Versão 3.*x*

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Para obter mais detalhes, consulte o artigo [Associação do armazenamento de filas](../azure-functions/functions-bindings-storage-queue-trigger.md#hostjson-properties).

#### <a name="version-2x"></a>Versão 2.*x*

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

Para obter mais detalhes, consulte a [referência host.json v1.x](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>Configuração de associação SendGrid (versão 3.*x*)

Este exemplo mostra como configurar a associação de saída SendGrid:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Para obter mais detalhes, consulte o artigo [Associação SendGrid](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings).

### <a name="service-bus-trigger-configuration-version-3x"></a>Configuração do gatilho do Barramento de Serviço (versão 3.*x*)

Este exemplo mostra como configurar o gatilho do Barramento de Serviço:

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Para obter mais detalhes, consulte o artigo [Associação do Barramento de Serviço](../azure-functions/functions-bindings-service-bus-output.md#hostjson-settings).

### <a name="configuration-for-other-bindings"></a>Configuração de outras associações

Alguns tipos de gatilho e associação definem seus próprios tipos de configuração personalizados. Por exemplo, o gatilho de Arquivo permite especificar o caminho raiz de monitoramento, conforme mostrado nestes exemplos:

#### <a name="version-3x"></a>Versão 3.*x*

```cs
static async Task Main()
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
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Versão 2.*x*

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

Às vezes, você deseja especificar um nome de fila, um nome de blob ou contêiner ou um nome de tabela no código em vez de embuti-lo no código. Por exemplo, você talvez queira especificar o nome da fila para o atributo `QueueTrigger` em um arquivo de configuração ou uma variável de ambiente.

Você pode fazer isso passando um objeto `NameResolver` para o objeto `JobHostConfiguration`. Você inclui espaços reservados no gatilho ou parâmetros do construtor de atributo de associação, e seu código `NameResolver` fornece os valores reais a serem usados no lugar desses espaços reservados. Você identifica espaços reservados ao redor deles com sinais de percentual (%), como mostrado aqui:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Este código lhe permite usar uma fila denominada `logqueuetest` no ambiente de teste e uma denominada `logqueueprod` na produção. Em vez de um nome de fila embutido em código, você especifica o nome de uma entrada na coleção `appSettings`.

Há um `NameResolver` padrão que entra em vigor se você não fornecer um personalizado. O padrão obtém valores de configurações de aplicativo ou variáveis de ambiente.

Sua classe `NameResolver` obtém o nome da fila em `appSettings`, conforme mostrado aqui:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>Versão 3.*x*

Você configura o resolvedor usando injeção de dependência. Esses exemplos exigem a seguinte instrução `using`:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Você adiciona o resolvedor chamando o método de extensão [`ConfigureServices`] no [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder), como neste exemplo:

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

#### <a name="version-2x"></a>Versão 2.*x*

Transfira a classe `NameResolver` para o objeto `JobHost`, conforme mostrado aqui:

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

## <a name="binding-at-runtime"></a>Associando no runtime

Se você precisar fazer algum trabalho em sua função antes de usar um atributo de associação como `Queue`, `Blob` ou `Table`, poderá usar a interface `IBinder`.

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

Para obter mais informações, consulte [Associação no runtime](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) na documentação do Azure Functions.

## <a name="binding-reference-information"></a>Informações de referência de associação

A documentação do Azure Functions fornece informações de referência sobre cada tipo de associação. Você encontrará as informações a seguir em cada artigo de referência de associação. (Este exemplo é baseado na Fila de armazenamento.)

* [Pacotes](../azure-functions/functions-bindings-storage-queue.md). O pacote que você precisa instalar para incluir suporte para a associação em um projeto do SDK do WebJobs.
* [Exemplos](../azure-functions/functions-bindings-storage-queue-trigger.md). Exemplos de código. O exemplo da biblioteca de classes C# se aplica ao SDK do WebJobs. Basta omitir o atributo `FunctionName`.
* [Atributos](../azure-functions/functions-bindings-storage-queue-trigger.md#attributes-and-annotations). Os atributos a serem usados para o tipo de associação.
* [Configuração](../azure-functions/functions-bindings-storage-queue-trigger.md#configuration). Explicações sobre as propriedades de atributo e os parâmetros do construtor.
* [Uso](../azure-functions/functions-bindings-storage-queue-trigger.md#usage). A quais tipos você pode associar e informações sobre como a associação funciona. Por exemplo: algoritmo de sondagem, processamento de fila de mensagens suspeita.
  
Para obter uma lista de artigos de referência de associação, consulte “Associações suportadas” no artigo [Gatilhos e associações](../azure-functions/functions-triggers-bindings.md#supported-bindings) do Azure Functions. Nessa lista, as associações de HTTP, Webhooks e Grade de Eventos são suportadas somente pelo Azure Functions, não pelo SDK do WebJobs.

## <a name="disable-attribute"></a>Atributo Desabilitar 

O atributo [`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) permite que você controle se uma função pode ser disparada. 

No exemplo a seguir, se a configuração do aplicativo `Disable_TestJob` tiver um valor `1` ou `True` (sem distinção entre maiúsculas e minúsculas), a função não será executada. Nesse caso, o runtime cria uma mensagem de log *A função 'Functions.TestJob' está desabilitada*.

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

O atributo [`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) faz com que uma função seja cancelada se ela não for concluída dentro de um período especificado. No exemplo a seguir, a função seria executada por um dia sem o atributo Timeout. O Timeout faz com que a função seja cancelada após 15 segundos.

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

Você pode aplicar o atributo Timeout no nível de classe ou método e especificar um tempo limite global usando `JobHostConfiguration.FunctionTimeout`. Os tempos limites no nível de classe ou de método substituem os tempos limites globais.

## <a name="singleton-attribute"></a>Atributo Singleton

Use o atributo [`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) assegura que apenas uma instância de uma função seja executada, mesmo quando houver várias instâncias do aplicativo Web host. Ele faz isso usando o [bloqueio distribuído](#viewing-lease-blobs).

Neste exemplo, apenas uma única instância da função `ProcessImage` é executada em um determinado momento:

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

Você pode usar essas configurações para garantir que sua função seja executada como um singleton em uma única instância. Para garantir que apenas uma única instância da função esteja em execução quando o aplicativo Web for expandido para várias instâncias, aplique um bloqueio de Singleton em nível de ouvinte na função (`[Singleton(Mode = SingletonMode.Listener)]`). Os bloqueios de ouvinte são adquiridos quando o JobHost é iniciado. Se três instâncias expandidas forem iniciadas ao mesmo tempo, somente uma das instâncias adquirirá o bloqueio e somente um ouvinte será iniciado.

> [!NOTE]
> Consulte este [repositório GitHub](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonMode.cs) para saber mais sobre como a função singleton. Function funciona.

### <a name="scope-values"></a>Valores de escopo

Você pode especificar uma *expressão/valor de escopo* em um singleton. A expressão/valor garante que todas as execuções da função em um escopo específico serão serializadas. Implementar um bloqueio mais granular dessa maneira pode permitir algum nível de paralelismo para sua função e, ao mesmo tempo, serializar outras invocações, conforme definido pelos seus requisitos. Por exemplo, no código a seguir, a expressão de escopo é associada ao valor `Region` da mensagem de entrada. Quando a fila contiver três mensagens nas regiões Leste, Leste e Oeste respectivamente, então, as mensagens com a região Leste serão executadas em série enquanto a mensagem com a região Oeste será executada em paralelo com as mensagens na região Leste.

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

O escopo padrão para um bloqueio é `SingletonScope.Function`, o que significa que o escopo de bloqueio (o caminho de concessão do blob) está vinculado ao nome da função totalmente qualificada. Para bloquear funções, especifique `SingletonScope.Host` e use um nome de ID de escopo que é o mesmo em todas as funções que você não deseja executar simultaneamente. No exemplo a seguir, apenas uma instância de `AddItem` ou `RemoveItem` é executada por vez:

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

O WebJobs SDK usa [concessões de blob do Azure](../storage/blobs/concurrency-manage.md#pessimistic-concurrency-for-blobs) nos bastidores para implementar o bloqueio distribuído. Os blobs de concessão usados pelo Singleton podem ser encontrados no contêiner `azure-webjobs-host` na conta de armazenamento `AzureWebJobsStorage` no caminho “bloqueios”. Por exemplo, o caminho de blob de concessão para o primeiro exemplo `ProcessImage` mostrado anteriormente pode ser `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Todos os caminhos incluem a ID de JobHost, 061851c758f04938a4426aa9ab3869c0 neste caso.

## <a name="async-functions"></a>Funções assíncronas

Para obter informações sobre como codificar funções assíncronas, consulte a documentação do [Azure Functions](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Tokens de cancelamento

Para obter informações sobre como lidar com tokens de cancelamento, consulte a documentação do Azure Functions nos [tokens de cancelamento e desligamento normal](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Várias instâncias

Se o seu aplicativo Web for executado em várias instâncias, um WebJob contínuo será executado em cada instância, ouvindo os gatilhos e chamando funções. As várias associações de gatilho são projetadas para compartilhar com eficiência o trabalho de forma colaborativa entre instâncias, para que a expansão para mais instâncias permita que você manipule mais carga.

Embora alguns gatilhos possam resultar em processamento duplo, os gatilhos de armazenamento de fila e de blob impedem automaticamente que uma função processe uma mensagem de fila ou de blob mais de uma vez. Para obter mais informações, consulte [Design para entrada idêntica](../azure-functions/functions-idempotent.md) na documentação do Azure Functions.

O gatilho de timer garante que apenas uma instância do timer seja executada, portanto você não terá mais de uma instância de função em execução em um determinado horário agendado.

No entanto, se desejar garantir que apenas uma instância de uma função seja executada, mesmo quando houver várias instâncias do aplicativo Web host, é possível usar o atributo [`Singleton`](#singleton-attribute).

## <a name="filters"></a>Filtros

Filtros de função (visualização) fornecem uma maneira de personalizar o pipeline de execução de WebJobs com sua própria lógica. Os filtros são semelhantes aos filtros do [ASP.NET Core](/aspnet/core/mvc/controllers/filters). Você pode implementá-los como atributos declarativos que são aplicados às suas funções ou classes. Para obter mais informações, consulte [Filtros de Função](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Log e monitoramento

Recomendamos a estrutura de registro em log que foi desenvolvida para ASP.NET. O artigo [Introdução](webjobs-sdk-get-started.md) mostra como usá-lo. 

### <a name="log-filtering"></a>Filtragem de linha

Cada log criado por uma instância de `ILogger` possui um `Category` e `Level` associados. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) é uma enumeração e o código inteiro indica importância relativa:

|LogLevel    |Código|
|------------|---|
|Trace       | 0 |
|Depurar       | 1 |
|Informações | 2 |
|Aviso     | 3 |
|Erro       | 4 |
|Crítico    | 5 |
|Nenhum        | 6 |

Você pode filtrar de forma independente cada categoria em um determinado [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel). Por exemplo, você talvez queira ver todos os logs para o processamento de gatilho de blob, mas apenas `Error` e superiores para todo o resto.

#### <a name="version-3x"></a>Versão 3.*x*

A versão 3.*x* do SDK depende da filtragem integrada ao .NET Core. A classe `LogCategories` permite que você defina categorias para funções, gatilhos e usuários específicos. Ela também define filtros para estados de host específicos, como `Startup` e `Results`. Isso permite que você ajuste a saída de log. Se nenhuma correspondência for encontrada nas categorias definidas, o filtro reverterá para o valor `Default` ao decidir se deseja filtrar a mensagem.

`LogCategories` requer a seguinte declaração de uso:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

O exemplo a seguir constrói um filtro que, por padrão, filtra todos os logs no nível de `Warning`. As categorias `Function` e `results` (equivalente a `Host.Results` na versão 2.*x*) são filtradas no nível de `Error`. O filtro compara a categoria atual com todos os níveis registrados na instância `LogCategories` e escolhe a maior correspondência. Isso significa que o nível de `Debug` registrado para `Host.Triggers` corresponde a `Host.Triggers.Queue` ou `Host.Triggers.Blob`. Isso permite que você controle categorias mais amplas sem a necessidade de adicionar cada uma delas.

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

#### <a name="version-2x"></a>Versão 2.*x*

Na versão 2.*x* do SDK, use a classe `LogCategoryFilter` para controlar a filtragem. O `LogCategoryFilter` tem uma propriedade `Default` com um valor inicial de `Information`, o que significa que todas as mensagens nos níveis `Information`, `Warning`, `Error` ou `Critical` são registradas, mas todas as mensagens nos níveis `Debug` ou `Trace` são filtradas.

Tal como acontece com `LogCategories` na versão 3.*x*, a propriedade `CategoryLevels` permite que você especifique níveis de log para categorias específicas para que você possa ajustar a saída de log. Se nenhuma correspondência for encontrada no dicionário `CategoryLevels`, o filtro reverterá para o valor `Default` ao decidir se deseja filtrar a mensagem.

O exemplo a seguir constrói um filtro que, por padrão, filtra todos os logs no nível de `Warning`. As categorias `Function` ou `Host.Results` são filtradas no nível de `Error`. O `LogCategoryFilter` compara a categoria atual com todos os `CategoryLevels` registrados e escolhe a maior correspondência. Portanto, o nível de `Debug` registrado para `Host.Triggers` corresponderá a `Host.Triggers.Queue` ou `Host.Triggers.Blob`. Isso permite que você controle categorias mais amplas sem a necessidade de adicionar cada uma delas.

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

O processo de implementação de telemetria personalizada para [Application Insights](../azure-monitor/app/app-insights-overview.md) depende da versão do SDK. Para saber como configurar o Application Insights, confira [Adicionar registro em log do Application Insights](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Versão 3.*x*

Como a versão 3.*x* do SDK do WebJobs depende do host genérico do .NET Core, um alocador de telemetria personalizada já não é mais fornecido. No entanto, você pode adicionar telemetria personalizada ao pipeline usando a injeção de dependência. Os exemplos nesta seção exigem as seguintes declarações `using`:

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
static async Task Main()
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
        await host.RunAsync();
    }
}
```

Quando o [`TelemetryConfiguration`] é construído, todos os tipos registrados de [`ITelemetryInitializer`] são incluídos. Para saber mais, consulte [API do Application Insights para métricas e eventos personalizados](../azure-monitor/app/api-custom-events-metrics.md).

Na versão 3.*x*, você já não precisa liberar o [`TelemetryClient`] quando o host é interrompido. O sistema de injeção de dependência do .NET Core automaticamente descarta o `ApplicationInsightsLoggerProvider` registrado que libera o [`TelemetryClient`].

#### <a name="version-2x"></a>Versão 2.*x*

Na versão 2.*x*, o [`TelemetryClient`] criado internamente pelo provedor do Application Insights para o SDK do WebJobs usa [`ServerTelemetryChannel`](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/.publicApi/Microsoft.AI.ServerTelemetryChannel.dll). Quando o ponto de extremidade do Application Insights está indisponível ou limitando solicitações de entrada, este canal [salva as solicitações no sistema de arquivos do aplicativo Web e reenvia-as depois](https://apmtips.com/posts/2015-09-03-more-telemetry-channels/).

O [`TelemetryClient`] é criado por uma classe que implementa `ITelemetryClientFactory`. Por padrão, é [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/).

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

O objeto `SamplingPercentageEstimatorSettings` configura a [amostragem adaptável](../azure-monitor/app/sampling.md). Isso significa que em determinados cenários de alto volume, o Application Insights envia um subconjunto selecionado de dados de telemetria para o servidor.

Depois de criar o alocador de telemetria, transfira-o para o provedor de logs do Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a name="next-steps"></a><a id="nextsteps"></a> Próximas etapas

Este artigo forneceu snippets de código que mostram como lidar com cenários comuns para trabalhar com o SDK do WebJobs. Para obter exemplos completos, consulte [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost).

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[`JobHostConfiguration`]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs