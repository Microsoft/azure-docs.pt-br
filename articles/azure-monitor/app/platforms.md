---
title: 'Application Insights: linguagens, plataformas e integrações | Microsoft Docs'
description: Linguagens, plataformas e integrações disponíveis para Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: dd2fb0c9ef157e3706ca3d902f7feebaf4c2f279
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478902"
---
# <a name="supported-languages"></a>Idiomas com suporte

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)

## <a name="supported-platforms-and-frameworks"></a>Plataformas e estruturas compatíveis

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Instrumentação para aplicativos já implantados (sem código, baseados em agente)
* [Conjuntos de dimensionamento de máquinas virtuais do Azure e VM do Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Serviço de Aplicativo do Azure](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET – para aplicativos que já estejam ao vivo](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Serviços de nuvem do Azure](../../azure-monitor/app/cloudservices.md), incluindo funções Web e de trabalho
* [Funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>Instrumentação por meio de código (SDKs)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Aplicativo Universal do Windows](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Funções de trabalho, serviços e aplicativos da área de trabalho do Windows](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>Estruturas de registro em log
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net, NLog ou System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J ou Logback](../../azure-monitor/app/java-trace-logs.md)
* [Plug-in LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>Análise de dados e exportação
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-community-sdks"></a>SDKs de comunidade sem suporte

Há uma série de SDKs de Application Insights da Comunidade. Os SDKs da Comunidade não são oficialmente compatíveis/mantidos pela Microsoft. Não é possível fornecer suporte para nenhum SDK que não esteja na lista oficialmente suportada. 