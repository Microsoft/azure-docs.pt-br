---
title: Baixar grandes quantidades de dados aleatórios do Armazenamento do Azure | Microsoft Docs
description: Saiba como usar o SDK do Azure para fazer o download de grandes quantidades de dados aleatórios a partir de uma conta de Armazenamento do Microsoft Azure
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: rogarana
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 0c029abd87e1b819cc4d96e906be8824c019f433
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584967"
---
# <a name="download-large-amounts-of-random-data-from-azure-storage"></a>Fazer o download de grandes quantidades de dados aleatórios a partir do armazenamento do Microsoft Azure

Este tutorial é a parte três de uma série. Este tutorial mostra como fazer o download de grandes quantidades de dados a partir do armazenamento do Microsoft Azure.

Na terceira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Atualizar o aplicativo
> * Executar o aplicativo
> * Validar o número de conexões

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você deve ter concluído o tutorial anterior de Armazenamento: [Carregar grandes quantidades de dados aleatórios em paralelo no armazenamento do Azure][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Entre em modo remoto na sua máquina virtual

 Para criar uma sessão de área de trabalho remota com a máquina virtual, utilize o comando a seguir no seu computador local. Substitua o endereço IP pelo publicIPAdress da sua máquina virtual. Quando solicitado, insira as credenciais usadas ao criar a máquina virtual.

```console
mstsc /v:<publicIpAddress>
```

## <a name="update-the-application"></a>Atualizar o aplicativo

No tutorial anterior, você fez o upload de arquivos somente para a conta de armazenamento. Abra `D:\git\storage-dotnet-perf-scale-app\Program.cs` em um editor de texto. Substitua o método `Main` pelo exemplo a seguir. Este exemplo comenta a tarefa de upload e remove a marca de comentário da tarefa de download e da tarefa para excluir o conteúdo na conta de armazenamento ao concluir.

```csharp
public static void Main(string[] args)
{
    Console.WriteLine("Azure Blob storage performance and scalability sample");
    // Set threading and default connection limit to 100 to 
    // ensure multiple threads and connections can be opened.
    // This is in addition to parallelism with the storage 
    // client library that is defined in the functions below.
    ThreadPool.SetMinThreads(100, 4);
    ServicePointManager.DefaultConnectionLimit = 100; // (Or More)

    bool exception = false;
    try
    {
        // Call the UploadFilesAsync function.
        // await UploadFilesAsync();

        // Uncomment the following line to enable downloading of files from the storage account.
        // This is commented out initially to support the tutorial at 
        // https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files
        await DownloadFilesAsync();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        exception = true;
    }
    finally
    {
        // The following function will delete the container and all files contained in them.
        // This is commented out initially as the tutorial at 
        // https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files
        // has you upload only for one tutorial and download for the other.
        if (!exception)
        {
            // await DeleteExistingContainersAsync();
        }
        Console.WriteLine("Press any key to exit the application");
        Console.ReadKey();
    }
}
```

Depois que o aplicativo tiver sido atualizado, você precisa compilar o aplicativo novamente. Abra um `Command Prompt` e navegue até `D:\git\storage-dotnet-perf-scale-app`. Recompilar o aplicativo executando `dotnet build` como apresentado no exemplo a seguir:

```console
dotnet build
```

## <a name="run-the-application"></a>Executar o aplicativo

Agora que o aplicativo foi recompilado, é hora de executá-lo com o código atualizado. Se já não estiver aberto, abra um `Command Prompt` e navegue até `D:\git\storage-dotnet-perf-scale-app`.

Digite `dotnet run` para executar o aplicativo.

```console
dotnet run
```

A tarefa `DownloadFilesAsync` é mostrada no exemplo a seguir:

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

O aplicativo lê os contêineres localizados na conta de armazenamento especificada no **storageconnectionstring**. Ele itera os blobs usando o método [GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) e os baixa para o computador local usando o método [DownloadToAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadtoasync).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Scalable.cs" id="Snippet_DownloadFilesAsync":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

O aplicativo lê os contêineres localizados na conta de armazenamento especificada no **storageconnectionstring**. Ele itera 10 blobs por vez usando o método [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync) nos contêineres e os baixa para o computador local usando o método [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync).

A tabela a seguir mostra o [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) definido para cada blob conforme ele é baixado.

|Propriedade|Valor|Descrição|
|---|---|---|
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| Essa propriedade desabilita a verificação de hash MD5 do conteúdo carregado. Desabilitar a validação de MD5 produz uma transferência mais rápida. Mas não confirma a validade ou a integridade dos arquivos que estão sendo transferidos. |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| false| Essa propriedade determina se um hash MD5 é calculado e armazenado.   |

```csharp
private static async Task DownloadFilesAsync()
{
    CloudBlobClient blobClient = GetCloudBlobClient();

    // Define the BlobRequestOptions on the download, including disabling MD5 
    // hash validation for this example, this improves the download speed.
    BlobRequestOptions options = new BlobRequestOptions
    {
        DisableContentMD5Validation = true,
        StoreBlobContentMD5 = false
    };

    // Retrieve the list of containers in the storage account.
    // Create a directory and configure variables for use later.
    BlobContinuationToken continuationToken = null;
    List<CloudBlobContainer> containers = new List<CloudBlobContainer>();
    do
    {
        var listingResult = await blobClient.ListContainersSegmentedAsync(continuationToken);
        continuationToken = listingResult.ContinuationToken;
        containers.AddRange(listingResult.Results);
    }
    while (continuationToken != null);

    var directory = Directory.CreateDirectory("download");
    BlobResultSegment resultSegment = null;
    Stopwatch time = Stopwatch.StartNew();

    // Download the blobs
    try
    {
        List<Task> tasks = new List<Task>();
        int max_outstanding = 100;
        int completed_count = 0;

        // Create a new instance of the SemaphoreSlim class to
        // define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        // Iterate through the containers
        foreach (CloudBlobContainer container in containers)
        {
            do
            {
                // Return the blobs from the container, 10 at a time.
                resultSegment = await container.ListBlobsSegmentedAsync(null, true, BlobListingDetails.All, 10, continuationToken, null, null);
                continuationToken = resultSegment.ContinuationToken;
                {
                    foreach (var blobItem in resultSegment.Results)
                    {

                        if (((CloudBlob)blobItem).Properties.BlobType == BlobType.BlockBlob)
                        {
                            // Get the blob and add a task to download the blob asynchronously from the storage account.
                            CloudBlockBlob blockBlob = container.GetBlockBlobReference(((CloudBlockBlob)blobItem).Name);
                            Console.WriteLine("Downloading {0} from container {1}", blockBlob.Name, container.Name);
                            await sem.WaitAsync();
                            tasks.Add(blockBlob.DownloadToFileAsync(directory.FullName + "\\" + blockBlob.Name, FileMode.Create, null, options, null).ContinueWith((t) =>
                            {
                                sem.Release();
                                Interlocked.Increment(ref completed_count);
                            }));

                        }
                    }
                }
            }
            while (continuationToken != null);
        }

        // Creates an asynchronous task that completes when all the downloads complete.
        await Task.WhenAll(tasks);
    }
    catch (Exception e)
    {
        Console.WriteLine("\nError encountered during transfer: {0}", e.Message);
    }

    time.Stop();
    Console.WriteLine("Download has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());
    Console.ReadLine();
}
```

---

### <a name="validate-the-connections"></a>Validar as conexões

Enquanto os arquivos estão sendo baixados, você pode verificar o número de conexões simultâneas para a sua conta de armazenamento. Abra uma janela do console e digite `netstat -a | find /c "blob:https"`. Este comando mostra o número de conexões atualmente abertas. Como você pode ver no exemplo, mais de 280 conexões estavam abertas durante o download dos arquivos aleatórios da conta de armazenamento.

```console
C:\>netstat -a | find /c "blob:https"
289

C:\>
```

## <a name="next-steps"></a>Próximas etapas

Na parte três da série, você aprendeu a abaixar grandes volumes de dados de uma conta de armazenamento e aprendeu a:

> [!div class="checklist"]
> * Executar o aplicativo
> * Validar o número de conexões

Vá para a parte quatro da série para verificar as taxas de transferência e as métricas de latência no portal.

> [!div class="nextstepaction"]
> [Verificar a taxa de transferência e as métricas de latência no portal](storage-blob-scalable-app-verify-metrics.md)

[previous-tutorial]: storage-blob-scalable-app-upload-files.md
