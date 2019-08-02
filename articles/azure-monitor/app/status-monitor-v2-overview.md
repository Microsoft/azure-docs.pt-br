---
title: Visão geral do Azure Status Monitor v2 | Microsoft Docs
description: Uma visão geral do Status Monitor v2. Monitore o desempenho do site sem reimplantar o site. Funciona com aplicativos Web ASP.NET hospedados localmente, em VMs ou no Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 0264cf3a972c35edb3ad6dc600ca39bdaa076dfd
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333694"
---
# <a name="status-monitor-v2"></a>Status Monitor v2

Status Monitor V2 é um módulo do PowerShell publicado no [Galeria do PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Ele substitui [status monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
O módulo fornece instrumentação sem código de aplicativos Web .NET hospedados com o IIS.
A telemetria é enviada para a portal do Azure, onde você pode [monitorar](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) seu aplicativo.

## <a name="powershell-gallery"></a>Galeria do PowerShell

O Status Monitor v2 está localizado aqui https://www.powershellgallery.com/packages/Az.ApplicationMonitor:.

![Galeria do PowerShell](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instruções
- Consulte as [instruções de introdução](status-monitor-v2-get-started.md) para obter um início com exemplos de código conciso.
- Consulte as [instruções detalhadas](status-monitor-v2-detailed-instructions.md) para obter um aprofundamento sobre como começar.

## <a name="powershell-api-reference"></a>Referência da API do PowerShell
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Desabilitar-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)
- [Start-ApplicationInsightsMonitoringTrace](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>Solução de problemas
- [Solução de problemas](status-monitor-v2-troubleshoot.md)
- [Problemas conhecidos](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Perguntas Frequentes

- Status Monitor v2 oferece suporte a instalações de proxy?

  *Sim*. Há várias maneiras de baixar o Status Monitor v2. Se o seu computador tiver acesso à Internet, você poderá integrar ao Galeria do PowerShell usando `-Proxy` parâmetros.
Você também pode baixar manualmente o módulo e instalá-lo no seu computador ou usá-lo diretamente.
Cada uma dessas opções é descrita nas [instruções detalhadas](status-monitor-v2-detailed-instructions.md).
  
- Como fazer verificar se a habilitação foi bem-sucedida?

  - O cmdlet [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md) pode ser usado para verificar se a habilitação foi bem-sucedida.
  - Recomendamos que você use métricas em [tempo real](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) para determinar rapidamente se seu aplicativo está enviando telemetria.

  - Você também pode usar [log Analytics](../log-query/get-started-portal.md) para listar todas as funções de nuvem que estão enviando telemetria no momento:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Próximas etapas

Exiba sua telemetria:

* [Explore](../../azure-monitor/app/metrics-explorer.md) as métricas para monitorar o desempenho e o uso.
* [Pesquise eventos e logs](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
* [Use a análise](../../azure-monitor/app/analytics.md) para consultas mais avançadas.
* [Crie painéis](../../azure-monitor/app/overview-dashboard.md).

Adicione mais telemetria:

* [Crie testes da Web](monitor-web-app-availability.md) para garantir que seu site permaneça ativo.
* [Adicione telemetria de cliente Web](../../azure-monitor/app/javascript.md) para ver exceções do código de página da Web e para habilitar chamadas de rastreamento.
* [Adicione o SDK do Application insights ao seu código](../../azure-monitor/app/asp-net.md) para que você possa inserir chamadas de rastreamento e log.

