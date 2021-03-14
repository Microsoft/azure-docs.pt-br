---
title: Mensagens do barramento de serviço do Azure – entidades do serviço de mensagem Java
description: Este artigo fornece uma visão geral das entidades de mensagens do barramento de serviço do Azure acessíveis por meio da API do serviço de mensagem Java.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: ee4e0124dced16b86d5292c647e129aa87645f22
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652574"
---
# <a name="java-message-service-jms-20-entities"></a>Entidades do Java Message Service (JMS) 2,0

Os aplicativos cliente que se conectam ao barramento de serviço Premium do Azure e usam a [biblioteca JMS do barramento de serviço do Azure](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) podem usar as entidades abaixo.

## <a name="queues"></a>Filas

As filas em JMS são semanticamente comparáveis com as filas tradicionais do [barramento de serviço](service-bus-queues-topics-subscriptions.md#queues).

Para criar uma fila, use os métodos abaixo na `JMSContext` classe-

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>Tópicos

Os tópicos em JMS são semanticamente comparáveis com os [Tópicos](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)tradicionais do barramento de serviço.

Para criar um tópico, use os métodos abaixo na `JMSContext` classe-

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>Filas temporárias

Se um aplicativo cliente exigir uma entidade temporária que existe durante o tempo de vida do aplicativo, ele poderá usar filas temporárias. Essas entidades são usadas no padrão de [solicitação-resposta](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) .

Para criar uma fila temporária, use os métodos abaixo na `JMSContext` classe-

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>Tópicos temporários

Assim como as filas temporárias, existem tópicos temporários para habilitar a publicação/assinatura por meio de uma entidade temporária que existe durante o tempo de vida do aplicativo.

Para criar um tópico temporário, use os métodos abaixo na `JMSContext` classe-

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>Assinaturas de Java Message Service (JMS)

Embora sejam semanticamente semelhantes às [assinaturas](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) (ou seja, existem em um tópico e habilitam a semântica de publicação/assinatura), a especificação do serviço de mensagens Java apresenta os conceitos de atributos **compartilhados**, não **compartilhados**, * * duráveis e **não duráveis** para uma determinada assinatura.

> [!NOTE]
> As assinaturas abaixo estão disponíveis na camada Premium do barramento de serviço do Azure para aplicativos cliente que se conectam ao barramento de serviço do Azure usando a [biblioteca JMS do barramento de serviço do Azure](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms).
>
> Somente assinaturas duráveis podem ser criadas usando o portal do Azure.
>

### <a name="shared-durable-subscriptions"></a>Assinaturas duráveis compartilhadas

Uma assinatura durável compartilhada é usada quando todas as mensagens publicadas em um tópico devem ser recebidas e processadas por um aplicativo, independentemente de o aplicativo estar ativamente consumindo na assinatura em todos os momentos.

Qualquer aplicativo autenticado para receber do barramento de serviço pode receber da assinatura durável compartilhada.

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

Como uma assinatura durável compartilhada, uma assinatura durável não compartilhada é usada quando todas as mensagens publicadas em um tópico devem ser recebidas e processadas por um aplicativo, independentemente de o aplicativo estar ativamente consumindo na assinatura.

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

## <a name="additional-concepts-for-java-message-service-jms-20-subscriptions"></a>Conceitos adicionais para assinaturas do Java Message Service (JMS) 2,0

### <a name="client-scoping"></a>Escopo do cliente

As assinaturas, conforme especificado na API do Java Message Service (JMS) 2,0, podem ou não ter o *escopo definido para aplicativos cliente específicos* (identificados com o apropriado `clientId` ).

Depois que a assinatura estiver no escopo, ela **só poderá ser acessada** de aplicativos cliente que tenham a mesma ID de cliente. 

Qualquer tentativa de acessar uma assinatura com escopo para uma ID de cliente específica (digamos, clientId1) de um aplicativo com outra ID de cliente (digamos, clientId2) levará à criação de outra assinatura com escopo para a outra ID do cliente (clientId2).

> [!NOTE]
> A ID do cliente pode ser nula ou vazia, mas deve corresponder à ID do cliente definida no aplicativo cliente JMS. Da perspectiva do barramento de serviço do Azure, uma ID de cliente nula e uma ID de cliente vazia têm o mesmo comportamento.
>
> Se a ID do cliente for definida como nula ou vazia, ela só poderá ser acessada por aplicativos cliente cuja ID do cliente também seja definida como nula ou vazia.
>

### <a name="shareability"></a>Compartilhamento

As assinaturas **compartilhadas** permitem que vários clientes/consumidores (ou seja, objetos JMSConsumer) recebam mensagens deles.

>[!NOTE]
> As assinaturas compartilhadas com escopo para uma ID de cliente específica ainda podem ser acessadas por vários clientes/consumidores (ou seja, objetos JMSConsumer), mas cada um dos aplicativos cliente deve ter a mesma ID de cliente.
>
 

Assinaturas não **compartilhadas** permitem que apenas um único cliente/consumidor (ou seja, objeto JMSConsumer) receba mensagens deles. Se um `JMSConsumer` for criado em uma assinatura não compartilhada enquanto já tiver uma `JMSConsumer` escuta ativa de mensagens, um `JMSException` será lançado.


### <a name="durability"></a>Durabilidade

As assinaturas **duráveis** são persistidas e continuam a coletar mensagens do tópico, independentemente de um aplicativo ( `JMSConsumer` ) estar consumindo mensagens dela.

As assinaturas **não duráveis** não são persistentes e coletam mensagens do tópico desde que um aplicativo ( `JMSConsumer` ) esteja consumindo mensagens dela. 

## <a name="representation-of-client-scoped-subscriptions"></a>Representação de assinaturas com escopo de cliente

Considerando que as assinaturas de escopo do cliente (JMS) devem coexistir com as assinaturas existentes, a maneira como [as assinaturas do](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)escopo do cliente (JMS) são representadas seguem o formato abaixo.

   * **\<SUBSCRIPTION-NAME\>**$**\<CLIENT-ID\>**$**D** (para assinaturas duráveis)
   * **\<SUBSCRIPTION-NAME\>**$**\<CLIENT-ID\>**$**ND** (para assinaturas não duráveis)

Aqui, **$** é o delimitador.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações e exemplos de uso do sistema de mensagens do Barramento de Serviço, consulte os tópicos avançados a seguir:

* [Visão geral de mensagens do Barramento de Serviço](service-bus-messaging-overview.md)
* [Usar a API 2,0 do serviço de mensagens Java com o barramento de serviço Premium do Azure](how-to-use-java-message-service-20.md)



