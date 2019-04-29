---
title: Obtenha mais do Azure Application Insights | Microsoft Docs
description: Após começar a usar o Application Insights, veja um resumo dos recursos que você pode explorar.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 7ec10a2d-c669-448d-8d45-b486ee32c8db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/03/2017
ms.author: mbullwin
ms.openlocfilehash: b81a555111ff49fcf2e14a75afdce81835d151bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691165"
---
# <a name="more-telemetry-from-application-insights"></a>Mais telemetria do Application Insights
Depois que você [adiciona o Application Insights a seu código ASP.NET](../../azure-monitor/app/asp-net.md), existem algumas coisas que pode fazer para obter ainda mais telemetria. 

| Ação | O que você ganha|
|---|---|
|(Servidores IIS) [Instale o Monitor de Status](https://go.microsoft.com/fwlink/?LinkId=506648) em cada máquina do servidor.<br/>(Aplicativos Web do Azure) No painel de controle do Azure para o aplicativo Web, abra a folha Application Insights.| [**Contadores de desempenho**](../../azure-monitor/app/performance-counters.md)<br/>[**Exceções**](asp-net-exceptions.md) - rastreamentos de pilha detalhados<br/>[**Dependências**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Adicionar o snippet de JavaScript às suas páginas da Web](../../azure-monitor/app/javascript.md)|[Desempenho da página](../../azure-monitor/app/usage-overview.md), exceções do navegador, desempenho do AJAX. Telemetria personalizada do lado do cliente.|
|[Criar testes de disponibilidade na Web](../../azure-monitor/app/monitor-web-app-availability.md)|Obter alertas se o seu site ficar indisponível|
|[Certificar-se de que buildinfo.config](https://msdn.microsoft.com/library/dn449058.aspx) seja gerado pelo MSBuild|[Criar anotações em gráficos de métricas](https://blogs.msdn.microsoft.com/visualstudioalm/2013/11/14/implementing-deployment-markers-in-application-insights/)
|[Gravar eventos e métricas personalizadas](../../azure-monitor/app/api-custom-events-metrics.md)|Contagem de métricas e eventos de negócios, acompanhar o uso detalhado e muito mais.|
|[Perfil de seu site ativo](https://aka.ms/AIProfilerPreview)|Intervalos de função detalhada de seu aplicativo Web ativo|






