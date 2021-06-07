---
title: Métricas com suporte na grade de eventos do Azure
description: Este artigo fornece Azure Monitor métricas com suporte no serviço de grade de eventos do Azure.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 321e318f9dab87fde20b33a6a3a906b020ada622
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588725"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Métricas com suporte na grade de eventos do Azure
Este artigo fornece listas de métricas de grade de eventos que são categorizadas por namespaces. 

## <a name="system-topics"></a>Tópicos do sistema

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Sim|Avaliações de filtro avançado|Contagem|Total|Total de filtros avançados avaliados em assinaturas de evento para este tópico.|EventSubscriptionName|
|DeadLetteredCount|Sim|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Não|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Erro, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Sim|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|EventSubscriptionName|
|DestinationProcessingDurationInMs|Não|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|EventSubscriptionName|
|DroppedEventCount|Sim|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason, EventSubscriptionName|
|MatchedEventCount|Sim|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|EventSubscriptionName|
|PublishFailCount|Sim|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, Error|
|PublishSuccessCount|Sim|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Sem dimensões|
|PublishSuccessLatencyInMs|Sim|Publicar Latência de Êxito|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Sem dimensões|
|UnmatchedEventCount|Sim|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Sem dimensões|


## <a name="custom-topics"></a>Tópicos personalizados

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Sim|Avaliações de filtro avançado|Contagem|Total|Total de filtros avançados avaliados em assinaturas de evento para este tópico.|EventSubscriptionName|
|DeadLetteredCount|Sim|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Não|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Erro, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Sim|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|EventSubscriptionName|
|DestinationProcessingDurationInMs|Não|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|EventSubscriptionName|
|DroppedEventCount|Sim|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason, EventSubscriptionName|
|MatchedEventCount|Sim|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|EventSubscriptionName|
|PublishFailCount|Sim|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, Error|
|PublishSuccessCount|Sim|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Sem dimensões|
|PublishSuccessLatencyInMs|Sim|Publicar Latência de Êxito|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Sem dimensões|
|UnmatchedEventCount|Sim|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Sem dimensões|

## <a name="domains"></a>Domínios

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Sim|Avaliações de filtro avançado|Contagem|Total|Total de filtros avançados avaliados em assinaturas de evento para este tópico.|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|DeadLetteredCount|Sim|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|Não|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, erro, ErrorType|
|DeliverySuccessCount|Sim|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Não|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Sim|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Sim|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|Tópico, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Sim|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|Tópico, ErrorType, erro|
|PublishSuccessCount|Sim|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Tópico|
|PublishSuccessLatencyInMs|Sim|Publicar Latência de Êxito|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Sem dimensões|

## <a name="event-subscriptions"></a>Assinaturas de evento

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Sim|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason|
|DeliveryAttemptFailCount|Não|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Error, ErrorType|
|DeliverySuccessCount|Sim|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|Sem dimensões|
|DestinationProcessingDurationInMs|Não|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|Sem dimensões|
|DroppedEventCount|Sim|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason|
|MatchedEventCount|Sim|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|Sem dimensões|


## <a name="extension-topics"></a>Tópicos de extensão

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|PublishFailCount|Sim|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, Error|
|PublishSuccessCount|Sim|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Sem dimensões|
|PublishSuccessLatencyInMs|Sim|Publicar Latência de Êxito|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Sem dimensões|
|UnmatchedEventCount|Sim|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Sem dimensões|

## <a name="partner-namespaces"></a>Namespaces de parceiro

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Sim|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Não|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Erro, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Sim|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|EventSubscriptionName|
|DestinationProcessingDurationInMs|Não|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|EventSubscriptionName|
|DroppedEventCount|Sim|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason, EventSubscriptionName|
|MatchedEventCount|Sim|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|EventSubscriptionName|
|PublishFailCount|Sim|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, Error|
|PublishSuccessCount|Sim|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Sem dimensões|
|PublishSuccessLatencyInMs|Sim|Publicar Latência de Êxito|Milissegundos|Total|Latência de êxito de publicação em milissegundos|Sem dimensões|
|UnmatchedEventCount|Sim|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Sem dimensões|


## <a name="partner-topics"></a>Tópicos de parceiros

|Métrica|Exportável por meio das Configurações de Diagnóstico?|Nome de exibição da métrica|Unidade|Tipo de agregação|Descrição|Dimensões|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Sim|Avaliações de filtro avançado|Contagem|Total|Total de filtros avançados avaliados em assinaturas de evento para este tópico.|EventSubscriptionName|
|DeadLetteredCount|Sim|Eventos de mensagens mortas|Contagem|Total|Total de eventos de mensagens mortas correspondentes a essa assinatura de evento|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Não|Eventos com falha de entrega|Contagem|Total|Total de eventos não entregues a essa assinatura de evento|Erro, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Sim|Eventos entregues|Contagem|Total|Total de eventos entregues a essa assinatura de evento|EventSubscriptionName|
|DestinationProcessingDurationInMs|Não|Duração do processamento de destino|Milissegundos|Média|Duração do processamento de destino em milissegundos|EventSubscriptionName|
|DroppedEventCount|Sim|Eventos removidos|Contagem|Total|Total de eventos removidos correspondentes a essa assinatura de evento|DropReason, EventSubscriptionName|
|MatchedEventCount|Sim|Eventos correspondentes|Contagem|Total|Total de eventos correspondentes a essa assinatura de evento|EventSubscriptionName|
|PublishFailCount|Sim|Publicar Eventos com Falha|Contagem|Total|Total de eventos com falha ao publicar neste tópico|ErrorType, Error|
|PublishSuccessCount|Sim|Eventos publicados|Contagem|Total|Total de eventos publicados neste tópico|Sem dimensões|
|UnmatchedEventCount|Sim|Eventos sem correspondência|Contagem|Total|Total de eventos sem correspondência com nenhuma das assinaturas de evento para este tópico|Sem dimensões|


## <a name="next-steps"></a>Próximas etapas
Consulte o seguinte artigo: [logs de diagnóstico](diagnostic-logs.md)
