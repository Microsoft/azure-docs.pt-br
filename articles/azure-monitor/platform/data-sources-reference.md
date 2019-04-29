---
title: Fontes de monitoramento de dados no Azure
description: Saiba mais sobre todas as fontes de dados de monitoramento disponíveis no Azure hoje.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: c703f735f59a8425c2a14641781f482a6e2d1c78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61364418"
---
# <a name="consume-monitoring-data-from-azure"></a>Consumir dados de monitoramento do Azure

Em toda a plataforma do Azure, estamos reunindo dados de monitoramento em um único lugar com o pipeline do Azure Monitor, mas, praticamente reconhecemos que hoje nem todos os dados de monitoramento ainda estão disponíveis no pipeline. Neste artigo, resumiremos as várias maneiras pelas quais é possível acessar de forma programática os dados de monitoramento dos serviços do Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="options-for-data-consumption"></a>Opções para consumo de dados

| Tipo de dados | Category | Serviços com suporte | Métodos de acesso |
| --- | --- | --- | --- |
| Métricas em nível de plataforma do Azure Monitor | Métricas | [Consulte a lista aqui](metrics-supported.md) | <ul><li>**API REST:** [API de Métricas do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**Blob de armazenamento ou hub de eventos:** [Configurações de Diagnóstico](diagnostic-logs-overview.md#diagnostic-settings)</li></ul> |
| Métricas de computação do SO convidado (por exemplo, contadores de desempenho) | Métricas | Máquinas Virtuais [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) e Linux (v2), [Serviços de Nuvem](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Tabela de armazenamento ou blob:** [Diagnóstico do Azure no Windows ou no Linux](diagnostics-extension-to-storage.md)</li><li>**Hub de eventos:** [Diagnóstico do Azure no Windows](diagnostics-extension-stream-event-hubs.md)</li></ul> |
| Métricas personalizadas ou de aplicativo | Métricas | Um aplicativo instrumentado com o Application Insights | <ul><li>**API REST:** [API REST do Application Insights](https://dev.applicationinsights.io/reference)</li></ul> |
| Métricas de armazenamento | Métricas | Armazenamento do Azure | <ul><li>**Tabela de armazenamento:** [Análise de Armazenamento](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Dados de cobrança | Métricas | Todos os serviços do Azure | <ul><li>**API REST:** [APIs de RateCard e Uso de Recursos do Azure](../../billing/billing-usage-rate-card-overview.md)</li></ul> |
| Log de Atividade | Eventos | Todos os serviços do Azure | <ul><li>**API REST:** [API de Eventos do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/eventcategories)</li><li>**Blob de armazenamento ou hub de eventos:** [Perfil de Log](activity-logs-overview.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Logs de Diagnóstico do Azure Monitor | Eventos | [Consulte a lista aqui](diagnostic-logs-schema.md) | <ul><li>**Blob de armazenamento ou hub de eventos:** [Configurações de Diagnóstico](diagnostic-logs-overview.md#diagnostic-settings)</li></ul> |
| Logs de computação do SO convidado (por exemplo, IIS, ETW, syslogs) | Eventos | Máquinas Virtuais [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) e Linux (v2), [Serviços de Nuvem](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md), [Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**Tabela de armazenamento ou blob:** [Diagnóstico do Azure no Windows ou no Linux](diagnostics-extension-to-storage.md)</li><li>**Hub de eventos:** [Diagnóstico do Azure no Windows](diagnostics-extension-stream-event-hubs.md)</li></ul> |
| Logs do Serviço de Aplicativo | Eventos | Serviços de aplicativos | <ul><li>**Armazenamento de arquivos, tabelas ou blobs:** [Diagnóstico de aplicativo Web](../../app-service/troubleshoot-diagnostic-logs.md)</li></ul> |
| Logs de armazenamento | Eventos | Armazenamento do Azure | <ul><li>**Tabela de armazenamento:** [Análise de Armazenamento](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Alertas da Central de Segurança | Eventos | Central de Segurança do Azure | <ul><li>**API REST:** [Alertas de Segurança](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Relatórios do Active Directory | Eventos | Azure Active Directory | <ul><li>**API REST:** [API do Graph do Azure Active Directory](../../active-directory/reports-monitoring/concept-reporting-api.md)</li></ul> |
| Status de recursos da Central de Segurança | Status | [Todos os recursos com suporte](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**API REST:** [Status de Segurança](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| Integridade de recursos | Status | Serviços com suporte | <ul><li>**API REST:** [API REST do Resource Health](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Alertas de métricas do Azure Monitor | Notificações | [Consulte a lista aqui](metrics-supported.md) | <ul><li>**Webhook:** [Alertas de métricas do Azure](alerts-webhooks.md)</li></ul> |
| Alertas do Log de Atividades do Azure Monitor | Notificações | Todos os serviços do Azure | <ul><li>**Webhook:** Alertas do Log de Atividades do Azure</li></ul> |
| Notificações de escala automática | Notificações | [Consulte a lista aqui](autoscale-overview.md#supported-services-for-autoscale) | <ul><li>**Webhook:** [Dimensionamento automático do esquema de conteúdo do webhook de notificação](autoscale-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| Alertas de consulta de pesquisa de logs | Notificações | Logs do Azure Monitor | <ul><li>**Webhook:** [Ação de webhook para regras de alerta do log](alerts-log-webhook.md)</li></ul> |
| Alertas de métricas do Application Insights | Notificações | Application Insights | <ul><li>**Webhook:** [Alertas do Application Insights](../../azure-monitor/app/alerts.md)</li></ul> |
| Testes na Web do Application Insights | Notificações | Application Insights | <ul><li>**Webhook:** [Alertas do Application Insights](../../azure-monitor/app/alerts.md)</li></ul> |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [métricas do Azure Monitor](data-platform.md)
- Saiba mais sobre o [Log de Atividades do Azure](activity-logs-overview.md)
- Saiba mais sobre os [Logs de Diagnóstico do Azure](diagnostic-logs-overview.md)

