---
title: Início Rápido – Usar o portal do Azure para criar uma fila do Barramento de Serviço | Microsoft Docs
description: Neste início rápido, você aprenderá a criar uma fila do Barramento de Serviço usando o portal do Azure. Em seguida, você usará um aplicativo cliente de exemplo para enviar e receber mensagens da fila.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/10/2019
ms.author: spelluru
ms.openlocfilehash: 315f8d30b7c7559947c599edd0e18eaa5a99ac22
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67513628"
---
# <a name="quickstart-use-azure-portal-to-create-a-service-bus-queue"></a>Início Rápido: Usar o portal do Azure para criar uma fila do Barramento de Serviço
Este início rápido descreve como enviar e receber mensagens de e para uma fila do Barramento de Serviço, usando o [portal do Azure][Azure portal] para criar uma fila e um namespace de mensagens nesse namespace e obter as credenciais de autorização nesse namespace. O procedimento mostra como enviar e receber mensagens dessa fila usando a [	Biblioteca do .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus).

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se você tem o seguinte instalado:

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, poderá criar uma [conta gratuita][] antes de começar.
- [Atualização 3 do Visual Studio 2017 (versão 15.3, 26730.01)](https://www.visualstudio.com/vs) ou posterior. Use o Visual Studio para compilar uma amostra que envia mensagens para uma fila e recebe mensagens dela. A amostra tem a finalidade de testar a fila criada usando o PowerShell. 
- [NET Core SDK](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="send-and-receive-messages"></a>Enviar e receber mensagens

Depois que o namespace e a fila forem provisionados e você tiver as credenciais necessárias, estará pronto para enviar e receber mensagens. É possível examinar o código [nesta pasta de exemplo do GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters).

Para executar o código, faça o seguinte:

1. Clone o [repositório GitHub do Barramento de Serviço](https://github.com/Azure/azure-service-bus/), emitindo o comando a seguir:

   ```
   git clone https://github.com/Azure/azure-service-bus.git
   ```
3. Navegue até a pasta de exemplo `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`.
4. Copie a cadeia de conexão e o nome da fila obtidos na seção Obter as credenciais de gerenciamento.
5.  Em um prompt de comando, digite o comando a seguir:

    ```
    dotnet build
    ```
6.  Navegue até a pasta `bin\Debug\netcoreapp2.0`.
7.  Digite o comando a seguir para executar o programa. Verifique se você substituiu `myConnectionString` pelo valor obtido anteriormente e `myQueueName` pelo nome da fila que você criou:

    ```shell
    dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
    ``` 
8. Observe 10 mensagens sendo enviadas para a fila e, subsequentemente, recebidas da fila:

   ![saída do programa](./media/service-bus-quickstart-portal/dotnet.png)

## <a name="clean-up-resources"></a>Limpar recursos

É possível usar o portal para remover o grupo de recursos, o namespace e a fila.

## <a name="understand-the-sample-code"></a>Entender o código de exemplo

Esta seção contém mais detalhes sobre o que o código de exemplo faz. 

### <a name="get-connection-string-and-queue"></a>Obter cadeia de conexão e fila

A cadeia de conexão e o nome da fila são passados para o método `Main()` como argumentos de linha de comando. `Main()` declara duas variáveis de cadeia de caracteres para manter esses valores:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string QueueName = "";

    for (int i = 0; i < args.Length; i++)
    {
        var p = new Program();
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i+1]}");
            ServiceBusConnectionString = args[i + 1]; 
        }
        else if(args[i] == "-QueueName")
        {
            Console.WriteLine($"QueueName: {args[i+1]}");
            QueueName = args[i + 1];
        }                
    }

    if (ServiceBusConnectionString != "" && QueueName != "")
        MainAsync(ServiceBusConnectionString, QueueName).GetAwaiter().GetResult();
    else
    {
        Console.WriteLine("Specify -Connectionstring and -QueueName to execute the example.");
        Console.ReadKey();
    }                            
}
```
 
Em seguida, o método `Main()` inicia o loop de mensagem assíncrona, `MainAsync()`.

### <a name="message-loop"></a>Loop de mensagem

O método MainAsync() cria um cliente de fila com os argumentos de linha de comando, chama um manipulador de mensagens de recebimento chamado `RegisterOnMessageHandlerAndReceiveMessages()` e envia o conjunto de mensagens:

```csharp
static async Task MainAsync(string ServiceBusConnectionString, string QueueName)
{
    const int numberOfMessages = 10;
    queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

    Console.WriteLine("======================================================");
    Console.WriteLine("Press any key to exit after receiving all the messages.");
    Console.WriteLine("======================================================");

    // Register QueueClient's MessageHandler and receive messages in a loop
    RegisterOnMessageHandlerAndReceiveMessages();

    // Send Messages
    await SendMessagesAsync(numberOfMessages);

    Console.ReadKey();

    await queueClient.CloseAsync();
}
```

O método `RegisterOnMessageHandlerAndReceiveMessages()` simplesmente define algumas opções de manipulador de mensagens e, em seguida, chama o métodos `RegisterMessageHandler()` do cliente de fila, que inicia o recebimento:

```csharp
static void RegisterOnMessageHandlerAndReceiveMessages()
{
    // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
    var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
    {
        // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
        // Set it according to how many messages the application wants to process in parallel.
        MaxConcurrentCalls = 1,

        // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
        // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
        AutoComplete = false
    };

    // Register the function that will process messages
    queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
} 
```

### <a name="send-messages"></a>Enviar mensagens

As operações de criação e envio de mensagens ocorrem no método `SendMessagesAsync()`:

```csharp
static async Task SendMessagesAsync(int numberOfMessagesToSend)
{
    try
    {
        for (var i = 0; i < numberOfMessagesToSend; i++)
        {
            // Create a new message to send to the queue
            string messageBody = $"Message {i}";
            var message = new Message(Encoding.UTF8.GetBytes(messageBody));

            // Write the body of the message to the console
            Console.WriteLine($"Sending message: {messageBody}");

            // Send the message to the queue
            await queueClient.SendAsync(message);
        }
    }
    catch (Exception exception)
    {
        Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
    }
}
```

### <a name="process-messages"></a>Processar mensagens

O método `ProcessMessagesAsync()` reconhece, processa e conclui o recebimento das mensagens:

```csharp
static async Task ProcessMessagesAsync(Message message, CancellationToken token)
{
    // Process the message
    Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

    // Complete the message so that it is not received again.
    await queueClient.CompleteAsync(message.SystemProperties.LockToken);
}
```
> [!NOTE]
> É possível gerenciar os recursos do Barramento de Serviço com o [Gerenciador de Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador de Barramento de Serviço permite que usuários se conectem a um namespace de serviço do Barramento de Serviço e administrem entidades de mensagens de uma maneira fácil. A ferramenta fornece recursos avançados, como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um namespace de Barramento de Serviço e outros recursos necessários para enviar e receber mensagens de uma fila. Para saber mais sobre como escrever o código para enviar e receber mensagens, continue com os tutoriais na seção **Enviar e receber mensagens**. 

> [!div class="nextstepaction"]
> [Enviar e receber mensagens](service-bus-dotnet-get-started-with-queues.md)


[conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png
