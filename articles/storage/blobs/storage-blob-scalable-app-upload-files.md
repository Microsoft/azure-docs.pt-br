---
title: Carregar grandes quantidades de dados aleatórios em paralelo no Armazenamento do Azure | Microsoft Docs
description: Saiba como usar o SDK do Azure para carregar grandes quantidades de dados aleatórios em paralelo para uma conta do Armazenamento do Azure
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: 83a888a28c1d1e51a1fe59649dfb956cd0f72203
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071416"
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Carregar grandes quantidades de dados aleatórios em paralelo no armazenamento do Azure

Este tutorial é a parte dois de uma série. Este tutorial mostra como implantar um aplicativo que carrega grandes quantidades de dados aleatórios com uma conta de armazenamento do Azure.

Na segunda parte da série, você aprenderá como:

> [!div class="checklist"]
> * Configurar a cadeia de conexão
> * Compilar o aplicativo
> * Executar o aplicativo
> * Validar o número de conexões

O armazenamento de blobs do Azure fornece um serviço escalonável para armazenar seus dados. Para garantir que seu aplicativo tenha a melhor performance possível, é recomendável que se tenha uma compreensão de como funciona o armazenamento de blob. É importante conhecer os limites de blobs do Azure. Para saber mais sobre esses limites, visite: [metas de escalabilidade do armazenamento de blob](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets).

A [Nomenclatura da partição](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#subheading47) é outro fator potencialmente importante durante a criação de um aplicativo de alto desempenho usando blobs. Em tamanhos de bloco maiores ou iguais a quatro MiB, [blobs de bloco de alta taxa de transferência](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) são usados e a nomenclatura da partição não afetará o desempenho. Em tamanhos de bloco menores que quatro MiB, o armazenamento do Azure usa um esquema de particionamento com base em intervalo de escala e balanceamento de carga. Essa configuração significa que arquivos com convenções de nomenclatura semelhantes ou prefixos vão para a mesma partição. Essa lógica inclui o nome do contêiner em que os arquivos estão sendo carregados. Neste tutorial, você deve usar arquivos que tenham GUIDs como nomes bem como conteúdo gerado aleatoriamente. Eles são então carregados em cinco contêineres diferentes com nomes aleatórios.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você deve ter concluído o tutorial anterior de Armazenamento: [Crie uma máquina virtual e uma conta de armazenamento para um aplicativo escalonável][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>Entre em modo remoto na sua máquina virtual

Use o comando a seguir em seu computador local para criar uma sessão remota de área de trabalho com a máquina virtual. Substitua o endereço IP pelo publicIPAdress da sua máquina virtual. Quando solicitado, insira as credenciais que você usou ao criar a máquina virtual.

```
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>Configurar a cadeia de conexão

No portal do Azure, navegue até sua conta de armazenamento. Selecione **Chaves de acesso** em **Configurações** na sua conta de armazenamento. Copie a **cadeia de conexão** da chave primária ou secundária. Faça logon remoto na máquina virtual que você criou na etapa anterior. Abra um **Prompt de comando** como administrador e execute o comando `setx` com o switch `/m`. Este comando salva uma variável de ambiente de configuração de máquina. No Windows, a variável de ambiente não está disponível até que você recarregue o **Prompt de comando**. Substitua **\<storageConnectionString\>** no exemplo a seguir:

```
setx storageconnectionstring "<storageConnectionString>" /m
```

Quando terminar, abra outro **Prompt de Comando**, navegue até `D:\git\storage-dotnet-perf-scale-app` e digite `dotnet build` para compilar o aplicativo.

## <a name="run-the-application"></a>Executar o aplicativo

Navegue até `D:\git\storage-dotnet-perf-scale-app`.

Digite `dotnet run` para executar o aplicativo. Na primeira vez que você executar `dotnet`, ela preencherá o cache do pacote local, para melhorar a velocidade de restauração e habilitar o acesso offline. Esse comando leva até um minuto para ser concluído e ocorre uma única vez.

```
dotnet run
```

O aplicativo cria cinco contêineres nomeados de maneira aleatória e começa a carregar os arquivos na pasta de preparação para a conta de armazenamento. O aplicativo define o mínimo de threads como 100 e o [LimiteDeConexãoPadrão](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit(v=vs.110).aspx) como 100 para garantir que um grande número de conexões simultâneas seja permitido ao executar o aplicativo.

Além de definir as configurações de limite de thread e conexão, o [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) para o método [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromstreamasync) é configurado para usar o paralelismo e desabilitar a validação do hash MD5. Os arquivos são carregados em blocos de 100 mb. Essa configuração fornece o melhor desempenho, mas pode ser custosa se usada em uma rede de baixa performance, já que se houver uma falha o bloco inteiro de 100 mb é repetido.

|Propriedade|Valor|DESCRIÇÃO|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)| 8| A configuração divide o blob em blocos ao carregar. Para o melhor desempenho, esse valor deve ser oito vezes o número de núcleos. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| verdadeiro| Essa propriedade desabilita a verificação de hash MD5 do conteúdo carregado. Desabilitar a validação de MD5 produz uma transferência mais rápida. Mas não confirma a validade ou a integridade dos arquivos que estão sendo transferidos.   |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| falso| Essa propriedade determina se um hash MD5 é calculado e armazenado com o arquivo.   |
| [RetryPolicy](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.retrypolicy)| Retirada de 2 segundos com máximo de 10 repetições |Determina a política de repetição de solicitações. As falhas de conexão são repetidas, neste exemplo uma política de [ExponentialRetry](/dotnet/api/microsoft.azure.batch.common.exponentialretry) é configurada com uma retirada de 2 segundos, e uma contagem máxima de 10 repetições. Essa configuração é importante quando seu aplicativo chegar próximo de atingir as [metas de escalabilidade de armazenamento de blob](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets).  |

A tarefa `UploadFilesAsync` é mostrada no exemplo a seguir:

```csharp
private static async Task UploadFilesAsync()
{
    // Create random 5 characters containers to upload files to.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();
    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // path to the directory to upload
    string uploadPath = currentdir + "\\upload";
    Stopwatch time = Stopwatch.StartNew();
    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);
        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections are retried with a backoff policy. As multiple large files are being uploaded
        // large block sizes this can cause an issue if an exponential retry policy is not defined.  Additionally parallel operations are enabled with a thread count of 8
        // This could be should be multiple of the number of cores that the machine has. Lastly MD5 hash validation is disabled for this example, this improves the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };
        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            // Create random file names and set the block size that is used for the upload.
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}.", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;
            await sem.WaitAsync();

            // Create tasks for each file that is uploaded. This is added to a collection that executes them all asyncronously.  
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));
            count++;
        }

        // Creates an asynchronous task that completes when all the uploads complete.
        await Task.WhenAll(tasks);

        time.Stop();

        Console.WriteLine("Upload has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());

        Console.ReadLine();
    }
    catch (DirectoryNotFoundException ex)
    {
        Console.WriteLine("Error parsing files in the directory: {0}", ex.Message);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
}
```

O exemplo a seguir é uma saída truncada de aplicativo em execução em um sistema Windows.

```
Created container https://mystorageaccount.blob.core.windows.net/9efa7ecb-2b24-49ff-8e5b-1d25e5481076
Created container https://mystorageaccount.blob.core.windows.net/bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b
Created container https://mystorageaccount.blob.core.windows.net/9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f
Created container https://mystorageaccount.blob.core.windows.net/47646f1a-c498-40cd-9dae-840f46072180
Created container https://mystorageaccount.blob.core.windows.net/38b2cdab-45fa-4cf9-94e7-d533837365aa
Iterating in directory: D:\git\storage-dotnet-perf-scale-app\upload
Found 50 file(s)
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\1d596d16-f6de-4c4c-8058-50ebd8141e4d.txt to container 9efa7ecb-2b24-49ff-8e5b-1d25e5481076.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\242ff392-78be-41fb-b9d4-aee8152a6279.txt to container bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.txt to container 9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\45930d63-b0d0-425f-a766-cda27ff00d32.txt to container 47646f1a-c498-40cd-9dae-840f46072180.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\5129b385-5781-43be-8bac-e2fbb7d2bd82.txt to container 38b2cdab-45fa-4cf9-94e7-d533837365aa.
...
Upload has been completed in 142.0429536 seconds. Press any key to continue
```

### <a name="validate-the-connections"></a>Validar as conexões

Enquanto os arquivos estão sendo carregados, você pode verificar o número de conexões simultâneas para a sua conta de armazenamento. Abra um **Prompt de Comando** e digite `netstat -a | find /c "blob:https"`. Este comando mostra o número de conexões atualmente abertas, usando `netstat`. O exemplo a seguir mostra uma saída semelhante para o que você vê ao executar o tutorial por conta própria. Como você pode ver no exemplo, 800 conexões foram abertas durante o carregamento dos arquivos aleatórios da conta de armazenamento. Esse valor é alterado ao longo da execução do carregamento. Ao carregar em partes do bloco paralelo, a quantidade de tempo necessária para transferir o conteúdo é bastante reduzida.

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>Próximas etapas

Na parte dois da série, você aprendeu sobre o carregamento de grandes quantidades de dados aleatórios para uma conta de armazenamento em paralelo, por exemplo como:

> [!div class="checklist"]
> * Configurar a cadeia de conexão
> * Compilar o aplicativo
> * Executar o aplicativo
> * Validar o número de conexões

Avançar para a parte três da série para baixar grandes quantidades de dados de uma conta de armazenamento.

> [!div class="nextstepaction"]
> [Fazer o download de grandes quantidades de dados aleatórios a partir do armazenamento do Azure](storage-blob-scalable-app-download-files.md)

[previous-tutorial]: storage-blob-scalable-app-create-vm.md
