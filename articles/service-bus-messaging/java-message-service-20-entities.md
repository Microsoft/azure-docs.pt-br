---
title: Mensagens do Barramento de Serviço do Azure – Entidades do serviço de mensagens do Java
description: Este artigo fornece uma visão geral das entidades de mensagens do Barramento de Serviço do Azure acessíveis por meio da API do serviço de mensagens do Java.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: ee4e0124dced16b86d5292c647e129aa87645f22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652574"
---
# <a name="java-message-service-jms-20-entities"></a>Entidades do JMS (Serviço de Mensagens Java) 2.0

Os aplicativos cliente que se conectam ao Barramento de Serviço Premium do Azure e usam a [biblioteca do JMS do Barramento de Serviço do Azure](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) podem usar as entidades abaixo.

## <a name="queues"></a>Filas

As filas no JMS são semanticamente comparáveis às tradicionais [filas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md#queues).

Para criar uma Fila, use os métodos abaixo na classe `JMSContext` –

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>Tópicos

Os tópicos no JMS são semanticamente comparáveis aos tradicionais [tópicos do Barramento de Serviço](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions).

Para criar um Tópico, use os métodos abaixo na classe `JMSContext` –

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>Filas temporárias

Se um aplicativo cliente exigir uma entidade temporária que exista apenas durante o tempo de vida do aplicativo, ele poderá usar filas temporárias. Essas entidades são usadas no padrão [Solicitação-Resposta](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html).

Para criar uma fila temporária, use os métodos abaixo na classe `JMSContext` –

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>Tópicos temporários

Assim como as Filas Temporárias, os Tópicos Temporários existem para habilitar a publicação/assinatura por meio de uma entidade temporária que existe apenas durante o tempo de vida do aplicativo.

Para criar um tópico temporário, use os métodos abaixo na classe `JMSContext` –

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>Assinaturas do JSM (Serviço de Mensagens do Java)

Embora sejam semanticamente semelhantes às [Assinaturas](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) (ou seja, existem em um tópico e habilitam a semântica de publicação/assinatura), a especificação do Serviço de Mensagens do Java apresenta os conceitos de atributos **Compartilhados**, **Não compartilhados**, **Duráveis e **Não duráveis** para determinada assinatura.

> [!NOTE]
> As assinaturas abaixo estão disponíveis na camada Premium do Barramento de Serviço do Azure para aplicativos cliente que se conectam ao Barramento de Serviço do Azure usando a [biblioteca do JMS do Barramento de Serviço do Azure](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms).
>
> Somente assinaturas duráveis podem ser criadas usando o portal do Azure.
>

### <a name="shared-durable-subscriptions"></a>Assinaturas duráveis compartilhadas

Uma assinatura durável compartilhada é usada quando todas as mensagens publicadas em um tópico devem ser recebidas e processadas por um aplicativo, independentemente de o aplicativo estar consumindo ativamente por meio da assinatura a todo momento.

Qualquer aplicativo autenticado para receber por meio do barramento de serviço pode receber por meio da assinatura durável compartilhada.

Para criar uma assinatura durável compartilhada, use os métodos abaixo na classe `JMSContext` –

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

A assinatura durável compartilhada continua existindo, a menos que seja excluída usando o método `unsubscribe` na classe `JMSContext`.

```java
void unsubscribe(String name)
```

### <a name="unshared-durable-subscriptions"></a>Assinaturas duráveis não compartilhadas

Assim como uma assinatura durável compartilhada, a assinatura durável não compartilhada é usada quando todas as mensagens publicadas em um tópico devem ser recebidas e processadas por um aplicativo, independentemente de o aplicativo estar consumindo ativamente por meio da assinatura.

No entanto, como essa é uma assinatura não compartilhada, somente o aplicativo que criou a assinatura pode receber dela.

Para criar uma assinatura durável não compartilhada, use os métodos abaixo por meio da classe `JMSContext` – 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> Neste momento, o recurso `noLocal` não tem suporte e foi ignorado.
>

A assinatura durável não compartilhada continua existindo, a menos que seja excluída usando o método `unsubscribe` na classe `JMSContext`.

```java
void unsubscribe(String name)
```

### <a name="shared-non-durable-subscriptions"></a>Assinaturas não duráveis compartilhadas

Uma assinatura não durável compartilhada é usada quando vários aplicativos cliente precisam receber e processar mensagens por meio de uma assinatura única, somente enquanto estiverem ativamente consumindo/recebendo por meio dela.

Como a assinatura não é durável, ela não é persistente. Essa assinatura não recebe mensagens quando não há mais consumidores ativos nela.

Para criar uma assinatura não durável compartilhada, crie uma `JmsConsumer` conforme mostrado nos métodos abaixo por meio da classe `JMSContext` –

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

A assinatura não durável compartilhada continuará existindo enquanto houver consumidores ativos recebendo dela.

### <a name="unshared-non-durable-subscriptions"></a>Assinaturas não duráveis não compartilhadas

Uma assinatura não durável não compartilhada é usada quando o aplicativo cliente precisa receber e processar mensagens por meio de uma assinatura, somente enquanto estiver consumindo ativamente dela. Somente pode existir um consumidor nessa assinatura, ou seja, o cliente que a criou.

Como a assinatura não é durável, ela não é persistente. As mensagens não são recebidas por essa assinatura quando não há nenhum consumidor ativo nela.

Para criar uma assinatura não durável não compartilhada, crie uma `JMSConsumer` conforme mostrado nos métodos abaixo por meio da classe `JMSContext` –

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> Neste momento, o recurso `noLocal` não tem suporte e foi ignorado.
>

A assinatura não durável não compartilhada continuará existindo enquanto houver um consumidor ativo recebendo dela.

### <a name="message-selectors"></a>Seletores de mensagens

Assim como existem os **Filtros e as Ações** para as assinaturas regulares do Barramento de Serviço, existem os **Seletores de Mensagens** para as assinaturas do JMS.

Os seletores de mensagens podem ser configurados em cada uma das assinaturas do JMS e existem como uma condição de filtro nas propriedades do cabeçalho da mensagem. Somente as mensagens com propriedades de cabeçalho correspondentes à expressão do seletor de mensagens são entregues. Um valor nulo ou uma cadeia de caracteres vazia indica que não há seletor de mensagem para o consumidor/a assinatura do JMS.

## <a name="additional-concepts-for-java-message-service-jms-20-subscriptions"></a>Conceitos adicionais para assinaturas do JMS (Serviço de Mensagens do Java) 2.0

### <a name="client-scoping"></a>Escopo do cliente

As assinaturas, conforme especificado na API do JMS (Serviço de Mensagens do Java) 2.0, podem ou não ter o *escopo definido para aplicativos cliente específicos* (identificados com a `clientId` apropriada).

Depois que a assinatura estiver no escopo, ela **só poderá ser acessada** por meio de aplicativos cliente que tenham a mesma ID de cliente. 

Qualquer tentativa de acessar uma assinatura com escopo para uma ID de cliente específica (digamos, clientId1) por meio de um aplicativo com outra ID de cliente (digamos, clientId2) levará à criação de outra assinatura com escopo definido para a outra ID de cliente (clientId2).

> [!NOTE]
> A ID de cliente pode ser nula ou vazia, mas deve corresponder à ID de cliente definida no aplicativo cliente do JMS. Da perspectiva do Barramento de Serviço do Azure, uma ID de cliente nula e uma ID de cliente vazia têm o mesmo comportamento.
>
> Se a ID de cliente for definida como nula ou vazia, ela só poderá ser acessada por aplicativos cliente cuja ID de cliente também tenha sido definida como nula ou vazia.
>

### <a name="shareability"></a>Capacidade de compartilhamento

As assinaturas **compartilhadas** permitem que vários clientes/consumidores (ou seja, objetos do JMSConsumer) recebam mensagens por meio delas.

>[!NOTE]
> As assinaturas compartilhadas com escopo definido para uma ID de cliente específica ainda podem ser acessadas por vários clientes/consumidores (ou seja, objetos do JMSConsumer), mas todos os aplicativos cliente devem ter a mesma ID de cliente.
>
 

Assinaturas **não compartilhadas** permitem que apenas um cliente/consumidor (ou seja, objeto do JMSConsumer) receba mensagens por meio delas. Se um `JMSConsumer` for criado em uma assinatura não compartilhada que já tem uma escuta ativa `JMSConsumer` de mensagens, uma `JMSException` será lançada.


### <a name="durability"></a>Durabilidade

As assinaturas **duráveis** são persistentes e continuam a coletar mensagens do tópico, independentemente se o aplicativo (`JMSConsumer`) está consumindo mensagens por meio delas.

As assinaturas **não duráveis** não são persistentes e coletam mensagens do tópico somente enquanto há um aplicativo (`JMSConsumer`) consumindo mensagens por meio dela. 

## <a name="representation-of-client-scoped-subscriptions"></a>Representação de assinaturas com escopo de cliente

Considerando que as assinaturas (JMS) com escopo do cliente devem coexistir com as assinaturas [existentes](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions), a maneira como as assinaturas com escopo do cliente (JMS) são representadas seguem o formato abaixo.

   * **\<SUBSCRIPTION-NAME\>** $ **\<CLIENT-ID\>** $**D** (para assinaturas duráveis)
   * **\<SUBSCRIPTION-NAME\>** $ **\<CLIENT-ID\>** $**ND** (para assinaturas não duráveis)

Aqui, **$** é o delimitador.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações e exemplos de uso do sistema de mensagens do Barramento de Serviço, consulte os tópicos avançados a seguir:

* [Visão geral de mensagens do Barramento de Serviço](service-bus-messaging-overview.md)
* [Usar a API do Serviço de Mensagens do Java 2.0 com o Barramento de Serviço Premium do Azure](how-to-use-java-message-service-20.md)



