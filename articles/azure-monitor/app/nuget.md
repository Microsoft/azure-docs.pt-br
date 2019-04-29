---
title: Azure Application Insights – coleta automática de dependência | Microsoft Docs
description: O Application Insights coleta e visualiza dependências automaticamente
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/16/2018
ms.author: mbullwin
ms.openlocfilehash: 3ad2f4788a765366066023724772f5432d0d56eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699198"
---
# <a name="application-insights-nuget-packages"></a>Pacotes NuGet do Application Insights

Veja abaixo a lista atual de pacotes NuGet de versão estável para o Application Insights.

## <a name="common-packages-for-aspnet"></a>Pacotes comuns do ASP.NET

| Nome do Pacote | Versão estável | DESCRIÇÃO | Baixar |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.8.0 | Fornece funcionalidade básica para transmissão de todos os tipos do Application Insights Telemetry e é um pacote dependente para todos os outros pacotes do Application Insights | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | Permite a interceptação de chamadas de método | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | O Coletor de Dependências do Application Insights para aplicativos .NET. Esse é um pacote dependente para pacotes específicos da plataforma do Application Insights e fornece a coleta automática de telemetria de dependências. | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | O Coletor de Contadores de Desempenho do Application Insights permite que você envie os dados coletados pelos Contadores de desempenho para o Application Insights. | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.Web | 2.8.0 | Application Insights para aplicativos Web .NET | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | O pacote NuGet do Windows Server do Application Insights oferece a coleta automática de telemetria de insights de aplicativos para aplicativos .NET. Esse pacote pode ser usado como um pacote dependente para pacotes específicos da plataforma do Application Insights ou como um pacote independente para aplicativos .NET que não são cobertos pelos pacotes específicos da plataforma (como para funções de trabalho do .NET). | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | Fornece um canal de telemetria para o SDK do Windows Server do Application Insights que preservará a telemetria em cenários offline. | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>Pacotes comuns do ASP.NET Core

| Nome do Pacote | Versão estável | DESCRIÇÃO | Baixar |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Application Insights para aplicativos Web ASP.NET Core. | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.8.0 | Este pacote fornece funcionalidade básica para transmissão de todos os tipos do Application Insights Telemetry e é um pacote dependente para todos os outros pacotes do Application Insights | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | O Coletor de Dependências do Application Insights para aplicativos .NET. Esse é um pacote dependente para pacotes específicos da plataforma do Application Insights e fornece a coleta automática de telemetria de dependências. | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | O Coletor de Contadores de Desempenho do Application Insights permite que você envie os dados coletados pelos Contadores de desempenho para o Application Insights. | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | O pacote NuGet do Windows Server do Application Insights oferece a coleta automática de telemetria de insights de aplicativos para aplicativos .NET. Esse pacote pode ser usado como um pacote dependente para pacotes específicos da plataforma do Application Insights ou como um pacote independente para aplicativos .NET que não são cobertos pelos pacotes específicos da plataforma (como para funções de trabalho do .NET). | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | Fornece um canal de telemetria para o SDK do Windows Server do Application Insights que preservará a telemetria em cenários offline. | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="listenerscollectorsappenders"></a>Ouvintes/coletores/appenders

| Nome do Pacote | Versão estável | DESCRIÇÃO | Baixar |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  Permite eventos de encaminhamento do DiagnosticSource para o Application Insights. | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | O EventSourceListener do Application Insights permite enviar dados de eventos do EventSource ao Application Insights. | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | O EtwCollector do Application Insights permite enviar dados do Rastreamento de Eventos para Windows (ETW) ao Application Insights. | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | Um TraceListener personalizado que permite enviar mensagens de log de rastreamento ao Application Insights. | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | Um appender personalizado que permite enviar mensagens de log do Log4Net ao Application Insights. | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  um destino personalizado que permite enviar mensagens de log do NLog ao Application Insights. | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | Monitora exceções em seu aplicativo e coleta automaticamente os instantâneos para análise offline. | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Nome do Pacote | Versão estável | DESCRIÇÃO | Baixar |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | Este pacote fornece a decoração automática de telemetria com o contexto do service fabric no qual o aplicativo está sendo executado. Não use este NuGet para aplicativos do Service Fabric nativo. | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Native | 2.2.0 | Módulo do Application Insights para aplicativos do Service Fabric. Use este NuGet apenas para aplicativos do Service Fabric nativo. Para aplicativos executados em contêineres, use o pacote Microsoft.ApplicationInsights.ServiceFabric. | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Status Monitor

| Nome do Pacote | Versão estável | DESCRIÇÃO | Baixar |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  Habilita a coleta de dados de tempo de execução para aplicativos x64 | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  Habilita a coleta de dados de tempo de execução para aplicativos x86. | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Esses pacotes fazem parte da funcionalidade principal do monitoramento de tempo de execução no [Status Monitor](../../azure-monitor/app/monitor-performance-live-website-now.md). Você não precisa baixar esses pacotes diretamente. Basta usar o instalador do Status Monitor. Se você quiser entender mais sobre como esses pacotes funcionam nos bastidores, essa [postagem no blog](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/) de um de nossos desenvolvedores é um bom começo.

## <a name="additional-packages"></a>Pacotes adicionais

| Nome do Pacote | Versão estável | DESCRIÇÃO | Baixar |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebSites | 2.6.5 | Essa extensão permite o monitoramento do Application Insights em um Serviço de Aplicativo do Azure. SDK versão 2.6.1. Instruções: Adicione as configurações de aplicativo 'APPINSIGHTS_INSTRUMENTATIONKEY' com o seu ikey e reinicie o webapp para ter um efeito.| [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injector | 2.6.7 | Este pacote contém os arquivos necessários para a injeção do Application Insights sem código | [Baixar Pacote](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Próximas etapas

- Monitore o [ASP.NET Core](../../azure-monitor/app/asp-net-core.md).
- Crie o perfil de [aplicativos Web ASP.NET Core Azure Linux](../../azure-monitor/app/profiler-aspnetcore-linux.md).
- Depure [instantâneos](../../azure-monitor/app/snapshot-debugger.md) do ASP.NET.