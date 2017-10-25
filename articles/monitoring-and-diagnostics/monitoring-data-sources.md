---
title: Consumir dados de monitoramento do Azure | Microsoft Docs
description: "Saiba mais sobre todas as fontes de dados de monitoramento disponíveis no Azure hoje."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/27/2017
ms.author: johnkem
ms.openlocfilehash: c7075c2e1a2500eca1d0aa9b3a797e8a0e903ede
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="consume-monitoring-data-from-azure"></a>Consumir dados de monitoramento do Azure

Em toda a plataforma do Azure, estamos reunindo dados de monitoramento em um único lugar com o pipeline do Azure Monitor, mas, praticamente reconhecemos que hoje nem todos os dados de monitoramento ainda estão disponíveis no pipeline. Neste artigo, resumiremos as várias maneiras pelas quais é possível acessar de forma programática os dados de monitoramento dos serviços do Azure.

## <a name="options-for-data-consumption"></a>Opções para consumo de dados

| Tipo de dados | Categoria | Serviços com suporte | Métodos de acesso |
| --- | --- | --- | --- |
| Métricas em nível de plataforma do Azure Monitor | Métricas | [Consulte a lista aqui](monitoring-supported-metrics.md) | <ul><li>**API REST:** [API de Métricas do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**Blob de armazenamento ou hub de eventos:** [Configurações de diagnóstico](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)</li></ul> |
| Métricas de computação do SO convidado (por exemplo, contadores de desempenho) | Métricas | Máquinas Virtuais [Windows](../virtual-machines-dotnet-diagnostics.md) e Linux (v2), [Serviços de Nuvem](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Tabela ou blob de armazenamento:** [Diagnóstico do Azure para Windows ou Linux](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**Hub de eventos:** [Diagnóstico do Azure para Windows](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| Métricas personalizadas ou de aplicativo | Métricas | Um aplicativo instrumentado com o Application Insights | <ul><li>**API REST:** [API REST do Application Insights](https://dev.applicationinsights.io/reference)</li></ul> |
| Métricas de armazenamento | Métricas | Armazenamento do Azure | <ul><li>**Tabela de armazenamento:** [Análise de armazenamento](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Dados de cobrança | Métricas | Todos os serviços do Azure | <ul><li>**API REST:** [APIs RateCard e Uso de Recursos do Azure](../billing/billing-usage-rate-card-overview.md)</li></ul> |
| Log de Atividade | Eventos | Todos os serviços do Azure | <ul><li>**API REST:** [API de Eventos do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/events)</li><li>**Blob de armazenamento ou hub de eventos:** [Log de Perfil](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Logs de Diagnóstico do Azure Monitor | Eventos | [Consulte a lista aqui](monitoring-diagnostic-logs-schema.md) | <ul><li>**Blob de armazenamento ou hub de eventos:** [Configurações de diagnóstico](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)</li></ul> |
| Logs de computação do SO convidado (por exemplo, IIS, ETW, syslogs) | Eventos | Máquinas Virtuais [Windows](../virtual-machines-dotnet-diagnostics.md) e Linux (v2), [Serviços de Nuvem](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Tabela ou blob de armazenamento:** [Diagnóstico do Azure para Windows ou Linux](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**Hub de eventos:** [Diagnóstico do Azure para Windows](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| Logs do Serviço de Aplicativo | Eventos | Serviços de aplicativos | <ul><li>**Armazenamento de arquivos, tabelas ou blobs:** [Diagnóstico de aplicativo Web](../app-service/web-sites-enable-diagnostic-log.md)</li></ul> |
| Logs de armazenamento | Eventos | Armazenamento do Azure | <ul><li>**Tabela de armazenamento:** [Análise de armazenamento](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Alertas da Central de Segurança | Eventos | Central de Segurança do Azure | <ul><li>**API REST:** [Alertas de Segurança](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Relatórios do Active Directory | Eventos | Azure Active Directory | <ul><li>**API REST:** [API do Graph no Azure Active Directory](../active-directory/active-directory-reporting-api-getting-started.md)</li></ul> |
| Status de recursos da Central de Segurança | Status | [Todos os recursos com suporte](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**API REST:** [Status de Segurança](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| Integridade de recursos | Status | Serviços com suporte | <ul><li>**API REST:** [API REST do Resource Health](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Alertas de métricas do Azure Monitor | Notificações | [Consulte a lista aqui](monitoring-supported-metrics.md) | <ul><li>**Webhook:** [alertas de métricas do Azure](insights-webhooks-alerts.md)</li></ul> |
| Alertas do Log de Atividades do Azure Monitor | Notificações | Todos os serviços do Azure | <ul><li>**Webhook:** alertas do Log de Atividades do Azure</li></ul> |
| Notificações de escala automática | Notificações | [Consulte a lista aqui](monitoring-overview-autoscale.md#supported-services-for-autoscale) | <ul><li>**Webhook:** [Escala automática do esquema de conteúdo do webhook de notificação](insights-autoscale-to-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| Alertas de Consulta da Pesquisa de Logs do OMS | Notificações | Log Analytics do OMS | <ul><li>**Webhook:** [Alertas do Log Analytics](../log-analytics/log-analytics-alerts-actions.md#webhook-actions)</li></ul> |
| Alertas de métricas do Application Insights | Notificações | Application Insights | <ul><li>**Webhook:** [Alertas do Application Insights](../application-insights/app-insights-alerts.md)</li></ul> |
| Testes na Web do Application Insights | Notificações | Application Insights | <ul><li>**Webhook:** [Alertas do Application Insights](../application-insights/app-insights-alerts.md)</li></ul> |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [métricas do Azure Monitor](monitoring-overview-metrics.md)
- Saiba mais sobre o [Log de Atividades do Azure](monitoring-overview-activity-logs.md)
- Saiba mais sobre os [Logs de Diagnóstico do Azure](monitoring-overview-of-diagnostic-logs.md)
