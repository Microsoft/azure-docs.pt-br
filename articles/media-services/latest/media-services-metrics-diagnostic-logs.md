---
title: Monitorar as métricas de serviços de mídia do Azure e logs de diagnóstico por meio do Azure Monitor | Microsoft Docs
description: Este artigo fornece uma visão geral de como monitorar as métricas de serviços de mídia do Azure e logs de diagnóstico por meio do Azure Monitor.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 6d26cd809d78bf05f66c9fa03be5063ca4d2d5e4
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806007"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>Monitorar logs de diagnóstico e métricas de serviços de mídia

[O Azure Monitor](../../azure-monitor/overview.md) habilita você a monitorar métricas e logs de diagnóstico que ajudam você a entende como estão o desempenho de seus aplicativos. Todos os dados coletados pelo Azure Monitor se encaixa em um dos dois tipos fundamentais, métricas e logs. Você pode monitorar os logs de diagnóstico dos serviços de mídia e criar alertas e notificações para os logs e métricas coletadas. Você pode visualizar e analisar os dados de métricas usando [Metrics explorer](../../azure-monitor/platform/metrics-getting-started.md). Você pode enviar logs para [armazenamento do Azure](https://azure.microsoft.com/services/storage/), transmiti-los para [Hubs de eventos](https://azure.microsoft.com/services/event-hubs/)e exportá-los para [do Log Analytics](https://azure.microsoft.com/services/log-analytics/), ou usar os serviços de terceiros 3ª.

Para obter uma visão detalhada, consulte [métricas do Azure Monitor](../../azure-monitor/platform/data-platform.md) e [logs de diagnóstico do Azure Monitor](../../azure-monitor/platform/diagnostic-logs-overview.md).

Este tópico discute suportado [métricas de serviços de mídia](#media-services-metrics) e [logs de diagnóstico de serviços de mídia](#media-services-diagnostic-logs).

## <a name="media-services-metrics"></a>Métricas de serviços de mídia

As métrica são coletadas em intervalos regulares independentemente da mudança no valor. Elas são úteis para alertar, pois podem ser utilizadas com frequência, e um alerta pode ser acionado rapidamente com uma lógica relativamente simples. Para obter informações sobre como criar alertas de métrica, consulte [criar, exibir e gerenciar alertas de métrica usando o Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

Os serviços de mídia dá suporte a métricas de monitoramento para os seguintes recursos:

* Conta
* Ponto de Extremidade de Streaming
 
### <a name="account"></a>Conta

Você pode monitorar as métricas de conta a seguir. 

|Nome da métrica|Display name|DESCRIÇÃO|
|---|---|---|
|AssetCount|Contagem de ativo|Ativos em sua conta.|
|AssetQuota|Cota de ativo|Cota de ativos em sua conta.|
|AssetQuotaUsedPercentage|Porcentagem de cota usada ativo|O percentual da cota ativo já usado.|
|ContentKeyPolicyCount|Contagem de políticas de chave de conteúdo|Políticas de chave de conteúdo em sua conta.|
|ContentKeyPolicyQuota|Cota de política de chave de conteúdo|Cota de políticas de chave conteúda em sua conta.|
|ContentKeyPolicyQuotaUsedPercentage|Cota de política de chave de conteúdo usada percentual|O percentual da cota de política de chave de conteúdo já em uso.|
|StreamingPolicyCount|Contagem de política de streaming|Políticas de streaming em sua conta.|
|StreamingPolicyQuota|Cota da política de streaming|Cota de políticas em sua conta de fluxo.|
|StreamingPolicyQuotaUsedPercentage|Cota de política de streaming usada percentual|O percentual da cota de Streaming política já usado.|
 
Você também deve revisar [cotas e limitações de conta](limits-quotas-constraints.md).

### <a name="streaming-endpoint"></a>Ponto de Extremidade de Streaming

Os seguintes serviços de mídia [pontos de extremidade de Streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints) métricas têm suporte:

|Nome da métrica|Display name|DESCRIÇÃO|
|---|---|---|
|Requests|Requests|Fornece o número total de solicitações HTTP atendidas pelo ponto de extremidade de Streaming.|
|Saída|Saída|O número total de bytes de saída. Por exemplo, bytes transmitidos pelo ponto de extremidade de Streaming.|
|SuccessE2ELatency|Latência de ponta a ponta com sucesso|Duração de tempo do que o ponto de extremidade de Streaming recebeu a solicitação quando o último byte da resposta foi enviado.|

### <a name="why-would-i-want-to-use-metrics"></a>Por que eu desejaria usar métricas? 

Aqui estão exemplos de como os serviços de mídia métricas de monitoramento podem ajudar você a entender como estão o desempenho de seus aplicativos. Algumas perguntas que podem ser tratadas com métricas de serviços de mídia são:

* Como posso monitorar meu ponto de extremidade de Streaming Standard saber quando eu excederam os limites?
* Como saber se tenho o suficiente unidades de escala de ponto de extremidade de Streaming Premium? 
* Como configurar um alerta para saber quando escalar verticalmente meus pontos de extremidade de Streaming?
* Como para configurar um alerta para saber quando a saída máximo configurada na conta foi atingida?
* Como pode ver a análise de falhas de solicitações e o que está causando a falha?
* Como posso ver quantas solicitações HLS ou traço estão sendo obtidas do packager?
* Como para configurar um alerta para saber quando o valor do limite de n º de solicitações com falha foi atingido? 

### <a name="example"></a>Exemplo

Consulte [como monitorar as métricas de serviços de mídia](media-services-metrics-howto.md)

## <a name="media-services-diagnostic-logs"></a>Logs de diagnóstico dos serviços de mídia

Logs de diagnóstico fornecem dados avançados e frequentes sobre a operação de um recurso do Azure. Para obter mais informações, consulte [como coletar e consumir dados de log dos recursos do Azure](../../azure-monitor/platform/diagnostic-logs-overview.md).

Os serviços de mídia suporta os seguintes logs de diagnóstico:

* Distribuição de chaves

### <a name="key-delivery"></a>Distribuição de chaves

|Nome|DESCRIÇÃO|
|---|---|
|Solicitação de serviço de distribuição de chaves|Logs que mostram as informações de solicitação de serviço de distribuição de chaves. Para obter mais detalhes, consulte [esquemas](media-services-diagnostic-logs-schema.md).|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Por que eu desejaria usar logs de diagnóstico? 

Algumas coisas que você pode examinar com logs de diagnóstico de entrega de chave são:

* Consulte o número de licenças fornecida pelo tipo DRM
* Consulte o número de licenças entregues pela política 
* Consulte os erros por tipo de política ou DRM
* Ver o número de solicitações de licença não autorizado de clientes

### <a name="example"></a>Exemplo

Consulte [como monitorar os logs de diagnóstico do serviço de mídia](media-services-diagnostic-logs-howto.md)

## <a name="next-steps"></a>Próximas etapas 

* [Como coletar e consumir dados de log dos recursos do Azure](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Criar, exibir e gerenciar alertas de métrica usando o Azure Monitor](../../azure-monitor/platform/alerts-metric.md)
* [Como monitorar as métricas de serviços de mídia](media-services-metrics-howto.md)
* [Como monitorar os logs de diagnóstico do serviço de mídia](media-services-diagnostic-logs-howto.md)
