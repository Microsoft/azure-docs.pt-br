---
title: Guia de início rápido – Monitoramento de aplicativos do Azure Spring Cloud com logs, métricas e rastreamento
description: Use o streaming de log, o Log Analytics, as métricas e o rastreamento para monitorar aplicativos de exemplo Piggymetrics no Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 75c50e6342a00ef37215d9e961503dd7ffb0d205
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878832"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>Início Rápido: Monitoramento de aplicativos do Azure Spring Cloud com logs, métricas e rastreamento

::: zone pivot="programming-language-csharp"
Com a funcionalidade de monitoramento integrado no Azure Spring Cloud, você pode depurar e monitorar problemas complexos. O Azure Spring Cloud integra o [rastreamento distribuído](https://steeltoe.io/docs/3/tracing/distributed-tracing) do Steeltoe ao [Application Insights](../azure-monitor/app/app-insights-overview.md) do Azure. Essa integração fornece uma funcionalidade avançada de logs, métricas e rastreamento distribuído no portal do Azure.

Os procedimentos a seguir explicam como usar o streaming de log, o Log Analytics, as métricas e o rastreamento distribuído com o aplicativo de exemplo que você implantou nos guias de início rápido anteriores.

## <a name="prerequisites"></a>Pré-requisitos

* Conclua os guias de início rápido anteriores nesta série:

  * [Provisionar o serviço Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md).
  * [Configurar o servidor de configuração do Azure Spring Cloud](spring-cloud-quickstart-setup-config-server.md).
  * [Criar e implantar aplicativos](spring-cloud-quickstart-deploy-apps.md).

## <a name="logs"></a>Logs

Há duas maneiras de ver os logs no Azure Spring Cloud: O **Streaming de Log** de logs em tempo real por instância de aplicativo ou o **Log Analytics** para logs agregados com capacidade de consulta avançada.

### <a name="log-streaming"></a>Streaming de log

Você pode usar o streaming de log na CLI do Azure com o comando a seguir.

```azurecli
az spring-cloud app logs -n solar-system-weather -f
```

Você verá uma saída semelhante ao exemplo a seguir:

```output
=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing action method Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather) - Validation state: Valid
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController[0]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Retrieved weather data from 4 planets
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing ObjectResult, writing value of type 'System.Collections.Generic.KeyValuePair`2[[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e],[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e]][]'.
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
```

> [!TIP]
> Use `az spring-cloud app logs -h` para explorar mais parâmetros e funcionalidade de fluxo de log.

### <a name="log-analytics"></a>Log Analytics

1. No portal do Azure, acesse a página **serviço | Visão geral** e selecione **Logs** na seção **Monitoramento**. Selecione **Executar** em uma das consultas de exemplo do Azure Spring Cloud.

   [ ![Entrada do Logs Analytics](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Edite a consulta para remover as cláusulas Where que limitam a exibição a logs de aviso e de erro.

1. Em seguida, selecione `Run` e você verá logs. Confira os [documentos do Azure Log Analytics](../azure-monitor/logs/get-started-queries.md) para obter mais diretrizes sobre como escrever consultas.

   [ ![Consulta do Log Analytics – Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png#lightbox)

## <a name="metrics"></a>Métricas

1. No portal do Azure, acesse a página **serviço | Visão geral** e selecione **Métricas** na seção **Monitoramento**. Adicione sua primeira métrica selecionando uma das métricas do .NET em **Desempenho (.NET)** ou **Solicitação (.NET)** na lista suspensa **Métrica** e `Avg` para **Agregação** para ver a linha do tempo para essa métrica.

   [ ![Entrada de métricas – Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png#lightbox)
    
1. Clique em **Adicionar filtro** na barra de ferramentas, selecione `App=solar-system-weather` para ver o uso da CPU somente para o aplicativo **solar-system-weather**.

   [ ![Usar filtro em métricas – Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png#lightbox)

1. Ignore o filtro criado na etapa anterior, selecione **Aplicar Divisão** e selecione `App` para **Valores** para ver o uso da CPU por aplicativos diferentes.

   [ ![Aplicar divisão em métricas – Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png#lightbox)

## <a name="distributed-tracing"></a>Rastreamento distribuído

1. No portal do Azure, acesse a página **serviço | Visão geral** e selecione **Rastreamento distribuído** na seção **Monitoramento**. Em seguida, selecione a guia **Exibir o mapa do aplicativo** à direita.

   [ ![Entrada de rastreamento distribuído – Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Agora você pode ver o status de chamadas entre aplicativos. 

   [ ![Visão geral do rastreamento distribuído – Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png#lightbox)
    
1. Selecione o link entre **solar-system-weather** e **planet-weather-provider** para ver mais detalhes, como chamadas mais lentas por métodos HTTP.

   [ ![Rastreamento distribuído – Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png#lightbox)
    
1. Por fim, selecione **Investigar Desempenho** para explorar uma análise de desempenho interna mais avançada.

   [ ![Desempenho de rastreamento distribuído – Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
Com a funcionalidade de monitoramento integrado no Azure Spring Cloud, você pode depurar e monitorar problemas complexos. O Azure Spring Cloud integra o [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) ao [Application Insights](../azure-monitor/app/app-insights-overview.md) do Azure. Essa integração fornece uma funcionalidade avançada de logs, métricas e rastreamento distribuído no portal do Azure. Os procedimentos a seguir explicam como usar o Streaming de Log, o Log Analytics, as Métricas e o rastreamento distribuído com aplicativos PiggyMetrics implantados.

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas anteriores: 

* [Provisionar uma instância do Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md)
* [Configurar o Config Server](spring-cloud-quickstart-setup-config-server.md)
* [Criar e implantar aplicativos](spring-cloud-quickstart-deploy-apps.md).

## <a name="logs"></a>Logs

Há duas maneiras de ver os logs no Azure Spring Cloud: O **Streaming de Log** de logs em tempo real por instância de aplicativo ou o **Log Analytics** para logs agregados com capacidade de consulta avançada.

### <a name="log-streaming"></a>Streaming de log

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Você pode usar o streaming de log na CLI do Azure com o comando a seguir.

```azurecli
az spring-cloud app logs -s <service instance name> -g <resource group name> -n gateway -f
```

Você verá logs como este:

[ ![Streaming de Log da CLI do Azure](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

> [!TIP]
> Use `az spring-cloud app logs -h` para explorar mais parâmetros e funcionalidades de fluxo de log.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Para obter os logs usando o Azure Toolkit for IntelliJ:

1. Selecione **Azure Explorer** e, em seguida, **Spring Cloud**.

1. Clique com o botão direito do mouse no aplicativo em execução.

1. Selecione **Logs de Streaming** na lista suspensa.

   ![Selecionar os logs de streaming](media/spring-cloud-intellij-howto/streaming-logs.png)
    
1. Selecione a **Instância**.

   ![Selecionar instância](media/spring-cloud-intellij-howto/select-instance.png)
    
1. O log de streaming ficará visível na janela de saída.

   ![Saída do log de streaming](media/spring-cloud-intellij-howto/streaming-log-output.png)

---
### <a name="log-analytics"></a>Log Analytics

1. Acesse a página **serviço | Visão geral** e selecione **Logs** na seção **Monitoramento**. Clique em **Executar** em uma das consultas de exemplo do Azure Spring Cloud. 

   [ ![Entrada do Logs Analytics](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Em seguida, você verá logs filtrados. Confira os [documentos do Azure Log Analytics](../azure-monitor/logs/get-started-queries.md) para obter mais diretrizes sobre como escrever consultas.

   [ ![Consulta do Logs Analytics](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png#lightbox)

## <a name="metrics"></a>Métricas

1. Acesse a página **serviço | Visão geral** e selecione **Métricas** na seção **Monitoramento**. Adicione a sua primeira métrica selecionando `system.cpu.usage` para **Métrica** e `Avg` para **Agregação** para ver a linha do tempo do uso geral da CPU.

   [ ![Entrada de métricas](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png#lightbox)
    
1. Clique em **Adicionar filtro** na barra de ferramentas acima, selecione `App=Gateway` para ver o uso da CPU somente para o aplicativo de **gateway**.

   [ ![Usar filtro em métricas](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png#lightbox)

1. Ignore o filtro criado acima, clique em **Aplicar Divisão** e selecione `App` para **Valores** para ver o uso da CPU por aplicativos diferentes.

   [ ![Aplicar divisão em métricas](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png#lightbox)

## <a name="distributed-tracing"></a>Rastreamento distribuído

1. Acesse a página **serviço | Visão geral** e selecione **Rastreamento distribuído** na seção **Monitoramento**. Em seguida, clique na guia **Exibir o mapa do aplicativo** à direita.

   [ ![Entrada do Rastreamento Distribuído](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Agora você pode ver o status de chamadas entre os aplicativos Piggymetrics. 

   [ ![Visão geral do rastreamento distribuído](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png#lightbox)
    
1. Clique no link entre **gateway** e **account-service** para ver mais detalhes, como chamadas mais lentas por métodos HTTP.

   [ ![Rastreamento distribuído](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png#lightbox)
    
1. Por fim, clique em **Investigar Desempenho** para explorar uma análise de desempenho interna mais avançada.

   [ ![Desempenho de rastreamento distribuído](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png#lightbox)

::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Nesses guias de início rápido, você criou recursos do Azure que continuarão acumulando encargos se permanecerem em sua assinatura. Se você acredita que não precisará desses recursos no futuro, exclua o grupo de recursos usando o portal ou executando o seguinte comando no Cloud Shell:

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Em um início rápido anterior, você também definiu o nome do grupo de recursos padrão. Se você não pretende avançar para o próximo início rápido, desmarque esse padrão executando o seguinte comando da CLI:

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Próximas etapas

Para explorar mais recursos de monitoramento do Azure Spring Cloud, confira:

> [!div class="nextstepaction"]
> [Serviços de diagnóstico](diagnostic-services.md)
>
> [Rastreamento distribuído](spring-cloud-howto-distributed-tracing.md)
>
> [Transmitir logs em tempo real](spring-cloud-howto-log-streaming.md)
