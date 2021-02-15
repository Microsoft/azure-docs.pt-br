---
title: 'Application Insights: linguagens, plataformas e integrações | Microsoft Docs'
description: Linguagens, plataformas e integrações disponíveis para Application Insights
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: d3bb1473d94db04d1dedf2b09e19b83197cc6e0e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88225097"
---
# <a name="supported-languages"></a>Idiomas com suporte

* [C#|VB (.NET)](./asp-net.md)
* [Java](./java-get-started.md)
* [JavaScript](./javascript.md)
* [Node.JS](./nodejs.md)
* [Python](./opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Plataformas e estruturas compatíveis

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Instrumentação para aplicativos já implantados (sem código, baseados em agente)
* [Conjuntos de dimensionamento de máquinas virtuais do Azure e VM do Azure](./azure-vm-vmss-apps.md)
* [Serviço de Aplicativo do Azure](./azure-web-apps.md)
* [ASP.NET – para aplicativos que já estejam ao vivo](./monitor-performance-live-website-now.md)
* [Serviços de nuvem do Azure](./cloudservices.md), incluindo funções Web e de trabalho
* [Azure Functions](../../azure-functions/functions-monitoring.md)
### <a name="instrumentation-through-code-sdks"></a>Instrumentação por meio de código (SDKs)
* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Android](../learn/mobile-center-quickstart.md) (App Center)
* [iOS](../learn/mobile-center-quickstart.md) (App Center)
* [Java EE](./java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python](./opencensus-python.md)
* [Aplicativo Universal do Windows](../learn/mobile-center-quickstart.md) (App Center)
* [Funções de trabalho, serviços e aplicativos da área de trabalho do Windows](./windows-desktop.md)
* [React](./javascript-react-plugin.md)
* [React Native](./javascript-react-native-plugin.md)

## <a name="logging-frameworks"></a>Estruturas de registro em log
* [ILogger](./ilogger.md)
* [Log4Net, NLog ou System.Diagnostics.Trace](./asp-net-trace-logs.md)
* [Java, Log4J ou Logback](./java-trace-logs.md)
* [Plug-in LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](/archive/blogs/msoms/application-insights-connector-in-oms)

## <a name="export-and-data-analysis"></a>Análise de dados e exportação
* [Power BI](https://powerbi.microsoft.com/blog/explore-your-application-insights-data-with-power-bi/)
* [Stream Analytics](./export-power-bi.md)

## <a name="unsupported-sdks"></a>SDKs sem suporte
Estamos cientes de que existem vários outros SDKs com suporte da Comunidade. No entanto, Azure Monitor só fornece suporte ao usar os SDKs com suporte listados nesta página. Estamos constantemente avaliando as oportunidades de expandir nosso suporte para outras linguagens, então siga nossa página de [anúncios do GitHub](https://github.com/microsoft/ApplicationInsights-Announcements/issues) para receber as notícias mais recentes do SDK. 

