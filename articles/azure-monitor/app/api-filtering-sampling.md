---
title: Filtragem e pré-processamento no SDK do Application Insights | Microsoft Docs
description: Escreva processadores de telemetria e inicializadores de telemetria para que o SDK filtre ou adicione Propriedades aos dados antes que a telemetria seja enviada ao portal de Application Insights.
ms.topic: conceptual
ms.date: 11/23/2016
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: cb9159b98b219c6fb04beb7bbbaade64fc72a30b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98927756"
---
# <a name="filter-and-preprocess-telemetry-in-the-application-insights-sdk"></a>Filtrar e pré-processar telemetria no SDK do Application Insights

Você pode gravar e configurar plug-ins para o SDK do Application Insights para personalizar como a telemetria pode ser enriqueceda e processada antes de ser enviada ao serviço de Application Insights.

* [Amostragem](sampling.md) reduz o volume de telemetria sem afetar as estatísticas. Ele mantém juntos os pontos de dados relacionados para que você possa navegar entre eles ao diagnosticar um problema. No portal, as contagens totais são multiplicadas para compensar a amostragem.
* A filtragem com processadores de telemetria permite filtrar a telemetria no SDK antes que ele seja enviado ao servidor. Por exemplo, você pode reduzir o volume de telemetria excluindo as solicitações de robôs. A filtragem é uma abordagem mais básica para reduzir o tráfego do que a amostragem. Ele permite mais controle sobre o que é transmitido, mas afeta suas estatísticas. Por exemplo, você pode filtrar todas as solicitações bem-sucedidas.
* [Inicializadores de telemetria adicionam ou modificam Propriedades](#add-properties) para qualquer telemetria enviada de seu aplicativo, o que inclui a telemetria dos módulos padrão. Por exemplo, você pode adicionar valores calculados ou números de versão por meio dos quais filtrar os dados no Portal.
* [A API do SDK](./api-custom-events-metrics.md) é usada para enviar eventos personalizados e métricas.

Antes de começar:

* Instale o SDK apropriado para seu aplicativo: [ASP.net](asp-net.md), [ASP.NET Core](asp-net-core.md), [não http/Worker para .net/.NET Core](worker-service.md)ou [JavaScript](javascript.md).

<a name="filtering"></a>

## <a name="filtering"></a>Filtragem

Essa técnica oferece controle direto sobre o que está incluído ou excluído do fluxo de telemetria. A filtragem pode ser usada para descartar itens de telemetria de serem enviados para Application Insights. Você pode usar a filtragem em conjunto com a amostragem, ou separadamente.

Para filtrar a telemetria, você escreve um processador de telemetria e o registra com `TelemetryConfiguration` . Toda a telemetria passa pelo processador. Você pode optar por descartá-lo do fluxo ou fornecê-lo ao próximo processador na cadeia. A telemetria dos módulos padrão, como o coletor de solicitação HTTP e o coletor de dependência, e a telemetria que você rastreou por conta própria está incluída. Por exemplo, você pode filtrar a telemetria sobre solicitações de robôs ou chamadas de dependências bem-sucedidas.

> [!WARNING]
> Filtrar a telemetria enviada do SDK usando processadores pode distorcer as estatísticas que você vê no portal e dificultar a seguir os itens relacionados.
>
> Em vez disso, considere usar a [amostragem](./sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Criar um processador de telemetria (C#)

1. Para criar um filtro, implemente `ITelemetryProcessor` .

    Os processadores de telemetria constroem uma cadeia de processamento. Ao criar uma instância de um processador de telemetria, você recebe uma referência para o próximo processador na cadeia. Quando um ponto de dados de telemetria é passado para o método Process, ele faz seu trabalho e, em seguida, chama (ou não chama) o próximo processador de telemetria na cadeia.

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. Adicione seu processador.

**Aplicativos** ASP.net

Inserir este trecho de código no ApplicationInsights.config:

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

Você pode transmitir valores de cadeia de caracteres do arquivo .config fornecendo propriedades nomeadas públicas em sua classe.

> [!WARNING]
> Fique atento para que o nome do tipo e todos os nomes de propriedade no arquivo .config correspondam aos nomes de classe e de propriedade no código. Se o arquivo. config fizer referência a um tipo ou propriedade inexistente, o SDK poderá falhar silenciosamente ao enviar qualquer telemetria.
>

Como alternativa, é possível inicializar o filtro no código. Em uma classe de inicialização adequada, por exemplo, AppStart no `Global.asax.cs` , insira o processador na cadeia:

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

Os clientes de telemetria criados após esse ponto usarão os processadores.

Aplicativos de serviço do ASP.NET **Core/Worker**

> [!NOTE]
> Adicionar um processador usando `ApplicationInsights.config` o ou `TelemetryConfiguration.Active` o não é válido para ASP.NET Core aplicativos ou se você estiver usando o SDK Microsoft. ApplicationInsights. WorkerService.

Para aplicativos escritos usando [ASP.NET Core](asp-net-core.md#adding-telemetry-processors) ou [WorkerService](worker-service.md#adding-telemetry-processors), a adição de um novo processador de telemetria é feita usando o `AddApplicationInsightsTelemetryProcessor` método de extensão em `IServiceCollection` , conforme mostrado. Esse método é chamado no `ConfigureServices` método de sua `Startup.cs` classe.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>Filtros de exemplo

#### <a name="synthetic-requests"></a>Solicitações sintéticas

Filtre os bots e os testes Web. Embora Metrics Explorer ofereça a opção de filtrar fontes sintéticas, essa opção reduz o tráfego e o tamanho de ingestão filtrando-os no próprio SDK.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>Autenticação com falha

Filtre as solicitações com uma resposta "401".

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrar chamadas de dependência rápidas remotas

Se você quiser diagnosticar apenas chamadas lentas, filtre as rápidas.

> [!NOTE]
> Essa filtragem irá distorcer as estatísticas que você vê no Portal.
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>Diagnosticar problemas de dependência

[Este blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) descreve um projeto para diagnosticar problemas de dependência ao enviar automaticamente pings regulares para as dependências.

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>Aplicativos Web do JavaScript

**Filtrar usando ITelemetryInitializer**

1. Crie uma função de retorno de chamada do inicializador de telemetria. A função de retorno de chamada usa `ITelemetryItem` como um parâmetro, que é o evento que está sendo processado. Retornar deste `false` retorno de chamada resulta no item de telemetria a ser filtrado.

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
         return false;
     }
  
     return true;
   };
   ```

2. Adicione o retorno de chamada do inicializador de telemetria:

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>Adicionar/modificar propriedades: ITelemetryInitializer

Use inicializadores de telemetria para enriquecer a telemetria com informações adicionais ou para substituir as propriedades de telemetria definidas pelos módulos de telemetria padrão.

Por exemplo, Application Insights para um pacote da Web coleta a telemetria sobre solicitações HTTP. Por padrão, ele sinaliza como falha qualquer solicitação com um código de resposta >= 400. Mas se você quiser tratar 400 como sucesso, você pode fornecer um inicializador de telemetria que define a propriedade Success.

Se você fornecer um inicializador de telemetria, ele será chamado sempre que qualquer um dos métodos Track * () for chamado. Isso inclui `Track()` métodos chamados pelos módulos de telemetria padrão. Por convenção, esses módulos não definem nenhuma propriedade que já foi definida por um inicializador. Inicializadores de telemetria são chamados antes de chamar processadores de telemetria. Portanto, todos os aprimoramentos feitos por inicializadores são visíveis para os processadores.

**Definir seu inicializador**

*C#*

```csharp
using System;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that overrides the default SDK
   * behavior of treating response codes >= 400 as failed requests
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        // Is this a TrackRequest() ?
        if (requestTelemetry == null) return;
        int code;
        bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
        if (!parsed) return;
        if (code >= 400 && code < 500)
        {
            // If we set the Success property, the SDK won't change it:
            requestTelemetry.Success = true;

            // Allow us to filter these requests in the portal:
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

**Aplicativos ASP.net: carregar seu inicializador**

Em ApplicationInsights.config:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

Como alternativa, você pode instanciar o inicializador no código, por exemplo, em global. aspx. cs:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

Veja mais deste [exemplo](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole).

**Aplicativos de serviço do ASP.NET Core/Worker: carregar seu inicializador**

> [!NOTE]
> Adicionar um inicializador usando `ApplicationInsights.config` `TelemetryConfiguration.Active` o ou o não é válido para aplicativos ASP.NET Core ou se você estiver usando o SDK Microsoft. ApplicationInsights. WorkerService.

Para aplicativos escritos usando [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) ou [WorkerService](worker-service.md#adding-telemetryinitializers), adicionar um novo inicializador de telemetria é feito adicionando-o ao contêiner de injeção de dependência, conforme mostrado. Isso é feito no `Startup.ConfigureServices` método.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```
### <a name="javascript-telemetry-initializers"></a>Inicializadores de telemetria JavaScript
*JavaScript*

Insira um inicializador de telemetria logo após o código de inicialização que você obteve do portal:

```JS
<script type="text/javascript">
    // ... initialization code
    ...({
        instrumentationKey: "your instrumentation key"
    });
    window.appInsights = appInsights;


    // Adding telemetry initializer.
    // This is called whenever a new telemetry item
    // is created.

    appInsights.addTelemetryInitializer(function (envelope) {
        var telemetryItem = envelope.data.baseData;

        // To check the telemetry items type - for example PageView:
        if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
            // this statement removes url from all page view documents
            telemetryItem.url = "URL CENSORED";
        }

        // To set custom properties:
        telemetryItem.properties = telemetryItem.properties || {};
        telemetryItem.properties["globalProperty"] = "boo";
        
        // To set cloud role name / instance
        envelope.tags["ai.cloud.role"] = "your role name";
        envelope.tags["ai.cloud.roleInstance"] = "your role instance";
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

Para obter um resumo das propriedades não personalizadas disponíveis no item de telemetria, consulte [Application insights modelo de dados de exportação](./export-data-model.md).

Você pode adicionar quantos inicializadores desejar. Eles são chamados na ordem em que são adicionados.

### <a name="opencensus-python-telemetry-processors"></a>Processadores de telemetria do OpenCensus Python

Os processadores de telemetria no Python OpenCensus são simplesmente funções de retorno de chamada chamadas para processar a telemetria antes de serem exportadas. A função de retorno de chamada deve aceitar um tipo de dados de [envelope](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86) como seu parâmetro. Para filtrar a telemetria de ser exportada, verifique se a função de retorno de chamada retorna `False` . Você pode ver o esquema para Azure Monitor tipos de dados nos envelopes [no GitHub](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py).

> [!NOTE]
> Você pode modificar `cloud_RoleName` alterando o `ai.cloud.role` atributo no `tags` campo.

```python
def callback_function(envelope):
    envelope.tags['ai.cloud.role'] = 'new_role_name'
```

```python
# Example for log exporter
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)

# Callback function to append '_hello' to each log message telemetry
def callback_function(envelope):
    envelope.data.baseData.message += '_hello'
    return True

handler = AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>')
handler.add_telemetry_processor(callback_function)
logger.addHandler(handler)
logger.warning('Hello, World!')
```
```python
# Example for trace exporter
import requests

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])

# Callback function to add os_type: linux to span properties
def callback_function(envelope):
    envelope.data.baseData.properties['os_type'] = 'linux'
    return True

exporter = AzureExporter(
    connection_string='InstrumentationKey=<your-instrumentation-key-here>'
)
exporter.add_telemetry_processor(callback_function)
tracer = Tracer(exporter=exporter, sampler=ProbabilitySampler(1.0))
with tracer.span(name='parent'):
response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit')
```

```python
# Example for metrics exporter
import time

from opencensus.ext.azure import metrics_exporter
from opencensus.stats import aggregation as aggregation_module
from opencensus.stats import measure as measure_module
from opencensus.stats import stats as stats_module
from opencensus.stats import view as view_module
from opencensus.tags import tag_map as tag_map_module

stats = stats_module.stats
view_manager = stats.view_manager
stats_recorder = stats.stats_recorder

CARROTS_MEASURE = measure_module.MeasureInt("carrots",
                                            "number of carrots",
                                            "carrots")
CARROTS_VIEW = view_module.View("carrots_view",
                                "number of carrots",
                                [],
                                CARROTS_MEASURE,
                                aggregation_module.CountAggregation())

# Callback function to only export the metric if value is greater than 0
def callback_function(envelope):
    return envelope.data.baseData.metrics[0].value > 0

def main():
    # Enable metrics
    # Set the interval in seconds in which you want to send metrics
    exporter = metrics_exporter.new_metrics_exporter(connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    exporter.add_telemetry_processor(callback_function)
    view_manager.register_exporter(exporter)

    view_manager.register_view(CARROTS_VIEW)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    mmap.measure_int_put(CARROTS_MEASURE, 1000)
    mmap.record(tmap)
    # Default export interval is every 15.0s
    # Your application should run for at least this amount
    # of time so the exporter will meet this interval
    # Sleep can fulfill this
    time.sleep(60)

    print("Done recording metrics")

if __name__ == "__main__":
    main()
```
Você pode adicionar quantos processadores desejar. Eles são chamados na ordem em que são adicionados. Se um processador lançar uma exceção, ele não afetará os processadores a seguir.

### <a name="example-telemetryinitializers"></a>Exemplo de TelemetryInitializers

#### <a name="add-a-custom-property"></a>Adicionar uma propriedade personalizada

O inicializador de exemplo a seguir adiciona uma propriedade personalizada a cada telemetria rastreada.

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.Properties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-a-cloud-role-name"></a>Adicionar um nome de função de nuvem

O inicializador de exemplo a seguir define o nome da função de nuvem para cada telemetria rastreada.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

#### <a name="add-information-from-httpcontext"></a>Adicionar informações de HttpContext

O inicializador de exemplo a seguir lê dados de [`HttpContext`](/aspnet/core/fundamentals/http-context) e anexa-os a uma `RequestTelemetry` instância do. O `IHttpContextAccessor` é fornecido automaticamente por injeção de dependência de construtor.

```csharp
public class HttpContextRequestTelemetryInitializer : ITelemetryInitializer
{
    private readonly IHttpContextAccessor httpContextAccessor;

    public HttpContextRequestTelemetryInitializer(IHttpContextAccessor httpContextAccessor)
    {
        this.httpContextAccessor =
            httpContextAccessor ??
            throw new ArgumentNullException(nameof(httpContextAccessor));
    }

    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        if (requestTelemetry == null) return;

        var claims = this.httpContextAccessor.HttpContext.User.Claims;
        Claim oidClaim = claims.FirstOrDefault(claim => claim.Type == "oid");
        requestTelemetry.Properties.Add("UserOid", oidClaim?.Value);
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor e ITelemetryInitializer

Qual é a diferença entre os processadores de telemetria e inicializadores de telemetria?

* Há algumas sobreposições no que você pode fazer com elas. Ambos podem ser usados para adicionar ou modificar propriedades de telemetria, embora seja recomendável usar inicializadores para essa finalidade.
* Os inicializadores de telemetria sempre são executados antes dos processadores de telemetria.
* Inicializadores de telemetria podem ser chamados mais de uma vez. Por convenção, eles não definem nenhuma propriedade já definida.
* Os processadores de telemetria permitem que você substitua completamente ou descarte um item de telemetria.
* Todos os inicializadores de telemetria registrados têm a garantia de serem chamados para cada item de telemetria. Para processadores de telemetria, o SDK garante a chamada do primeiro processador de telemetria. Se o restante dos processadores é chamado ou não é decidido pelos processadores de telemetria anteriores.
* Use inicializadores de telemetria para enriquecer a telemetria com propriedades adicionais ou substituir uma existente. Use um processador de telemetria para filtrar a telemetria.

## <a name="troubleshoot-applicationinsightsconfig"></a>Solucionar problemas ApplicationInsights.config

* Confirme se o nome do tipo totalmente qualificado e o nome do assembly estão corretos.
* Confirme se o arquivo applicationinsights. config está no seu diretório de saída e contém todas as alterações recentes.

## <a name="reference-docs"></a>Documentos de Referência

* [Visão geral da API](./api-custom-events-metrics.md)
* [Referência do ASP.NET](/previous-versions/azure/dn817570(v=azure.100))

## <a name="sdk-code"></a>Código do SDK

* [SDK de Núcleo do ASP.NET](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [SDK do ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [SDK do JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next-steps"></a><a name="next"></a>Próximas etapas
* [Pesquisar eventos e logs](./diagnostic-search.md)
* [exemplos](./sampling.md)
* [Solução de problemas](../faq.md)

