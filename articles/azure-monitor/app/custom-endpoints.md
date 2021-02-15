---
title: Aplicativo Azure insights substituir pontos de extremidade do SDK padrão
description: Modifique o Azure Monitor padrão Application Insights pontos de extremidade do SDK para regiões como o Azure governamental.
ms.topic: conceptual
ms.date: 07/26/2019
ms.custom: references_regions, devx-track-js
ms.openlocfilehash: d6cea9044cd4898480fcc30532a05e6c8a407012
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333283"
---
# <a name="application-insights-overriding-default-endpoints"></a>Application Insights substituindo pontos de extremidade padrão

Para enviar dados de Application Insights para determinadas regiões, você precisará substituir os endereços de ponto de extremidade padrão. Cada SDK requer modificações ligeiramente diferentes, todas descritas neste artigo. Essas alterações exigem o ajuste do código de exemplo e a substituição dos valores de espaço reservado para `QuickPulse_Endpoint_Address` , `TelemetryChannel_Endpoint_Address` e `Profile_Query_Endpoint_address` pelos endereços de ponto de extremidade reais para sua região específica. O fim deste artigo contém links para os endereços de ponto de extremidade para regiões em que essa configuração é necessária.

> [!NOTE]
> [Cadeias de conexão](./sdk-connection-string.md?tabs=net) são o novo método preferencial de configuração de pontos de extremidade personalizados dentro de Application insights.

---

## <a name="sdk-code-changes"></a>Alterações de código do SDK

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> O arquivo de applicationinsights.config é substituído automaticamente sempre que uma atualização do SDK é executada. Depois de executar uma atualização do SDK, certifique-se de inserir novamente os valores de ponto de extremidade específicos da região.

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>Custom_QuickPulse_Endpoint_Address</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>Profile_Query_Endpoint_address</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Modifique o appsettings.jsno arquivo em seu projeto da seguinte maneira para ajustar o ponto de extremidade principal:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

Os valores para métricas ao vivo e o ponto de extremidade de consulta de perfil só podem ser definidos por meio de código. Para substituir os valores padrão para todos os valores de ponto de extremidade por meio de código, faça as seguintes alterações no `ConfigureServices` método do `Startup.cs` arquivo:

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //Place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //Place in the ConfigureServices method. Place this before services.AddApplicationInsightsTelemetry("instrumentation key"); if it's present
```

# <a name="azure-functions"></a>[Azure Functions](#tab/functions)

Por Azure Functions agora é recomendável usar [cadeias de conexão](./sdk-connection-string.md?tabs=net) definidas nas configurações do aplicativo da função. Para acessar as configurações do aplicativo para sua função no painel funções, selecione **configurações**configurações do aplicativo de  >  **configuração**  >  **Application settings**. 

Nome: `APPLICATIONINSIGHTS_CONNECTION_STRING` valor: `Connection String Value`

# <a name="java"></a>[Java](#tab/java)

Modifique o arquivo de applicationinsights.xml para alterar o endereço do ponto de extremidade padrão.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

Modifique o `application.properties` arquivo e adicione:

```yaml
azure.application-insights.channel.in-process.endpoint-address= TelemetryChannel_Endpoint_Address
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "TelemetryChannel_Endpoint_Address"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "Profile_Query_Endpoint_address"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "QuickPulse_Endpoint_Address"; //live metrics
appInsights.Configuration.start();
```

Os pontos de extremidade também podem ser configurados por meio de variáveis de ambiente:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "Profile_Query_Endpoint_address"
Live Metrics Endpoint: "QuickPulse_Endpoint_Address"
```

# <a name="javascript"></a>[JavaScript](#tab/js)

```javascript
<script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      endpointUrl: "TelemetryChannel_Endpoint_Address"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

# <a name="python"></a>[Python](#tab/python)

Para obter orientação sobre como modificar o ponto de extremidade de ingestão para o SDK do opencensus-Python, consulte o [repositório opencensus-Python.](https://github.com/census-instrumentation/opencensus-python/blob/af284a92b80bcbaf5db53e7e0813f96691b4c696/contrib/opencensus-ext-azure/opencensus/ext/azure/common/__init__.py)

---

## <a name="regions-that-require-endpoint-modification"></a>Regiões que exigem modificação de ponto de extremidade

Atualmente, as únicas regiões que exigem modificações de ponto de extremidade são o [Azure governamental](../../azure-government/compare-azure-government-global-azure.md#application-insights) e o [Azure China](/azure/china/resources-developer-guide).

|Região |  Nome do Ponto de Extremidade | Valor |
|-----------------|:------------|:-------------|
| Azure China | Canal de telemetria | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure China | QuickPulse (métricas ao vivo) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure China | Consulta de perfil |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Government | Canal de telemetria |`https://dc.applicationinsights.us/v2/track` |
| Azure Government | QuickPulse (métricas ao vivo) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Government | Consulta de perfil |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

Se você usar atualmente a [API REST Application insights](https://dev.applicationinsights.io/
) que normalmente é acessada por meio de ' API.applicationinsights.Io ', será necessário usar um ponto de extremidade que seja local para sua região:

|Região |  Nome do Ponto de Extremidade | Valor |
|-----------------|:------------|:-------------|
| Azure China | API REST | `api.applicationinsights.azure.cn` |
| Azure Government | API REST | `api.applicationinsights.us`|

> [!NOTE]
> **Não há suporte** para o monitoramento baseado em agente/extensão sem código para serviços Azure apps nessas regiões. Assim que essa funcionalidade for disponibilizada, este artigo será atualizado.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre as modificações personalizadas do Azure governamental, consulte as diretrizes detalhadas para [monitoramento e gerenciamento do Azure](../../azure-government/compare-azure-government-global-azure.md#application-insights).
- Para saber mais sobre o Azure China, consulte o [manual do Azure na China](/azure/china/).
