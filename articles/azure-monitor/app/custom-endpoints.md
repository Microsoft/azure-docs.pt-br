---
title: As informações de Azure Monitor Aplicativo Azure substituem os pontos de extremidade do SDK padrão | Microsoft Docs
description: Modifique os pontos de extremidade do SDK do Aplicativo Azure insights padrão para regiões como o Azure governamental.
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: mbullwin
ms.openlocfilehash: c086f94a161853cba3a9ed2b98f13ea17b90dd20
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478808"
---
 # <a name="application-insights-overriding-default-endpoints"></a>Application Insights substituindo pontos de extremidade padrão

Para enviar dados de Application Insights para determinadas regiões, você precisará substituir os endereços de ponto de extremidade padrão. Cada SDK requer modificações ligeiramente diferentes, todas descritas neste artigo. Essas alterações exigem o ajuste do código de exemplo e a substituição dos `QuickPulse_Endpoint_Address`valores `TelemetryChannel_Endpoint_Address`de espaço `Profile_Query_Endpoint_address` reservado para, e pelos endereços de ponto de extremidade reais para sua região específica. O fim deste artigo contém links para os endereços de ponto de extremidade para regiões em que essa configuração é necessária.

## <a name="sdk-code-changes"></a>Alterações de código do SDK

### <a name="net-with-applicationinsightsconfig"></a>.NET com applicationinsights. config

> [!NOTE]
> O arquivo applicationinsights. config é substituído automaticamente sempre que uma atualização do SDK é executada. Depois de executar uma atualização do SDK, certifique-se de inserir novamente os valores de ponto de extremidade específicos da região.

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

### <a name="net-core"></a>.NET Core

Modifique o arquivo appSettings. JSON em seu projeto da seguinte maneira para ajustar o ponto de extremidade principal:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

Os valores para métricas ao vivo e o ponto de extremidade de consulta de perfil só podem ser definidos por meio de código. Para substituir os valores padrão para todos os valores de ponto de extremidade por meio de código, `ConfigureServices` faça as seguintes `Startup.cs` alterações no método do arquivo:

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton(new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //place in ConfigureServices method. If present, place this prior to   services.AddApplicationInsightsTelemetry("instrumentation key");
```

### <a name="java"></a>Java

Modifique o arquivo applicationinsights. xml para alterar o endereço do ponto de extremidade padrão.

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

### <a name="nodejs"></a>Node.js

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

### <a name="javascript"></a>JavaScript

```javascript
<script type="text/javascript">
   var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){
      function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/next/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t
   }({
      instrumentationKey:"INSTRUMENTATION_KEY"
      endpointUrl: "TelemetryChannel_Endpoint_Address"
   });

   window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

## <a name="regions-that-require-endpoint-modification"></a>Regiões que exigem modificação de ponto de extremidade

Atualmente, as únicas regiões que exigem modificações de ponto de extremidade são o [Azure governamental](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights) e o [Azure China](https://docs.microsoft.com/azure/china/resources-developer-guide).

|Região |  Nome do Ponto de Extremidade | Valor |
|-----------------|:------------|:-------------|
| Azure China | Canal de telemetria | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure China | QuickPulse (métricas ao vivo) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure China | Consulta de perfil |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Governamental | Canal de telemetria |`https://dc.applicationinsights.us/v2/track` |
| Azure Governamental | QuickPulse (métricas ao vivo) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Governamental | Consulta de perfil |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

> [!NOTE]
> **Não há suporte** para o monitoramento baseado em agente/extensão sem código para serviços Azure apps nessas regiões. Assim que essa funcionalidade for disponibilizada, este artigo será atualizado.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre as modificações personalizadas do Azure governamental, consulte as diretrizes detalhadas para [monitoramento e gerenciamento do Azure](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights).
- Para saber mais sobre o Azure China, consulte o [manual do Azure na China](https://docs.microsoft.com/azure/china/).