---
title: "Contagem de mensagens do Barramento de Serviço do Azure | Microsoft Docs"
description: "Recupere a contagem de mensagens do Barramento de Serviço do Azure."
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
ms.date: 10/03/2017
ms.author: sethm
ms.openlocfilehash: f5d2aa551bbe77a66459907cf5cd1313bb907981
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="message-counters"></a>Contadores de mensagens

Você pode recuperar a contagem de mensagens mantidas em filas e assinaturas usando o Azure Resource Manager e as APIs [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) do Barramento de Serviço no SDK do .NET Framework.

Com o PowerShell, você pode obter a contagem da seguinte maneira:

```powershell
(Get-AzureRmServiceBusQueue -ResourceGroup mygrp -NamespaceName myns -QueueName myqueue).CountDetails
```

## <a name="message-count-details"></a>Detalhes da contagem de mensagens

Saber a contagem de mensagens ativas é útil para determinar se uma fila cria uma lista de pendências que requer mais recursos para o processamento do que o que foi implantado atualmente. Os detalhes de contador a seguir estão disponíveis na classe [MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails):

-   [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ActiveMessageCount): as mensagens na fila ou na assinatura que estão no estado ativo e estão prontas para entrega.
-   [DeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.deadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_DeadLetterMessageCount): as mensagens na fila de mensagens mortas.
-   [ScheduledMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.scheduledmessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_ScheduledMessageCount): as mensagens no estado agendado.
-   [TransferDeadLetterMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transferdeadlettermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferDeadLetterMessageCount): as mensagens com falha na transferência para outra fila ou tópico e que foram movidas para a fila de mensagens mortas de transferência.
-   [TransferMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.transfermessagecount#Microsoft_ServiceBus_Messaging_MessageCountDetails_TransferMessageCount): as mensagens pendentes são transferidas para outra fila ou tópico.

Se um aplicativo deseja dimensionar os recursos com base no comprimento da fila, ele deve fazer isso com um ritmo muito calculado. A aquisição dos contadores de mensagem é uma operação cara dentro do agente de mensagens e executá-la com frequência afeta de forma direta e adversa o desempenho da entidade.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os tópicos a seguir:

* [Conceitos fundamentais do barramento de serviço](service-bus-fundamentals-hybrid-solutions.md)
* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)