---
title: Explorar os logs de rastreamento .NET no Azure Application Insights com ILogger
description: Exemplos de como usar o provedor do Azure Application Insights ILogger com aplicativos ASP.NET Core e o Console.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: 615eaa3df7cabad72ac321978eb01d93a7bfa988
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60901050"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider para logs de ILogger do .NET Core

ASP.NET Core dá suporte a uma API de log que funciona com tipos diferentes de provedores de log internos e de terceiros. Registro em log é feito chamando o log () ou uma variável dela em `ILogger` instâncias. Este artigo mostra como usar `ApplicationInsightsLoggerProvider` para capturar `ILogger` faz logon no console e aplicativos ASP.NET Core. Este artigo também descreve como `ApplicationInsightsLoggerProvider` é integrado com outros dados de telemetria do Application Insights.
Para saber mais entrar no Asp.Net Core, consulte [deste artigo](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>Aplicativos ASP.NET Core

Começando com [SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 de versão e versões posteriores, `ApplicationInsightsLoggerProvider` é habilitado por padrão, ao habilitar o monitoramento regular do Application Insights usando os métodos padrão - pela chamando `UseApplicationInsights` método de extensão no IWebHostBuilder ou `AddApplicationInsightsTelemetry` método de extensão no IServiceCollection. `ILogger` logs capturados pelo `ApplicationInsightsLoggerProvider` estão sujeitos a mesma configuração de quaisquer outros dados de telemetria coletados. ou seja eles têm o mesmo conjunto de `TelemetryInitializer`s, `TelemetryProcessor`s, usa o mesmo `TelemetryChannel`e serão correlacionados e da mesma forma que todos os outros dados de telemetria de amostra.  Se você estiver usando esta versão do SDK ou superior, nenhuma ação é necessária para capturar `ILogger` logs.

Por padrão, somente `ILogger` logs de `Warning` ou acima (de todas as categorias) são enviados ao Application Insights. Esse comportamento pode ser alterado, aplicando filtros, conforme mostrado [aqui](#control-logging-level). Também são necessárias etapas adicionais se `ILogger` registra em log do `Program.cs` ou `Startup.cs` devem ser capturados conforme mostrado [aqui](#capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications).

Se você usar uma versão anterior do SDK Microsoft.ApplicationInsights.AspNet, ou você deseja usar apenas ApplicationInsightsLoggerProvider, sem qualquer outra monitoramento do Application Insights, siga as etapas abaixo.

1. Instale o pacote do nuget.

```xml
    <ItemGroup>
      <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
    </ItemGroup>
```

2. modifique `Program.cs` conforme mostrado abaixo

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
            builder =>
            {
                // Providing an instrumentation key here is required if you are using
                // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                // or if you want to capture logs from early in the application startup
                // pipeline from Startup.cs or Program.cs itself.
                builder.AddApplicationInsights("ikey");

                // Optional: Apply filters to control what logs are sent to Application Insights.
                // The following configures LogLevel Information or above to be sent to
                // Application Insights for all categories.
                builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                 ("", LogLevel.Information);
            }
        );
}
```

O código acima irá configurar `ApplicationInsightsLoggerProvider`. A seguir mostra um exemplo de classe de controlador, que usa `ILogger` para enviar logs, que são capturados pelo Application Insights.

```csharp
public class ValuesController : ControllerBase
{
    private readonly `ILogger` _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications"></a>Capturar logs de ILogger do Startup.cs, Program.cs em aplicativos do Asp.Net Core

Com o novo ApplicationInsightsLoggerProvider, é possível capturar logs de no início do pipeline de inicialização do aplicativo. Até mesmo porém ApplicationInsightsLoggerProvider é automaticamente habilitado o Application Insights (de 2.7.0-beta3 em diante), não têm a configuração de chave de instrumentação até mais tarde no pipeline, portanto, somente logs do controlador / outras classes serão capturados. Capturar todos os logs a partir `Program.cs` e `Startup.cs` em si, um precisa explicitamente habilitar ApplicationInsightsLoggerProvider com uma chave de instrumentação. Também é importante observar que `TelemetryConfiguration` não estiver totalmente conjunto quando fazendo algo a partir `Program.cs` ou `Startup.cs` em si, portanto, esses logs usará uma configuração de mínimo vazia, que usa o InMemoryChannel, nenhuma amostragem e nenhuma telemetria padrão inicializadores ou processadores.

A seguir mostra exemplos de `Program.cs` e `Startup.cs` usando essa funcionalidade.

#### <a name="example-programcs"></a>Exemplo Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        // This will be picked up by AI
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // providing an instrumentation key here is required if you are using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Program.cs
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Program", LogLevel.Trace);
            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Startup.cs
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Startup", LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Exemplo Startup.cs

```csharp
public class Startup
{
    private readonly `ILogger` _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrating-from-old-applicationinsightsloggerprovider"></a>Migrando do antigo ApplicationInsightsLoggerProvider

O suporte para versões de SDK Microsoft.ApplicationInsights.AspNet 2.7.0-beta2, antes de um provedor de log agora é obsoleto. Este provedor foi habilitado com `AddApplicationInsights()` método de extensão de `ILoggerFactory`. Esse provedor agora é obsoleto e os usuários são sugeridos para migrar para o novo provedor. A migração envolve duas etapas.

1. Remova a chamada ILoggerFactory.AddApplicationInsights() de `Startup.Configure()` método para evitar o registro em log duplo.
2. Reaplicar as regras de filtragem do código, eles não serão respeitados pelo novo provedor. Sobrecargas de ILoggerFactory.AddApplicationInsights() levaram mínimo funções LogLevel ou filtro. Com o novo provedor, a filtragem é parte da estrutura de registro em log em si e não é feito pelo provedor do Application Insights. Portanto, qualquer filtro fornecido por meio `ILoggerFactory.AddApplicationInsights()` sobrecargas devem ser removidas e regras de filtragem devem ser fornecidas a seguir [esses](#control-logging-level) instruções. Se você usar `appsettings.json` para filtrar o registro em log, ele continuará a funcionar com o novo provedor como ambos usam o mesmo provedor Alias - **ApplicationInsights**.

Enquanto o provedor antigo ainda pode ser usado (ele agora está obsoleto e será removido somente na versão principal alteração 3.xx), migrando para o provedor mais recente é altamente recomendável pelos seguintes motivos.

1. Provedor anterior não tinha suporte do [escopos](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). No novo provedor, as propriedades de escopo são automaticamente adicionadas como propriedades personalizadas à telemetria coletada.
2. Os logs agora podem ser capturados muito mais cedo no pipeline de inicialização do aplicativo. ou seja Logs de inicialização e o programa de classes agora podem ser capturados.
3. Com o novo provedor, a filtragem é feita no nível da estrutura em si. Filtragem de logs para o provedor do Application Insights pode ser feito em exatamente da mesma maneira que para outros provedores, incluindo provedores internos, como o Console de depuração e assim por diante. Também é possível aplicar os mesmo filtros em vários provedores.
4. O [recomendado](https://github.com/aspnet/Announcements/issues/255) maneira no ASP.NET Core (versão 2.0 e posteriores) para habilitar provedores de log é por meio de métodos de extensão no ILoggingBuilder em `Program.cs` em si.

> [!Note]
> O novo provedor está disponível para aplicativos destinados ao `NETSTANDARD2.0` ou superior. Se seu aplicativo for destinado ao versões mais antigas do .NET Core, como .NET Core 1.1 ou destinados ao .NET Framework, continue a usar o provedor antigo.

## <a name="console-application"></a>Aplicativo de console

O código a seguir mostra um aplicativo de Console de exemplo configurado para enviar `ILogger` rastreamentos para o Application Insights.

Pacotes instalados:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

No exemplo acima, o pacote autônomo `Microsoft.Extensions.Logging.ApplicationInsights` é usado. Por padrão, essa configuração usa o mínimo necessário de `TelemetryConfiguration` para enviar dados ao Application Insights. Mínimo necessário significa que o canal usado será `InMemoryChannel`, sem nenhuma amostragem e sem TelemetryInitializers padrão. Esse comportamento pode ser substituído por um aplicativo de Console, conforme mostrado no exemplo a seguir.

Instale esse pacote adicional:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

A seção a seguir mostra como substituir o padrão `TelemetryConfiguration` usando `services.Configure<TelemetryConfiguration>()` método. Este exemplo configura `ServerTelemetryChannel`, de amostragem e adiciona um personalizado `ITelemetryInitializer` para o `TelemetryConfiguration`.

```csharp
    // Create DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly call Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if application terminates, telemetry is sent to the back-end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Nível de log de controle

O Asp.Net Core `ILogger` infraestrutura tem um mecanismo interno para aplicar [filtragem](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering) de logs, que permite aos usuários controlar os logs sejam enviados para cada provedores registrados, incluindo o provedor do Application Insights. A filtragem pode ser feita na configuração (normalmente usando `appsettings.json` arquivo) ou no código. Esse recurso é fornecido pela estrutura em si e não é específico para o provedor do Application Insights.

Exemplos de aplicar as regras de filtro a ApplicationInsightsLoggerProvider são fornecidos abaixo.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Criar regras de filtro na configuração com appSettings. JSON

Para ApplicationInsightsLoggerProvider, o alias do provedor é `ApplicationInsights`. O abaixo da seção mostrada na `appsettings.json` configura logs `Warning` e acima de todas as categorias, `Error` e acima de categorias, começando com "Microsoft" a serem enviados ao `ApplicationInsightsLoggerProvider`.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft": "Error"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="create-filter-rules-in-code"></a>Criar regras de filtro no código

O código abaixo o trecho de código configura logs `Warning` e acima de todas as categorias, `Error` e acima de categorias, começando com a Microsoft a serem enviados ao `ApplicationInsightsLoggerProvider`. Essa configuração é o mesmo que a configuração acima feita em `appsettings.json`.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>Perguntas frequentes

*1. O que é o ApplicationInsightsLoggerProvider novo e antigo?*

* [SDK de Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) acompanha um ApplicationInsightsLoggerProvider internos (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), que foi habilitado usando ILoggerFactory métodos de extensão. Esse provedor está marcado como obsoleto de 2.7.0-beta2 em diante e será completamente removida na próxima alteração de versão principal. [Isso](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) de pacote em si não obsoleto e é necessário para habilitar o monitoramento de solicitações, dependências etc.

* A alternativa sugerida é o novo pacote autônomo [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), que contém um (ApplicationInsightsLoggerProvider aprimorada Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) e métodos de extensão no ILoggerBuilder para habilitá-la.

* [SDK de Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 versão em diante, receberá uma dependência no pacote acima e habilita `ILogger` capturar automaticamente.

*2. Estou vendo algumas `ILogger` os logs são mostrados duas vezes no Application Insights?*

* Essa duplicação é possível se você tiver a versão mais antiga (agora obsoleta) do `ApplicationInsightsLoggerProvider` ativada chamando `AddApplicationInsights` em `ILoggerFactory`. Verifique se seu `Configure` tem o seguinte método e removê-lo.

   ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
        // ..other code.
    }
   ```

* Se você estiver tendo duplo registro em log durante a depuração do Visual Studio, em seguida, modifique o código usado para habilitar o Application Insights como segue, definindo `EnableDebugLogger` seja false. Esse problema de duplicação e a correção só é relevante ao depurar o aplicativo.

   ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
        options.EnableDebugLogger = false;
        services.AddApplicationInsightsTelemetry(options);
        // ..other code.
    }
   ```

*3. Atualizei para [SDK Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 de versão e agora estou vendo que registra em log do `ILogger` são capturados automaticamente. Como posso desativar esse recurso completamente?*

* Ver [isso](../../azure-monitor/app/ilogger.md#control-logging-level) seção para saber como filtrar logs em geral. A desativação ApplicationInsightsLoggerProvider usar `LogLevel.None` para ele.

  No código

    ```csharp
        builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                          ("", LogLevel.None);
    ```

  Na configuração

    ```json
    {
      "Logging": {
        "ApplicationInsights": {
          "LogLevel": {
            "Default": "None"
          }
    }
    ```

*4. Estou vendo algumas `ILogger` logs não estarem tendo mesmas propriedades que outras pessoas?*

* Capturas de Insights de aplicativo e envia `ILogger` registra em log usando a mesma `TelemetryConfiguration` usado para todos os outros dados de telemetria. Há uma exceção a essa regra. O padrão `TelemetryConfiguration` não estiver totalmente conjunto quando fazendo algo a partir do `Program.cs` ou `Startup.cs` em si, para que os logs desses locais não terão a configuração padrão e, portanto, não serão executado todas as `TelemetryInitializer`s e `TelemetryProcessor`s.

*5. Estou usando o pacote autônomo Microsoft.Extensions.Logging.ApplicationInsights, e eu quero fazer alguma telemetria personalizada adicional manualmente. Como posso fazer isso?*

* Ao usar o pacote autônomo, `TelemetryClient` não é injetado para o contêiner de injeção de dependência, portanto, os usuários devem criar uma nova instância da `TelemetryClient` usando a mesma configuração usada pelo provedor de agente de log, conforme mostrado abaixo. Isso garante que a mesma configuração é usada para toda a telemetria personalizada, bem como aqueles capturada de ILogger.

```csharp
public class MyController : ApiController
{
   // This telemtryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Observe que, se o pacote de aspnetcore package é usado para habilitar o Application Insights, em seguida, o exemplo acima deve ser modificado para obter `TelemetryClient` diretamente no construtor. Ver [isso](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core-no-visualstudio#frequently-asked-questions) para o exemplo completo.


*6. Que tipo de telemetria do Application Insights é produzido a partir `ILogger` logs? ou onde posso ver `ILogger` logs no Application Insights?*

* Captura de ApplicationInsightsLoggerProvider `ILogger` registra em log e cria `TraceTelemetry` dele. Se um objeto de exceção é passado para o método de log () em ILogger, em seguida, em vez de `TraceTelemetry`, um `ExceptionTelemetry` é criado. Esses itens de telemetria podem ser encontradas nos mesmos locais como qualquer outro `TraceTelemetry` ou `ExceptionTelemetry` para o Application Insights, incluindo o portal, o analytics ou o depurador local do Visual Studio.
Se você preferir sempre enviam `TraceTelemetry`, em seguida, use o trecho de código ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```.

*7. Eu não tenho o SDK instalado, e posso usar a extensão do aplicativo Web do Azure para habilitar o Application Insights para meus aplicativos do Asp.Net Core. Como usar o novo provedor?*

* Extensão do Application Insights no aplicativo Web do Azure usa o provedor antigo. Regras de filtragem podem ser modificadas no `appsettings.json` para seu aplicativo. Se você quiser tirar proveito do novo provedor, use a instrumentação de tempo de compilação, aproveitando dependência nuget no SDK. Este documento será atualizado quando extensão para usar o novo provedor.

*8. Eu estou usando o pacote autônomo Microsoft.Extensions.Logging.ApplicationInsights e habilitando o provedor do Application Insights pelo construtor de chamada. AddApplicationInsights("ikey"). Há uma opção para obter a chave de instrumentação da configuração?*


* Modifique `Program.cs` e `appsettings.json` conforme mostrado abaixo.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>()
            .ConfigureLogging((hostingContext, logging) =>
            {
                // hostingContext.HostingEnvironment can be used to determine environments as well.
                var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                logging.AddApplicationInsights(appInsightKey);
            });
}
```

Seção relevante do `appsettings.json`

```json
{
  "myikeyfromconfig": "putrealikeyhere"
}
```

O código acima é necessário somente ao usar o provedor de log autônomo. Para o monitoramento regular do Application Insights, chave de instrumentação é carregada automaticamente do caminho de configuração `ApplicationInsights:Instrumentationkey` e `appsettings.json` deve ter aparência abaixo.

```json
{
  "ApplicationInsights":
    {
        "Instrumentationkey":"putrealikeyhere"
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre:

* [Registro em log no Asp.Net Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Os logs de rastreamento do .NET no Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
