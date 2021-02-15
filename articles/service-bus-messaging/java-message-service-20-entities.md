---
title: Mensagens do barramento de serviço do Azure – entidades do serviço de mensagem Java (versão prévia)
description: Este artigo fornece uma visão geral das entidades de mensagens do barramento de serviço do Azure acessíveis por meio da API do serviço de mensagem Java.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: 1a7fe3d6355146ccf0fce50266a6f3b8da5231b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87801392"
---
# <a name="java-message-service-jms-20-entities-preview"></a>Java Message Service (JMS) 2,0 entidades (visualização)

Os aplicativos cliente que se conectam ao barramento de serviço Premium do Azure e utilizando a [biblioteca JMS do barramento de serviço do Azure](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) podem aproveitar as entidades abaixo.

## <a name="queues"></a>Filas

As filas em JMS são semanticamente comparáveis com as filas tradicionais do [barramento de serviço](service-bus-queues-topics-subscriptions.md#queues).

Para criar uma fila, utilize os métodos abaixo na `JMSContext` classe-

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>Tópicos

Os tópicos em JMS são semanticamente comparáveis com os [Tópicos](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)tradicionais do barramento de serviço.

Para criar um tópico, utilize os métodos abaixo na `JMSContext` classe-

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>Filas temporárias

Quando um aplicativo cliente requer uma entidade temporária que existe durante o tempo de vida do aplicativo, ele pode usar filas temporárias. Eles são utilizados no padrão de [solicitação-resposta](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) .

Para criar uma fila temporária, utilize os métodos abaixo na `JMSContext` classe-

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>Tópicos temporários

Assim como as filas temporárias, existem tópicos temporários para habilitar a publicação/assinatura por meio de uma entidade temporária que existe durante o tempo de vida do aplicativo.

Para criar um tópico temporário, utilize os métodos abaixo na `JMSContext` classe-

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>Assinaturas de Java Message Service (JMS)

Embora sejam semanticamente semelhantes às [assinaturas](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) (ou seja, existem em um tópico e habilitam a semântica de publicação/assinatura), a especificação do serviço de mensagens Java apresenta os conceitos de atributos **compartilhados**, não **compartilhados**, **duráveis** e **não duráveis** para uma determinada assinatura.

> [!NOTE]
> As assinaturas abaixo estão disponíveis na camada Premium do barramento de serviço do Azure para visualização de aplicativos cliente que se conectam ao barramento de serviço do Azure usando a [biblioteca JMS do barramento de serviço do Azure](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms).
>
> Para a visualização pública, essas assinaturas não podem ser criadas usando o portal do Azure.
>

### <a name="shared-durable-subscriptions"></a>Assinaturas duráveis compartilhadas

Uma assinatura durável compartilhada é usada quando todas as mensagens publicadas em um tópico devem ser recebidas e processadas por um aplicativo, independentemente de o aplicativo estar ativamente consumindo na assinatura em todos os momentos.

Como essa é uma assinatura compartilhada, qualquer aplicativo autenticado para receber do barramento de serviço pode receber da assinatura.

Para criar uma assinatura durável compartilhada, use os métodos abaixo na `JMSContext` classe-

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

A assinatura durável compartilhada continua existindo, a menos que seja excluída usando o `unsubscribe` método na `JMSContext` classe.

```java
void unsubscribe(String name)
```

### <a name="unshared-durable-subscriptions"></a>Assinaturas duráveis não compartilhadas

Assim como uma assinatura durável compartilhada, uma assinatura durável não compartilhada é usada quando todas as mensagens publicadas em um tópico são recebidas e processadas por um aplicativo, independentemente de o aplicativo estar ativamente consumindo na assinatura em todos os momentos.

No entanto, como essa é uma assinatura não compartilhada, somente o aplicativo que criou a assinatura pode receber dela.

Para criar uma assinatura durável não compartilhada, use os métodos abaixo da `JMSContext` classe- 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal`No momento, o recurso não tem suporte e foi ignorado.
>

A assinatura durável não compartilhada continua existindo, a menos que seja excluída usando o `unsubscribe` método na `JMSContext` classe.

```java
void unsubscribe(String name)
```

### <a name="shared-non-durable-subscriptions"></a>Assinaturas não duráveis compartilhadas

Uma assinatura não durável compartilhada é usada quando vários aplicativos cliente precisam receber e processar mensagens de uma única assinatura, somente até que estejam ativamente consumindo/recebendo.

Como a assinatura não é durável, ela não é persistente. As mensagens não são recebidas por essa assinatura quando não há consumidores ativos nela.

Para criar uma assinatura não durável compartilhada, crie uma `JmsConsumer` conforme mostrado nos métodos abaixo da `JMSContext` classe-

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

A assinatura não durável compartilhada continua existindo até que os consumidores ativos recebam dela.

### <a name="unshared-non-durable-subscriptions"></a>Assinaturas não duráveis não compartilhadas

Uma assinatura não durável não compartilhada é usada quando o aplicativo cliente precisa receber e processar mensagens de uma assinatura, somente até que esteja ativamente consumindo a partir dela. Somente um consumidor pode existir nessa assinatura, ou seja, o cliente que criou a assinatura.

Como a assinatura não é durável, ela não é persistente. As mensagens não são recebidas por essa assinatura quando não há nenhum consumidor ativo nela.

Para criar uma assinatura não durável não compartilhada, crie uma `JMSConsumer` conforme mostrado nos métodos abaixo da `JMSContext` classe-

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal`No momento, o recurso não tem suporte e foi ignorado.
>

A assinatura não durável não compartilhada continua existindo até que haja um consumidor ativo recebendo-se dela.

### <a name="message-selectors"></a>Seletores de mensagem

Assim como os **filtros e as ações** existem para assinaturas regulares do barramento de serviço, existem **seletores de mensagens** para assinaturas JMS.

Os seletores de mensagens podem ser configurados em cada uma das assinaturas JMS e existem como uma condição de filtro nas propriedades do cabeçalho da mensagem. Somente as mensagens com propriedades de cabeçalho correspondentes à expressão do seletor de mensagem são entregues. Um valor nulo ou uma cadeia de caracteres vazia indica que não há um seletor de mensagem para a assinatura/consumidor JMS.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações e exemplos de uso do sistema de mensagens do Barramento de Serviço, consulte os tópicos avançados a seguir:

* [Visão geral de mensagens do Barramento de Serviço](service-bus-messaging-overview.md)
* [Usar a API 2,0 do serviço de mensagens Java com o barramento de serviço Premium do Azure (visualização)](how-to-use-java-message-service-20.md)



