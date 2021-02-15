---
title: Métricas com suporte na grade de eventos do Azure
description: Este artigo fornece Azure Monitor métricas com suporte no serviço de grade de eventos do Azure.
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 87c91077c8eeca2134da53774979c212a82e3b7d
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042140"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Métricas com suporte na grade de eventos do Azure
Este artigo fornece listas de métricas de grade de eventos que são categorizadas por namespaces. 

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|No|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, erro, ErrorType|
|DeliverySuccessCount|Yes|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|No|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Yes|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Yes|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Yes|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|Tópico, ErrorType, erro|
|PublishSuccessCount|Yes|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Tópico|
|PublishSuccessLatencyInMs|Yes|Publicar Latência de Êxito|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Sem dimensões|
| AdvancedFilterEvaluationCount | Yes | Avaliações de filtro avançado | Contagem | Total | Total de filtros avançados avaliados nas assinaturas de evento | EventSubscriptionName |



## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason|
|DeliveryAttemptFailCount|No|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Error, ErrorType|
|DeliverySuccessCount|Yes|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|Sem dimensões|
|DestinationProcessingDurationInMs|No|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|Sem dimensões|
|DroppedEventCount|Yes|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason|
|MatchedEventCount|Yes|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|Sem dimensões|


## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|PublishFailCount|Yes|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, Error|
|PublishSuccessCount|Yes|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Sem dimensões|
|PublishSuccessLatencyInMs|Yes|Publicar Latência de Êxito|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Sem dimensões|
|UnmatchedEventCount|Yes|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Sem dimensões|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Erro, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|EventSubscriptionName|
|DroppedEventCount|Yes|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|EventSubscriptionName|
|PublishFailCount|Yes|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, Error|
|PublishSuccessCount|Yes|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Sem dimensões|
|PublishSuccessLatencyInMs|Yes|Publicar Latência de Êxito|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Sem dimensões|
|UnmatchedEventCount|Yes|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Sem dimensões|
| AdvancedFilterEvaluationCount | Yes | Avaliações de filtro avançado | Contagem | Total | Total de filtros avançados avaliados nas assinaturas de evento | EventSubscriptionName |



## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Description|Dimensões|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Erro, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|EventSubscriptionName|
|DroppedEventCount|Yes|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|EventSubscriptionName|
|PublishFailCount|Yes|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, Error|
|PublishSuccessCount|Yes|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Sem dimensões|
|PublishSuccessLatencyInMs|Yes|Publicar Latência de Êxito|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Sem dimensões|
|UnmatchedEventCount|Yes|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Sem dimensões|
| AdvancedFilterEvaluationCount | Yes | Avaliações de filtro avançado | Contagem | Total | Total de filtros avançados avaliados nas assinaturas de evento | Tópico, EventSubscriptionName, DomainEventSubscriptionName |

## <a name="next-steps"></a>Próximas etapas
Consulte o seguinte artigo: [logs de diagnóstico](diagnostic-logs.md)
