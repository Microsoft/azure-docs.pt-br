---
title: Monitorando serviços de mídia
description: Comece aqui para saber como monitorar os serviços de mídia
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femilia
ms.topic: how-to
ms.service: media-services
ms.custom: subject-monitoring
ms.date: 03/17/2021
ms.openlocfilehash: 90ca92dc19c588d0b19adf009301cf844e0cdbde
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609031"
---
# <a name="monitor-media-services"></a>Monitorar serviços de mídia

Quando você tem aplicativos e processos de negócios críticos que dependem de recursos do Azure, recomendamos monitorar a disponibilidade, o desempenho e a operação desses recursos. Este artigo descreve os dados de monitoramento gerados pelos serviços de mídia e como você pode usar os recursos de Azure Monitor para analisar e alertar sobre esses dados.

## <a name="metrics-are-useful"></a>As métricas são úteis

Aqui estão exemplos de como o monitoramento das métricas dos serviços de mídia pode ajudá-lo a entender como seus aplicativos estão sendo executados. Algumas perguntas que podem ser abordadas com as métricas dos serviços de mídia são:

- Como fazer monitorar meu ponto de extremidade de streaming padrão para saber quando eu exceda os limites?
- Como fazer saber se tenho unidades de escala de ponto de extremidade de streaming Premium suficientes?
- Como posso definir um alerta para saber quando escalar verticalmente meus pontos de extremidade de streaming?
- Como fazer definir um alerta para saber quando a egresso máxima configurada na conta foi atingida?
- Como posso ver a divisão das solicitações que falharam e o que está causando a falha?
- Como posso ver quantas solicitações HLS ou DASH estão sendo extraídas do empacotador?
- Como fazer definir um alerta para saber quando eu pressiona o valor limite das solicitações com falha?

<!--THIS DOESN'T BELONG HERE Concurrency becomes a concern for the number of Streaming Endpoints used in a single account over time. You need to keep in mind the relationship between the number of concurrent streams with complex publishing parameters like dynamic packaging to multiple protocols, multiple DRM encryptions etc. Each additional published live stream adds to the CPU and output bandwidth on the Streaming Endpoint. With that in mind, you should use Azure Monitor to closely watch the Streaming Endpoint's utilization (CPU and Egress capacity) to make certain that you are scaling it appropriately (or split traffic out between multiple Streaming Endpoints if you are getting into very high concurrency).-->

<!-- Optional diagram showing monitoring for your service. If you need help creating one, contact 
robb@microsoft.com -->

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?

Os serviços de mídia criam dados de monitoramento usando o [Azure monitor](../../../azure-monitor/overview.md), que é um serviço de monitoramento de pilha completo no Azure que fornece um conjunto completo de recursos para monitorar seus recursos do Azure, além de recursos em outras nuvens e locais.

Comece lendo o artigo [monitorando os recursos do Azure com Azure monitor](../../../azure-monitor/essentials/monitor-azure-resource.md), que descreve os seguintes conceitos:

- O que é o Azure Monitor?
- Custos associados ao monitoramento
- Monitoramento de dados coletados no Azure
- Configuração de coleta de dados
- Ferramentas padrão no Azure para analisar e alertar sobre dados de monitoramento

## <a name="monitoring-data"></a>Dados de monitoramento

Os serviços de mídia coletam os mesmos tipos de dados de monitoramento que outros recursos do Azure descritos em [monitoramento de dados de recursos do Azure](../../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data).

Todos os dados coletados pelo Azure Monitor se enquadram em um dos dois tipos básicos: métricas e logs. Com esses dois tipos, você pode:

- Visualize e analise os dados de métricas usando o Metrics Explorer.
- Monitore os logs de diagnóstico dos serviços de mídia e crie alertas e notificações para eles.
- Com os logs, você pode:
  - Enviá-los para o armazenamento do Azure
  - Transmitir para os hubs de eventos do Azure
  - Exportá-los para Log Analytics
  - Usar serviços de terceiros

Confira o artigo [monitorando a referência de dados dos serviços de mídia](monitor-media-services-data-reference.md) para obter informações detalhadas sobre as métricas de métricas e logs criadas pelos serviços de mídia.

## <a name="collection-and-routing"></a>Coleta e roteamento

As *métricas de plataforma* e o *log de atividades* são coletados e armazenados automaticamente, mas podem ser roteados para outros locais usando uma configuração de diagnóstico.  

*Os logs de recursos* **não** são coletados e armazenados até você criar uma configuração de diagnóstico e roteá-los para um ou mais locais.

Consulte o artigo [criar configuração de diagnóstico para coletar logs e métricas de plataforma no Azure](../../../azure-monitor/essentials/diagnostic-settings.md) para o processo detalhado de criação de uma configuração de diagnóstico usando o portal do Azure, a CLI ou o PowerShell.

Ao criar uma configuração de diagnóstico, você especifica quais categorias de logs coletar. As categorias dos serviços de mídia estão listadas na [referência de dados de monitoramento dos serviços de mídia](monitor-media-services-data-reference.md).

## <a name="analyzing-metrics"></a>Analisando as métricas

Você pode analisar as métricas dos serviços de mídia com métricas de outros serviços do Azure usando o Metrics Explorer abrindo **métricas** no menu **Azure monitor** . Consulte [Introdução ao Azure Metrics Explorer](../../../azure-monitor/essentials/metrics-getting-started.md) para obter detalhes sobre como usar essa ferramenta.

Para obter uma lista das métricas coletadas para os serviços de mídia, consulte [monitoramento de dados do Media Services referência](monitor-media-services-data-reference.md).

## <a name="analyzing-logs"></a>Análise de logs

Os dados em Logs do Azure Monitor são armazenados em tabelas em que cada tabela tem o próprio conjunto de propriedades exclusivas.  

Todos os logs de recursos no Azure Monitor têm os mesmos campos seguidos por campos específicos do serviço. O esquema comum é descrito em [Azure monitor esquema de log de recursos](../../../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema).

O esquema dos logs de recursos dos serviços de mídia é encontrado em [monitorando a referência de dados dos serviços de mídia](monitor-media-services-data-reference.md).

O [log de atividades](../../../azure-monitor/essentials/activity-log.md) é um log de plataforma no Azure que fornece informações sobre eventos de nível de assinatura. Você pode exibi-lo de modo independente ou roteá-lo para os Logs do Azure Monitor, nos quais você pode fazer consultas muito mais complexas usando o Log Analytics.

Para obter uma lista dos tipos de logs de recursos coletados para os serviços de mídia, consulte [monitoramento de dados dos serviços de mídia](monitor-media-services-data-reference.md).

### <a name="why-would-i-want-to-use-diagnostic-logs"></a>Por que desejo usar os logs de diagnóstico?

Algumas coisas que você pode examinar com os logs de diagnóstico são:

- O número de licenças entregues pelo tipo de DRM.
- O número de licenças entregues pela política.
- Erros por DRM ou tipo de política.
- O número de solicitações de licença não autorizadas de clientes.

## <a name="alerts"></a>Alertas

Os alertas do Azure Monitor notificam você proativamente quando condições importantes são encontradas nos dados de monitoramento. Eles permitem que você identifique e resolva problemas no seu sistema antes que os clientes os percebam. Você pode definir alertas em [métricas](../../../azure-monitor/alerts/alerts-metric-overview.md), [logs](../../../azure-monitor/alerts/alerts-unified-log.md) e [log de atividades](../../../azure-monitor/alerts/activity-log-alerts.md).

As métricas dos serviços de mídia são coletadas em intervalos regulares, independentemente de o valor ser alterado ou não. Eles são úteis para alertas porque podem ser amostrados com frequência. Um alerta pode ser acionado rapidamente com lógica relativamente simples.

<!--
The following table lists common and recommended alert rules for Media Services.

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable
**PLACEHOLDER** table

| Alert type | Condition | Description  |
|:---|:---|:---|
| | | |
| | | |
-->

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
