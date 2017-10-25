---
title: "Filas de mensagens mortas do Barramento de Serviço | Microsoft Docs"
description: "Visão geral das filas de mensagens mortas do Barramento de Serviço"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 68b2aa38-dba7-491a-9c26-0289bc15d397
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2017
ms.author: clemensv;sethm
ms.openlocfilehash: c16bcf30ab96f79e59404a41852e4cd227e28b08
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Visão geral das filas de mensagens mortas do Barramento de Serviço 

As filas e as assinaturas de tópico do Barramento de Serviço fornecem uma subfila secundária chamada DLQ (*fila de mensagens mortas*). A fila de mensagens mortas não precisa ser explicitamente criada e não pode ser excluída ou de alguma forma gerenciada independentemente da entidade principal.

Este artigo discute as filas de mensagens mortas no barramento de serviço do Azure. Grande parte da discussão é ilustrada pelo [exemplo de Filas de Mensagens Mortas](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) no GitHub.
 
## <a name="the-dead-letter-queue"></a>A fila de mensagens mortas

A finalidade da fila de mensagens mortas é manter mensagens que não podem ser entregues a nenhum receptor ou mensagens que não puderam ser processadas. As mensagens podem ser removidas da DLQ e inspecionadas. Um aplicativo pode, com a ajuda de um operador, corrigir problemas e reenviar a mensagem, registrar o fato de que houve um erro e tomar uma medida corretiva. 

De uma perspectiva de API e protocolo, a DLQ é bem semelhante a qualquer outra fila, com exceção de que essas mensagens podem ser enviadas apenas por meio do gesto de mensagens mortas da entidade pai. Além disso, a vida útil não é observada, e você não pode colocar uma mensagem em estado morto na DLQ. A fila de mensagens mortas é totalmente compatível com operações transacionais e de entrega de espiada-bloqueio.

Observe que não há limpeza automática da DLQ. As mensagens permanecem na DLQ até você recuperá-las de lá explicitamente e chamar [Complete()](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_CompleteAsync) na mensagem morta.

## <a name="moving-messages-to-the-dlq"></a>Movendo mensagens para a DLQ

Há várias atividades no Barramento de Serviço que fazem com que as mensagens sejam enviadas por push à DLQ de dentro do próprio mecanismo de mensagens. Um aplicativo também pode explicitamente colocar mensagens na DLQ. 

Como a mensagem é movida pelo agente, duas propriedades são adicionadas à mensagem, uma vez que o agente chama sua versão interna do método [DeadLetter](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeadLetter_System_String_System_String_) na mensagem: `DeadLetterReason` e `DeadLetterErrorDescription`.

Os aplicativos podem definir seus próprios códigos para a propriedade `DeadLetterReason`, mas o sistema define os valores a seguir.

| Condição | DeadLetterReason | DeadLetterErrorDescription |
| --- | --- | --- |
| Sempre |HeaderSizeExceeded |A cota de tamanho para este fluxo foi excedida. |
| !TopicDescription.<br />EnableFilteringMessagesBeforePublishing e SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions |exception.GetType().Name |exception.Message |
| EnableDeadLetteringOnMessageExpiration |TTLExpiredException |A mensagem expirou e foi colocada no estado de mensagem morta. |
| SubscriptionDescription.RequiresSession |A ID da sessão é nula. |A entidade habilitada para sessão não permite uma mensagem cuja identificação de sessão seja nula. |
| !fila de mensagens mortas |MaxTransferHopCountExceeded |Nulo |
| Mensagem morta explícita do aplicativo |Especificado pelo aplicativo |Especificado pelo aplicativo |

## <a name="exceeding-maxdeliverycount"></a>Excedendo MaxDeliveryCount
Cada uma das filas e assinaturas tem uma propriedade [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount) e [SubscriptionDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription#Microsoft_ServiceBus_Messaging_SubscriptionDescription_MaxDeliveryCount), respectivamente; o valor padrão é 10. Sempre que uma mensagem tiver sido entregue em um bloqueio ([ReceiveMode.PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode)), mas tiver sido explicitamente abandonada ou o bloqueio tiver expirado, [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeliveryCount) será incrementada. Quando [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_DeliveryCount) exceder [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount), a mensagem será movida para a DLQ, especificando o código de motivo `MaxDeliveryCountExceeded`.

Esse comportamento não pode ser desabilitado, mas você pode definir [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount) para um número bem alto.

## <a name="exceeding-timetolive"></a>Excedendo TimeToLive
Quando a propriedade [QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_EnableDeadLetteringOnMessageExpiration) ou [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription#Microsoft_ServiceBus_Messaging_SubscriptionDescription_EnableDeadLetteringOnMessageExpiration) for definida como **true** (o padrão é **false**), todas as mensagens expiradas serão movidas para a DLQ, especificando o código de motivo `TTLExpiredException`.

Observe que as mensagens expiradas são apenas limpas e, portanto, movidas para a DLQ quando há pelo menos um receptor ativo extraindo na assinatura ou fila principal; esse comportamento é intencional.

## <a name="errors-while-processing-subscription-rules"></a>Erros ao processar as regras de assinatura
Quando a propriedade [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription#Microsoft_ServiceBus_Messaging_SubscriptionDescription_EnableDeadLetteringOnFilterEvaluationExceptions) for habilitada para uma assinatura, qualquer erro que ocorra durante as execuções da regra de filtro SQL de uma assinatura será capturado na DLQ com a mensagem ofensiva.

## <a name="application-level-dead-lettering"></a>Mensagens mortas no nível de aplicativo
Além dos recursos de mensagens mortas fornecidas pelo sistema, os aplicativos podem usar a DLQ para rejeitar explicitamente mensagens inaceitáveis. Isso pode incluir mensagens que não podem ser processadas adequadamente devido a qualquer tipo de problema no sistema, mensagens que mantêm cargas malformadas ou mensagens que falham na autenticação quando algum esquema de segurança no nível de mensagem é usado.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Mensagens mortas em cenários de ForwardTo ou SendVia

Mensagens serão enviadas para a fila de mensagens mortas para transferência sob as seguintes condições:

- Uma mensagem passa por mais de 3 filas ou tópicos [encadeados](service-bus-auto-forwarding.md).
- A fila ou o tópico de destino é desabilitado ou excluído.
- O tópico ou a fila de destino excede o tamanho máximo de entidade.

Para recuperar essas mensagens mortas, você pode criar um destinatário usando o método utilitário [FormatTransferDeadletterPath](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_FormatTransferDeadLetterPath_System_String_).

## <a name="example"></a>Exemplo
O trecho de código a seguir cria um receptor de mensagem. No loop de recebimento da fila principal, o código recupera a mensagem com [Receive(TimeSpan.Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_Receive_System_TimeSpan_), que solicita ao agente para retornar instantaneamente qualquer mensagem prontamente disponível ou retornar sem resultado. Se o código receber uma mensagem, ele a abandonará imediatamente, o que incrementa a `DeliveryCount`. Depois que o sistema move a mensagem para a DLQ, a fila principal é esvaziada e o loop é encerrado, pois [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver#Microsoft_ServiceBus_Messaging_MessageReceiver_ReceiveAsync_System_TimeSpan_) retorna **null**.

```csharp
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Confira os artigos a seguir para obter mais informações sobre as filas do Barramento de Serviço:

* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Filas do Azure e filas do Barramento de Serviço – comparações](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

