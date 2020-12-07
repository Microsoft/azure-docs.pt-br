---
title: Usar tópicos e assinaturas do Barramento de Serviço do Azure com Java (azure-messaging-servicebus)
description: Neste guia de início rápido, você escreverá um código Java usando o pacote azure-messaging-servicebus para enviar mensagens para um tópico do Barramento de Serviço do Azure e receber mensagens de assinaturas para esse tópico.
ms.devlang: Java
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: 011dbfef1155b82daca216e9519db07188260130
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489540"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-java"></a>Enviar mensagens para um tópico do Barramento de Serviço do Azure e receber mensagens de assinaturas para o tópico (Java)
Neste guia de início rápido, você escreverá um código Java usando o pacote azure-messaging-servicebus para enviar mensagens para um tópico do Barramento de Serviço do Azure e receber mensagens de assinaturas para esse tópico.

> [!IMPORTANT]
> Este início rápido usa o novo pacote azure-messaging-servicebus. Para ver um início rápido que usa o antigo pacote azure-servicebus, confira [Enviar e receber mensagens usando azure-servicebus](service-bus-java-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Para concluir este tutorial, você precisa de uma conta do Azure. Ative seus [benefícios de assinante do Visual Studio ou do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscreva-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Siga as etapas no [Início Rápido: Usar o portal do Azure para criar um tópico do Barramento de Serviço e assinaturas para o tópico](service-bus-quickstart-topics-subscriptions-portal.md). Anote a cadeia de conexão, o nome do tópico e o nome de uma assinatura. Você usará apenas uma assinatura neste guia de início rápido. 
- Instalar o [SDK do Azure para Java][Azure SDK for Java]. Se estiver usando o Eclipse, instale o [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse], que inclui o SDK do Azure para Java. Você pode adicionar as **Bibliotecas do Microsoft Azure para Java** ao seu projeto. Se estiver usando o IntelliJ, confira [Instalar o Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Nesta seção, você criará um projeto de console Java e adicionará um código para enviar mensagens para o tópico criado. 

### <a name="create-a-java-console-project"></a>Criar um projeto de console Java
Crie um projeto Java usando o Eclipse ou uma ferramenta de sua preferência. 

### <a name="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para usar o Barramento de serviço
Adicione uma referência à biblioteca do Barramento de Serviço do Azure. A biblioteca de clientes Java para o Barramento de Serviço está disponível no [Repositório Central do Maven](https://search.maven.org/search?q=a:azure-messaging-servicebus). Você pode fazer referência a essa biblioteca usando a seguinte declaração de dependência em seu arquivo do projeto Maven:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-servicebus</artifactId>
    <version>7.0.0</version>
</dependency>
```

### <a name="add-code-to-send-messages-to-the-topic"></a>Adicionar um código para enviar mensagens para o tópico
1. Adicione as instruções `import` a seguir ao tópico do arquivo Java. 

    ```java
    import com.azure.messaging.servicebus.*;
    import com.azure.messaging.servicebus.models.*;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. Na classe, defina variáveis para armazenar a cadeia de conexão e o nome do tópico, conforme mostrado abaixo: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String topicName = "<TOPIC NAME>";    
    static String subName = "<SUBSCRIPTION NAME>";
    ```

    Substitua `<NAMESPACE CONNECTION STRING>` pela cadeia de conexão para o namespace do Barramento de Serviço. Além disso, substitua `<TOPIC NAME>` pelo nome do tópico.
3. Adicione um método chamado `sendMessage` à classe para enviar uma mensagem ao tópico. 

    ```java
        static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
                .buildClient();
        
        // send one message to the topic
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the topic: " + topicName);        
    }
    ```
1. Adicione um método chamado `createMessages` à classe para criar uma lista de mensagens. Normalmente, você obtém essas mensagens de diferentes partes do seu aplicativo. Aqui, criamos uma lista de mensagens de exemplo.

    ```java
    static List<ServiceBusMessage> createMessages()
    {
        // create a list of messages and return it to the caller
        ServiceBusMessage[] messages = {
                new ServiceBusMessage("First message"),
                new ServiceBusMessage("Second message"),
                new ServiceBusMessage("Third message")
        };
        return Arrays.asList(messages);
    }
    ```
1. Adicione um método chamado `sendMessageBatch` para enviar mensagens ao tópico criado. Esse método cria uma `ServiceBusSenderClient` para o tópico, invoca o método `createMessages` para obter a lista de mensagens, prepara um ou mais lotes e envia os lotes ao tópico. 

```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the topic 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
                .buildClient();

        // Creates an ServiceBusMessageBatch where the ServiceBus.
        ServiceBusMessageBatch messageBatch = senderClient.createMessageBatch();        
        
        // create a list of messages
        List<ServiceBusMessage> listOfMessages = createMessages();
        
        // We try to add as many messages as a batch can fit based on the maximum size and send to Service Bus when
        // the batch can hold no more messages. Create a new batch for next set of messages and repeat until all
        // messages are sent.        
        for (ServiceBusMessage message : listOfMessages) {
            if (messageBatch.tryAddMessage(message)) {
                continue;
            }

            // The batch is full, so we create a new batch and send the batch.
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the topic: " + topicName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }

        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the topic: " + topicName);
        }

        //close the client
        senderClient.close();
    }
```

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura
Nesta seção, você adicionará o código para recuperar mensagens de uma assinatura para o tópico. 

1. Adicione um método chamado `receiveMessages` para receber mensagens da assinatura. Esse método cria um `ServiceBusProcessorClient` para a assinatura especificando um manipulador para o processamento de mensagens e outro para o tratamento de erros. Em seguida, ele inicia o processador, aguarda alguns segundos, imprime as mensagens recebidas e, por fim, interrompe e fecha o processador.

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        // Consumer that processes a single message received from Service Bus
        Consumer<ServiceBusReceivedMessageContext> messageProcessor = context -> {
            ServiceBusReceivedMessage message = context.getMessage();
            System.out.println("Received message: " + message.getBody().toString() + " from the subscription: " + subName);
        };

        // Consumer that handles any errors that occur when receiving messages
        Consumer<Throwable> errorHandler = throwable -> {
            System.out.println("Error when receiving messages: " + throwable.getMessage());
            if (throwable instanceof ServiceBusReceiverException) {
                ServiceBusReceiverException serviceBusReceiverException = (ServiceBusReceiverException) throwable;
                System.out.println("Error source: " + serviceBusReceiverException.getErrorSource());
            }
        };

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .topicName(topicName)
            .subscriptionName(subName)
            .processMessage(messageProcessor)
            .processError(errorHandler)
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }
    ```
2. Atualize o método `main` para invocar os métodos `sendMessage`, `sendMessageBatch` e `receiveMessages` e gerar `InterruptedException`.     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>Executar o aplicativo
Execute o programa para ver a saída semelhante à seguinte saída:

```console
Sent a batch of messages to the topic: mytopic
Starting the processor
Received message: First message from the subscription: mysub
Received message: Second message from the subscription: mysub
Received message: Third message from the subscription: mysub
Stopping and closing the processor
```

Na página **Visão geral** do namespace do Barramento de Serviço no portal do Azure, você poderá ver a contagem de mensagens de **entrada** e **saída**. Talvez seja necessário aguardar alguns minutos e atualizar a página para ver os valores mais recentes. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Contagem de mensagens de entrada e saída" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Alterne para a guia **Tópicos** no painel da metade inferior e selecione o tópico para ver a página **Tópico do Barramento de Serviço** do tópico. Nessa página, você verá quatro mensagens de entrada e quatro mensagens de saída no gráfico **Mensagens**. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Mensagens de entrada e saída" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png":::

Se você comentar a chamada `receiveMessages` no método `main` e executar o aplicativo novamente, na página **Tópico do Barramento de Serviço**, verá oito mensagens de entrada (quatro novas), mas quatro mensagens de saída. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Página do tópico atualizada" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png":::

Nessa página, se você selecionar uma assinatura, chegará à página **Assinatura do Barramento de Serviço**. Você poderá ver a contagem de mensagens ativas, a contagem de mensagens mortas, entre outros nessa página. Neste exemplo, há quatro mensagens ativas que ainda não foram recebidas por um destinatário. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Contagem de mensagens ativas" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png":::

## <a name="next-steps"></a>Próximas etapas
Confira os seguintes exemplos e a documentação:

- [Biblioteca de clientes do Barramento de Serviço do Azure para Java – Leiame](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Exemplos no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/servicebus/azure-messaging-servicebus/src/samples)
- [Referência da API Java](/java/api/overview/azure/servicebus?preserve-view=true&view=azure-java-preview)


[Azure SDK for Java]: /java/api/overview/azure/
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage