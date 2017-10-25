---
title: "Visão geral das filas de mensagens, tópicos e assinaturas do Barramento de Serviço do Azure | Microsoft Docs"
description: "Visão geral de entidades do sistema de mensagens do Barramento de Serviço."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a306ced4-74e9-47c6-990a-d9c47efa31d5
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: sethm
ms.openlocfilehash: 00f9f38fbae028486270053dedb4df580a3f1a44
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Filas, tópicos e assinaturas do Barramento de Serviço

O Barramento de Serviço do Microsoft Azure oferece suporte a um conjunto de tecnologias middleware orientado a mensagens, baseado em nuvem, incluindo o serviço de enfileiramento de mensagens confiável e o sistema de mensagens de publicação/assinatura durável. Esses recursos do sistema de mensagens "agenciado" podem ser considerados como recursos do sistema de mensagens separados que dão suporte a cenários de assinatura de publicação, de desacoplamento temporal e de balanceamento de carga usando a malha do sistema de mensagens do Barramento de Serviço. A comunicação desacoplada tem diversas vantagens; por exemplo, clientes e servidores podem se conectar como necessário e executar as operações deles de forma assíncrona.

As entidades de mensagens que formam o núcleo dos recursos do sistema de mensagens agenciado no Barramento de Serviço são filas, tópicos e assinaturas, e regras/ações.

## <a name="queues"></a>Filas

As filas oferecem entrega de mensagem do tipo *FIFO (primeiro a entrar, primeiro a sair)* para um ou mais consumidores concorrentes. Ou seja, normalmente espera-se que as mensagens sejam recebidas e processadas pelos receptores na ordem em que foram adicionadas à fila, sendo que cada mensagem é recebida e processada por apenas um consumidor de mensagem. Um dos principais benefícios da utilização de filas é obter o “desacoplamento temporal” de componentes do aplicativo. Em outras palavras, os produtores (remetentes) e os consumidores (receptores) não precisam enviar e receber mensagens ao mesmo tempo porque as mensagens são armazenadas de forma duradoura na fila. Além disso, o produtor não precisa esperar por uma resposta do consumidor a fim de continuar a processar e enviar mensagens.

Um benefício relacionado é o “nivelamento de carga”, que permite que produtores e consumidores enviem e recebam mensagens em taxas diferentes. Em muitos aplicativos, a carga do sistema varia ao longo do tempo; no entanto, o tempo de processamento necessário para cada unidade de trabalho normalmente é constante. A intermediação de produtores e de consumidores de mensagem com uma fila significa que o aplicativo que está consumindo só precisa ser provisionado para poder lidar com a carga média em vez da carga de pico. A profundidade da fila aumentará e diminuirá conforme a carga de entrada variar. Isso economiza dinheiro diretamente em termos da quantidade de infraestrutura necessária para atender à carga do aplicativo. À medida que a carga aumenta, mais processos de trabalho poderão ser adicionados à leitura da fila. Cada mensagem é processada por apenas umdos processos de trabalho. Além disso, esse balanceamento de carga baseado em pull permite o uso ideal dos computadores de trabalho, mesmo se os computadores de trabalho forem diferentes em relação à capacidade de processamento, já que eles receberá as mensagens por pull em sua própria taxa máxima. Esse padrão geralmente é chamado de padrão de "consumidor concorrente".

A utilização de filas para intermediar entre produtores e consumidores oferece um acoplamento flexível inerente entre os componentes. Como produtores e consumidores não têm conhecimento uns dos outros, um consumidor poderá ser atualizado sem afetar o produtor.

A criação de uma fila é um processo de várias etapas. Você executa operações de gerenciamento para entidades de mensagens do Barramento de Serviço (filas e tópicos) por meio da classe [Microsoft.ServiceBus.NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager), que é construída por meio do fornecimento do endereço básico do namespace do Barramento de Serviço e das credenciais do usuário. O [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) oferece métodos para a criação, a enumeração e a exclusão de entidades do sistema de mensagens. Após a criação de um objeto [Microsoft.ServiceBus.TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#microsoft_servicebus_tokenprovider) desde o nome e a chave SAS, e de um objeto de gerenciamento de namespace de serviço, você poderá usar o método [Microsoft.ServiceBus.NamespaceManager.CreateQueue](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateQueue_System_String_) para criar a fila. Por exemplo:

```csharp
// Create management credentials
TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
// Create namespace client
NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
```

Você pode criar um objeto de fila e uma fábrica do sistema de mensagens com o URI do Barramento de Serviço como um argumento. Por exemplo:

```csharp
QueueDescription myQueue;
myQueue = namespaceClient.CreateQueue("TestQueue");
MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials); 
QueueClient myQueueClient = factory.CreateQueueClient("TestQueue");
```

Em seguida, você poderá enviar mensagens para a fila. Por exemplo, se você tiver uma lista de mensagens agenciadas chamada `MessageList`, o código será semelhante a este:

```csharp
for (int count = 0; count < 6; count++)
{
    var issue = MessageList[count];
    issue.Label = issue.Properties["IssueTitle"].ToString();
    myQueueClient.Send(issue);
}
```

Você recebe mensagens da fila como a seguir:

```csharp
while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 0, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

No modo [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode), a operação de recebimento é única; ou seja, quando o Barramento de Serviço receber a solicitação, marcará a mensagem como sendo consumida e a retornará ao aplicativo. O modo **ReceiveAndDelete** é o modelo mais simples e funciona melhor em cenários nos quais o aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Barramento de Serviço marca a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem consumida antes da falha.

No modo [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode), a operação de recebimento se torna uma operação de dois estágios, o que possibilita o suporte aos aplicativos que não podem tolerar mensagens ausentes. Quando o Barramento de Serviço recebe a solicitação, ele localiza a próxima mensagem a ser consumida, bloqueia-a para evitar que outros consumidores a recebam e, em seguida, retorna-a para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento chamando [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) na mensagem recebida. Quando o Barramento de Serviço vê a chamada a **Complete**, marca a mensagem como sendo consumida.

Se o aplicativo receptor não for capaz de processar a mensagem por algum motivo, ele chamará o método [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) na mensagem recebida (em vez do método [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete)). Isso permitirá ,que o Barramento de Serviço desbloqueie a mensagem na fila e disponibilize-a para que ela possa ser recebida novamente pelo mesmo consumidor ou por outro consumidor concorrente. Em segundo lugar, há um tempo limite associado a um bloqueio e, se houver falha no processamento da mensagem pelo aplicativo da expiração do tempo limite de bloqueio (por exemplo, se o aplicativo travar), o Barramento de Serviço desbloqueará a mensagem e a disponibilizará para ser recebida novamente (basicamente executando uma operação [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) por padrão).

Observe que, se houver falha do aplicativo após o processamento da mensagem, mas antes de a solicitação **Complete** ser emitida, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Com frequência, isso é chamado de processamento *Pelo menos uma vez*; ou seja, cada mensagem é processada pelo menos uma vez. No entanto, em determinadas situações a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar processamento duplicado, será necessária uma lógica adicional no aplicativo para detectar duplicadas que poderão ser obtidas com base na propriedade **MessageId** da mensagem, que permanece constante nas tentativas da entrega. Isso é conhecido como processamento *Exatamente Uma Vez*.

## <a name="topics-and-subscriptions"></a>Tópicos e assinaturas
Em contraste com as filas, em que cada mensagem é processada por um único consumidor, *tópicos* e *assinaturas* fornecem uma forma de comunicação de um para muitos em um padrão de *publicação/assinatura*. Útil para o dimensionamento para grandes números de destinatários, cada mensagem publicada é disponibilizada para cada assinatura registrada com o tópico. As mensagens são enviadas a um tópico e entregues a uma ou mais assinaturas associadas, dependendo das regras de filtro que puderem ser definidas por assinatura. As assinaturas podem usar filtros adicionais para restringir as mensagens que desejam receber. As mensagens são enviadas a um tópico da mesma forma como são enviadas para uma fila, mas as mensagens não são recebidas diretamente do tópico. Em vez disso, elas são recebidas de assinaturas. Uma assinatura de tópico é semelhante a uma fila virtual que recebe cópias das mensagens enviadas para o tópico. As mensagens são recebidas de uma assinatura de forma idêntica à maneira como são recebidas de uma fila.

Por comparação, a funcionalidade de envio de mensagens de uma fila é diretamente mapeada para um tópico e sua funcionalidade de recebimento de mensagens é mapeada para uma assinatura. Entre outras coisas, isso significa que as assinaturas dão suporte aos mesmos padrões descritos anteriormente nesta seção em relação a filas: consumidor concorrente, desacoplamento temporal, nivelamento de carga e balanceamento de carga.

A criação de um tópico é semelhante à criação de uma fila, como mostrado no exemplo da seção anterior. Crie o URI do serviço e, em seguida, use a classe [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) para criar o cliente de namespace. Você pode criar um tópico usando o método [CreateTopic](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateTopic_System_String_). Por exemplo:

```csharp
TopicDescription dataCollectionTopic = namespaceClient.CreateTopic("DataCollectionTopic");
```

Em seguida, adicione assinaturas como desejado:

```csharp
SubscriptionDescription myAgentSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Inventory");
SubscriptionDescription myAuditSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Dashboard");
```

Então, você poderá criar um cliente de tópico. Por exemplo:

```csharp
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider);
TopicClient myTopicClient = factory.CreateTopicClient(myTopic.Path)
```

Usando o remetente da mensagem, você pode enviar e receber mensagens de e para o tópico, como mostrado na seção anterior. Por exemplo:

```csharp
foreach (BrokeredMessage message in messageList)
{
    myTopicClient.Send(message);
    Console.WriteLine(
    string.Format("Message sent: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

De forma semelhante ao que acontece às filas, as mensagens são recebidas de uma assinatura usando um objeto [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) em vez de um objeto [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient). Crie o cliente de assinatura, passando o nome do tópico, o nome da assinatura e (opcionalmente) o modo de recebimento como parâmetros. Por exemplo, com a assinatura de **Inventory**:

```csharp
// Create the subscription client
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider); 

SubscriptionClient agentSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Inventory", ReceiveMode.PeekLock);
SubscriptionClient auditSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Dashboard", ReceiveMode.ReceiveAndDelete); 

while ((message = agentSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Inventory...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
    message.Complete();
}          

// Create a receiver using ReceiveAndDelete mode
while ((message = auditSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Dashboard...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

### <a name="rules-and-actions"></a>Regras e ações
Em muitos cenários, as mensagens com características específicas precisam ser processadas de maneiras diferentes. Para habilitar isso, você pode configurar assinaturas para localizar as mensagens com as propriedades desejáveis e, em seguida, realizar determinadas modificações nessas propriedades. Embora as assinaturas do Barramento de Serviço vejam todas as mensagens enviadas para o tópico, você só poderá copiar um subconjunto dessas mensagens para a fila de assinatura virtual. Isso é feito usando filtros de assinatura. Tais modificações são chamadas de *ações de filtro*. Quando uma assinatura for criada, você poderá fornecer uma expressão de filtro que funcione nas propriedades da mensagem, as propriedades do sistema (por exemplo, **Label**) e as propriedades personalizadas do aplicativo (por exemplo, **StoreName**). A expressão de filtro SQL é opcional neste caso; sem uma expressão de filtro SQL, qualquer ação de filtro definida em uma assinatura será executada em todas as mensagens para essa assinatura.

Usando o exemplo anterior, para filtrar as mensagens que venham apenas de **Store1**, você deverá criar a assinatura de Painel da seguinte maneira:

```csharp
namespaceManager.CreateSubscription("IssueTrackingTopic", "Dashboard", new SqlFilter("StoreName = 'Store1'"));
```

Com esse filtro de assinatura ativado, somente as mensagens com a propriedade `StoreName` definida como `Store1` são copiadas para a fila virtual para a assinatura de `Dashboard`.

Para saber mais sobre possíveis valores de filtro, consulte a documentação das classes [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) e [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction). Além disso, veja os exemplos [Brokered Messaging: Advanced Filters (Sistema de mensagens agenciado: filtros avançados)](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) e [Topic Filters (Filtros do tópico)](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters).

## <a name="next-steps"></a>Próximas etapas
Consulte os tópicos avançados a seguir para saber mais e obter exemplos de como usar o sistema de mensagens agenciado do Barramento de Serviço.

* [Visão geral de mensagens do Barramento de Serviço](service-bus-messaging-overview.md)
* [Tutorial do .NET do sistema de mensagens agenciado do Barramento de Serviço](service-bus-brokered-tutorial-dotnet.md)
* [Tutorial REST do sistema de mensagens agenciado do Barramento de Serviço](service-bus-brokered-tutorial-rest.md)
* [Exemplo de filtros do tópico](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/TopicFilters)
* [Sistema de mensagens agenciado: filtros avançados](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)

