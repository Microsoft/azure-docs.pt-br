---
title: Usar o Armazenamento de Filas do Azure para monitorar as notificações de trabalho dos Serviços de Mídia com o .NET | Microsoft Docs
description: Saiba como usar o Armazenamento de Filas do Azure para monitorar as notificações de trabalho dos Serviços de Mídia. O exemplo de código é escritos em C# e usam o SDK dos Serviços de Mídia para .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: f535d0b5-f86c-465f-81c6-177f4f490987
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 811ed6dde4ed98222bdd2589d8d6fdd8e0f64ce8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465619"
---
# <a name="use-azure-queue-storage-to-monitor-media-services-job-notifications-with-net"></a>Usar o Armazenamento de Fila do Azure para monitorar as notificações de trabalho dos Serviços de Mídia com .NET 

Quando você executa trabalhos de codificação, geralmente precisa de uma maneira de acompanhar o andamento do trabalho. Você pode configurar os serviços de mídia para entregar notificações para [Armazenamento de Filas do Azure](../../storage/storage-dotnet-how-to-use-queues.md). Você pode monitorar o andamento do trabalho obtendo notificações do Armazenamento de Filas. 

As mensagens entregues ao Armazenamento de Fila podem ser acessadas de qualquer lugar no mundo. A arquitetura do sistema de mensagens do armazenamento de Filas é confiável e altamente escalonável. É recomendável sondar o armazenamento de Filas em busca de mensagens, em vez de usar outros métodos.

Um cenário comum para escutar as notificações dos Serviços de Mídia é se você está desenvolvendo um sistema de gerenciamento de conteúdo que precisa executar alguma tarefa adicional após a conclusão de um trabalho de codificação (por exemplo, disparar a próxima etapa em um fluxo de trabalho ou publicar conteúdo).

Este artigo mostra como obter mensagens de notificação do Armazenamento de Filas.  

## <a name="considerations"></a>Considerações
Considere o seguinte ao desenvolver aplicativos dos Serviços de Mídia que usam o armazenamento de Filas:

* O Armazenamento de filas não fornece uma garantia de entrega ordenada PEPS (primeiro a entrar, primeiro a sair). Para obter mais informações, consulte [Filas do Azure e Filas do Barramento de Serviço do Azure comparadas e contrastadas](https://msdn.microsoft.com/library/azure/hh767287.aspx).
* O Armazenamento de filas não é um serviço de envio por push. Você precisa sondar a fila.
* Você pode ter qualquer número de filas. Para obter mais informações, consulte [API REST do Serviço de Fila](https://docs.microsoft.com/rest/api/storageservices/Queue-Service-REST-API).
* O Armazenamento de filas tem algumas limitações e especificações a serem consideradas. Elas são descritas em [Filas do Azure e Filas do Barramento de Serviço do Azure – semelhanças e diferenças](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted).

## <a name="net-code-example"></a>Exemplo de código do .NET

O exemplo de código nesta seção faz o seguinte:

1. Define a classe **EncodingJobMessage** que mapeia para o formato de mensagem de notificação. O código desserializa mensagens recebidas da fila em objetos do tipo **EncodingJobMessage** .
2. Carrega as informações de conta dos Serviços de Mídia e Armazenamento do arquivo app.config. O exemplo de código usa essas informações para criar os objetos **CloudMediaContext** e **CloudQueue**.
3. Cria a fila que recebe mensagens de notificação sobre o trabalho de codificação.
4. Cria o ponto de extremidade de notificação que é mapeado para a fila.
5. Anexa o ponto de extremidade de notificação ao trabalho e envia o trabalho de codificação. Você pode ter vários pontos de extremidade de notificação anexados a um trabalho.
6. Passa **NotificationJobState.FinalStatesOnly** para o método **AddNew**. (Neste exemplo, estamos interessados apenas nos estados finais do processamento do trabalho.)

        job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, _notificationEndPoint);
7. Se você passar **NotificationJobState.All**, obterá todas estas notificações de alteração de estado: na fila, agendado, em processamento e concluído. No entanto, conforme observado anteriormente, o Armazenamento de filas não garante a entrega ordenada. Para ordenar mensagens, use a propriedade **Timestamp** (definida no tipo **EncodingJobMessage** no exemplo abaixo). Há a possibilidade de mensagens duplicadas. Para verificar se há duplicatas, use a **propriedade ETag** (definida no tipo **EncodingJobMessage**). Também é possível que algumas notificações de alteração de estado sejam ignoradas.
8. Aguarda até o trabalho chegar ao estado Concluído, verificando a fila a cada 10 segundos. Exclui as mensagens depois que elas são processadas.
9. Exclui a fila e o ponto de extremidade de notificação.

> [!NOTE]
> A maneira recomendada de monitorar o estado de um trabalho é ouvir mensagens de notificação, conforme mostrado no exemplo a seguir:
>
> Como alternativa, você pode verificar o estado de um trabalho usando a propriedade **IJob.State** .  Uma mensagem de notificação sobre a conclusão de um trabalho poderá chegar antes que o estado em **IJob** seja definido como **Concluído**. A propriedade **IJob.State** reflete o estado preciso com um pequeno atraso.
>
>

### <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto do Visual Studio

1. Configure seu ambiente de desenvolvimento e preencha o arquivo de configuração app.config com as informações de conexão, conforme descrito em [Desenvolvimento de Serviços de Mídia com o .NET](media-services-dotnet-how-to-use.md). 
2. Crie uma nova pasta (a pasta pode estar em qualquer lugar na unidade local) e copie um arquivo .mp4 que você deseja codificar e transmitir ou fazer o download progressivamente. Neste exemplo, o caminho "C:\Media" é usado.
3. Adicione uma referência para a biblioteca **System.Runtime.Serialization**.

### <a name="code"></a>Código

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using System.Collections.Generic;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Queue;
using System.Runtime.Serialization.Json;

namespace JobNotification
{
    public class EncodingJobMessage
    {
        // MessageVersion is used for version control.
        public String MessageVersion { get; set; }

        // Type of the event. Valid values are
        // JobStateChange and NotificationEndpointRegistration.
        public String EventType { get; set; }

        // ETag is used to help the customer detect if
        // the message is a duplicate of another message previously sent.
        public String ETag { get; set; }

        // Time of occurrence of the event.
        public String TimeStamp { get; set; }

        // Collection of values specific to the event.

        // For the JobStateChange event the values are:
        //     JobId - Id of the Job that triggered the notification.
        //     NewState- The new state of the Job. Valid values are:
        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
        //     OldState- The old state of the Job. Valid values are:
        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished

        // For the NotificationEndpointRegistration event the values are:
        //     NotificationEndpointId- Id of the NotificationEndpoint
        //          that triggered the notification.
        //     State- The state of the Endpoint.
        //          Valid values are: Registered and Unregistered.

        public IDictionary<string, object> Properties { get; set; }
    }

    class Program
    {

        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly string _StorageConnectionString = 
            ConfigurationManager.AppSettings["StorageConnectionString"];

        private static CloudMediaContext _context = null;
        private static CloudQueue _queue = null;
        private static INotificationEndPoint _notificationEndPoint = null;

        private static readonly string _singleInputMp4Path =
            Path.GetFullPath(@"C:\Media\BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            string endPointAddress = Guid.NewGuid().ToString();

            // Create the context.
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
            
            // Create the queue that will be receiving the notification messages.
            _queue = CreateQueue(_StorageConnectionString, endPointAddress);

            // Create the notification point that is mapped to the queue.
            _notificationEndPoint =
                    _context.NotificationEndPoints.Create(
                    Guid.NewGuid().ToString(), NotificationEndPointType.AzureQueue, endPointAddress);


            if (_notificationEndPoint != null)
            {
                IJob job = SubmitEncodingJobWithNotificationEndPoint(_singleInputMp4Path);
                WaitForJobToReachedFinishedState(job.Id);
            }

            // Clean up.
            _queue.Delete();
            _notificationEndPoint.Delete();
        }


        static public CloudQueue CreateQueue(string storageAccountConnectionString, string endPointAddress)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageAccountConnectionString);

            // Create the queue client
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

            // Retrieve a reference to a queue
            CloudQueue queue = queueClient.GetQueueReference(endPointAddress);

            // Create the queue if it doesn't already exist
            queue.CreateIfNotExists();

            return queue;
        }


        public static IJob SubmitEncodingJobWithNotificationEndPoint(string inputMediaFilePath)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("My MP4 to Smooth Streaming encoding job");

            //Create an encrypted asset and upload the mp4.
            IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted,
                inputMediaFilePath);

            // Get a media processor reference, and pass to it the name of the
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task with the conversion details, using a configuration file.
            ITask task = job.Tasks.AddNew("My encoding Task",
                processor,
                "Adaptive Streaming",
                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            // Add a notification point to the job. You can add multiple notification points.  
            job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly,
                _notificationEndPoint);

            job.Submit();

            return job;
        }

        public static void WaitForJobToReachedFinishedState(string jobId)
        {
            int expectedState = (int)JobState.Finished;
            int timeOutInSeconds = 600;

            bool jobReachedExpectedState = false;
            DateTime startTime = DateTime.Now;
            int jobState = -1;

            while (!jobReachedExpectedState)
            {
                // Specify how often you want to get messages from the queue.
                Thread.Sleep(TimeSpan.FromSeconds(10));

                foreach (var message in _queue.GetMessages(10))
                {
                    using (Stream stream = new MemoryStream(message.AsBytes))
                    {
                        DataContractJsonSerializerSettings settings = new DataContractJsonSerializerSettings();
                        settings.UseSimpleDictionaryFormat = true;
                        DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(EncodingJobMessage), settings);
                        EncodingJobMessage encodingJobMsg = (EncodingJobMessage)ser.ReadObject(stream);

                        Console.WriteLine();

                        // Display the message information.
                        Console.WriteLine("EventType: {0}", encodingJobMsg.EventType);
                        Console.WriteLine("MessageVersion: {0}", encodingJobMsg.MessageVersion);
                        Console.WriteLine("ETag: {0}", encodingJobMsg.ETag);
                        Console.WriteLine("TimeStamp: {0}", encodingJobMsg.TimeStamp);
                        foreach (var property in encodingJobMsg.Properties)
                        {
                            Console.WriteLine("    {0}: {1}", property.Key, property.Value);
                        }

                        // We are only interested in messages
                        // where EventType is "JobStateChange".
                        if (encodingJobMsg.EventType == "JobStateChange")
                        {
                            string JobId = (String)encodingJobMsg.Properties.Where(j => j.Key == "JobId").FirstOrDefault().Value;
                            if (JobId == jobId)
                            {
                                string oldJobStateStr = (String)encodingJobMsg.Properties.
                                                            Where(j => j.Key == "OldState").FirstOrDefault().Value;
                                string newJobStateStr = (String)encodingJobMsg.Properties.
                                                            Where(j => j.Key == "NewState").FirstOrDefault().Value;

                                JobState oldJobState = (JobState)Enum.Parse(typeof(JobState), oldJobStateStr);
                                JobState newJobState = (JobState)Enum.Parse(typeof(JobState), newJobStateStr);

                                if (newJobState == (JobState)expectedState)
                                {
                                    Console.WriteLine("job with Id: {0} reached expected state: {1}",
                                        jobId, newJobState);
                                    jobReachedExpectedState = true;
                                    break;
                                }
                            }
                        }
                    }
                    // Delete the message after we've read it.
                    _queue.DeleteMessage(message);
                }

                // Wait until timeout
                TimeSpan timeDiff = DateTime.Now - startTime;
                bool timedOut = (timeDiff.TotalSeconds > timeOutInSeconds);
                if (timedOut)
                {
                    Console.WriteLine(@"Timeout for checking job notification messages,
                                        latest found state ='{0}', wait time = {1} secs",
                        jobState,
                        timeDiff.TotalSeconds);

                    throw new TimeoutException();
                }
            }
        }

        static private IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            var asset = _context.Assets.Create("UploadSingleFile_" + DateTime.UtcNow.ToString(),
                assetCreationOptions);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);
            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading of {0}", assetFile.Name);

            return asset;
        }

        static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

O exemplo anterior produziu a saída a seguir: Os valores variarão.

    Created assetFile BigBuckBunny.mp4
    Upload BigBuckBunny.mp4
    Done uploading of BigBuckBunny.mp4

    EventType: NotificationEndPointRegistration
    MessageVersion: 1.0
    ETag: e0238957a9b25bdf3351a88e57978d6a81a84527fad03bc23861dbe28ab293f6
    TimeStamp: 2013-05-14T20:22:37
        NotificationEndPointId: nb:nepid:UUID:d6af9412-2488-45b2-ba1f-6e0ade6dbc27
        State: Registered
        Name: dde957b2-006e-41f2-9869-a978870ac620
        Created: 2013-05-14T20:22:35

    EventType: JobStateChange
    MessageVersion: 1.0
    ETag: 4e381f37c2d844bde06ace650310284d6928b1e50101d82d1b56220cfcb6076c
    TimeStamp: 2013-05-14T20:24:40
        JobId: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54
        JobName: My MP4 to Smooth Streaming encoding job
        NewState: Finished
        OldState: Processing
        AccountName: westeuropewamsaccount
    job with Id: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54 reached expected
    State: Finished


## <a name="next-step"></a>Próxima etapa
Revise os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
