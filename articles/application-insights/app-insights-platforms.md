---
title: "Application Insights: linguagens, plataformas e integrações| Microsoft Docs"
description: "Linguagens, plataformas e integrações disponíveis para o Application Insights"
services: application-insights
documentationcenter: 
author: OlegAnaniev-MSFT
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: bwren
ms.openlocfilehash: d49ad2ff584f42c0e4732a5cff60d23cdf631512
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="developer-analytics-languages-platforms-and-integrations"></a>Análise para o desenvolvedor: linguagens, plataformas e integrações
Esses itens são implementações do [Application Insights](app-insights-overview.md) sobre as quais ouvimos falar, incluindo algumas de terceiros.

## <a name="languages---officially-supported-by-application-insights-team"></a>Linguagens oficialmente suportadas pela equipe do Application Insights
* [C#|VB (.NET)](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [Páginas da Web JavaScript](app-insights-javascript.md)
* [Node.JS](app-insights-nodejs.md)

## <a name="languages---community-supported"></a>Linguagens com suporte da comunidade
* [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
* [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
* [Ruby](https://rubygems.org/gems/application_insights)
* [Qualquer outra](#projects)

## <a name="platforms-and-frameworks"></a>Plataformas e estruturas
* [Angular](https://www.npmjs.com/package/angular-applicationinsights)
* [ASP.NET](app-insights-asp-net.md)
* [ASP.NET – para aplicativos que já estejam ao vivo](app-insights-monitor-performance-live-website-now.md)
* [ASP.NET Core](app-insights-asp-net-core.md)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (HockeyApp)
* [Aplicativos Web do Azure](app-insights-azure-web-apps.md)
* [Serviços de Nuvem do Azure](app-insights-cloudservices.md)&#151; incluindo funções Web e de trabalho
* [Funções do Azure](https://github.com/christopheranderson/azure-functions-app-insights-sample)
* [Docker](app-insights-docker.md)
* [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
* [iOS](https://github.com/Microsoft/ApplicationInsights-iOS) (HockeyApp)
* [J2EE](app-insights-java-get-started.md)
* [J2EE – para aplicativos que já estejam ao vivo](app-insights-java-live.md)
* [Aplicativo para Mac OS X](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x) (HockeyApp)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
* [Spring](http://joe.blog.freemansoft.com/2015/12/enabling-microsoft-application-insight.html)
* [Aplicativo universal do Windows](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp) (HockeyApp)
* [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
* [Aplicativo para Windows Phone 8 e 8.1](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81) (HockeyApp)
* [Aplicativo Windows Presentation Foundation](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps) (HockeyApp)
* [Funções de trabalho, serviços e aplicativos da área de trabalho do Windows](app-insights-windows-desktop.md)
* [Qualquer outra](#projects)

## <a name="logging-frameworks"></a>Estruturas de registro em log
* [Log4Net, NLog ou System.Diagnostics.Trace](app-insights-diagnostic-search.md)
* [Java, Log4J ou Logback](app-insights-java-trace-logs.md)
* [Registro Semântico (SLAB)](https://github.com/fidmor89/SLAB_AppInsights) - é integrado ao [Bloco de Aplicativos do Registro Semântico](https://msdn.microsoft.com/library/dn440729.aspx)
* [Teste de carga baseado em nuvem](http://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)
* [Plug-in LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Log Analytics do OMS](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)
* [Logary](https://www.nuget.org/packages/Logary.Targets.AppInsights/)

## <a name="content-management-systems"></a>Sistemas de Gerenciamento de Conteúdo
* [Concrete](https://github.com/fidmor89/appInsights-Concrete)
* [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
* [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
* [Orchard](https://azure.microsoft.com/blog/integrating-application-insights-into-a-modular-cms-and-a-multi-tenant-public-saas/preview/)
* [SharePoint](app-insights-sharepoint.md)
* [WordPress](https://wordpress.org/plugins/application-insights/)

## <a name="export-and-data-analysis"></a>Análise de Dados e Exportação
* [Alooma](https://www.alooma.com/blog/application-insights-amazon-redshift)
* [Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](app-insights-export-power-bi.md)

## <a name="projects"></a> Criar seu próprio SDK
Se ainda não houver um SDK para sua linguagem ou plataforma, você gostaria de criar um? Examine o código dos SDKs existentes listados no [Projeto de SDK do Application Insights no GitHub](https://github.com/Microsoft/AppInsights-Home).
