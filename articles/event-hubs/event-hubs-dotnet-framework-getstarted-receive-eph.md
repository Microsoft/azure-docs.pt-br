---
title: Receber eventos de Hubs de Eventos do Azure usando o .NET Framework | Microsoft Docs
description: Siga este tutorial para receber eventos de Hubs de Eventos do Azure usando o .NET Framework.
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/10/2017
ms.author: sethm
ms.openlocfilehash: 5d2f6f53af182a8ac0430de0ca3701a9a30e0bf4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="receive-events-from-azure-event-hubs-using-the-net-framework"></a>Receber eventos de Hubs de Eventos do Azure usando o .NET Framework

## <a name="introduction"></a>Introdução

Os Hubs de Eventos são um serviço que processa grandes quantidades de dados de eventos (telemetria) a partir de aplicativos e dispositivos conectados. Depois de coletar dados para Hubs de Eventos, você pode armazenar os dados usando um cluster de armazenamento ou transformá-los usando um provedor de análise em tempo real. Essa funcionalidade de coleta e processamento de eventos em grande escala é um componente fundamental de arquiteturas de aplicativos modernas, incluindo a IoT (Internet das Coisas).

Este tutorial mostra como escrever um aplicativo de console do .NET Framework que recebe mensagens de um hub de eventos usando o  **[Host do Processador de Eventos][EventProcessorHost]**. Para enviar eventos usando o .NET Framework, veja o artigo [Enviar eventos para os Hubs de Eventos do Azure usando o .NET Framework](event-hubs-dotnet-framework-getstarted-send.md) artigo, ou clique no idioma apropriado de envio no sumário à esquerda.

O [Host do Processador de Eventos][EventProcessorHost] é uma classe do .NET que simplifica o recebimento de eventos de hubs de eventos ao gerenciar pontos de verificação persistentes e recebimentos paralelos desses hubs de eventos. Ao usar o [Host EventProcessor][Event Processor Host], você pode dividir eventos através de vários receptores, mesmo quando hospedados em nós diferentes. Este exemplo mostra como usar o Host [EventProcessor][EventProcessorHost] para um único destinatário. O exemplo de [Processamento de eventos de escala horizontal][Scale out Event Processing with Event Hubs] mostra como usar o [Host EventProcessor][EventProcessorHost] com vários destinatários.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

* [Microsoft Visual Studio 2015 ou superior](http://visualstudio.com). As capturas de tela neste tutorial usam o Visual Studio 2017.
* Uma conta ativa do Azure. Se não tiver uma, você poderá criar uma conta gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/free/).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Como criar um namespace de hubs de eventos e um hub de eventos

A primeira etapa é usar o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo Hubs de eventos e obter as credenciais de gerenciamento das quais que seu aplicativo precisa para se comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento [neste artigo](event-hubs-create.md) e então continue com as etapas a seguir neste tutorial.

## <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

Para usar o [Host EventProcessor][EventProcessorHost], você deverá ter uma [conta do Armazenamento do Azure][Azure Storage account]:

1. Faça logon no [portal do Azure][Azure portal] e clique em **Novo** na parte superior esquerda da tela.
2. Clique em **Armazenamento** e em **conta de Armazenamento**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage1.png)
3. Na folha **Criar conta de armazenamento** , digite um nome para sua conta de armazenamento. Escolha uma assinatura do Azure, o grupo de recursos e o local no qual o recurso será criado. Em seguida, clique em **Criar**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)
4. Na lista de contas de armazenamento, clique na conta de armazenamento recém-criada.
5. Na folha da conta de Armazenamento, clique em **Chaves de acesso**. Copie o valor da **chave1** de para usar posteriormente neste tutorial.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

## <a name="create-a-receiver-console-application"></a>Criar um aplicativo de console do destinatário

1. No Visual Studio, crie um novo projeto de aplicativo de área de trabalho do Visual C# usando o modelo de projeto de **Aplicativo de Console** . Nomeie o projeto como **Destinatário**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)
2. No Gerenciador de Soluções, clique com o botão direito no projeto **Destinatário** e clique em **Gerenciar Pacotes NuGet para Solução**.
3. Clique na guia **Procurar** e procure `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Clique em **Instalar**e aceite os termos de uso.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    O Visual Studio baixa, instala e adiciona uma referência ao [Hub de Eventos do Barramento de Serviço do Azure - pacote NuGet do EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), com todas as suas dependências.
4. Clique com o botão direito do mouse no projeto do **Receptor**, clique em **Adicionar** e clique em **Classe**. Nomeie a nova classe **SimpleEventProcessor** e clique em **Adicionar** para criar a classe.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
5. Adicione as seguintes instruções na parte superior do arquivo SimpleEventProcessor.cs file:
    
  ```csharp
  using Microsoft.ServiceBus.Messaging;
  using System.Diagnostics;
  ```
    
  Em seguida, substitua o seguinte código no corpo da classe:
    
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
6. Na classe **Program**, adicione a seguinte instrução `using` na parte superior do arquivo:
    
  ```csharp
  using Microsoft.ServiceBus.Messaging;
  ```
    
  Em seguida, substitua o método `Main` na classe `Program` pelo código a seguir, substituindo o nome do hub de eventos e a cadeia de conexão no nível de namespace que você salvou anteriormente, e a conta de armazenamento e a chave que você copiou nas seções anteriores. 
    
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

7. Execute o programa e certifique-se de que não existem erros.
  
Parabéns! Agora, você recebeu mensagens de um hub de eventos usando o host do processador de eventos.


> [!NOTE]
> Este tutorial usa uma única instância do [EventProcessorHost][EventProcessorHost]. Para aumentar a taxa de transferência, é recomendável que você execute várias instâncias do [EventProcessorHost][EventProcessorHost], como mostrado no exemplo de [Processamento de eventos de escala horizontal][Processamento de eventos de escala horizontal]. Nesses casos, as diversas instâncias são coordenadas automaticamente umas com as outras para balancear a carga de eventos recebidos. Se você quiser que vários destinatários processem, cada um, *todos* os eventos, você deve usar o conceito **ConsumerGroup** . Ao receber eventos em máquinas diferentes, pode ser útil especificar nomes para instâncias de [EventProcessorHost][EventProcessorHost] com base em máquinas (ou funções) nas quais eles foram implantados. Para saber mais sobre esses tópicos, confira os tópicos [Visão geral de Hubs de Eventos][Event Hubs overview] e [Guia de programação de Hubs de Eventos][Event Hubs Programming Guide].
> 
> 

## <a name="next-steps"></a>Próximas etapas

Agora que você criou um aplicativo funcional que cria um hub de eventos e envia e recebe dados, poderá saber mais ao visitar estes links:

* [Host do Processador de Eventos][Event Processor Host]
* [Visão Geral dos Hubs de Eventos][Event Hubs overview]
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[EventProcessorHost]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Azure portal]: https://portal.azure.com
