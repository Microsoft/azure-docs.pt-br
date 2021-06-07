---
title: Filas de mensagens mortas do Barramento de Serviço | Microsoft Docs
description: Descreve as filas de mensagens mortas no Barramento de Serviço do Azure. As filas e as assinaturas de tópico do Barramento de Serviço fornecem uma subfila secundária chamada fila de mensagens mortas.
ms.topic: article
ms.date: 06/23/2020
ms.custom: fasttrack-edit, devx-track-csharp
ms.openlocfilehash: ad62f946584071e7ce6fd55f48b5f7ee8db44a2f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98630091"
---
# <a name="overview-of-service-bus-dead-letter-queues"></a>Visão geral das filas de mensagens mortas do Barramento de Serviço

As filas e as assinaturas de tópico do Barramento de Serviço do Azure fornecem uma subfila secundária chamada DLQ (*fila de mensagens mortas*). A fila de mensagens mortas não precisa ser explicitamente criada e não pode ser excluída ou de alguma forma gerenciada independentemente da entidade principal.

Este artigo descreve as filas de mensagens mortas no Barramento de Serviço. Grande parte da discussão é ilustrada pelo [exemplo de Filas de mensagens mortas](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/DeadletterQueue) no GitHub.
 
## <a name="the-dead-letter-queue"></a>A fila de mensagens mortas

A finalidade da fila de mensagens mortas é manter mensagens que não podem ser entregues a nenhum receptor ou mensagens que não puderam ser processadas. As mensagens podem ser removidas da DLQ e inspecionadas. Um aplicativo pode, com a ajuda de um operador, corrigir problemas e reenviar a mensagem, registrar o fato de que houve um erro e tomar uma medida corretiva. 

De uma perspectiva de API e protocolo, a DLQ é bem semelhante a qualquer outra fila, com exceção de que essas mensagens podem ser enviadas apenas por meio da operação de mensagens mortas da entidade pai. Além disso, a vida útil não é observada, e você não pode colocar uma mensagem em estado morto na DLQ. A fila de mensagens mortas é totalmente compatível com operações transacionais e de entrega de espiada-bloqueio.

Não há limpeza automática da DLQ. As mensagens permanecem na DLQ até você recuperá-las de lá explicitamente e chamar [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) na mensagem morta.

## <a name="dlq-message-count"></a>Contagem de mensagens da DLQ
Não é possível obter a contagem de mensagens da fila de mensagens mortas no nível do tópico. Isso ocorre porque as mensagens não ficam no nível do tópico, a menos que o Barramento de Serviço gere um erro interno. Em vez disso, quando um remetente envia uma mensagem a um tópico, a mensagem é encaminhada para as assinaturas do tópico em milissegundos e, portanto, não reside mais no nível do tópico. Portanto, você pode ver mensagens na DLQ associada à assinatura do tópico. No exemplo a seguir, **Service Bus Explorer** mostra que atualmente há 62 mensagens em DLQ para a assinatura "test1". 

![Contagem de mensagens da DLQ](./media/service-bus-dead-letter-queues/dead-letter-queue-message-count.png)

Você também pode obter a contagem de mensagens DLQ usando o comando da CLI do Azure: [`az servicebus topic subscription show`](/cli/azure/servicebus/topic/subscription#az-servicebus-topic-subscription-show). 

## <a name="moving-messages-to-the-dlq"></a>Movendo mensagens para a DLQ

Há várias atividades no Barramento de Serviço que fazem com que as mensagens sejam enviadas por push à DLQ de dentro do próprio mecanismo de mensagens. Um aplicativo também pode explicitamente colocar mensagens na DLQ. 

Como a mensagem é movida pelo agente, duas propriedades são adicionadas à mensagem, uma vez que o agente chama sua versão interna do método [DeadLetter](/dotnet/api/microsoft.azure.servicebus.queueclient.deadletterasync) na mensagem: `DeadLetterReason` e `DeadLetterErrorDescription`.

Os aplicativos podem definir seus próprios códigos para a propriedade `DeadLetterReason`, mas o sistema define os valores a seguir.

| DeadLetterReason | DeadLetterErrorDescription |
| --- | --- |
|HeaderSizeExceeded |A cota de tamanho para este fluxo foi excedida. |
|TTLExpiredException |A mensagem expirou e foi colocada no estado de mensagem morta. Confira a seção [Excedendo TimeToLive](#exceeding-timetolive) para obter detalhes. |
|A ID da sessão é nula. |A entidade habilitada para sessão não permite uma mensagem cuja identificação de sessão seja nula. |
|MaxTransferHopCountExceeded | O número máximo de saltos permitidos ao encaminhar entre filas. O valor é definido como 4. |
| MaxDeliveryCountExceededExceptionMessage | A mensagem não pôde ser consumida após o máximo de tentativas da entrega. Confira a seção [Como exceder o MaxDeliveryCount](#exceeding-maxdeliverycount) para obter detalhes. |

## <a name="exceeding-maxdeliverycount"></a>Excedendo MaxDeliveryCount

Cada uma das filas e assinaturas tem uma propriedade [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) e [SubscriptionDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount), respectivamente; o valor padrão é 10. Sempre que uma mensagem tiver sido entregue em um bloqueio ([ReceiveMode.PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)), mas tiver sido explicitamente abandonada ou o bloqueio tiver expirado, [BrokeredMessage.DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) será incrementada. Quando [DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) exceder [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount), a mensagem será movida para a DLQ, especificando o código de motivo `MaxDeliveryCountExceeded`.

Esse comportamento não pode ser desabilitado, mas você pode definir [MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) para um número alto.

## <a name="exceeding-timetolive"></a>Excedendo TimeToLive

Quando a propriedade [QueueDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.queuedescription) ou [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) for definida como **true** (o padrão é **false**), todas as mensagens expiradas serão movidas para a DLQ, especificando o código de motivo `TTLExpiredException`.

As mensagens expiradas só são limpas e movidas para o DLQ quando há pelo menos uma recepção de receptor ativa da fila principal ou da assinatura, e [as mensagens adiadas](./message-deferral.md) também não serão limpas e movidas para a fila de mensagens mortas depois que elas expirarem. Esses comportamentos são por design.

## <a name="errors-while-processing-subscription-rules"></a>Erros ao processar as regras de assinatura

Quando a propriedade [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) for habilitada para uma assinatura, qualquer erro que ocorra durante as execuções da regra de filtro SQL de uma assinatura será capturado na DLQ com a mensagem ofensiva. Não use essa opção em um ambiente de produção no qual nem todos os tipos de mensagem tenham assinantes.

## <a name="application-level-dead-lettering"></a>Mensagens mortas no nível de aplicativo

Além dos recursos de mensagens mortas fornecidas pelo sistema, os aplicativos podem usar a DLQ para rejeitar explicitamente mensagens inaceitáveis. Elas podem incluir mensagens que não puderam ser processadas adequadamente devido a qualquer tipo de problema no sistema, mensagens que mantêm cargas malformadas ou mensagens que falham na autenticação quando algum esquema de segurança no nível de mensagem é usado.

## <a name="dead-lettering-in-forwardto-or-sendvia-scenarios"></a>Mensagens mortas em cenários de ForwardTo ou SendVia

Mensagens serão enviadas para a fila de mensagens mortas para transferência sob as seguintes condições:

- Uma mensagem que passe por mais de quatro filas ou tópicos [encadeados](service-bus-auto-forwarding.md).
- A fila ou o tópico de destino é desabilitado ou excluído.
- O tópico ou a fila de destino excede o tamanho máximo de entidade.

Para recuperar essas mensagens mortas, você pode criar um destinatário usando o método utilitário [FormatTransferDeadletterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formattransferdeadletterpath).

## <a name="example"></a>Exemplo

O snippet de código a seguir cria um receptor de mensagem. No loop de recebimento da fila principal, o código recupera a mensagem com [Receive(TimeSpan.Zero)](/dotnet/api/microsoft.servicebus.messaging.messagereceiver), que solicita ao agente para retornar instantaneamente qualquer mensagem prontamente disponível ou retornar sem resultado. Se o código receber uma mensagem, ele a abandonará imediatamente, o que incrementa a `DeliveryCount`. Depois que o sistema move a mensagem para a DLQ, a fila principal é esvaziada e o loop é encerrado, pois [ReceiveAsync](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) retorna **null**.

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

## <a name="path-to-the-dead-letter-queue"></a>Caminho para a fila de mensagens mortas
É possível acessar a fila de mensagens mortas usando a seguinte sintaxe:

```
<queue path>/$deadletterqueue
<topic path>/Subscriptions/<subscription path>/$deadletterqueue
```

Se você estiver usando o SDK do .NET, poderá obter o caminho para a fila de mensagens mortas usando o método SubscriptionClient.FormatDeadLetterPath(). Esse método usa o nome do tópico/assinatura e os sufixos com **/$DeadLetterQueue**.


## <a name="next-steps"></a>Próximas etapas

Confira os artigos a seguir para obter mais informações sobre as filas do Barramento de Serviço:

* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Filas do Azure e filas do Barramento de Serviço – comparações](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

