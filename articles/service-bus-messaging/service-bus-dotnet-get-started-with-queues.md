---
title: Introdução às filas do Barramento de Serviço do Azure (Azure.Messaging.ServiceBus)
description: Neste tutorial, você criará um aplicativo .NET Core C# para enviar e receber mensagens em uma fila do Barramento de Serviço.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ec3f53e6f69614028c013efa5f0e6852cbc3f8ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98631633"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-net"></a>Enviar e receber mensagens em filas do Barramento de Serviço do Azure (.NET)
Neste tutorial, você criará um aplicativo de console .NET Core para enviar e receber mensagens em uma fila do Barramento de Serviço usando o pacote **Azure.Messaging.ServiceBus**. 

> [!Important]
> Este guia de início rápido usa o novo pacote Azure.Messaging.ServiceBus. Para obter um guia de início rápido que usa o pacote antigo Microsoft.Azure.ServiceBus, confira [Enviar e receber eventos usando o pacote Microsoft.Azure.ServiceBus](service-bus-dotnet-get-started-with-queues-legacy.md).

## <a name="prerequisites"></a>Pré-requisitos

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- Uma assinatura do Azure. Para concluir este tutorial, você precisa de uma conta do Azure. Ative seus [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscreva-se em uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Caso você não tenha uma fila para trabalhar, siga as etapas do artigo [Usar o portal do Azure para criar uma fila do Barramento de Serviço](service-bus-quickstart-portal.md) para criar uma fila. Anote a **cadeia de conexão** do namespace do Barramento de Serviço e o nome da **fila** que você criou.

## <a name="send-messages-to-a-queue"></a>Enviar mensagens a uma fila
Neste guia de início rápido, você criará um aplicativo de console .NET Core C# para enviar mensagens à fila.

### <a name="create-a-console-application"></a>Criar um aplicativo de console
Inicie o Visual Studio e crie um projeto de **Aplicativo de Console (.NET Core)** para C#. 

### <a name="add-the-service-bus-nuget-package"></a>Adicionar o pacote NuGet do Barramento de Serviço

1. Clique com o botão direito do mouse no projeto recém-criado e selecione **Gerenciar Pacotes NuGet**.
1. Selecione **Procurar**. Procure e selecione **[Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)** .
1. Selecione **Instalar** para concluir a instalação e feche o gerenciador de pacotes NuGet.

### <a name="add-code-to-send-messages-to-the-queue"></a>Adicionar o código para enviar mensagens à fila

1. Em *Program.cs*, adicione as seguintes instruções `using` à parte superior da definição de namespace, antes da declaração de classe:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    
    using Azure.Messaging.ServiceBus;
    ```

1. Na classe `Program`, declare as seguintes variáveis:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string queueName = "<QUEUE NAME>";
    ```

    Insira a cadeia de conexão do namespace como a variável `connectionString`. Insira o nome da fila.

1. Logo após o método `Main()`, adicione o seguinte método `SendMessagesAsync()` que é responsável por enviar uma mensagem:

    ```csharp
        static async Task SendMessageAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // create a message that we can send
                ServiceBusMessage message = new ServiceBusMessage("Hello world!");

                // send the message
                await sender.SendMessageAsync(message);
                Console.WriteLine($"Sent a single message to the queue: {queueName}");
            }
        }
    ```
1. Adicione um método chamado `CreateMessages` para criar uma fila (fila do .NET) de mensagens para a classe `Program`. Normalmente, você obtém essas mensagens de diferentes partes do seu aplicativo. Aqui, criamos uma fila de mensagens de exemplo.

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message in the batch"));
            messages.Enqueue(new ServiceBusMessage("Second message in the batch"));
            messages.Enqueue(new ServiceBusMessage("Third message in the batch"));
            return messages;
        }
    ```
1. Adicione um método chamado `SendMessageBatchAsync` à classe `Program` e adicione o código a seguir. Esse método usa uma fila de mensagens e prepara um ou mais lotes a serem enviados à fila do Barramento de Serviço. 

    ```csharp
        static async Task SendMessageBatchAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // get the messages to be sent to the Service Bus queue
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus queue
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus queue
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

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {queueName}");
            }
        }
    ```
1. Substitua o método `Main()` pelo método **assíncrono** `Main` a seguir. Ele chama os métodos send para enviar uma só mensagem e um lote de mensagens para a fila. 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();
        }
    ```
5. Execute o aplicativo. Você verá as mensagens a seguir. 

    ```console
    Sent a single message to the queue: myqueue
    Sent a batch of messages to the queue: myqueue
    ```       
1. No portal do Azure, siga estas etapas:
    1. Acesse o namespace do Barramento de Serviço. 
    1. Na página **Visão geral**, selecione a fila no painel inferior central. 
    1. Observe os valores da seção **Essentials**.

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png" alt-text="Mensagens recebidas com a contagem e o tamanho" lightbox="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png":::

    Observe os seguintes valores:
    - O valor de **Contagem de mensagens ativas** da fila agora é **4**. Toda vez que você executa esse aplicativo de remetente sem recuperar as mensagens, esse valor aumenta em 4.
    - O tamanho atual da fila aumenta o valor **ATUAL** em **Essentials** toda vez que o aplicativo adiciona mensagens à fila.
    - No gráfico **Mensagens** da seção inferior **Métricas**, você verá que há quatro mensagens de entrada para a fila. 

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
Nesta seção, você adicionará o código para recuperar mensagens da fila.

1. Adicione os métodos a seguir à classe `Program` que processa as mensagens e os erros. 

    ```csharp
        // handle received messages
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        // handle any errors when receiving messages
        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. Adicione um método chamado `ReceiveMessagesAsync` à classe `Program` e adicione o código a seguir para receber mensagens. 

    ```csharp
        static async Task ReceiveMessagesAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions());

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
1. Adicione uma chamada ao método `ReceiveMessagesAsync` por meio do método `Main`. Comente o método `SendMessagesAsync` se você quiser testar apenas o recebimento das mensagens. Caso contrário, você verá outras quatro mensagens enviadas à fila. 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();

            // receive message from the queue
            await ReceiveMessagesAsync();
        }
    ```

## <a name="run-the-app"></a>Executar o aplicativo
Execute o aplicativo. Aguarde um minuto e clique em qualquer tecla para parar de receber mensagens. Você verá a saída a seguir (barra de espaços da tecla). 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Wait for a minute and then press any key to end the processing
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch

Stopping the receiver...
Stopped receiving messages
```

Verifique o portal novamente. 

- Os valores de **Contagem de mensagens ativas** e **ATUAL** agora são **0**.
- No gráfico **Mensagens** na seção inferior **Métricas**, você verá que há oito mensagens de entrada e oito mensagens de saída para a fila. 

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png" alt-text="Mensagens ativas e tamanho após o recebimento" lightbox="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png":::

## <a name="next-steps"></a>Próximas etapas
Confira os seguintes exemplos e a documentação:

- [Biblioteca de clientes do Barramento de Serviço do Azure para .NET – Leiame](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [Exemplos no GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [Referência da API REST](/dotnet/api/azure.messaging.servicebus?preserve-view=true)