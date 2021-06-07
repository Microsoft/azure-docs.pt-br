---
title: Cadeias de conexão no Aplicativo Azure insights | Microsoft Docs
description: Como usar cadeias de conexão.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.custom: devx-track-js, devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: df87b060423aeff9fa5f83f21634395fe30e0bbb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102486277"
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

    Nos casos em que o monitoramento do servidor Web da intranet é necessário, nossa solução anterior solicitou que os clientes adicionassem pontos de extremidade de serviço individuais à sua configuração. Para mais informações, consulte [aqui](../faq.md#can-i-monitor-an-intranet-web-server). 
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
   - `ProfilerEndpoint` (ex: `https://profiler.monitor.azure.com` )
   - `SnapshotEndpoint` (ex: `https://snapshot.monitor.azure.com` )

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
- Os URIs de serviço regional baseiam-se nos [padrões do SDK](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs) e se conectarão ao Azure global público:
   - Ingestão `https://dc.services.visualstudio.com/`
   - Métricas ao vivo: `https://rt.services.visualstudio.com/`
   - Profiler `https://profiler.monitor.azure.com/`
   - Depurador `https://snapshot.monitor.azure.com/`



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

### <a name="code-samples"></a>Exemplos de código

# <a name="netnetcore"></a>[.NET/. NetCore](#tab/net)

Defina a propriedade [TelemetryConfiguration. ConnectionString](https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274) ou [ApplicationInsightsServiceOptions. ConnectionString](https://github.com/microsoft/ApplicationInsights-dotnet/blob/81288f26921df1e8e713d31e7e9c2187ac9e6590/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs#L66-L69)

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

O trecho atual (listado abaixo) é a versão "5", a versão é codificada no trecho de código como VA: "#" e a [versão atual também está disponível no GitHub](https://go.microsoft.com/fwlink/?linkid=2156318).

```html
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
// name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
// ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
// useXhr: 1, // Use XHR instead of fetch to report failures (if available),
crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
// onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
cfg: { // Application Insights Configuration
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}});
</script>
```

> [!NOTE]
> Para facilitar a leitura e reduzir possíveis erros de JavaScript, todas as opções de configuração possíveis são listadas em uma nova linha no código de trecho acima, se você não quiser alterar o valor de uma linha comentada, ela poderá ser removida.

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

---

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

