---
title: Carimbos de data/hora e sequenciamento de mensagem do Barramento de Serviço do Azure | Microsoft Docs
description: Este artigo explica como preservar o sequenciamento e a ordenação (com carimbos de data/hora) das mensagens do barramento de serviço do Azure.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: fdb18802e576ad114fd3f783d5efd7bb826a5f94
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85341172"
---
# <a name="message-sequencing-and-timestamps"></a>Carimbos de data/hora e sequenciamento de mensagem

O sequenciamento e o carimbo de data/hora são dois recursos que estão sempre habilitados em todas as entidades do Barramento de Serviço e na superfície por meio das propriedades [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) e [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) das mensagens recebidas ou procuradas.

Para os casos nos quais a ordem absoluta de mensagens é significativa e/ou nos quais um consumidor precisa de um identificador exclusivo confiável para mensagens, o agente aplica selo nas mensagens com um número de sequência crescente e sem lacunas relacionado à fila ou ao tópico. Para entidades particionadas, o número da sequência é emitido com relação à partição.

O valor **SequenceNumber** é um inteiro de 64 bits exclusivo atribuído a uma mensagem conforme ela é aceita e armazenada pelo agente e por funções como seu identificador interno. Para entidades particionadas, os 16 bits de nível mais alto refletem o identificador da partição. Os números de sequência passam para zero quando o intervalo de 48/64 bits é esgotado.

O número de sequência pode ser confiável como um identificador exclusivo, pois ele é atribuído por uma autoridade central e neutra, e não pelos clientes. Ele também representa a ordem verdadeira de chegada e é mais preciso do que um carimbo de data/hora enquanto critério de ordem, porque os carimbos de data/hora não podem ter uma resolução alta o suficiente em taxas de mensagens extremas e podem estar sujeitos a (embora mínima) distorção do relógio em situações em que a propriedade do agente faz a transição entre os nós.

A ordem de chegada absoluta é importante. Por exemplo, nos cenários comerciais, nos quais um número limitado de bens oferecidos são servidos por ordem de chegada enquanto durarem; as vendas de tíquete conjuntas são um exemplo.

A capacidade de carimbo de data/hora funciona como uma autoridade neutra e confiável que captura com precisão a hora UTC de chegada de uma mensagem, refletida na propriedade **EnqueuedTimeUtc**. O valor será útil se um cenário de negócios depender de prazos, como, por exemplo, se um item de trabalho foi enviado em uma determinada data antes da meia-noite, mas o processamento está muito atrás na lista de pendências da fila.

## <a name="scheduled-messages"></a>Mensagens agendadas

É possível enviar mensagens a uma fila ou a um tópico para processamento atrasado; por exemplo, para agendar a disponibilidade de um trabalho para processamento por um sistema em um determinado momento. Essa capacidade detecta um confiável agendador distribuído baseado em tempo.

As mensagens agendadas não são materializadas na fila até o tempo de enfileiramento definido. Antes disso, as mensagens agendadas podem ser canceladas. O cancelamento exclui a mensagem.

É possível agendar mensagens definindo a propriedade [ScheduledEnqueueTimeUtc](/dotnet/api/microsoft.azure.servicebus.message.scheduledenqueuetimeutc) ao enviar uma mensagem por meio do caminho de envio regular ou explicitamente com a API [ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_). Esta última retorna imediatamente o **SequenceNumber** da mensagem agendada, que você pode usar posteriormente para cancelar a mensagem agendada se necessário. As mensagens agendadas e seus números de sequência também podem ser descobertos usando a [pesquisa de mensagens](message-browsing.md).

O **SequenceNumber** para uma mensagem agendada somente será válido enquanto a mensagem estiver nesse estado. Conforme a mensagem faz a transição para o estado ativo, a mensagem é acrescentada à fila como se ela tivesse sido enfileirada no instante atual, que inclui a atribuição de um novo **SequenceNumber**.

Como o recurso está ancorado em mensagens individuais e as mensagens só podem ser enfileiradas uma vez, o Barramento de Serviço não dá suporte a agendamentos recorrentes para mensagens.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os seguintes tópicos:

* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)