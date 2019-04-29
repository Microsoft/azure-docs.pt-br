---
title: Enviar e receber eventos usando Java - Hubs de eventos do Azure | Microsoft Docs
description: Este artigo fornece um passo a passo da criação de um aplicativo Java que envia eventos para Hubs de eventos do Azure.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 0487cac6a0cf7d37befdf0d7cfab33ad6a62cf7f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822870"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-java"></a>Enviar eventos para ou receber eventos de Hubs de eventos do Azure usando Java

Os Hubs de Eventos do Azure são uma plataforma de streaming de Big Data e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos pelos dispositivos e software distribuídos. Os dados enviados para um Hub de Eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Para obter uma visão detalhada dos Hubs de Eventos, confira [Visão geral de Hubs de Eventos](event-hubs-about.md) e [Recursos de Hubs de Eventos](event-hubs-features.md).

Este tutorial mostra como criar aplicativos Java para enviar eventos para ou receber eventos de um hub de eventos. 

> [!NOTE]
> Você pode baixar do [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend) este início rápido como um exemplo, substituir as cadeias de caracteres `EventHubConnectionString` e `EventHubName` pelos valores do hub de eventos e executá-lo. Como alternativa, é possível seguir as etapas deste tutorial para criar sua própria solução.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

- Uma conta ativa do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- Um ambiente de desenvolvimento Java. Este tutorial usa o [Eclipse](https://www.eclipse.org/).
- **Criar um namespace de Hubs de eventos e um hub de eventos**. A primeira etapa é usar o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo Hubs de eventos e obter as credenciais de gerenciamento das quais que seu aplicativo precisa para se comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento [nesse artigo](event-hubs-create.md). Em seguida, obtenha o valor da chave de acesso para o hub de eventos seguindo as instruções do artigo: [Obter a cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A chave de acesso será usada no código que você escreverá posteriormente no tutorial. O nome da chave padrão é: **RootManageSharedAccessKey**.

## <a name="send-events"></a>Enviar eventos 
Esta seção mostra como criar um aplicativo Java para enviar eventos um hub de eventos. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Adicionar a referência à biblioteca de Hubs de Eventos do Azure

A biblioteca de clientes Java para Hubs de Eventos está disponível para uso em projetos Maven no [Repositório Central do Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Você pode fazer referência a essa biblioteca usando a seguinte declaração de dependência em seu arquivo do projeto Maven:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

Para diferentes tipos de ambiente de build, é possível obter explicitamente os arquivos JAR liberados mais recentemente no [Repositório Central do Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Para um editor de eventos simples, importe o pacote *com.microsoft.azure.eventhubs* para as classes de cliente dos Hubs de Eventos e o pacote *com.microsoft.azure.servicebus* para as classes de utilitário como exceções comuns que são compartilhadas com o cliente de mensagens do Barramento de Serviço do Azure. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Escrever código para enviar mensagens ao hub de eventos

Para o exemplo a seguir, primeiro crie um novo projeto do Maven para um aplicativo de console/shell em seu ambiente de desenvolvimento Java favorito. Adicione uma classe chamada `SimpleSend` e adicione o seguinte código à classe:

```java
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
            
            
    }
 }
```

### <a name="construct-connection-string"></a>Construir cadeia de conexão

Use a classe ConnectionStringBuilder para construir um valor de cadeia de conexão para passar para a instância do cliente de Hubs de Eventos. Substitua os espaços reservados pelos valores obtidos quando você criou o namespace e o hub de eventos:

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("speventhubns") 
                .setEventHubName("speventhub")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("2+WMsyyy1XmUtEnRsfOmTTyGasfJgsVjGAOIN20J1Y8=");
```

### <a name="write-code-to-send-events"></a>Escrever código para enviar eventos

Crie um evento singular transformando uma cadeia de caracteres em sua codificação de bytes UTF-8. Em seguida, crie uma nova instância de cliente dos Hubs de Eventos usando a cadeia de conexão e envie a mensagem:   

```java 
        final Gson gson = new GsonBuilder().create();

        // The Executor handles all asynchronous tasks and this is passed to the EventHubClient instance.
        // This enables the user to segregate their thread pool based on the work load.
        // This pool can then be shared across multiple EventHubClient instances.
        // The following sample uses a single thread executor, as there is only one EventHubClient instance,
        // handling different flavors of ingestion to Event Hubs here.
        final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(4);

        // Each EventHubClient instance spins up a new TCP/SSL connection, which is expensive.
        // It is always a best practice to reuse these instances. The following sample shows this.
        final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);


        try {
            for (int i = 0; i < 10; i++) {

                String payload = "Message " + Integer.toString(i);
                byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
                EventData sendEvent = EventData.create(payloadBytes);

                // Send - not tied to any partition
                // Event Hubs service will round-robin the events across all Event Hubs partitions.
                // This is the recommended & most reliable way to send to Event Hubs.
                ehClient.sendSync(sendEvent);
            }

            System.out.println(Instant.now() + ": Send Complete...");
            System.out.println("Press Enter to stop.");
            System.in.read();
        } finally {
            ehClient.closeSync();
            executorService.shutdown();
        }

``` 

Compile e execute o programa e certifique-se de que não existam erros.

Parabéns! Agora você enviou mensagens para um hub de eventos.

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>Apêndice: Como as mensagens são roteadas para as partições de Hub de Eventos

Antes que as mensagens sejam recuperadas pelos consumidores, elas precisam primeiro ser publicadas nas partições pelos editores. Quando as mensagens são publicadas para o Hub de Eventos de modo síncrono usando o método sendSync() no objeto com.microsoft.azure.eventhubs.EventHubClient, a mensagem foi enviada para uma partição específica ou distribuída a todas as partições disponíveis de forma round robin, dependendo de a chave de partição ser especificada ou não.

Quando uma cadeia de caracteres que representa a chave de partição for especificada, a chave receberá um hash para determinar a qual partição enviar o evento.

Quando a chave de partição não for definida, as mensagens serão distribuídas por round robin a todas as partições disponíveis

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="receive-events"></a>Receber eventos
O código neste tutorial se baseia no [código de EventProcessorSample no GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample), que você pode examinar para ver todo o aplicativo funcional.

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>Receber mensagens com EventProcessorHost em Java

**EventProcessorHost** é uma classe Java que simplifica o recebimento de eventos dos Hubs de Eventos gerenciando pontos de verificação persistentes e recebimentos paralelos desses Hubs de Eventos. Usando o EventProcessorHost, você pode dividir eventos entre vários destinatários, mesmo quando hospedados em nós diferentes. Este exemplo mostra como usar o EventProcessorHost para um único destinatário.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Para usar o EventProcessorHost, você deve ter uma [conta de armazenamento do Azure] [conta de armazenamento do Azure]:

1. Entrar a [portal do Azure](https://portal.azure.com)e clique em **+ criar um recurso** no lado esquerdo da tela.
2. Clique em **Armazenamento** e em **conta de Armazenamento**. Na janela **Criar conta de armazenamento**, digite um nome para a conta de armazenamento. Preencha o restante dos campos, selecione a região desejada e clique em **Criar**.
   
    ![Criar Conta de Armazenamento](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)

3. Clique na conta de armazenamento criada recentemente e, em seguida, clique em **Chaves de Acesso**:
   
    ![Obter chaves de acesso](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

    Copie o valor key1 para um local temporário. Você o usará posteriormente neste tutorial.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Crie um projeto Java usando o Host de EventProcessor

A biblioteca de cliente Java para os Hubs de Eventos está disponível para uso em projetos do Maven por meio do [Repositório Central do Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22), e pode ser referenciada usando a seguinte declaração de dependência dentro do arquivo de projeto do Maven: 

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.4.0</version>
</dependency>
```

Para diferentes tipos de ambientes de compilação, é possível obter explicitamente os arquivos JAR liberados mais do [repositório Central Maven] [https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22].  

1. Para o exemplo a seguir, primeiro crie um novo projeto do Maven para um aplicativo de console/shell em seu ambiente de desenvolvimento Java favorito. A classe é chamada `ErrorNotificationHandler`.     
   
    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. Use o código a seguir para criar uma nova classe chamada `EventProcessorSample`. Substitua os espaços reservados pelos valores usados durante a criação do hub de eventos e da conta de armazenamento:
   
   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";
        
           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);
        
           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);
        
           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
               try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
           {
               return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!
        
           System.out.println("End of sample");
       }
    ```
3. Crie mais uma classe chamada `EventProcessor` usando o código a seguir:
   
    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. 
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. 
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. 
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;
                    
                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. 
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                            data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

Este tutorial usa uma única instância do EventProcessorHost. Para aumentar a taxa de transferência, é recomendável executar várias instâncias de EventProcessorHost, preferencialmente em computadores separados.  Isso também fornece redundância. Nesses casos, as diversas instâncias são coordenadas automaticamente umas com as outras para balancear a carga de eventos recebidos. Se você quiser que vários destinatários processem, cada um, *todos* os eventos, você deve usar o conceito **ConsumerGroup** . Ao receber eventos em máquinas diferentes, pode ser útil especificar nomes para instâncias de EventProcessorHost com base em máquinas (ou funções) nas quais eles foram implantados.

### <a name="publishing-messages-to-eventhub"></a>Publicar mensagens no EventHub

Antes que as mensagens sejam recuperadas pelos consumidores, elas precisam primeiro ser publicadas nas partições pelos editores. Vale observar que, quando as mensagens são publicadas no Hub de Eventos de modo síncrono usando o método sendSync() no objeto com.microsoft.azure.eventhubs.EventHubClient, a mensagem foi enviada para uma partição específica ou distribuída a todas as partições disponíveis de forma round robin, dependendo de a chave de partição ser especificada ou não.

Quando uma cadeia de caracteres que representa a chave de partição for especificada, a chave receberá um hash para determinar a qual partição enviar o evento.

Quando a chave de partição não for definida, as mensagens serão distribuídas por round robin a todas as partições disponíveis

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

```

### <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>Implementar um CheckpointManager personalizado para EventProcessorHost (EPH)

A API fornece um mecanismo para implementar seu gerenciador de ponto de verificação personalizado em cenários em que a implementação padrão não é compatível com o seu caso de uso.

O gerenciador de ponto de verificação padrão usa o armazenamento de blobs. No entanto, se substituir o gerenciador de ponto de verificação usado pelo EPH por sua própria implementação, você poderá usar qualquer armazenamento que quiser para fazer a sua implementação desse gerenciador.

Crie uma classe que implementa a interface com.microsoft.azure.eventprocessorhost.ICheckpointManager

Use sua implementação personalizada do gerenciador de ponto de verificação (com.microsoft.azure.eventprocessorhost.ICheckpointManager)

Dentro de sua implementação, você pode substituir o mecanismo de ponto de verificação padrão e implementar nossos próprio pontos de verificação com base em seu próprio armazenamento de dados (como o SQL Server, CosmosDB e o Cache do Azure para Redis). É recomendável que o armazenamento usado para fazer sua implementação do gerenciador de ponto de verificação seja acessível para todas as instâncias do EPH que estejam processando eventos para o grupo de consumidores.

Você pode usar qualquer armazenamento de dados que estiver disponível em seu ambiente.

A classe com.microsoft.azure.eventprocessorhost.EventProcessorHos fornece dois construtores que permitem que você substitua o gerenciador de ponto de verificação do EventProcessorHost.


## <a name="next-steps"></a>Próximas etapas
Leia os seguintes artigos: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Recursos e a terminologia nos Hubs de eventos do Azure](event-hubs-features.md)
- [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)

