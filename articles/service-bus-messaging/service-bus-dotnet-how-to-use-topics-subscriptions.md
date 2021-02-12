---
title: Enviar mensagens para tópicos do Barramento de Serviço do Azure usando azure-messaging-servicebus
description: Este guia de início rápido mostra como enviar mensagens para tópicos do Barramento de Serviço do Azure usando o pacote azure-messaging-servicebus.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 60504bcf9e2c3f9460eee9a2e72d18767c0cfa71
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631667"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-net"></a>Enviar mensagens para um tópico do Barramento de Serviço do Azure e receber mensagens de assinaturas para o tópico (.NET)
Este tutorial mostra como criar um aplicativo de console .NET Core que envia mensagens para um tópico do Barramento de Serviço e recebe mensagens de uma assinatura do tópico. 

> [!Important]
> Este guia de início rápido usa o novo pacote **Azure.Messaging.ServiceBus**. Para obter um guia de início rápido que usa o pacote antigo Microsoft.Azure.ServiceBus, confira [Enviar e receber mensagens usando o pacote Microsoft.Azure.ServiceBus](service-bus-dotnet-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- Uma assinatura do Azure. Para concluir este tutorial, você precisa de uma conta do Azure. Ative seus [benefícios de assinante do Visual Studio ou do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) ou inscreva-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Siga as etapas no [Início Rápido: Usar o portal do Azure para criar um tópico do Barramento de Serviço e assinaturas para o tópico](service-bus-quickstart-topics-subscriptions-portal.md). Anote a cadeia de conexão, o nome do tópico e o nome de uma assinatura. Você usará apenas uma assinatura neste guia de início rápido. 

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Nesta seção, você criará um aplicativo de console .NET Core no Visual Studio e adicionará um código para enviar mensagens para o tópico criado. 

### <a name="create-a-console-application"></a>Criar um aplicativo de console
Inicie o Visual Studio e crie um projeto de **Aplicativo de Console (.NET Core)** para C#. 

### <a name="add-the-service-bus-nuget-package"></a>Adicionar o pacote NuGet do Barramento de Serviço

1. Clique com o botão direito do mouse no projeto recém-criado e selecione **Gerenciar Pacotes NuGet**.
1. Selecione **Procurar**. Procure e selecione **[Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)** .
1. Selecione **Instalar** para concluir a instalação e feche o gerenciador de pacotes NuGet.

### <a name="add-code-to-send-messages-to-the-topic"></a>Adicionar um código para enviar mensagens para o tópico 

1. Em Program.cs, adicione as seguintes instruções `using` na parte superior da definição do namespace, antes da declaração de classe:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
1. Na classe `Program`, declare as seguintes variáveis:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string topicName = "<TOPIC NAME>";
        static string subscriptionName = "<SUBSCRIPTION NAME>";
    ```

    Substitua os seguintes valores:
    - `<NAMESPACE CONNECTION STRING>` pela cadeia de conexão para o namespace do Barramento de Serviço
    - `<TOPIC NAME>` pelo nome do tópico
    - `<SUBSCRIPTION NAME>` pelo nome da assinatura
2. Adicione um método chamado `SendMessageToTopicAsync` que envia uma mensagem ao tópico. 

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
1. Adicione um método chamado `CreateMessages` para criar uma fila (fila do .NET) de mensagens para a classe `Program`. Normalmente, você obtém essas mensagens de diferentes partes do seu aplicativo. Aqui, criamos uma fila de mensagens de exemplo.

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
1. Adicione um método chamado `SendMessageBatchAsync` à classe `Program` e adicione o código a seguir. Esse método usa uma fila de mensagens e prepara um ou mais lotes para enviá-los ao tópico do Barramento de Serviço. 

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
1. Substitua o método `Main()` pelo método **assíncrono** `Main` a seguir. Ele chama os métodos send para enviar uma só mensagem e um lote de mensagens para o tópico.  

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();
        }
    ```
5. Execute o aplicativo. Você deve ver o seguinte resultado:

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
- [Exemplos no GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [Referência da API REST](/dotnet/api/azure.messaging.servicebus?preserve-view=true)