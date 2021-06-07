---
title: Barramento de serviço do Azure-pesquisa de mensagem
description: Procurar e inspecionar mensagens do barramento de serviço permite que um cliente do barramento de serviço do Azure enumere todas as mensagens em uma fila ou assinatura.
ms.topic: article
ms.date: 11/11/2020
ms.openlocfilehash: c52c9c967d4eada1a931e188ed4d25f7691cfb91
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94553634"
---
# <a name="message-browsing"></a>Procura de mensagens

A procura de mensagens, ou a inspeção, permite que um cliente do barramento de serviço enumere todas as mensagens em uma fila ou uma assinatura para fins de diagnóstico e depuração.

A operação de inspeção em uma fila retorna todas as mensagens na fila, não apenas aquelas disponíveis para aquisição imediata com `Receive()` o `OnMessage()` loop ou. A `State` propriedade de cada mensagem informa se a mensagem está ativa (disponível para ser recebida), [adiada](message-deferral.md), ou [agendada](message-sequencing.md). A operação de inspeção em uma assinatura retorna todas as mensagens, exceto as mensagens agendadas no log de mensagens de assinatura. 

As mensagens consumidas e expiradas são limpas por uma execução assíncrona de "coleta de lixo". Essa etapa pode não ocorrer necessariamente imediatamente após as mensagens expirarem. É por isso que o `Peek` pode retornar mensagens que já expiraram. Essas mensagens serão removidas ou mensagens mortas quando uma operação de recebimento for invocada na fila ou assinatura na próxima vez. Tenha esse comportamento em mente ao tentar recuperar mensagens adiadas da fila. Uma mensagem expirada não é mais elegível para recuperação regular por outros meios, mesmo quando ela está sendo retornada por Peek. Retornar essas mensagens é por design, pois Peek é uma ferramenta de diagnóstico que reflete o estado atual do log.

Peek também retorna mensagens que foram bloqueadas e que estão sendo processadas por outros receptores. No entanto, como Peek retorna um instantâneo desconectado, o estado de bloqueio de uma mensagem não pode ser observado em mensagens inspecionadas. As propriedades [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) e [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) geram uma [InvalidOperationException](/dotnet/api/system.invalidoperationexception) quando o aplicativo tenta lê-las.

## <a name="peek-apis"></a>APIs de Peek

Os métodos [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) e [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) existem nas bibliotecas de cliente .net e Java e nos objetos receptores: **MessageReceiver**, **MessageSession**. Espiar funciona em filas, assinaturas e suas respectivas filas de mensagens mortas.

Quando chamado repetidamente, **Peek** enumera todas as mensagens no log de fila ou assinatura, em ordem, do número de sequência mais baixo disponível para o mais alto. É a ordem na qual as mensagens foram enfileiradas, não a ordem em que as mensagens podem eventualmente ser recuperadas.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) recupera várias mensagens e as retorna como uma enumeração. Se nenhuma mensagem estiver disponível, o objeto de enumeração estará vazio, não nulo.

Você também pode usar uma sobrecarga do método com um [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) no qual iniciar e, em seguida, chamar a sobrecarga do método sem parâmetros para enumerar mais. **PeekBatch** funciona de forma equivalente, mas recupera um conjunto de mensagens de uma só vez.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os seguintes tópicos:

* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)
