---
title: Proteger o acesso aos dados de um aplicativo na nuvem com o Armazenamento do Azure | Microsoft Docs
description: Use tokens SAS, criptografia e HTTPS para proteger os dados do aplicativo na nuvem
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 11b141617bea5962c45e1b91cf6629c2edd26c3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="secure-access-to-an-applications-data-in-the-cloud"></a>Proteger o acesso aos dados de um aplicativo na nuvem

Este tutorial é a parte três de uma série. Aprenda a proteger o acesso à conta de armazenamento. 

Na terceira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Usar tokens SAS para acessar imagens em miniatura
> * Ativar a criptografia no lado do servidor
> * Habilitar o transporte somente para HTTPS

O [Armazenamento de Blobs do Azure](../common/storage-introduction.md#blob-storage) fornece um serviço robusto para armazenar arquivos de aplicativos. Este tutorial estende o [tópico anterior][previous-tutorial] para mostrar como proteger o acesso à sua conta de armazenamento de um aplicativo Web. Quando você terminar as imagens são criptografadas e o aplicativo Web usa tokens SAS seguros para acessar as imagens em miniatura.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial você deve ter concluído o Tutorial de armazenamento anterior: [Automate resizing uploaded images using Event Grid][previous-tutorial] (Automatizar o redimensionamento de imagens carregadas usando a Grade de Eventos). 

## <a name="set-container-public-access"></a>Configurar o acesso público ao contêiner

Nesta parte da série de tutoriais, os tokens SAS são usadas para acessar as miniaturas. Nesta etapa, você define o acesso público do contêiner de _miniaturas_ como `off`.

```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \ --account-name $blobStorageAccount \ --account-key $blobStorageAccountKey \ --name thumbs  \
--public-access off
``` 

## <a name="configure-sas-tokens-for-thumbnails"></a>Configurar tokens SAS para miniaturas

Na parte um dessa série de tutoriais, o aplicativo Web estava mostrando imagens de um contêiner público. Nessa parte da série, você usa tokens de [SAS (Assinatura de Acesso Seguro)](../common/storage-dotnet-shared-access-signature-part-1.md#what-is-a-shared-access-signature) para recuperar as imagens em miniatura. Os tokens SAS permitem que você forneça acesso restrito a um contêiner ou blob com base em IP, protocolo, intervalo de tempo ou direitos permitidos.

Neste exemplo, o repositório de código-fonte usa o branch `sasTokens`, que tem um exemplo de código atualizado. Exclua a implantação existente do GitHub com o [az webapp deployment source delete](/cli/azure/webapp/deployments/source#delete). Em seguida, configure a implantação do GitHub para o aplicativo Web com o comando [az webapp deployment source config](/cli/azure/webapp/deployment/source#config).  

No comando a seguir, `<web-app>` é o nome do seu aplicativo Web.  

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch sasTokens --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

O branch `sasTokens` do repositório atualiza o arquivo `StorageHelper.cs`. Ele substitui a tarefa `GetThumbNailUrls` com o exemplo de código abaixo. A tarefa atualizada recupera as URLs de miniatura definindo uma [SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet) para especificar a hora de início, a hora de expiração e as permissões para o token SAS. Uma vez implantado, o aplicativo Web agora recupera as miniaturas com uma URL usando um token SAS. A tarefa atualizada é mostrada no exemplo a seguir:
    
```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the container
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ThumbnailContainer);

    // Set the permission of the container to public
    await container.SetPermissionsAsync(new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });

    BlobContinuationToken continuationToken = null;

    BlobResultSegment resultSegment = null;

    //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    //When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        //or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);

        foreach (var blobItem in resultSegment.Results)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            //Set the expiry time and permissions for the blob.
            //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
            //The shared access signature will be valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

            sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);

            sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);

            sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

            //Generate the shared access signature on the blob, setting the constraints directly on the signature.
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(blob.Uri + sasBlobToken);

        }

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }

    while (continuationToken != null);

    return await Task.FromResult(thumbnailUrls);
}
```

As classes, propriedades e métodos a seguir são usados na tarefa anterior:

|Classe  |Propriedades| Métodos  |
|---------|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)    |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)     | |[CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)        |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     | |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)     | |[SetPermissionsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.setpermissionsasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_SetPermissionsAsync_Microsoft_WindowsAzure_Storage_Blob_BlobContainerPermissions_) <br> [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_ListBlobsSegmentedAsync_System_String_System_Boolean_Microsoft_WindowsAzure_Storage_Blob_BlobListingDetails_System_Nullable_System_Int32__Microsoft_WindowsAzure_Storage_Blob_BlobContinuationToken_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_)       |
|[BlobContinuationToken](/dotnet/api/microsoft.windowsazure.storage.blob.blobcontinuationtoken?view=azure-dotnet)     |         |
|[BlobResultSegment](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment?view=azure-dotnet)    | [Resultados](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment.results?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobResultSegment_Results)         |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)    |         | [GetSharedAccessSignature](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_GetSharedAccessSignature_Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_)
|[SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet)     | [SharedAccessStartTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessstarttime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessStartTime)<br>[SharedAccessExpiryTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessexpirytime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessExpiryTime)<br>[Permissões](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.permissions?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_Permissions) |        |

## <a name="server-side-encryption"></a>Criptografia no servidor

[A SSE (Criptografia do Serviço de Armazenamento) do Azure](../common/storage-service-encryption.md) ajuda a proteger seus dados. A SSE criptografa os dados em repouso, tratando da criptografia, descriptografia e gerenciamento de chaves. Todos os dados são criptografados usando a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits, um dos codificadores de blocos mais potentes.

No exemplo a seguir você habilita a criptografia para blobs. Os blobs existentes criados antes de habilitar a criptografia não são criptografados. O cabeçalho `x-ms-server-encrypted` em uma solicitação para um blob mostra o status da criptografia do blob.

```azurecli-interactive
az storage account update --encryption-services blob --name <storage-account-name> --resource-group myResourceGroup
```

Carregue uma nova imagem para o aplicativo Web agora que a criptografia está habilitada.

Usando `curl` com a opção `-I` para recuperar somente os cabeçalhos, substitua seus próprios valores para `<storage-account-name>`, `<container>` e `<blob-name>`.  

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <storage-account-name> \
    --account-key <storage-account-key> \
    --container-name <container> \
    --name <blob-name> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d` \
    --output tsv)

curl https://<storage-account-name>.blob.core.windows.net/<container>/<blob-name>?$sasToken -I
```

Na resposta, observe que o cabeçalho `x-ms-server-encrypted` mostra `true`. Esse cabeçalho identifica que os dados agora são criptografados com SSE.

```
HTTP/1.1 200 OK
Content-Length: 209489
Content-Type: image/png
Last-Modified: Mon, 11 Sep 2017 19:27:42 GMT
Accept-Ranges: bytes
ETag: "0x8D4F94B2BE76D45"
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 57047db3-001e-0050-3e34-2ba769000000
x-ms-version: 2017-04-17
x-ms-lease-status: unlocked
x-ms-lease-state: available
x-ms-blob-type: BlockBlob
x-ms-server-encrypted: true
Date: Mon, 11 Sep 2017 19:27:46 GMT
```

## <a name="enable-https-only"></a>Habilitar o HTTPS apenas

Para garantir que as solicitações de dados de e para uma conta de armazenamento são seguras, você pode limitar solicitações para HTTPS apenas. Atualize o protocolo necessário da conta de armazenamento usando o comando [az storage account update](/cli/azure/storage/account#update).

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

Teste a conexão usando `curl` que usa o protocolo `HTTP`.

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

Agora que a transferência segura é necessária, você pode receber a seguinte mensagem:

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>Próximas etapas

Na parte três da série, você aprendeu como proteger o acesso à conta de armazenamento, como:

> [!div class="checklist"]
> * Usar tokens SAS para acessar imagens em miniatura
> * Ativar a criptografia no lado do servidor
> * Habilitar o transporte somente para HTTPS

Avance para a parte quatro da série para saber como monitorar e resolver problemas de um aplicativo de armazenamento de nuvem.

> [!div class="nextstepaction"]
> [Monitorar e solucionar problemas de armazenamento de aplicativos de nuvem do aplicativo](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json