---
title: Enviar ou receber eventos de Hubs de Eventos do Azure usando o Java (mais recente)
description: Este artigo fornece um passo a passo de como criar um aplicativo Java que envia/recebe eventos para/de Hubs de Eventos do Azure usando o pacote azure-messaging-eventhubs mais recente.
ms.topic: quickstart
ms.date: 04/05/2021
ms.custom: devx-track-java
ms.openlocfilehash: bae0d4147fddf57398d494ca7f13c347f892e45e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448433"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Usar o Java para enviar eventos ou receber eventos dos Hubs de Eventos do Azure (azure-messaging-eventhubs)
Este início rápido mostra como enviar e receber eventos de um hub de eventos usando o pacote Java **azure-messaging-eventhubs**.

> [!IMPORTANT]
> Este início rápido usa o novo pacote **azure-messaging-eventhubs**. Para um início rápido que usa os pacotes antigos **azure-eventhubs** e **azure-eventhubs-eph**, confira [Enviar e receber eventos usando o azure-eventhubs e o azure-eventhubs-eph](event-hubs-java-get-started-send-legacy.md). 


## <a name="prerequisites"></a>Pré-requisitos
Se você estiver conhecendo agora os Hubs de Eventos do Azure, confira [Visão geral dos Hubs de Eventos](event-hubs-about.md) antes de prosseguir com este início rápido. 

Para concluir este início rápido, você precisará dos seguintes pré-requisitos:

- **Assinatura do Microsoft Azure**. Para usar os serviços do Azure, incluindo os Hubs de Eventos do Azure, você precisa ter uma assinatura.  Caso não tenha uma conta existente do Azure, inscreva-se em uma [avaliação gratuita](https://azure.microsoft.com/free/) ou use os benefícios do assinante do MSDN quando [criar uma conta](https://azure.microsoft.com).
- Um ambiente de desenvolvimento Java. Este guia de início rápido usa [Eclipse](https://www.eclipse.org/). É necessário um JDK (Java Development Kit) com a versão 8 ou superior. 
- **Criar um namespace de Hubs de Eventos e um hub de eventos**. A primeira etapa é usar o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo Hubs de eventos e obter as credenciais de gerenciamento das quais que seu aplicativo precisa para se comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento [nesse artigo](event-hubs-create.md). Em seguida, obtenha a **cadeia de conexão para o namespace dos Hubs de Eventos** seguindo as instruções do artigo: [Obter a cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Você usa a cadeia de conexão posteriormente no início rápido.

## <a name="send-events"></a>Enviar eventos 
Esta seção mostra como criar um aplicativo Java para enviar eventos para um hub de eventos. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Adicionar a referência à biblioteca de Hubs de Eventos do Azure

A biblioteca de clientes Java para os Hubs de Eventos está disponível no [Repositório Central do Maven](https://search.maven.org/search?q=a:azure-messaging-eventhubs). Você pode fazer referência a essa biblioteca usando a seguinte declaração de dependência em seu arquivo do projeto Maven:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.6.0</version>
</dependency>
```

> [!NOTE]
> Atualize a versão para a última versão publicada no repositório do Maven. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Escrever código para enviar mensagens ao hub de eventos

Para o exemplo a seguir, primeiro crie um novo projeto do Maven para um aplicativo de console/shell em seu ambiente de desenvolvimento Java favorito. Adicione uma classe chamada `Sender` e adicione o seguinte código à classe:

> [!IMPORTANT]
> Atualize `<Event Hubs namespace connection string>` com a cadeia de conexão do namespace dos Hubs de Eventos. Atualize `<Event hub name>` com o nome do hub de eventos no namespace. 

```java
import com.azure.messaging.eventhubs.*;
import java.util.Arrays;
import java.util.List;

public class Sender {
    private static final String connectionString = "<Event Hubs namespace connection string>";
    private static final String eventHubName = "<Event hub name>";

    public static void main(String[] args) {
        publishEvents();
    }
}
```
### <a name="add-code-to-publish-events-to-the-event-hub"></a>Adicionar o código para publicar eventos no hub de eventos
Adicione um método chamado `publishEvents` à classe `Sender`, conforme mostrado abaixo. 

```java
    /**
     * Code sample for publishing events.
     * @throws IllegalArgumentException if the event data is bigger than max batch size.
     */
    public static void publishEvents() {
        // create a producer client
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // sample events in an array
        List<EventData> allEvents = Arrays.asList(new EventData("Foo"), new EventData("Bar"));
        
        // create a batch
        EventDataBatch eventDataBatch = producer.createBatch();

        for (EventData eventData : allEvents) {
            // try to add the event from the array to the batch
            if (!eventDataBatch.tryAdd(eventData)) {
                // if the batch is full, send it and then create a new batch
                producer.send(eventDataBatch);
                eventDataBatch = producer.createBatch();

                // Try to add that event that couldn't fit before.
                if (!eventDataBatch.tryAdd(eventData)) {
                    throw new IllegalArgumentException("Event is too large for an empty batch. Max size: "
                        + eventDataBatch.getMaxSizeInBytes());
                }
            }
        }
        // send the last batch of remaining events
        if (eventDataBatch.getCount() > 0) {
            producer.send(eventDataBatch);
        }
        producer.close();
    }
```

Compile o programa e certifique-se de que não existem erros. Você executará esse programa depois de executar o programa receptor. 

## <a name="receive-events"></a>Receber eventos
O código neste tutorial se baseia no [exemplo de EventProcessorClient no GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorBlobCheckpointStoreSample.java), que você pode examinar para ver todo o aplicativo funcional.

> [!WARNING]
> Se você executar esse código no Azure Stack Hub, haverá erros de runtime, a menos que você direcione uma versão específica da API de Armazenamento. Isso ocorre porque o SDK dos Hubs de Eventos usa a API do Armazenamento do Microsoft Azure mais recente disponível no Azure, que pode não estar disponível em sua plataforma do Azure Stack Hub. O Azure Stack Hub pode dar suporte a uma versão diferente do SDK do Armazenamento de Blobs do que aquelas normalmente disponíveis no Azure. Se estiver usando o Armazenamento de Blobs do Azure como um armazenamento de ponto de verificação, verifique a [versão da API do Armazenamento do Azure com suporte de seu build do Azure Stack Hub](/azure-stack/user/azure-stack-acs-differences?#api-version) e tenha como destino essa versão em seu código. 
>
> Por exemplo, se a execução estiver sendo feita no Azure Stack Hub versão 2005, a versão mais alta disponível para o serviço de Armazenamento será a versão 2019-02-02. Por padrão, a biblioteca de clientes do SDK dos Hubs de Eventos usa a versão mais alta disponível no Azure (2019-07-07 no momento do lançamento do SDK). Nesse caso, além de seguir as etapas desta seção, você também precisará adicionar o código para ter como destino a versão de API 2019-02-02 do serviço de Armazenamento. Para obter um exemplo de como ter como destino uma versão de API específica do Armazenamento, confira [esta amostra no GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). 


### <a name="create-an-azure-storage-and-a-blob-container"></a>Criar um Armazenamento do Azure e um contêiner de blob
Neste guia de início rápido, você usará o Armazenamento do Azure (especificamente, o Armazenamento de Blobs) como o repositório de pontos de verificação. A marcação de pontos de verificação é um processo pelo qual um processador de eventos marca ou confirma a posição do último evento processado com êxito em uma partição. Normalmente, a marcação de um ponto de verificação é feita na função que processa os eventos. Para saber mais sobre a marcação de pontos de verificação, confira [Processador de eventos](event-processor-balance-partition-load.md).

Siga estas etapas para criar uma conta de Armazenamento do Azure. 

1. [Crie uma conta de Armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Crie um contêiner de blob](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Obtenha a cadeia de conexão para a conta de armazenamento](../storage/common/storage-configure-connection-string.md)

    Anote a **cadeia de conexão** e o **nome do contêiner**. Você os usará no código de recebimento. 

### <a name="add-event-hubs-libraries-to-your-java-project"></a>Adicionar bibliotecas dos Hubs de Eventos ao seu projeto Java
Adicione as dependências a seguir ao arquivo pom.xml. 

- [azure-messaging-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs)
- [azure-messaging-eventhubs-checkpointstore-blob](https://search.maven.org/search?q=a:azure-messaging-eventhubs-checkpointstore-blob)

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.6.0</version>
    </dependency>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs-checkpointstore-blob</artifactId>
        <version>1.5.1</version>
    </dependency>
</dependencies>
```

1. Adicione as instruções **import** a seguir à parte superior do arquivo Java. 

    ```java
    import com.azure.messaging.eventhubs.EventHubClientBuilder;
    import com.azure.messaging.eventhubs.EventProcessorClient;
    import com.azure.messaging.eventhubs.EventProcessorClientBuilder;
    import com.azure.messaging.eventhubs.checkpointstore.blob.BlobCheckpointStore;
    import com.azure.messaging.eventhubs.models.ErrorContext;
    import com.azure.messaging.eventhubs.models.EventContext;
    import com.azure.storage.blob.BlobContainerAsyncClient;
    import com.azure.storage.blob.BlobContainerClientBuilder;
    import java.util.function.Consumer;
    ```
2. Crie uma classe chamada `Receiver` e adicione as variáveis de cadeia de caracteres a seguir à classe. Substitua os espaços reservados pelos valores corretos. 

    > [!IMPORTANT]
    > Substitua os espaços reservados pelos valores corretos.
    > - `<Event Hubs namespace connection string>` pela cadeia de conexão do namespace dos Hubs de Eventos. Atualizar 
    > - `<Event hub name>` pelo nome do hub de eventos no namespace. 
    > - `<Storage connection string>` pela cadeia de conexão da conta de armazenamento do Azure. 
    > - `<Storage container name>` pelo nome do contêiner no armazenamento de blobs do Azure. 

    ```java
    private static final String connectionString = "<Event Hubs namespace connection string>";
    private static final String eventHubName = "<Event hub name>";
    private static final String storageConnectionString = "<Storage connection string>";
    private static final String storageContainerName = "<Storage container name>";
    ```
1. Adicione o método `main` a seguir à classe. 

    ```java
    public static void main(String[] args) throws Exception {
        // Create a blob container client that you use later to build an event processor client to receive and process events
        BlobContainerAsyncClient blobContainerAsyncClient = new BlobContainerClientBuilder()
            .connectionString(storageConnectionString) 
            .containerName(storageContainerName) 
            .buildAsyncClient();
    
        // Create a builder object that you will use later to build an event processor client to receive and process events and errors.
        EventProcessorClientBuilder eventProcessorClientBuilder = new EventProcessorClientBuilder()
            .connectionString(connectionString, eventHubName) 
            .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
            .processEvent(PARTITION_PROCESSOR) 
            .processError(ERROR_HANDLER) 
            .checkpointStore(new BlobCheckpointStore(blobContainerAsyncClient)); 

        // Use the builder object to create an event processor client 
        EventProcessorClient eventProcessorClient = eventProcessorClientBuilder.buildEventProcessorClient();

        System.out.println("Starting event processor");
        eventProcessorClient.start();

        System.out.println("Press enter to stop.");
        System.in.read();

        System.out.println("Stopping event processor");
        eventProcessorClient.stop();
        System.out.println("Event processor stopped.");

        System.out.println("Exiting process");
    }    
    ```
4. Adicione os dois métodos auxiliares (`PARTITION_PROCESSOR` e `ERROR_HANDLER`) que processam eventos e erros para a classe `Receiver`. 

    ```java
    public static final Consumer<EventContext> PARTITION_PROCESSOR = eventContext -> {
        System.out.printf("Processing event from partition %s with sequence number %d with body: %s %n", 
                eventContext.getPartitionContext().getPartitionId(), eventContext.getEventData().getSequenceNumber(), eventContext.getEventData().getBodyAsString());

        if (eventContext.getEventData().getSequenceNumber() % 10 == 0) {
            eventContext.updateCheckpoint();
        }
    };
    
    public static final Consumer<ErrorContext> ERROR_HANDLER = errorContext -> {
        System.out.printf("Error occurred in partition processor for partition %s, %s.%n",
            errorContext.getPartitionContext().getPartitionId(),
            errorContext.getThrowable());
    };    
    ```
3. Compile o programa e certifique-se de que não existem erros. 

## <a name="run-the-applications"></a>Executar os aplicativos
1. Execute primeiro o aplicativo **receptor**.
1. Em seguida, execute o aplicativo **remetente**. 
1. Na janela do aplicativo **receptor**, confirme se você vê os eventos que foram publicados pelo aplicativo remetente.

    ```cmd
    Starting event processor
    Press enter to stop.
    Processing event from partition 0 with sequence number 331 with body: Foo 
    Processing event from partition 0 with sequence number 332 with body: Bar 
    ```
1. Pressione **ENTER** na janela do aplicativo receptor para interromper o aplicativo. 

    ```cmd
    Starting event processor
    Press enter to stop.
    Processing event from partition 0 with sequence number 331 with body: Foo 
    Processing event from partition 0 with sequence number 332 with body: Bar 
    
    Stopping event processor
    Event processor stopped.
    Exiting process
    ```

## <a name="next-steps"></a>Próximas etapas
Confira as seguintes amostras no GitHub:

- [amostras de azure-messaging-eventhubs](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [amostras de azure-messaging-eventhubs-checkpointstore-blob](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob).  
