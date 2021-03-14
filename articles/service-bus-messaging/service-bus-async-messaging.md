---
title: Sistema de mensagens assíncronas do Barramento de Serviço | Microsoft Docs
description: Saiba como o barramento de serviço do Azure dá suporte a assincronismo por meio de um mecanismo de armazenamento e encaminhamento com filas, tópicos e assinaturas.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e37c18b95bca7ef1e6e8f0d74976bb73b214624a
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500624"
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
> O termo **armazenamento** pode significar o Armazenamento do Azure e o SQL Azure.
> 
> 

O Barramento de Serviço contém várias mitigações para esses problemas. As seções a seguir discutem cada problema e as respectivas mitigações.

### <a name="throttling"></a>Limitação
Com o Barramento de Serviço, a limitação possibilita o gerenciamento cooperativo de taxa de mensagens. Cada nó individual do Barramento de Serviço hospeda várias entidades. Cada uma dessas entidades faz demandas ao sistema em termos de CPU, memória, armazenamento e outros aspectos. Quando qualquer um desses aspectos detecta uso que excede os limites definidos, o Barramento de Serviço pode negar determinada solicitação. O chamador recebe um [ServerBusyException][ServerBusyException] e se recupera após 10 segundos.

Como uma mitigação, o código deve ler o erro e interromper repetições da mensagem por pelo menos 10 segundos. Como o erro pode ocorrer em partes do aplicativo cliente, espera-se que cada parte dele execute independentemente a lógica de repetição. O código pode reduzir a probabilidade de restrição, habilitando o particionamento em uma fila ou um tópico.

### <a name="issue-for-an-azure-dependency"></a>Problema para uma dependência do Azure
Ocasionalmente, outros componentes do Azure podem ter problemas de serviço. Por exemplo, quando um sistema que usa o Barramento de Serviço está sendo atualizado, esse sistema pode ter os recursos reduzidos temporariamente. Para resolver esses tipos de problemas, o Barramento de Serviço investiga e implementa mitigações regularmente. Ocorrem efeitos colaterais dessas mitigações. Por exemplo, para lidar com problemas temporários com armazenamento, o Barramento de Serviço implementa um sistema que permite que operações de envio de mensagem funcionem constantemente. Devido à natureza da mitigação, uma mensagem enviada pode levar até 15 minutos para ser exibida na fila ou assinatura afetada e estar pronta para uma operação de recebimento. Em termos gerais, a maioria das entidades não enfrentará esse problema. No entanto, devido ao número de entidades no Barramento de Serviço no Azure, essa mitigação às vezes é necessária para um pequeno subconjunto de clientes do Barramento de Serviço.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Falha do Barramento de Serviço em um único subsistema
Com qualquer aplicativo, circunstâncias podem fazer com que um componente interno do Barramento de Serviço se torne inconsistente. Quando o Barramento de Serviço detecta isso, ele coleta dados do aplicativo para ajudar a diagnosticar o que aconteceu. Depois que os dados são coletados, o aplicativo é reiniciado em uma tentativa de retorná-lo a um estado consistente. Esse processo acontece rapidamente e resulta em uma entidade que parece estar disponível por alguns minutos, embora os tempos de inatividade típicos sejam mais curtos.

Nesses casos, o aplicativo cliente gera uma exceção do tipo [System.TimeoutException][System.TimeoutException] ou [MessagingException][MessagingException]. O SDK do Barramento de Serviço contém uma mitigação para esse problema na forma de lógica de repetição de cliente automatizada. Depois que o período de repetição se esgota e a mensagem não é entregue, você pode explorar usando outros recursos mencionados no artigo sobre como [lidar com interrupções e desastres][handling outages and disasters].

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu os conceitos básicos sobre mensagens assíncronas no Barramento de Serviço, saiba mais sobre como [lidar com interrupções e desastres][handling outages and disasters].

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: /dotnet/api/system.timeoutexception
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
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[System.TimeSpan.Zero]: /dotnet/api/system.timespan.zero
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[UnauthorizedAccessException]: /dotnet/api/system.unauthorizedaccessexception
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[handling outages and disasters]: service-bus-outages-disasters.md