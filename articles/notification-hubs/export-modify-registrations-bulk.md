---
title: Exportar e importar registros de Hubs de notificação do Azure em massa | Microsoft Docs
description: Saiba como usar os Hubs de notificação em massa suporte para executar um grande número de operações em um hub de notificação, ou para exportar todos os registros.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/18/2019
ms.author: jowargo
ms.openlocfilehash: c24fcd5f007b641bb594bb07348491f70c03ea41
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60623979"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>Exportar e importar registros de Hubs de notificação do Azure em massa
Há cenários em que é necessário para criar ou modificar grandes números de registros em um hub de notificação. Alguns desses cenários são atualizações de marcas após computações em lote ou a migração de uma implementação de push existente para usar os Hubs de notificação.

Este artigo explica como executar um grande número de operações em um hub de notificação ou para exportar todos os registros em massa.

## <a name="high-level-flow"></a>Fluxo de alto nível
Suporte do lote foi projetado para dar suporte a trabalhos de longa execução que envolvem milhões de registros. Para alcançar essa escala, o suporte a lotes usa o armazenamento do Azure para armazenar detalhes do trabalho e saída. Para operações de atualização em massa, o usuário é necessário para criar um arquivo em um contêiner de blob, cujo conteúdo é a lista de operações de atualização de registro. Ao iniciar o trabalho, o usuário fornece uma URL para o blob de entrada, juntamente com uma URL para um diretório de saída (também em um contêiner de blob). Depois que o trabalho for iniciado, o usuário pode verificar o status consultando um local de URL fornecido no início do trabalho. Um trabalho específico só pode executar operações de um tipo específico (cria, atualiza ou exclui). Operações de exportação são executadas de maneira semelhante.

## <a name="import"></a>Importar

### <a name="set-up"></a>Configurar
Esta seção pressupõe que você tenha as seguintes entidades:

- Um hub de notificação provisionado.
- Um contêiner de blob de armazenamento do Azure.
- Referências para o [pacote NuGet do armazenamento do Azure](https://www.nuget.org/packages/windowsazure.storage/) e [pacote NuGet de Hubs de notificação](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.9).

### <a name="create-input-file-and-store-it-in-a-blob"></a>Criar arquivo de entrada e armazená-lo em um blob
Um arquivo de entrada contém uma lista de registros serializados em XML, um por linha. Usando o SDK do Azure, o exemplo de código a seguir mostra como serializar os registros e carregá-los para o contêiner de blob.

```csharp
private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
{
    StringBuilder builder = new StringBuilder();
    foreach (var registrationDescription in descriptions)
    {
        builder.AppendLine(RegistrationDescription.Serialize());
    }

    var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
    using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
    {
        inputBlob.UploadFromStream(stream);
    }
}
```

> [!IMPORTANT]
> O código anterior serializa os registros na memória e, em seguida, carrega o fluxo inteiro em um blob. Se você tiver carregado um arquivo de mais do que apenas alguns megabytes, consulte as diretrizes de BLOBs do Azure sobre como executar essas etapas; Por exemplo, [blobs de blocos](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

### <a name="create-url-tokens"></a>Criar tokens de URL
Uma vez carregado o arquivo de entrada, gere as URLs para fornecer ao seu hub de notificação para o arquivo de entrada e o diretório de saída. Você pode usar dois contêineres de blob diferente para entrada e saída.

```csharp
static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + sasContainerToken);
}

static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Read
    };
    string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + "/" + filePath + sasToken);
}
```

### <a name="submit-the-job"></a>Enviar o trabalho
Com as duas URLs de entrada e saídas, você agora pode iniciar o trabalho em lotes.

```csharp
NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
var createTask = client.SubmitNotificationHubJobAsync(
new NotificationHubJob {
        JobType = NotificationHubJobType.ImportCreateRegistrations,
        OutputContainerUri = outputContainerSasUri,
        ImportFileUri = inputFileSasUri
    }
);
createTask.Wait();

var job = createTask.Result;
long i = 10;
while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
{
    var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
    getJobTask.Wait();
    job = getJobTask.Result;
    Thread.Sleep(1000);
    i--;
}
```

Além das URLs de entrada e saídas, este exemplo cria um `NotificationHubJob` objeto que contém um `JobType` objeto, que pode ser um dos seguintes tipos:

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

Depois que a chamada for concluída, o trabalho dará continuidade ao hub de notificação e você pode verificar seu status com a chamada para [GetNotificationHubJobAsync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync?view=azure-dotnet).

Após a conclusão do trabalho, você pode inspecionar os resultados examinando os arquivos a seguir em seu diretório de saída:

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

Esses arquivos contêm a lista de operações bem-sucedidas e com falhas de seu lote. O formato de arquivo é `.cvs`, no qual cada linha tem o número da linha do arquivo de entrada original e a saída da operação (geralmente a descrição do registro criada ou atualizada).

### <a name="full-sample-code"></a>Código de exemplo completo
O código de exemplo a seguir importa registros para um hub de notificação.

```csharp
using Microsoft.Azure.NotificationHubs;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using System.Linq;
using System.Runtime.Serialization;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Xml;

namespace ConsoleApplication1
{
    class Program
    {
        private static string CONNECTION_STRING = "---";
        private static string HUB_NAME = "---";
        private static string INPUT_FILE_NAME = "CreateFile.txt";
        private static string STORAGE_ACCOUNT = "---";
        private static string STORAGE_PASSWORD = "---";
        private static StorageUri STORAGE_ENDPOINT = new StorageUri(new Uri("---"));

        static void Main(string[] args)
        {
            var descriptions = new[]
            {
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMkUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMjUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMhUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMdUxREQFBlVTTkMwMQ"),
            };

            //write to blob store to create an input file
            var blobClient = new CloudBlobClient(STORAGE_ENDPOINT, new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(STORAGE_ACCOUNT, STORAGE_PASSWORD));
            var container = blobClient.GetContainerReference("testjobs");
            container.CreateIfNotExists();

            SerializeToBlob(container, descriptions);

            // TODO then create Sas
            var outputContainerSasUri = GetOutputDirectoryUrl(container);
            var inputFileSasUri = GetInputFileUrl(container, INPUT_FILE_NAME);


            //Lets import this file
            NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
            var createTask = client.SubmitNotificationHubJobAsync(
                new NotificationHubJob {
                    JobType = NotificationHubJobType.ImportCreateRegistrations,
                    OutputContainerUri = outputContainerSasUri,
                    ImportFileUri = inputFileSasUri
                }
            );
            createTask.Wait();

            var job = createTask.Result;
            long i = 10;
            while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
            {
                var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
                getJobTask.Wait();
                job = getJobTask.Result;
                Thread.Sleep(1000);
                i--;
            }
        }

        private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
        {
            StringBuilder builder = new StringBuilder();
            foreach (var registrationDescription in descriptions)
            {
                builder.AppendLine(RegistrationDescription.Serialize());
            }

            var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
            using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
            {
                inputBlob.UploadFromStream(stream);
            }
        }

        static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + sasContainerToken);
        }

        static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + "/" + filePath + sasToken);
        }

        static string GetJobPath(string namespaceName, string notificationHubPath, string jobId)
        {
            return string.Format(CultureInfo.InvariantCulture, @"{0}//{1}/{2}/", namespaceName, notificationHubPath, jobId);
        }
    }
}
```

## <a name="export"></a>Exportação
A exportação do registro é semelhante à importação, com as seguintes diferenças:

- Você só precisa da URL de saída.
- Você cria um NotificationHubJob do tipo ExportRegistrations.

### <a name="sample-code-snippet"></a>Trecho de código de exemplo
Aqui está um trecho de código de exemplo para a exportação de registros em Java:

```java
// submit an export job
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);

// wait until the job is done
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}

```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os registros, consulte os seguintes artigos:

- [Gerenciamento de registro](notification-hubs-push-notification-registration-management.md)
- [Marcas de registros](notification-hubs-tags-segment-push-message.md)
- [Registros de modelo](notification-hubs-templates-cross-platform-push-messages.md)
