---
title: Explore os logs de rastreamento do .NET com informações de ILogger-Aplicativo Azure
description: Exemplos de uso do provedor do Aplicativo Azure insights ILogger com ASP.NET Core e aplicativos de console.
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 987d5b78c5fe680f43ff6a001e7a31a8ae9f6124
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931454"
---
# <a name="applicationinsightsloggerprovider-for-microsoftextensionlogging"></a>ApplicationInsightsLoggerProvider para Microsoft. Extension. Logging

Este artigo demonstra como usar o `ApplicationInsightsLoggerProvider` para capturar `ILogger` logs no console do e ASP.NET Core aplicativos.
Para saber mais sobre registro em log, consulte [fazendo logon ASP.NET Core](/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>ASP.NET Core aplicativos

`ApplicationInsightsLoggerProvider` é habilitado por padrão para aplicativos ASP.NET Core quando o ApplicationInsights é configurado usando [código](./asp-net-core.md) ou abordagem [sem código](./azure-web-apps.md?tabs=netcore#enable-agent-based-monitoring) .

Somente os logs de *aviso* e acima `ILogger` (de todas as [categorias](/aspnet/core/fundamentals/logging/#log-category)) são enviados para Application insights por padrão. Mas você pode [personalizar esse comportamento](./asp-net-core.md#how-do-i-customize-ilogger-logs-collection). Etapas adicionais são necessárias para capturar logs do ILogger de **Program.cs** ou **Startup.cs**. (Consulte [capturando logs do ILogger de startup.cs e Program.cs em aplicativos ASP.NET Core](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Se você quiser usar apenas `ApplicationInsightsLoggerProvider` sem qualquer outro monitoramento de Application insights, use as etapas a seguir.

1. Instale o pacote NuGet:

   ```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
    </ItemGroup>
   ```

1. Modifique `Program.cs` conforme mostrado aqui:

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
                   // Providing an instrumentation key here is required if you're using
                   // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                   // or if you want to capture logs from early in the application startup
                   // pipeline from Startup.cs or Program.cs itself.
                   builder.AddApplicationInsights("put-actual-ikey-here");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

O código na etapa 2 é configurado `ApplicationInsightsLoggerProvider` . O código a seguir mostra uma classe de controlador de exemplo, que usa `ILogger` para enviar logs. Os logs são capturados por Application Insights.

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogError("An example of an Error level message");
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Capturar logs do ILogger de Startup.cs e Program.cs em aplicativos ASP.NET Core

> [!NOTE]
> No ASP.NET Core 3,0 e posterior, não é mais possível injetar `ILogger` em Startup.cs e Program.cs. Consulte https://github.com/aspnet/Announcements/issues/353 para obter mais detalhes.

`ApplicationInsightsLoggerProvider` pode capturar logs do início na inicialização do aplicativo. Embora o ApplicationInsightsLoggerProvider seja habilitado automaticamente no Application Insights (a partir da versão 2.7.1), ele não tem uma chave de instrumentação configurada até mais tarde no pipeline. Portanto, somente os logs das classes/Other do **controlador**serão capturados. Para capturar todos os logs que começam com **Program.cs** e **Startup.cs** em si, você deve habilitar explicitamente uma chave de instrumentação para ApplicationInsightsLoggerProvider. Além disso, o *TelemetryConfiguration* não está totalmente configurado quando você faz logon do **Program.cs** ou **Startup.cs** em si. Portanto, esses logs terão uma configuração mínima que usa [InMemoryChannel](./telemetry-channels.md), sem [amostragem](./sampling.md)e nenhum [dos inicializadores ou processadores de telemetria](./api-filtering-sampling.md)padrão.

Os exemplos a seguir demonstram essa funcionalidade com **Program.cs** e **Startup.cs**.

#### <a name="example-programcs"></a>Exemplo de Program.cs

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
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Program).FullName, LogLevel.Trace);

            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             (typeof(Startup).FullName, LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Exemplo de Startup.cs

```csharp
public class Startup
{
    private readonly ILogger _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migrar do antigo ApplicationInsightsLoggerProvider

Versões do SDK Microsoft. ApplicationInsights. AspNet antes do 2.7.1 suportavam um provedor de registro em log que agora é obsoleto. Esse provedor foi habilitado por meio do método de extensão **AddApplicationInsights ()** de ILoggerFactory. Recomendamos que você migre para o novo provedor, que envolve duas etapas:

1. Remova a chamada *ILoggerFactory. AddApplicationInsights ()* do método **Startup.Configlhar ()** para evitar o log duplo.
2. Reaplique as regras de filtragem no código, pois elas não serão respeitadas pelo novo provedor. Sobrecargas de *ILoggerFactory. AddApplicationInsights ()* levaram o mínimo de funções LogLevel ou Filter. Com o novo provedor, a filtragem faz parte da própria estrutura de registro em log. Isso não é feito pelo provedor de Application Insights. Portanto, todos os filtros fornecidos via *ILoggerFactory. AddApplicationInsights ()* sobrecargas devem ser removidos. E regras de filtragem devem ser fornecidas seguindo as instruções de [nível de log de controle](#control-logging-level) . Se você usar *appsettings.jsno* para filtrar o registro em log, ele continuará funcionando com o novo provedor, porque ambos usam o mesmo alias do provedor, *ApplicationInsights*.

Você ainda pode usar o provedor antigo. (Ele será removido somente em uma alteração de versão principal para 3. *XX*.) mas recomendamos que você migre para o novo provedor pelos seguintes motivos:

- O provedor anterior não tem suporte para [escopos de log](/aspnet/core/fundamentals/logging#log-scopes). No novo provedor, as propriedades do escopo são adicionadas automaticamente como propriedades personalizadas à telemetria coletada.
- Os logs agora podem ser capturados muito antes no pipeline de inicialização do aplicativo. Os logs do **programa** e das classes de **inicialização** agora podem ser capturados.
- Com o novo provedor, a filtragem é feita no próprio nível da estrutura. Você pode filtrar os logs para o provedor de Application Insights da mesma maneira que para outros provedores, incluindo provedores internos como console, depuração e assim por diante. Você também pode aplicar os mesmos filtros a vários provedores.
- No ASP.NET Core (2,0 e posterior), a maneira recomendada para  [habilitar os provedores de log](https://github.com/aspnet/Announcements/issues/255) é usando métodos de extensão em ILoggingBuilder no próprio **Program.cs** .

> [!Note]
> O novo provedor está disponível para aplicativos direcionados para o netstandard 2.0 ou posterior. No [SDK Microsoft. ApplicationInsights. AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão 2.14.0 em diante, o novo provedor também está disponível para aplicativos direcionados .NET Framework NET461 ou posterior. Se seu aplicativo se destina a versões mais antigas do .NET Core, como o .NET Core 1,1, ou se ele se destina ao .NET Framework menos de NET46, continue a usar o provedor antigo.

## <a name="console-application"></a>Aplicativo de console

> [!NOTE]
> Há um novo SDK Application Insights chamado [Microsoft. ApplicationInsights. WorkerService](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) , que pode ser usado para habilitar Application insights (ILogger e outras telemetrias Application insights) para qualquer aplicativo de console. É recomendável usar esse pacote e as instruções associadas, [aqui](./worker-service.md).

Se você quiser usar apenas ApplicationInsightsLoggerProvider sem nenhum outro monitoramento de Application Insights, use as etapas a seguir.

Pacotes instalados:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.15.0" />  
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create the DI container.
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

        logger.LogInformation("Logger is working");

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

Este exemplo usa o pacote autônomo `Microsoft.Extensions.Logging.ApplicationInsights` . Por padrão, essa configuração usa o TelemetryConfiguration "mínimo simples" para enviar dados para Application Insights. Mínimo significa que InMemoryChannel é o canal usado. Não há nenhuma amostragem e nenhum TelemetryInitializers padrão. Esse comportamento pode ser substituído para um aplicativo de console, como mostra o exemplo a seguir.

Instale esse pacote adicional:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

A seção a seguir mostra como substituir o TelemetryConfiguration padrão usando o método **services.Configlhar \<TelemetryConfiguration> ()** . Este exemplo configura `ServerTelemetryChannel` e faz amostragem. Ele adiciona um ITelemetryInitializer personalizado ao TelemetryConfiguration.

```csharp
    // Create the DI container.
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
    
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Nível de log de controle

`ILogger` tem um mecanismo interno para aplicar a [filtragem de log](/aspnet/core/fundamentals/logging#log-filtering). Isso permite que você controle os logs que são enviados para cada provedor registrado, incluindo o provedor de Application Insights. A filtragem pode ser feita na configuração (normalmente usando um *appsettings.jsno* arquivo) ou no código.

Os exemplos a seguir mostram como aplicar regras de filtro ao `ApplicationInsightsLoggerProvider` .

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Criar regras de filtro na configuração com appsettings.jsem

Para ApplicationInsightsLoggerProvider, o alias do provedor é `ApplicationInsights` . A seção a seguir de *appsettings.json* instrui os provedores de log em geral a fazer logon no *aviso* de nível e acima. Em seguida, ele substitui as `ApplicationInsightsLoggerProvider` categorias de log a que começam com "Microsoft" em um *erro* de nível e acima.

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Microsoft": "Error"
      }
    }
  }
}
```

### <a name="create-filter-rules-in-code"></a>Criar regras de filtro no código

O trecho de código a seguir configura os logs de *aviso* e acima de todas as categorias e de *erro* e acima das categorias que começam com "Microsoft" para serem enviadas `ApplicationInsightsLoggerProvider` . Essa configuração é a mesma da seção anterior em *appsettings.jsem*.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="logging-scopes"></a>Escopos de log

`ApplicationInsightsLoggingProvider` o dá suporte a escopos de [log](/aspnet/core/fundamentals/logging#log-scopes) e escopos são habilitados por padrão.

Se o escopo for do tipo `IReadOnlyCollection<KeyValuePair<string,object>>` , cada par chave-valor na coleção será adicionado à telemetria do Application insights como propriedades personalizadas. No exemplo a seguir, os logs serão capturados como `TraceTelemetry` e terão ("myKey", "myValue") nas propriedades.

```csharp
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
    {
        _logger.LogError("An example of an Error level message");
    }
```

Se qualquer outro tipo for usado como escopo, ele será armazenado sob a propriedade "Scope" na telemetria do Application insights. No exemplo a seguir, o `TraceTelemetry` terá uma propriedade chamada "Scope" que contém o escopo.

```csharp
    using (_logger.BeginScope("hello scope"))
    {
        _logger.LogError("An example of an Error level message");
    }
```

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Quais são as versões antigas e novas do ApplicationInsightsLoggerProvider?

[O SDK Microsoft. ApplicationInsights. AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) incluiu um ApplicationInsightsLoggerProvider interno (Microsoft. ApplicationInsights. AspNetCore. Logging. ApplicationInsightsLoggerProvider), que foi habilitado por meio de métodos de extensão **ILoggerFactory** . Este provedor está marcado como obsoleto da versão 2.7.1. Ele será removido completamente na próxima alteração de versão principal. O pacote do [Microsoft. ApplicationInsights. AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) não é obsoleto. É necessário habilitar o monitoramento de solicitações, dependências e assim por diante.

A alternativa sugerida é o novo pacote autônomo [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), que contém um ApplicationInsightsLoggerProvider aprimorado (Microsoft. Extensions. Logging. ApplicationInsights. ApplicationInsightsLoggerProvider) e métodos de extensão no ILoggerBuilder para habilitá-lo.

[Microsoft. ApplicationInsights. AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão 2.7.1 usa uma dependência do novo pacote e habilita o ILogger Capture automaticamente.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Por que alguns logs de ILogger são mostrados duas vezes no Application Insights?

A duplicação poderá ocorrer se você tiver a versão mais antiga (agora obsoleta) do ApplicationInsightsLoggerProvider habilitada chamando `AddApplicationInsights` on `ILoggerFactory` . Verifique se o método **Configure** tem o seguinte e remova-o:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Se você tiver um log duplo ao depurar do Visual Studio, defina `EnableDebugLogger` como  *false* no código que habilita Application insights, da seguinte maneira. Essa duplicação e correção só é relevante quando você está depurando o aplicativo.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdk-version-271-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Atualizei para [Microsoft. ApplicationInsights. AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão 2.7.1, e logs de ILogger são capturados automaticamente. Como fazer desativar esse recurso completamente?

Consulte a seção [nível de log de controle](#control-logging-level) para ver como filtrar os logs em geral. Para desligar o ApplicationInsightsLoggerProvider, use `LogLevel.None` :

**No código:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**Em configuração:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Por que alguns logs do ILogger não têm as mesmas propriedades que outras?

Application Insights captura e envia logs do ILogger usando o mesmo TelemetryConfiguration usado para todas as outras telemetrias. Mas há uma exceção. Por padrão, o TelemetryConfiguration não está totalmente configurado quando você faz logon do **Program.cs** ou do **Startup.cs**. Os logs desses locais não terão a configuração padrão, portanto, eles não estarão executando todos os TelemetryInitializers e TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Estou usando o pacote autônomo Microsoft. Extensions. Logging. ApplicationInsights e quero registrar manualmente uma telemetria personalizada adicional. Como devo fazer isso?

Quando você usa o pacote autônomo, o `TelemetryClient` não é injetado no contêiner de di, portanto, você precisa criar uma nova instância do `TelemetryClient` e usar a mesma configuração que o provedor de agente de log usa, como mostra o código a seguir. Isso garante que a mesma configuração seja usada para toda a telemetria personalizada, bem como telemetria do ILogger.

```csharp
public class MyController : ApiController
{
   // This telemetryclient can be used to track additional telemetry using TrackXXX() api.
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
> Se você usar o pacote Microsoft. ApplicationInsights. AspNetCore para habilitar Application Insights, modifique esse código para obter `TelemetryClient` diretamente no construtor. Para obter um exemplo, consulte [estas perguntas frequentes](./asp-net-core.md#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Que tipo de telemetria Application Insights é produzido dos logs do ILogger? Ou onde posso ver os logs do ILogger no Application Insights?

O ApplicationInsightsLoggerProvider captura logs do ILogger e cria TraceTelemetry a partir deles. Se um objeto de exceção for passado para o `Log` método em `ILogger` , *ExceptionTelemetry* será criado em vez de TraceTelemetry. Esses itens de telemetria podem ser encontrados nos mesmos locais que qualquer outro TraceTelemetry ou ExceptionTelemetry para Application Insights, incluindo o portal, a análise ou o depurador local do Visual Studio.

Se você preferir sempre enviar TraceTelemetry, use este trecho de código: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Não tenho o SDK instalado e uso a extensão de aplicativos Web do Azure para habilitar Application Insights para meus aplicativos ASP.NET Core. Como fazer usar o novo provedor? 

A extensão de Application Insights nos aplicativos Web do Azure usa o novo provedor. Você pode modificar as regras de filtragem na *appsettings.jsno* arquivo para seu aplicativo.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Estou usando o pacote autônomo Microsoft. Extensions. Logging. ApplicationInsights e habilitando o provedor de Application Insights chamando o **Construtor. AddApplicationInsights ("iKey")**. Há uma opção para obter uma chave de instrumentação da configuração?


Modifique Program.cs e appsettings.jsem da seguinte maneira:

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

   Seção relevante de `appsettings.json` :

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Esse código é necessário apenas quando você usa um provedor de log autônomo. Para o monitoramento de Application Insights regular, a chave de instrumentação é carregada automaticamente do caminho de configuração *ApplicationInsights: Instrumentationkey*. Appsettings.jsem deve ser assim:

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

* [Registro em log no ASP.NET Core](/aspnet/core/fundamentals/logging)
* [Logs de rastreamento do .NET no Application Insights](./asp-net-trace-logs.md)

