---
title: Azure Application Insights para aplicativos de console | Microsoft Docs
description: Monitorar aplicativos web de disponibilidade, desempenho e uso.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/30/2019
ms.reviewer: lmolkova
ms.author: mbullwin
ms.openlocfilehash: 0c2a28462633d47ad1d3f247793e3fcf6f4d40c0
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795456"
---
# <a name="application-insights-for-net-console-applications"></a>Application Insights para aplicativos do console .NET
O [Application Insights](../../azure-monitor/app/app-insights-overview.md) permite que você monitore seu aplicativo Web quanto à disponibilidade, desempenho e uso.

Você precisa de uma assinatura do [Microsoft Azure](https://azure.com). Entre com uma conta da Microsoft, que você pode ter para o Windows, Xbox Live ou outros serviços de nuvem da Microsoft. Sua equipe pode ter uma assinatura organizacional do Azure: peça ao proprietário que adicione você a ela usando sua conta da Microsoft.

## <a name="getting-started"></a>Introdução

* No [portal do Azure](https://portal.azure.com), [crie um recurso Application Insights](../../azure-monitor/app/create-new-resource.md). Para o tipo de aplicativo, escolha **Geral**.
* Faça uma cópia da chave de instrumentação. Localize a chave no menu suspenso **Essentials** do novo recurso que você criou. 
* Instale o pacote [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) mais recente.
* Defina a chave de instrumentação em seu código antes de acompanhar qualquer telemetria (ou defina a variável de ambiente APPINSIGHTS_INSTRUMENTATIONKEY). Depois disso, você deve ser capaz de acompanhar a telemetria manualmente e vê-la no portal do Azure

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

* Instale a versão mais recente do pacote [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) - ele acompanha automaticamente HTTP, SQL ou outras chamadas de dependência externas.

Você pode inicializar e configurar o Application Insights pelo código ou usando o arquivo `ApplicationInsights.config`. Verifique se a inicialização ocorre o mais cedo possível. 

> [!NOTE]
> As instruções referentes a **ApplicationInsights.config** são aplicáveis apenas a aplicativos direcionados ao .NET Framework e não são aplicáveis a aplicativos .NET Core.

### <a name="using-config-file"></a>Usando o arquivo de configuração
Por padrão, o SDK do Application Insights procura pelo arquivo `ApplicationInsights.config` no diretório de trabalho quando `TelemetryConfiguration` está sendo criado

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

Você também pode especificar o caminho para o arquivo de configuração.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

Para obter mais informações, consulte [referência do arquivo de configuração](configuration-with-applicationinsights-config.md).

Você pode obter um exemplo completo do arquivo de configuração ao instalar a versão mais recente do pacote [Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer). Aqui está a configuração **mínima** para a coleção de dependência que é equivalente ao exemplo de código.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>Configurando a coleta de telemetria do código
> [!NOTE]
> Não há suporte para ler o arquivo de configuração no .NET Core. Você pode considerar o uso [SDK do Application Insights para ASP.NET Core](../../azure-monitor/app/asp-net-core.md)

* Durante a inicialização do aplicativo, crie e configure a instância `DependencyTrackingTelemetryModule` - ela deve ser singleton e deve ser preservada durante o tempo de vida do aplicativo.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* Adicionar inicializadores de telemetria comuns

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

Se você criou a configuração de texto sem formatação com `TelemetryConfiguration()` construtor, você precisa habilitar o suporte a correlação Além disso. **Não é necessário** se você ler a configuração de arquivo, usado `TelemetryConfiguration.CreateDefault()` ou `TelemetryConfiguration.Active`.

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* Você talvez queira instalar e inicializar o módulo de coletor do contador de desempenho conforme descrito [aqui](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)


#### <a name="full-example"></a>Exemplo completo

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient();
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking so wait a bit
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

            // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

            // enable known dependency tracking, note that in future versions, we will extend this list. 
            // please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>Próximas etapas
* [Monitore dependências](../../azure-monitor/app/asp-net-dependencies.md) para ver se REST, SQL ou outros recursos externos estão causando lentidão.
* [Use a API](../../azure-monitor/app/api-custom-events-metrics.md) para enviar seus próprios eventos e métricas para uma exibição mais detalhada do desempenho e do uso do aplicativo.
