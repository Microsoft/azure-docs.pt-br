---
title: Monitorando Azure Time Series Insights referência de dados | Microsoft Docs
description: Documentação de referência para monitoramento Azure Time Series Insights.
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: 0b564ddfdea2cf24b7f9b1bc608d47fa4cfe541b
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632542"
---
# <a name="monitoring-azure-time-series-insights-data-reference"></a>Monitorando Azure Time Series Insights referência de dados

Saiba mais sobre os dados e os recursos coletados pelo Azure Monitor de seu ambiente de Azure Time Series Insights. Consulte [monitoramento Time Series insights]( ./how-to-monitor-tsi.md) para obter detalhes sobre como coletar e analisar dados de monitoramento.

## <a name="metrics"></a>Métricas

Esta seção lista todas as métricas de plataforma coletadas automaticamente coletadas para Azure Time Series Insights. Para obter uma lista de todas as métricas de suporte de Azure Monitor (incluindo Azure Time Series Insights), consulte [Azure monitor métricas com suporte](../azure-monitor/platform/metrics-supported.md). O provedor de recursos para essas métricas é [Microsoft. TimeSeriesInsights/ambientes/EventSources](../azure-monitor/platform/metrics-supported.md#microsofttimeseriesinsightsenvironmentseventsources) e [Microsoft. TimeSeriesInsights/Environments](../azure-monitor/platform/metrics-supported.md#microsofttimeseriesinsightsenvironments).


### <a name="ingress"></a>Entrada
 
|Métrica|Nome de exibição da métrica|Unit|Tipo de agregação|Description|
|---|---|---|---|---|
|IngressReceivedBytes|Bytes de entrada recebidos|Bytes|Total|Contagem de bytes lidos da fonte de evento|
|IngressReceivedInvalidMessages|Mensagens de entrada inválidas recebidas|Contagem|Total|Contagem de mensagens inválidas lidas na origem do evento|
|IngressReceivedMessages|Mensagens de entrada recebidas|Contagem|Total|Contagem de mensagens lidas na origem do evento|
|IngressReceivedMessagesCountLag|Atraso na contagem das mensagens de entrada recebidas|Contagem|Média|Diferença entre o número de sequência da última mensagem enfileirada na partição de origem do evento e o número de sequência de mensagens sendo processadas na entrada|
|IngressReceivedMessagesTimeLag|Tempo de atraso das mensagens de entrada recebidas|Segundos|Máximo|Diferença entre a hora em que a mensagem é enfileirada na origem do evento e a hora em que ela é processada na entrada|
|IngressStoredBytes|Bytes de entrada armazenados|Bytes|Total|Tamanho total de eventos processados com êxito e disponíveis para consulta|
|IngressStoredEvents|Eventos de entrada armazenados|Contagem|Total|Contagem de eventos nivelados processados com êxito e disponíveis para consulta|

### <a name="storage"></a>Armazenamento

|Métrica|Nome de exibição da métrica|Unit|Tipo de agregação|Description|
|---|---|---|---|---|
|WarmStorageMaxProperties|Propriedades máximas de armazenamento quente|Contagem|Máximo|Número máximo de propriedades usadas permitidas pelo ambiente para SKU S1/S2 e número máximo de propriedades permitidas pelo armazenamento quente para o SKU PAYG|
|WarmStorageUsedProperties|Propriedades usadas de armazenamento quente |Contagem|Máximo|Número de propriedades usadas pelo ambiente para SKU S1/S2 e número de propriedades usadas pelo armazenamento quente para SKU PAYG|

## <a name="resource-logs"></a>Logs de recursos

Esta seção lista os tipos de logs de recursos que você pode coletar para seu ambiente de Azure Time Series Insights.

| Categoria | Nome de exibição | Descrição |
|----- |----- |----- |
| Entrada | TSIIngress | A categoria de entrada rastreia os erros que ocorrem no pipeline de entrada. Essa categoria inclui erros que ocorrem durante o recebimento de eventos (como falhas para se conectar a uma fonte de evento) e eventos de processamento (como erros ao analisar uma carga de evento). |

## <a name="schemas"></a>Esquemas
Os esquemas a seguir estão em uso pelo Azure Time Series Insights

### <a name="tsiingress-table"></a>Tabela TSIIngress

| Propriedade | Descrição |
|----- |----- |
| TimeGenerated | Hora (UTC) em que esse evento é gerado. |
| Location | A localização do recurso. |
| Categoria | Categoria do evento de log. |
| OperationName | Nome da operação do evento. |
| CorrelationId | A ID de correlação da solicitação. |
| Nível | O nível de severidade do evento. |
| ResultDescription | Descrição do resultado da operação, por exemplo, ' recebimento proibido de erro '. |
| Mensagem | A mensagem associada ao erro. Inclui detalhes sobre o que deu errado e como mitigar o erro. |
| ErrorCode | O código associado ao erro |
| EventSourcetype | O tipo de origem do evento. Pode ser o Hub de eventos ou o Hub IoT. |
| EventSourceproperties | Uma coleção de propriedades específicas para a origem do evento. Contém detalhes como o grupo de consumidores e o nome da chave de acesso. |
