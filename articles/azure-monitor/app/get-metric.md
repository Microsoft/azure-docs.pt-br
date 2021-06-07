---
title: Get-Metric em Azure Monitor Application Insights
description: Saiba como usar efetivamente a chamada getmetric () para capturar métricas previamente agregadas localmente para aplicativos .NET e .NET Core com Azure Monitor Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: 0ce2651d5cfcb1578d78982af109a004aaac11f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101719773"
---
# <a name="custom-metric-collection-in-net-and-net-core"></a>Coleção de métricas personalizadas no .NET e no .NET Core

Os SDKs Azure Monitor Application Insights .NET e .NET Core têm dois métodos diferentes de coletar métricas personalizadas, `TrackMetric()` e `GetMetric()` . A principal diferença entre esses dois métodos é a agregação local. `TrackMetric()` falta de pré-autenticação enquanto `GetMetric()` tem pré-autenticação. A abordagem recomendada é usar a agregação, portanto, `TrackMetric()` não é mais o método preferencial para coletar métricas personalizadas. Este artigo explicará como usar o método getmetric () e algumas das razões por trás de como ele funciona.

## <a name="trackmetric-versus-getmetric"></a>TrackMetric versus getmetric

`TrackMetric()` envia telemetria bruta, indicando uma métrica. Não é eficiente enviar um único item de telemetria para cada valor. `TrackMetric()` também é ineficiente em termos de desempenho, já que cada `TrackMetric(item)` um passa pelo pipeline completo do SDK de inicializadores e processadores de telemetria. Ao contrário de `TrackMetric()` , `GetMetric()` o manipula a pré-autenticação local para você e envia apenas uma métrica de resumo agregada em um intervalo fixo de um minuto. Portanto, se você precisar monitorar com atenção alguma métrica personalizada no segundo ou até mesmo nível de milissegundos, poderá fazer isso enquanto apenas o custo do tráfego de rede e do armazenamento do monitoramento somente a cada minuto. Isso também reduz bastante o risco de que a limitação ocorra, já que o número total de itens de telemetria que precisam ser enviados para uma métrica agregada é muito reduzido.

Em Application Insights, as métricas personalizadas coletadas por meio do `TrackMetric()` e `GetMetric()` não estão sujeitas à [amostragem](./sampling.md). A amostragem de métricas importantes pode levar a cenários em que os alertas que você pode ter criado sobre essas métricas podem se tornar não confiáveis. Ao nunca fazer amostragem de suas métricas personalizadas, você geralmente pode ter certeza de que quando os limites de alerta forem violados, um alerta será acionado.  Mas como as métricas personalizadas não são amostradas, há algumas preocupações potenciais.

Se você precisar controlar as tendências em uma métrica a cada segundo, ou em um intervalo ainda mais granular, isso poderá resultar em:

- Aumento dos custos de armazenamento de dados. Há um custo associado à quantidade de dados que você envia para Azure Monitor. (Quanto mais dados você enviar, maior será o custo geral do monitoramento.)
- Aumento do tráfego de rede/sobrecarga de desempenho. (Em alguns cenários, isso pode ter um custo monetário e de desempenho do aplicativo.)
- Risco de limitação de ingestão. (O serviço de Azure Monitor descarta os pontos de dados ("limitadores") quando seu aplicativo envia uma taxa muito alta de telemetria em um intervalo de tempo curto.)

A limitação é uma preocupação específica que, como a amostragem, a limitação pode levar a alertas perdidos, pois a condição para disparar um alerta pode ocorrer localmente e, em seguida, ser descartada no ponto de extremidade de ingestão devido a excesso de dados sendo enviados. É por isso que, para .NET e .NET Core, não recomendamos usar `TrackMetric()` a menos que você tenha implementado sua própria lógica de agregação local. Se você estiver tentando rastrear todas as instâncias que um evento ocorre durante um determinado período de tempo, você pode achar [`TrackEvent()`](./api-custom-events-metrics.md#trackevent) melhor se ajustar. Embora tenha em mente que, ao contrário das métricas personalizadas, os eventos personalizados estão sujeitos à amostragem. É claro que você ainda pode usar `TrackMetric()` mesmo sem escrever sua própria pré-autenticação local, mas se você fizer isso, fique atento às armadilhas.

No resumo `GetMetric()` , é a abordagem recomendada, já que ela faz a agregação, acumula valores de todas as chamadas Track () e envia um resumo/agregação a cada minuto. Isso pode reduzir significativamente o custo e a sobrecarga de desempenho enviando menos pontos de dados e, ao mesmo tempo, coletando todas as informações relevantes.

> [!NOTE]
> Somente os SDKs .NET e .NET Core têm um método getmetric (). Se você estiver usando o Java, poderá usar as [métricas micrometer](./micrometer-java.md) ou `TrackMetric()` . Para JavaScript e Node.js você ainda usaria `TrackMetric()` , mas tenha em mente as advertências que foram descritas na seção anterior. Para Python, você pode usar [OpenCensus. stats](./opencensus-python.md#metrics) para enviar métricas personalizadas, mas a implementação de métricas é diferente.

## <a name="getting-started-with-getmetric"></a>Introdução ao getmetric

Para nossos exemplos, vamos usar um aplicativo básico do serviço de trabalho do .NET Core 3,1. Se você quiser replicar exatamente o ambiente de teste que foi usado com esses exemplos, siga as etapas 1-6 do [artigo do serviço de trabalho de monitoramento](./worker-service.md#net-core-30-worker-service-application) para adicionar Application insights a um modelo de projeto de serviço de trabalho básico. Esses conceitos se aplicam a qualquer aplicativo geral em que o SDK possa ser usado, incluindo aplicativos Web e aplicativos de console.

### <a name="sending-metrics"></a>Enviando métricas

Substitua o conteúdo do `worker.cs` arquivo pelo seguinte:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;
using Microsoft.ApplicationInsights;

namespace WorkerService3
{
    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {   // The following line demonstrates usages of GetMetric API.
            // Here "computersSold", a custom metric name, is being tracked with a value of 42 every second.
            while (!stoppingToken.IsCancellationRequested)
            {
                _telemetryClient.GetMetric("ComputersSold").TrackValue(42);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(1000, stoppingToken);
            }
        }
    }
}
```

Se você executar o código acima e observar a telemetria sendo enviada por meio da janela de saída do Visual Studio ou de uma ferramenta como o Fiddler de Telerik, você verá o loop while executando repetidamente sem nenhuma telemetria sendo enviada e um único item de telemetria será enviado por toda a marca 60-Second, que, no caso de nosso teste, terá a seguinte aparência :

```json
Application Insights Telemetry: {"name":"Microsoft.ApplicationInsights.Dev.00000000-0000-0000-0000-000000000000.Metric", "time":"2019-12-28T00:54:19.0000000Z",
"ikey":"00000000-0000-0000-0000-000000000000",
"tags":{"ai.application.ver":"1.0.0.0",
"ai.cloud.roleInstance":"Test-Computer-Name",
"ai.internal.sdkVersion":"m-agg2c:2.12.0-21496",
"ai.internal.nodeName":"Test-Computer-Name"},
"data":{"baseType":"MetricData",
"baseData":{"ver":2,"metrics":[{"name":"ComputersSold",
"kind":"Aggregation",
"value":1722,
"count":41,
"min":42,
"max":42,
"stdDev":0}],
"properties":{"_MS.AggregationIntervalMs":"42000",
"DeveloperMode":"true"}}}}
```

Este item de telemetria única representa uma agregação de 41 medidas de métricas distintas. Como estávamos enviando o mesmo valor repetidamente, temos um *desvio padrão (DESVPAD)* de 0 com valores *máximos (máximos)* e *mínimos (* mínimos) idênticos. A propriedade *Value* representa uma soma de todos os valores individuais que foram agregados.

> [!NOTE]
> Getmetric não dá suporte ao acompanhamento do último valor (ou seja, "medidor") ou ao rastreamento de histogramas/distribuições.

Se examinarmos nosso recurso de Application Insights na experiência de logs (análise), esse item de telemetria individual será semelhante ao seguinte:

![Log Analytics exibição de consulta](./media/get-metric/log-analytics.png)

> [!NOTE]
> Enquanto o item de telemetria bruta não continha um campo/Propriedade Sum explícita depois de ser ingerido, criamos um para você. Nesse caso `value` , as propriedades e `valueSum` representam a mesma coisa.

Você também pode acessar a telemetria de métrica personalizada na seção [_métricas_](../essentials/metrics-charts.md) do Portal. Como uma [métrica baseada em log e personalizada](pre-aggregated-metrics-log-metrics.md). (A captura de tela abaixo é um exemplo de baseado em log.) ![Exibição do Metrics Explorer](./media/get-metric/metrics-explorer.png)

### <a name="caching-metric-reference-for-high-throughput-usage"></a>Referência de métrica de cache para uso de alta taxa de transferência

Em alguns casos, os valores de métrica são observados com muita frequência. Por exemplo, um serviço de alta taxa de transferência que processa 500 solicitações/segundo pode querer emitir 20 métricas de telemetria para cada solicitação. Isso significa controlar 10.000 valores por segundo. Nesses cenários de alta taxa de transferência, os usuários podem precisar ajudar o SDK, evitando algumas pesquisas.

Por exemplo, nesse caso, o exemplo acima realizou uma pesquisa para um identificador para a métrica "ComputersSold" e, em seguida, rastreou um valor observado 42. Em vez disso, o identificador pode ser armazenado em cache para várias invocações de faixa:

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is where the cache is stored to handle faster lookup
            Metric computersSold = _telemetryClient.GetMetric("ComputersSold");
            while (!stoppingToken.IsCancellationRequested)
            {

                computersSold.TrackValue(42);

                computersSold.TrackValue(142);

                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

Além de armazenar em cache o identificador de métrica, o exemplo acima também reduziu o `Task.Delay` de 50 milissegundos para que o loop fosse executado com mais frequência, resultando em `TrackValue()` invocações de 772.

## <a name="multi-dimensional-metrics"></a>Métricas multidimensionais

Os exemplos na seção anterior mostram métricas de dimensionalidade zero. As métricas também podem ser multidimensionais. Atualmente, damos suporte a até 10 dimensões.

 Aqui está um exemplo de como criar uma métrica unidimensional:

```csharp
//...

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            // This is an example of a metric with a single dimension.
            // FormFactor is the name of the dimension.
            Metric computersSold= _telemetryClient.GetMetric("ComputersSold", "FormFactor");

            while (!stoppingToken.IsCancellationRequested)
            {
                // The number of arguments (dimension values)
                // must match the number of dimensions specified while GetMetric.
                // Laptop, Tablet, etc are values for the dimension "FormFactor"
                computersSold.TrackValue(42, "Laptop");
                computersSold.TrackValue(20, "Tablet");
                computersSold.TrackValue(126, "Desktop");


                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);
                await Task.Delay(50, stoppingToken);
            }
        }

```

A execução desse código por pelo menos 60 segundos resultará na envio de três itens de telemetria distintos para o Azure, cada um representando a agregação de um dos três fatores forma. Como antes, você pode examiná-los na exibição de logs (análise):

![Exibição do log Analytics de métrica multidimensional](./media/get-metric/log-analytics-multi-dimensional.png)

Bem como na experiência do Metrics Explorer:

![Métricas personalizadas](./media/get-metric/custom-metrics.png)

No entanto, você observará que não é possível dividir a métrica pela sua nova dimensão personalizada ou exibir sua dimensão personalizada com a exibição de métricas:

![Divisão de suporte](./media/get-metric/splitting-support.png)

Por padrão, as métricas multidimensionais dentro da experiência do Gerenciador de métricas não são ativadas no Application Insights recursos.

### <a name="enable-multi-dimensional-metrics"></a>Habilitar métricas multidimensionais

Para habilitar métricas multidimensionais para um recurso Application insights, selecione **uso e custos estimados**  >  **métricas personalizadas**  >  **habilitar alertas em dimensões de métricas personalizadas**  >  **OK**. Mais detalhes sobre isso podem ser encontrados [aqui](pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).

Depois de fazer essa alteração e enviar a nova telemetria multidimensional, você poderá aplicar a **divisão**.

> [!NOTE]
> Somente as métricas enviadas recentemente depois que o recurso foi ativado no portal terão dimensões armazenadas.

![Aplicar a separação](./media/get-metric/apply-splitting.png)

E exiba suas agregações de métrica para cada dimensão _FormFactor_ :

![Fatores forma](./media/get-metric/formfactor.png)

### <a name="how-to-use-metricidentifier-when-there-are-more-than-three-dimensions"></a>Como usar o MetricIdentifier quando há mais de três dimensões

No momento, há suporte para 10 dimensões, mais de três dimensões requer o uso de `MetricIdentifier` :

```csharp
// Add "using Microsoft.ApplicationInsights.Metrics;" to use MetricIdentifier
// MetricIdentifier id = new MetricIdentifier("[metricNamespace]","[metricId],"[dim1]","[dim2]","[dim3]","[dim4]","[dim5]");
MetricIdentifier id = new MetricIdentifier("CustomMetricNamespace","ComputerSold", "FormFactor", "GraphicsCard", "MemorySpeed", "BatteryCapacity", "StorageCapacity");
Metric computersSold  = _telemetryClient.GetMetric(id);
computersSold.TrackValue(110,"Laptop", "Nvidia", "DDR4", "39Wh", "1TB");
```

## <a name="custom-metric-configuration"></a>Configuração de métrica personalizada

Se você quiser alterar a configuração de métrica, será necessário fazer isso no local em que a métrica é inicializada.

### <a name="special-dimension-names"></a>Nomes de dimensão especiais

As métricas não usam o contexto de telemetria do `TelemetryClient` usado para acessá-las, os nomes de dimensão especiais disponíveis como constantes na `MetricDimensionNames` classe são a melhor solução para essa limitação.

Agregações de métricas enviadas pelo "tamanho de solicitação de operação especial"-a métrica **não** terá seu `Context.Operation.Name` conjunto para "operação especial". Enquanto `TrackMetric()` ou qualquer outro TrackXXX () terá `OperationName` definido corretamente como "operação especial".

``` csharp
        //...
        TelemetryClient specialClient;
        private static int GetCurrentRequestSize()
        {
            // Do stuff
            return 1100;
        }
        int requestSize = GetCurrentRequestSize()

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                //...
                specialClient.Context.Operation.Name = "Special Operation";
                specialClient.GetMetric("Special Operation Request Size").TrackValue(requestSize);
                //...
            }
                   
        }
```

Nessa circunstância, use os nomes de dimensão especiais listados na `MetricDimensionNames` classe para especificar `TelemetryContext` valores.

Por exemplo, quando a agregação de métrica resultante da próxima instrução for enviada para o Application Insights ponto de extremidade de nuvem, seu `Context.Operation.Name` campo de dados será definido como "operação especial":

```csharp
_telemetryClient.GetMetric("Request Size", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation");
```

Os valores dessa dimensão especial serão copiados para o `TelemetryContext` e não serão usados como uma dimensão ' normal '. Se você também quiser manter uma dimensão de operação para exploração de métrica normal, você precisará criar uma dimensão separada para essa finalidade:

```csharp
_telemetryClient.GetMetric("Request Size", "Operation Name", MetricDimensionNames.TelemetryContext.Operation.Name).TrackValue(requestSize, "Special Operation", "Special Operation");
```

### <a name="dimension-and-time-series-capping"></a>Dimensão e limitação da série temporal

 Para impedir que o subsistema de telemetria use acidentalmente seus recursos, você pode controlar o número máximo de séries de dados por métrica. Os limites padrão não são mais do que 1000 séries de dados totais por métrica e não mais que 100 valores diferentes por dimensão.

 No contexto de dimensão e Time Series capping, usamos `Metric.TrackValue(..)` para garantir que os limites sejam observados. Se os limites já forem atingidos, `Metric.TrackValue(..)` o retornará "false" e o valor não será acompanhado. Caso contrário, ele retornará "true". Isso será útil se os dados de uma métrica se originarem da entrada do usuário.

O `MetricConfiguration` Construtor obtém algumas opções sobre como gerenciar diferentes séries dentro da respectiva métrica e um objeto de uma classe que implementa `IMetricSeriesConfiguration` que especifica o comportamento de agregação para cada série individual da métrica:

``` csharp
var metConfig = new MetricConfiguration(seriesCountLimit: 100, valuesPerDimensionLimit:2,
                new MetricSeriesConfigurationForMeasurement(restrictToUInt32Values: false));

Metric computersSold = _telemetryClient.GetMetric("ComputersSold", "Dimension1", "Dimension2", metConfig);

// Start tracking.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value1");
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value2");

// The following call gives 3rd unique value for dimension2, which is above the limit of 2.
computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3");
// The above call does not track the metric, and returns false.
```

* `seriesCountLimit` é o número máximo de séries de tempo de dados que uma métrica pode conter. Quando esse limite for atingido, as chamadas para `TrackValue()` não serão rastreadas.
* `valuesPerDimensionLimit` limita o número de valores distintos por dimensão de maneira semelhante.
* `restrictToUInt32Values` Determina se apenas valores inteiros não negativos devem ser controlados.

Aqui está um exemplo de como enviar uma mensagem para saber se os limites de Cap são excedidos:

```csharp
if (! computersSold.TrackValue(100, "Dim1Value1", "Dim2Value3"))
{
// Add "using Microsoft.ApplicationInsights.DataContract;" to use SeverityLevel.Error
_telemetryClient.TrackTrace("Metric value not tracked as value of one of the dimension exceeded the cap. Revisit the dimensions to ensure they are within the limits",
SeverityLevel.Error);
}
```

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais ](./worker-service.md)sobre o monitoramento de aplicativos de serviço do Worker.
* Para obter mais detalhes sobre as [métricas baseadas em log e previamente agregadas](./pre-aggregated-metrics-log-metrics.md).
* [Gerenciador de métricas](../essentials/metrics-getting-started.md)
* Como habilitar o Application Insights para [aplicativos ASP.NET Core](asp-net-core.md)
* Como habilitar o Application Insights para [aplicativos ASP.net](asp-net.md)
