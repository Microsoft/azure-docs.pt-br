---
title: Aplicativos do Application Insights para Worker Service (aplicativos não HTTP)
description: Monitoramento de aplicativos .NET Core/.NET Framework não HTTP com o Application Insights do Azure Monitor.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/11/2020
ms.openlocfilehash: c1ca594626d4384c9dfb62990ee2017d2094fca4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371848"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Aplicativos do Application Insights para Worker Service (aplicativos não HTTP)

O [SDK do Application Insights para Worker Service](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) é um novo SDK que é mais adequado para cargas de trabalho não HTTP, como mensagens, tarefas em segundo plano, aplicativos de console etc. Esses tipos de aplicativos não têm a noção de uma solicitação HTTP de entrada como um Aplicativo Web ASP.NET/ASP.NET Core tradicional e, portanto, não dão suporte para o uso de pacotes do Application Insights para aplicativos [ASP.NET](asp-net.md) ou [ASP.NET Core](asp-net-core.md).

O novo SDK não faz nenhuma coleta de telemetria por si só. Em vez disso, ele traz outros coletores automáticos do Application Insights conhecidos, como o [DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/), o [PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/), o [ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) etc. Esse SDK expõe os métodos de extensão no `IServiceCollection` para habilitar e configurar a coleta de telemetria.

## <a name="supported-scenarios"></a>Cenários com suporte

O [SDK do Application Insights para Worker Service](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) é mais adequado para aplicativos não HTTP, independentemente de onde ou como eles são executados. Se o seu aplicativo estiver em execução e tiver conectividade de rede com o Azure, será possível coletar telemetria. O monitoramento do Application Insights tem suporte em todos os lugares do .NET Core. Este pacote pode ser usado no [.NET Core 3.0 Worker Service](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances) recém-lançado, nas [tarefas em segundo plano no ASP.NET Core 2.1/2.2](/aspnet/core/fundamentals/host/hosted-services), nos aplicativos de console (.NET Core/.NET Framework) etc.

## <a name="prerequisites"></a>Pré-requisitos

Uma chave de instrumentação válida do Application Insights. Essa chave é necessária para enviar qualquer telemetria para o Application Insights. Se você precisar criar um recurso do Application Insights para obter uma chave de instrumentação, confira [Criar um recurso do Application Insights](./create-new-resource.md).

## <a name="using-application-insights-sdk-for-worker-services"></a>Usando o SDK do Application Insights para Worker Services

1. Instale o pacote [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) no aplicativo.
   O snippet a seguir mostra as alterações que precisam ser adicionadas ao arquivo `.csproj` do seu projeto.

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.13.1" />
    </ItemGroup>
```

1. Chame o método de extensão `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)` no `IServiceCollection`, fornecendo a chave de instrumentação. Esse método deve ser chamado no começo do aplicativo. A localização exata depende do tipo de aplicativo.

1. Recupere uma instância de `ILogger` ou de `TelemetryClient` do contêiner de DI (Injeção de Dependência) chamando `serviceProvider.GetRequiredService<TelemetryClient>();` ou usando uma Injeção de Construtor. Esta etapa dispara a configuração do `TelemetryConfiguration` e de módulos de coleta automática.

Instruções específicas para cada tipo de aplicativo são descritas nas seções a seguir.

## <a name="net-core-30-worker-service-application"></a>Aplicativo .NET Core 3.0 Worker Service

O exemplo completo está compartilhado [aqui](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights)

1. Baixe e instale o [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
2. Crie um projeto do Worker Service usando o novo modelo de projeto do Visual Studio ou a linha de comando `dotnet new worker`
3. Instale o pacote [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) no aplicativo.

4. Adicione `services.AddApplicationInsightsTelemetryWorkerService();` ao método `CreateHostBuilder()` na sua classe `Program.cs`, como neste exemplo:

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. Modifique o seu `Worker.cs` de acordo com o exemplo abaixo.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;
        private static HttpClient _httpClient = new HttpClient();

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
                {
                    _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                    _logger.LogInformation("Calling bing.com");
                    var res = await _httpClient.GetAsync("https://bing.com");
                    _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                    _telemetryClient.TrackEvent("Bing call event completed");
                }

                await Task.Delay(1000, stoppingToken);
            }
        }
    }
```

6. Configure a chave de instrumentação.

    Embora seja possível fornecer a chave de instrumentação como um argumento para `AddApplicationInsightsTelemetryWorkerService`, recomendamos que você especifique a chave de instrumentação na configuração. O exemplo de código a seguir mostra como especificar uma chave de instrumentação no `appsettings.json`. Verifique se o `appsettings.json` foi copiado para a pasta raiz do aplicativo durante a publicação.

```json
    {
        "ApplicationInsights":
        {
            "InstrumentationKey": "putinstrumentationkeyhere"
        },
        "Logging":
        {
            "LogLevel":
            {
                "Default": "Warning"
            }
        }
    }
```

Como alternativa, especifique a chave de instrumentação em qualquer uma das variáveis de ambiente a seguir.
`APPINSIGHTS_INSTRUMENTATIONKEY` ou `ApplicationInsights:InstrumentationKey`

Por exemplo: `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
OU `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

Normalmente, `APPINSIGHTS_INSTRUMENTATIONKEY` especifica a chave de instrumentação para aplicativos implantados em Aplicativos Web como Trabalhos da Web.

> [!NOTE]
> Uma chave de instrumentação especificada no código é preferível à variável de ambiente `APPINSIGHTS_INSTRUMENTATIONKEY` que, por sua vez, é preferível a outras opções.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>Tarefas em segundo plano do ASP.NET Core com serviços hospedados

[Este](/aspnet/core/fundamentals/host/hosted-services?tabs=visual-studio) documento descreve como criar tarefas em segundo plano no aplicativo ASP.NET Core 2.1/2.2.

O exemplo completo está compartilhado [aqui](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/BackgroundTasksWithHostedService)

1. Instale o pacote [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) no aplicativo.
2. Adicione `services.AddApplicationInsightsTelemetryWorkerService();` ao método `ConfigureServices()`, como neste exemplo:

```csharp
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .ConfigureAppConfiguration((hostContext, config) =>
            {
                config.AddJsonFile("appsettings.json", optional: true);
            })
            .ConfigureServices((hostContext, services) =>
            {
                services.AddLogging();
                services.AddHostedService<TimedHostedService>();

                // instrumentation key is read automatically from appsettings.json
                services.AddApplicationInsightsTelemetryWorkerService();
            })
            .UseConsoleLifetime()
            .Build();

        using (host)
        {
            // Start the host
            await host.StartAsync();

            // Wait for the host to shutdown
            await host.WaitForShutdownAsync();
        }
    }
```

A seguir está o código do `TimedHostedService`, no qual a lógica da tarefa em segundo plano reside.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class TimedHostedService : IHostedService, IDisposable
    {
        private readonly ILogger _logger;
        private Timer _timer;
        private TelemetryClient _telemetryClient;
        private static HttpClient httpClient = new HttpClient();

        public TimedHostedService(ILogger<TimedHostedService> logger, TelemetryClient tc)
        {
            _logger = logger;
            this._telemetryClient = tc;
        }

        public Task StartAsync(CancellationToken cancellationToken)
        {
            _logger.LogInformation("Timed Background Service is starting.");

            _timer = new Timer(DoWork, null, TimeSpan.Zero,
                TimeSpan.FromSeconds(1));

            return Task.CompletedTask;
        }

        private void DoWork(object state)
        {
            _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

            using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
            {
                _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                _logger.LogInformation("Calling bing.com");
                var res = httpClient.GetAsync("https://bing.com").GetAwaiter().GetResult();
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                _telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. Configure a chave de instrumentação.
   Use o mesmo `appsettings.json` do exemplo do .NET Core 3.0 Worker Service acima.

## <a name="net-corenet-framework-console-application"></a>Aplicativo de console do .NET Core/.NET Framework

Conforme mencionado no início deste artigo, o novo pacote pode ser usado para habilitar o Application Insights Telemetry até mesmo de um aplicativo de console regular. Esse pacote tem como destino o [`NetStandard2.0`](/dotnet/standard/net-standard) e, portanto, pode ser usado para aplicativos de console no .NET Core 2.0 ou superior e no .NET Framework 4.7.2 ou superior.

O exemplo completo está compartilhado [aqui](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/ConsoleAppWithApplicationInsights)

1. Instale o pacote [Microsoft.ApplicationInsights.WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) no aplicativo.

2. Modifique o Program.cs como no exemplo abaixo.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Logging;
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;

    namespace WorkerSDKOnConsole
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                // Create the DI container.
                IServiceCollection services = new ServiceCollection();

                // Being a regular console app, there is no appsettings.json or configuration providers enabled by default.
                // Hence instrumentation key and any changes to default logging level must be specified here.
                services.AddLogging(loggingBuilder => loggingBuilder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("Category", LogLevel.Information));
                services.AddApplicationInsightsTelemetryWorkerService("instrumentationkeyhere");

                // Build ServiceProvider.
                IServiceProvider serviceProvider = services.BuildServiceProvider();

                // Obtain logger instance from DI.
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                // Obtain TelemetryClient instance from DI, for additional manual tracking or to flush.
                var telemetryClient = serviceProvider.GetRequiredService<TelemetryClient>();

                var httpClient = new HttpClient();

                while (true) // This app runs indefinitely. replace with actual application termination logic.
                {
                    logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                    // Replace with a name which makes sense for this operation.
                    using (telemetryClient.StartOperation<RequestTelemetry>("operation"))
                    {
                        logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                        logger.LogInformation("Calling bing.com");                    
                        var res = await httpClient.GetAsync("https://bing.com");
                        logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                        telemetryClient.TrackEvent("Bing call event completed");
                    }

                    await Task.Delay(1000);
                }

                // Explicitly call Flush() followed by sleep is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                telemetryClient.Flush();
                Task.Delay(5000).Wait();
            }
        }
    }
```

Esse aplicativo de console também usa o mesmo `TelemetryConfiguration` padrão e pode ser personalizado da mesma maneira que os exemplos na seção anterior.

## <a name="run-your-application"></a>Execute seu aplicativo.

Execute seu aplicativo. Os trabalhos de exemplo de todos os itens acima fazem uma chamada HTTP a cada segundo para bing.com e emitem alguns logs usando o `ILogger`. Estas linhas estão encapsuladas dentro da chamada `StartOperation` de `TelemetryClient`, que é usada para criar uma operação (neste exemplo, `RequestTelemetry` é denominada "operation"). O Application Insights coletará esses logs do ILogger (em aviso ou acima por padrão) e as respectivas dependências, e eles serão correlacionados ao `RequestTelemetry` com a relação pai-filho. A correlação também funciona em limites de processo/rede. Por exemplo, se a chamada foi feita para outro componente monitorado, ela também será correlacionada a esse pai.

Essa operação personalizada do `RequestTelemetry` pode ser considerada como o equivalente de uma solicitação da Web de entrada em um Aplicativo Web típico. Embora não seja necessário usar uma operação, ela se adapta melhor ao [modelo de dados de correlação do Application Insights](./correlation.md), com o `RequestTelemetry` agindo como a operação pai e cada telemetria gerada dentro da iteração do trabalho sendo tratada como logicamente pertencente à mesma operação. Essa abordagem também garante que toda a telemetria gerada (automática e manual) terá o mesmo `operation_id`. Como a amostragem se baseia no `operation_id`, o algoritmo de amostragem mantém ou descarta toda a telemetria de uma iteração.

Veja a seguir a lista completa de telemetria coletada automaticamente pelo Application Insights.

### <a name="live-metrics"></a>Live Metrics    

O [Live Metrics](./live-stream.md) pode ser usado para verificar rapidamente se o monitoramento do Application Insights está configurado corretamente. Embora possa levar alguns minutos antes que a telemetria comece a aparecer no portal e na análise, o Live Metrics mostra o uso da CPU do processo em execução quase em tempo real. Elas também podem mostrar outras telemetrias, como Solicitações, Dependências, Rastreamentos etc.

### <a name="ilogger-logs"></a>Logs do ILogger

Os logs emitidos por meio do `ILogger` de severidade `Warning` ou superior são capturados automaticamente. Siga a [documentação do ILogger](ilogger.md#control-logging-level) para personalizar quais níveis de log são capturados pelo Application Insights.

### <a name="dependencies"></a>Dependências

Por padrão, a coleta de dependências está habilitada. [Este](asp-net-dependencies.md#automatically-tracked-dependencies) artigo explica as dependências coletadas automaticamente e contém etapas para fazer o acompanhamento manual.

### <a name="eventcounter"></a>EventCounter

O `EventCounterCollectionModule` é habilitado por padrão e coletará um conjunto padrão de contadores de aplicativos .NET Core 3.0. O tutorial do [EventCounter](eventcounters.md) lista o conjunto padrão de contadores coletados. Ele também tem instruções sobre como personalizar a lista.

### <a name="manually-tracking-additional-telemetry"></a>Acompanhamento manual de telemetria adicional

Embora o SDK colete automaticamente a telemetria, conforme explicado acima, na maioria dos casos o usuário precisará enviar telemetria adicional para o serviço do Application Insights. A maneira recomendada de acompanhar a telemetria adicional é obter uma instância do `TelemetryClient` na Injeção de Dependência e chamar um dos métodos da [API](api-custom-events-metrics.md) `TrackXXX()` com suporte para ela. Outro caso de uso típico é o [acompanhamento personalizado das operações](custom-operations-tracking.md). Essa abordagem é demonstrada nos exemplos do Worker acima.

## <a name="configure-the-application-insights-sdk"></a>Configurar o SDK do Application Insights

O `TelemetryConfiguration` padrão usado pelo SDK do serviço de trabalho é semelhante à configuração automática usada em um aplicativo ASP.NET ou ASP.NET Core, mas sem o TelemetryInitializers usado para enriquecer a telemetria no `HttpContext`.

Você pode personalizar o SDK do Application Insights para o Worker Service para alterar a configuração padrão. Os usuários do SDK do ASP.NET Core do Application Insights podem estar familiarizados com a alteração da configuração usando a [injeção de dependência](/aspnet/core/fundamentals/dependency-injection) interna do ASP.NET Core. O SDK do WorkerService também é baseado em princípios semelhantes. Faça quase todas as alterações de configuração na seção `ConfigureServices()` chamando os métodos apropriados em `IServiceCollection`, conforme detalhado abaixo.

> [!NOTE]
> Ao usar esse SDK, não há suporte para alterar a configuração modificando o `TelemetryConfiguration.Active` e as alterações não serão refletidas.

### <a name="using-applicationinsightsserviceoptions"></a>Como usar o ApplicationInsightsServiceOptions

Você pode modificar algumas configurações comuns passando `ApplicationInsightsServiceOptions` para `AddApplicationInsightsTelemetryWorkerService`, como neste exemplo:

```csharp
using Microsoft.ApplicationInsights.WorkerService;

public void ConfigureServices(IServiceCollection services)
{
    var aiOptions = new ApplicationInsightsServiceOptions();
    // Disables adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;

    // Disables QuickPulse (Live Metrics stream).
    aiOptions.EnableQuickPulseMetricStream = false;
    services.AddApplicationInsightsTelemetryWorkerService(aiOptions);
}
```

Observe que o `ApplicationInsightsServiceOptions` neste SDK está no namespace `Microsoft.ApplicationInsights.WorkerService` vez de `Microsoft.ApplicationInsights.AspNetCore.Extensions` no SDK do ASP.NET Core.

Configurações usadas com frequência no `ApplicationInsightsServiceOptions`

|Configuração | Descrição | Padrão
|---------------|-------|-------
|EnableQuickPulseMetricStream | Habilita/desabilita o recurso LiveMetrics | true
|EnableAdaptiveSampling | Habilita/desabilita a amostragem adaptável | true
|EnableHeartbeat | Habilita/desabilita o recurso de Pulsações, que periodicamente (padrão de 15 min) envia uma métrica personalizada chamada 'HeartBeatState' com informações sobre o runtime, como a Versão do .NET, informações do Ambiente do Azure, se ele é aplicável etc. | true
|AddAutoCollectedMetricExtractor | Habilita/desabilita o extrator AutoCollectedMetrics, que é um TelemetryProcessor que envia métricas previamente agregadas sobre as Solicitações/Dependências antes da realização da amostragem. | true
|EnableDiagnosticsTelemetryModule | Habilita/desabilita o `DiagnosticsTelemetryModule`. Desabilitar isso fará com que as seguintes configurações sejam ignoradas: `EnableHeartbeat`, `EnableAzureInstanceMetadataTelemetryModule`, `EnableAppServicesHeartbeatTelemetryModule` | true

Confira as [definições configuráveis no `ApplicationInsightsServiceOptions`](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) para obter a lista mais atualizada.

### <a name="sampling"></a>amostragem

O SDK do Application Insights para o Worker Service dá suporte à amostragem adaptável e de taxa fixa. A amostragem adaptável é habilitada por padrão. A amostragem pode ser desabilitada usando a opção `EnableAdaptiveSampling` no [ApplicationInsightsServiceOptions](#using-applicationinsightsserviceoptions)

Para definir as configurações de amostragem adicionais, o exemplo a seguir pode ser usado.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WorkerService;

public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new ApplicationInsightsServiceOptions();
    
    // Disable adaptive sampling.
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetryWorkerService(aiOptions);

    // Add Adaptive Sampling with custom settings.
    // the following adds adaptive sampling with 15 items per sec.
    services.Configure<TelemetryConfiguration>((telemetryConfig) =>
        {
            var builder = telemetryConfig.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
            builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond: 15);
            builder.Build();
        });
    //...
}
```

É possível encontrar mais informações no documento [Amostragem](#sampling).

### <a name="adding-telemetryinitializers"></a>Como adicionar TelemetryInitializers

Use os [inicializadores de telemetria](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) quando desejar definir propriedades que são enviadas com toda a telemetria.

Adicione qualquer `TelemetryInitializer` novo ao contêiner `DependencyInjection` e o SDK o adicionará automaticamente ao `TelemetryConfiguration`.

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>Como remover TelemetryInitializers

Os inicializadores de telemetria estão presentes por padrão. Para remover todos os inicializadores específicos ou de telemetria, use o código de exemplo a seguir *depois* de chamar `AddApplicationInsightsTelemetryWorkerService()`.

```csharp
   public void ConfigureServices(IServiceCollection services)
   {
        services.AddApplicationInsightsTelemetryWorkerService();
        // Remove a specific built-in telemetry initializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                            (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
   }
```

### <a name="adding-telemetry-processors"></a>Como adicionar processadores de telemetria

Você pode adicionar processadores de telemetria personalizados ao `TelemetryConfiguration` usando o método de extensão `AddApplicationInsightsTelemetryProcessor` no `IServiceCollection`. Use processadores de telemetria em [cenários de filtragem avançada](./api-filtering-sampling.md#itelemetryprocessor-and-itelemetryinitializer) para permitir um controle mais direto sobre o que está incluído ou excluído da telemetria que você envia para o serviço do Application Insights. Use o exemplo a seguir.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Como configurar ou remover o TelemetryModules padrão

O Application Insights usa módulos de telemetria para coletar automaticamente a telemetria sobre cargas de trabalho específicas sem a necessidade de acompanhamento manual.

Os módulos de coleta automática a seguir estão habilitados por padrão. Esses módulos são responsáveis por coletar automaticamente a telemetria. Você pode desabilitá-los ou configurá-los para alterar o comportamento padrão deles.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule` – (No momento, há um problema envolvendo esse módulo de telemetria. Para obter uma solução alternativa temporária, confira o [Problema 1689 do GitHub](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1689
).)
* `AzureInstanceMetadataTelemetryModule`

Para configurar qualquer `TelemetryModule` padrão, use o método de extensão `ConfigureTelemetryModule<T>` em `IServiceCollection`, conforme mostrado no exemplo a seguir.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();

            // The following configures QuickPulseTelemetryModule.
            // Similarly, any other default modules can be configured.
            services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) =>
            {
                module.AuthenticationApiKey = "keyhere";
            });

            // The following removes PerformanceCollectorModule to disable perf-counter collection.
            // Similarly, any other default modules can be removed.
            var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>
                                        (t => t.ImplementationType == typeof(PerformanceCollectorModule));
            if (performanceCounterService != null)
            {
                services.Remove(performanceCounterService);
            }
    }
```

### <a name="configuring-telemetry-channel"></a>Como configurar o canal de telemetria

O canal padrão é `ServerTelemetryChannel`. Você pode substituí-lo como mostra o exemplo a seguir.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="disable-telemetry-dynamically"></a>Desabilitar a telemetria dinamicamente

Se você quiser desabilitar a telemetria de modo condicional e dinâmico, poderá resolver a instância de `TelemetryConfiguration` com o contêiner de injeção de dependência do ASP.NET Core em qualquer lugar no seu código e definir o sinalizador `DisableTelemetry` nele.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>Como posso acompanhar a telemetria que não é coletada automaticamente?

Obtenha uma instância de `TelemetryClient` usando a injeção de construtor e chame o método `TrackXXX()` necessário nela. Não recomendamos a criação de instâncias de `TelemetryClient`. Uma instância singleton do `TelemetryClient` já está registrada no contêiner de `DependencyInjection`, que compartilha o `TelemetryConfiguration` com o restante da telemetria. A criação de uma instância de `TelemetryClient` é recomendada apenas se ela precisar de uma configuração separada do restante da telemetria.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Posso usar o IDE do Visual Studio para integrar o Application Insights a um projeto do Worker Service?

Atualmente, há suporte para a integração do IDE do Visual Studio apenas para aplicativos ASP.NET/ASP.NET Core. Este documento será atualizado quando o Visual Studio enviar suporte para integração de aplicativos do Worker Service.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>Posso habilitar o monitoramento do Application Insights usando ferramentas como o Status Monitor?

Não. Atualmente, o [Status Monitor](./monitor-performance-live-website-now.md) e o [Status Monitor v2](./status-monitor-v2-overview.md) dão suporte apenas ao ASP.NET 4.x.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Se eu executar meu aplicativo no Linux, todos os recursos serão compatíveis?

Sim. O suporte a recursos neste SDK é o mesmo em todas as plataformas, com as seguintes exceções:

* Os contadores de desempenho têm suporte apenas no Windows, com exceção da CPU/Memória do Processo mostrada no Live Metrics.
* Embora o `ServerTelemetryChannel` esteja habilitado por padrão, se o aplicativo estiver sendo executado no Linux ou no MacOS, se houver problemas de rede, o canal não criará automaticamente uma pasta de armazenamento local na qual manter a telemetria temporariamente. Devido a essa limitação, a telemetria será perdida quando houver problemas temporários de rede ou de servidor. Para contornar esse problema, configure uma pasta local para o canal:

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

## <a name="sample-applications"></a>Aplicativos de exemplo

[Aplicativo de Console do .NET Core](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/ConsoleAppWithApplicationInsights): use esse exemplo se você estiver usando um Aplicativo de Console escrito em .NET Core (2.0 ou superior) ou em .NET Framework (4.7.2 ou superior)

[Tarefas em segundo plano do ASP.NET Core com HostedServices](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/BackgroundTasksWithHostedService): use esse exemplo se você estiver no ASP.NET Core 2.1/2.2 e estiver criando tarefas em segundo plano de acordo com as diretrizes oficiais que podem ser encontradas [aqui](/aspnet/core/fundamentals/host/hosted-services)

[.NET Core 3.0 Worker Service](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/examples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights): use esse exemplo se você tiver um aplicativo .NET Core 3.0 Worker Service de acordo com as diretrizes oficiais que podem ser encontradas [aqui](/aspnet/core/fundamentals/host/hosted-services?tabs=visual-studio#worker-service-template)

## <a name="open-source-sdk"></a>SDK do código-fonte aberto

* [Ler e contribuir para o código](https://github.com/microsoft/ApplicationInsights-dotnet).

Para obter as atualizações e as correções de bugs mais recentes, [confira as notas sobre a versão](./release-notes.md).

## <a name="next-steps"></a>Próximas etapas

* [Use a API](./api-custom-events-metrics.md) para enviar os seus eventos e métricas para uma exibição detalhada do desempenho e do uso do aplicativo.
* [Rastreie dependências adicionais que não são acompanhadas automaticamente](./auto-collect-dependencies.md).
* [Enriqueça ou filtre a telemetria coletada automaticamente](./api-filtering-sampling.md).
* [Injeção de Dependência no ASP.NET Core](/aspnet/core/fundamentals/dependency-injection).
