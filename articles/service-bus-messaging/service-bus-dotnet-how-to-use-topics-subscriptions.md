---
title: Enviar mensagens para tópicos do Barramento de Serviço do Azure usando azure-messaging-servicebus
description: Este guia de início rápido mostra como enviar mensagens para tópicos do Barramento de Serviço do Azure usando o pacote azure-messaging-servicebus.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 03/16/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: 79eb7783fd3daf546539dd5b9048f4e9f484374f
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279792"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-net"></a>Enviar mensagens para um tópico do Barramento de Serviço do Azure e receber mensagens de assinaturas para o tópico (.NET)
Neste tutorial, você criará um aplicativo em C# para executar as seguintes tarefas:

1. Enviar mensagens para um tópico do Barramento de Serviço. 

    Um tópico do Barramento de Serviço fornecerá um ponto de extremidade para aplicativos do remetente enviarem mensagens. Um tópico pode ter uma ou mais assinaturas. Cada assinatura de um tópico obterá uma cópia da mensagem enviada ao tópico. Para obter mais informações sobre tópicos do Barramento de Serviço, confira [O que é o Barramento de Serviço do Azure?](service-bus-messaging-overview.md). 
1. Receber mensagens de uma assinatura do tópico. 

    :::image type="content" source="./media/service-bus-messaging-overview/about-service-bus-topic.png" alt-text="Tópicos e assinaturas do Barramento de Serviço":::

    > [!Important]
    > Este guia de início rápido usa o novo pacote **Azure.Messaging.ServiceBus**. Caso esteja usando o pacote Microsoft.Azure.ServiceBus antigo, confira como [Enviar e receber mensagens usando o pacote Microsoft.Azure.ServiceBus](service-bus-dotnet-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Para concluir este tutorial, você precisa de uma conta do Azure. Ative seus [benefícios de assinante do Visual Studio ou do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscreva-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Siga as etapas deste [Guia de início rápido](service-bus-quickstart-topics-subscriptions-portal.md) para criar um tópico do Barramento de Serviço e assinaturas para ele. 

    > [!NOTE]
    > Neste tutorial, você usará uma cadeia de conexão para o namespace, o nome do tópico e o nome de uma das assinaturas para o tópico.  
- [Visual Studio 2019](https://www.visualstudio.com/vs). 
 
## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Nesta seção, você criará um aplicativo de console do .NET Core no Visual Studio e adicionará um código para enviar mensagens ao tópico do Barramento de Serviço criado. 

### <a name="create-a-console-application"></a>Criar um aplicativo de console
Crie um aplicativo de console do .NET Core usando o Visual Studio. 

1. Inicie o Visual Studio.  
1. Caso veja a página **Introdução**, clique em **Criar um projeto**. 
1. Na página **Criar um projeto** siga estas etapas: 
    1. Selecione **C#** para a linguagem de programação. 
    1. Selecione **Console** para o tipo de projeto. 
    1. Selecione **Aplicativo de Console (.NET Core)** na lista de modelos. 
    1. Em seguida, selecione **Avançar**. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project.png" alt-text="Criar um projeto de aplicativo de console":::
1. Na página **Configurar novo projeto**, siga estas etapas: 
    1. Em **Nome do projeto**, insira um nome para o projeto. 
    1. Em **Local**, selecione um local para os arquivos do projeto e da solução. 
    1. Em **Nome da solução**, insira um nome para a solução. Uma solução do Visual Studio pode ter um ou mais projetos. Neste guia de início rápido, a solução terá somente um projeto. 
    1. Selecione **Criar** para criar o cluster. 
            
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project-2.png" alt-text="Inserir o nome e o local do projeto e da solução":::    


### <a name="add-the-service-bus-nuget-package"></a>Adicionar o pacote NuGet do Barramento de Serviço
1. Clique com o botão direito do mouse no projeto recém-criado e selecione **Gerenciar Pacotes NuGet**.

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/manage-nuget-packages-menu.png" alt-text="Menu Gerenciar Pacotes NuGet":::        
1. Alterne para a guia **Procurar**.
1. Procure e selecione **[Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)** .
1. Selecione **Instalar** para concluir a instalação.

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-service-bus-package.png" alt-text="Selecionar um pacote NuGet do Barramento de Serviço.":::
5. Caso veja a caixa de diálogo **Visualizar Alterações**, clique em **OK** para continuar. 
1. Caso veja a página **Aceitação da Licença**, clique em **Aceito** para continuar. 
    

### <a name="add-code-to-send-messages-to-the-topic"></a>Adicionar um código para enviar mensagens para o tópico 

1. Na janela **Gerenciador de Soluções**, clique duas vezes em **Program.cs** para abrir o arquivo no editor. 
1. Adicione as seguintes instruções `using` na parte superior da definição do namespace, antes da declaração de classe:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
1. Na classe `Program`, declare as seguintes variáveis acima da função `Main`:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string topicName = "<SERVICE BUS TOPIC NAME>";
        static string subscriptionName = "<SERVICE BUS - TOPIC SUBSCRIPTION NAME>";
    ```

    Substitua os seguintes valores:
    - `<NAMESPACE CONNECTION STRING>` pela cadeia de conexão para o namespace do Barramento de Serviço
    - `<TOPIC NAME>` pelo nome do tópico
    - `<SUBSCRIPTION NAME>` pelo nome da assinatura

### <a name="send-a-single-message-to-the-topic"></a>Enviar uma mensagem única ao tópico
Adicione um método chamado `SendMessageToTopicAsync` à classe `Program` acima ou abaixo do método `Main`. Esse método enviará uma mensagem única ao tópico.

```csharp
    static async Task SendMessageToTopicAsync()
    {
        // create a Service Bus client 
        await using (ServiceBusClient client = new ServiceBusClient(connectionString))
        {
            // create a sender for the topic
            ServiceBusSender sender = client.CreateSender(topicName);
            await sender.SendMessageAsync(new ServiceBusMessage("Hello, World!"));
            Console.WriteLine($"Sent a single message to the topic: {topicName}");
        }
    }
```

Esse método executará as seguintes etapas: 
1. Cria um objeto [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) usando uma cadeia de conexão para o namespace. 
1. Usa um objeto `ServiceBusClient` para criar um objeto [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) para o tópico do Barramento de Serviço especificado. Esta etapa usará o método [ServiceBusClient.CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender).
1. Depois o método enviará uma mensagem de teste única ao tópico do Barramento de Serviço usando o método [ServiceBusSender.SendMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessageasync). 

### <a name="send-a-batch-of-messages-to-the-topic"></a>Enviar um lote de mensagens ao tópico
1. Adicione um método chamado `CreateMessages` para criar uma fila (do .NET, não uma fila do Barramento de Serviço) de mensagens para a classe `Program`. Normalmente, você obtém essas mensagens de diferentes partes do seu aplicativo. Aqui, criamos uma fila de mensagens de exemplo. Caso não esteja familiarizado com filas do .NET, confira [Queue.Enqueue](/dotnet/api/system.collections.queue.enqueue).

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message"));
            messages.Enqueue(new ServiceBusMessage("Second message"));
            messages.Enqueue(new ServiceBusMessage("Third message"));
            return messages;
        }
    ```
1. Adicione um método chamado `SendMessageBatchAsync` à classe `Program` e adicione o código abaixo. Esse método usa uma fila de mensagens e prepara um ou mais lotes para enviá-los ao tópico do Barramento de Serviço. 

    ```csharp
        static async Task SendMessageBatchToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {

                // create a sender for the topic 
                ServiceBusSender sender = client.CreateSender(topicName);

                // get the messages to be sent to the Service Bus topic
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus topic
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus topic
                while (messages.Count > 0)
                {
                    // start a new batch 
                    using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();

                    // add the first message to the batch
                    if (messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue once the message is added to the batch
                        messages.Dequeue();
                    }
                    else
                    {
                        // if the first message can't fit, then it is too large for the batch
                        throw new Exception($"Message {messageCount - messages.Count} is too large and cannot be sent.");
                    }

                    // add as many messages as possible to the current batch
                    while (messages.Count > 0 && messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue as it has been added to the batch
                        messages.Dequeue();
                    }

                    // now, send the batch
                    await sender.SendMessagesAsync(messageBatch);

                    // if there are any remaining messages in the .NET queue, the while loop repeats 
                }

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {topicName}");
            }
        }
    ```    

    Veja estas etapas importantes do código:
    1. Cria um objeto [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) usando uma cadeia de conexão para o namespace. 
    1. Invoca o método [CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) no objeto `ServiceBusClient` a fim de criar um objeto [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) para o tópico do Barramento de Serviço especificado. 
    1. Invoca o método auxiliar `GetMessages` para obter uma fila de mensagens a serem enviadas ao tópico do Barramento de Serviço. 
    1. Cria um método [ServiceBusMessageBatch](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch) usando [ServiceBusSender.CreateMessageBatchAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.createmessagebatchasync).
    1. Adiciona mensagens ao lote usando o método [ServiceBusMessageBatch.TryAddMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch.tryaddmessage). Conforme as mensagens forem adicionadas ao lote, elas serão removidas da fila do .NET. 
    1. Envia o lote de mensagens ao tópico do Barramento de Serviço usando o método [ServiceBusSender.SendMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessagesasync).

### <a name="update-the-main-method"></a>Atualizar o método principal
Substitua o método `Main()` pelo método **assíncrono** `Main` a seguir. Ele chama os métodos send para enviar uma só mensagem e um lote de mensagens para o tópico.  

```csharp
    static async Task Main()
    {
        // send a single message to the topic
        await SendMessageToTopicAsync();

        // send a batch of messages to the topic
        await SendMessageBatchToTopicAsync();
    }
```

### <a name="test-the-app-to-send-messages-to-the-topic"></a>Testar o aplicativo para enviar mensagens ao tópico
1. Execute o aplicativo. Você deve ver o seguinte resultado:

    ```console
    Sent a single message to the topic: mytopic
    Sent a batch of 3 messages to the topic: mytopic
    ```
1. No portal do Azure, siga estas etapas:
    1. Acesse o namespace do Barramento de Serviço. 
    1. Na página **Visão geral**, no painel inferior central, alterne para a guia **Tópicos** e selecione o tópico do Barramento de Serviço. No exemplo a seguir, ele é `mytopic`.
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-topic.png" alt-text="Selecionar tópico":::
    1. Na página **Tópico do Barramento de Serviço**, no gráfico **Mensagens** da seção inferior **Métricas**, você verá que há quatro mensagens de entrada para o tópico. Se o valor não for exibido, aguarde alguns minutos e atualize a página para ver o gráfico atualizado. 

        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png" alt-text="Mensagens enviadas para o tópico" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png":::
    4. Selecione a assinatura no painel inferior. No exemplo a seguir, ela é **S1**. Na página **Assinatura do Barramento de Serviço**, você verá a **Contagem de mensagens ativas** como **4**. A assinatura recebeu as quatro mensagens que você enviou para o tópico, mas nenhum destinatário as selecionou ainda. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png" alt-text="Mensagens recebidas na assinatura" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png":::
    

## <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura

1. Adicione os métodos a seguir à classe `Program` que processa as mensagens e os erros. 

    ```csharp
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body} from subscription: {subscriptionName}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. Adicione o método `ReceiveMessagesFromSubscriptionAsync` a seguir à classe `Program`.

    ```csharp
        static async Task ReceiveMessagesFromSubscriptionAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(topicName, subscriptionName, new ServiceBusProcessorOptions());

                // add handler to process messages
                processor.ProcessMessageAsync += MessageHandler;

                // add handler to process any errors
                processor.ProcessErrorAsync += ErrorHandler;

                // start processing 
                await processor.StartProcessingAsync();

                Console.WriteLine("Wait for a minute and then press any key to end the processing");
                Console.ReadKey();

                // stop processing 
                Console.WriteLine("\nStopping the receiver...");
                await processor.StopProcessingAsync();
                Console.WriteLine("Stopped receiving messages");
            }
        }
    ```

    Veja estas etapas importantes do código:
    1. Cria um objeto [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) usando uma cadeia de conexão para o namespace. 
    1. Invoca o método [CreateProcessor](/dotnet/api/azure.messaging.servicebus.servicebusclient.createprocessor) no objeto `ServiceBusClient` a fim de criar um objeto [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) para o tópico do Barramento de Serviço e a combinação de assinatura especificados. 
    1. Especifica os manipuladores dos eventos [ProcessMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) e [ProcessErrorAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processerrorasync) do objeto `ServiceBusProcessor`. 
    1. Inicia o processamento de mensagens invocando o método [StartProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.startprocessingasync) no objeto `ServiceBusProcessor`. 
    1. Quando o usuário pressionar uma tecla para encerrar o processamento, ele invocará o método [StopProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.stopprocessingasync) no objeto `ServiceBusProcessor`. 
1. Adicione uma chamada ao método `ReceiveMessagesFromSubscriptionAsync` ao método `Main`. Comente o método `SendMessagesToTopicAsync` se você quiser testar apenas o recebimento das mensagens. Caso contrário, você verá outras quatro mensagens enviadas ao tópico. 

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();

            // receive messages from the subscription
            await ReceiveMessagesFromSubscriptionAsync();
        }
    ```
## <a name="run-the-app"></a>Executar o aplicativo
Execute o aplicativo. Aguarde um minuto e clique em qualquer tecla para parar de receber mensagens. Você verá a saída a seguir (barra de espaços da tecla). 

```console
Sent a single message to the topic: mytopic
Sent a batch of 3 messages to the topic: mytopic
Wait for a minute and then press any key to end the processing
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub

Stopping the receiver...
Stopped receiving messages
```

Verifique o portal novamente. 

- Na página **Tópico do Barramento de Serviço**, no gráfico **Mensagens**, você verá oito mensagens de entrada e oito mensagens de saída. Se esses números não forem exibidos, aguarde alguns minutos e atualize a página para ver o gráfico atualizado. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png" alt-text="Mensagens enviadas e recebidas" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png":::
- Na página **Assinatura do Barramento de Serviço**, você verá a **Contagem de mensagens ativas** como zero. Isso ocorre porque um receptor recebeu mensagens dessa assinatura e concluiu as mensagens. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png" alt-text="Contagem de mensagens ativas na assinatura no final" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png":::
    


## <a name="next-steps"></a>Próximas etapas
Confira os seguintes exemplos e a documentação:

- [Biblioteca de clientes do Barramento de Serviço do Azure para .NET – Leiame](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [Exemplos do .NET para o Barramento de Serviço do Azure no GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [Referência da API REST](/dotnet/api/azure.messaging.servicebus?preserve-view=true)