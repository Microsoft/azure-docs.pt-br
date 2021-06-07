---
title: Hubs de Eventos do Azure – enviar e receber eventos usando o .NET Framework
description: 'Início Rápido: Este artigo apresenta instruções passo a passo para a criação de um aplicativo .NET Framework que envia eventos para os Hubs de Eventos do Azure.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 3c2ce58f86fa27544641c0917e96fa3172017232
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309349"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-net-framework"></a>Início Rápido: Enviar ou receber eventos de Hubs de Eventos do Azure usando o .NET Framework
Os Hubs de Eventos do Azure são uma plataforma de streaming de Big Data e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos pelos dispositivos e software distribuídos. Os dados enviados para um Hub de Eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Para obter uma visão detalhada dos Hubs de Eventos, confira [Visão geral de Hubs de Eventos](event-hubs-about.md) e [Recursos de Hubs de Eventos](event-hubs-features.md).

Este tutorial mostra como criar aplicativos de console do .NET Framework em C# para enviar ou receber eventos de um eventhub. 

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

- [Microsoft Visual Studio 2019](https://visualstudio.com).
- **Criar um namespace de Hubs de Eventos e um hub de eventos**. A primeira etapa é usar o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo Hubs de eventos e obter as credenciais de gerenciamento das quais que seu aplicativo precisa para se comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento [nesse artigo](event-hubs-create.md). Em seguida, obtenha a **cadeia de conexão para o namespace do hub de eventos** seguindo as instruções do artigo: [Obter a cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Você usa a cadeia de conexão posteriormente no tutorial.

## <a name="send-events"></a>Enviar eventos 
Esta seção mostra como criar um aplicativo de console .NET Framework para enviar eventos para um hub de eventos. 

### <a name="create-a-console-application"></a>Criar um aplicativo de console

No Visual Studio, crie um novo projeto de aplicativo de área de trabalho do Visual C# usando o modelo de projeto de **Aplicativo de Console** . Nomeie o projeto como **Remetente**.
   
![Captura de tela que mostra a janela "Novo Projeto" com "Aplicativo de Console (.NET Framework)" selecionado e o nome do projeto "Remetente" e o botão "OK" realçados.](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)

### <a name="add-the-event-hubs-nuget-package"></a>Adicione o pacote NuGet de Hubs de Eventos

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **Remetente** clique em **Gerenciar Pacotes NuGet para Solução**. 
2. Clique na guia **Procurar** e procure `WindowsAzure.ServiceBus`. Clique em **Instalar** e aceite os termos de uso. 
   
    ![Instale o pacote do NuGet do Barramento de Serviço](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    O Visual Studio faz o download, instala e adiciona uma referência ao [pacote NuGet de biblioteca do Barramento de Serviço do Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus).

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Escrever código para enviar mensagens ao hub de eventos

1. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :
   
    ```csharp
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```
2. Adicione os seguintes campos à classe **Programa**, substituindo os valores do espaço reservado pelo nome do hub de eventos criado na seção anterior e pela cadeia de conexão no nível do namespace que você salvou anteriormente. Você pode copiar a cadeia de conexão do hub de eventos da chave **Primária da cadeia de conexão** em **RootManageSharedAccessKey** na página do Hub de Eventos no portal do Azure. Para obter as etapas detalhadas, confira [Obter cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal).
   
    ```csharp
    static string eventHubName = "Your Event Hub name";
    static string connectionString = "namespace connection string";
    ```
3. Adicione o seguinte método à classe **Programa** :
   
      ```csharp
      static void SendingRandomMessages()
      {
          var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
          while (true)
          {
              try
              {
                  var message = Guid.NewGuid().ToString();
                  Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                  eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
              }
              catch (Exception exception)
              {
                  Console.ForegroundColor = ConsoleColor.Red;
                  Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                  Console.ResetColor();
              }
       
              Thread.Sleep(200);
          }
      }
      ```
   
      Esse método envia continuamente os eventos para seu hub de eventos com um atraso de 200 ms.
4. Por fim, adicione as seguintes linhas ao método **Principal** :
   
      ```csharp
      Console.WriteLine("Press Ctrl-C to stop the sender process");
      Console.WriteLine("Press Enter to start now");
      Console.ReadLine();
      SendingRandomMessages();
      ```
5. Execute o programa e certifique-se de que não existem erros.
  
## <a name="receive-events"></a>Receber eventos
Nesta seção, você escreve um aplicativo de console do .NET Framework que recebe mensagens de um hub de eventos usando o [Host do Processador de Eventos](event-hubs-event-processor-host.md). O [Host do Processador de Eventos](event-hubs-event-processor-host.md) é uma classe do .NET que simplifica o recebimento de eventos de hubs de eventos ao gerenciar pontos de verificação persistentes e recebimentos paralelos desses hubs de eventos. Ao usar o Host do Processador de Eventos, você pode dividir eventos através de vários receptores, mesmo quando hospedados em nós diferentes. 

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>Criar um aplicativo de console

No Visual Studio, crie um novo projeto de aplicativo de área de trabalho do Visual C# usando o modelo de projeto de **Aplicativo de Console** . Nomeie o projeto como **Destinatário**.
   
![Criar um aplicativo de console](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)

### <a name="add-the-event-hubs-nuget-package"></a>Adicione o pacote NuGet de Hubs de Eventos

1. No Gerenciador de Soluções, clique com o botão direito no projeto **Destinatário** e clique em **Gerenciar Pacotes NuGet para Solução**.
2. Clique na guia **Procurar** e procure `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Clique em **Instalar** e aceite os termos de uso.
   
    ![Pesquisar pacote NuGet do Host do Processador de Eventos](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    O Visual Studio baixa, instala e adiciona uma referência ao [Hub de Eventos do Barramento de Serviço do Azure - pacote NuGet do EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), com todas as suas dependências.

### <a name="implement-the-ieventprocessor-interface"></a>Implementar a interface IEventProcessor

1. Clique com o botão direito do mouse no projeto do **Receptor**, clique em **Adicionar** e clique em **Classe**. Nomeie a nova classe **SimpleEventProcessor** e clique em **Adicionar** para criar a classe.
   
    ![Adicionar classe SimpleEventProcessor](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
2. Adicione as seguintes instruções na parte superior do arquivo SimpleEventProcessor.cs file:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      using System.Diagnostics;
      ```
    
3. Substitua o seguinte código no corpo da classe:
    
      ```csharp
      class SimpleEventProcessor : IEventProcessor
      {
        Stopwatch checkpointStopWatch;
        
        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }
        
        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }
        
        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
        
                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                    context.Lease.PartitionId, data));
            }
        
            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
      }
      ```
    
      Essa classe é chamada pelo **EventProcessorHost** para processar eventos recebidos do hub de eventos. A classe `SimpleEventProcessor` usa um cronômetro para chamar o método de ponto de verificação periodicamente no contexto do **EventProcessorHost** . Esse processamento garante que, se o destinatário for reiniciado, ele perderá, no máximo, cinco minutos de trabalho de processamento.

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Atualizar o método Main para usar o SimpleEventProcessor

1. Na classe **Program**, adicione a seguinte instrução `using` na parte superior do arquivo:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      ```
    
2. Substitua o método `Main` na classe `Program` pelo código a seguir, substituindo o nome do hub de eventos e a cadeia de conexão no nível de namespace que você salvou anteriormente, e a conta de armazenamento e a chave que você copiou nas seções anteriores. 
    
      ```csharp
      static void Main(string[] args)
      {
        string eventHubConnectionString = "{Event Hubs namespace connection string}";
        string eventHubName = "{Event Hub name}";
        string storageAccountName = "{storage account name}";
        string storageAccountKey = "{storage account key}";
        string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
        
        string eventProcessorHostName = Guid.NewGuid().ToString();
        EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
        Console.WriteLine("Registering EventProcessor...");
        var options = new EventProcessorOptions();
        options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
        eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
        
        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();
        eventProcessorHost.UnregisterEventProcessorAsync().Wait();
      }
      ```
    
3. Execute o programa e certifique-se de que não existem erros.
  
## <a name="next-steps"></a>Próximas etapas
Leia os seguintes artigos: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Recursos e terminologia nos Hubs de Eventos do Azure](event-hubs-features.md).
- [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.yml)


<!-- Links -->
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Event Hubs overview]: event-hubs-about.md
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: event-hubs-event-processor-host.md
[Azure portal]: https://portal.azure.com
