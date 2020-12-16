---
title: Usar filas do Barramento de Serviço do Azure com o Java (azure-messaging-servicebus)
description: Neste tutorial, você aprenderá a usar o Java para enviar e receber mensagens em uma fila do Barramento de Serviço do Azure. Você usará o novo pacote azure-messaging-servicebus.
ms.devlang: Java
ms.topic: quickstart
ms.date: 11/09/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 4eae72b38d36348a6bdc98d3cbd98a5a712b3161
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97399291"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-java"></a>Enviar e receber mensagens em filas do Barramento de Serviço do Azure (Java)
Neste guia de início rápido, você criará um aplicativo Java para enviar e receber mensagens em uma fila do Barramento de Serviço do Azure. 

> [!IMPORTANT]
> Este início rápido usa o novo pacote azure-messaging-servicebus. Para ver um início rápido que usa o antigo pacote azure-servicebus, confira [Enviar e receber mensagens usando azure-servicebus](service-bus-java-how-to-use-queues-legacy.md).


## <a name="prerequisites"></a>Pré-requisitos
- Uma assinatura do Azure. Para concluir este tutorial, você precisa de uma conta do Azure. Ative seus [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscreva-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Caso você não tenha uma fila para trabalhar, siga as etapas do artigo [Usar o portal do Azure para criar uma fila do Barramento de Serviço](service-bus-quickstart-portal.md) para criar uma fila. Anote a **cadeia de conexão** do namespace do Barramento de Serviço e o nome da **fila** que você criou.
- Instalar o [SDK do Azure para Java][Azure SDK for Java]. Se estiver usando o Eclipse, instale o [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse], que inclui o SDK do Azure para Java. Você pode adicionar as **Bibliotecas do Microsoft Azure para Java** ao seu projeto. Se estiver usando o IntelliJ, confira [Instalar o Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-queue"></a>Enviar mensagens a uma fila
Nesta seção, você criará um projeto de console Java e adicionará um código para enviar mensagens à fila criada anteriormente. 

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

### <a name="add-code-to-send-messages-to-the-queue"></a>Adicionar o código para enviar mensagens à fila
1. Adicione as instruções `import` a seguir ao tópico do arquivo Java. 

    ```java
    import com.azure.messaging.servicebus.*;
    import com.azure.messaging.servicebus.models.*;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. Na classe, defina variáveis para armazenar a cadeia de conexão e o nome da fila, conforme mostrado abaixo: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String queueName = "<QUEUE NAME>";    
    ```

    Substitua `<NAMESPACE CONNECTION STRING>` pela cadeia de conexão para o namespace do Barramento de Serviço. Além disso, substitua `<QUEUE NAME>` pelo nome da fila.
3. Adicione um método chamado `sendMessage` à classe para enviar uma mensagem à fila. 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();
        
        // send one message to the queue
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the queue: " + queueName);        
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
1. Adicione um método chamado `sendMessageBatch` para enviar mensagens à fila criada. Esse método cria uma `ServiceBusSenderClient` para a fila, invoca o método `createMessages` para obter a lista de mensagens, prepara um ou mais lotes e envia os lotes à fila. 

```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
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
            System.out.println("Sent a batch of messages to the queue: " + queueName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }
        
        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
        }

        //close the client
        senderClient.close();
    }
```

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
Nesta seção, você adicionará o código para recuperar mensagens da fila. 

1. Adicione um método chamado `receiveMessages` para receber mensagens da fila. Esse método cria um `ServiceBusProcessorClient` para a fila especificando um manipulador para o processamento de mensagens e outro para o tratamento de erros. Em seguida, ele inicia o processador, aguarda alguns segundos, imprime as mensagens recebidas e, por fim, interrompe e fecha o processador.

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        // consumer that processes a single message received from Service Bus
        Consumer<ServiceBusReceivedMessageContext> messageProcessor = context -> {
            ServiceBusReceivedMessage message = context.getMessage();
            System.out.println("Received message: " + message.getBody().toString());
        };

        // handles any errors that occur when receiving messages
        Consumer<Throwable> errorHandler = throwable -> {
            System.out.println("Error when receiving messages: " + throwable.getMessage());
            if (throwable instanceof ServiceBusReceiverException) {
                ServiceBusReceiverException serviceBusReceiverException = (ServiceBusReceiverException) throwable;
                System.out.println("Error source: " + serviceBusReceiverException.getErrorSource());
            }
        };

        // create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .queueName(queueName)
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
Ao executar o aplicativo, você verá as mensagens a seguir na janela do console. 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Starting the processor
Received message: Hello, World!
Received message: First message in the batch
Received message: Second message in the batch
Received message: Three message in the batch
Stopping and closing the processor
```

Na página **Visão geral** do namespace do Barramento de Serviço no portal do Azure, você poderá ver a contagem de mensagens de **entrada** e **saída**. Talvez seja necessário aguardar alguns minutos e atualizar a página para ver os valores mais recentes. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Contagem de mensagens de entrada e saída" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Selecione a fila nesta página **Visão geral** para acessar a página **Fila do Barramento de Serviço**. Você também verá a contagem de mensagens de **entrada** e **saída** nessa página. Além disso, verá outras informações, como o **tamanho atual** da fila, o **tamanho máximo**, a **contagem de mensagens ativas** etc. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Detalhes da fila" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>Próximas etapas
Confira os seguintes exemplos e a documentação:

- [Biblioteca de clientes do Barramento de Serviço do Azure para Java – Leiame](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Exemplos no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/servicebus/azure-messaging-servicebus/src/samples)
- [Referência da API Java](/java/api/overview/azure/servicebus?preserve-view=true&view=azure-java-stable)

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
