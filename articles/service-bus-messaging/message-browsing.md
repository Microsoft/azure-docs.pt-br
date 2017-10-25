---
title: "Procura de mensagens do Barramento de Serviço do Azure | Microsoft Docs"
description: "Procurar e espiar mensagens do Barramento de Serviço"
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: sethm
ms.openlocfilehash: b0bc1ef7570ccac07975e2560a1d0501d3cde2b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="message-browsing"></a>Procura de mensagens

A procura de mensagens (“espiada”) permite que um cliente enumere todas as mensagens que residem em uma fila ou assinatura, normalmente para fins de diagnóstico e depuração.

As operações peek retornam todas as mensagens que existem no log de mensagem da fila ou da assinatura, não apenas aqueles disponíveis para a aquisição imediata com o loop *Receive()* ou o *OnMessage()*. A propriedade *State* de cada mensagem informa se a mensagem está ativa (disponível para ser recebida), adiada (consulte Adiamento [link TBD]) ou agendada (consulte Mensagens agendadas [link TBD]).

Mensagens consumidas e expiradas são limpas por uma execução de “coleta de lixo” assíncrona e não necessariamente exatamente quando as mensagens expiram e, portanto, Peek pode de fato retornar mensagens que já expiraram e serão removidas ou estarão mortas quando uma operação de recebimento for invocada a seguir na fila ou assinatura.

Isso é especialmente importante para se ter em mente ao tentar recuperar mensagens desviadas da fila. Uma mensagem para a qual o instante [ExpiresAtUtc](/dotnet/api/microsoft.azure.servicebus.message.expiresatutc#Microsoft_Azure_ServiceBus_Message_ExpiresAtUtc) passou não é elegível para recuperação regular por outros meios, mesmo quando ela está sendo retornada por Peek. O retorno dessas mensagens é intencional, uma vez que Peek é uma ferramenta de diagnóstico refletindo o estado atual do log.

Peek também retorna mensagens que foram bloqueadas e estão sendo processadas por outros receptores no momento, mas ainda não foram concluídas. No entanto, como Peek retorna um instantâneo desconectado, o estado de bloqueio de uma mensagem não pode ser observado em mensagens espiadas e as propriedades [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.lockeduntilutc#Microsoft_Azure_ServiceBus_Core_MessageReceiver_LockedUntilUtc) e [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) geram uma [InvalidOperationException](/dotnet/api/system.invalidoperationexception) quando o aplicativo tenta lê-las.

## <a name="peek-apis"></a>APIs de Peek

Os métodos [Peek/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) e [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) existem em todas as bibliotecas de cliente .NET e Java e em todos os objetos de destinatário: **MessageReceiver**, **MessageSession**, **QueueClient** e **SubscriptionClient**. Peek funciona em todas as filas e assinaturas e suas respectivas filas de mensagens mortas.

Quando chamado várias vezes, o método Peek enumera todas as mensagens que existem no log de fila ou assinatura, em ordem de número de sequência, do número de sequência mais baixo disponível para o mais alto. Esta é a ordem na qual as mensagens foram enfileiradas, não é a ordem na qual as mensagens podem ser recuperadas eventualmente.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) recupera várias mensagens e as retorna como uma enumeração. Se nenhuma mensagem estiver disponível, o objeto de enumeração estará vazio, não nulo.

Você também pode propagar uma sobrecarga do método com um [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) em que iniciar e, em seguida, chamar a sobrecarga de método sem parâmetros para enumerar ainda mais. **PeekBatch** funciona de forma equivalente, mas recupera um conjunto de mensagens de uma só vez.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os tópicos a seguir:

* [Conceitos fundamentais do barramento de serviço](service-bus-fundamentals-hybrid-solutions.md)
* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)