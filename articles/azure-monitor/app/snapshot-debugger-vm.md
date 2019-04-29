---
title: Habilitar o depurador de instantâneos para aplicativos .NET no Azure Service Fabric, serviço de nuvem e máquinas virtuais | Microsoft Docs
description: Habilitar o depurador de instantâneos para aplicativos .NET no Azure Service Fabric, serviço de nuvem e máquinas virtuais
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: brahmnes
ms.openlocfilehash: ac937ddb1bcaed6813a0de4d631f820eff01e26f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60783493"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-service-fabric-cloud-service-and-virtual-machines"></a>Habilitar o depurador de instantâneos para aplicativos .NET no Azure Service Fabric, serviço de nuvem e máquinas virtuais

Se o ASP.NET ou ASP.NET core execuções de aplicativo no serviço de aplicativo do Azure, as instruções abaixo também podem ser usadas. A menos que seu aplicativo requer uma configuração personalizada do depurador de instantâneo, ele é altamente recomendável [habilitar o depurador de instantâneo por meio da página de portal do Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json). Se seu aplicativo é executado no Azure Service Fabric, serviço de nuvem, máquinas virtuais ou computadores locais, as instruções a seguir devem ser usadas. 
    
## <a name="configure-snapshot-collection-for-aspnet-applications"></a>Configurar a coleta de instantâneo para aplicativos ASP.NET

1. [Habilite o Application Insights no seu aplicativo web](../../azure-monitor/app/asp-net.md), se você ainda não tiver feito isso.

2. Inclua o pacote do NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) em seu aplicativo.

3. Se necessário, personalizadas a configuração do depurador de instantâneo adicionada ao [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). A configuração padrão do depurador de instantâneos está basicamente vazia e todas as configurações são opcionais. Aqui está um exemplo que mostra uma configuração equivalente para a configuração padrão:

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>3</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Os instantâneos são coletados apenas em exceções que são relatadas ao Application Insights. Em alguns casos (por exemplo, versões mais antigas da plataforma .NET), talvez seja necessário [configurar coleta de exceção](../../azure-monitor/app/asp-net-exceptions.md#exceptions) para ver exceções com instantâneos no portal.


## <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Configurar a coleta de instantâneo para aplicativos do Núcleo do ASP.NET 2.0

1. [Habilite o Application Insights no seu aplicativo web Núcleo do ASP.NET](../../azure-monitor/app/asp-net-core.md), se você ainda não tiver feito isso.

    > [!NOTE]
    > Verifique se o seu aplicativo faz referência à versão 2.1.1 ou mais recente do pacote Microsoft.ApplicationInsights.AspNetCore.

2. Inclua o pacote do NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) em seu aplicativo.

3. Modifique a classe `Startup` do seu aplicativo para adicionar e configurar o processador de telemetria do Coletor de Instantâneo.

    Adicione o seguinte usando as instruções para `Startup.cs`

   ```csharp
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   using Microsoft.ApplicationInsights.AspNetCore;
   using Microsoft.ApplicationInsights.Extensibility;
   ```

   Adicione a classe `SnapshotCollectorTelemetryProcessorFactory` a seguir para a classe `Startup`.

   ```csharp
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           private readonly IServiceProvider _serviceProvider;

           public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
               _serviceProvider = serviceProvider;

           public ITelemetryProcessor Create(ITelemetryProcessor next)
           {
               var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
               return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
           }
       }
       ...
    ```
    Adicione os serviços `SnapshotCollectorConfiguration` e `SnapshotCollectorTelemetryProcessorFactory` para o pipeline de inicialização:

    ```csharp
       // This method gets called by the runtime. Use this method to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           // Configure SnapshotCollector from application settings
           services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

           // Add SnapshotCollector telemetry processor.
           services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

           // TODO: Add other services your application needs here.
       }
   }
   ```

4. Se necessário, a configuração do depurador de instantâneo personalizada, adicionando uma seção SnapshotCollectorConfiguration ao appSettings. JSON. Todas as configurações na configuração do depurador de instantâneo são opcionais. Aqui está um exemplo que mostra uma configuração equivalente para a configuração padrão:

   ```json
   {
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

## <a name="configure-snapshot-collection-for-other-net-applications"></a>Configurar a coleta de instantâneo para outros aplicativos .NET

1. Se seu aplicativo ainda não está instrumentado com o Application Insights, comece [habilitando o Application Insights e definindo a chave de instrumentação](../../azure-monitor/app/windows-desktop.md).

2. Adicione o pacote do NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) em seu aplicativo.

3. Os instantâneos são coletados apenas em exceções que são relatadas ao Application Insights. Talvez seja necessário modificar o código para relatá-los. O código de tratamento de exceção depende da estrutura do seu aplicativo, mas há um exemplo abaixo:
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```

## <a name="next-steps"></a>Próximas etapas

- Gere tráfego para seu aplicativo que pode disparar uma exceção. Em seguida, aguarde de 10 a 15 minutos para instantâneos a serem enviados para a instância do Application Insights.
- Ver [instantâneos](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json) no portal do Azure.
- Para obter ajuda com solução de problemas do Profiler, consulte [solução de problemas do depurador de instantâneo](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).
