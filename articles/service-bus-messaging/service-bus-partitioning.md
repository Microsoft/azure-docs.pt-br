---
title: "Criar tópicos e filas particionados do Barramento de Serviço | Microsoft Docs"
description: "Descreve como particionar filas e tópicos do Barramento de Serviço usando vários agentes de mensagem."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a0c7d5a2-4876-42cb-8344-a1fc988746e7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/07/2017
ms.author: sethm;hillaryc
ms.openlocfilehash: 5a4e69ea7e13cb017f8fb432c524c6a8ce9228a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="partitioned-queues-and-topics"></a>Filas e tópicos particionados
O Barramento de Serviço do Azure emprega vários agentes de mensagens para processar mensagens e vários repositórios de mensagens para armazenar mensagens. Uma fila ou um tópico convencional é manipulado por um único agente de mensagem e armazenado em um repositório de mensagens. As *partições* do Barramento de Serviço permitem que filas ou tópicos, ou *entidades de mensagens*, sejam particionados entre diversos agentes e repositórios de mensagens. Isso significa que a taxa de transferência geral de uma entidade particionada não é mais limitada pelo desempenho de um único agente ou repositório de mensagens. Além disso, uma interrupção temporária de um repositório de mensagens não torna uma fila ou um tópico particionado indisponível. Filas e tópicos particionados podem conter todos os recursos avançados do Barramento de Serviço, como o suporte a transações e sessões.

Para saber mais sobre aspectos internos do Barramento de Serviço, confira o artigo [Arquitetura do Barramento de Serviço][Service Bus architecture].

O particionamento é habilitado por padrão na criação da entidade em todos os tópicos e filas de mensagens Standard e Premium. Você pode criar entidades da camada de mensagens Standard sem particionamento, mas filas e tópicos em um namespace Premium são sempre particionados; essa opção não pode ser desabilitada. 

Não é possível alterar a opção de particionamento em uma fila ou um tópico existente nas camadas Standard e Premium. Você pode definir a opção apenas quando cria a entidade.

## <a name="how-it-works"></a>Como ele funciona

Cada fila ou tópico particionado consiste em vários fragmentos. Cada fragmento é armazenado em um repositório de mensagens diferente e manipulado por um agente de mensagens diferente. Quando uma mensagem é enviada a uma fila ou um tópico particionado, o Barramento de Serviço atribui a mensagem a um dos fragmentos. A seleção é feita aleatoriamente pelo Barramento de Serviço ou por uma chave de partição que pode ser especificada pelo remetente.

Quando um cliente deseja receber uma mensagem de uma fila particionada ou de uma assinatura de um tópico particionado, o Barramento de Serviço consulta todos os fragmentos de mensagens e, em seguida, retorna a primeira mensagem obtida de qualquer um dos repositórios de mensagens para o destinatário. O Barramento de Serviço armazena em cache as outras mensagens e as retorna ao receber solicitações de recebimento adicionais. Um cliente receptor não está ciente do particionamento; o comportamento voltado para o cliente de uma fila ou um tópico particionado (por exemplo, ler, concluir, adiar, colocar mensagem na fila de mensagens mortas, executar pré-busca) é idêntico ao comportamento de uma entidade regular.

Não há custo adicional ao enviar ou receber uma mensagem de uma fila ou um tópico particionado.

## <a name="enable-partitioning"></a>Habilitar particionamento

Para usar filas e tópicos particionados com o Barramento de Serviço do Azure, use o SDK do Azure versão 2.2 ou posterior, ou especifique `api-version=2013-10` em suas solicitações HTTP.

### <a name="standard"></a>Standard

Na camada de mensagens Standard, é possível criar filas e tópicos do Barramento de Serviço em tamanhos de 1, 2, 3, 4 ou 5 GB (o padrão é 1 GB). Com o particionamento habilitado, o Barramento de Serviço cria 16 cópias (16 partições) da entidade para cada GB especificado. Assim, se você criar uma fila que tenha 5 GB, com 16 partições, o tamanho máximo da fila será (5 \* 16) = 80 GB. É possível ver o tamanho máximo da fila ou do tópico particionado observando sua entrada no [Portal do Azure][Azure portal], na folha **Visão Geral** da entidade em questão.

### <a name="premium"></a>Premium

Em um namespace de camada Premium, você pode criar filas e tópicos do Barramento de Serviços em tamanhos de 1, 2, 3, 4, 5, 10, 20, 40 ou 80 GB (o padrão é 1 GB). Com o particionamento habilitado por padrão, o Barramento de Serviço cria duas partições por entidade. É possível ver o tamanho máximo da fila ou do tópico particionado observando sua entrada no [Portal do Azure][Azure portal], na folha **Visão Geral** da entidade em questão.

Para saber mais sobre o particionamento na camada de mensagens Premium, confira [Camadas de sistema de mensagens Premium e Standard do Barramento de Serviço](service-bus-premium-messaging.md). 

### <a name="create-a-partitioned-entity"></a>Criar uma entidade particionada

Há várias maneiras de criar uma fila ou um tópico particionado. Ao criar a fila ou o tópico de seu aplicativo, você pode habilitar o particionamento de fila ou tópico definindo respectivamente a propriedade [QueueDescription.EnablePartitioning][QueueDescription.EnablePartitioning] ou [TopicDescription.EnablePartitioning][TopicDescription.EnablePartitioning] como **true**. Essas propriedades devem ser definidas no momento em que a fila ou o tópico é criado. Conforme declarado anteriormente, não é possível alterar essas propriedades em uma fila ou um tópico existente. Por exemplo:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Como alternativa, você pode criar uma fila ou um tópico particionado no [Portal do Azure][Azure portal] ou no Visual Studio. Ao criar uma fila ou um tópico no portal, a opção **Habilitar particionamento** na folha **Criar** da fila ou do tópico é marcada por padrão. Você só pode desabilitar essa opção em uma entidade da camada Standard; na camada Premium, o particionamento está sempre habilitado. No Visual Studio, clique na caixa de seleção **Habilitar Particionamento** na caixa de diálogo **Nova Fila** ou **Novo Tópico**.

## <a name="use-of-partition-keys"></a>Uso de chaves de partição
Quando uma mensagem é enfileirada em uma fila ou um tópico particionado, o Barramento de Serviço verifica a presença de uma chave de partição. Se encontrar uma, ele selecionará o fragmento com base na chave. Se não encontrar uma chave de partição, ele selecionará o fragmento com base em um algoritmo interno.

### <a name="using-a-partition-key"></a>Usando uma chave de partição
Alguns cenários, como sessões ou transações, exigem que as mensagens sejam armazenadas em um fragmento específico. Todos esses cenários exigem o uso de uma chave de partição. Todas as mensagens que usam a mesma chave de partição são atribuídas ao mesmo fragmento. Se o fragmento estiver indisponível temporariamente, o Barramento de Serviço retornará um erro.

Dependendo do cenário, propriedades de mensagem diferentes são usadas como uma chave de partição:

**SessionId**: caso uma mensagem tenha a propriedade [BrokeredMessage.SessionId][BrokeredMessage.SessionId] definida, o Barramento de Serviço usará essa propriedade como a chave de partição. Dessa forma, todas as mensagens que pertencem à mesma sessão são manipuladas pelo mesmo agente de mensagens. Isso habilita o Barramento de Serviço a garantir a ordenação das mensagens, bem como a consistência dos estados de sessão.

**PartitionKey**: caso uma mensagem tenha a propriedade [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey] definida, mas não a propriedade [BrokeredMessage.SessionId][BrokeredMessage.SessionId], o Barramento de Serviço usará a propriedade [PartitionKey][PartitionKey] como a chave de partição. Caso a mensagem tenha as propriedades [SessionId][SessionId] e [PartitionKey][PartitionKey] definidas, as propriedades deverão ser idênticas. Se a propriedade [PartitionKey][PartitionKey] for definida com um valor diferente da propriedade [SessionId][SessionId], o Barramento de Serviço retornará uma exceção de operação inválida. A propriedade [PartitionKey][PartitionKey] deverá ser usada caso um remetente envie mensagens transacionais sem reconhecimento de sessão. A chave de partição garante que todas as mensagens enviadas em uma transação sejam manipuladas pelo mesmo agente de mensagens.

**MessageId**: caso a fila ou o tópico tenha a propriedade [QueueDescription.RequiresDuplicateDetection][QueueDescription.RequiresDuplicateDetection] definida como **true** e as propriedades [BrokeredMessage.SessionId][BrokeredMessage.SessionId] ou [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey] não forem definidas, a propriedade [BrokeredMessage.MessageId][BrokeredMessage.MessageId] servirá como a chave de partição. (Observe que as bibliotecas Microsoft .NET e AMQP atribuirão automaticamente uma ID de mensagem se o aplicativo de envio não o fizer.) Nesse caso, todas as cópias da mesma mensagem são manipuladas pelo mesmo agente de mensagens. Isso permite que o Barramento de Serviço detecte e elimine mensagens duplicadas. Se a propriedade [QueueDescription.RequiresDuplicateDetection][QueueDescription.RequiresDuplicateDetection] não for definida como **true**, o Barramento de Serviço não considerará a propriedade [MessageId][MessageId] como uma chave de partição.

### <a name="not-using-a-partition-key"></a>Não usando uma chave de partição
Na ausência de uma chave de partição, o Barramento de Serviço distribui mensagens em forma de round robin para todos os fragmentos da fila ou do tópico particionado. Se o fragmento escolhido não estiver disponível, o Barramento de Serviço atribuirá a mensagem a um fragmento diferente. Dessa forma, a operação de envio será bem-sucedida independentemente da indisponibilidade temporária de um repositório de mensagens. No entanto, você não obterá a ordenação garantida que uma chave de partição oferece.

Para obter uma discussão mais detalhada sobre a compensação entre disponibilidade (sem chave de partição) e consistência (usando uma chave de partição), consulte [este artigo](../event-hubs/event-hubs-availability-and-consistency.md). Essa informação aplica-se igualmente às entidades do Barramento de Serviço particionadas e partições de Hubs de Eventos.

Para dar ao Barramento de Serviço tempo suficiente para enfileirar a mensagem em um fragmento diferente, o valor de [MessagingFactorySettings.OperationTimeout][MessagingFactorySettings.OperationTimeout] especificado pelo cliente que envia a mensagem deve ser maior que 15 segundos. É recomendável definir a propriedade [OperationTimeout][OperationTimeout] com o valor padrão de 60 segundos.

Observe que uma chave de partição "fixa" uma mensagem a um fragmento específico. Se o repositório de mensagens que mantém o fragmento estiver indisponível, o Barramento de Serviço retornará um erro. Na ausência de uma chave de partição, o Barramento de Serviço pode escolher um fragmento diferente, e a operação será bem-sucedida. Portanto, é recomendável que você não forneça uma chave de partição, a menos que seja necessário.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Tópicos avançados: usar transações com entidades particionadas
As mensagens enviadas como parte de uma transação devem especificar uma chave de partição. Essa pode ser uma das seguintes propriedades: [BrokeredMessage.SessionId][BrokeredMessage.SessionId], [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey] ou [BrokeredMessage.MessageId][BrokeredMessage.MessageId]. Todas as mensagens que são enviadas como parte da mesma transação devem especificar a mesma chave de partição. Caso você tente enviar uma mensagem sem uma chave de partição em uma transação, o Barramento de Serviço retornará uma exceção de operação inválida. Caso você tente enviar várias mensagens que tenham chaves de partição diferentes na mesma transação, o Barramento de Serviço retornará uma exceção de operação inválida. Por exemplo:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

Se qualquer uma das propriedades que servem como uma chave de partição for definida, o Barramento de Serviço fixará a mensagem a um fragmento específico. Esse comportamento ocorre quer uma transação seja usada ou não. É recomendável que você não especifique uma chave de partição se isso não for necessário.

## <a name="using-sessions-with-partitioned-entities"></a>Usando sessões com entidades particionadas
Para enviar uma mensagem transacional a uma fila ou um tópico com reconhecimento de sessão, a mensagem deve ter a propriedade [BrokeredMessage.SessionId][BrokeredMessage.SessionId] definida. Se a propriedade [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey] também for especificada, ela deverá ser idêntica à propriedade [SessionId][SessionId]. Caso elas sejam diferentes, o Barramento de Serviço retornará uma exceção de operação inválida.

Diferentemente de filas ou tópicos regulares (não particionados), não é possível usar uma única transação para enviar várias mensagens a sessões diferentes. Se você tentar fazer isso, o Barramento de Serviço retornará uma exceção de operação inválida. Por exemplo:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.SessionId = "mySession";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Encaminhamento automático de mensagens com entidades particionadas
O Barramento de Serviço dá suporte ao encaminhamento automático de mensagens de, para ou entre entidades particionadas. Para habilitar o encaminhamento automático de mensagens, defina a propriedade [QueueDescription.ForwardTo][QueueDescription.ForwardTo] na assinatura ou fila de origem. Se a mensagem especificar uma chave de partição ([SessionId][SessionId], [PartitionKey][PartitionKey] ou [MessageId][MessageId]), ela será usada para a entidade de destino.

## <a name="considerations-and-guidelines"></a>Considerações e diretrizes
* **Recursos de alta consistência**: se uma entidade usa recursos como sessões, detecção de duplicidades ou controle explícito de chave de particionamento, as operações de mensagens serão sempre roteadas para fragmentos específicos. Se qualquer um dos fragmentos tiver alto tráfego ou se o repositório subjacente não estiver íntegro, essas operações falharão e a disponibilidade será reduzida. Em geral, a consistência ainda é muito maior do que em entidades não particionadas; somente um subconjunto de tráfego tem problemas, em vez de todo o tráfego. Para obter mais informações, consulte esta [discussão sobre disponibilidade e consistência](../event-hubs/event-hubs-availability-and-consistency.md).
* **Gerenciamento**: operações como Criar, Atualizar e Excluir devem ser executadas em todos os fragmentos da entidade. Se qualquer fragmento não estiver íntegro, isso poderá resultar em falhas para essas operações. Para a operação Get, informações como contagens de mensagens devem ser agregadas de todos os fragmentos. Se qualquer fragmento não estiver íntegro, o status de disponibilidade será relatado como limitado.
* **Cenários de mensagens de baixo volume**: para esses cenários, particularmente ao usar o protocolo HTTP, talvez você precise executar várias operações de recebimento para obter todas as mensagens. Para solicitações de recebimento, o front-end executa um recebimento em todos os fragmentos e armazena em cache todas as respostas recebidas. Uma solicitação de recebimento subsequente na mesma conexão se beneficiará desse armazenamento em cache, e as latências de recebimento serão menores. No entanto, se você tiver várias conexões ou se usar HTTP, isso estabelecerá uma nova conexão para cada solicitação. Assim, não há garantia de que ela chegará ao mesmo nó. Se todas as mensagens existentes forem bloqueadas e armazenadas em cache no outro front-end, a operação de recebimento resultará em **nulo**. As mensagens eventualmente expiram e você pode recebê-las novamente. O keep-alive de HTTP é recomendável.
* **Pesquisar/inspecionar mensagens**: o [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) nem sempre retorna o número de mensagens especificado na propriedade [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_MessageCount). Há duas razões comuns para isso. Uma razão é que o tamanho agregado da coleção de mensagens excede o tamanho máximo de 256 KB. Outra razão é que, se a fila ou o tópico tiver a [Propriedade EnablePartitioning](/dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_EnablePartitioning) definida como **verdadeiro**, uma partição poderá não ter mensagens suficientes para concluir o número solicitado de mensagens. Em geral, se um aplicativo quiser receber um número específico de mensagens, ele deverá solicitar [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) repetidamente até obter o número de mensagens ou até não existirem mais mensagens para inspecionar. Para obter mais informações, incluindo exemplos de código, confira [QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) ou [SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_PeekBatch_System_Int32_).

## <a name="latest-added-features"></a>Últimos recursos adicionados
* Agora há suporte para adicionar ou remover regras com entidades particionadas. Diferentes de entidades não particionadas, essas operações não têm suporte em transações. 
* Agora há suporte a AMQP para enviar e receber mensagens de e para uma entidade particionada.
* O AMQP agora tem suporte para as seguintes operações: [Enviar em Lote](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_SendBatch_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_BrokeredMessage__), [Receber em Lote](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_ReceiveBatch_System_Int32_), [Receber pelo Número de Sequência](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_Receive_System_Int64_), [Inspecionar](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_Peek), [Renovar Bloqueio](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_RenewMessageLock_System_Guid_), [Agendar Mensagem](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_ScheduleMessageAsync_Microsoft_ServiceBus_Messaging_BrokeredMessage_System_DateTimeOffset_), [Cancelar Mensagem Agendada](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_CancelScheduledMessageAsync_System_Int64_), [Adicionar Regra](/dotnet/api/microsoft.servicebus.messaging.ruledescription), [Remover Regra](/dotnet/api/microsoft.servicebus.messaging.ruledescription), [Renovar Bloqueio de Sessão](/dotnet/api/microsoft.servicebus.messaging.messagesession#Microsoft_ServiceBus_Messaging_MessageSession_RenewLock), [Definir Estado de Sessão](/dotnet/api/microsoft.servicebus.messaging.messagesession#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_), [Obter Estado de Sessão](/dotnet/api/microsoft.servicebus.messaging.messagesession#Microsoft_ServiceBus_Messaging_MessageSession_GetState) e [Enumerar Sessões](/dotnet/api/microsoft.servicebus.messaging.queueclient#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessionsAsync).

## <a name="partitioned-entities-limitations"></a>Limitações das entidades particionadas
Atualmente, o Barramento de Serviço impõe as seguintes limitações a filas e tópicos particionados:

* Filas e tópicos particionados não dão suporte ao envio de mensagens que pertencem a sessões diferentes em uma única transação.
* O Barramento de Serviço atualmente permite até 100 filas ou tópicos particionados por namespace. Cada fila ou tópico particionado conta para a cota de 10.000 entidades por namespace (não se aplica à camada Premium).

## <a name="next-steps"></a>Próximas etapas
Veja a discussão sobre o [Suporte a AMQP 1.0 para filas e tópicos particionados do Barramento de Serviço][AMQP 1.0 support for Service Bus partitioned queues and topics] para saber mais sobre como particionar entidades de mensagens. 

[Service Bus architecture]: service-bus-architecture.md
[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_EnablePartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription#Microsoft_ServiceBus_Messaging_TopicDescription_EnablePartitioning
[BrokeredMessage.SessionId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId
[BrokeredMessage.PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_PartitionKey
[SessionId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_PartitionKey
[QueueDescription.RequiresDuplicateDetection]: /dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId
[MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId
[MessagingFactorySettings.OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md
