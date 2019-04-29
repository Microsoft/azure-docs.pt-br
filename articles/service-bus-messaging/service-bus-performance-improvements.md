---
title: Práticas recomendadas para melhorar o desempenho usando o Barramento de Serviço do Azure| Microsoft Docs
description: Descreve como usar o Barramento de Serviço para otimizar o desempenho na troca de mensagens agenciadas.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: f5ce8a237bc2ba7fe15acfcd6afa0edcda7ef713
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589655"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Práticas recomendadas para melhorias de desempenho usando o Sistema de Mensagens do Barramento de Serviço

Este artigo descreve como usar o Barramento de Serviço do Azure para otimizar o desempenho na troca de mensagens agenciadas. A primeira parte deste artigo descreve os diferentes mecanismos oferecidos para ajudar a melhorar o desempenho. A segunda parte oferece orientação sobre como usar o Barramento de Serviço de uma maneira que possa oferecer o melhor desempenho em um determinado cenário.

Ao longo deste artigo, o termo "cliente" refere-se a qualquer entidade que acesse o Barramento de Serviço. Um cliente pode assumir a função de um remetente ou de um receptor. O termo "remetente" é usado para um cliente de tópico ou fila de Barramento de Serviço que envia mensagens para uma assinatura de tópico ou fila de Barramento de Serviço. O termo "receptor" refere-se a um cliente de fila ou de assinatura do Barramento de Serviço que recebe mensagens de uma fila ou uma assinatura do Barramento de Serviço.

Estas seções apresentam vários conceitos usados pelo Barramento de Serviço para melhorar o desempenho.

## <a name="protocols"></a>Protocolos

O Barramento de Serviço permite que os clientes enviem e recebam mensagens por meio de um destes três protocolos:

1. Advanced Message Queuing Protocol (AMQP)
2. Protocolo do sistema de mensagens do Barramento de Serviço (SBMP)
3. HTTP

AMQP e SBMP são mais eficientes, pois mantêm a conexão com o Barramento de Serviço enquanto a fábrica do sistema de mensagens existir. Ele também implementa o envio em lote e a pré-busca. A menos que mencionado explicitamente, todo o conteúdo deste artigo supõe o uso do AMQP ou do SBMP.

## <a name="reusing-factories-and-clients"></a>Reutilizando fábricas e clientes

Os objetos de cliente do Barramento de Serviço, como [QueueClient][QueueClient] ou [MessageSender][MessageSender], são criados por meio de um objeto [MessagingFactory][MessagingFactory], que também oferece gerenciamento interno de conexões. É recomendável que você não feche fábricas de mensagens ou os clientes de fila, de tópico e de assinatura depois de enviar uma mensagem e então recriá-los ao enviar a próxima mensagem. Fechar uma fábrica do sistema de mensagens exclui a conexão com o serviço Barramento de Serviço e uma nova conexão é estabelecida na recriação da fábrica. O estabelecimento de uma conexão é uma operação cara que você pode evitar usando o mesmo alocador e objetos de cliente para diversas operações. Você pode usar esses objetos de cliente com segurança em operações assíncronas simultâneas e de vários threads. 

## <a name="concurrent-operations"></a>Operações simultâneas

A execução de uma operação (enviar, receber, excluir etc.) leva algum tempo. Esse tempo inclui o processamento da operação pelo serviço do barramento de serviço, além da latência da solicitação e resposta. Para aumentar o número de operações por hora, elas devem ser executadas simultaneamente. 

O cliente agenda operações simultâneas realizando operações assíncronas. A próxima solicitação é iniciada antes que a solicitação anterior seja concluída. O snippet de código a seguir é um exemplo de uma operação de envio assíncrono:
  
 ```csharp
  Message m1 = new BrokeredMessage(body);
  Message m2 = new BrokeredMessage(body);
  
  Task send1 = queueClient.SendAsync(m1).ContinueWith((t) => 
    {
      Console.WriteLine("Sent message #1");
    });
  Task send2 = queueClient.SendAsync(m2).ContinueWith((t) => 
    {
      Console.WriteLine("Sent message #2");
    });
  Task.WaitAll(send1, send2);
  Console.WriteLine("All messages sent");
  ```
  
  O código a seguir é um exemplo de uma operação de recebimento assíncrono. Consulte o programa completo [aqui](https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues):
  
  ```csharp
  var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);
  var doneReceiving = new TaskCompletionSource<bool>();

  receiver.RegisterMessageHandler(...);
  ```

## <a name="receive-mode"></a>Modo de recebimento

Ao criar um cliente de fila ou assinatura, você pode especificar um modo de recebimento: *PeekLock* ou *Receber e Excluir*. O modo de recebimento padrão é [PeekLock][PeekLock]. Ao operar nesse modo, o cliente envia uma solicitação para receber uma mensagem do Barramento de Serviço. Depois que o cliente tiver recebido a mensagem, ele enviará uma solicitação para concluir a mensagem.

Ao definir o modo de recebimento como [ReceiveAndDelete][ReceiveAndDelete], as duas etapas serão combinadas em uma única solicitação. Essas etapas reduzem o número total de operações e podem melhorar a taxa de transferência geral da mensagem. Este ganho de desempenho vem com o risco de perda de mensagens.

O Barramento de Serviço não dá suporte a transações para operações de receber e excluir. Além disso, a semântica do bloqueio de pico é necessária para todos os cenários em que o cliente deseje adiar uma mensagem ou enviá-la para as [mensagens mortas](service-bus-dead-letter-queues.md).

## <a name="client-side-batching"></a>Envio em lote no lado do cliente

O envio em lote no lado do cliente permite que um cliente de fila ou de tópico atrase o envio de uma mensagem por um determinado período. Se o cliente enviar mensagens adicionais durante esse período, ele transmitirá as mensagens em um único lote. O envio em lote do lado do cliente também faz com que um cliente de fila ou assinatura agrupe em lote diversas solicitações **Concluir** em uma única solicitação. O envio em lote está disponível apenas para operações **Enviar** e **Concluir** assíncronas. As operações síncronas são imediatamente enviadas para o serviço Barramento de Serviço. O envio em lote não ocorre para as operações de pico ou de recebimento, e também não ocorre entre clientes.

Por padrão, um cliente usa um intervalo de lote de 20 ms. Você pode alterar o intervalo de lote definindo a propriedade [BatchFlushInterval][BatchFlushInterval] antes de criar a fábrica de mensagens. Essa configuração afeta todos os clientes criados por essa fábrica.

Para desabilitar o envio em lote, defina a propriedade [BatchFlushInterval][BatchFlushInterval] como **TimeSpan.Zero**. Por exemplo: 

```csharp
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

O envio em lote não afeta o número de operações faturáveis do sistema de mensagens e está disponível somente para o protocolo de cliente do Barramento de Serviço usando a biblioteca [Microsoft.ServiceBus.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/). O protocolo HTTP não dá suporte ao envio em lote.

> [!NOTE]
> Definir BatchFlushInterval garante que o envio em lote é implícito da perspectiva do aplicativo. ou seja, o aplicativo faz SendAsync () e CompleteAsync() chama e não faz chamadas específicas do lote.
>
> Envio em lote de lado do cliente explícita pode ser implementado utilizando o abaixo da chamada de método - 
> ```csharp
> Task SendBatchAsync (IEnumerable<BrokeredMessage> messages);
> ```
> Aqui o tamanho combinado das mensagens deve ser menor que o tamanho máximo suportado pelo tipo de preço.

## <a name="batching-store-access"></a>Acesso ao repositório do envio em lote

Para aumentar a taxa de transferência de uma fila, tópico ou assinatura, o Barramento de Serviço agrupa várias mensagens ao gravar em seu repositório interno. Se habilitada em uma fila ou tópico, a gravação de mensagens no repositório ocorrerá em lote. Se habilitada em uma fila ou assinatura, a exclusão de mensagens no repositório ocorrerá em lote. Se o acesso ao repositório em lote estiver habilitado para uma entidade, o Barramento de Serviço atrasará uma operação de gravação do repositório em relação àquela entidade por até 20 ms. 

> [!NOTE]
> Não há nenhum risco de perda de mensagens com o envio em lote, mesmo se houver uma falha de barramento de serviço ao final de um intervalo de envio em lote de 20 ms. 

As operações de armazenamento adicionais que ocorrerem durante esse intervalo serão adicionadas ao lote. O acesso ao repositório em lote só afetará as operações **Enviar** e **Concluir**; as operações de receber não são afetadas. O acesso ao repositório em lote é uma propriedade em uma entidade. O envio em lote ocorrerá em todas as entidades que permitirem o acesso ao repositório em lote.

Quando uma nova fila, um novo tópico ou uma nova assinatura for criada, o acesso ao repositório em lote será habilitado por padrão. Para desabilitar o acesso ao repositório em lote, defina a propriedade [EnableBatchedOperations][EnableBatchedOperations] como **false** antes de criar a entidade. Por exemplo: 

```csharp
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

O acesso ao repositório em lote não afeta o número de operações faturáveis do sistema de mensagens faturáveis e é uma propriedade de uma fila, um tópico ou uma assinatura. É independente do modo de recebimento e do protocolo usado entre um cliente e o serviço Barramento de Serviço.

## <a name="prefetching"></a>Pré-busca

A [pré-busca](service-bus-prefetch.md) permite que o cliente de fila ou de assinatura carregue mensagens adicionais do serviço ao executar uma operação de recebimento. O cliente armazena essas mensagens em um cache local. O tamanho do cache é determinado pelas propriedades [QueueClient.PrefetchCount][QueueClient.PrefetchCount] ou [SubscriptionClient.PrefetchCount][SubscriptionClient.PrefetchCount]. Cada cliente que permite a pré-busca mantém seu próprio cache. Um cache não é compartilhado entre os clientes. Se o cliente iniciar uma operação de recebimento e se o cache estiver vazio, o serviço transmitirá um lote de mensagens. O tamanho do lote é igual ao tamanho do cache ou a 256 KB, o que for menor. Se o cliente iniciar uma operação de recebimento e se o cache contiver uma mensagem, a mensagem será retirada do cache.

Quando uma mensagem for pré-buscada, ela será bloqueada pelo serviço. Com o bloqueio, a mensagem pré-buscada não pode ser recebida por um receptor diferente. Se o receptor não puder concluir a mensagem antes da expiração do bloqueio, a mensagem ficará disponível para outros destinatários. A cópia pré-buscada da mensagem permanecerá no cache. O receptor que consumir a cópia armazenada em cache expirada receberá uma exceção ao tentar concluir essa mensagem. Por padrão, o bloqueio da mensagem expira após 60 segundos. Esse valor pode ser estendido para 5 minutos. Para impedir o consumo de mensagens expiradas, o tamanho do cache sempre deverá ser menor do que o número de mensagens que podem ser consumidas por um cliente no intervalo de tempo limite de bloqueio.

Ao usar a expiração de bloqueio padrão de 60 segundos, um bom valor para [PrefetchCount][SubscriptionClient.PrefetchCount] será de 20 vezes as taxas máximas de processamento de todos os receptores da fábrica. Por exemplo, um alocador cria três receptores e cada receptor pode processar até dez mensagens por segundo. A contagem de pré-busca não deve exceder 20 X 3 X 10 = 600. Por padrão, [PrefetchCount][QueueClient.PrefetchCount] é definido como 0, o que significa que nenhuma mensagem adicional será buscada do serviço.

A pré-busca de mensagens aumenta a taxa de transferência geral de uma fila ou uma assinatura porque reduz o número geral de operações de mensagem, ou as viagens de ida e volta. A busca da primeira mensagem, no entanto, demorará mais (devido ao tamanho maior da mensagem). O recebimento de mensagens pré-buscadas será mais rápido porque essas mensagens já foram baixadas pelo cliente.

A propriedade de vida útil (TTL) de uma mensagem é verificada pelo servidor no momento em que o servidor envia a mensagem para o cliente. O cliente não verifica a propriedade TTL da mensagem quando a mensagem é recebida. Em vez disso, a mensagem poderá ser recebida mesmo se a TTL da mensagem tiver passado enquanto a mensagem estava armazenada em cache pelo cliente.

A pré-busca não afeta o número de operações faturáveis do sistema de mensagens e está disponível somente para o protocolo de cliente do Barramento de Serviço. O protocolo HTTP não dá suporte à pré-busca. A pré-busca está disponível para as operações de recebimento síncrono e assíncrono.

## <a name="prefetching-and-receivebatch"></a>A pré-busca e ReceiveBatch

Embora os conceitos de pré-busca juntos de várias mensagens possuem semântica semelhante ao processamento de mensagens em um lote (ReceiveBatch), há algumas pequenas diferenças que devem ser mantidas em mente ao aproveitar esses juntos.

Pré-busca é uma configuração (ou modo) no cliente (QueueClient e SubscriptionClient) e ReceiveBatch é uma operação (que tem a semântica de solicitação-resposta).

Ao usar esses juntos, considere os seguintes casos:-

* Pré-busca deve ser maior que ou igual ao número de mensagens que estiver esperando receber da ReceiveBatch.
* Pré-busca pode ser até n/3 vezes o número de mensagens processadas por segundo, onde n é a duração de bloqueio padrão.

Há alguns desafios com tendo um greedy abordagem (ou seja, mantendo a contagem de pré-busca muito alta), porque ele implica que a mensagem ficará bloqueada para um destinatário específico. A recomendação é tentar out pré-busca valores entre os limites mencionados acima e Empiricamente, identifique o que se encaixa.

## <a name="multiple-queues"></a>Várias filas

Se a carga esperada não puder ser manipulada por uma única fila ou tópico particionado, você deverá usar várias entidades de mensagens. Ao usar várias entidades, crie um cliente dedicado para cada entidade em vez de usar o mesmo cliente para todas as entidades.

## <a name="development-and-testing-features"></a>Recursos de desenvolvimento e teste

O Barramento de Serviço tem um recurso usado especificamente para desenvolvimento que **nunca deve ser usado em configurações de produção**: [TopicDescription.EnableFilteringMessagesBeforePublishing][].

Quando novas regras ou filtros são adicionados ao tópico, é possível usar [TopicDescription.EnableFilteringMessagesBeforePublishing][] para verificar se a nova expressão de filtro está funcionando conforme o esperado.

## <a name="scenarios"></a>Cenários

As seções a seguir descrevem cenários típicos de mensagens e as configurações preferenciais do Barramento de Serviço. As taxas de transferência são classificadas como pequena (menos de 1 mensagem/segundo), moderada (1 mensagem/segundo ou mais, mas menos de 100 mensagens por segundo) e alta (100 mensagens/segundo ou mais). O número de clientes é classificado como pequeno (5 ou menos), moderado (de 5 a 20) e grande (mais de 20).

### <a name="high-throughput-queue"></a>Fila de alta taxa de transferência

Meta: maximizar a taxa de transferência de uma única fila. O número de remetentes e de receptores é pequeno.

* Para aumentar a taxa geral de envio para a fila, use diversas fábricas de mensagens para criar remetentes. Para cada remetente, use operações assíncronas ou vários threads.
* Para aumentar a taxa geral de recebimento da fila, use diversas fábricas de mensagens para criar receptores.
* Use operações assíncronas para tirar proveito do envio em lote no lado do cliente.
* Defina o intervalo de envio em lote como 50 ms para reduzir o número de transmissões de protocolo de cliente do Barramento de Serviço. Se forem usados vários remetentes, aumente o intervalo de envio em lote para 100 ms.
* Deixe o acesso ao repositório em lote habilitado. Esse acesso aumenta a taxa geral em que as mensagens podem ser gravadas na fila.
* Defina a contagem de pré-busca como 20 vezes as taxas máximas de processamento de todos os receptores de uma fábrica. Essa contagem reduz o número de transmissões de protocolo de cliente do Barramento de Serviço.
* Use uma fila particionada para obter desempenho e disponibilidade aprimorados.

### <a name="multiple-high-throughput-queues"></a>Várias filas de alta taxa de transferência

Meta: maximizar a taxa de transferência geral de diversas filas. A taxa de transferência de uma fila individual é moderada ou alta.

Para obter a taxa de transferência máxima em várias filas, use as configurações descritas para maximizar a taxa de transferência de uma única fila. Além disso, use fábricas diferentes para criar clientes que enviem ou recebam de filas diferentes.

### <a name="low-latency-queue"></a>Fila de baixa latência

Meta: minimizar a latência de ponta a ponta de uma fila ou um tópico. O número de remetentes e de receptores é pequeno. A taxa de transferência da fila é pequena ou moderada.

* Desabilite o envio em lote no lado do cliente. O cliente envia uma mensagem imediatamente.
* Desabilite o acesso ao repositório em lote. O serviço grava imediatamente a mensagem no repositório.
* Se estiver usando um único cliente, defina a contagem de pré-busca como 20 vezes a taxa de processamento do receptor. Se várias mensagens chegarem à fila ao mesmo tempo, o protocolo de cliente do Barramento de Serviço transmite todas ao mesmo tempo. Quando o cliente receber a próxima mensagem, essa mensagem já estará no cache local. O cache deve ser pequeno.
* Se você estiver usando vários clientes, defina a contagem de pré-busca como 0. Ao definir a contagem, o segundo cliente poderá receber a segunda mensagem enquanto o primeiro cliente ainda estiver processando a primeira mensagem.
* Use uma fila particionada para obter desempenho e disponibilidade aprimorados.

### <a name="queue-with-a-large-number-of-senders"></a>Fila com um grande número de remetentes

Meta: maximizar a taxa de transferência de uma fila ou um tópico com um grande número de remetentes. Cada remetente envia mensagens com uma taxa moderada. O número de receptores é pequeno.

O Barramento de Serviço permite até 1000 conexões simultâneas a uma entidade do sistema de mensagens (ou 5000 usando AMQP). Esse limite é imposto no nível de namespace e tópicos/filas/assinaturas são controlados pelo limite de conexões simultâneas por namespace. Para filas, esse número é compartilhado entre remetentes e receptores. Se todas as 1000 conexões forem obrigatórias para os remetentes, substitua a fila por um tópico e uma assinatura única. Um tópico aceita até 1000 conexões simultâneas dos remetentes, enquanto a assinatura aceita 1000 conexões simultâneas adicionais dos receptores. Se mais de 1000 remetentes simultâneos forem necessários, os remetentes deverão enviar mensagens para o protocolo do Barramento de Serviço via HTTP.

Para maximizar a taxa de transferência, execute as etapas a seguir:

* Se cada remetente residir em um processo diferente, use somente uma única fábrica por processo.
* Use operações assíncronas para tirar proveito do envio em lote no lado do cliente.
* Use o intervalo de lote padrão de 20 ms para reduzir o número de transmissões de protocolo de cliente do Barramento de Serviço.
* Deixe o acesso ao repositório em lote habilitado. Esse acesso aumenta a taxa geral em que as mensagens podem ser gravadas na fila ou no tópico.
* Defina a contagem de pré-busca como 20 vezes as taxas máximas de processamento de todos os receptores de uma fábrica. Essa contagem reduz o número de transmissões de protocolo de cliente do Barramento de Serviço.
* Use uma fila particionada para obter desempenho e disponibilidade aprimorados.

### <a name="queue-with-a-large-number-of-receivers"></a>Fila com um grande número de receptores

Meta: maximizar a taxa de recebimento de uma fila ou de uma assinatura com um grande número de receptores. Cada receptor recebe mensagens a uma taxa moderada. O número de remetentes é pequeno.

O Barramento de Serviço permite até 1000 conexões simultâneas a uma entidade. Se uma fila exigir mais de 1.000 receptores, substitua a fila por um tópico e várias assinaturas. Cada assinatura pode dar suporte a até 1000 conexões simultâneas. Como alternativa, os receptores poderão acessar a fila por meio do protocolo HTTP.

Para maximizar a taxa de transferência, faça o seguinte:

* Se cada receptor residir em um processo diferente, use somente uma única fábrica por processo.
* Os receptores poderão usar operações síncronas ou assíncronas. Dada a taxa de recebimento moderada de um receptor individual, o envio em lote no lado do cliente de uma solicitação Concluir não afetará a taxa de transferência do receptor.
* Deixe o acesso ao repositório em lote habilitado. Esse acesso reduz a carga geral da entidade. Isso também reduz a taxa geral em que as mensagens podem ser gravadas na fila ou no tópico.
* Defina a contagem de pré-busca como um valor pequeno (por exemplo, PrefetchCount = 10). Essa contagem impede que os receptores fiquem ociosos enquanto outros receptores tenham grandes quantidades de mensagens armazenadas em cache.
* Use uma fila particionada para obter desempenho e disponibilidade aprimorados.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Tópico com um pequeno número de assinaturas

Meta: maximizar a taxa de transferência de um tópico com um pequeno número de assinaturas. Uma mensagem é recebida por muitas assinaturas, o que significa que a taxa de recebimento combinada em todas as assinaturas é maior do que a taxa de envio. O número de remetentes é pequeno. O número de receptores por assinatura é pequeno.

Para maximizar a taxa de transferência, faça o seguinte:

* Para aumentar a taxa geral de envio para o tópico, use diversas fábricas de mensagens para criar remetentes. Para cada remetente, use operações assíncronas ou vários threads.
* Para aumentar a taxa geral de recebimento de uma assinatura, use diversas fábricas de mensagens para criar receptores. Para cada receptor, use operações assíncronas ou vários threads.
* Use operações assíncronas para tirar proveito do envio em lote no lado do cliente.
* Use o intervalo de lote padrão de 20 ms para reduzir o número de transmissões de protocolo de cliente do Barramento de Serviço.
* Deixe o acesso ao repositório em lote habilitado. Esse acesso aumenta a taxa geral em que as mensagens podem ser gravadas no tópico.
* Defina a contagem de pré-busca como 20 vezes as taxas máximas de processamento de todos os receptores de uma fábrica. Essa contagem reduz o número de transmissões de protocolo de cliente do Barramento de Serviço.
* Use um tópico particionado para obter desempenho e disponibilidade aprimorados.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Tópico com um grande número de assinaturas

Meta: maximizar a taxa de transferência de um tópico com um grande número de assinaturas. Uma mensagem é recebida por várias assinaturas, o que significa que a taxa de recebimento combinada em todas as assinaturas é muito maior do que a taxa de envio. O número de remetentes é pequeno. O número de receptores por assinatura é pequeno.

Os tópicos com um grande número de assinaturas normalmente terão uma taxa de transferência geral baixa se todas as mensagens forem roteadas a todas as assinaturas. Essa taxa de transferência baixa é causada pelo fato de que cada mensagem é recebida várias vezes e todas as mensagens contidas em um tópico e todas as suas assinaturas são armazenadas no mesmo repositório. Supõe-se que o número de remetentes e o número de receptores por assinatura é baixo. O Barramento de Serviço dá suporte a até 2.000 assinaturas por tópico.

Para maximizar a taxa de transferência, experimente as etapas a seguir:

* Use operações assíncronas para tirar proveito do envio em lote no lado do cliente.
* Use o intervalo de lote padrão de 20 ms para reduzir o número de transmissões de protocolo de cliente do Barramento de Serviço.
* Deixe o acesso ao repositório em lote habilitado. Esse acesso aumenta a taxa geral em que as mensagens podem ser gravadas no tópico.
* Defina a contagem de pré-busca como 20 vezes a taxa de recebimento esperada em segundos. Essa contagem reduz o número de transmissões de protocolo de cliente do Barramento de Serviço.
* Use um tópico particionado para obter desempenho e disponibilidade aprimorados.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como otimizar o desempenho do Barramento de Serviço, veja [Entidades de mensagens particionadas][Partitioned messaging entities].

[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[PeekLock]: /dotnet/api/microsoft.azure.servicebus.receivemode
[ReceiveAndDelete]: /dotnet/api/microsoft.azure.servicebus.receivemode
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[EnableBatchedOperations]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations
[QueueClient.PrefetchCount]: /dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount
[SubscriptionClient.PrefetchCount]: /dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[Partitioned messaging entities]: service-bus-partitioning.md
[TopicDescription.EnableFilteringMessagesBeforePublishing]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing
