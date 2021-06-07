---
title: Guia do desenvolvedor de JMS 2,0 do barramento de serviço do Azure
description: Como usar a API do Java Message Service (JMS) 2,0 para se comunicar com o barramento de serviço do Azure
ms.topic: article
ms.date: 01/17/2021
ms.openlocfilehash: 492da46a5ce4f5955a72972d91a8c56d54c385bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726947"
---
# <a name="azure-service-bus-jms-20-developer-guide"></a>Guia do desenvolvedor de JMS 2,0 do barramento de serviço do Azure

Este guia contém informações detalhadas para ajudá-lo a ter sucesso na comunicação com o barramento de serviço do Azure usando a API do Java Message Service (JMS) 2,0.

Como desenvolvedor de Java, se você for novo no barramento de serviço do Azure, considere ler os artigos a seguir.

| Introdução | Conceitos |
|----------------|-------|
| <ul> <li> [O que é o barramento de serviço do Azure](service-bus-messaging-overview.md) </li> <li> [Filas, tópicos e assinaturas](service-bus-queues-topics-subscriptions.md) </li> </ul> | <ul> <li> [Barramento de serviço do Azure-camada Premium](service-bus-premium-messaging.md) </li> </ul> |

## <a name="java-message-service-jms-programming-model"></a>Modelo de programação de Java Message Service (JMS)

O modelo de programação da API do serviço de mensagens Java é conforme mostrado abaixo- 

> [!NOTE]
>
>A **camada Premium do barramento de serviço do Azure** dá suporte a JMS 1,1 e JMS 2,0.
> <br> <br>
> **Barramento de serviço do Azure-a camada Standard** dá suporte à funcionalidade JMS 1,1 limitada. Para obter mais detalhes, consulte esta [documentação](service-bus-java-how-to-use-jms-api-amqp.md).
>

# <a name="jms-20-programming-model"></a>[Modelo de programação JMS 2,0](#tab/JMS-20)

:::image type="content" source="./media/jms-developer-guide/java-message-service-20-programming-model.png "alt-text="Diagrama mostrando o modelo de programação JMS 2,0." border="false":::

# <a name="jms-11-programming-model"></a>[Modelo de programação JMS 1,1](#tab/JMS-11)

:::image type="content" source="./media/jms-developer-guide/java-message-service-11-programming-model.png "alt-text="Diagrama mostrando o modelo de programação JMS 1,1." border="false":::

---

## <a name="jms---building-blocks"></a>Blocos de construção JMS

Os blocos de construção abaixo estão disponíveis para comunicação com o aplicativo JMS.

> [!NOTE]
> O guia a seguir foi adaptado do [tutorial do Oracle Java EE 6 para Java Message Service (JMS)](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html)
>
> É recomendável referir-se a este tutorial para melhor compreensão do Java Message Service (JMS).
>

### <a name="connection-factory"></a>Fábrica de conexões
O objeto de fábrica de conexão é usado pelo cliente para se conectar ao provedor JMS. A fábrica de conexão encapsula um conjunto de parâmetros de configuração de conexão que são definidos pelo administrador.

Cada fábrica de conexão é uma instância `ConnectionFactory` do `QueueConnectionFactory` ou `TopicConnectionFactory` interface.

Para simplificar a conexão com o barramento de serviço do Azure, essas interfaces são implementadas por meio do `ServiceBusJmsConnectionFactory` `ServiceBusJmsQueueConnectionFactory` e, `ServiceBusJmsTopicConnectionFactory` respectivamente. A fábrica de conexão pode ser instanciada com os parâmetros abaixo- 
   * Cadeia de conexão-a cadeia de conexão para o namespace da camada Premium do barramento de serviço do Azure.
   * Recipiente de propriedades ServiceBusJmsConnectionFactorySettings que contém
      * connectionIdleTimeoutMS-tempo limite de conexão ocioso em milissegundos.
      * sinalizador traceFrames-booliano para coletar quadros de rastreamento AMQP para depuração.
      * *outros parâmetros de configuração*

A fábrica pode ser criada como abaixo. A cadeia de conexão é um parâmetro obrigatório, mas as propriedades adicionais são opcionais.

```java
ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, null);
```

> [!IMPORTANT]
> Aplicativos Java que aproveitam a API JMS 2,0 devem se conectar ao barramento de serviço do Azure usando apenas a cadeia de conexão. Atualmente, a autenticação para clientes JMS só tem suporte usando a cadeia de conexão.
>
> A autenticação com suporte do AAD (Azure Active Directory) não é suportada atualmente.
>

### <a name="jms-destination"></a>Destino JMS

Um destino é o objeto que um cliente usa para especificar o destino das mensagens que ele produz e a origem das mensagens que consome.

Os destinos são mapeados para entidades no barramento de serviço do Azure – filas (em cenários ponto a ponto) e tópicos (em cenários pub-sub).

### <a name="connections"></a>conexões

Uma conexão encapsula uma conexão virtual com um provedor JMS. Com o barramento de serviço do Azure, isso representa uma conexão com estado entre o aplicativo e o barramento de serviço do Azure em AMQP.

Uma conexão é criada a partir da fábrica de conexões, conforme mostrado abaixo.

```java
Connection connection = factory.createConnection();
```

### <a name="sessions"></a>Sessões

Uma sessão é um contexto de thread único para produzir e consumir mensagens. Ele pode ser utilizado para criar mensagens, produtores de mensagens e consumidores, mas também fornece um contexto transacional para permitir o agrupamento de envios e recebimentos em uma unidade de trabalho atômica.

Uma sessão pode ser criada a partir do objeto de conexão, conforme mostrado abaixo.

```java
Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
```

#### <a name="session-modes"></a>Modos de sessão

Uma sessão pode ser criada com qualquer um dos modos abaixo.

| Modos de sessão | Comportamento |
| ----------------- | -------- |
|**Session.AUTO_ACKNOWLEDGE** | A sessão confirma automaticamente o recebimento de uma mensagem do cliente quando a sessão é retornada com êxito de uma chamada para Receive ou quando o ouvinte de mensagem que a sessão chamou para processar a mensagem é retornado com êxito.|
|**Session.CLIENT_ACKNOWLEDGE** |O cliente reconhece uma mensagem consumida chamando o método de confirmação da mensagem.|
|**Session.DUPS_OK_ACKNOWLEDGE**|Esse modo de confirmação instrui a sessão a reconhecer lentamente a entrega de mensagens.| 
|**Session.SESSION_TRANSACTED**|Esse valor pode ser passado como o argumento para o método CreateSession (int SessionMode) no objeto de conexão para especificar que a sessão deve usar uma transação local.| 

Quando o modo de sessão não é especificado, o **Session.AUTO_ACKNOWLEDGE** é escolhido por padrão.

### <a name="jmscontext"></a>JMSContext

> [!NOTE]
> JMSContext é definido como parte da especificação JMS 2,0.
>

JMSContext combina a funcionalidade fornecida pelo objeto de conexão e de sessão. Ele pode ser criado a partir do objeto de fábrica de conexão.

```java
JMSContext context = connectionFactory.createContext();
```

#### <a name="jmscontext-modes"></a>Modos de JMSContext

Assim como o objeto **Session** , o JMSContext pode ser criado com os mesmos modos de reconhecimento, conforme mencionado nos [modos de sessão](#session-modes).

```java
JMSContext context = connectionFactory.createContext(JMSContext.AUTO_ACKNOWLEDGE);
```

Quando o modo não é especificado, o **JMSContext.AUTO_ACKNOWLEDGE** é escolhido por padrão.

### <a name="jms-message-producers"></a>Produtores de mensagem JMS

Um produtor de mensagem é um objeto criado usando um JMSContext ou uma sessão e usado para enviar mensagens para um destino.

Ele pode ser criado como um objeto autônomo como abaixo- 

```java
JMSProducer producer = context.createProducer();
```

ou criado em tempo de execução quando uma mensagem é necessária para ser enviada.

```java
context.createProducer().send(destination, message);
```

### <a name="jms-message-consumers"></a>Consumidores de mensagens JMS

Um consumidor de mensagens é um objeto que é criado por um JMSContext ou uma sessão e usado para receber mensagens enviadas a um destino. Ele pode ser criado conforme mostrado abaixo-

```java
JMSConsumer consumer = context.createConsumer(dest);
```

#### <a name="synchronous-receives-via-receive-method"></a>Recebimentos síncronos por meio do método Receive ()

O consumidor da mensagem fornece uma maneira síncrona de receber mensagens do destino por meio do `receive()` método.

Se nenhum argumento/tempo limite for especificado ou um tempo limite de ' 0 ' for especificado, o consumidor será bloqueado indefinidamente, a menos que a mensagem chegue ou a conexão seja interrompida (o que for anterior).

```java
Message m = consumer.receive();
Message m = consumer.receive(0);
```

Quando um argumento positivo diferente de zero é fornecido, o consumidor é bloqueado até que o temporizador expire.

```java
Message m = consumer.receive(1000); // time out after one second.
```

#### <a name="asynchronous-receives-with-jms-message-listeners"></a>Recebimentos assíncronos com ouvintes de mensagem JMS

Um ouvinte de mensagem é um objeto usado para manipulação assíncrona de mensagens em um destino. Ele implementa a `MessageListener` interface que contém o `onMessage` método em que a lógica de negócios específica deve residir.

Um objeto de ouvinte de mensagem deve ser instanciado e registrado em um consumidor de mensagem específico usando o `setMessageListener` método.

```java
Listener myListener = new Listener();
consumer.setMessageListener(myListener);
```

### <a name="consuming-from-topics"></a>Consumindo de tópicos

Os [consumidores de mensagens JMS](#jms-message-consumers) são criados em um [destino](#jms-destination) que pode ser uma fila ou um tópico.

Os consumidores nas filas são simplesmente objetos do lado do cliente que residem no contexto da sessão (e conexão) entre o aplicativo cliente e o barramento de serviço do Azure.

No entanto, os consumidores de tópicos têm duas partes 
   * Um **objeto do lado do cliente** que reside no contexto da sessão (ou JMSContext) e,
   * Uma **assinatura** que é uma entidade no barramento de serviço do Azure.

As assinaturas estão documentadas [aqui](java-message-service-20-entities.md#java-message-service-jms-subscriptions) e podem ser uma das seguintes: 
   * Assinaturas duráveis compartilhadas
   * Assinaturas não duráveis compartilhadas
   * Assinaturas duráveis não compartilhadas
   * Assinaturas não duráveis não compartilhadas

### <a name="jms-queue-browsers"></a>Navegadores de fila JMS

A API JMS fornece um `QueueBrowser` objeto que permite que o aplicativo procure as mensagens na fila e exiba os valores de cabeçalho para cada mensagem.

Um navegador de fila pode ser criado usando o JMSContext como mostrado abaixo.

```java
QueueBrowser browser = context.createBrowser(queue);
```

> [!NOTE]
> A API JMS não fornece uma API para procurar um tópico.
>
> Isso ocorre porque o próprio tópico não armazena as mensagens. Assim que a mensagem é enviada para o tópico, ela é encaminhada para as assinaturas apropriadas.
>

### <a name="jms-message-selectors"></a>Seletores de mensagem JMS

Os seletores de mensagem podem ser usados pelo recebimento de aplicativos para filtrar as mensagens recebidas. Com os seletores de mensagem, o aplicativo de recebimento descarrega o trabalho de filtragem de mensagens para o provedor JMS (nesse caso, o barramento de serviço do Azure) em vez de assumir a responsabilidade propriamente dita.

Os seletores podem ser utilizados ao criar qualquer um dos consumidores abaixo- 
   * Assinatura durável compartilhada
   * Assinatura durável não compartilhada
   * Assinatura não durável compartilhada
   * Assinatura não durável não compartilhada
   * Navegador de filas


## <a name="summary"></a>Resumo

Este guia do desenvolvedor demonstra como os aplicativos cliente Java que usam o Java Message Service (JMS) podem se conectar com o barramento de serviço do Azure.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o barramento de serviço do Azure e detalhes sobre as entidades de Java Message Service (JMS), confira os links abaixo- 
* [Barramento de serviço-filas, tópicos e assinaturas](service-bus-queues-topics-subscriptions.md)
* [Barramento de serviço-entidades de serviço de mensagem Java](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [Suporte para o AMQP 1.0 no Barramento de Serviço do Azure](service-bus-amqp-overview.md)
* [Guia do Desenvolvedor do AMQP 1.0 do Barramento de Serviço](service-bus-amqp-dotnet.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [API do serviço de mensagens Java (documento Oracle externo)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [Saiba como migrar do ActiveMQ para o barramento de serviço](migrate-jms-activemq-to-servicebus.md)
