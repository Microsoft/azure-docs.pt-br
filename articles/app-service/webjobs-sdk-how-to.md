---
title: Como usar o SDK WebJobs - Azure
description: Saiba mais sobre como escrever código para o WebJobs SDK. Crie plano de fundo controlada por evento trabalhos de processamento que acessam dados em serviços do Azure e serviços de terceiros.
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
ms.openlocfilehash: 38d8bdfcba48d2080b434ebec192b41f3663ae6a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831785"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Como usar o SDK do Azure WebJobs para o processamento em segundo plano controlado por evento

Este artigo fornece orientação sobre como trabalhar com o SDK de WebJobs do Azure. Para se familiarizar com o WebJobs imediatamente, consulte [Introdução ao SDK de WebJobs do Azure para processamento em segundo plano controlada por evento](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>Versões do SDK do WebJobs

Essas são as principais diferenças entre a versão 3. *x* e a versão 2. *x* do SDK do WebJobs:

* Versão 3. *x* adiciona suporte para o .NET Core.
* Na versão 3. *x*, você precisa instalar explicitamente a extensão de associação de armazenamento exigida pelo SDK do WebJobs. Na versão 2. *x*, as associações de armazenamento foram incluídas no SDK.
* Ferramentas para o .NET Core do Visual Studio (3. *x*) projetos é diferente de ferramentas para o .NET Framework (2. *x*) projetos. Para obter mais informações, consulte [desenvolver e implantar o WebJobs usando o Visual Studio - serviço de aplicativo do Azure](webjobs-dotnet-deploy-vs.md).

Quando possível, os exemplos são fornecidos para a versão 3. *x* e a versão 2. *x*.

> [!NOTE]
> [O Azure Functions](../azure-functions/functions-overview.md) baseia-se no SDK do WebJobs, e este artigo fornece links para documentação do Azure Functions para alguns tópicos. Observe que essas diferenças entre funções e o SDK do WebJobs:
> * Azure Functions versão 2. *x* corresponde à versão 3 do SDK de WebJobs. *x*e o Azure Functions 1. *x* corresponde ao SDK de trabalhos Web 2. *x*. Repositórios de código-fonte usam o SDK de WebJobs numeração.
> * Código de exemplo para o Azure Functions C# bibliotecas de classes é como código do SDK de WebJobs, exceto que não é necessário um `FunctionName` atributo em um projeto do SDK de WebJobs.
> * Alguns tipos de associação têm suporte apenas em funções, como HTTP (Webhooks) e a grade de eventos (que é baseado em HTTP).
>
> Para obter mais informações, consulte [Comparar o WebJobs SDK e o Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>Host do WebJobs

O host é um contêiner de tempo de execução para funções.  Ele escuta gatilhos e chamadas de funções. Na versão 3. *x*, o host é uma implementação de `IHost`. Na versão 2. *x*, você usa o `JobHost` objeto. Você cria uma instância do host em seu código e escreve um código para personalizar seu comportamento.

Isso é uma diferença importante entre usando o SDK de WebJobs diretamente e usá-lo indiretamente por meio de funções do Azure. No Azure Functions, o serviço controla o host e você não pode personalizar o host ao escrever código. O Azure Functions lhe permite personalizar o comportamento de host por meio das configurações no arquivo host. JSON. Essas configurações são cadeias de caracteres, não código, e isso limita os tipos de personalizações que você pode fazer.

### <a name="host-connection-strings"></a>Cadeias de conexão do host

O SDK de WebJobs procura cadeias de conexão de armazenamento do Azure e do barramento de serviço do Azure no arquivo Settings quando você executa localmente ou no ambiente de trabalho Web quando você executa no Azure. Por padrão, uma conexão de armazenamento cadeia configuração denominada `AzureWebJobsStorage` é necessária.  

Versão 2. *x* do SDK permite que você use seus próprios nomes para essas cadeias de caracteres de conexão ou armazená-los em outro lugar. Você pode definir nomes no código usando o [ `JobHostConfiguration` ], conforme mostrado aqui:

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

Porque a versão 3. *x* usa a configuração padrão do .NET Core APIs, há uma API para alterar nomes de cadeia de caracteres de conexão.

### <a name="host-development-settings"></a>Configurações de desenvolvimento do host

Você pode executar o host no modo de desenvolvimento para tornar o desenvolvimento local mais eficiente. Aqui estão algumas das configurações que são alteradas quando você executa no modo de desenvolvimento:

| Propriedade | Configuração de desenvolvimento |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` para maximizar a saída de log. |
| `Queues.MaxPollingInterval`  | Um valor baixo para garantir que os métodos de fila sejam disparados imediatamente.  |
| `Singleton.ListenerLockPeriod` | Quinze segundos para ajudar no desenvolvimento iterativo rápido. |

O processo para habilitar o modo de desenvolvimento depende da versão do SDK. 

#### <a name="version-3x"></a>Versão 3. *x*

Versão 3. *x* usa as APIs padrão do ASP.NET Core. Chame o [ `UseEnvironment` ](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) método sobre o [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder) instância. Passar uma cadeia de caracteres denominada `development`, como neste exemplo:

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

A classe `JobHostConfiguration` tem um método `UseDevelopmentSettings` que habilita o modo de desenvolvimento.  O exemplo a seguir mostra como usar as configurações de desempenho. Para tornar `config.IsDevelopment` retornar `true` quando ele é executado localmente, defina uma variável de ambiente local chamada `AzureWebJobsEnv` com o valor `Development`.

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

Na versão 3. *x*, o limite de conexão padrão é infinitas conexões. Se por algum motivo você precisar alterar esse limite, você pode usar o [ `MaxConnectionsPerServer` ](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) propriedade o [ `WinHttpHandler` ](/dotnet/api/system.net.http.winhttphandler) classe.

Na versão 2. *x*, você controlar o número de conexões simultâneas com um host usando o [Defaultconnectionlimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) API. 2. *x*, você deve aumentar esse valor do padrão de 2 antes de iniciar o host de trabalhos Web.

Saída de todas as solicitações HTTP feitas por meio de uma função `HttpClient` fluir por meio de `ServicePointManager`. Depois de atingir o valor definido em `DefaultConnectionLimit`, `ServicePointManager` começa da fila as solicitações antes de enviá-los. Suponha que seu `DefaultConnectionLimit` seja definido como 2 e seu código faça 1.000 solicitações HTTP. Inicialmente, apenas duas solicitações são permitidas por meio do sistema operacional. As outras 998 são colocadas na fila até que haja espaço para elas. Isso significa que seu `HttpClient` pode atingir o tempo limite, porque parece que fez a solicitação, mas a solicitação nunca foi enviada pelo sistema operacional para o servidor de destino. Para que você possa ver o comportamento que não faz sentido: o `HttpClient` local está demorando 10 segundos para concluir uma solicitação, mas o serviço está retornando cada solicitação em 200 ms. 

O valor padrão para aplicativos ASP.NET é `Int32.MaxValue`, e isso é muito provável que funcione bem para trabalhos Web em execução em um básico ou superior plano serviço de aplicativo. Trabalhos Web normalmente a configuração Always On é necessário e que só tem suporte básico e posteriores planos do App Service.

Caso o WebJob esteja em execução em um Plano do Serviço de Aplicativo Gratuito ou Compartilhado, seu aplicativo ficará restrito à área restrita do Serviço de Aplicativo, que atualmente tem um [limite de conexão de 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Com um limite de conexão não associado no `ServicePointManager`, é mais provável que seja atingido o limite de conexão de área restrita e o site será desligado. Nesse caso, a configuração de `DefaultConnectionLimit` para algo inferior, como 50 ou 100, pode evitar que isso aconteça e ainda permitir uma taxa de transferência suficiente.

A configuração deve ser definida antes de todas as solicitações HTTP serem feitas. Por esse motivo, o host de trabalhos Web não deve ajustar a configuração automaticamente. Pode haver solicitações HTTP que ocorrem antes do início do host, que pode resultar em comportamento inesperado. A melhor abordagem é definir o valor imediatamente no seu `Main` método antes de inicializar `JobHost`, conforme mostrado aqui:

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

Funções devem ser métodos públicos e deve ter um atributo de gatilho ou o [ `NoAutomaticTrigger` ](#manual-triggers) atributo.

### <a name="automatic-triggers"></a>Disparadores automáticos

Os disparadores automáticos chamam uma função em resposta a um evento. Considere este exemplo de uma função que é disparado por uma mensagem adicionada ao armazenamento de filas do Azure. Ele responde ao ler um blob de armazenamento de BLOBs do Azure:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

O `QueueTrigger` atributo instrui o tempo de execução para chamar a função sempre que uma mensagem da fila aparece no `myqueue-items` fila. O `Blob` atributo instrui o tempo de execução para usar a mensagem da fila para ler um blob na *itens de trabalho de exemplo* contêiner. O conteúdo da mensagem da fila, passado para a função no `myQueueItem` parâmetro, é o nome do blob.


### <a name="manual-triggers"></a>Gatilhos manuais

Para disparar uma função manualmente, use o `NoAutomaticTrigger` de atributo, conforme mostrado aqui:

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

O processo para disparar manualmente a função depende da versão do SDK.

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

As associações de entrada e saída fornecem uma maneira declarativa para criar dados a partir do Azure ou de serviços externos disponíveis para seu código. As associações de saída fornecem uma maneira de atualizar os dados. O [começar](webjobs-sdk-get-started.md) artigo mostra um exemplo de cada um.

Você pode usar um valor de retorno do método para uma associação de saída, aplicando o atributo para o valor de retorno do método. Consulte o exemplo na [usando a função do Azure retornam o valor](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Tipos de associação

O processo para instalar e gerenciar tipos de associação depende se você estiver usando a versão 3. *x* ou a versão 2. *x* do SDK. Você pode encontrar o pacote de instalação para um tipo de ligação específica, na seção de "Pacotes" das funções do Azure do tipo de associação [artigo de referência](#binding-reference-information). Uma exceção é o gatilho de arquivos e ligação (para o sistema de arquivos local), que não é compatível com o Azure Functions.

#### <a name="version-3x"></a>Versão 3. *x*

Na versão 3. *x*, as associações de armazenamento são incluídas no `Microsoft.Azure.WebJobs.Extensions.Storage` pacote. Chame o `AddAzureStorage` método de extensão no `ConfigureWebJobs` método, conforme mostrado aqui:

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

Para usar outros tipos de gatilho e associação, instale o pacote do NuGet que os contém e chame o método de extensão `Add<binding>` implementado na extensão. Por exemplo, se você quiser usar uma associação do Azure Cosmos DB, instale `Microsoft.Azure.WebJobs.Extensions.CosmosDB` e chamar `AddCosmosDB`, semelhante a esta:

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

Esses tipos de gatilho e associação são incluídos na versão 2. *x* da `Microsoft.Azure.WebJobs` pacote:

* Armazenamento de blob
* Armazenamento de filas
* Armazenamento de tabela

Para usar outros tipos de associação e gatilho, instale o pacote do NuGet que os contém e chame um método `Use<binding>` no objeto `JobHostConfiguration`. Por exemplo, se você quiser usar um gatilho de temporizador, instale `Microsoft.Azure.WebJobs.Extensions` e chame `UseTimers` no `Main` método, conforme mostrado aqui:

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

O processo de associação para o [ `ExecutionContext` ] depende da versão do SDK.

#### <a name="version-3x"></a>Versão 3. *x*

Chame o `AddExecutionContextBinding` método de extensão no `ConfigureWebJobs` método, conforme mostrado aqui:

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

O pacote `Microsoft.Azure.WebJobs.Extensions` mencionado anteriormente também fornece um tipo especial de associação que você pode registrar chamando o método `UseCore`. Essa associação permite que você defina uma [ `ExecutionContext` ] parâmetro na assinatura da função, que é habilitada assim:

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

* **Versão 3. *x*:** Definir a configuração quando o `Add<Binding>` método é chamado no `ConfigureWebJobs`.
* **Versão 2. *x*:** Definir a configuração definindo propriedades em um objeto de configuração que você passa para `JobHost`.

Essas configurações de associação específicas são equivalentes às configurações na [arquivo de projeto de host. JSON](../azure-functions/functions-host-json.md) no Azure Functions.

Você pode configurar as seguintes associações:

* [Gatilho do Azure cosmos DB](#azure-cosmosdb-trigger-configuration-version-3x)
* [Gatilho de Hubs de eventos](#event-hubs-trigger-configuration-version-3x)
* Gatilho de armazenamento de filas
* [Associação SendGrid](#sendgrid-binding-configuration-version-3x)
* [Gatilho do barramento de serviço](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Configuração de gatilho do Azure cosmos DB (versão 3. *x*)

Este exemplo mostra como configurar o gatilho do Azure Cosmos DB:

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

Para obter mais detalhes, consulte o [associação do Azure CosmosDB](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings) artigo.

### <a name="event-hubs-trigger-configuration-version-3x"></a>Configuração de gatilho de Hubs de eventos (versão 3. *x*)

Este exemplo mostra como configurar o gatilho de Hubs de eventos:

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

Para obter mais detalhes, consulte o [associação de Hubs de eventos](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) artigo.

### <a name="queue-storage-trigger-configuration"></a>Configuração do gatilho de armazenamento de fila

Estes exemplos mostram como configurar o gatilho do armazenamento de fila:

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

Para obter mais detalhes, consulte o [associação de armazenamento de fila](../azure-functions/functions-bindings-storage-queue.md#hostjson-settings) artigo.

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

Para obter mais detalhes, consulte o [referência do host. JSON v1.x](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>Configuração de associação do SendGrid (versão 3. *x*)

Este exemplo mostra como configurar o SendGrid associação de saída:

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

Para obter mais detalhes, consulte o [associação SendGrid](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) artigo.

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

Para obter mais detalhes, consulte o [associação de barramento de serviço](../azure-functions/functions-bindings-service-bus.md#hostjson-settings) artigo.

### <a name="configuration-for-other-bindings"></a>Configuração de outras associações

Alguns tipos de gatilho e associação de definem seus próprios tipos de configuração personalizada. Por exemplo, o gatilho de arquivo permite que você especifique o caminho raiz para monitorar, como nestes exemplos:

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

Às vezes você deseja especificar um nome de fila, um nome de blob ou contêiner ou um nome de tabela em vez de embuti-la. Por exemplo, você talvez queira especificar o nome da fila para o atributo `QueueTrigger` em um arquivo de configuração ou uma variável de ambiente.

Você pode fazer isso passando um `NameResolver` objeto para o `JobHostConfiguration` objeto. Você inclui espaços reservados no gatilho ou parâmetros do construtor de atributo de associação, e seu código `NameResolver` fornece os valores reais a serem usados no lugar desses espaços reservados. Identificar os espaços reservados ao colocá-los com porcentagem (%)) sinais, conforme mostrado aqui:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Este código lhe permite usar uma fila denominada `logqueuetest` no ambiente de teste e uma denominada `logqueueprod` na produção. Em vez de um nome de fila embutido em código, você especifica o nome de uma entrada na coleção `appSettings`.

Não há um padrão `NameResolver` que entra em vigor se você não fornecer uma personalizada. O padrão obtém valores de configurações de aplicativo ou variáveis de ambiente.

Sua `NameResolver` classe obtém o nome da fila de `appSettings`, conforme mostrado aqui:

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

Você pode configurar o resolvedor usando a injeção de dependência. Esses exemplos exigem a seguinte instrução `using`:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Adicionar o resolvedor chamando o [ `ConfigureServices` ] método de extensão em [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder), como neste exemplo:

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

Passar seu `NameResolver` classe para o `JobHost` do objeto, como mostrado aqui:

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

Se você precisar fazer algum trabalho em sua função antes de usar um atributo de associação, como `Queue`, `Blob`, ou `Table`, você pode usar o `IBinder` interface.

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

A documentação do Azure Functions fornece informações de referência sobre cada tipo de associação. Você encontrará as informações a seguir em cada artigo de referência de associação. (Este exemplo se baseia na fila de armazenamento).

* [Pacotes](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x). O pacote que você precisa instalar para incluir suporte para a associação em um projeto do SDK de WebJobs.
* [Exemplos](../azure-functions/functions-bindings-storage-queue.md#trigger---example). Exemplos de código. O C# exemplo de biblioteca de classe se aplica ao SDK do WebJobs. Basta omitir o `FunctionName` atributo.
* [Atributos](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes). Os atributos a ser usado para o tipo de associação.
* [Configuração](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration). Explicações sobre as propriedades de atributo e os parâmetros do construtor.
* [Uso](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Os tipos que você pode associar a e informações sobre como funciona a associação. Por exemplo: algoritmo de sondagem, processamento de fila de mensagens suspeita.
  
Para obter uma lista de artigos de referência de associação, consulte "Ligações com suporte" no [gatilhos e associações](../azure-functions/functions-triggers-bindings.md#supported-bindings) artigo para o Azure Functions. Nessa lista, as associações de HTTP, Webhooks e a grade de eventos são compatíveis apenas com funções do Azure, não pelo SDK do WebJobs.

## <a name="disable-attribute"></a>Atributo Desabilitar 

O [ `Disable` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) atributo lhe permite controlar se uma função pode ser disparado. 

No exemplo a seguir, se a configuração de aplicativo `Disable_TestJob` tem um valor de `1` ou `True` (diferencia maiusculas de minúsculas), a função não será executado. Nesse caso, o tempo de execução cria uma mensagem de log *A função 'Functions.TestJob' está desabilitada*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Quando você altera os valores de configuração de aplicativo no portal do Azure, o trabalho Web é reiniciado para acompanhar a nova configuração.

O atributo pode ser declarado no nível de classe, método ou parâmetro. O nome da configuração pode também conter expressões de associação.

## <a name="timeout-attribute"></a>Atributo de tempo limite

O [ `Timeout` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) atributo faz com que uma função seja cancelada se ela não finalizar dentro de um período de tempo especificado. No exemplo a seguir, a função seria executado por um dia sem o atributo de tempo limite. Tempo limite faz com que a função a ser cancelado após 15 segundos.

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

Você pode aplicar o atributo de tempo limite no nível de classe ou método, e você pode especificar um tempo limite global usando `JobHostConfiguration.FunctionTimeout`. Tempos limite de nível de classe ou método substituir tempos limites globais.

## <a name="singleton-attribute"></a>Atributo Singleton

O [ `Singleton` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) atributo garante que apenas uma instância de uma função é executado, mesmo quando houver várias instâncias do aplicativo web host. Ele faz isso por meio [distribuídos bloqueio](#viewing-lease-blobs).

Neste exemplo, apenas uma única instância de `ProcessImage` função é executada em um determinado momento:

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

Você pode usar essas configurações para garantir que sua função seja executada como um singleton em uma única instância. Para garantir que apenas uma única instância da função está em execução quando o aplicativo web pode ser dimensionado para várias instâncias, aplicar um bloqueio de singleton de nível de ouvinte na função (`[Singleton(Mode = SingletonMode.Listener)]`). Bloqueios de ouvinte são adquiridos quando o JobHost é iniciado. Se três instâncias expandidas forem iniciadas ao mesmo tempo, somente uma das instâncias adquirirá o bloqueio e somente um ouvinte será iniciado.

### <a name="scope-values"></a>Valores de escopo

Você pode especificar uma */valor da expressão de escopo* em um singleton. O valor da expressão/garante que todas as execuções da função em um escopo específico serão serializadas. Implementar um bloqueio mais granular dessa forma pode permitir algum nível de paralelismo para sua função durante a serialização outras chamadas, conforme determinado por seus requisitos. Por exemplo, a expressão de escopo no código a seguir associa ao `Region` valor da mensagem de entrada. Quando a fila contém três mensagens nas regiões Leste, Leste e Oeste, respectivamente, as mensagens que têm a região Leste são executados em série enquanto a mensagem com a região que Oeste é executado em paralelo com aqueles no Leste.

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

O escopo padrão para um bloqueio é `SingletonScope.Function`, que significa que o escopo do bloqueio (o caminho de concessão de blob) está ligado ao nome da função totalmente qualificado. Para bloquear em funções, especificar `SingletonScope.Host` e usar um nome de identificação de escopo que é o mesmo em todas as funções que você não quiser executar simultaneamente. No exemplo a seguir, apenas uma instância de `AddItem` ou `RemoveItem` é executada por vez:

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

O WebJobs SDK usa [concessões de blob do Azure](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) nos bastidores para implementar o bloqueio distribuído. Os blobs de concessão usados pelo Singleton podem ser encontrados na `azure-webjobs-host` recipiente no `AzureWebJobsStorage` conta de armazenamento sob o caminho "bloqueios". Por exemplo, o caminho de blob de concessão para o primeiro exemplo `ProcessImage` mostrado anteriormente pode ser `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Todos os caminhos incluem a ID de JobHost, 061851c758f04938a4426aa9ab3869c0 neste caso.

## <a name="async-functions"></a>Funções assíncronas

Para obter informações sobre como funções do código assíncrono, consulte o [documentação do Azure Functions](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Tokens de cancelamento

Para obter informações sobre como lidar com tokens de cancelamento, consulte a documentação do Azure Functions nos [tokens de cancelamento e desligamento normal](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Várias instâncias

Se o seu aplicativo Web for executado em várias instâncias, um WebJob contínuo será executado em cada instância, ouvindo os gatilhos e chamando funções. As várias associações de gatilho são projetadas para compartilhar com eficiência o trabalho de forma colaborativa entre instâncias, para que a expansão para mais instâncias permita que você manipule mais carga.

Os gatilhos de blob e fila automaticamente impedir que uma função de processamento de uma mensagem de fila ou blob mais de uma vez. funções não precisam ser idempotentes.

O gatilho de timer garante que apenas uma instância do timer seja executada, portanto você não terá mais de uma instância de função em execução em um determinado horário agendado.

Se você quiser garantir que apenas uma instância de uma função é executada, mesmo quando houver várias instâncias do aplicativo web host, você pode usar o [ `Singleton` ](#singleton-attribute) atributo.

## <a name="filters"></a>Filtros

Filtros de função (visualização) fornecem uma maneira de personalizar o pipeline de execução de WebJobs com sua própria lógica. Os filtros são semelhantes às [ASP.NET Core filtros](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Você pode implementá-los como atributos declarativos que são aplicados para suas funções ou classes. Para obter mais informações, consulte [Filtros de Função](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Log e monitoramento

Recomendamos que a estrutura de log que foi desenvolvida para o ASP.NET. O [começar](webjobs-sdk-get-started.md) artigo mostra como usá-lo. 

### <a name="log-filtering"></a>Filtragem de linha

Cada log criado por uma instância de `ILogger` possui um `Category` e `Level` associados. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) é uma enumeração, e o código inteiro indica a importância relativa:

|LogLevel    |Código|
|------------|---|
|Rastreamento       | 0 |
|Depurar       | 1 |
|Informações | 2 |
|Aviso     | 3 |
|Erro       | 4 |
|Crítico    | 5 |
|Nenhum        | 6 |

Você pode filtrar independentemente cada categoria para um determinado [ `LogLevel` ](/dotnet/api/microsoft.extensions.logging.loglevel). Por exemplo, você talvez queira ver todos os logs para o processamento de gatilho de blob, mas apenas `Error` e superiores para todo o resto.

#### <a name="version-3x"></a>Versão 3. *x*

Versão 3. *x* do SDK depende a filtragem criados no .NET Core. A classe `LogCategories` permite que você defina categorias para funções, gatilhos e usuários específicos. Ele também define os filtros para estados de host específico, como `Startup` e `Results`. Isso permite que você ajustar a saída de log. Se nenhuma correspondência for encontrada nas categorias definidas, o filtro reverterá para o valor `Default` ao decidir se deseja filtrar a mensagem.

`LogCategories` requer a seguinte declaração de uso:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

O exemplo a seguir constrói um filtro que, por padrão, filtra todos os logs no `Warning` nível. O `Function` e `results` categorias (equivalente a `Host.Results` na versão 2. *x*) são filtrados no `Error` nível. O filtro compara a categoria atual com todos os níveis registrados na instância `LogCategories` e escolhe a maior correspondência. Isso significa que o `Debug` nível registrado para `Host.Triggers` corresponde a `Host.Triggers.Queue` ou `Host.Triggers.Blob`. Isso permite que você controle categorias mais amplas sem a necessidade de adicionar cada uma delas.

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

Na versão 2. *x* do SDK, você deve usar o `LogCategoryFilter` classe para controlar a filtragem. O `LogCategoryFilter` tem uma `Default` propriedade com um valor inicial de `Information`, que significa que todas as mensagens no `Information`, `Warning`, `Error`, ou `Critical` níveis são registrados, mas quaisquer mensagens no `Debug` ou `Trace` níveis são filtrados de distância.

Assim como acontece com `LogCategories` na versão 3. *x*, o `CategoryLevels` propriedade permite que você especificar níveis de log para categorias específicas, para que você pode ajustar a saída de log. Se nenhuma correspondência for encontrada no dicionário `CategoryLevels`, o filtro reverterá para o valor `Default` ao decidir se deseja filtrar a mensagem.

O exemplo a seguir constrói um filtro que, por padrão, filtra todos os logs no nível de `Warning`. O `Function` e `Host.Results` categorias são filtradas no `Error` nível. O `LogCategoryFilter` compara a categoria atual com todos os `CategoryLevels` registrados e escolhe a maior correspondência. Portanto, o `Debug` nível registrado para `Host.Triggers` corresponderá `Host.Triggers.Queue` ou `Host.Triggers.Blob`. Isso permite que você controle categorias mais amplas sem a necessidade de adicionar cada uma delas.

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

O processo para implementar a telemetria personalizada para [Application Insights](../azure-monitor/app/app-insights-overview.md) depende da versão do SDK. Para saber como configurar o Application Insights, confira [Adicionar registro em log do Application Insights](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Versão 3. *x*

Porque a versão 3. *x* do SDK do WebJobs depende do host genérico, uma fábrica de telemetria personalizada não é mais fornecido do .NET Core. Mas você pode adicionar telemetria personalizada para o pipeline usando a injeção de dependência. Os exemplos nesta seção exigem as seguintes declarações `using`:

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

Quando o [`TelemetryConfiguration`] é construído, todos os tipos registrados de [`ITelemetryInitializer`] são incluídos. Para obter mais informações, consulte [API do Application Insights para métricas e eventos personalizados](../azure-monitor/app/api-custom-events-metrics.md).

Na versão 3. *x*, você não precisa que liberar o [ `TelemetryClient` ] quando o host for interrompido. O sistema de injeção de dependência do .NET Core automaticamente descarta o `ApplicationInsightsLoggerProvider` registrado que libera o [`TelemetryClient`].

#### <a name="version-2x"></a>Versão 2. *x*

Na versão 2. *x*, o [ `TelemetryClient` ] criado internamente pelo provedor Application Insights para o SDK de WebJobs usa [ `ServerTelemetryChannel` ](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Quando o ponto de extremidade do Application Insights está indisponível ou limitando solicitações de entrada, este canal [salva as solicitações no sistema de arquivos do aplicativo Web e reenvia-as depois](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

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

O `SamplingPercentageEstimatorSettings` configura o objeto [amostragem adaptável](https://docs.microsoft.com/azure/application-insights/app-insights-sampling). Isso significa que, em determinados cenários de alto volume, Applications Insights envia um subconjunto selecionado de dados de telemetria para o servidor.

Depois de criar a fábrica de telemetria, passá-la para o provedor de log do Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> Próximas etapas

Este artigo forneceu trechos de código que mostram como lidar com cenários comuns para trabalhar com o SDK de WebJobs. Para obter exemplos completos, consulte [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk-samples).

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[`JobHostConfiguration`]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
