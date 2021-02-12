---
title: Sessões de mensagens do Barramento de Serviço do Azure | Microsoft Docs
description: Este artigo explica como usar as sessões para habilitar a manipulação ordenada e conjunta de sequências não associadas de mensagens relacionadas.
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: 6d316571d69d2e1e73ddca4ccca53c116ee8fa5f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98680746"
---
# <a name="message-sessions"></a>Sessões de mensagem
As sessões do Barramento de Serviço do Microsoft Azure permitem o tratamento conjunto e ordenado de sequências não associadas de mensagens relacionadas. As sessões podem ser usadas em padrões **PEPS (primeiro a entrar, primeiro a sair)** e **solicitação-resposta**. Este artigo mostra como usar sessões para implementar esses padrões ao usar o Barramento de Serviço. 

> [!NOTE]
> A camada básica do Barramento de Serviço não dá suporte a sessões. As camadas Standard e Premium dão suporte a sessões. Para conferir as diferenças entre essas camadas, consulte [preços do Barramento de Serviço](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="first-in-first-out-fifo-pattern"></a>Padrão PEPS (primeiro a entrar, primeiro a sair)
Para obter uma garantia de PEPS no Barramento de Serviço, use as sessões. O Barramento de Serviço não é prescritivo em relação à natureza da relação entre as mensagens e também não define um modelo específico para determinar onde uma sequência de mensagem começa ou termina.

Qualquer remetente pode criar uma sessão ao enviar mensagens em uma fila ou tópico definindo a propriedade [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) como algum identificador definido pelo aplicativo que é exclusivo para a sessão. No nível do protocolo AMQP 1.0, esse valor é mapeado para a propriedade *group-id*.

Em filas ou assinaturas com reconhecimento de sessão, as sessões passam a existir quando há pelo menos uma mensagem na [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) da sessão. Quando uma sessão existe, não há uma API ou tempo definido para quando a sessão expira ou desaparece. Teoricamente, uma mensagem pode ser recebida em uma sessão hoje, a próxima mensagem em um ano e se a **SessionId** corresponder, a sessão é a mesma da perspectiva do Barramento de Serviço.

Normalmente, no entanto, um aplicativo tem uma noção clara de onde um conjunto de mensagens relacionadas começa e termina. O Barramento de Serviço não define nenhuma regra específica.

Um exemplo de como delinear uma sequência para transferir um arquivo é definir a propriedade **Label** para a primeira mensagem como **start**, para as mensagens intermediárias como **content** e para a última mensagem como **end**. A posição relativa das mensagens de conteúdo pode ser computada como o delta de *SequenceNumber* da mensagem atual com relação ao *SequenceNumber* da mensagem **start**.

O recurso de sessão no Barramento de Serviço habilita uma operação de recebimento específica, na forma de [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) nas APIs Java e C#. Habilite o recurso definindo a propriedade [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) na fila ou assinatura por meio do Azure Resource Manager ou definindo o sinalizador no portal. Isso é necessário antes de tentar usar as operações de API relacionadas.

No portal, defina o sinalizador com a caixa de seleção a seguir:

![Captura de tela da caixa de diálogo Criar fila com a opção habilitar sessões selecionada e descrita em vermelho.][2]

> [!NOTE]
> Quando as sessões são habilitadas em uma fila ou em uma assinatura, os aplicativos cliente **não podem mais** enviar/receber mensagens regulares. Todas as mensagens devem ser enviadas como parte de uma sessão (definindo a ID de sessão) e recebidas por meio do recebimento da sessão.

As APIs para sessões existem em clientes de fila e assinatura. Há um modelo imperativo que controla quando as sessões e mensagens são recebidas e um modelo baseado em manipulador, semelhante ao _OnMessage *, que oculta a complexidade do gerenciamento do loop de recebimento.

### <a name="session-features"></a>Recursos de sessão

As sessões fornecem a demultiplexação simultânea de fluxos de mensagens intercaladas enquanto preserva e garante a entrega ordenada.

![Um diagrama que mostra como o recurso de sessões preserva a entrega ordenada.][1]

Um destinatário de [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) é criado pelo cliente aceitando uma sessão. O cliente chama [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) ou [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) no C#. No modelo de retorno de chamada reativo, ele registra um manipulador de sessão.

Quando o objeto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) é aceito e enquanto é mantido por um cliente, esse cliente mantém um bloqueio exclusivo em todas as mensagens com a [SessionId](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) dessa sessão existente na fila ou assinatura e também em todas as mensagens com essa **SessionId** que ainda chegam enquanto a sessão é mantida.

O bloqueio é liberado quando **Close** ou **CloseAsync** é chamado ou quando o bloqueio expira em casos em que o aplicativo não consegue executar a operação de fechamento. O bloqueio de sessão deve ser tratado como um bloqueio exclusivo em um arquivo, o que significa que o aplicativo deve fechar a sessão assim que não precisar mais dela e/ou não esperar nenhuma mensagem adicional.

Quando vários destinatários simultâneos efetuam o pull da fila, as mensagens que pertencem a uma sessão específica são expedidas para um destinatário específico que atualmente mantém o bloqueio para a sessão. Com essa operação, um fluxo de mensagens intercaladas em uma fila ou assinatura é corretamente desmultiplexado para diferentes destinatários e esses destinatários também podem residir em computadores cliente diferentes, uma vez que o gerenciamento de bloqueio ocorre no lado do servidor, dentro do Barramento de Serviço.

A ilustração anterior mostra três receptores de sessão concomitantes. Uma Sessão com `SessionId` = 4 não tem nenhum cliente proprietário e ativo, o que significa que nenhuma mensagem é entregue dessa sessão específica. Uma sessão atua de várias maneiras, como uma subfila.

O bloqueio da sessão mantido pelo destinatário da sessão é um abrangente para os bloqueios de mensagem usados pelo modo de liquidação de *bloqueio de pico*. Somente um destinatário pode ter um bloqueio em uma sessão. Um destinatário pode ter muitas mensagens em andamento, mas as mensagens serão recebidas na ordem. Abandonar uma mensagem faz com que a mesma mensagem seja atendida novamente com a próxima operação de recebimento.

### <a name="message-session-state"></a>Gerenciar estado de sessão

Quando os fluxos de trabalho são processados em sistemas de nuvem de alta disponibilidade e grande escala, o manipulador de fluxo de trabalho associado a uma determinada sessão deve ser capaz de se recuperar de falhas inesperadas e de retomar o trabalho concluído parcialmente em um computador ou processo diferente do em que o trabalho começou.

O recurso de estado de sessão permite uma anotação definida pelo aplicativo de uma sessão de mensagem dentro do agente, de forma que o estado de processamento registrado relativo a essa sessão seja disponibilizado automaticamente quando a sessão é adquirida por um novo processador.

Da perspectiva do Barramento de Serviço, o estado de sessão da mensagem é um objeto binário opaco que pode conter dados do tamanho de uma mensagem, que é 256 KB para o Barramento de Serviço e 1 MB para o Barramento de Serviço Premium. O estado de processamento relativo a uma sessão pode ser mantido dentro do estado de sessão ou o estado de sessão pode apontar para um registro de banco de dados ou local de armazenamento que contém tal informação.

As APIs para gerenciar o estado de sessão, [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) e [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), podem ser encontradas no objeto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) nas APIs de Java e C#. Uma sessão que anteriormente não tinha nenhum estado de sessão definido retorna uma referência **nula** para **GetState**. A limpeza do estado de sessão definido anteriormente é feita com [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

O estado de sessão permanece, desde que não tenha sido desmarcado (retornando **nulo**), mesmo que todas as mensagens em uma sessão sejam consumidas.

Todas as sessões existentes em uma fila ou assinatura podem ser enumeradas com o método **SessionBrowser** na API de Java e com [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) em [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) e [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) no cliente .NET Framework.

O estado de sessão mantido em uma fila ou em uma assinatura conta para a cota de armazenamento dessa entidade. Quando o aplicativo é concluído com uma sessão, é recomendável, que o aplicativo limpe seu estado retido para evitar custos de gerenciamento externo.

### <a name="impact-of-delivery-count"></a>Impacto da contagem de entregas

A definição de contagem de entregas por mensagem no contexto de sessões varia um pouco da definição na ausência de sessões. Eis uma tabela que resume quando a contagem de entregas é incrementada.

| Cenário | A contagem de entregas da mensagem é incrementada |
|----------|---------------------------------------------|
| A sessão é aceita, mas o bloqueio de sessão expira (devido ao tempo limite) | Sim |
| A sessão é aceita, as mensagens dentro da sessão não são concluídas (mesmo se estiverem bloqueadas), e a sessão é fechada | Não |
| A sessão é aceita, as mensagens são concluídas e, depois, a sessão é fechada explicitamente | N/A (é o fluxo padrão. Aqui, as mensagens são removidas da sessão) |

## <a name="request-response-pattern"></a>Padrão de solicitação-resposta
O [padrão solicitação-resposta](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) é um padrão de integração bem estabelecido que permite ao aplicativo remetente enviar uma solicitação e fornece uma maneira de o destinatário enviar corretamente uma resposta de volta para o aplicativo remetente. Esse padrão normalmente precisa de uma fila ou tópico de curta duração para o aplicativo enviar respostas. Nesse cenário, as sessões fornecem uma solução alternativa simples com semântica comparável. 

Vários aplicativos podem enviar suas solicitações a uma única fila de solicitações, com um parâmetro de cabeçalho específico definido para identificar exclusivamente o aplicativo remetente. O aplicativo destinatário pode processar as solicitações recebidas na fila e enviar respostas na fila habilitada para a sessão, definindo a ID da sessão como o identificador exclusivo que o remetente enviou na mensagem de solicitação. Depois, o aplicativo que enviou a solicitação poderá receber mensagens na ID de sessão específica e processar as respostas corretamente.

> [!NOTE]
> O aplicativo que envia as solicitações iniciais deve saber sobre a ID da sessão e usar `SessionClient.AcceptMessageSession(SessionID)` para bloquear a sessão na qual está esperando a resposta. É uma boa ideia usar um GUID que identifique exclusivamente a instância do aplicativo como uma ID de sessão. Não deve haver nenhum manipulador de sessão nem `AcceptMessageSession(timeout)` na fila para garantir que as respostas estejam disponíveis para serem bloqueadas e processadas por destinatários específicos.

## <a name="next-steps"></a>Próximas etapas

- Consulte as [amostras Microsoft.Azure.ServiceBus samples](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) ou [amostras Microsoft.ServiceBus.Messaging samples](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) para ver um exemplo que usa o cliente .NET Framework para lidar com mensagens com reconhecimento de sessão. 

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os seguintes tópicos:

* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
