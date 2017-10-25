---
title: "Sistema de mensagens assíncronas do Barramento de Serviço | Microsoft Docs"
description: "Descrição da mensagem assíncrona do Barramento de Serviço do Azure."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f1435549-e1f2-40cb-a280-64ea07b39fc7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/06/2017
ms.author: sethm
ms.openlocfilehash: d36360f3fb46adf96f53976584987590791b07d0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Padrões de mensagens assíncronas e alta disponibilidade

O serviço de mensagens assíncrono pode ser implementado de diversas maneiras diferentes. Com filas, tópicos e assinaturas, o Barramento de Serviço do Azure dá suporte ao assincronismo por meio de um mecanismo de encaminhamento e repositório. Na operação normal (síncrona), você envia mensagens para filas e tópicos e recebe mensagens de filas e assinaturas. Os aplicativos escritos por você dependem de essas entidades estarem sempre disponíveis. Quando a integridade da entidade é alterada, devido a diversas circunstâncias, é necessário ter uma maneira de fornecer uma entidade de capacidade reduzida que possa atender à maioria das necessidades.

Os aplicativos normalmente usam padrões de mensagens assíncronas para habilitar diversos cenários de comunicação. Você pode criar aplicativos nos quais clientes podem enviar mensagens a serviços, mesmo quando o serviço não está em execução. Para os aplicativos que têm intermitência de comunicações, uma fila pode ajudar a normalizar a carga, fornecendo um lugar para um buffer de comunicações. Finalmente, você pode obter um balanceador de carga simples, mas eficaz, para distribuir mensagens em vários computadores.

Para manter a disponibilidade de qualquer uma dessas entidades, considere as várias formas diferentes pelas quais essas entidades podem parecer indisponíveis para um sistema de mensagens durável. Em termos gerais, vemos a entidade se tornar indisponível para os aplicativos que escrevemos das seguintes maneiras:

* Não é possível enviar mensagens.
* Não é possível receber mensagens.
* Não é possível gerenciar entidades (criar, recuperar, atualizar ou excluir entidades).
* Não é possível entrar em contato com o serviço.

Para cada uma dessas falhas, existem modos de falhas diferentes que habilitam um aplicativo a continuar a executar o trabalho em algum nível de capacidade reduzida. Por exemplo, um sistema que pode enviar mensagens, mas não recebê-las, ainda pode receber pedidos de clientes, mas não pode processar esses pedidos. Este tópico aborda problemas potenciais que podem ocorrer e como eles são atenuados. O Barramento de Serviço introduziu diversas mitigações que você deverá aceitar, e este tópico também descreve as regras que regem o uso dessas mitigações que exigem aceitação.

## <a name="reliability-in-service-bus"></a>Confiabilidade no Barramento de Serviço
Há diversas maneiras de lidar com problemas de mensagens e entidades, e há diretrizes que regem o uso apropriado dessas mitigações. Para entender as diretrizes, primeiro você deve entender o que pode falhar no Barramento de Serviço. Devido ao design de sistemas do Azure, todos esses problemas tendem a ter vida curta. Em um alto nível, as diferentes causas de indisponibilidade ocorrem da seguinte maneira:

* A limitação de um sistema externo do qual o Barramento de Serviço depende. A limitação ocorre devido a interações com os recursos de computação e armazenamento.
* Problema para um sistema do qual o Barramento de Serviço depende. Por exemplo, determinado armazenamento pode encontrar problemas.
* Falha do Barramento de Serviço em um único subsistema. Nessa situação, um nó de computação pode entrar em um estado inconsistente e deve se reiniciar sozinho, fazendo com que todas as entidades a que ele serve tenham a carga equilibrada para outros nós. Por sua vez, isso pode causar um curto período de processamento de mensagens lento.
* Falha do Barramento de Serviço em um datacenter do Azure. Essa é uma "falha catastrófica" durante a qual o sistema não pode ser acessado por vários minutos ou algumas horas.

> [!NOTE]
> O termo **armazenamento** pode significar Armazenamento do Azure e SQL Azure.
> 
> 

O Barramento de Serviço contém várias mitigações para esses problemas. As seções a seguir discutem cada problema e as respectivas mitigações.

### <a name="throttling"></a>Limitação
Com o Barramento de Serviço, a limitação possibilita o gerenciamento cooperativo de taxa de mensagens. Cada nó individual do Barramento de Serviço hospeda várias entidades. Cada uma dessas entidades faz demandas ao sistema em termos de CPU, memória, armazenamento e outros aspectos. Quando qualquer um desses aspectos detecta uso que excede os limites definidos, o Barramento de Serviço pode negar determinada solicitação. O chamador recebe um [ServerBusyException][ServerBusyException] e faz nova tentativa após 10 segundos.

Como uma mitigação, o código deve ler o erro e interromper repetições da mensagem por pelo menos 10 segundos. Como o erro pode ocorrer em partes do aplicativo cliente, espera-se que cada parte dele execute independentemente a lógica de repetição. O código pode reduzir a probabilidade de restrição, habilitando o particionamento em uma fila ou um tópico.

### <a name="issue-for-an-azure-dependency"></a>Problema para uma dependência do Azure
Ocasionalmente, outros componentes do Azure podem ter problemas de serviço. Por exemplo, quando um sistema que usa o Barramento de Serviço está sendo atualizado, esse sistema pode ter os recursos reduzidos temporariamente. Para resolver esses tipos de problemas, o Barramento de Serviço investiga e implementa mitigações regularmente. Ocorrem efeitos colaterais dessas mitigações. Por exemplo, para lidar com problemas temporários com armazenamento, o Barramento de Serviço implementa um sistema que permite que operações de envio de mensagem funcionem constantemente. Devido à natureza da mitigação, uma mensagem enviada pode levar até 15 minutos para ser exibida na fila ou assinatura afetada e estar pronta para uma operação de recebimento. Em termos gerais, a maioria das entidades não enfrentará esse problema. No entanto, devido ao número de entidades no Barramento de Serviço no Azure, essa mitigação às vezes é necessária para um pequeno subconjunto de clientes do Barramento de Serviço.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Falha do Barramento de Serviço em um único subsistema
Com qualquer aplicativo, circunstâncias podem fazer com que um componente interno do Barramento de Serviço se torne inconsistente. Quando o Barramento de Serviço detecta isso, ele coleta dados do aplicativo para ajudar a diagnosticar o que aconteceu. Depois que os dados são coletados, o aplicativo é reiniciado em uma tentativa de retorná-lo a um estado consistente. Esse processo acontece rapidamente e resulta em uma entidade que parece estar disponível por alguns minutos, embora os tempos de inatividade típicos sejam mais curtos.

Nesses casos, o aplicativo cliente gera uma exceção [System.TimeoutException][System.TimeoutException] ou [MessagingException][MessagingException]. O SDK do Barramento de Serviço contém uma mitigação para esse problema na forma de lógica de repetição de cliente automatizada. Depois que o período de repetição se esgota e a mensagem não é entregue, você pode explorar usando outros recursos, como [namespaces emparelhados][paired namespaces]. Namespaces emparelhados têm outras restrições que são abordadas neste artigo.

### <a name="failure-of-service-bus-within-an-azure-datacenter"></a>Falha do Barramento de Serviço em um datacenter do Azure
O motivo mais provável para uma falha em um datacenter do Azure é uma implantação de atualização com falha do Barramento de Serviço ou um sistema dependente. À medida que a plataforma amadureceu, a probabilidade desse tipo de falha foi reduzida. Uma falha do datacenter também pode acontecer por razões que incluem o seguinte:

* Falta de energia (a fonte de energia e a geração de energia ficam indisponíveis).
* Conectividade (interrupção da Internet entre o cliente e o Azure).

Em ambos os casos, um desastre natural ou humano causou o problema. Para solucionar esse problema e garantir que ainda possa enviar mensagens, você pode usar [namespaces emparelhados][paired namespaces] para possibilitar que as mensagens sejam enviadas a um segundo local enquanto o local principal se torna íntegro novamente. Para saber mais, confira [Práticas recomendadas para isolar aplicativos contra interrupções e desastres do Barramento de Serviço][Best practices for insulating applications against Service Bus outages and disasters].

## <a name="paired-namespaces"></a>Namespaces emparelhados
O recurso de [namespaces emparelhados][paired namespaces] dá suporte a cenários em que uma entidade do Barramento de Serviço ou uma implantação em um datacenter se torna indisponível. Embora esse evento ocorra raramente, sistemas distribuídos ainda devem estar preparados para lidar com os piores cenários. Normalmente, esse evento acontece porque algum elemento dos quais o Barramento de Serviço depende está enfrentando um problema de curto prazo. Para manter a disponibilidade do aplicativo durante uma interrupção, os usuários do Barramento de Serviço podem usar dois namespaces separados, preferencialmente em datacenters separados, para hospedar suas entidades de mensagens. O restante desta seção usa a seguinte terminologia:

* Namespace principal: o namespace com o qual seu aplicativo interage para operações de envio e recebimento.
* Namespace secundário: o namespace que atua como um backup para o namespace principal. A lógica de aplicativo não interage com esse namespace.
* Intervalo de failover: o tempo necessário para aceitar falhas normais antes que o aplicativo alterne do namespace principal para o namespace secundário.

Namespaces emparelhados dão suporte à *disponibilidade de envio*. A disponibilidade de envio preserva a capacidade de enviar mensagens. Para usar a disponibilidade de envio, seu aplicativo deve atender aos seguintes requisitos:

1. As mensagens são recebidas apenas do namespace principal.
2. As mensagens enviadas a determinada fila ou tópico podem chegar fora de ordem.
3. As mensagens em uma sessão podem chegar fora de ordem. Essa é uma falha de funcionalidade normal das sessões. Isso significa que seu aplicativo usa sessões para mensagens agrupadas logicamente.
4. O estado da sessão é mantido somente no namespace principal.
5. A fila principal pode ficar online e começar a aceitar mensagens antes de a fila secundária entregar todas as mensagens à fila principal.

As seções a seguir discutem as APIs e como as APIs são implementadas e mostra um código de exemplo que usa o recurso. Observe que há implicações de cobranças associadas a esse recurso.

### <a name="the-messagingfactorypairnamespaceasync-api"></a>A API MessagingFactory.PairNamespaceAsync
O recurso de namespaces emparelhados inclui o método [PairNamespaceAsync][PairNamespaceAsync] na classe [Microsoft.ServiceBus.Messaging.MessagingFactory][Microsoft.ServiceBus.Messaging.MessagingFactory]:

```csharp
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

Quando a tarefa é concluída, o emparelhamento do namespace também é concluído e está pronto para atuar em qualquer [MessageReceiver][MessageReceiver], [QueueClient][QueueClient] ou [TopicClient][TopicClient] criado com a instância [MessagingFactory][MessagingFactory]. [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions][Microsoft.ServiceBus.Messaging.PairedNamespaceOptions] é a classe base para os diferentes tipos de emparelhamento que estão disponíveis com um objeto [MessagingFactory][MessagingFactory]. Atualmente, a única classe derivada é uma que se chama [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions], que implementa os requisitos de disponibilidade de envio. [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions] tem um conjunto de construtores que se baseiam uns nos outros. Observando o construtor com a maioria dos parâmetros, você pode compreender o comportamento de outros construtores.

```csharp
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

Esses parâmetros têm os seguintes significados:

* *secondaryNamespaceManager*: uma instância inicializada de [NamespaceManager][NamespaceManager] para o namespace secundário que o método [PairNamespaceAsync][PairNamespaceAsync] pode usar para configurar o namespace secundário. O gerenciador de namespace é usado para obter a lista de filas no namespace e garantir que as filas obrigatórias de lista de pendências existam. Se essas filas não existirem, elas serão criadas. [NamespaceManager][NamespaceManager] requer a capacidade de criar um token com a declaração **Manage**.
* *messagingFactory*: a instância de [MessagingFactory][MessagingFactory] para o namespace secundário. O objeto [MessagingFactory][MessagingFactory] é usado para enviar e, se a propriedade [EnableSyphon][EnableSyphon] estiver definida como **true**, receber mensagens de filas de lista de pendências.
* *backlogQueueCount*: o número de filas de lista de pendências a serem criadas. Esse valor deve ser pelo menos 1. Ao serem enviadas mensagens à lista de pendências, umas dessas filas é escolhida aleatoriamente. Se você definir o valor como 1, somente uma fila poderá ser usada. Quando isso acontece e a fila de lista de pendências gera erros, o cliente não pode tentar uma fila de lista de pendências diferente e pode não enviar a mensagem. É recomendável definir esse valor como algum valor maior e usar como padrão o valor de 10. Você pode alterar isso para um valor maior ou menor, dependendo de quantos dados seu aplicativo envia por dia. Cada fila de lista de pendências pode manter até 5 GB de mensagens.
* *failoverInterval*: o tempo durante o qual você aceitará falhas no namespace principal antes de alternar qualquer entidade para o namespace secundário. Os failovers ocorrem em entidades individuais. Entidades em um único namespace frequentemente estão em nós diferentes no Barramento de Serviço. Uma falha em uma entidade não implica falha em outra. Você pode definir esse valor como [System.TimeSpan.Zero][System.TimeSpan.Zero] para realizar o failover para o secundário imediatamente após a primeira falha não transitória. Falhas que disparam o temporizador de failover são qualquer [MessagingException][MessagingException] em que a propriedade [IsTransient][IsTransient] é falsa ou um [System.TimeoutException][System.TimeoutException]. Outras exceções, como [UnauthorizedAccessException][UnauthorizedAccessException], não causam o failover, pois indicam que o cliente está configurado incorretamente. Um [ServerBusyException][ServerBusyException] não causa failover porque o padrão correto é esperar 10 segundos e, em seguida, enviar a mensagem novamente.
* *enableSyphon*: indica que esse emparelhamento específico também deve encaminhar mensagens do namespace secundário de volta ao namespace primário. Em geral, aplicativos que enviam mensagens devem definir esse valor como **false**; aplicativos que recebem mensagens devem definir esse valor como **true**. A razão disso é que, com frequência, há menos receptores de mensagens do que remetentes de mensagens. Dependendo do número de destinatários, você pode optar por ter uma única instância do aplicativo lidando com as tarefas de encaminhamento. Usar muitos receptores tem implicações de cobranças para cada fila de lista de pendências.

Para usar o código, crie uma instância principal de [MessagingFactory][MessagingFactory], uma instância secundária de [MessagingFactory][MessagingFactory], uma instância secundária de [NamespaceManager][NamespaceManager] e uma instância de [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions]. A chamada pode ser tão simples quanto o seguinte exemplo:

```csharp
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

Quando a tarefa retornada pelo método [PairNamespaceAsync][PairNamespaceAsync] for concluída, tudo está configurado e pronto para uso. Antes que a tarefa seja retornada, você pode não ter concluído todo o trabalho de segundo plano necessário para que o emparelhamento funcione corretamente. Como resultado, você não deve começar a enviar mensagens até que a tarefa seja retornada. Se alguma falha ocorrer, como credenciais inválidas ou falha ao criar filas de listas de pendências, essas exceções serão geradas quando a tarefa for concluída. Depois que a tarefa for retornada, verifique se as filas foram encontradas ou criadas examinando a propriedade [BacklogQueueCount][BacklogQueueCount] em sua instância de [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions]. No código anterior, essa operação aparece da seguinte maneira:

```csharp
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu os conceitos básicos sobre mensagens assíncronas no Barramento de Serviço, leia mais detalhes sobre [namespaces emparelhados][paired namespaces].

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PairNamespaceAsync_Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_EnableSyphon
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_BacklogQueueCount
[paired namespaces]: service-bus-paired-namespaces.md
