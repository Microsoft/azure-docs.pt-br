---
title: Usar filas do Barramento de Serviço do Azure com o Java (azure-messaging-servicebus)
description: Neste tutorial, você aprenderá a usar o Java para enviar e receber mensagens em uma fila do Barramento de Serviço do Azure. Você usará o novo pacote azure-messaging-servicebus.
ms.date: 02/13/2021
ms.topic: quickstart
ms.devlang: Java
ms.custom:
- seo-java-july2019
- seo-java-august2019
- seo-java-september2019
- devx-track-java
- mode-api
ms.openlocfilehash: e3998e812bb921ff8bea5e83199bc4e142ed2e83
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533419"
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
Adicione referências às bibliotecas do Azure Core e do Barramento de Serviço do Azure. 

Se você estiver usando o Eclipse e criado um aplicativo de console Java, converta seu projeto Java em um Maven: clique com o botão direito do mouse no projeto na janela **Explorador de Pacotes**, selecione **Configurar** -> **Converter para o projeto Maven**. Em seguida, adicione dependências a essas duas bibliotecas, conforme mostrado no exemplo a seguir.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.myorg.sbusquickstarts</groupId>
    <artifactId>sbustopicqs</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <build>
        <sourceDirectory>src</sourceDirectory>
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <release>15</release>
                </configuration>
            </plugin>
        </plugins>
    </build>
    <dependencies>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-core</artifactId>
            <version>1.13.0</version>
        </dependency>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-messaging-servicebus</artifactId>
            <version>7.0.2</version>
        </dependency>
    </dependencies>
</project>
```

### <a name="add-code-to-send-messages-to-the-queue"></a>Adicionar o código para enviar mensagens à fila
1. Adicione as instruções `import` a seguir ao tópico do arquivo Java. 

    ```java
    import com.azure.messaging.servicebus.*;
    
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.TimeUnit;
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

    > [!IMPORTANT]
    > Substitua `QueueTest` em `QueueTest::processMessage` no código pelo nome da sua classe. 

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        CountDownLatch countdownLatch = new CountDownLatch(1);

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .queueName(queueName)
            .processMessage(QueueTest::processMessage)
            .processError(context -> processError(context, countdownLatch))
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }   
    ```
2. Adicione o método `processMessage` para processar uma mensagem recebida da assinatura do Barramento de Serviço. 

    ```java
    private static void processMessage(ServiceBusReceivedMessageContext context) {
        ServiceBusReceivedMessage message = context.getMessage();
        System.out.printf("Processing message. Session: %s, Sequence #: %s. Contents: %s%n", message.getMessageId(),
            message.getSequenceNumber(), message.getBody());
    }    
    ```
3. Adicione o método `processError` para manipular mensagens de erro.

    ```java
    private static void processError(ServiceBusErrorContext context, CountDownLatch countdownLatch) {
        System.out.printf("Error when receiving messages from namespace: '%s'. Entity: '%s'%n",
            context.getFullyQualifiedNamespace(), context.getEntityPath());

        if (!(context.getException() instanceof ServiceBusException)) {
            System.out.printf("Non-ServiceBusException occurred: %s%n", context.getException());
            return;
        }

        ServiceBusException exception = (ServiceBusException) context.getException();
        ServiceBusFailureReason reason = exception.getReason();

        if (reason == ServiceBusFailureReason.MESSAGING_ENTITY_DISABLED
            || reason == ServiceBusFailureReason.MESSAGING_ENTITY_NOT_FOUND
            || reason == ServiceBusFailureReason.UNAUTHORIZED) {
            System.out.printf("An unrecoverable error occurred. Stopping processing with reason %s: %s%n",
                reason, exception.getMessage());

            countdownLatch.countDown();
        } else if (reason == ServiceBusFailureReason.MESSAGE_LOCK_LOST) {
            System.out.printf("Message lock lost for message: %s%n", context.getException());
        } else if (reason == ServiceBusFailureReason.SERVICE_BUSY) {
            try {
                // Choosing an arbitrary amount of time to wait until trying again.
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                System.err.println("Unable to sleep for period of time");
            }
        } else {
            System.out.printf("Error source %s, reason %s, message: %s%n", context.getErrorSource(),
                reason, context.getException());
        }
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
Processing message. Session: 88d961dd801f449e9c3e0f8a5393a527, Sequence #: 1. Contents: Hello, World!
Processing message. Session: e90c8d9039ce403bbe1d0ec7038033a0, Sequence #: 2. Contents: First message
Processing message. Session: 311a216a560c47d184f9831984e6ac1d, Sequence #: 3. Contents: Second message
Processing message. Session: f9a871be07414baf9505f2c3d466c4ab, Sequence #: 4. Contents: Third message
Stopping and closing the processor
```

Na página **Visão geral** do namespace do Barramento de Serviço no portal do Azure, você poderá ver a contagem de mensagens de **entrada** e **saída**. Talvez seja necessário aguardar alguns minutos e atualizar a página para ver os valores mais recentes. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Contagem de mensagens de entrada e saída" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Selecione a fila nesta página **Visão geral** para acessar a página **Fila do Barramento de Serviço**. Você também verá a contagem de mensagens de **entrada** e **saída** nessa página. Além disso, verá outras informações, como o **tamanho atual** da fila, o **tamanho máximo**, a **contagem de mensagens ativas** etc. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Detalhes da fila" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>Próximas etapas
Confira os seguintes exemplos e a documentação:

- [Biblioteca de clientes do Barramento de Serviço do Azure para Java – Leiame](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Exemplos no GitHub](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Referência da API Java](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)

[Azure SDK for Java]: /azure/developer/java/sdk/get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
