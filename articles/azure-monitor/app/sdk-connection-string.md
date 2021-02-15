---
title: Cadeias de conexão no Aplicativo Azure insights | Microsoft Docs
description: Como usar cadeias de conexão.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.custom: devx-track-js, devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: f4227c28329233c7f414c6c45e4a3c1420bf47be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335153"
---
# <a name="connection-strings"></a>Cadeias de conexão

## <a name="overview"></a>Visão geral

As cadeias de conexão fornecem aos usuários do Application Insight um único parâmetro de configuração, eliminando a necessidade de várias configurações de proxy. Altamente útil para servidores Web da intranet, soberanas ou ambientes de nuvem híbridas que procuram enviar dados para o serviço de monitoramento.

Os pares chave-valor fornecem uma maneira fácil para os usuários definirem uma combinação de sufixo de prefixo para cada serviço/produto de Application Insights (ia).

> [!IMPORTANT]
> Não é recomendável definir a cadeia de conexão e a chave de instrumentação. Caso um usuário defina ambos, o que foi definido por último terá precedência. 


## <a name="scenario-overview"></a>Visão geral do cenário 

Os cenários de clientes nos quais visualizamos isso tem o maior impacto:

- Exceções de firewall ou redirecionamentos de proxy 

    Nos casos em que o monitoramento do servidor Web da intranet é necessário, nossa solução anterior solicitou que os clientes adicionassem pontos de extremidade de serviço individuais à sua configuração. Para saber mais, clique [aqui](../faq.md#can-i-monitor-an-intranet-web-server). 
    As cadeias de conexão oferecem uma alternativa melhor, reduzindo esse esforço para uma única configuração. Um prefixo simples, emenda de sufixo, permite preenchimento automático e redirecionamento de todos os pontos de extremidade para os serviços corretos. 

- Ambientes de nuvem híbrida ou soberanas

    Os usuários podem enviar dados para uma [região do Azure governamental](../../azure-government/compare-azure-government-global-azure.md#application-insights)definida.
    As cadeias de conexão permitem definir configurações de ponto de extremidade para seus servidores de intranet ou configurações de nuvem híbrida. 

## <a name="getting-started"></a>Introdução

### <a name="finding-my-connection-string"></a>Localizando minha cadeia de conexão?

Sua cadeia de conexão é exibida na folha visão geral do recurso de Application Insights.

![Cadeia de conexão na folha visão geral](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>Esquema

#### <a name="max-length"></a>Comprimento máximo

A conexão tem um comprimento máximo com suporte de 4096 caracteres.

#### <a name="key-value-pairs"></a>Pares de chave-valor

A cadeia de conexão consiste em uma lista de configurações representadas como pares chave-valor separados por ponto e vírgula: `key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>Sintaxe

- `InstrumentationKey` (ex: 00000000-0000-0000-0000-000000000000)  A cadeia de conexão é um campo **obrigatório** .
- `Authorization` (ex: iKey) (Essa configuração é opcional porque hoje só damos suporte à autorização iKey.)
- `EndpointSuffix` (ex: applicationinsights.azure.cn) Definir o sufixo do ponto de extremidade instruirá o SDK ao qual a nuvem do Azure se conectará. O SDK montará o restante do ponto de extremidade para serviços individuais.
- Pontos de extremidade explícitos.
  Qualquer serviço pode ser explicitamente substituído na cadeia de conexão.
   - `IngestionEndpoint` (ex: `https://dc.applicationinsights.azure.com` )
   - `LiveEndpoint` (ex: `https://live.applicationinsights.azure.com` )
   - `ProfilerEndpoint` (ex: `https://profiler.applicationinsights.azure.com` )
   - `SnapshotEndpoint` (ex: `https://snapshot.applicationinsights.azure.com` )

#### <a name="endpoint-schema"></a>Esquema de ponto de extremidade

`<prefix>.<suffix>`
- Prefixo: define um serviço. 
- Sufixo: define o nome de domínio comum.

##### <a name="valid-suffixes"></a>Sufixos válidos

Aqui está uma lista de sufixos válidos 
- applicationinsights.azure.cn
- applicationinsights.us


Consulte também: https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>Prefixos válidos

- [Ingestão de telemetria](./app-insights-overview.md): `dc`
- [Métricas ao vivo](./live-stream.md): `live`
- [Criador de perfil](./profiler-overview.md): `profiler`
- [Instantâneo](./snapshot-debugger.md): `snapshot`



## <a name="connection-string-examples"></a>Exemplos de cadeia de conexão


### <a name="minimal-valid-connection-string"></a>Cadeia de conexão mínima válida

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

Neste exemplo, somente a chave de instrumentação foi definida.

- O esquema de autorização assume como padrão "iKey" 
- Chave de instrumentação: 00000000-0000-0000-0000-000000000000
- Os URIs de serviço regional baseiam-se nos [padrões do SDK](https://github.com/microsoft/ApplicationInsights-dotnet/blob/e50d569cebf485e72e98f4a08a0bc0e30cdf42bc/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs#L6) e se conectarão ao Azure global público:
   - Ingestão `https://dc.services.visualstudio.com/`
   - Métricas ao vivo: `https://rt.services.visualstudio.com/`
   - Profiler `https://agent.azureserviceprofiler.net/`
   - Depurador `https://agent.azureserviceprofiler.net/`



### <a name="connection-string-with-endpoint-suffix"></a>Cadeia de conexão com sufixo de ponto de extremidade

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

Neste exemplo, essa cadeia de conexão especifica o sufixo do ponto de extremidade e o SDK criará pontos de extremidades de serviço.

- O esquema de autorização assume como padrão "iKey" 
- Chave de instrumentação: 00000000-0000-0000-0000-000000000000
- Os URIs de serviço regional se baseiam no sufixo de ponto de extremidade fornecido: 
   - Ingestão `https://dc.ai.contoso.com`
   - Métricas ao vivo: `https://live.ai.contoso.com`
   - Profiler `https://profiler.ai.contoso.com`
   - Depurador `https://snapshot.ai.contoso.com`  



### <a name="connection-string-with-explicit-endpoint-overrides"></a>Cadeia de conexão com substituições de ponto de extremidade explícitas 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

Neste exemplo, essa cadeia de conexão especifica substituições explícitas para cada serviço. O SDK usará os pontos de extremidade exatos fornecidos sem modificação.

- O esquema de autorização assume como padrão "iKey" 
- Chave de instrumentação: 00000000-0000-0000-0000-000000000000
- Os URIs de serviço regional baseiam-se nos valores de substituição explícitos: 
   - Ingestão `https://custom.com:111/`
   - Métricas ao vivo: `https://custom.com:222/`
   - Profiler `https://custom.com:333/`
   - Depurador `https://custom.com:444/`  


## <a name="how-to-set-a-connection-string"></a>Como definir uma cadeia de conexão

Há suporte para cadeias de conexão nas seguintes versões do SDK:
- .NET e .NET Core v 2.12.0
- Java v 2.5.1 e Java 3,0
- JavaScript v 2.3.0
- NodeJS v 1.5.0
- Python v 1.0.0

Uma cadeia de conexão pode ser definida no código, na variável de ambiente ou no arquivo de configuração.



### <a name="environment-variable"></a>Variável de ambiente

- Cadeia de conexão: `APPLICATIONINSIGHTS_CONNECTION_STRING`

# <a name="netnetcore"></a>[.NET/. NetCore](#tab/net)

TelemetryConfiguration. ConnectionString: https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274

.NET definido explicitamente:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

Arquivo de configuração .NET:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```

NetCore definido explicitamente:
```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions { ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;" };
    services.AddApplicationInsightsTelemetry(options: options);
}
```

O NetCore config.jsem: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


# <a name="java"></a>[Java](#tab/java)


Java (v 2.5. x) definido explicitamente:
```java
TelemetryConfiguration.getActive().setConnectionString("InstrumentationKey=00000000-0000-0000-0000-000000000000");
```

ApplicationInsights.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000;</ConnectionString>
</ApplicationInsights>
```

# <a name="javascript"></a>[JavaScript](#tab/js)

Importante: o JavaScript não dá suporte ao uso de variáveis de ambiente.

Usando o trecho de código:

```javascript
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("addTelemetryInitializer"),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),t.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```


Configuração manual:
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

# <a name="python"></a>[Python](#tab/python)

Recomendamos que os usuários definam a variável de ambiente.

Para definir explicitamente a cadeia de conexão:

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```


## <a name="next-steps"></a>Próximas etapas

Introdução ao runtime com:

* [Aplicativos hospedados no IIS na VM do Azure e no conjunto de dimensionamento de máquinas virtuais do Azure](./azure-vm-vmss-apps.md)
* [Servidor IIS](./monitor-performance-live-website-now.md)
* [Aplicativos Web do Azure](./azure-web-apps.md)

Introdução ao tempo de desenvolvimento com:

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-get-started.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)

