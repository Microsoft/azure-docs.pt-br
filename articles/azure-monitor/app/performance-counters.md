---
title: Contadores de desempenho no Application Insights | Microsoft Docs
description: Monitore o sistema e contadores de desempenho .NET personalizados no Application Insights.
ms.topic: conceptual
ms.date: 12/13/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 423e17ef2b44286c28b464836075284929d8644c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031354"
---
# <a name="system-performance-counters-in-application-insights"></a>Contadores de desempenho do sistema no Application Insights

O Windows fornece uma ampla variedade de [contadores de desempenho](/windows/desktop/perfctrs/about-performance-counters) como ocupação da CPU, memória, disco e uso da rede. Também é possível definir contadores de desempenho próprios. Há suporte para coleção de contadores de desempenho contanto que seu aplicativo esteja em execução no IIS em um host local ou em uma máquina virtual à qual você tem acesso administrativo. Embora os aplicativos executados como aplicativos Web do Azure não tenham acesso direto aos contadores de desempenho, um subconjunto de contadores disponíveis é coletado pelo Application Insights.

## <a name="view-counters"></a>Visualizar contadores

O painel Métricas mostra o conjunto padrão de contadores de desempenho.

![Contadores de desempenho reportados no Application Insights](./media/performance-counters/performance-counters.png)

Os contadores padrão atuais que estão configurados para serem coletados para aplicativos Web ASP.NET são:
- % de processo\\Tempo do processador
- % de processo\\Tempo do processador normalizado
- Memória\\Bytes disponíveis
- Solicitações de ASP.NET/segundo
- Exceções .NET CLR lançadas/segundo
- Tempo de execução de solicitação de aplicativos ASP.NET
- Processo\\Bytes particulares
- Processo\\Bytes de dados de E/S/segundo
- Aplicativos ASP.NET\\Fila de solicitações em aplicativos
- \Processador(_Total)\\% de tempo do processador

Os contadores padrão atuais que estão configurados para serem coletados para ASP.NET Core aplicativos Web são:
- % de processo\\Tempo do processador
- % de processo\\Tempo do processador normalizado
- Memória\\Bytes disponíveis
- Processo\\Bytes particulares
- Processo\\Bytes de dados de E/S/segundo
- \Processador(_Total)\\% de tempo do processador

## <a name="add-counters"></a>Adicionar contadores

Se o contador de desempenho desejado não estiver incluído na lista de métricas, você poderá adicioná-lo.

1. Descubra quais contadores estão disponíveis no servidor usando este comando do PowerShell no servidor local:

    `Get-Counter -ListSet *`

    (Consulte [`Get-Counter`](/powershell/module/microsoft.powershell.diagnostics/get-counter).)
2. Abra o ApplicationInsights.config.

   * Se você adicionou o Application Insights ao seu aplicativo durante o desenvolvimento, edite ApplicationInsights.config no projeto e implante-o novamente nos servidores.
3. Edite a diretiva do coletor de desempenho:

    ```XML

        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <Counters>
            <Add PerformanceCounter="\Objects\Processes"/>
            <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
          </Counters>
        </Add>
    ```

> [!NOTE]
> Os aplicativos ASP.NET Core não têm `ApplicationInsights.config` e, portanto, o método acima não é válido para aplicativos ASP.NET Core.

É possível capturar os contadores padrão e os que você implementou sozinho. `\Objects\Processes` é um exemplo de um contador padrão, disponível em todos os sistemas Windows. `\Sales(photo)\# Items Sold` é um exemplo de um contador personalizado que pode ser implementado em um serviço Web.

O formato é `\Category(instance)\Counter"`, ou apenas `\Category\Counter` para categorias que não têm instâncias.

`ReportAs` é necessário para os nomes de contador que não correspondem a `[a-zA-Z()/-_ \.]+` - isto é, eles contêm caracteres que não estão nos seguintes conjuntos: letras, colchetes, barra invertida, hífen, sublinhado, espaço, ponto.

Se você especificar uma instância, ela será coletada como uma dimensão "CounterInstanceName" da métrica reportada.

### <a name="collecting-performance-counters-in-code-for-aspnet-web-applications-or-netnet-core-console-applications"></a>Coletar contadores de desempenho em código para aplicativos Web ASP.NET ou aplicativos em console .NET/.NET Core
Para coletar contadores de desempenho do sistema e enviá-los ao Application Insights, você pode adaptar o snippet a seguir:


```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Process([replace-with-application-process-name])\Page Faults/sec", "PageFaultsPerfSec"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

Ou você pode fazer a mesma coisa com métricas personalizadas que você criou:

```csharp
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

### <a name="collecting-performance-counters-in-code-for-aspnet-core-web-applications"></a>Coletar contadores de desempenho em código para aplicativos Web ASP.NET Core

Modifique o método `ConfigureServices` na sua classe `Startup.cs`, conforme mostrado abaixo.

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures PerformanceCollectorModule.
  services.ConfigureTelemetryModule<PerformanceCollectorModule>((module, o) =>
            {
                // the application process name could be "dotnet" for ASP.NET Core self-hosted applications.
                module.Counters.Add(new PerformanceCounterCollectionRequest(
    @"\Process([replace-with-application-process-name])\Page Faults/sec", "DotnetPageFaultsPerfSec"));
            });
    }
```

## <a name="performance-counters-in-analytics"></a>Contadores de desempenho no Analytics
Você pode pesquisar e exibir relatórios do contador de desempenho no [Analytics](../logs/log-query-overview.md).

O esquema **performanceCounters** expõe o nome `category`, `counter` e o nome `instance` de cada contador de desempenho.  Na telemetria de cada aplicativo, você verá apenas os contadores para aquele aplicativo. Por exemplo, para ver quais contadores estão disponíveis: 

![Contadores de desempenho na análise do Application Insights](./media/performance-counters/analytics-performance-counters.png)

(Aqui, 'Instance' se refere à instância do contador de desempenho, e não à instância da máquina do servidor ou da função. O nome da instância do contador de desempenho normalmente segmenta os contadores, como tempo de processador, pelo nome do processo ou aplicativo.)

Para obter um gráfico da memória disponível no período recente: 

![Gráfico de tempo da memória na análise do Application Insights](./media/performance-counters/analytics-available-memory.png)

Como outras telemetrias, o **performanceCounters** também tem uma coluna `cloud_RoleInstance` que indica a identidade da instância do servidor host no qual seu aplicativo está sendo executado. Por exemplo, para comparar o desempenho do seu aplicativo em diferentes computadores: 

![Desempenho segmentado por instância de função na análise do Application Insights](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>Contadores do ASP.NET e do Application Insights

*Qual é a diferença entre a Taxa de exceções e as Métricas de exceções?*

* *Taxa de exceções* é um contador de desempenho do sistema. O CLR conta todas as exceções tratadas e sem tratamento que são lançadas, e divide o total em um intervalo de amostragem pela duração do intervalo. O SDK do Application Insights coleta esse resultado e o envia para o portal.

* *Exceções* é uma contagem dos relatórios TrackException recebida pelo portal no intervalo de amostragem do gráfico. Ele inclui apenas as exceções tratadas em que você tenha gravado chamadas TrackException em seu código e não inclui todas as [exceções sem tratamento](./asp-net-exceptions.md). 

## <a name="performance-counters-for-applications-running-in-azure-web-apps"></a>Contadores de desempenho para aplicativos sendo executados em aplicativos Web do Azure

Os aplicativos ASP.NET e ASP.NET Core implantados nos aplicativos Web do Azure são executados em um ambiente de área restrita especial. Esse ambiente não permite acesso direto aos contadores de desempenho do sistema. No entanto, um subconjunto limitado de contadores é exposto como variáveis de ambiente, conforme descrito [aqui](https://github.com/projectkudu/kudu/wiki/Perf-Counters-exposed-as-environment-variables). O SDK do Application Insights para ASP.NET e ASP.NET Core coleta contadores de desempenho de aplicativos Web do Azure a partir dessas variáveis de ambiente especiais. Só um subconjunto de contadores está disponível nesse ambiente, e a lista completa pode ser encontrada [aqui.](https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/PerformanceCollector/Perf.Shared/Implementation/WebAppPerformanceCollector/CounterFactory.cs)

## <a name="performance-counters-in-aspnet-core-applications"></a>Contadores de desempenho em aplicativos do ASP.Net Core

O suporte para contadores de desempenho no ASP.Net Core é limitado:

* As versões 2.4.1 e posteriores do [SDK](https://nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) coletarão contadores de desempenho se o aplicativo estiver sendo executado em aplicativos Web do Azure (Windows).
* As versões 2.7.1 e posteriores do SDK coletarão contadores de desempenho se o aplicativo estiver sendo executado no Windows e se destinar a `NETSTANDARD2.0` ou posterior.
* Para aplicativos destinados a .NET Framework, todas as versões do SDK dão suporte a contadores de desempenho.
* As versões 2.8.0 e posteriores do SDK dão suporte ao contador de CPU/memória no Linux. Nenhum outro contador tem suporte no Linux. A maneira recomendada para obter contadores do sistema no Linux (e em outros ambientes não Windows) é usando [EventCounters](eventcounters.md)

## <a name="alerts"></a>Alertas
Assim como ocorre com outras métricas, você pode [definir um alerta](../alerts/alerts-log.md) para avisar se um contador de desempenho fica fora de um limite especificado. Abra o painel Alertas e clique em Adicionar Alerta.

## <a name="next-steps"></a><a name="next"></a>Próximas etapas

* [Acompanhamento de dependência](./asp-net-dependencies.md)
* [Acompanhamento de exceções](./asp-net-exceptions.md)

