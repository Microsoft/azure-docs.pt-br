---
title: Filtrar a telemetria do Application Insights do Azure no aplicativo Web Java | Microsoft Docs
description: Reduza o tráfego da telemetria filtrando os eventos que você não precisa monitorar.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/23/2016
ms.author: mbullwin
ms.openlocfilehash: ee50a0e9c7fca8f01f12b3508c86d901b5315120
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60897107"
---
# <a name="filter-telemetry-in-your-java-web-app"></a>Filtrar a telemetria no aplicativo Web Java

Os filtros fornecem uma maneira de selecionar a telemetria que seu [aplicativo Web Java envia ao Application Insights](java-get-started.md). Existem alguns filtros prontos para uso, mas você também pode escrever seus próprios filtros personalizados.

Os filtros prontos para uso incluem:

* Nível de severidade de rastreamento
* URLs específicas, palavras-chave ou códigos de resposta
* Respostas rápidas, isto é, solicitações às quais seu aplicativo respondeu rapidamente
* Nomes de eventos específicos

> [!NOTE]
> Os filtros distorcem as métricas do aplicativo. Por exemplo, você pode decidir que, para diagnosticar respostas lentas, vai definir um filtro para descartar tempos rápidos de resposta. Mas você deve estar ciente de que a média dos tempos de resposta relatada pelo Application Insights será mais lenta que a velocidade verdadeira e a contagem de solicitações será menor que a contagem real.
> Se isso for um problema, use [Amostragem](../../azure-monitor/app/sampling.md).

## <a name="setting-filters"></a>Definindo filtros

Em ApplicationInsights.xml, adicione uma seção `TelemetryProcessors`, como neste exemplo:


```XML

    <ApplicationInsights>
      <TelemetryProcessors>

        <BuiltInProcessors>
           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="100"/>
                  <Add name="NotNeededResponseCodes" value="200-400"/>
           </Processor>

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="100"/>
                  <Add name="NotNeededNames" value="home,index"/>
                  <Add name="NotNeededUrls" value=".jpg,.css"/>
           </Processor>

           <Processor type="TelemetryEventFilter">
                  <!-- Names of events we don't want to see -->
                  <Add name="NotNeededNames" value="Start,Stop,Pause"/>
           </Processor>

           <!-- Exclude telemetry from availability tests and bots -->
           <Processor type="SyntheticSourceFilter">
                <!-- Optional: specify which synthetic sources,
                     comma-separated
                     - default is all synthetics -->
                <Add name="NotNeededSources" value="Application Insights Availability Monitoring,BingPreview"
           </Processor>

        </BuiltInProcessors>

        <CustomProcessors>
          <Processor type="com.fabrikam.MyFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>

      </TelemetryProcessors>
    </ApplicationInsights>

```




[Inspecione o conjunto completo de processadores internos](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal/processor).

## <a name="built-in-filters"></a>Filtros internos

### <a name="metric-telemetry-filter"></a>Filtro de telemetria da métrica

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded` — lista de nomes de métricas personalizadas separados por vírgula.


### <a name="page-view-telemetry-filter"></a>Filtro de telemetria da exibição de página

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS` — duração refere-se ao tempo levado para carregar a página. Se for definida, as páginas que são carregadas mais rapidamente que o tempo definido não serão apontadas.
* `NotNeededNames` — lista de nomes de página separados por vírgula.
* `NotNeededUrls` — lista de fragmento de URL separados por vírgula. Por exemplo, `"home"` filtra todas as páginas que têm "início" na URL.


### <a name="request-telemetry-filter"></a>Filtro de telemetria da solicitação


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Filtro de fonte sintética

Filtra toda a telemetria que têm valores na propriedade SyntheticSource. Isso inclui solicitações de bots, rastreadores e testes de disponibilidade.

Filtre a telemetria para todas as solicitações sintéticas:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Filtre a telemetria para fontes sintéticas específicas:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded` — lista de nomes de fonte sintética separados por vírgula.

### <a name="telemetry-event-filter"></a>Filtro de eventos de telemetria

Filtra eventos personalizados (registrados usando [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)).


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames` — lista de nomes de eventos separados por vírgula.


### <a name="trace-telemetry-filter"></a>Filtro de telemetria de rastreamento

Filtra rastreamentos de log (registrados usando [TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) ou um [coletor de estrutura de registros](java-trace-logs.md)).

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* Os valores válidos de `FromSeverityLevel`são:
  *  DESATIVADO             — filtrar TODOS os rastreamentos
  *  RASTREAMENTO           — sem filtragem. igual a Nível de rastreamento
  *  INFORMAÇÕES            — filtrar nível de RASTREAMENTO
  *  AVISO            — filtrar RASTREAMENTO e INFORMAÇÕES
  *  ERRO           — filtrar AVISO, INFORMAÇÕES, RASTREAMENTO
  *  CRÍTICO        — filtrar todos, menos CRÍTICO


## <a name="custom-filters"></a>Filtros personalizados

### <a name="1-code-your-filter"></a>1. Codificar seu filtro

No seu código, crie uma classe que implementa `TelemetryProcessor`:

```Java

    package com.fabrikam.MyFilter;
    import com.microsoft.applicationinsights.extensibility.TelemetryProcessor;
    import com.microsoft.applicationinsights.telemetry.Telemetry;

    public class SuccessFilter implements TelemetryProcessor {

       /* Any parameters that are required to support the filter.*/
       private final String successful;

       /* Initializers for the parameters, named "setParameterName" */
       public void setNotNeeded(String successful)
       {
          this.successful = successful;
       }

       /* This method is called for each item of telemetry to be sent.
          Return false to discard it.
          Return true to allow other processors to inspect it. */
       @Override
       public boolean process(Telemetry telemetry) {
        if (telemetry == null) { return true; }
        if (telemetry instanceof RequestTelemetry)
        {
            RequestTelemetry requestTelemetry = (RequestTelemetry)telemetry;
            return request.getSuccess() == successful;
        }
        return true;
       }
    }

```


### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. Invocar o filtro no arquivo de configuração

Em ApplicationInsights.xml:

```XML


    <ApplicationInsights>
      <TelemetryProcessors>
        <CustomProcessors>
          <Processor type="com.fabrikam.SuccessFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>
      </TelemetryProcessors>
    </ApplicationInsights>

```

### <a name="3-invoke-your-filter-java-spring"></a>3. Invocar o filtro (Java Spring)

Para aplicativos baseados no Spring framework, processadores de telemetria personalizada devem ser registrados em sua classe principal do aplicativo como um bean. Eles poderão então ser vinculada automaticamente quando o aplicativo é iniciado.

```Java
@Bean
public TelemetryProcessor successFilter() {
      return new SuccessFilter();
}
```

Você precisará criar seus próprios parâmetros de filtro no `application.properties` e aproveitar a estrutura de configuração externalizados do Spring Boot para passar esses parâmetros em seu filtro personalizado. 


## <a name="troubleshooting"></a>solução de problemas

*Meu filtro não está funcionando.*

* Verifique se você forneceu valores válidos de parâmetro. Por exemplo, durações devem ser números inteiros. Valores inválidos farão com que o filtro seja ignorado. Se seu filtro personalizado lançar uma exceção de um construtor ou método set, ele será ignorado.

## <a name="next-steps"></a>Próximas etapas

* [Amostragem](../../azure-monitor/app/sampling.md) — considere a amostragem como uma alternativa que não distorce suas métricas.
