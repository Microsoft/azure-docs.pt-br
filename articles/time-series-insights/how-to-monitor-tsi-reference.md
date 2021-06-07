---
title: Referência de dados de monitoramento do Azure Time Series Insights | Microsoft Docs
description: Documentação de referência para monitoramento do Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: 20d376ee501edf16711617847c685dfd531f2c80
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504576"
---
# <a name="monitoring-azure-time-series-insights-data-reference"></a>Monitorando a referência de dados do Azure Time Series Insights

Saiba mais sobre os dados e os recursos coletados pelo Azure Monitor em seu ambiente do Azure Time Series Insights. Confira [Monitoramento do Time Series Insights]( ./how-to-monitor-tsi.md) para obter detalhes sobre como coletar e analisar dados de monitoramento.

## <a name="metrics"></a>Métricas

Esta seção lista todas as métricas de plataforma coletadas automaticamente para o Azure Time Series Insights. Para ver uma lista de todas as métricas de suporte do Azure Monitor (incluindo o Azure Time Series Insights), confira [Métricas com suporte do Azure Monitor](../azure-monitor/essentials/metrics-supported.md).
O provedor de recursos para essas métricas é [Microsoft.TimeSeriesInsights/environments/eventsources](../azure-monitor/essentials/metrics-supported.md#microsofttimeseriesinsightsenvironmentseventsources) e [Microsoft.TimeSeriesInsights/environments](../azure-monitor/essentials/metrics-supported.md#microsofttimeseriesinsightsenvironments).


### <a name="ingress"></a>Entrada

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|IngressReceivedBytes|Bytes de entrada recebidos|Bytes|Total|Contagem de bytes lidos da fonte de evento|
|IngressReceivedInvalidMessages|Mensagens de entrada inválidas recebidas|Contagem|Total|Contagem de mensagens inválidas lidas na origem do evento|
|IngressReceivedMessages|Mensagens de entrada recebidas|Contagem|Total|Contagem de mensagens lidas na origem do evento|
|IngressReceivedMessagesCountLag|Atraso na contagem das mensagens de entrada recebidas|Contagem|Média|Diferença entre o número de sequência da última mensagem enfileirada na partição de origem do evento e o número de sequência das mensagens que estão sendo processadas na Entrada|
|IngressReceivedMessagesTimeLag|Tempo de atraso das mensagens de entrada recebidas|Segundos|Máximo|Diferença entre a hora em que a mensagem é enfileirada na origem do evento e a hora em que ela é processada na entrada|
|IngressStoredBytes|Bytes de entrada armazenados|Bytes|Total|Tamanho total de eventos processados com êxito e disponíveis para consulta|
|IngressStoredEvents|Eventos de entrada armazenados|Contagem|Total|Contagem de eventos nivelados processados com êxito e disponíveis para consulta|

### <a name="storage"></a>Armazenamento

|Métrica|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|WarmStorageMaxProperties|Máximo de propriedades do armazenamento warm|Contagem|Máximo|Número máximo de propriedades usadas permitidas pelo ambiente para SKU S1/S2 e número máximo de propriedades permitidas pelo armazenamento warm para o SKU PAYG|
|WarmStorageUsedProperties|Propriedades usadas pelo armazenamento warm |Contagem|Máximo|Número de propriedades usadas pelo ambiente para SKU S1/S2 e número de propriedades usadas pelo armazenamento warm para SKU PAYG|

## <a name="resource-logs"></a>Logs de recursos

Esta seção lista os tipos de logs de recursos que você pode coletar para o ambiente do Azure Time Series Insights.

| Categoria | Nome de exibição | Descrição |
|----- |----- |----- |
| Entrada | TSIIngress | A categoria Entrada rastreia os erros que ocorrem no pipeline de entrada. Essa categoria inclui erros que ocorrem durante o recebimento de eventos (como falhas para se conectar a uma origem do evento) e eventos de processamento (como erros ao analisar uma carga do evento). |

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
| ResultDescription | Descrição do resultado da operação, por exemplo, "Recebimento de erro proibido". |
| Mensagem | A mensagem associada ao erro. Inclui detalhes sobre o que deu errado e como mitigar o erro. |
| ErrorCode | O código associado ao erro |
| EventSourceType | O tipo de origem do evento. Pode ser o Hub de eventos ou o hub IoT. |
| EventSourceProperties | Uma coleção de propriedades específicas da origem do evento. Contém detalhes como o grupo de consumidores e o nome da chave de acesso. |
