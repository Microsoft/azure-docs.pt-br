---
title: "Sessões de mensagens do Barramento de Serviço do Azure | Microsoft Docs"
description: "Manipule sequências de mensagens do Barramento de Serviço do Azure com sessões."
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
ms.date: 09/28/2017
ms.author: sethm
ms.openlocfilehash: c6441d2119518e89a869ee52e5f0b80450ae2bbe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="message-sessions--first-in-first-out-fifo"></a>Sessões de mensagem/PEPS (primeiro a entrar, primeiro a sair) 

As sessões de Barramento de Serviço permitem o tratamento conjunto ordenado de sequências não associadas de mensagens relacionadas. Para obter uma garantia de PEPS no Barramento de Serviço, você precisa usar sessões. O Barramento de Serviço não é prescritivo em relação à natureza da relação entre as mensagens e também não define um modelo específico para determinar onde uma sequência de mensagem começa ou termina.

Qualquer remetente pode criar uma sessão ao enviar mensagens em uma fila ou tópico definindo a propriedade de agente [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) para algum identificador definido pelo aplicativo que é exclusivo para a sessão. No nível do protocolo AMQP 1.0, esse valor é mapeado para a propriedade *group-id*.

Em filas ou assinaturas com reconhecimento de sessão, as sessões passam a existir quando há pelo menos uma mensagem na [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) da sessão. Quando uma sessão existe, não há uma API ou tempo definido para quando a sessão expira ou desaparece. Teoricamente, uma mensagem pode ser recebida em uma sessão hoje, a próxima mensagem em um ano e se a **SessionId** corresponder, a sessão é a mesma da perspectiva do Barramento de Serviço.

Normalmente, no entanto, um aplicativo tem uma noção clara de onde um conjunto de mensagens relacionadas começa e termina, mas o Barramento de Serviço não define nenhuma regra específica.

Um exemplo de como delinear uma sequência para transferir um arquivo é definir a propriedade **Label** para a primeira mensagem como **start**, para as mensagens intermediárias como **content** e para a última mensagem como **end**. A posição relativa das mensagens de conteúdo pode ser computada como o delta de *SequenceNumber* do *SequenceNumber* da mensagem de **start**.

O recurso de sessão no Barramento de Serviço habilita uma operação de recebimento específica, na forma de [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) nas APIs Java e C#. Habilite o recurso definindo a propriedade [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) na fila ou assinatura por meio do Azure Resource Manager ou definindo o sinalizador no portal. Isso é necessário antes de tentar usar as operações de API relacionadas.

No portal, o sinalizador é definido com a caixa de seleção a seguir:

![][2]

As APIs para sessões existem em clientes de fila e assinatura. Há um modelo imperativo em que você pode controlar quando as sessões e mensagens são recebidas e um modelo baseado no manipulador, semelhante ao *OnMessage*, que oculta a complexidade do gerenciamento do loop de recebimento.

## <a name="session-features"></a>Recursos de sessão

As sessões fornecem demultiplexação simultânea de fluxos de mensagens intercaladas enquanto preserva e garante a entrega ordenada.

![][1]

Um destinatário de [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) é criado pelo cliente aceitando uma sessão. Obrigatoriamente, o cliente chama [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) ou [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) no C#. No modelo de retorno de chamada reativo, ele registra um manipulador de sessão, conforme discutido posteriormente.

Quando a [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) é aceita e enquanto ela é mantida por um cliente, esse cliente mantém um bloqueio exclusivo em todas as mensagens com a [SessionId](/en-us/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid?view=azureservicebus-4.1.1#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) dessa sessão existente na fila ou assinatura e também em todas as mensagens com essa **SessionId** que ainda chegam enquanto a sessão é mantida.

O bloqueio é liberado quando **Close** ou **CloseAsync** é chamado ou quando o bloqueio expira em casos em que o aplicativo não pode fazer isso. O bloqueio de sessão deve ser tratado como um bloqueio exclusivo em um arquivo, o que significa que o aplicativo deve fechar a sessão assim que não precisar mais dela e/ou não esperar nenhuma mensagem adicional.

Quando vários destinatários simultâneos efetuam o pull da fila, as mensagens que pertencem a uma sessão específica são expedidas para um destinatário específico que atualmente mantém o bloqueio para a sessão. Com essa operação, um fluxo de mensagens intercaladas que reside em uma fila ou assinatura é corretamente desmultiplexado para diferentes destinatários e esses destinatários também podem residir em computadores cliente diferentes, uma vez que o gerenciamento de bloqueio ocorre no lado do servidor, dentro do Barramento de Serviço.

Uma fila, no entanto, ainda é uma fila: não há nenhum acesso aleatório. Se vários destinatários simultâneos aguardarem para aceitar sessões específicas ou aguardarem mensagens de sessões específicas e houver uma mensagem no tipo da fila pertencente a uma sessão que nenhum destinatário reivindicou ainda, as entregas aguardam até um destinatário de sessão reivindicar essa sessão.

A ilustração anterior mostra três destinatários de sessões simultâneas, que devem pegar ativamente mensagens da fila para cada destinatário para progredir. A sessão anterior com *SessionId*=4 não tem nenhum cliente proprietário ativo, o que significa que nenhuma mensagem é entregue a ninguém até que essa mensagem seja pega por um destinatário de sessão proprietário recém-criado.

Embora isso possa parecer restritivo, um único processo de destinatário pode manipular muitas sessões simultâneas facilmente, especialmente quando elas são escritas com código estritamente assíncrono, lidar com dezenas de sessões simultâneas é efetivamente automático com o modelo de retorno de chamada.

A estratégia para lidar com várias sessões simultâneas, em que cada sessão recebe mensagens apenas esporadicamente, é o manipulador remover a sessão após um tempo ocioso e retomar o processamento quando a sessão for aceita conforme a chegada da próxima sessão.

O bloqueio da sessão mantido pelo destinatário da sessão é um abrangente para os bloqueios de mensagem usados pelo modo de liquidação de *bloqueio de pico*. Um destinatário não pode ter duas mensagens simultaneamente "em trânsito", mas as mensagens devem ser processadas em ordem. Uma nova mensagem só pode ser obtida quando a mensagem anterior foi concluída ou definida como morta. Abandonar uma mensagem faz com que a mesma mensagem seja atendida novamente com a próxima operação de recebimento.

## <a name="message-session-state"></a>Gerenciar estado de sessão

Quando os fluxos de trabalho são processados sistemas de nuvem de alta disponibilidade e grande escala, o manipulador de fluxo de trabalho associado a uma determinada sessão deve ser capaz de se recuperar de falhas inesperadas e também ser capaz de retomar o trabalho concluído parcialmente em um computador ou processo diferente do em que o trabalho começou.

O recurso de estado de sessão permite uma anotação definida pelo aplicativo de uma sessão de mensagem dentro do agente, de forma que o estado de processamento registrado relativo a essa sessão seja disponibilizado automaticamente quando a sessão é adquirida por um novo processador.

Da perspectiva do Barramento de Serviço, o estado de sessão da mensagem é um objeto binário opaco que pode conter dados do tamanho de uma mensagem, que é 256 KB para o Barramento de Serviço e 1 MB para o Barramento de Serviço Premium. O estado de processamento relativo a uma sessão pode ser mantido dentro do estado de sessão ou o estado de sessão pode apontar para um registro de banco de dados ou local de armazenamento que contém tal informação.

As APIs para gerenciar o estado de sessão, [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) e [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), podem ser encontradas no objeto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) nas APIs de Java e C#. Uma sessão que anteriormente não tinha nenhum estado de sessão definido retorna uma referência **nula** para **GetState**. A limpeza do estado de sessão definido anteriormente é feita com [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

Todas as sessões existentes em uma fila ou assinatura podem ser enumeradas com o método **SessionBrowser** na API de Java e com [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) em [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) e [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) no cliente .NET.

O estado de sessão mantido em uma fila ou em uma assinatura conta para a cota de armazenamento dessa entidade. Quando o aplicativo é concluído com uma sessão, é recomendável, que o aplicativo limpe seu estado retido para evitar custos de gerenciamento externo.

## <a name="next-steps"></a>Próximas etapas

- [Um exemplo completo](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/BasicSessionSendReceiveUsingQueueClient) do envio e recebimento de mensagens baseadas em sessão de filas do Barramento de Serviço usando a biblioteca .NET Standard.
- [Um exemplo](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) que usa o cliente do .NET Framework para lidar com mensagens com reconhecimento de sessão. 

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os tópicos a seguir:

* [Conceitos fundamentais do barramento de serviço](service-bus-fundamentals-hybrid-solutions.md)
* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png