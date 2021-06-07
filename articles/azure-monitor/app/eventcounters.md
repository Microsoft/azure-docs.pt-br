---
title: Contadores de eventos no Application Insights | Microsoft Docs
description: Monitore o sistema e os EventCounters do .NET/.NET Core personalizados no Application Insights.
ms.topic: conceptual
ms.date: 09/20/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: d1ae0937c25a68798acd87fe8b2a0a54aa765b35
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579537"
---
# <a name="eventcounters-introduction"></a>Introdução ao EventCounters

[`EventCounter`](/dotnet/core/diagnostics/event-counters) é o mecanismo .NET/.NET Core para publicar e consumir contadores ou estatísticas. Os EventCounters têm suporte em todas as plataformas de sistema operacional: Windows, Linux e macOS. Pode ser pensado como um equivalente multiplataforma para o [PerformanceCounters](/dotnet/api/system.diagnostics.performancecounter) que só tem suporte em sistemas Windows.

Embora os usuários possam publicar qualquer personalizado `EventCounters` para atender às suas necessidades, o .NET Core 3,0 e o tempo de execução mais recente publica um conjunto desses contadores por padrão. Este documento abordará as etapas necessárias para coletar e exibir `EventCounters` (definido pelo sistema ou definido pelo usuário) no insights aplicativo Azure.

## <a name="using-application-insights-to-collect-eventcounters"></a>Usar o Application Insights para coletar EventCounters

Application Insights dá suporte à coleta `EventCounters` com seu `EventCounterCollectionModule` , que faz parte do pacote NuGet recentemente lançado [Microsoft. ApplicationInsights. EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector). `EventCounterCollectionModule` é habilitado automaticamente ao usar [AspNetCore](asp-net-core.md) ou [WorkerService](worker-service.md). `EventCounterCollectionModule` coleta contadores com uma frequência de coleta não configurável de 60 segundos. Não há permissões especiais necessárias para coletar EventCounters.

## <a name="default-counters-collected"></a>Contadores padrão coletados

Começando com a versão 2.15.0 do SDK do [AspNetCore](asp-net-core.md) ou do [SDK do WorkerService](worker-service.md), nenhum contador é coletado por padrão. O próprio módulo está habilitado, para que os usuários possam simplesmente adicionar os contadores desejados para coletá-los.

Para obter uma lista de contadores conhecidos publicados pelo tempo de execução do .NET, consulte documento de [contadores disponíveis](/dotnet/core/diagnostics/event-counters#available-counters) .

## <a name="customizing-counters-to-be-collected"></a>Personalizar os contadores a serem coletados

O exemplo a seguir mostra como adicionar/remover contadores. Essa personalização seria feita no método `ConfigureServices` do seu aplicativo depois que a coleção de telemetria do Application Insights ser habilitada usando `AddApplicationInsightsTelemetry()` ou `AddApplicationInsightsWorkerService()`. Veja a seguir um exemplo de código de um aplicativo ASP.NET Core. Para outros tipos de aplicativos, confira [este](worker-service.md#configuring-or-removing-default-telemetrymodules) documento.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows how to configure the module to collect
        // additional counters.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters, if any.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );
    }
```

## <a name="disabling-eventcounter-collection-module"></a>Desabilitando o módulo de coleta EventCounter

`EventCounterCollectionModule` pode ser desabilitado usando o `ApplicationInsightsServiceOptions` . Um exemplo ao usar o SDK do ASP.NET Core é mostrado abaixo.

```csharp
    using Microsoft.ApplicationInsights.AspNetCore.Extensions;
    using Microsoft.Extensions.DependencyInjection;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
        applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
        services.AddApplicationInsightsTelemetry(applicationInsightsServiceOptions);
    }
```

Uma abordagem semelhante também pode ser usada para o SDK do WorkerService, mas o namespace deve ser alterado, conforme mostrado no exemplo abaixo.

```csharp
    using Microsoft.ApplicationInsights.WorkerService;
    using Microsoft.Extensions.DependencyInjection;

    var applicationInsightsServiceOptions = new ApplicationInsightsServiceOptions();
    applicationInsightsServiceOptions.EnableEventCounterCollectionModule = false;
    services.AddApplicationInsightsTelemetryWorkerService(applicationInsightsServiceOptions);
```

## <a name="event-counters-in-metric-explorer"></a>Contadores de eventos no Metric Explorer

Para exibir as métricas do EventCounter no [Metric Explorer](../essentials/metrics-charts.md), selecione o recurso Application Insights e escolha Métricas baseadas em log como namespace da métrica. Em seguida, as métricas EventCounter são exibidas em Categoria personalizada.

> [!div class="mx-imgBorder"]
> ![Contadores de eventos relatados no Gerenciador de métricas Application Insights](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Contadores de evento no Analytics

Você também pode pesquisar e exibir relatórios de contador de eventos no [Analytics](../logs/log-query-overview.md), na tabela **customMetrics**.

Por exemplo, execute a consulta a seguir para ver quais contadores são coletados e estão disponíveis para consulta:

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Contadores de eventos relatados na análise de Application Insights](./media/event-counters/analytics-event-counters.png)

Para obter um gráfico de um contador específico (por exemplo: `ThreadPool Completed Work Item Count`) no período recente, execute a consulta a seguir.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Chat de um único contador no Application Insights](./media/event-counters/analytics-completeditems-counters.png)

Como outras telemetrias, o **customMetrics** também tem uma coluna `cloud_RoleInstance` que indica a identidade da instância do servidor host no qual seu aplicativo está sendo executado. A consulta acima mostra o valor do contador por instância e pode ser usada para comparar o desempenho de diferentes instâncias de servidor.

## <a name="alerts"></a>Alertas
Assim como ocorre com outras métricas, você pode [definir um alerta](../alerts/alerts-log.md) para avisar se um contador de evento fica fora de um limite especificado. Abra o painel Alertas e clique em Adicionar Alerta.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="can-i-see-eventcounters-in-live-metrics"></a>Posso ver o EventCounters no Live Metrics?

O Live Metrics não mostra o EventCounters a partir de hoje. Use o Metric Explorer ou o Analytics para ver a telemetria.

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Habilitei o Application Insights no portal de aplicativo Web do Azure. Mas não consigo ver o EventCounters?

 A [extensão do Application Insights](./azure-web-apps.md) para ASP.NET Core ainda não é compatível com esse recurso. Este documento será atualizado quando esse recurso for compatível.

## <a name="next-steps"></a><a name="next"></a>Próximas etapas

* [Acompanhamento de dependência](./asp-net-dependencies.md)

