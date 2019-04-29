---
title: Criar tópicos e filas particionados do Barramento de Serviço | Microsoft Docs
description: Descreve como particionar filas e tópicos do Barramento de Serviço usando vários agentes de mensagem.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 02/06/2019
ms.author: aschhab
ms.openlocfilehash: 699581c7ccd3f36da0cd0c1def623607b7c0a13b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589670"
---
# <a name="partitioned-queues-and-topics"></a>Filas e tópicos particionados

O Barramento de Serviço do Azure emprega vários agentes de mensagens para processar mensagens e vários repositórios de mensagens para armazenar mensagens. Uma fila ou um tópico convencional é manipulado por um único agente de mensagem e armazenado em um repositório de mensagens. As *partições* do Barramento de Serviço permitem que filas ou tópicos, ou *entidades de mensagens*, sejam particionados entre diversos agentes e repositórios de mensagens. Particionar significa que a taxa de transferência geral de uma entidade particionada não é mais limitada pelo desempenho de um único agente ou repositório de mensagens. Além disso, uma interrupção temporária de um repositório de mensagens não torna uma fila ou um tópico particionado indisponível. Filas e tópicos particionados podem conter todos os recursos avançados do Barramento de Serviço, como o suporte a transações e sessões.

> [!NOTE]
> Particionar está disponível na criação da identidade para todas as filas e tópicos em SKUs Básico ou Standard. Não está disponível para o SKU de Sistema de Mensagens Premium, mas qualquer entidade particionada existente anteriormente nos namespaces Premium continuará a funcionar como esperado.
 
Não é possível alterar a opção de particionamento em uma fila ou um tópico existente; você pode definir a opção apenas quando cria a entidade.

## <a name="how-it-works"></a>Como ele funciona

Cada fila ou tópico particionado consiste em várias partições. Cada partição é armazenada em um repositório de mensagens diferente e manipulada por um agente de mensagem diferente. Quando uma mensagem é enviada para uma fila ou tópico particionado, o barramento de serviço atribui a mensagem a uma das partições. A seleção é feita aleatoriamente pelo Barramento de Serviço ou por uma chave de partição que pode ser especificada pelo remetente.

Quando um cliente deseja receber uma mensagem de uma fila particionada ou de uma assinatura para um tópico particionado, o barramento de serviço consulta todas as partições para mensagens e, em seguida, retorna a primeira mensagem obtida de qualquer um dos repositórios de mensagens para o receptor. O Barramento de Serviço armazena em cache as outras mensagens e as retorna ao receber solicitações de recebimento adicionais. Um cliente receptor não está ciente do particionamento; o comportamento voltado para o cliente de uma fila ou um tópico particionado (por exemplo, ler, concluir, adiar, colocar mensagem na fila de mensagens mortas, executar pré-busca) é idêntico ao comportamento de uma entidade regular.

Não há custo adicional ao enviar ou receber uma mensagem de uma fila ou um tópico particionado.

## <a name="enable-partitioning"></a>Habilitar particionamento

Para usar filas e tópicos particionados com o Barramento de Serviço do Azure, use o SDK do Azure versão 2.2 ou posterior, ou especifique `api-version=2013-10` em suas solicitações HTTP.

### <a name="standard"></a>Standard

Na camada de mensagens Standard, é possível criar filas e tópicos do Barramento de Serviço em tamanhos de 1, 2, 3, 4 ou 5 GB (o padrão é 1 GB). Com o particionamento habilitado, o barramento de serviço cria 16 cópias (16 partições) da entidade, cada um do mesmo tamanho especificado. Assim, se você criar uma fila que tenha 5 GB, com 16 partições, o tamanho máximo da fila será (5 \* 16) = 80 GB. É possível ver o tamanho máximo da fila ou do tópico particionado observando sua entrada no [Portal do Azure][Azure portal], na folha **Visão Geral** da entidade em questão.

### <a name="premium"></a>Premium

Em um namespace de camada Premium, não há suporte para particionamento de entidades. Entretanto, é possível criar filas e tópicos do Barramento de Serviço em tamanhos de 1, 2, 3, 4, 5, 10, 20, 40 ou 80 GB (o padrão é 1 GB). É possível ver o tamanho da fila ou do tópico observando sua entrada no [Portal do Azure][Azure portal], na folha **Visão Geral** da entidade em questão.

### <a name="create-a-partitioned-entity"></a>Criar uma entidade particionada

Há várias maneiras de criar uma fila ou um tópico particionado. Ao criar a fila ou o tópico de seu aplicativo, você pode habilitar o particionamento de fila ou tópico definindo respectivamente a propriedade [QueueDescription.EnablePartitioning][QueueDescription.EnablePartitioning] ou [TopicDescription.EnablePartitioning][TopicDescription.EnablePartitioning] como **true**. Essas propriedades devem ser definidas no momento em que a fila ou tópico é criado e estão disponíveis somente na antiga biblioteca [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/). Conforme declarado anteriormente, não é possível alterar essas propriedades em uma fila ou um tópico existente. Por exemplo: 

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Como alternativa, você pode criar uma fila ou um tópico particionado no [Portal do Azure][Azure portal]. Ao criar uma fila ou um tópico no portal, a opção **Habilitar particionamento** na caixa de diálogo **Criar** da fila ou do tópico é marcada por padrão. Você só pode desabilitar essa opção em uma entidade da camada Standard; na camada Premium, o particionamento não tem suporte, e a caixa de seleção não tem efeito. 

## <a name="use-of-partition-keys"></a>Uso de chaves de partição

Quando uma mensagem é enfileirada em uma fila ou um tópico particionado, o Barramento de Serviço verifica a presença de uma chave de partição. Se ele encontrar um, ele seleciona a partição com base nessa chave. Se não encontrar uma chave de partição, ele seleciona a partição com base em um algoritmo interno.

### <a name="using-a-partition-key"></a>Usando uma chave de partição

Alguns cenários, como sessões ou transações, exigem que as mensagens sejam armazenadas em uma partição específica. Todos esses cenários exigem o uso de uma chave de partição. Todas as mensagens que usam a mesma chave de partição são atribuídas à mesma partição. Se a partição está temporariamente indisponível, o barramento de serviço retornará um erro.

Dependendo do cenário, propriedades de mensagem diferentes são usadas como uma chave de partição:

**SessionId**: caso uma mensagem tenha a propriedade [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) definida, o Barramento de Serviço a usará **SessionID** como a chave de partição. Dessa forma, todas as mensagens que pertencem à mesma sessão são manipuladas pelo mesmo agente de mensagens. Sessões habilitam o Barramento de Serviço a garantir a ordenação das mensagens, bem como a consistência dos estados de sessão.

**PartitionKey**: caso uma mensagem tenha a propriedade [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) definida, mas não a propriedade [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), o Barramento de Serviço usará o valor da propriedade [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) como a chave de partição. Caso a mensagem tenha as propriedades [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) e [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) definidas, as propriedades deverão ser idênticas. Se a propriedade [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) for definida com um valor diferente da propriedade [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), o Barramento de Serviço retornará uma exceção de operação inválida. A propriedade [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) deverá ser usada caso um remetente enviar mensagens transacionais sem reconhecimento de sessão. A chave de partição garante que todas as mensagens enviadas em uma transação sejam manipuladas pelo mesmo agente de mensagens.

**MessageId**: caso a fila ou o tópico tenha a propriedade [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) definida como **true** e as propriedades [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) ou [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) não forem definidas, o valor da propriedade [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) servirá como a chave de partição. (As bibliotecas Microsoft .NET e AMQP atribuirão automaticamente uma ID de mensagem se o aplicativo de envio não o fizer.) Nesse caso, todas as cópias da mesma mensagem são manipuladas pelo mesmo agente de mensagens. Este ID permite que o Barramento de Serviço detecte e elimine mensagens duplicadas. Se a propriedade [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) não for definida como **true**, o Barramento de Serviço não considerará a propriedade [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) como uma chave de partição.

### <a name="not-using-a-partition-key"></a>Não usando uma chave de partição

Na ausência de uma chave de partição, o barramento de serviço distribui mensagens em um estilo round-robin para todas as partições da fila ou tópico particionado. Se a partição escolhida não estiver disponível, o barramento de serviço atribui a mensagem para uma partição diferente. Dessa forma, a operação de envio será bem-sucedida independentemente da indisponibilidade temporária de um repositório de mensagens. No entanto, você não obterá a ordenação garantida que uma chave de partição oferece.

Para obter uma discussão mais detalhada sobre a compensação entre disponibilidade (sem chave de partição) e consistência (usando uma chave de partição), consulte [este artigo](../event-hubs/event-hubs-availability-and-consistency.md). Essa informação aplica-se igualmente às entidades do Barramento de Serviço particionadas.

Para dar ao barramento de serviço tempo suficiente para enfileirar a mensagem em uma partição diferente, o [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) valor especificado pelo cliente que envia a mensagem deve ser maior que 15 segundos. É recomendável que você defina a propriedade [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) com o valor padrão de 60 segundos.

Uma chave de partição "fixa" uma mensagem para uma partição específica. Se o repositório de mensagens que contém essa partição não estiver disponível, o barramento de serviço retornará um erro. Na ausência de uma chave de partição, o barramento de serviço pode escolher uma partição diferente e a operação for bem-sucedida. Portanto, é recomendável que você não forneça uma chave de partição, a menos que seja necessário.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Tópicos avançados: usar transações com entidades particionadas

As mensagens enviadas como parte de uma transação devem especificar uma chave de partição. A chave pode ser uma das seguintes propriedades: [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) ou [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid). Todas as mensagens que são enviadas como parte da mesma transação devem especificar a mesma chave de partição. Caso você tente enviar uma mensagem sem uma chave de partição em uma transação, o Barramento de Serviço retornará uma exceção de operação inválida. Caso você tente enviar várias mensagens que tenham chaves de partição diferentes na mesma transação, o Barramento de Serviço retornará uma exceção de operação inválida. Por exemplo: 

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.SendAsync(msg); 
    ts.CompleteAsync();
}
committableTransaction.Commit();
```

Se qualquer uma das propriedades que servem como uma chave de partição for definida, o barramento de serviço fixará a mensagem a uma partição específica. Esse comportamento ocorre quer uma transação seja usada ou não. É recomendável que você não especifique uma chave de partição se isso não for necessário.

## <a name="using-sessions-with-partitioned-entities"></a>Usando sessões com entidades particionadas

Para enviar uma mensagem transacional a uma fila ou um tópico com reconhecimento de sessão, a mensagem deve ter a propriedade [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) definida. Se a propriedade [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) também for especificada, ela deverá ser idêntica à propriedade [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid). Caso elas sejam diferentes, o Barramento de Serviço retornará uma exceção de operação inválida.

Diferentemente de filas ou tópicos regulares (não particionados), não é possível usar uma única transação para enviar várias mensagens a sessões diferentes. Se você tentar fazer isso, o Barramento de Serviço retornará uma exceção de operação inválida. Por exemplo: 

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.SessionId = "mySession";
    messageSender.SendAsync(msg); 
    ts.CompleteAsync();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Encaminhamento automático de mensagens com entidades particionadas

O Barramento de Serviço dá suporte ao encaminhamento automático de mensagens de, para ou entre entidades particionadas. Para habilitar o encaminhamento automático de mensagens, defina a propriedade [QueueDescription.ForwardTo][QueueDescription.ForwardTo] na assinatura ou fila de origem. Se a mensagem especificar uma chave de partição ([SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) ou [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)), essa chave será usada para a entidade de destino.

## <a name="considerations-and-guidelines"></a>Considerações e diretrizes
* **Recursos de alta consistência**: Se uma entidade usa recursos como sessões, detecção de duplicidades ou controle explícito de chave de particionamento, em seguida, as operações de mensagens sejam sempre roteadas para a partição específica. Se nenhuma partição tiver alto tráfego ou o repositório subjacente não está íntegro, essas operações falharão e disponibilidade será reduzida. Em geral, a consistência ainda é muito maior do que em entidades não particionadas; somente um subconjunto de tráfego tem problemas, em vez de todo o tráfego. Para obter mais informações, consulte esta [discussão sobre disponibilidade e consistência](../event-hubs/event-hubs-availability-and-consistency.md).
* **Gerenciamento**: Operações como criar, atualizar e excluir devem ser executadas em todas as partições da entidade. Se qualquer partição não está íntegra, isso poderia resultar em falhas para essas operações. Para a operação Get, informações como contagens de mensagem devem ser agregadas de todas as partições. Se qualquer partição não está íntegra, o status de disponibilidade será relatado como limitado.
* **Cenários de mensagens de baixo volume**: para esses cenários, particularmente ao usar o protocolo HTTP, talvez você precise executar várias operações de recebimento para obter todas as mensagens. Para solicitações de recebimento, o front-end executa um recebimento em todas as partições e os armazena em cache todas as respostas recebidas. Uma solicitação de recebimento subsequente na mesma conexão se beneficiará desse armazenamento em cache, e as latências de recebimento serão menores. No entanto, se você tiver várias conexões ou se usar HTTP, isso estabelecerá uma nova conexão para cada solicitação. Assim, não há garantia de que ela chegará ao mesmo nó. Se todas as mensagens existentes forem bloqueadas e armazenadas em cache no outro front-end, a operação de recebimento resultará em **nulo**. As mensagens eventualmente expiram e você pode recebê-las novamente. O keep-alive de HTTP é recomendável.
* **Pesquisar/inspecionar mensagens**: disponível apenas na antiga biblioteca [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/). O [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) nem sempre retorna o número de mensagens especificado na propriedade [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount). Há duas razões comuns para este comportamento. Uma razão é que o tamanho agregado da coleção de mensagens excede o tamanho máximo de 256 KB. Outra razão é que, se a fila ou o tópico tiver a [Propriedade EnablePartitioning](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) definida como **verdadeiro**, uma partição poderá não ter mensagens suficientes para concluir o número solicitado de mensagens. Em geral, se um aplicativo quiser receber um número específico de mensagens, ele deverá solicitar [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) repetidamente até obter o número de mensagens ou até não existirem mais mensagens para inspecionar. Para obter mais informações, incluindo exemplos de código, confira a documentação das API [QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) ou [SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch).

## <a name="latest-added-features"></a>Últimos recursos adicionados

* Agora há suporte para adicionar ou remover regras com entidades particionadas. Diferentes de entidades não particionadas, essas operações não têm suporte em transações. 
* Agora há suporte a AMQP para enviar e receber mensagens de e para uma entidade particionada.
* O AMQP agora tem suporte para as seguintes operações: [Enviar em Lote](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch), [Receber em Lote](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch), [Receber pelo Número de Sequência](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [Inspecionar](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [Renovar Bloqueio](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [Agendar Mensagem](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync), [Cancelar Mensagem Agendada](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync), [Adicionar Regra](/dotnet/api/microsoft.azure.servicebus.ruledescription), [Remover Regra](/dotnet/api/microsoft.azure.servicebus.ruledescription), [Renovar Bloqueio de Sessão](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock), [Definir Estado de Sessão](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), [Obter Estado de Sessão](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate) e [Enumerar Sessões](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Limitações das entidades particionadas

Atualmente, o Barramento de Serviço impõe as seguintes limitações a filas e tópicos particionados:

* As filas e tópicos particionados não são compatíveis com a camada do Sistema de Mensagens Premium. As sessões são compatíveis com a camada Premier com o uso de SessionId. 
* Filas e tópicos particionados não dão suporte ao envio de mensagens que pertencem a sessões diferentes em uma única transação.
* O Barramento de Serviço atualmente permite até 100 filas ou tópicos particionados por namespace. Cada fila ou tópico particionado conta para a cota de 10.000 entidades por namespace (não se aplica à camada Premium).

## <a name="next-steps"></a>Próximas etapas

Leia sobre os conceitos básicos da especificação de mensagens AMQP 1.0 no [guia do protocolo AMQP 1.0](service-bus-amqp-protocol-guide.md).

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md
