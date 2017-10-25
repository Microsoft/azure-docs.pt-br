---
title: "Visão geral do processamento de transações no Barramento de Serviço do Azure | Microsoft Docs"
description: "Visão geral das transações atômicas do Barramento de Serviço do Azure e do recurso Enviar por"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 64449247-1026-44ba-b15a-9610f9385ed8
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2017
ms.author: clemensv;sethm
ms.openlocfilehash: a88f2d81ab43e38c9363a67aaefc178b47bfb259
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-service-bus-transaction-processing"></a>Visão geral do processamento de transações do Barramento de Serviço
Este artigo aborda as funcionalidades de transação do Barramento de Serviço do Azure. Grande parte da discussão é ilustrada na [amostra de Transações atômicas com o Barramento de Serviço](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions). Este artigo é limitado a uma visão geral do processamento de transações e ao recurso *Enviar por* do Barramento de Serviço, enquanto a amostra Transações atômicas tem um escopo mais amplo e complexo.

## <a name="transactions-in-service-bus"></a>Transações no Barramento de Serviço
Uma [transação](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions#what-are-transactions) agrupa duas ou mais operações em um *escopo de execução*. Por natureza, essa transação deve garantir que todas as operações que pertencem a determinado grupo de operações sejam concluídas com êxito ou com falha em conjunto. Nesse sentido, as transações agem como uma unidade, que, geralmente, é conhecida como *atomicidade*. 

O Barramento de Serviço é um agente de mensagens transacionais e assegura a integridade transacional de todas as operações internas em seus repositórios de mensagens. Todas as transferências de mensagens no Barramento de Serviço, como a movimentação de mensagens para uma [fila de mensagens mortas](service-bus-dead-letter-queues.md) ou [encaminhamento automático](service-bus-auto-forwarding.md) de mensagens entre entidades, são transacionais. Assim, caso o Barramento de Serviço aceite uma mensagem, isso significa ela já foi armazenada e rotulada com um número de sequência. Daí em diante, todas as transferências de mensagens no Barramento de Serviço são operações coordenadas entre entidades e não resultarão em perda (origem com êxito e destino com falha) nem em duplicação (origem com falha e destino com êxito) da mensagem.

O Barramento de Serviço dá suporte a operações de agrupamento em uma única entidade de mensagens (fila, tópico e assinatura) no escopo de uma transação. Por exemplo, você pode enviar várias mensagens para uma fila em um escopo de transação, e as mensagens só serão confirmadas no log da fila quando a transação for concluída com êxito.

## <a name="operations-within-a-transaction-scope"></a>Operações em um escopo de transação
As operações que podem ser executadas em um escopo de transação são as seguintes:

* **[QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient), [MessageSender](/dotnet/api/microsoft.servicebus.messaging.messagesender), [TopicClient](/dotnet/api/microsoft.servicebus.messaging.topicclient)**: Send, SendAsync, SendBatch, SendBatchAsync 
* **[BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)**: Complete, CompleteAsync, Abandon, AbandonAsync, Deadletter, DeadletterAsync, Defer, DeferAsync, RenewLock, RenewLockAsync 

As operações de recebimento não são incluídas, pois presume-se que o aplicativo obtenha as mensagens usando o modo [ReceiveMode.PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode) em algum loop de recebimento ou com um retorno de chamada [OnMessage](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__Microsoft_ServiceBus_Messaging_OnMessageOptions_) e só então abre um escopo de transação para o processamento da mensagem.

Em seguida, a disposição da mensagem (conclusão, abandono, mensagens mortas, adiamento) ocorre no escopo do resultado geral da transação e depende dele.

## <a name="transfers-and-send-via"></a>Transferências e “enviar por”
Para habilitar a transferência transacional dos dados de uma fila para um processador e, em seguida, para outra fila, o Barramento de Serviço dá suporte a *transferências*. Em uma operação de transferência, um remetente primeiro envia uma mensagem para uma “fila de transferência” e a fila de transferência move imediatamente a mensagem para a fila de destino pretendida usando a mesma implementação de transferência robusta da qual a funcionalidade de encaminhamento automático depende. A mensagem nunca é confirmada no log da fila de transferência de modo que fique visível para os consumidores da fila de transferência.

A potência dessa funcionalidade transacional se torna aparente quando a própria fila de transferência é a origem das mensagens de entrada do remetente. Em outras palavras, o Barramento de Serviço pode transferir a mensagem para a fila de destino por meio da fila de transferência, ao mesmo tempo que executa uma operação de conclusão (ou adiamento ou mensagens mortas) nas mensagens de entrada, tudo em uma única operação atômica. 

### <a name="see-it-in-code"></a>Ver em código
Para configurar essas transferências, você cria um remetente da mensagem que tem como alvo a fila de destino por meio da fila de transferência. Você também terá um destinatário que efetua pull das mensagens dessa mesma fila. Por exemplo:

```csharp
var sender = this.messagingFactory.CreateMessageSender(destinationQueue, myQueueName);
var receiver = this.messagingFactory.CreateMessageReceiver(myQueueName);
```

Uma transação simples usa então esses elementos, como mostrado no exemplo a seguir:

```csharp
var msg = receiver.Receive();

using (scope = new TransactionScope())
{
    // Do whatever work is required 

    var newmsg = ... // package the result 

    msg.Complete(); // mark the message as done
    sender.Send(newmsg); // forward the result

    scope.Complete(); // declare the transaction done
} 
```

## <a name="next-steps"></a>Próximas etapas

Confira os artigos a seguir para obter mais informações sobre as filas do Barramento de Serviço:

* [Encadeando entidades do Barramento de Serviço com o encaminhamento automático](service-bus-auto-forwarding.md)
* [Amostra de encaminhamento automático](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AutoForward)
* [Amostra de Transações atômicas com o Barramento de Serviço](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/AtomicTransactions)
* [Filas do Azure e filas do Barramento de Serviço – comparações](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
* [Como usar filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)

