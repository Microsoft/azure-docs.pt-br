---
title: Azure Application Insights para ASP.NET Core sem o Visual Studio | Microsoft Docs
description: Monitorar aplicativos web ASP.NET Core de disponibilidade, desempenho e uso.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: cithomas
ms.openlocfilehash: 8243523887ec9861459b2d196126237cf89bad97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691335"
---
# <a name="application-insights-for-aspnet-core-applications"></a>Application Insights para aplicativos ASP.NET Core

Este artigo descreve as etapas de habilitação do Application Insights para um [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2) aplicativo sem usar o IDE do Visual Studio. Se você tiver o IDE do Visual Studio instalado, então [isso](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) documento contém instruções específicas do Visual Studio. Seguindo as instruções neste artigo, o Application Insights iniciará a coleta de solicitações, dependências, exceções, contadores de desempenho, as pulsações e logs de seu aplicativo ASP.NET Core. O aplicativo de exemplo usado é um [aplicativo MVC](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/?view=aspnetcore-2.2) direcionamento `netcoreapp2.2`, mas as instruções aqui são aplicáveis a todos os aplicativos do ASP.NET Core. Todas as exceções são destacadas quando aplicável.

## <a name="supported-scenarios"></a>Cenários com suporte

Application Insights SDK (Software Development Kit) para o ASP.NET Core pode monitorar seus aplicativos, independentemente de onde ou como o aplicativo é executado. Se seu aplicativo está em execução e tem conectividade de rede para o serviço Application Insights, a telemetria é esperada para ser coletado. Esse suporte inclui, mas não está limitado a qualquer sistema operacional (Windows, Linux, Mac), o método de hospedagem (em processo versus fora do processo), o método de implantação dependente de estrutura vs (independentes), servidor Web (IIS, o Kestrel), plataforma (aplicativos Web do Azure, VM do Azure Docker, AKS e assim por diante.) ou IDE (linha de comando do Visual Studio, VS Code).

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo do ASP.NET Core está funcionando. Siga [isso](https://docs.microsoft.com/aspnet/core/getting-started/) guia para criar um aplicativo ASP.NET Core, se necessário.
- Uma válido Application Insights chave de instrumentação, que é necessário para enviar qualquer telemetria ao serviço Application Insights. Siga [esses](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) instruções para criar um novo recurso do Application Insights, se necessário e obter uma chave de instrumentação.

## <a name="enabling-application-insights-server-side-telemetry"></a>Habilitando a telemetria do lado do servidor do Application Insights

1. Instale o SDK do Application Insights para ASP.NET Core, que é um pacote do NuGet regular. É recomendável usar sempre a versão estável mais recente. Algumas das funcionalidades descritas neste artigo podem não estar disponíveis em versões mais antigas. Notas de versão completo para o SDK podem ser encontradas [aqui](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases). 

A seguir mostra as alterações a serem adicionadas ao arquivo. csproj do projeto.

```xml
    <ItemGroup>
      <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
    </ItemGroup>
```

2. Adicione `services.AddApplicationInsightsTelemetry();` à `ConfigureServices()` método na sua `Startup` classe. Exemplo completo abaixo.

```csharp
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        // The following line enables Application Insights telemetry collection.
        services.AddApplicationInsightsTelemetry();

        // code adding other services for your application
        services.AddMvc();
    }
```

3. Configure a chave de instrumentação.

   Embora seja possível fornecer a chave de instrumentação como um argumento para `services.AddApplicationInsightsTelemetry("putinstrumentationkeyhere");`, é recomendável especificar a chave de instrumentação na configuração. A seguir mostra como especificar uma chave de instrumentação em `appsettings.json`. Certifique-se de `appsettings.json` é copiado para a pasta raiz de aplicativo durante a publicação.

```json
    {
      "ApplicationInsights": {
        "InstrumentationKey": "putinstrumentationkeyhere"
      },
      "Logging": {
        "LogLevel": {
          "Default": "Warning"
        }
      }
    }
```

    Alternately, the instrumentation key can also be specified in either of the following environment variables.
    APPINSIGHTS_INSTRUMENTATIONKEY
    ApplicationInsights:InstrumentationKey

    Example:
    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

`APPINSIGHTS_INSTRUMENTATIONKEY` normalmente é usado para especificar a chave de instrumentação para aplicativos implantados para aplicativos Web do Azure.

> [!NOTE]
> Uma chave de instrumentação especificada no código wins pela variável de ambiente `APPINSIGHTS_INSTRUMENTATIONKEY`, qual vence sobre outras opções.

4. Executar o aplicativo e fazer solicitações a ele. Telemetria agora deve começar a fluir para o Application Insights. A telemetria a seguir é coletada automaticamente pelo SDK do Application Insights.

    1. Solicitações - solicitações da web de entrada para seu aplicativo.
    1. Dependências
        1. Chamadas HTTP feitas com `HttpClient`
        1. Chamadas SQL feitas com `SqlClient`
        1. Chamadas de armazenamento do Azure feitas com [cliente de armazenamento do Azure](https://www.nuget.org/packages/WindowsAzure.Storage/)*
        1. [SDK do cliente do hub de eventos](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) versão 1.1.0 e superior
        1. [SDK do cliente do barramento de serviço](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) versão 3.0.0 e superior

             * O azure Cosmos DB é acompanhado automaticamente somente se o HTTP/HTTPS é usado. O modo TCP não será capturado pelo Application Insights.


    1. [Contadores de desempenho](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/)
        1. Suporte para contadores de desempenho no ASP.NET Core é limitado ao seguinte
            1. O SDK versão 2.4.1 e acima coleta contadores de desempenho se o aplicativo estiver em execução no Azure Web App (Windows)
            1. SDK versão 2.7.0-beta3 e acima coleta contadores de desempenho se o aplicativo está em execução no Windows e direcionamento `NETSTANDARD2.0` ou superior.
            1. Para aplicativos destinados ao .NET Framework completo, os contadores de desempenho têm suporte em todas as versões do SDK.

            Este documento será atualizado quando o suporte de contador de desempenho no Linux é adicionado.
    1. [Live Metrics](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)
    1. `ILogger` logs de gravidade `Warning` ou acima são automaticamente capturados do SDK versão 2.7.0-beta3 ou superior. Leia mais [aqui](https://docs.microsoft.com/azure/azure-monitor/app/ilogger).

Ele pode levar alguns minutos para que a telemetria começar a aparecer no portal. Para verificar rapidamente se tudo está funcionando, é melhor usar [Live Metrics](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream), enquanto a tomada de solicitações para o aplicativo em execução.

## <a name="send-ilogger-logs-to-application-insights"></a>Enviar logs de ILogger ao Application Insights

Application Insights oferece suporte à captura de logs enviados por meio de ILogger. Leia a documentação completa [aqui](https://docs.microsoft.com/azure/azure-monitor/app/ilogger).

## <a name="enable-client-side-telemetry-for-web-applications"></a>Habilitar a telemetria do lado do cliente para aplicativos Web

As etapas acima são suficientes para iniciar a coleta de telemetria do lado servidor. Se seu aplicativo tiver componentes do lado do cliente, siga as etapas abaixo para iniciar a coleta [telemetria de uso](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview) a partir daí.

1. No viewimports. cshtml, adicione injeção:

```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
```

2. No layout. cshtml, inserir HtmlHelper ao final da `<head>` seção, mas antes de qualquer outro script. Qualquer telemetria JavaScript personalizada que você deseja relatar a partir da página deve ser inserida após este trecho de código:

```cshtml
    @Html.Raw(JavaScriptSnippet.FullScript)
    </head>
```

Modifique os nomes de arquivos, de acordo com seu aplicativo real. Os nomes usados acima são de um modelo de aplicativo MVC padrão.

## <a name="configuring-application-insights-sdk"></a>Configurando o SDK do Application Insights

SDK do Application Insights para ASP.NET Core pode ser personalizado para alterar a configuração padrão. Os usuários do SDK do ASP.NET do Application Insights podem estar familiarizados com a configuração usando `ApplicationInsights.config`, ou modificando `TelemetryConfiguration.Active`. Para o ASP.NET Core, configuração é feita de forma diferente. SDK do ASP.NET Core é adicionado para o aplicativo usando internos do ASP.NET Core [DependencyInjection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) mecanismo e configurando o mesmo também usa DependencyInjection. Quase todas as alterações são feitas na `ConfigureServices()` método de sua `Startup.cs` de classe, a menos que indicado de outra forma. Siga as seções abaixo para saber mais.

> [!NOTE]
> É importante observar que a configuração modificando modificando `TelemetryConfiguration.Active` não é recomendado em aplicativos ASP.NET Core.

### <a name="configuring-using-applicationinsightsserviceoptions"></a>Configurar usando ApplicationInsightsServiceOptions

É possível modificar algumas configurações comuns, passando `ApplicationInsightsServiceOptions` para `services.AddApplicationInsightsTelemetry();`. Um exemplo é mostrado abaixo.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetry(aiOptions);
    }
```

A lista exata de definições configuráveis na `ApplicationInsightsServiceOptions` pode ser encontrado [aqui](https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/f0b8631e482d25982eb52092103b34e3ff6e5fef/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs).

### <a name="sampling"></a>amostragem

SDK do Application Insights para ASP.NET Core dá suporte a FixedRate e amostragem adaptável. Amostragem adaptável está habilitada por padrão. Siga [isso](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) documento para aprender a configurar a amostragem para aplicativos ASP.NET Core.

### <a name="adding-telemetryinitializers"></a>Adicionando TelemetryInitializers

Para adicionar uma nova `TelemetryInitializer`, adicioná-lo no contêiner DependencyInjection, conforme mostrado abaixo. `TelemetryInitializer`s adicionado ao contêiner DependencyInjection será captado pelo SDK automaticamente.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>Removendo TelemetryInitializers

Para remover todos os ou TelemetryInitializers específicos, que estão presentes por padrão, use o seguinte código de exemplo **após** chamar `AddApplicationInsightsTelemetry()`.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // Remove a specific built-in TelemetryInitializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                         (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
    }
```

### <a name="adding-telemetryprocessors"></a>Adicionando TelemetryProcessors

Processadores de telemetria personalizada podem ser adicionados para o `TelemetryConfiguration` usando o método de extensão `AddApplicationInsightsTelemetryProcessor` em `IServiceCollection`. Use o exemplo a seguir.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>Configuração ou remoção de padrão TelemetryModules

Os seguintes módulos de coleta automática são habilitados por padrão e são responsáveis por coletar automaticamente a telemetria. Pode ser desabilitados e configurados para alterar o comportamento padrão.

1. `RequestTrackingTelemetryModule`
1. `DependencyTrackingTelemetryModule`
1. `PerformanceCollectorModule`
1. `QuickPulseTelemetryModule`
1. `AppServicesHeartbeatTelemetryModule`
1. `AzureInstanceMetadataTelemetryModule`

Para configurar qualquer padrão `TelemetryModule`, use o método de extensão `ConfigureTelemetryModule<T>` em `IServiceCollection` conforme mostrado no exemplo a seguir.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures DependencyTrackingTelemetryModule.
        // Similarly, any other default modules can be configured.
        services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
                        {
                            module.EnableW3CHeadersInjection = true;
                        });

        // The following removes PerformanceCollectorModule to disable perf-counter collection.
        // Similarly, any other default modules can be removed.
        var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
        if (performanceCounterService != null)
        {
         services.Remove(performanceCounterService);
        }
    }
```

### <a name="configuring-telemetry-channel"></a>Configurando o canal de telemetria

O canal padrão usado é o `ServerTelemetryChannel`. Ele pode ser substituído pelo exemplo a seguir.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>Perguntas frequentes

*1. Eu quero acompanhar a telemetria adicional além da telemetria coletado automaticamente. Como fazer isso?*

* Obtenha uma instância de `TelemetryClient` usando a injeção de construtor e chamar o necessária `TrackXXX()` método nele. Não é recomendável criar um novo `TelemetryClient` instâncias do aplicativo do ASP.NET Core, como uma instância singleton da `TelemetryClient` já está registrado no contêiner de DI compartilha `TelemetryConfiguration` com o restante da telemetria. Criando um novo `TelemetryClient` instância é recomendada somente se ele deve ter uma configuração separada do restante da telemetria. O exemplo a seguir mostra como acompanhar a telemetria adicional de um controlador.

```csharp
public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // use constructor injection to get TelemetryClient instance
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // call required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

 Consulte a [métricas personalizadas do Application Insights referência da API](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/) para descrição de relatórios do Application Insights de dados personalizados.

*2. Alguns modelos do Visual Studio usado o método de extensão UseApplicationInsights() no IWebHostBuilder para habilitar o Application Insights. Esse uso ainda é válida?*

* Habilitar o Application Insights com esse método é válido e é usado na integração do Visual Studio e nas extensões do aplicativo Web do Azure. No entanto, é recomendável usar `services.AddApplicationInsightsTelemery()` pois ele fornece sobrecargas para controlar algumas configurações. Ambos os método internamente faz a mesma coisa, portanto, se não há nenhuma configuração personalizada a ser aplicado, chamar qualquer uma serve.

*3. Eu estou implantando meu aplicativo ASP.NET Core, aplicativos Web do Azure. Devo habilitar a extensão do Application Insights de aplicativos Web ainda?*

* Se o SDK estiver instalado no momento da compilação, conforme mostrado neste artigo, não é necessário para habilitar a extensão do Application Insights a partir do portal de aplicativos Web. Mesmo se a extensão estiver instalada, ela será retirada, quando ele detecta que o SDK já foi adicionada ao aplicativo. Habilitar o Application Insights de extensão libera você da instalação e atualização do SDK ao seu aplicativo. No entanto, habilitar o Application Insights, de acordo com este artigo é mais flexível para motivos abaixo.
    1. Telemetria do Application Insights continuem a funcionar
        1. Todos os sistemas operacionais - Mac Windows, Linux.
        1. Todos os publicação modo - independente ou dependente do Framework.
        1. Todas as estruturas de destino, incluindo o .NET Framework completo.
        1. Todas as opções de hospedagem - aplicativo Web do Azure, VMs, Linux, contêineres, AKS, não do Azure.
    1. Telemetria pode ser vista localmente, durante a depuração do Visual Studio.
    1. Permite o uso do acompanhamento de telemetria personalizada adicional `TrackXXX()` API.
    1. Tem controle total sobre a configuração.

*4. Posso habilitar o monitoramento do Application Insights usando ferramentas como o Monitor de Status?*

* Não. [Monitor de status](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) e sua substituição futura [IISConfigurator](https://github.com/Microsoft/ApplicationInsights-Announcements/issues/21) atualmente suporta apenas a ASP.NET. O documento será atualizado quando o suporte para o aplicativo ASP.NET Core está disponível.

*5. Eu tenho um aplicativo do ASP.NET Core 2.0? Não é Application Insights habilitado automaticamente para que eles sem eu fazer nada?*

* `Microsoft.AspNetCore.All` metapacote 2.0 incluídos SDK do Application Insights (versão 2.1.0), e se você executar o aplicativo no depurador do Visual Studio, Visual Studio habilita o application insights e mostra a telemetria localmente no próprio IDE. Telemetria não foi enviada para o serviço do Application Insights, a menos que uma chave de instrumentação é especificada explicitamente. É recomendável seguir as instruções neste artigo para habilitar o Application Insights, até mesmo para 2.0 aplicativos.

*6. Executo meu aplicativo no Linux. Todos os recursos também têm suporte no Linux?*

* Sim. Suporte de recurso para o SDK é o mesmo em todas as plataformas, com as seguintes exceções:
    1. Contadores de desempenho ainda não têm suporte no Windows não. Este documento será atualizado quando o suporte a Linux é adicionado.
    1. Embora `ServerTelemetryChannel` está habilitado por padrão, se o aplicativo é executado em não-windows, o canal não cria automaticamente uma pasta de armazenamento local para manter a telemetria temporariamente se houver problemas de rede. Essa limitação faz com que a telemetria serão perdidos se houver problemas de servidor ou de rede temporária. A solução alternativa para esse problema é para o usuário configure uma pasta local para o canal, conforme mostrado abaixo.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure channel to use the given folder to temporarily
        // store telemetry items during network or application insights server issues.
        // User should ensure that the given folder already exists,
        // and that application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```
