---
title: "Mensagens de pré-busca do Barramento de Serviço do Azure | Microsoft Docs"
description: "Melhore o desempenho realizando a pré-busca de mensagens do Barramento de Serviço do Azure."
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
ms.date: 10/04/2017
ms.author: sethm
ms.openlocfilehash: 4a4a06f90c2c48d35d836f0be89fec9cc47f32c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="prefetch-azure-service-bus-messages"></a>Executar a pré-busca de mensagens do Barramento de Serviço do Azure

Quando a *Pré-busca* está habilitada em qualquer um dos clientes oficiais do Barramento de Serviço, o destinatário obtém silenciosamente mais mensagens, até o limite de [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount), além do que o aplicativo solicitou inicialmente.

Uma única chamada inicial para [Receive](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) ou [ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) adquire, portanto, uma mensagem para consumo imediato que é retornado assim que está disponível. O cliente adquire então mais mensagens em segundo plano para preencher o buffer de pré-busca.

## <a name="enable-prefetch"></a>Habilitar a pré-busca

No .NET, você deve habilita o recurso de Pré-busca definindo a propriedade [PrefetchCount](/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount#Microsoft_Azure_ServiceBus_QueueClient_PrefetchCount) de um **MessageReceiver**, **QueueClient** ou **SubscriptionClient** com um número maior que zero. Definir o valor como zero desativa pré-busca.

Você pode adicionar facilmente esta configuração ao lado de recebimento das configurações dos exemplos [QueuesGettingStarted](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/QueuesGettingStarted) ou [ReceiveLoop](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ReceiveLoop) para ver o efeito nesses contextos.

Enquanto as mensagens estão disponíveis no buffer de pré-busca, qualquer chamada subsequente para **Receive**/**ReceiveAsync** é atendida imediatamente do buffer e o buffer é reposto em segundo plano conforme o espaço se torna disponível. Se não houver nenhuma mensagem disponível para entrega, a operação de recebimento esvazia o buffer e aguarda ou bloqueia, conforme esperado.

A pré-busca também funciona da mesma forma com as APIs [OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage) e [OnMessageAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessageasync).

## <a name="if-it-is-faster-why-is-prefetch-not-the-default-option"></a>Se ela é mais rápida, por que Pré-busca não é a opção padrão?

A Pré-busca acelera o fluxo de mensagens ao ter uma mensagem prontamente disponível para recuperação local quando e antes de o aplicativo solicitar uma. Esse ganho de taxa de transferência é o resultado de uma decisão de compensação que o autor do aplicativo deve tomar explicitamente:

Com o modo de recebimento [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode.receiveanddelete), todas as mensagens adquiridas no buffer de pré-busca não estão mais disponíveis na fila e residem apenas no buffer de pré-busca na memória até serem recebidas no aplicativo por meio das APIs **Receive**/**ReceiveAsync** ou **OnMessage**/**OnMessageAsync**. Se o aplicativo for encerrado antes que as mensagens sejam recebidas no aplicativo, essas mensagens serão perdidas de forma irrecuperável.

No modo de recebimento [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode.peeklock), as mensagens buscadas no buffer de Pré-busca são obtidas no buffer em um estado bloqueado e têm o tempo limite para o bloqueio sendo contado. Se o buffer de pré-busca for grande e processamento demorar tanto que o bloqueio da mensagem expira enquanto reside no buffer de pré-busca ou até mesmo enquanto o aplicativo está processando a mensagem, poderão ocorrer eventos confusos para o aplicativo lidar.

O aplicativo pode adquirir uma mensagem com um bloqueio expirado ou prestes a expirar. Nesse caso, o aplicativo pode processar a mensagem, mas depois descobrir que não pode concluir devido à expiração do bloqueio. O aplicativo pode verificar a propriedade [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.lockeduntilutc#Microsoft_Azure_ServiceBus_Core_MessageReceiver_LockedUntilUtc) (que está sujeita à defasagem de tempo entre o agente e o relógio do computador local). Se o bloqueio de mensagem tiver expirado, o aplicativo deverá ignorar a mensagem. Nenhuma chamada à API ou com a mensagem deverá ser feita. Se a mensagem não estiver expirada, mas a expiração for iminente, o bloqueio poderá ser renovado e estendido pelo período de bloqueio padrão chamando [message.RenewLock()](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Se o bloqueio expirar silenciosamente no buffer de pré-busca, a mensagem será tratada como abandonada e novamente disponibilizada para recuperação na fila. Isso pode fazer com que ela seja buscada no buffer de pré-busca, colocada no final. Se o buffer de pré-busca geralmente não puder ser processado durante a expiração da mensagem, isso fará com que as mensagens sejam pré-buscadas repetidamente, mas nunca entregues de forma efetiva em um estado utilizável (validade bloqueada) e serão eventualmente movidas para a fila de mensagens mortas assim que a contagem de entrega máxima for excedida.

Se você precisar de um alto nível de confiabilidade para processamento de mensagens e o processamento exigir tempo e trabalho significativos, é recomendável usar o recurso de pré-busca de forma conservadora ou até mesmo não usá-lo.

Se você precisar de uma taxa de transferência alta e o processamento de mensagens normalmente for barato, a pré-busca gerará benefícios de taxa de transferência significativos.

A contagem de pré-busca máxima e a duração do bloqueio configurado na fila ou na assinatura precisam ser equilibradas, de modo que o tempo limite de bloqueio pelo menos exceda o tempo de processamento de mensagem esperado para o tamanho máximo do buffer de pré-busca, mais uma mensagem. Ao mesmo tempo, o tempo limite de bloqueio não deve ser longo o suficiente para que as mensagens possam exceder seu [TimeToLive](/dotnet/api/microsoft.azure.servicebus.message.timetolive#Microsoft_Azure_ServiceBus_Message_TimeToLive) quando são acidentalmente descartadas, exigindo assim que seu bloqueio expire antes de serem entregues novamente.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os tópicos a seguir:

* [Conceitos fundamentais do barramento de serviço](service-bus-fundamentals-hybrid-solutions.md)
* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)
