---
title: Usar a API 2,0 do serviço de mensagens Java com o barramento de serviço Premium do Azure
description: Como usar o Java Message Service (JMS) com o barramento de serviço do Azure
ms.topic: article
ms.date: 07/17/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 8363011187a4c2ef77681ece4bb8b1de73ec7a63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87801342"
---
# <a name="use-java-message-service-20-api-with-azure-service-bus-premium-preview"></a>Usar a API 2,0 do serviço de mensagens Java com o barramento de serviço Premium do Azure (visualização)

Este artigo explica como usar a popular API do **Java Message Service (JMS) 2,0** para interagir com o barramento de serviço do Azure por meio do protocolo AMQP 1,0 (protocolo de enfileiramento de mensagens avançado).

> [!NOTE]
> O suporte para a API JMS (Java Message Service) 2,0 só está disponível na **camada Premium do barramento de serviço do Azure** e está em **Visualização**no momento.
>

## <a name="get-started-with-service-bus"></a>Introdução ao Barramento de serviço

Este guia pressupõe que você já tenha um namespace do barramento de serviço. Se não tiver, você poderá [criar o namespace e a fila](service-bus-create-namespace-portal.md) usando o [portal do Azure](https://portal.azure.com). 

Para obter mais informações sobre como criar filas e namespaces do barramento de serviço, consulte Introdução [às filas do barramento de serviço por meio do portal do Azure](service-bus-quickstart-portal.md).

## <a name="what-jms-features-are-supported"></a>Quais recursos JMS têm suporte?

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

## <a name="downloading-the-java-message-service-jms-client-library"></a>Baixando a biblioteca de cliente Java Message Service (JMS)

Para utilizar todos os recursos disponíveis na camada Premium do barramento de serviço do Azure, a biblioteca abaixo deve ser adicionada ao caminho de compilação do projeto.

[Azure-ServiceBus-JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)

> [!NOTE]
> Para adicionar o [Azure-ServiceBus-JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) ao caminho de compilação, utilize a ferramenta de gerenciamento de dependência preferencial para seu projeto, como [Maven](https://maven.apache.org/) ou [gradle](https://gradle.org/).
>

## <a name="coding-java-applications"></a>Codificando os aplicativos Java

Depois que as dependências forem importadas, os aplicativos Java podem ser escritos de maneira independente do provedor JMS.

### <a name="connecting-to-azure-service-bus-using-jms"></a>Conectando-se ao barramento de serviço do Azure usando JMS

Para se conectar com o barramento de serviço do Azure usando clientes JMS, você precisa da **cadeia de conexão** que está disponível nas ' políticas de acesso compartilhado ' no [portal do Azure](https://portal.azure.com) na **cadeia de conexão primária**.

1. Criar uma instância do `ServiceBusJmsConnectionFactorySettings`

    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();
    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. Instancie o `ServiceBusJmsConnectionFactory` com o apropriado `ServiceBusConnectionString` .

    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. Use o `ConnectionFactory` para criar um `Connection` e, em seguida, um `Session` 

    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    ou um `JMSContext` (para clientes JMS 2,0)

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

### <a name="write-the-jms-application"></a>Escrever o aplicativo JMS

Depois que o `Session` ou `JMSContext` tiver sido instanciado, seu aplicativo poderá usar as APIs JMS familiares para executar operações de gerenciamento e de dados.

Consulte a lista de [recursos JMS com suporte](how-to-use-java-message-service-20.md#what-jms-features-are-supported) para ver quais APIs têm suporte como parte desta visualização.

Veja abaixo alguns trechos de código de exemplo para começar a usar JMS-

#### <a name="sending-messages-to-a-queue-and-topic"></a>Enviando mensagens para uma fila e um tópico

```java
// Create the queue and topic
Queue queue = jmsContext.createQueue("basicQueue");
Topic topic = jmsContext.createTopic("basicTopic");
// Create the message
Message msg = jmsContext.createMessage();

// Create the JMS message producer
JMSProducer producer = jmsContext.createProducer();

// send the message to the queue
producer.send(queue, msg);
// send the message to the topic
producer.send(topic, msg);
```

#### <a name="receiving-messages-from-a-queue"></a>Recebendo mensagens de uma fila

```java
// Create the queue
Queue queue = jmsContext.createQueue("basicQueue");

// Create the message consumer
JMSConsumer consumer = jmsContext.createConsumer(queue);

// Receive the message
Message msg = (Message) consumer.receive();
```

#### <a name="receiving-messages-from-a-shared-durable-subscription-on-a-topic"></a>Recebendo mensagens de uma assinatura durável compartilhada em um tópico

```java
// Create the topic
Topic topic = jmsContext.createTopic("basicTopic");

// Create a shared durable subscriber on the topic
JMSConsumer sharedDurableConsumer = jmsContext.createSharedDurableConsumer(topic, "sharedDurableConsumer");

// Receive the message
Message msg = (Message) sharedDurableConsumer.receive();
```

## <a name="summary"></a>Resumo

Este guia demonstra como os aplicativos cliente Java que usam o Java Message Service (JMS) sobre o AMQP 1,0 podem interagir com o barramento de serviço do Azure.

Você também pode usar o AMQP 1.0 do Service Bus de outras linguagens, incluindo .NET, C, Python e PHP. Os componentes criados com essas diferentes linguagens podem trocar mensagens de forma confiável e com total fidelidade usando o suporte do AMQP 1.0 no Service Bus.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o barramento de serviço do Azure e detalhes sobre as entidades de Java Message Service (JMS), confira os links abaixo- 
* [Barramento de serviço-filas, tópicos e assinaturas](service-bus-queues-topics-subscriptions.md)
* [Barramento de serviço-entidades de serviço de mensagem Java](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities-preview)
* [Suporte para o AMQP 1.0 no Barramento de Serviço do Azure](service-bus-amqp-overview.md)
* [Guia do Desenvolvedor do AMQP 1.0 do Barramento de Serviço](service-bus-amqp-dotnet.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [API do serviço de mensagens Java (documento Oracle externo)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [Saiba como migrar do ActiveMQ para o barramento de serviço](migrate-jms-activemq-to-servicebus.md)
