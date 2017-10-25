---
title: Contadores de desempenho no Application Insights | Microsoft Docs
description: Monitore o sistema e contadores de desempenho .NET personalizados no Application Insights.
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: bwren
ms.openlocfilehash: 038d6e051be8112b9264e7efa6485965d11e32c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="system-performance-counters-in-application-insights"></a>Contadores de desempenho do sistema no Application Insights
O Windows fornece uma ampla variedade de [contadores de desempenho](http://www.codeproject.com/Articles/8590/An-Introduction-To-Performance-Counters) como ocupação da CPU, memória, disco e uso da rede. Você também pode definir seus próprios. O [Application Insights](app-insights-overview.md) pode mostrar esses contadores de desempenho se o seu aplicativo estiver em execução no IIS em um host local ou máquina virtual a qual você tem acesso administrativo. Os gráficos indicam os recursos disponíveis para seu aplicativo ativo e podem ajudar a identificar uma carga sem balanceamento entre instâncias do servidor.

Os contadores de desempenho aparecem na folha Servidores, que inclui uma tabela que segmenta pela instância do servidor.

![Contadores de desempenho reportados no Application Insights](./media/app-insights-performance-counters/counters-by-server-instance.png)

(Os contadores de desempenho não estão disponíveis para aplicativos Web do Azure. Mas você pode [enviar o Diagnóstico do Azure para o Application Insights](app-insights-azure-diagnostics.md).)

## <a name="view-counters"></a>Visualizar contadores
A folha Servidores mostra um conjunto padrão de contadores de desempenho. 

Para ver outros contadores, edite os gráficos na folha Servidores, ou abra uma nova folha [Metrics Explorer](app-insights-metrics-explorer.md) e adicione novos gráficos. 

Os contadores disponíveis são listados como métricas quando você edita um gráfico.

![Contadores de desempenho reportados no Application Insights](./media/app-insights-performance-counters/choose-performance-counters.png)

Para ver todos os gráficos mais úteis em um único lugar, crie um [painel](app-insights-dashboards.md) e fixe-os a ele.

## <a name="add-counters"></a>Adicionar contadores
Se o contador de desempenho desejado não for mostrado na lista de métricas, é porque o SDK do Application Insights não está coletando em seu servidor Web. Você pode configurá-lo para fazer isso.

1. Descubra quais contadores estão disponíveis em seu servidor usando este comando do PowerShell no servidor:
   
    `Get-Counter -ListSet *`
   
    (Consulte [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx).)
2. Abra o ApplicationInsights.config.
   
   * Se você adicionou o Application Insights ao seu aplicativo durante o desenvolvimento, edite applicationinsights.config em seu projeto e implante-o novamente em seus servidores.
   * Se você usou o Status Monitor para instrumentar um aplicativo Web em tempo de execução, localize applicationinsights.config no diretório raiz do aplicativo no IIS. Atualize-o em cada instância de servidor.
3. Edite a diretiva do coletor de desempenho:
   
```XML
   
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

Você pode capturar os contadores padrão e aqueles implementados por conta própria. `\Objects\Processes` é um exemplo de um contador padrão, disponível em todos os sistemas Windows. `\Sales(photo)\# Items Sold` é um exemplo de um contador personalizado que pode ser implementado em um serviço Web. 

O formato é `\Category(instance)\Counter"`, ou apenas `\Category\Counter` para categorias que não têm instâncias.

`ReportAs` é necessário para os nomes de contador que não correspondem a `[a-zA-Z()/-_ \.]+` - isto é, eles contêm caracteres que não estão nos seguintes conjuntos: letras, colchetes, barra invertida, hífen, sublinhado, espaço, ponto.

Se você especificar uma instância, ela será coletada como uma dimensão "CounterInstanceName" da métrica reportada.

### <a name="collecting-performance-counters-in-code"></a>Coletando contadores de desempenho no código
Para coletar contadores de desempenho do sistema e enviá-los ao Application Insights, você pode adaptar o trecho a seguir:


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\.NET CLR Memory([replace-with-application-process-name])\# GC Handles", "GC Handles")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

Ou você pode fazer a mesma coisa com métricas personalizadas que você criou:

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

## <a name="performance-counters-in-analytics"></a>Contadores de desempenho no Analytics
Você pode pesquisar e exibir relatórios do contador de desempenho no [Analytics](app-insights-analytics.md).

O esquema **performanceCounters** expõe o nome `category`, `counter` e o nome `instance` de cada contador de desempenho.  Na telemetria de cada aplicativo, você verá apenas os contadores para aquele aplicativo. Por exemplo, para ver quais contadores estão disponíveis: 

![Contadores de desempenho na análise do Application Insights](./media/app-insights-performance-counters/analytics-performance-counters.png)

(Aqui 'Instance' refere-se á instância do contador de desempenho, não à instância do servidor ou função. O nome da instância do contador de desempenho normalmente segmenta os contadores, como tempo de processador, pelo nome do processo ou aplicativo.)

Para obter um gráfico da memória disponível no período recente: 

![Gráfico de tempo da memória na análise do Application Insights](./media/app-insights-performance-counters/analytics-available-memory.png)

Como outras telemetrias, o **performanceCounters** também tem uma coluna `cloud_RoleInstance` que indica a identidade da instância do servidor host no qual seu aplicativo está sendo executado. Por exemplo, para comparar o desempenho do seu aplicativo em diferentes computadores: 

![Desempenho segmentado por instância de função na análise do Application Insights](./media/app-insights-performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>Contadores do ASP.NET e do Application Insights
*Qual é a diferença entre a Taxa de exceções e as Métricas de exceções?*

* *Taxa de exceções* é um contador de desempenho do sistema. O CLR conta todas as exceções tratadas e sem tratamento que são lançadas, e divide o total em um intervalo de amostragem pela duração do intervalo. O SDK do Application Insights coleta esse resultado e o envia para o portal.
* *Exceções* é uma contagem dos relatórios TrackException recebida pelo portal no intervalo de amostragem do gráfico. Ele inclui apenas as exceções tratadas em que você tenha gravado chamadas TrackException em seu código e não inclui todas as [exceções sem tratamento](app-insights-asp-net-exceptions.md). 

## <a name="alerts"></a>Alertas
Assim como ocorre com outras métricas, você pode [definir um alerta](app-insights-alerts.md) para avisar se um contador de desempenho fica fora de um limite especificado. Abra a folha Alertas e clique em Adicionar Alerta.

## <a name="next"></a>Próximas etapas
* [Acompanhamento de dependência](app-insights-asp-net-dependencies.md)
* [Acompanhamento de exceções](app-insights-asp-net-exceptions.md)

