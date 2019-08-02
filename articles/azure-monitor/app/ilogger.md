---
title: Explorar os logs de rastreamento .NET no Azure Application Insights com ILogger
description: Exemplos de uso do provedor do Aplicativo Azure insights ILogger com ASP.NET Core e aplicativos de console.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: 925264bb69093ab70465665e1d2da615a7a3e53d
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261765"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider para logs do ILogger do .NET Core

O ASP.NET Core dá suporte a uma API de log que funciona com diferentes tipos de provedores de log internos e de terceiros. O registro em log é feito chamando **log ()** ou uma variante dele em instâncias de *ILogger* . Este artigo demonstra como usar o *ApplicationInsightsLoggerProvider* para capturar logs do ILogger no console do e ASP.NET Core aplicativos. Este artigo também descreve como o ApplicationInsightsLoggerProvider se integra a outras telemetrias de Application Insights.
Para obter mais informações, consulte [Entrar no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>ASP.NET Core aplicativos

O ApplicationInsightsLoggerProvider é habilitado por padrão em [Microsoft. ApplicationInsights. AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão 2.7.0-Beta3 (e posterior) quando você ativa o monitoramento de Application insights regular por meio de um dos métodos padrão:
- Chamando o método de extensão **UseApplicationInsights** em IWebHostBuilder 
- Chamando o método de extensão **AddApplicationInsightsTelemetry** em IServiceCollection

Os logs do ILogger que o ApplicationInsightsLoggerProvider captura estão sujeitos à mesma configuração que qualquer outra telemetria coletada. Eles têm o mesmo conjunto de TelemetryInitializers e TelemetryProcessors, usam o mesmo TelemetryChannel e são correlacionados e são amostrados da mesma maneira que outras telemetrias. Se você usar a versão 2.7.0-Beta3 ou posterior, nenhuma ação será necessária para capturar logs do ILogger.

Somente os logs ILogger de *aviso* ou superior (de todas as categorias) são enviados para Application insights por padrão. Mas você pode [aplicar filtros para modificar esse comportamento](#control-logging-level). Etapas adicionais são necessárias para capturar logs do ILogger de **Program.cs** ou **Startup.cs**. (Consulte [capturando logs do ILogger de startup.cs e Program.cs em aplicativos ASP.NET Core](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Se você usar uma versão anterior do SDK do Microsoft. ApplicationInsights. AspNet ou se quiser usar apenas o ApplicationInsightsLoggerProvider sem nenhum outro Application Insights monitoramento, use o seguinte procedimento:

1. Instale o pacote NuGet:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Modifique **Program.cs** conforme mostrado aqui:

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

O código na etapa 2 é configurado `ApplicationInsightsLoggerProvider`. O código a seguir mostra uma classe de controlador de exemplo `ILogger` , que usa para enviar logs. Os logs são capturados por Application Insights.

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

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Capturar logs do ILogger de Startup.cs e Program.cs em aplicativos ASP.NET Core

> [!NOTE]
> No ASP.NET Core 3,0 e posterior, não é mais possível injetar `ILogger` em Startup.cs e Program.cs. Consulte https://github.com/aspnet/Announcements/issues/353 para obter mais detalhes.

O novo ApplicationInsightsLoggerProvider pode capturar logs do início do pipeline de inicialização do aplicativo. Embora o ApplicationInsightsLoggerProvider seja habilitado automaticamente no Application Insights (começando com a versão 2.7.0-Beta3), ele não tem uma chave de instrumentação configurada até mais tarde no pipeline. Portanto, somente os logs das classes/Other do **controlador**serão capturados. Para capturar todos os logs que começam com **Program.cs** e **Startup.cs** em si, você deve habilitar explicitamente uma chave de instrumentação para ApplicationInsightsLoggerProvider. Além disso, o *TelemetryConfiguration* não está totalmente configurado quando você faz logon do **Program.cs** ou **Startup.cs** em si. Portanto, esses logs terão uma configuração mínima que usa InMemoryChannel, sem amostragem e nenhum dos inicializadores ou processadores de telemetria padrão.

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

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migrar do antigo ApplicationInsightsLoggerProvider

Versões do SDK Microsoft. ApplicationInsights. AspNet antes de 2.7.0-beta2 suportavam um provedor de log que agora é obsoleto. Esse provedor foi habilitado por meio do método de extensão **AddApplicationInsights ()** de ILoggerFactory. Recomendamos que você migre para o novo provedor, que envolve duas etapas:

1. Remova a chamada *ILoggerFactory. AddApplicationInsights ()* do método **Startup. Configure ()** para evitar o log duplo.
2. Reaplique as regras de filtragem no código, pois elas não serão respeitadas pelo novo provedor. Sobrecargas de *ILoggerFactory. AddApplicationInsights ()* levaram o mínimo de funções LogLevel ou Filter. Com o novo provedor, a filtragem faz parte da própria estrutura de registro em log. Isso não é feito pelo provedor de Application Insights. Portanto, todos os filtros fornecidos via *ILoggerFactory. AddApplicationInsights ()* sobrecargas devem ser removidos. E regras de filtragem devem ser fornecidas seguindo as instruções de [nível de log de controle](#control-logging-level) . Se você usar *appSettings. JSON* para filtrar o registro em log, ele continuará funcionando com o novo provedor, porque ambos usam o mesmo alias do provedor, *ApplicationInsights*.

Você ainda pode usar o provedor antigo. (Ele será removido somente em uma alteração de versão principal para 3. *XX*.) Mas é recomendável migrar para o novo provedor pelos seguintes motivos:

- O provedor anterior não tem suporte para escopos de [log](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). No novo provedor, as propriedades do escopo são adicionadas automaticamente como propriedades personalizadas à telemetria coletada.
- Os logs agora podem ser capturados muito antes no pipeline de inicialização do aplicativo. Os logs do **programa** e das classes de **inicialização** agora podem ser capturados.
- Com o novo provedor, a filtragem é feita no próprio nível da estrutura. Você pode filtrar os logs para o provedor de Application Insights da mesma maneira que para outros provedores, incluindo provedores internos como console, depuração e assim por diante. Você também pode aplicar os mesmos filtros a vários provedores.
- No ASP.NET Core (2,0 e posterior), a maneira recomendada para [habilitar os provedores de log](https://github.com/aspnet/Announcements/issues/255) é usando métodos de extensão em ILoggingBuilder no próprio **Program.cs** .

> [!Note]
> O novo provedor está disponível para aplicativos direcionados para o netstandard 2.0 ou posterior. Se seu aplicativo se destina a versões mais antigas do .NET Core, como o .NET Core 1,1, ou se ele se destina ao .NET Framework, continue a usar o provedor antigo.

## <a name="console-application"></a>Aplicativo de console

O código a seguir mostra um exemplo de aplicativo de console que é configurado para enviar rastreamentos de ILogger para Application Insights.

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

Este exemplo usa o pacote `Microsoft.Extensions.Logging.ApplicationInsights`autônomo. Por padrão, essa configuração usa o TelemetryConfiguration "mínimo simples" para enviar dados para Application Insights. Mínimo significa que InMemoryChannel é o canal usado. Não há nenhuma amostragem e nenhum TelemetryInitializers padrão. Esse comportamento pode ser substituído para um aplicativo de console, como mostra o exemplo a seguir.

Instale esse pacote adicional:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

A seção a seguir mostra como substituir o TelemetryConfiguration padrão usando os **serviços. Configure\<o método TelemetryConfiguration > ()** . Este exemplo configura `ServerTelemetryChannel` e faz amostragem. Ele adiciona um ITelemetryInitializer personalizado ao TelemetryConfiguration.

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

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Nível de log de controle

O ASP.NET Core *ILogger* infraestrutura tem um mecanismo interno para aplicar a [filtragem de log](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). Isso permite que você controle os logs que são enviados para cada provedor registrado, incluindo o provedor de Application Insights. A filtragem pode ser feita na configuração (normalmente usando um arquivo *appSettings. JSON* ) ou no código. Esse recurso é fornecido pela própria estrutura. Não é específico do provedor de Application Insights.

Os exemplos a seguir aplicam regras de filtro a ApplicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Criar regras de filtro na configuração com appSettings. JSON

Para ApplicationInsightsLoggerProvider, o alias do provedor `ApplicationInsights`é. A seção a seguir de *appSettings. JSON* configura os logs de *aviso* e acima de todas as categorias e *erros* e acima de categorias que começam com "Microsoft `ApplicationInsightsLoggerProvider`" para envio.

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

O trecho de código a seguir configura os logs de *aviso* e acima de todas as categorias e de *erro* e acima das categorias que começam com "Microsoft" para `ApplicationInsightsLoggerProvider`serem enviadas. Essa configuração é a mesma da seção anterior em appSettings *. JSON*.

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

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Quais são as versões antigas e novas do ApplicationInsightsLoggerProvider?

[O SDK Microsoft. ApplicationInsights. AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) incluiu um ApplicationInsightsLoggerProvider interno (Microsoft. ApplicationInsights. AspNetCore. Logging. ApplicationInsightsLoggerProvider), que foi habilitado por meio de **ILoggerFactory** métodos de extensão. Este provedor está marcado como obsoleto da versão 2.7.0-beta2. Ele será removido completamente na próxima alteração de versão principal. O pacote do [Microsoft. ApplicationInsights. AspNetCore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) não é obsoleto. É necessário habilitar o monitoramento de solicitações, dependências e assim por diante.

A alternativa sugerida é o novo pacote autônomo [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), que contém um ApplicationInsightsLoggerProvider aprimorado ( Microsoft. Extensions. Logging. ApplicationInsights. ApplicationInsightsLoggerProvider) e métodos de extensão no ILoggerBuilder para habilitá-lo.

[Microsoft. ApplicationInsights. AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão 2.7.0-Beta3 assume uma dependência do novo pacote e habilita o ILogger Capture automaticamente.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Por que alguns logs de ILogger são mostrados duas vezes no Application Insights?

A duplicação poderá ocorrer se você tiver a versão mais antiga (agora obsoleta) do ApplicationInsightsLoggerProvider `AddApplicationInsights` habilitada chamando on `ILoggerFactory`. Verifique se o método **Configure** tem o seguinte e remova-o:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Se você tiver um log duplo ao depurar do Visual Studio, defina `EnableDebugLogger` como *false* no código que habilita Application insights, da seguinte maneira. Essa duplicação e correção só é relevante quando você está depurando o aplicativo.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdkhttpswwwnugetorgpackagesmicrosoftapplicationinsightsaspnetcore-version-270-beta3-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Atualizei para [Microsoft. ApplicationInsights. AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão 2.7.0-Beta3 e os logs de ILogger são capturados automaticamente. Como fazer desativar esse recurso completamente?

Consulte a seção [nível de log de controle](../../azure-monitor/app/ilogger.md#control-logging-level) para ver como filtrar os logs em geral. Para desligar o ApplicationInsightsLoggerProvider, use `LogLevel.None`:

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

Quando você usa o pacote autônomo, `TelemetryClient` o não é injetado no contêiner de di, portanto, você precisa criar uma nova instância `TelemetryClient` do e usar a mesma configuração que o provedor de agente de log usa, como mostra o código a seguir. Isso garante que a mesma configuração seja usada para toda a telemetria personalizada, bem como telemetria do ILogger.

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
> Se você usar o pacote Microsoft. ApplicationInsights. AspNetCore para habilitar Application insights, modifique esse código para obter `TelemetryClient` diretamente no construtor. Para obter um exemplo, consulte [estas perguntas frequentes](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Que tipo de telemetria Application Insights é produzido dos logs do ILogger? Ou onde posso ver os logs do ILogger no Application Insights?

O ApplicationInsightsLoggerProvider captura logs do ILogger e cria TraceTelemetry a partir deles. Se um objeto de exceção for passado para o método **log ()** em ILogger, *ExceptionTelemetry* será criado em vez de TraceTelemetry. Esses itens de telemetria podem ser encontrados nos mesmos locais que qualquer outro TraceTelemetry ou ExceptionTelemetry para Application Insights, incluindo o portal, a análise ou o depurador local do Visual Studio.

Se você preferir sempre enviar TraceTelemetry, use este trecho de código:```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Não tenho o SDK instalado e uso a extensão de aplicativos Web do Azure para habilitar Application Insights para meus aplicativos ASP.NET Core. Como fazer usar o novo provedor? 

A extensão de Application Insights nos aplicativos Web do Azure usa o provedor antigo. Você pode modificar as regras de filtragem no arquivo *appSettings. JSON* para seu aplicativo. Para tirar proveito do novo provedor, use a instrumentação de tempo de compilação fazendo uma dependência do NuGet no SDK. Este artigo será atualizado quando a extensão mudar para usar o novo provedor.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Estou usando o pacote autônomo Microsoft. Extensions. Logging. ApplicationInsights e habilitando o provedor de Application Insights chamando o **Construtor. AddApplicationInsights ("iKey")** . Há uma opção para obter uma chave de instrumentação da configuração?


Modifique Program.cs e appSettings. JSON da seguinte maneira:

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

   Seção relevante de `appsettings.json`:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Esse código é necessário apenas quando você usa um provedor de log autônomo. Para o monitoramento de Application insights regular, a chave de instrumentação é carregada automaticamente do *caminho de configuração ApplicationInsights: Instrumentationkey*. AppSettings. JSON deve ter a seguinte aparência:

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

* [Fazendo logon ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Logs de rastreamento do .NET no Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
