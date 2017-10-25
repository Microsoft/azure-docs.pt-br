---
title: "Guia de programação para Hubs de Eventos do Azure | Microsoft Docs"
description: "Escreva código para Hubs de Eventos do Azure usando o SDK do .NET."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 64cbfd3d-4a0e-4455-a90a-7f3d4f080323
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/17/2017
ms.author: sethm
ms.openlocfilehash: 405ec2b27b488b570c4a5c86e4950ff98233360e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="event-hubs-programming-guide"></a>Guia de programação dos Hubs de Eventos

Este artigo descreve alguns cenários comuns de produção de código usando os Hubs de Evento do Azure e o SDK do .NET. Ele supõe uma compreensão preliminar de Hubs de Eventos. Para obter uma visão geral conceitual dos Hubs de Eventos, confira [Visão geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md).

## <a name="event-publishers"></a>Editores de eventos

Envie eventos para um Hub de Eventos usando HTTP POST ou via uma conexão AMQP 1.0. A escolha de qual usar e quando depende do cenário específico que está sendo abordado. Conexões do AMQP 1.0 são medidas como conexões negociadas no Barramento de Serviço e são mais apropriadas em cenários com volumes de mensagem frequentemente mais altos e requisitos de latência mais baixos, já que fornecem um canal de mensagens persistente.

Você cria e gerencia Hubs de Eventos usando a classe [NamespaceManager][] . Ao usar APIs gerenciadas pelo .NET, as principais construções para publicar os dados nos Hubs de Eventos são as classes [EventHubClient](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) e [EventData][]. [EventHubClient][] fornece o canal de comunicação AMQP pelo qual os eventos são enviados ao Hub de Eventos. A classe [EventData][] representa um evento e é usada para publicar mensagens em um hub de eventos. Essa classe inclui o corpo, alguns metadados e informações de cabeçalho sobre o evento. Outras propriedades são adicionadas ao objeto [EventData][] à medida que ele passa por um hub de eventos.

## <a name="get-started"></a>Introdução

As classes do .NET que são compatíveis com Hubs de Eventos são fornecidos no assembly Microsoft.ServiceBus.dll. A maneira mais fácil de consultar a API do Barramento de Serviço e configurar seu aplicativo com todas as dependências do Barramento de Serviço é baixar o [pacote NuGet do Barramento de Serviço](https://www.nuget.org/packages/WindowsAzure.ServiceBus). Como alternativa, você pode usar o [Console do Gerenciador de Pacotes](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) no Visual Studio. Digite o comando a seguir na janela [Console do Gerenciador de Pacotes](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) :

```
Install-Package WindowsAzure.ServiceBus
```

## <a name="create-an-event-hub"></a>Criar um Hub de Evento
Você pode usar a classe [NamespaceManager][] para criar Hubs de Eventos. Por exemplo:

```csharp
var manager = new Microsoft.ServiceBus.NamespaceManager("mynamespace.servicebus.windows.net");
var description = manager.CreateEventHub("MyEventHub");
```

Na maioria dos casos, é recomendável que você use os métodos [CreateEventHubIfNotExists][] para evitar gerar exceções se o serviço for reiniciado. Por exemplo:

```csharp
var description = manager.CreateEventHubIfNotExists("MyEventHub");
```

Todas as operações de criação de Hubs de Eventos, inclusive [CreateEventHubIfNotExists][], exigem permissões **Gerenciar** no namespace em questão. Se você quiser limitar as permissões de seus aplicativos de consumidor ou publicador, pode evitar essas chamadas de criação de operação no código de produção, ao usar credenciais com permissões limitadas.

A classe [EventHubDescription](/dotnet/api/microsoft.servicebus.messaging.eventhubdescription) contém detalhes sobre um hub de eventos, incluindo as regras de autorização, intervalo de retenção de mensagem, ID de partição, status e caminho. Você pode usar essa classe para atualizar os metadados em um hub de eventos.

## <a name="create-an-event-hubs-client"></a>Criar um cliente de Hubs de Eventos
A classe principal para interagir com Hubs de Eventos é [Microsoft.ServiceBus.Messaging.EventHubClient][EventHubClient]. Essa classe fornece recursos de remetente e destinatário. Você pode criar uma instância dessa classe usando o método [Criar](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.create) , conforme mostrado no exemplo a seguir.

```csharp
var client = EventHubClient.Create(description.Path);
```

Esse método usa as informações de conexão do Barramento de Serviço no arquivo App.config, na seção `appSettings` . Para obter um exemplo do XML `appSettings` usado para armazenar informações de conexão de Barramento de Serviço, consulte a documentação do método [Microsoft.ServiceBus.Messaging.EventHubClient.Create(System.String)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Create_System_String_) .

Outra opção é criar o cliente por meio de uma cadeia de conexão. Essa opção funciona bem ao usar funções de trabalho do Azure, porque você pode armazenar a cadeia de caracteres nas propriedades de configuração do trabalho. Por exemplo:

```csharp
EventHubClient.CreateFromConnectionString("your_connection_string");
```

A cadeia de conexão será no mesmo formato em que aparece no arquivo App.config dos métodos anteriores:

```
Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[key]
```

Por fim, também é possível criar um objeto [EventHubClient][] a partir de uma instância [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory), como mostrado no exemplo a seguir.

```csharp
var factory = MessagingFactory.CreateFromConnectionString("your_connection_string");
var client = factory.CreateEventHubClient("MyEventHub");
```

É importante observar que outros objetos [EventHubClient][] criados em uma instância de fábrica de mensagens vão reutilizar a mesma conexão TCP subjacente. Portanto, esses objetos têm um limite, no lado do cliente, na taxa de transferência. O método [Criar](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Create_System_String_) reutiliza uma única fábrica de mensagens única. Se você precisa de uma taxa de transferência muito alta de um único remetente, é possível criar diversas fábricas de mensagens e um objeto [EventHubClient][] de cada fábrica de mensagens.

## <a name="send-events-to-an-event-hub"></a>Enviar eventos para um hub de eventos
Você envia eventos a um hub de eventos, criando uma instância [EventData][] e enviando-a por meio do método [Send](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_). Esse método usa um único parâmetro [EventData][] da instância e o envia de forma síncrona para um hub de eventos.

## <a name="event-serialization"></a>Serialização de evento
A classe [EventData][] tem [quatro construtores sobrecarregados ](/dotnet/api/microsoft.servicebus.messaging.eventdata#constructors_) que usam vários parâmetros, como um objeto e um serializador, matriz de bytes ou fluxo. Também é possível criar uma instância da classe [EventData][] e definir o fluxo de corpo posteriormente. Ao usar o JSON com [EventData][], você pode usar **Encoding.UTF8.GetBytes()** para recuperar a matriz de bytes para uma cadeia de caracteres codificada em JSON.

## <a name="partition-key"></a>Chave de partição
A classe [EventData][] tem uma propriedade [PartitionKey][] que permite que o remetente especifique um valor de hash para produzir uma atribuição de partição. Usar uma chave de partição garante que todos os eventos com a mesma chave sejam enviados para a mesma partição no hub de eventos. Chaves de partição comuns incluem IDs de sessão de usuário e IDs de remetente exclusivo. A propriedade [PartitionKey][] é opcional e pode ser fornecida ao usar o método [Microsoft.ServiceBus.Messaging.EventHubClient.Send(Microsoft.ServiceBus.Messaging.EventData)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) ou [Microsoft.ServiceBus.Messaging.EventHubClient.SendAsync(Microsoft.ServiceBus.Messaging.EventData)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_SendAsync_Microsoft_ServiceBus_Messaging_EventData_). Se você não fornecer um valor para [PartitionKey][], os eventos enviados serão distribuídos para partições que usam um modelo round-robin.

### <a name="availability-considerations"></a>Considerações sobre disponibilidade

Usar uma chave de partição é opcional, e você deve considerar cuidadosamente se deseja ou não usar uma. Em muitos casos, usar uma chave de partição é uma boa opção se a ordem dos eventos for importante. Quando você usa uma chave de partição, essas partições exigem disponibilidade em um único nó, e podem ocorrer interrupções ao longo do tempo; por exemplo, durante a reinicialização e aplicação de patches de nós de computação. Assim, se você definir uma ID de partição, e essa partição ficar indisponível por algum motivo, uma tentativa de acessar os dados na partição falhará. Se a alta disponibilidade for mais importante, não especifique uma chave de partição; nesse caso, os eventos serão enviados às partições usando o modelo de round-robin descrito anteriormente. Nesse cenário, você está fazendo uma opção explícita entre disponibilidade (sem ID de partição) e consistência (fixando eventos a uma ID de partição).

Outra consideração é o tratamento de atrasos no processamento de eventos. Em alguns casos, talvez seja melhor descartar os dados e repetir a operação do que acompanhar o processamento, o que pode causar ainda mais atrasos de processamento de downstream. Por exemplo, com uma cotação de bolsa é melhor aguardar dados atualizados, mas em um bate-papo ao vivo ou um cenário de VOIP é melhor ter os dados rapidamente, mesmo se não estiverem completos.

Com base nessas considerações de disponibilidade, nesses cenários, você pode escolher uma das seguinte estratégias de tratamento de erro:

- Parar (para de ler de Hubs de Eventos até que tudo seja corrigido)
- Descartar (as mensagens não são importantes, descarte-as)
- Tentar novamente (repete as mensagens como você achar adequado)
- [Mensagens mortas](../service-bus-messaging/service-bus-dead-letter-queues.md) (use uma fila ou outro hub de eventos para considerar mensagens mortas apenas as mensagens que não puderam ser processadas)

Para obter mais informações e uma discussão sobre as compensações entre disponibilidade e a consistência, consulte [Disponibilidade e a consistência nos Hubs de Eventos](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Operações de envio de eventos em lote
Envio de eventos em lotes pode aumentar significativamente a taxa de transferência. O método [SendBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_SendBatch_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_EventData__) usa um parâmetro **IEnumerable** do tipo [EventData][] e envia o lote inteiro como uma operação atômica para o hub de eventos.

```csharp
public void SendBatch(IEnumerable<EventData> eventDataList);
```

Observe que um único lote não deve exceder o limite de 256 KB de um evento. Além disso, cada mensagem no lote usa a mesma identidade do publicador. É responsabilidade do remetente garantir que o lote não exceda o tamanho máximo do evento. Em caso afirmativo, um erro **Enviar** do cliente é gerado.

## <a name="send-asynchronously-and-send-at-scale"></a>Enviar de forma assíncrona e em escala
Você também pode enviar eventos para um hub de eventos de forma assíncrona. O envio de forma assíncrona pode aumentar a taxa em que um cliente é capaz de enviar eventos. Os métodos [Send](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) e [SendBatch](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_SendBatch_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_EventData__) estão disponíveis nas versões assíncronas que retornam um objeto [Task](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx). Embora essa técnica possa aumentar a taxa de transferência, ela também pode fazer com que o cliente continue a enviar eventos mesmo enquanto está sendo limitado pelo serviço de Hubs de Eventos e pode resultar em falhas por parte do cliente ou mensagens perdidas, se não for implementada corretamente. Além disso, você pode usar a propriedade [RetryPolicy](/dotnet/api/microsoft.servicebus.messaging.cliententity#Microsoft_ServiceBus_Messaging_ClientEntity_RetryPolicy) no cliente para controlar as opções de novas tentativas do cliente.

## <a name="create-a-partition-sender"></a>Criar um remetente de partição
Embora seja mais comum enviar eventos para um hub de eventos sem uma chave de partição, em alguns casos, você pode enviar eventos diretamente para uma determinada partição. Por exemplo:

```csharp
var partitionedSender = client.CreatePartitionedSender(description.PartitionIds[0]);
```

[CreatePartitionedSender](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_CreatePartitionedSender_System_String_) retorna um objeto [EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender) que pode ser usado para publicar eventos em uma partição específica do hub de eventos.

## <a name="event-consumers"></a>Consumidores de evento
O Hubs de Eventos tem dois modelos principais para consumo de eventos: receptores diretos e abstrações de nível superior, como [EventProcessorHost][]. Receptores diretos são responsáveis por sua própria coordenação de acesso a partições em um grupo de consumidores.

### <a name="direct-consumer"></a>Consumidor direto
A maneira mais direta de ler por meio de uma partição em um grupo de consumidores é usar a classe [EventHubReceiver](/dotnet/apie/microsoft.servicebus.messaging.eventhubreceiver) . Para criar uma instância dessa classe, você deve usar uma instância da classe [EventHubConsumerGroup](/dotnet/api/microsoft.servicebus.messaging.eventhubconsumergroup) . No exemplo de código a seguir, a ID da partição deve ser especificada ao criar o destinatário para o grupo de consumidores.

```csharp
EventHubConsumerGroup group = client.GetDefaultConsumerGroup();
var receiver = group.CreateReceiver(client.GetRuntimeInformation().PartitionIds[0]);
```

O método [CreateReceiver](/dotnet/api/microsoft.servicebus.messaging.eventhubconsumergroup#methods_summary) tem várias sobrecargas que facilitam o controle sobre o leitor que está sendo criado. Esses métodos incluem a especificação de um deslocamento como uma cadeia de caracteres ou um carimbo de data/hora e a capacidade de especificar se deseja incluir o deslocamento especificado no fluxo retornado ou iniciar depois dele. Depois de criar o receptor, você pode iniciar o recebimento de eventos no objeto retornado. O método [Receber](/dotnet/api/microsoft.servicebus.messaging.eventhubreceiver#methods_summary) tem quatro sobrecargas que controlam os parâmetros da operação de recebimento, como tamanho de lote e tempo de espera. Você pode usar as versões assíncronas desses métodos para aumentar a taxa de transferência de um consumidor. Por exemplo:

```csharp
bool receive = true;
string myOffset;
while(receive)
{
    var message = receiver.Receive();
    myOffset = message.Offset;
    string body = Encoding.UTF8.GetString(message.GetBytes());
    Console.WriteLine(String.Format("Received message offset: {0} \nbody: {1}", myOffset, body));
}
```

Em relação a uma partição específica, as mensagens são recebidas na ordem em que foram enviadas para o hub de eventos. O deslocamento é um token de cadeia de caracteres usado para identificar uma mensagem em uma partição.

Observe que uma única partição em um grupo de consumidores não pode ter mais de 5 leitores simultâneos conectados em algum momento. Conforme os leitores são conectados ou desconectados, suas sessões podem permanecer ativas por vários minutos antes que o serviço reconheça que foram desconectados. Durante esse tempo, a reconexão a uma partição pode falhar. Para obter um exemplo completo de como gravar um destinatário direto para Hubs de Eventos, consulte o exemplo [Receptores diretos de Hubs de Eventos](https://code.msdn.microsoft.com/Event-Hub-Direct-Receivers-13fa95c6).

### <a name="event-processor-host"></a>Host do processador de eventos
A classe [EventProcessorHost][] processa dados de Hubs de Eventos. Você deve usar essa implementação ao criar os leitores de evento na plataforma .NET. [EventProcessorHost][] fornece um ambiente de tempo de execução seguro, thread-safe, de vários processos, para implementações de processador de eventos, que também fornece gerenciamento de concessão de ponto de verificação e partição.

Para usar a classe [EventProcessorHost][], você pode implementar o [IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor). Essa interface contém três métodos:

* [OpenAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_OpenAsync_Microsoft_ServiceBus_Messaging_PartitionContext_)
* [CloseAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_CloseAsync_Microsoft_ServiceBus_Messaging_PartitionContext_Microsoft_ServiceBus_Messaging_CloseReason_)
* [ProcessEventsAsync](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor#Microsoft_ServiceBus_Messaging_IEventProcessor_ProcessEventsAsync_Microsoft_ServiceBus_Messaging_PartitionContext_System_Collections_Generic_IEnumerable_Microsoft_ServiceBus_Messaging_EventData__)

Para iniciar o processamento de eventos, instancie o [EventProcessorHost][] fornecendo os parâmetros adequados para seu hub de eventos. Em seguida, chame [RegisterEventProcessorAsync](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost#Microsoft_ServiceBus_Messaging_EventProcessorHost_RegisterEventProcessorAsync__1) para registrar sua implementação [IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor) na execução. Neste ponto, o host tentará adquirir uma concessão em todas as partições do hub de eventos usando um algoritmo “greedy”. Essas concessões vão durar por determinado período e, em seguida, devem ser renovadas. Como novos nós, as instâncias de trabalho, nesse caso, ficam online, colocam reservas de concessão e com o tempo, a carga é trocada entre os nós, à medida que cada uma tenta adquirir mais concessões.

![Host do processador de eventos](./media/event-hubs-programming-guide/IC759863.png)

Ao longo do tempo, é estabelecido um equilíbrio. Essa funcionalidade dinâmica permite que o dimensionamento automático com base em CPU seja aplicado aos consumidores de ampliação e redução de escala. Como os Hubs de Eventos não têm um conceito direto de contagem de mensagens, a utilização média da CPU é, geralmente, o melhor mecanismo para medir back end ou escala do consumidor. Se os publicadores começam a publicar mais eventos do que os consumidores podem processar, o aumento de CPU nos consumidores pode ser usado para provocar escala automática na contagem de instâncias de trabalho.

A classe [EventProcessorHost][] também implementa um mecanismo de verificação com base em armazenamento do Azure. Esse mecanismo armazena o deslocamento por base de partição, de forma que cada consumidor pode determinar qual foi o último ponto de verificação do consumidor anterior. Conforme as partições fazem transição entre os nós por meio de concessões, esse é o mecanismo de sincronização que facilita a alternância de carga.

## <a name="publisher-revocation"></a>Revogação do publicador
Além dos recursos avançados de tempo de execução do [EventProcessorHost][], os Hubs de Eventos permitem a revogação do editor para impedir que publicadores específicos enviem eventos para um hub de eventos. Esses recursos serão especialmente úteis se o token do editor tiver sido comprometido ou uma atualização de software estiver causando um comportamento inadequado. Nessas situações, a identidade do editor, que faz parte do token SAS, pode ser impedida de publicar eventos.

Para obter mais informações sobre a revogação do publicador e como enviar para os Hubs de Eventos como um publicador, consulte o exemplo [Publicação Segura em Larga Escala dos Hubs de Eventos](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab).

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os cenários de Hubs de Eventos, consulte estes links:

* [Visão geral de API de Hubs de Eventos](event-hubs-api-overview.md)
* [O que são Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Disponibilidade e consistência nos Hubs de Eventos](event-hubs-availability-and-consistency.md)
* [Referência de API do host de processador de eventos](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.servicebus.messaging.eventhubclient
[EventData]: /dotnet/api/microsoft.servicebus.messaging.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
