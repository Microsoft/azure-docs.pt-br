---
title: Carregar arquivos em uma conta dos Serviços de Mídia usando o .NET | Microsoft Docs
description: Saiba como obter o conteúdo de mídia nos serviços de mídia ao criar e carregar ativos.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: c9c86380-9395-4db8-acea-507c52066f73
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 4098d55a0b7505b2178c95d612c078a427adc9a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61464208"
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>Carregar arquivos em uma conta dos Serviços de Mídia usando o .NET 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portal](media-services-portal-upload-files.md)
> 
> 

No Serviços de Mídia, você carrega (ou ingere) seus arquivos digitais em um ativo. A entidade **Asset** pode conter vídeo, áudio, imagens, coleções de miniaturas, sequências de texto e arquivos de legendas (e os metadados sobre esses arquivos).  Depois que os arquivos são carregados, o conteúdo é armazenado com segurança na nuvem para processamento adicional e transmissão.

Os arquivos no ativo são chamados **Arquivos de Ativo**. A instância de **AssetFile** e o arquivo de mídia real são dois objetos diferentes. A instância de AssetFile contém metadados sobre o arquivo de mídia, enquanto o arquivo de mídia contém o conteúdo de mídia real.

> [!NOTE]
> As seguintes considerações se aplicam:
> 
> * Os serviços de mídia usam o valor da propriedade IAssetFile.Name ao construir URLs para o conteúdo de streaming (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por esse motivo, não é permitida a codificação por porcentagem. O valor da propriedade **Name** não pode ter quaisquer dos seguintes [caracteres reservados para codificação de percentual](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Além disso, pode haver somente um '.' para a extensão de nome de arquivo.
> * O comprimento do nome não deve ser maior do que 260 caracteres.
> * Há um limite no tamanho máximo de arquivo com suporte para o processamento nos Serviços de Mídia. Confira [este](media-services-quotas-and-limitations.md) artigo para obter detalhes sobre a limitação de tamanho do arquivo.
> * Há um limite de 1.000.000 políticas para diferentes políticas de AMS (por exemplo, para política de Localizador ou ContentKeyAuthorizationPolicy). Use a mesma ID de política, se você estiver sempre usando os mesmos dias/permissões de acesso, por exemplo, políticas de localizadores que devem permanecer no local por um longo período (políticas de não carregamento). Para saber mais, confira [este artigo](media-services-dotnet-manage-entities.md#limit-access-policies).
> 

Ao criar ativos, as opções de criptografia a seguir poderão ser especificadas:

* **None** - nenhuma criptografia é usada. Esse é o valor padrão. Ao usar essa opção, seu conteúdo não é protegido quando está em trânsito ou em repouso no armazenamento.
  Se você pretende enviar um MP4 usando download progressivo, utilize essa opção: 
* **CommonEncryption** - use essa opção se você estiver carregando conteúdo que já foi criptografado e protegido com criptografia comum ou DRM PlayReady (por exemplo, Smooth Streaming protegido com DRM PlayReady).
* **EnvelopeEncrypted** – use essa opção se você estiver carregando HLS criptografado com AES. Observe que os arquivos devem ter sido codificados e criptografados pelo Gerenciador de Transformação.
* **StorageEncrypted** - criptografa o conteúdo limpo localmente usando a criptografia AES de 256 bits e, em seguida, carrega-o para o armazenamento do Azure, onde ele é armazenado, criptografado em rest. Ativos protegidos pela criptografia de armazenamento são descriptografados automaticamente e posicionados em um sistema de arquivos criptografado antes da codificação, então opcionalmente criptografados novamente antes do carregamento como um novo ativo de saída. O caso de uso primário para criptografia de armazenamento é quando você deseja proteger seus arquivos de mídia de entrada de alta qualidade com criptografia forte em repouso no disco.
  
    Os Serviços de Mídia fornecem criptografia para armazenamento em disco para seus ativos, não por conexão, como o DRM (Gerenciador de Direitos Digitais).
  
    Se seu ativo tiver o armazenamento criptografado, você deverá configurar a política de entrega de ativos. Para obter mais informações, consulte [Configurando a política de entrega de ativos](media-services-dotnet-configure-asset-delivery-policy.md).

Se você especificar para que o ativo seja criptografado com a opção **CommonEncrypted** ou uma opção **EnvelopeEncrypted**, será necessário associar o ativo a uma **ContentKey**. Para obter mais informações, consulte [Como criar uma ContentKey](media-services-dotnet-create-contentkey.md). 

Se você especificar para que o ativo seja criptografado com uma opção **StorageEncrypted**, o SDK de Serviços de Mídia para .NET criará uma **ContentKey** de **StorageEncrypted** para o ativo.

Este artigo mostra como usar o SDK de Serviços de Mídia, assim como extensões de SDK do .NET de Serviços de Mídia para carregar arquivos em um ativo de Serviços de Mídia.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Carregar um único arquivo com o SDK do .NET dos Serviços de Mídia
O código a seguir usa o .NET para carregar um único arquivo. O AccessPolicy e Localizador são criados e destruídos pela função de Carregamento. 

```csharp
        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }
```


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>Carregar vários arquivos com o SDK do .NET dos Serviços de Mídia
O código a seguir mostra como criar um ativo e carregar vários arquivos.

O código faz o seguinte:

* Cria um ativo vazio usando o método CreateEmptyAsset definido na etapa anterior.
* Cria uma instância de **AccessPolicy** que define as permissões e a duração do acesso ao ativo.
* Cria uma instância de **Locator** que fornece acesso ao ativo.
* Cria uma instância de **BlobTransferClient** . Esse tipo representa um cliente que opera nos blobs do Azure. Neste exemplo, o cliente monitora o progresso do upload. 
* Enumere os arquivos no diretório especificado e cria uma instância de **AssetFile** para cada arquivo.
* Carregue os arquivos para os serviços de mídia usando o método **UploadAsync** . 

> [!NOTE]
> Use o método UploadAsync para garantir que as chamadas não estejam bloqueadas e os arquivos sejam carregados em paralelo.
> 
> 

```csharp
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AssetFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }

    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }
```


Ao carregar um grande número de ativos, considere o seguinte:

* Criar um novo objeto **CloudMediaContext** por thread. A classe **CloudMediaContext** não é thread-safe.
* Aumente NumberOfConcurrentTransfers do valor padrão de 2 para um valor maior como 5. Configurar essa propriedade afeta todas as instâncias de **CloudMediaContext**. 
* Mantenha ParallelTransferThreadCount no valor padrão de 10.

## <a id="ingest_in_bulk"></a>Ingestão de ativos em massa usando o SDK do .NET dos Serviços de Mídia
O carregamento de grandes arquivos de ativo pode ser um gargalo durante a criação do ativo. A ingestão de ativos em massa, ou "Ingestão em massa", envolve a dissociação da criação do ativo do processo de carregamento. Para usar uma abordagem de ingestão em massa, crie um manifesto (IngestManifest) que descreve o ativo e seus arquivos associados. Em seguida, use o método de carregamento de sua escolha para carregar os arquivos associados ao contêiner de blob do manifesto. Os serviços de mídia do Microsoft Azure observa o contêiner de blob associado ao manifesto. Depois que um arquivo é carregado para o contêiner de blob, os serviços de mídia do Microsoft Azure concluem a criação do ativo com base na configuração do ativo no manifesto (IngestManifestAsset).

Para criar um novo IngestManifest, chame o método Criar exposto pela coleção IngestManifests no CloudMediaContext. Esse método cria um novo IngestManifest com o nome manifesto fornecido.

```csharp
    IIngestManifest manifest = context.IngestManifests.Create(name);
```

Crie os ativos que são associados a IngestManifest em massa. Configure as opções de criptografia desejadas no ativo para a ingestão em massa.

```csharp
    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);
```

Um IngestManifestAsset associa um ativo a um IngestManifest em massa para ingestão em massa. Ele também associa os AssetFiles que formam cada ativo. Para criar um IngestManifestAsset, use o método Criar no contexto do servidor.

O exemplo a seguir demonstra a adição de dois novos IngestManifestAssets que associam os dois ativos criados anteriormente no manifesto de ingestão em massa. Cada IngestManifestAsset associa também um conjunto de arquivos que são carregados para cada ativo durante a ingestão em massa.  

```csharp
    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
```

Você pode usar qualquer aplicativo de cliente de alta velocidade capaz de carregar os arquivos de ativo para o URI do contêiner de armazenamento de blobs fornecido pela propriedade **IIngestManifest.BlobStorageUriForUpload** do IngestManifest. 

O código a seguir mostra como usar o SDK .NET para carregar os arquivos de ativos.

```csharp
    static void UploadBlobFile(string containerName, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);

            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);

            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);

            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });

        copytask.Start();
    }
```

O código para carregar os arquivos de ativo para o exemplo usado neste artigo é mostrado no exemplo de código a seguir:

```csharp
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
```

Você pode determinar o progresso da ingestão em massa para todos os ativos associados com um **IngestManifest** sondando a propriedade Estatística do **IngestManifest**. Para atualizar informações sobre o andamento, você deve usar um novo **CloudMediaContext** sempre que sondar a propriedade Estatísticas.

O exemplo a seguir demonstra a sondagem em um IngestManifest pela sua **Id**.

```csharp
    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();

          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);

                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }

             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }
```


## <a name="upload-files-using-net-sdk-extensions"></a>Carregar arquivos usando as extensões do SDK do .NET
O exemplo a seguir mostra como carregar um único arquivo usando as Extensões do SDK do .NET. Nesse caso, o método **CreateFromFile** é usado, mas a versão assíncrona também está disponível (**CreateFromFileAsync**). O método **CreateFromFile** permite que você especifique o nome do arquivo, a opção de criptografia e um retorno de chamada para relatar o progresso do carregamento do arquivo.

```csharp
    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }
```

O exemplo a seguir chama a função UploadFile e especifica a criptografia de armazenamento como a opção de criação de ativos.  

```csharp
    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);
```

## <a name="next-steps"></a>Próximas etapas

Agora você pode codificar seus ativos carregados. Para saber mais, veja [Codificar ativos](media-services-portal-encode.md).

Você também pode usar as Azure Functions para disparar um trabalho de codificação baseado em um arquivo que chega no contêiner configurado. Para obter mais informações, confira [este exemplo](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Próxima etapa
Agora que você carregou um ativo nos Serviços de Mídia, acesse o artigo [Como obter um processador de mídia][How to Get a Media Processor].

[How to Get a Media Processor]: media-services-get-media-processor.md

