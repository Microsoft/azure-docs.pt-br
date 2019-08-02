---
title: API do Application Insights para métricas e eventos personalizados | Microsoft Docs
description: Insira algumas linhas de código em seu aplicativo da área de trabalho ou de dispositivo, página da Web ou serviço para acompanhar o uso e diagnosticar problemas.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: mbullwin
ms.openlocfilehash: 2ec3b620138c4ae0487c29e38062c044a5210572
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314800"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>API do Application Insights para métricas e eventos personalizados

Insira algumas linhas de código em seu aplicativo para descobrir o que os usuários estão fazendo com ele ou para ajudar a diagnosticar problemas. Você pode enviar telemetria de aplicativos da área de trabalho e de dispositivo, clientes Web e servidores Web. Use a API de telemetria de núcleo do [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) para enviar eventos e métricas personalizados, bem como suas próprias versões de telemetria padrão. Essa API é a mesma API usada pelos coletores de dados padrão do Application Insights.

> [!NOTE]
> `TrackMetric()` não é mais o método preferencial de envio de métricas personalizadas para aplicativos com base no .NET. Na [versão 2.60-beta 3](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/CHANGELOG.md#version-260-beta3) do SDK do .NET do Application Insights, foi introduzido o novo método [`TelemetryClient.GetMetric()`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.getmetric?view=azure-dotnet). Começando com a [versão 2.72](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.getmetric?view=azure-dotnet) do SDK do .NET do Application Insights, essa funcionalidade passou a fazer parte da versão estável.

## <a name="api-summary"></a>Resumo da API

A API principal é uniforme em todas as plataformas, além de algumas variações como `GetMetric` (somente .NET).

| Método | Usado para |
| --- | --- |
| [`TrackPageView`](#page-views) |Páginas, telas, folhas ou formulários. |
| [`TrackEvent`](#trackevent) |Ações de usuário e outros eventos. Usado para acompanhar o comportamento do usuário ou para monitorar o desempenho. |
| [`GetMetric`](#getmetric) |Métricas zeradas e multidimensionais, agregação configurada centralmente, apenas C#. |
| [`TrackMetric`](#trackmetric) |Medições de desempenho, como comprimentos de fila, não relacionadas a eventos específicos. |
| [`TrackException`](#trackexception) |Registrar em log as exceções para diagnóstico. Rastrear onde elas ocorrem em relação a outros eventos e examinar os rastreamentos de pilha. |
| [`TrackRequest`](#trackrequest) |Registrar em log a frequência e a duração das solicitações do servidor para análise de desempenho. |
| [`TrackTrace`](#tracktrace) |Mensagens de log de diagnóstico. Você também pode capturar logs de terceiros. |
| [`TrackDependency`](#trackdependency) |Registrar em log a duração e a frequência das chamadas para componentes externos dos quais seu aplicativo depende. |

Você pode [anexar propriedades e métricas](#properties) à maioria dessas chamadas de telemetria.

## <a name="prep"></a>Antes de começar

Se você ainda não tem uma referência no SDK do Application Insights:

* Adicione o SDK do Application Insights a seu projeto:

  * [Projeto do ASP.NET](../../azure-monitor/app/asp-net.md)
  * [ASP.NET Core projeto](../../azure-monitor/app/asp-net-core.md)
  * [Projeto Java](../../azure-monitor/app/java-get-started.md)
  * [Projeto do Node.js](../../azure-monitor/app/nodejs.md)
  * [JavaScript em cada página da Web](../../azure-monitor/app/javascript.md) 
* Em seu código de servidor Web ou dispositivo, inclua:

    *C#:* `using Microsoft.ApplicationInsights;`

    *Visual Basic:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

    *Node.js:* `var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Obter uma instância do TelemetryClient

Obter uma instância de `TelemetryClient` (exceto em JavaScript em páginas da Web):

*C#*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```

*Visual Basic*

```vb
Private Dim telemetry As New TelemetryClient
```

*Java*

```java
private TelemetryClient telemetry = new TelemetryClient();
``` 

*Node.js*

```javascript
var telemetry = applicationInsights.defaultClient;
```

TelemetryClient é thread-safe.

Para projetos ASP.NET e Java, as solicitações HTTP de entrada são automaticamente capturadas. Você talvez queira criar instâncias adicionais do TelemetryClient para outro módulo do seu aplicativo. Por exemplo, você pode ter uma instância de TelemetryClient em sua classe de middleware para relatar eventos de lógica de negócios. Você pode definir propriedades, como UserId e DeviceId para identificar a máquina. Essas informações são anexadas a todos os eventos que a instância envia. 

*C#*

```csharp
TelemetryClient.Context.User.Id = "...";
TelemetryClient.Context.Device.Id = "...";
```

*Java*

```java
telemetry.getContext().getUser().setId("...");
telemetry.getContext().getDevice().setId("...");
```

Em projetos de Node.js, é possível usar `new applicationInsights.TelemetryClient(instrumentationKey?)` para criar uma nova instância, mas isso só é recomendado para cenários que exigem a configuração isolada do `defaultClient` singleton.

## <a name="trackevent"></a>TrackEvent

No Application Insights, um *evento personalizado* é um ponto de dados que você pode exibir no [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) como uma contagem agregada e na [Pesquisa de Diagnóstico](../../azure-monitor/app/diagnostic-search.md) como ocorrências individuais. (Ele não está relacionado ao MVC ou a outros “eventos” de estrutura.)

Insira chamadas de `TrackEvent` em seu código para fazer a contagem de vários eventos. Com que frequência os usuários escolhem um determinado recurso, com que frequência eles atingem metas específicas ou talvez com que frequência cometem tipos de erro específicos.

Por exemplo, em um aplicativo de jogo, envie um evento sempre que um usuário ganhar o jogo:

*JavaScript*

```javascript
appInsights.trackEvent("WinGame");
```

*C#*

```csharp
telemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
telemetry.TrackEvent("WinGame")
```

*Java*

```java
telemetry.trackEvent("WinGame");
```

*Node.js*

```javascript
telemetry.trackEvent({name: "WinGame"});
```

### <a name="custom-events-in-analytics"></a>Eventos personalizados na Análise

A telemetria está disponível na tabela `customEvents` na [Análise do Application Insights](analytics.md). Cada linha representa uma chamada para `trackEvent(..)` em seu aplicativo.

Se a [amostragem](../../azure-monitor/app/sampling.md) estiver em funcionamento, a propriedade itemCount mostrará um valor maior que 1. Para exemplo itemCount==10 significa que de 10 chamadas para trackEvent(), o processo de amostragem somente transmitirá um deles. Para obter uma contagem correta de eventos personalizados, você deve, portanto, usar um `customEvents | summarize sum(itemCount)`código como.

## <a name="getmetric"></a>GetMetric

### <a name="examples"></a>Exemplos

*C#*

```csharp
#pragma warning disable CA1716  // Namespace naming

namespace User.Namespace.Example01
{
    using System;
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    /// <summary>
    /// Most simple cases are one-liners.
    /// This is all possible without even importing an additional namespace.
    /// </summary>

    public class Sample01
    {
        /// <summary />
        public static void Exec()
        {
            // *** SENDING METRICS ***

            // Recall how you send custom telemetry with Application Insights in other cases, e.g. Events.
            // The following will result in an EventTelemetry object to be sent to the cloud right away.
            TelemetryClient client = new TelemetryClient();
            client.TrackEvent("SomethingInterestingHappened");

            // Metrics work very similar. However, the value is not sent right away.
            // It is aggregated with other values for the same metric, and the resulting summary (aka "aggregate" is sent automatically every minute.
            // To mark this difference, we use a pattern that is similar, but different from the established TrackXxx(..) pattern that sends telemetry right away:

            client.GetMetric("CowsSold").TrackValue(42);

            // *** MULTI-DIMENSIONAL METRICS ***

            // The above example shows a zero-dimensional metric.
            // Metrics can also be multi-dimensional.
            // In the initial version we are supporting up to 2 dimensions, and we will add support for more in the future as needed.
            // Here is an example for a one-dimensional metric:

            Metric animalsSold = client.GetMetric("AnimalsSold", "Species");

            animalsSold.TrackValue(42, "Pigs");
            animalsSold.TrackValue(24, "Horses");

            // The values for Pigs and Horses will be aggregated separately from each other and will result in two distinct aggregates.
            // You can control the maximum number of number data series per metric (and thus your resource usage and cost).
            // The default limits are no more than 1000 total data series per metric, and no more than 100 different values per dimension.
            // We discuss elsewhere how to change them.
            // We use a common .NET pattern: TryXxx(..) to make sure that the limits are observed.
            // If the limits are already reached, Metric.TrackValue(..) will return False and the value will not be tracked. Otherwise it will return True.
            // This is particularly useful if the data for a metric originates from user input, e.g. a file:

            Tuple<int, string> countAndSpecies = ReadSpeciesFromUserInput();
            int count = countAndSpecies.Item1;
            string species = countAndSpecies.Item2;

            if (!animalsSold.TrackValue(count, species))

            {
                client.TrackTrace($"Data series or dimension cap was reached for metric {animalsSold.Identifier.MetricId}.", SeverityLevel.Error);
            }

            // You can inspect a metric object to reason about its current state. For example:
            int currentNumberOfSpecies = animalsSold.GetDimensionValues(1).Count;
        }

        private static void ResetDataStructure()
        {
            // Do stuff
        }

        private static Tuple<int, string> ReadSpeciesFromUserInput()
        {
            return Tuple.Create(18, "Cows");
        }

        private static int AddItemsToDataStructure()
        {
            // Do stuff
            return 5;
        }
    }
}
```

## <a name="trackmetric"></a>TrackMetric

> [!NOTE]
> Microsoft. ApplicationInsights. TelemetryClient. TrackMetric não é o método preferencial para enviar métricas. As métricas devem sempre ser agregadas previamente em um período de tempo antes de serem enviadas. Use uma das sobrecargas GetMetric(..) para obter um objeto de métrica para acessar as funcionalidades de pré-agregação do SDK. Se você estiver implementando sua própria lógica de pré-autenticação, poderá usar o método TrackMetric () para enviar as agregações resultantes. Se o aplicativo exigir o envio de um item de telemetria separado em cada ocasião sem agregação ao longo do tempo, esse provavelmente será um caso de uso de telemetria de evento. Confira TelemetryClient.TrackEvent (Microsoft.ApplicationInsights.DataContracts.EventTelemetry).

O Application Insights pode traçar o gráfico de métrica que não estão associadas a eventos específicos. Por exemplo, você pode monitorar um comprimento de fila em intervalos regulares. Com as métricas, as medidas individuais são menos interessantes que as variações e tendências, então gráficos estatísticos são úteis.

Para enviar métricas para o Application Insights, você poderá usar a API `TrackMetric(..)`. Há duas maneiras de enviar uma métrica:

* Valor único. Toda vez que você realiza uma medida em seu aplicativo, envia o valor correspondente para o Application Insights. Por exemplo, suponha que você tenha uma métrica que descreve o número de itens em um contêiner. Durante um período de tempo específico, você primeiro coloca três itens no contêiner e, em seguida, remove dois itens. Da mesma forma, você chamaria `TrackMetric` duas vezes: primeiro, passando o valor `3` e, em seguida, o valor `-2`. O Application Insights armazena os dois valores em seu nome.

* Agregação. Ao trabalhar com as métricas, raramente há interesse em cada medida. Em vez disso, um resumo do que aconteceu durante um determinado período de tempo é importante. Esse tipo de resumo é chamado de _agregação_. No exemplo acima, a soma de métricas agregadas para esse período de tempo é `1` e a contagem dos valores de métrica é `2`. Ao usar a abordagem de agregação, você chamará somente `TrackMetric` uma vez por período de tempo e enviará os valores de agregação. Essa é a abordagem recomendada pois ela pode reduzir significativamente a sobrecarga de custo e desempenho enviando menos pontos de dados para o Application Insights, ainda assim coletando todas as informações relevantes.

### <a name="examples"></a>Exemplos

#### <a name="single-values"></a>Valores únicos

Para enviar um único valor de métrica:

*JavaScript*

 ```javascript
appInsights.trackMetric("queueLength", 42.0);
 ```

*C#*

```csharp
var sample = new MetricTelemetry();
sample.Name = "metric name";
sample.Value = 42.3;
telemetryClient.TrackMetric(sample);
```

*Java*

```java
telemetry.trackMetric("queueLength", 42.0);
```

*Node.js*

 ```javascript
telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

### <a name="custom-metrics-in-analytics"></a>Métricas personalizadas no Analytics

A telemetria está disponível na tabela `customMetrics` na [Análise do Application Insights](analytics.md). Cada linha representa uma chamada para `trackMetric(..)` em seu aplicativo.

* `valueSum` – essa é a soma das medidas. Para obter o valor médio, divida por `valueCount`.
* `valueCount` – o número de medidas que foram agregadas nessa chamada para `trackMetric(..)`.

## <a name="page-views"></a>Visualizações de página

Em um aplicativo de página da Web ou dispositivo, a telemetria de exibição de páginas é enviada por padrão quando cada tela ou página é carregada. Porém, você pode alterar isso para acompanhar as exibições de páginas em momentos diferentes ou adicionais. Por exemplo, em um aplicativo que exibe guias ou folhas, talvez você queira acompanhar uma página sempre que o usuário abrir uma nova folha.

Dados de usuário e de sessão são enviados como propriedades, juntamente com exibições de páginas, assim, os gráficos de usuário e sessão são ativados quando há telemetria de exibições de páginas.

### <a name="custom-page-views"></a>Exibições de páginas personalizadas

*JavaScript*

```javascript
appInsights.trackPageView("tab1");
```

*C#*

```csharp
telemetry.TrackPageView("GameReviewPage");
```

*Visual Basic*

```vb
telemetry.TrackPageView("GameReviewPage")
```

*Java*

```java
telemetry.trackPageView("GameReviewPage");
```

Se você tiver várias guias contidas em diferentes páginas HTML, você pode especificar a URL também:

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>Definindo o tempo das exibições de página

Por padrão, os tempos relatados como **Tempo de carregamento da exibição de página** são medidos a partir do momento em que o navegador envia a solicitação até o momento em que o evento de carregamento de página do navegador é chamado.

Em vez disso, é possível:

* Defina uma duração explícita na chamada [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview): `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`.
* Usar as chamadas de definição de tempo da exibição de página `startTrackPage` e `stopTrackPage`.

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

O nome que você usa como o primeiro parâmetro associa as chamadas de início e parada. Ele usa o nome da página atual como padrão.

As durações de carregamento de página resultantes exibidas no Metrics Explorer são derivadas do intervalo entre as chamadas de início e parada. Cabe a você definir qual intervalo terá o tempo definido.

### <a name="page-telemetry-in-analytics"></a>Telemetria de página na Análise

Na [Análise](analytics.md), duas tabelas mostram dados das operações do navegador:

* A tabela `pageViews` contém dados sobre a URL e o título da página
* A tabela `browserTimings` contém dados sobre o desempenho do cliente, como o tempo necessário para processar os dados de entrada

Para descobrir quanto tempo o navegador leva para processar páginas diferentes:

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

Para descobrir as popularidades de navegadores diferentes:

```kusto
pageViews
| summarize count() by client_Browser
```

Para associar as exibições de página a chamadas AJAX, utilize dependências:

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest

O SDK do servidor usa TrackRequest para registrar solicitações HTTP no log.

Você mesmo também poderá chamá-la se desejar simular solicitações em um contexto em que você não tenha o módulo de serviço Web em execução.

No entanto, a maneira recomendada para enviar uma telemetria de solicitação é aquela em que a solicitação atua como um <a href="#operation-context">contexto de operação</a>.

## <a name="operation-context"></a>Contexto de operação

Você pode correlacionar os itens de telemetria, associando-os ao contexto de operação. O módulo de rastreamento de solicitação padrão faz isso para exceções e outros eventos que são enviados enquanto uma solicitação HTTP está sendo processada. Em [Pesquisar](../../azure-monitor/app/diagnostic-search.md) e em [Análise](analytics.md), você pode usar a ID para localizar facilmente os eventos associados à solicitação usando a respectiva ID de operação.

Consulte [Correlação de telemetria no Application Insights](../../azure-monitor/app/correlation.md) para obter mais informações.

Ao acompanhar a telemetria manualmente, a maneira mais fácil de garantir a correlação de telemetria usando esse padrão:

*C#*

```csharp
// Establish an operation context and associated telemetry item:
using (var operation = telemetryClient.StartOperation<RequestTelemetry>("operationName"))
{
    // Telemetry sent in here will use the same operation ID.
    ...
    telemetryClient.TrackTrace(...); // or other Track* calls
    ...

    // Set properties of containing telemetry item--for example:
    operation.Telemetry.ResponseCode = "200";

    // Optional: explicitly send telemetry item:
    telemetryClient.StopOperation(operation);

} // When operation is disposed, telemetry item is sent.
```

Com a configuração de um contexto de operação, `StartOperation` cria um item de telemetria do tipo que você especificar. Ele envia o item de telemetria quando você descarta a operação ou se você chamar `StopOperation` explicitamente. Se você usar `RequestTelemetry` como o tipo de telemetria, sua duração será definida como o intervalo limitado entre o início e a parada.

Itens de telemetria relatados em um escopo da operação se tornam “filhos” dessa operação. Contextos de operação podem ser aninhados.

No Search, o contexto de operação é usado para criar a lista **Itens Relacionados**:

![Itens relacionados](./media/api-custom-events-metrics/21.png)

Consulte [Acompanhar operações personalizadas com o SDK do .NET do Application Insights](../../azure-monitor/app/custom-operations-tracking.md) para obter mais informações sobre o acompanhamento de operações personalizadas.

### <a name="requests-in-analytics"></a>Consultas na Análise

Na [Análise do Application Insights](analytics.md), as solicitações aparecem na tabela `requests`.

Se a [amostragem](../../azure-monitor/app/sampling.md) estiver em funcionamento, a propriedade itemCount mostrará um valor maior que 1. Para exemplo itemCount==10 significa que de 10 chamadas para trackRequest(), o processo de amostragem somente transmitirá um deles. Para obter uma contagem correta de solicitações e a duração média segmentada por nomes de solicitação, use um código como:

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>TrackException

Envie exceções ao Application Insights:

* Para [contá-las](../../azure-monitor/app/metrics-explorer.md) como uma indicação da frequência de um problema.
* Para [examinar ocorrências individuais](../../azure-monitor/app/diagnostic-search.md).

Os relatórios incluem os rastreamentos de pilha.

*C#*

```csharp
try
{
    ...
}
catch (Exception ex)
{
    telemetry.TrackException(ex);
}
```

*Java*

```java
try {
    ...
} catch (Exception ex) {
    telemetry.trackException(ex);
}
```

*JavaScript*

```javascript
try
{
    ...
}
catch (ex)
{
    appInsights.trackException(ex);
}
```

*Node.js*

```javascript
try
{
    ...
}
catch (ex)
{
    telemetry.trackException({exception: ex});
}
```

Os SDKs capturam muitas exceções automaticamente; portanto, você não precisa chamar sempre explicitamente o TrackException.

* ASP.NET: [Escrever código para detectar exceções](../../azure-monitor/app/asp-net-exceptions.md).
* Java EE: [Exceções são capturadas automaticamente](../../azure-monitor/app/java-get-started.md#exceptions-and-request-failures).
* JavaScript: Exceções são capturadas automaticamente. Se quiser desabilitar a coleta automática, adicione uma linha ao snippet de código que você insere em suas páginas da Web:

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Exceções na Análise

Na [Análise do Application Insights](analytics.md), as exceções aparecem na tabela `exceptions`.

Se a [amostragem](../../azure-monitor/app/sampling.md) estiver em funcionamento, a propriedade `itemCount` mostrará um valor maior que 1. Para exemplo itemCount==10 significa que de 10 chamadas para trackException(), o processo de amostragem somente transmitirá um deles. Para obter uma contagem correta de exceções segmentadas por tipo de exceção, use um código como:

```kusto
exceptions
| summarize sum(itemCount) by type
```

A maioria das informações de pilha importante já foi extraída em variáveis separadas, mas você pode extrair e separar a estrutura `details` para obter mais. Como essa estrutura é dinâmica, você deverá converter o resultado para o tipo esperado. Por exemplo:

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Para associar as solicitações relacionadas a exceções, use uma junção:

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>TrackTrace

Use TrackTrace para ajudar a diagnosticar problemas enviando uma "trilha de navegação estrutural" ao Application Insights. Você pode enviar partes de dados de diagnóstico e inspecioná-los na [Pesquisa de Diagnóstico](../../azure-monitor/app/diagnostic-search.md).

No .NET, os [Adaptadores de log](../../azure-monitor/app/asp-net-trace-logs.md) usam essa API para enviar logs de terceiros ao portal.

Em Java para [Standard loggers like Log4J, Logback](../../azure-monitor/app/java-trace-logs.md) use o Application Insights Log4j ou Logback Appenders para enviar logs de terceiros para o portal.

*C#*

```csharp
telemetry.TrackTrace(message, SeverityLevel.Warning, properties);
```

*Java*

```java
telemetry.trackTrace(message, SeverityLevel.Warning, properties);
```

*Node.js*

```javascript
telemetry.trackTrace({
    message: message,
    severity: applicationInsights.Contracts.SeverityLevel.Warning,
    properties: properties
});
```

*JavaScript do lado do cliente/navegador*

```javascript
trackTrace(message: string, properties?: {[string]:string}, severityLevel?: AI.SeverityLevel)
```

Registrar um evento de diagnóstico, como entrar ou sair de um método.

 Parâmetro | DESCRIÇÃO
---|---
`message` | Dados de diagnóstico. Pode ser muito mais longo do que um nome.
`properties` | Mapa de cadeia de caracteres para cadeia de caracteres: Dados adicionais usados para [filtrar exceções](https://azure.microsoft.com/documentation/articles/app-insights-api-custom-events-metrics/#properties) no portal. O padrão é vazio.
`severityLevel` | Valores com suporte: [SeverityLevel.ts](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/JavaScript/JavaScriptSDK.Interfaces/Contracts/Generated/SeverityLevel.ts)

Você pode pesquisar no conteúdo da mensagem, mas (diferentemente de valores de propriedade) não é possível filtrar nele.

O limite de tamanho de `message` é muito maior do que o limite de propriedades.
Uma vantagem de TrackTrace é que você pode colocar dados relativamente compridos na mensagem. Por exemplo, você pode codificar dados POST.  

Além disso, você pode adicionar um nível de severidade à mensagem. E, como ocorre com outros casos de telemetria, você pode adicionar valores de propriedade para ajudar a filtrar ou a pesquisar diferentes conjuntos de rastreamentos. Por exemplo:

*C#*

```csharp
var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
telemetry.TrackTrace("Slow database response",
                SeverityLevel.Warning,
                new Dictionary<string,string> { {"database", db.ID} });
```

*Java*

```java
Map<String, Integer> properties = new HashMap<>();
properties.put("Database", db.ID);
telemetry.trackTrace("Slow Database response", SeverityLevel.Warning, properties);
```

Em [Pesquisar](../../azure-monitor/app/diagnostic-search.md), você pode filtrar com facilidade todas as mensagens de um nível de gravidade específico que se relacionam a um determinado banco de dados.

### <a name="traces-in-analytics"></a>Rastreamentos na Análise

Na [Análise do Application Insights](analytics.md), as chamadas para TrackTrace aparecem na tabela `traces`.

Se a [amostragem](../../azure-monitor/app/sampling.md) estiver em funcionamento, a propriedade itemCount mostrará um valor maior que 1. Para exemplo itemCount==10 significa que de 10 chamadas para `trackTrace()`, o processo de amostragem somente transmitirá um deles. Para obter uma contagem correta de chamadas de rastreamento, você deverá, portanto, usar um código como `traces | summarize sum(itemCount)`.

## <a name="trackdependency"></a>TrackDependency

Use a chamada a TrackDependency para acompanhar os tempos de resposta e taxas de êxito de chamadas a uma parte externa do código. Os resultados são exibidos nos gráficos de dependência no portal.

*C#*

```csharp
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
catch(Exception ex) 
{
    success = false;
    telemetry.TrackException(ex);
    throw new Exception("Operation went wrong", ex);
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("DependencyType", "myDependency", "myCall", startTime, timer.Elapsed, success);
}
```

*Java*

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
}
finally {
    long endTime = System.currentTimeMillis();
    long delta = endTime - startTime;
    RemoteDependencyTelemetry dependencyTelemetry = new RemoteDependencyTelemetry("My Dependency", "myCall", delta, success);
    telemetry.setTimeStamp(startTime);
    telemetry.trackDependency(dependencyTelemetry);
}
```

*JavaScript*

```javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({
        dependencyTypeName: "myDependency",
        name: "myCall",
        duration: elapsed,
        success: success
    });
}
```

Lembre-se de que os SDKs de servidor incluem um [módulo de dependência](../../azure-monitor/app/asp-net-dependencies.md) que descobre e rastreia automaticamente determinadas chamadas de dependência; por exemplo, para bancos de dados e APIs REST. Você precisa instalar um agente em seu servidor para fazer com que o módulo funcione. 

Em Java, algumas chamadas de dependência podem ser automaticamente rastreadas com o [Agente Java](../../azure-monitor/app/java-agent.md).

Use essa chamada se quiser rastrear chamadas que o rastreamento automatizado não captura ou se não quiser instalar o agente.

Para desativar o módulo padrão de rastreamento de dependência no módulo C#, edite [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) e exclua a referência a `DependencyCollector.DependencyTrackingTelemetryModule`. Em Java, não instale o agente de java se você não deseja coletar dependências padrão automaticamente.

### <a name="dependencies-in-analytics"></a>Dependências na Análise

Na [Análise do Application Insights](analytics.md), as chamadas para trackDependency aparecem na tabela `dependencies`.

Se a [amostragem](../../azure-monitor/app/sampling.md) estiver em funcionamento, a propriedade itemCount mostrará um valor maior que 1. Para exemplo itemCount==10 significa que de 10 chamadas para trackDependency(), o processo de amostragem somente transmitirá um deles. Para obter uma contagem correta das dependências segmentadas por componente de destino, use um código como:

```kusto
dependencies
| summarize sum(itemCount) by target
```

Para associar as solicitações relacionadas a dependências, use uma junção:

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>Liberando dados

Normalmente, o SDK envia dados em intervalos fixos (normalmente 30 segundos) ou sempre que o buffer estiver cheio (normalmente 500 itens). No entanto, em alguns casos, você talvez queira liberar o buffer - por exemplo, se estiver usando o SDK em um aplicativo que é desligado.

*C#*

 ```csharp
telemetry.Flush();
// Allow some time for flushing before shutdown.
System.Threading.Thread.Sleep(5000);
```

*Java*

```java
telemetry.flush();
//Allow some time for flushing before shutting down
Thread.sleep(5000);
```

*Node.js*

```javascript
telemetry.flush();
```

A função é assíncrona para o [canal de telemetria do servidor](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/).

Idealmente, o método flush () deve ser usado na atividade de desligamento do Aplicativo.

## <a name="authenticated-users"></a>usuários autenticados

Em um aplicativo Web, os usuários são (por padrão) identificados por cookies. Um usuário pode ser contado mais de uma vez se ele acessar seu aplicativo de um computador ou navegador diferente, ou se ele excluir cookies.

Se os usuários entrarem em seu aplicativo, você poderá obter uma contagem mais precisa, definindo a ID do usuário autenticado no código do navegador:

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

Em um aplicativo MVC Web ASP.NET, por exemplo:

*Razor*

```cshtml
@if (Request.IsAuthenticated)
{
    <script>
        appInsights.setAuthenticatedUserContext("@User.Identity.Name
            .Replace("\\", "\\\\")"
            .replace(/[,;=| ]+/g, "_"));
    </script>
}
```

Não é necessário usar o nome do usuário real de conexão. Só é preciso ser uma ID que seja exclusiva para esse usuário. Não deve incluir espaços, nem os caracteres `,;=|`.

A ID de usuário também é definida em um cookie de sessão e enviada ao servidor. Se o SDK do servidor estiver instalado, a ID de usuário autenticado será enviada como parte das propriedades de contexto da telemetria do cliente e do servidor. Assim, você poderá filtrar e pesquisar nele.

Se seu aplicativo agrupa os usuários em contas, você também pode passar um identificador para a conta (com as mesmas restrições de caracteres).

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

No [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md), você pode criar um gráfico que contabiliza **Usuários Autenticados** e **Contas de usuário**.

Você também pode [pesquisar](../../azure-monitor/app/diagnostic-search.md) por pontos de dados do cliente com contas e nomes de usuário específicos.

## <a name="properties"></a>Filtragem, pesquisa e segmentação de dados usando propriedades

Você pode anexar propriedades e medidas para seus eventos (e também para métricas, exibições de página e outros dados de telemetria).

*Propriedades* são valores de cadeia de caracteres que você pode usar para filtrar a telemetria nos relatórios de uso. Por exemplo, se o aplicativo fornecer vários jogos, você poderá anexar o nome do jogo a cada evento, de modo que seja possível ver quais jogos são mais populares.

Há um limite de 8192 para o tamanho da cadeia de caracteres. (Se você deseja enviar grandes quantidades de dados, use o parâmetro de mensagem de TrackTrace.)

*Métricas* são valores numéricos que podem ser apresentados graficamente. Por exemplo, convém verificar se há um aumento gradual nas pontuações que seus jogadores atingem. Os grafos podem ser segmentados pelas propriedades enviadas com o evento para que você possa obter grafos separado ou empilhados para jogos diferentes.

Para que os valores de métrica sejam exibidos corretamente, eles devem ser maiores ou iguais a 0.

Há alguns [limites no número de propriedades, valores de propriedade e métricas](#limits) que você pode usar.

*JavaScript*

```javascript
appInsights.trackEvent
    ("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );

appInsights.trackPageView
    ("page name", "http://fabrikam.com/pageurl.html",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );
```

*C#*

```csharp
// Set up some properties and metrics:
var properties = new Dictionary <string, string>
    {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
var metrics = new Dictionary <string, double>
    {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

// Send the event:
telemetry.TrackEvent("WinGame", properties, metrics);
```

*Node.js*

```javascript
// Set up some properties and metrics:
var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

// Send the event:
telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});
```

*Visual Basic*

```vb
' Set up some properties:
Dim properties = New Dictionary (Of String, String)
properties.Add("game", currentGame.Name)
properties.Add("difficulty", currentGame.Difficulty)

Dim metrics = New Dictionary (Of String, Double)
metrics.Add("Score", currentGame.Score)
metrics.Add("Opponents", currentGame.OpponentCount)

' Send the event:
telemetry.TrackEvent("WinGame", properties, metrics)
```

*Java*

```java
Map<String, String> properties = new HashMap<String, String>();
properties.put("game", currentGame.getName());
properties.put("difficulty", currentGame.getDifficulty());

Map<String, Double> metrics = new HashMap<String, Double>();
metrics.put("Score", currentGame.getScore());
metrics.put("Opponents", currentGame.getOpponentCount());

telemetry.trackEvent("WinGame", properties, metrics);
```

> [!NOTE]
> Tome cuidado para não registrar informações de identificação pessoal nas propriedades.
>
>

### <a name="alternative-way-to-set-properties-and-metrics"></a>Maneira alternativa de definir propriedades e métricas

Se for mais conveniente, você poderá coletar os parâmetros de um evento em um objeto separado:

```csharp
var event = new EventTelemetry();

event.Name = "WinGame";
event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
event.Properties["game"] = currentGame.Name;
event.Properties["difficulty"] = currentGame.Difficulty;
event.Metrics["Score"] = currentGame.Score;
event.Metrics["Opponents"] = currentGame.Opponents.Length;

telemetry.TrackEvent(event);
```

> [!WARNING]
> Não reutilize a mesma instância de item de telemetria (`event`, neste exemplo) para chamar Track*() várias vezes. Isso pode fazer com que a telemetria seja enviada com a configuração incorreta.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Medidas personalizadas e propriedades na Análise

Na [Análise](analytics.md), as métricas personalizadas e as propriedades são exibidas nos atributos `customMeasurements` e `customDimensions` de cada registro de telemetria.

Por exemplo, se você tiver adicionado uma propriedade chamada "game" para a telemetria de solicitação, essa consulta contará as ocorrências de valores diferentes de "game" e mostrará a média da "pontuação" da métrica personalizada:

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

Observe que:

* Quando você extrai um valor do JSON customDimensions ou customMeasurements, ele tem o tipo dinâmico e, portanto, você deve convertê-lo em `tostring` ou `todouble`.
* Para levar em conta a possibilidade de [amostragem](../../azure-monitor/app/sampling.md), você deverá usar `sum(itemCount)`, e não `count()`.

## <a name="timed"></a> Eventos de tempo

Às vezes, você quer colocar em gráfico quanto tempo leva para realizar uma ação. Por exemplo, talvez você queira saber quanto tempo os usuários levam para considerar as opções de um jogo. Você pode usar o parâmetro measurement para isso.

*C#*

```csharp
var stopwatch = System.Diagnostics.Stopwatch.StartNew();

// ... perform the timed action ...

stopwatch.Stop();

var metrics = new Dictionary <string, double>
    {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

// Set up some properties:
var properties = new Dictionary <string, string>
    {{"signalSource", currentSignalSource.Name}};

// Send the event:
telemetry.TrackEvent("SignalProcessed", properties, metrics);
```

*Java*

```java
long startTime = System.currentTimeMillis();

// Perform timed action

long endTime = System.currentTimeMillis();
Map<String, Double> metrics = new HashMap<>();
metrics.put("ProcessingTime", (double)endTime-startTime);

// Setup some properties
Map<String, String> properties = new HashMap<>();
properties.put("signalSource", currentSignalSource.getName());

// Send the event
telemetry.trackEvent("SignalProcessed", properties, metrics);
```

## <a name="defaults"></a>Propriedades padrão para telemetria personalizada

Se quiser definir valores de propriedade padrão para alguns dos eventos personalizados que escrever, você poderá defini-los em uma instância de TelemetryClient. Eles são anexados a cada item de telemetria enviado do cliente.

*C#*

```csharp
using Microsoft.ApplicationInsights.DataContracts;

var gameTelemetry = new TelemetryClient();
gameTelemetry.Context.Properties["Game"] = currentGame.Name;
// Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
Dim gameTelemetry = New TelemetryClient()
gameTelemetry.Context.Properties("Game") = currentGame.Name
' Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame")
```

*Java*

```java
import com.microsoft.applicationinsights.TelemetryClient;
import com.microsoft.applicationinsights.TelemetryContext;
...


TelemetryClient gameTelemetry = new TelemetryClient();
TelemetryContext context = gameTelemetry.getContext();
context.getProperties().put("Game", currentGame.Name);

gameTelemetry.TrackEvent("WinGame");
```

*Node.js*

```javascript
var gameTelemetry = new applicationInsights.TelemetryClient();
gameTelemetry.commonProperties["Game"] = currentGame.Name;

gameTelemetry.TrackEvent({name: "WinGame"});
```

Chamadas de telemetria individuais podem substituir os valores padrão em seus dicionários de propriedades.

*Para clientes Web JavaScript*, use inicializadores de telemetria JavaScript.

*Para adicionar propriedades a toda a telemetria*, incluindo os dados de módulos de coleta padrão, [implemente `ITelemetryInitializer`](../../azure-monitor/app/api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Amostragem, filtragem e processamento da telemetria

É possível escrever códigos para processar a telemetria antes que ela seja enviada do SDK. O processamento inclui dados que são enviados dos módulos de telemetria padrão, como a coleta de solicitação HTTP e a coleta de dependência.

[Adicione propriedades](../../azure-monitor/app/api-filtering-sampling.md#add-properties) à telemetria implementando `ITelemetryInitializer`. Por exemplo, é possível adicionar números de versão ou valores que são calculados de outras propriedades.

A [filtragem](../../azure-monitor/app/api-filtering-sampling.md#filtering) pode modificar ou descartar a telemetria antes que ela seja enviada do SDK pela implementação de `ITelemetryProcessor`. Você controla o que é enviado ou descartado, mas precisa levar em conta o efeito em suas métricas. Dependendo de como você descartar os itens, você poderá perder a capacidade de navegar entre itens relacionados.

A [amostragem](../../azure-monitor/app/api-filtering-sampling.md) é uma solução em pacote para reduzir o volume de dados enviados de seu aplicativo ao portal. Isso é feito sem afetar as métricas exibidas. E também sem afetar sua capacidade de diagnosticar problemas ao navegar entre itens relacionados, como exceções, solicitações e exibições de página.

[Saiba mais](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Desabilitando a telemetria

Para *parar e iniciar dinamicamente* a coleta e a transmissão de telemetria:

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

Para *desabilitar os coletores padrão selecionados* – por exemplo, contadores de desempenho, solicitações HTTP ou dependências – exclua ou comente as linhas relevantes em [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Você poderá fazer isso, por exemplo, se quiser enviar seus próprios dados de TrackRequest.

*Node.js*

```javascript
telemetry.config.disableAppInsights = true;
```

Para *desabilitar coletores padrão selecionados* – por exemplo, contadores de desempenho, solicitações HTTP ou dependências – no momento da inicialização, vincule os métodos de configuração ao código de inicialização do SDK:

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

Para desabilitar esses coletores após a inicialização, use o objeto de configuração: `applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="debug"></a>Modo de desenvolvedor

Durante a depuração, é útil ter sua telemetria emitida pelo pipeline para que você possa ver os resultados imediatamente. Você também obtém mensagens adicionais que ajudarão a rastrear problemas com a telemetria. Desative-a na produção, pois isso pode tornar seu aplicativo mais lento.

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

*Node.js*

Para o Node. js, você pode habilitar o modo de desenvolvedor habilitando `setInternalLogging` o log `maxBatchSize` interno via e definindo como 0, o que faz com que a telemetria seja enviada assim que for coletada.

```js
applicationInsights.setup("ikey")
  .setInternalLogging(true, true)
  .start()
applicationInsights.defaultClient.config.maxBatchSize = 0;
```

## <a name="ikey"></a> Definição da chave de instrumentação para telemetria personalizada selecionada

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-ikey"></a> Chave de instrumentação dinâmica

Para evitar a mistura de telemetria dos ambientes de desenvolvimento, teste e produção, você pode [criar recursos separados do Application Insights](../../azure-monitor/app/create-new-resource.md ) e alterar suas chaves de acordo com o ambiente.

Em vez de obter a chave de instrumentação do arquivo de configuração, você pode defini-la em seu código. Defina a chave em um método de inicialização como global.aspx.cs em um serviço ASP.NET:

*C#*

```csharp
protected void Application_Start()
{
    Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey =
        // - for example -
        WebConfigurationManager.Settings["ikey"];
    ...
}
```

*JavaScript*

```javascript
appInsights.config.instrumentationKey = myKey;
```

Em páginas da Web, convém defini-la com base no estado do servidor Web, em vez de codificá-la literalmente no script. Por exemplo, em uma página da Web gerada em um aplicativo ASP.NET:

*JavaScript no Razor*

```cshtml
<script type="text/javascript">
// Standard Application Insights webpage script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
    // Generate from server property:
    @Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey;
}) // ...
```

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="telemetrycontext"></a>TelemetryContext

TelemetryClient tem uma propriedade de Contexto, que contém valores que serão enviadas com todos os dados de telemetria. Normalmente, eles são definidos pelos módulos padrão de telemetria, mas você pode também defini-las por conta própria. Por exemplo:

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

Se você definir qualquer um desses valores por conta própria, considere remover a linha relevante de [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), de modo que não haja confusão entre os seus valores e os valores padrão.

* **Componente**: O aplicativo e sua versão.
* **Dispositivo**: Dados sobre o dispositivo em que o aplicativo está sendo executado. (Em aplicativos Web, esse é o servidor ou dispositivo cliente do qual a telemetria é enviada).
* **InstrumentationKey**: O recurso do Application Insights no Azure onde a telemetria é exibida. Normalmente, escolhido no Applicationinsights.config.
* **Localização**: A localização geográfica do dispositivo.
* **Operação**: Em aplicativos Web, a solicitação HTTP atual. Em outros tipos de aplicativos, você pode definir isso para agrupar eventos juntos.
  * **ID**: Um valor gerado que correlaciona eventos diferentes, para que, ao inspecionar qualquer evento na Pesquisa de Diagnóstico você possa encontrar itens relacionados.
  * **Nome**: Um identificador, geralmente a URL da solicitação HTTP.
  * **SyntheticSource**: Se não for nulo ou vazio, uma cadeia de caracteres que indica que a origem da solicitação foi identificada como um robô ou teste na Web. Por padrão, ela é excluída dos cálculos no Metrics Explorer.
* **Propriedades**: Propriedades que são enviadas com todos os dados telemétricos. Pode ser substituído nas chamadas individuais de Track*.
* **Session**: Sessão do usuário. A ID é definida para um valor gerado, que é alterado quando o usuário ficar inativo por um tempo.
* **Usuário**: Informação do usuário.

## <a name="limits"></a>limites

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

Para evitar atingir o limite de taxa de dados, use [amostragem](../../azure-monitor/app/sampling.md).

Para determinar por quanto tempo os dados são mantidos, confira [Retenção e privacidade de dados](../../azure-monitor/app/data-retention-privacy.md).

## <a name="reference-docs"></a>Documentos de Referência

* [Referência do ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)
* [Referência do Java](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [Referência do JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [SDK do Android](https://github.com/Microsoft/ApplicationInsights-Android)
* [SDK do iOS](https://github.com/Microsoft/ApplicationInsights-iOS)

## <a name="sdk-code"></a>Código do SDK

* [SDK de Núcleo do ASP.NET](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Pacotes do Windows Server](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [SDK do Node.js](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [SDK do JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)
* [Todas as plataformas](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>Perguntas

* *Que exceções podem ser lançadas por chamadas Track_()?*

    nenhuma. Você não precisa encapsulá-las em cláusulas try-catch. Se o SDK encontrar problemas, ele registrará mensagens em log na saída do console de depuração e (se elas passarem despercebidas) na Pesquisa de Diagnóstico.
* *Há uma API REST para obter dados do portal?*

    Sim, a [API de acesso a dados](https://dev.applicationinsights.io/). Outras maneiras de extrair dados incluem [exportar do Analytics para o Power BI](../../azure-monitor/app/export-power-bi.md ) e a [exportação contínua](../../azure-monitor/app/export-telemetry.md).

## <a name="next"></a>Próximas etapas

* [Pesquisar eventos e logs](../../azure-monitor/app/diagnostic-search.md)
* [Solução de problemas](../../azure-monitor/app/troubleshoot-faq.md)
