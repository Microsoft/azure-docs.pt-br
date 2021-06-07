---
title: Proteger o acesso aos dados do aplicativo
titleSuffix: Azure Storage
description: Use tokens SAS, criptografia e HTTPS para proteger os dados do aplicativo na nuvem.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: tamram
ms.reviewer: ozgun
ms.custom: mvc, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: f7c5dbaf30965fdd5f438f0351cfa2cd60e05b70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92746555"
---
# <a name="secure-access-to-application-data"></a>Proteger o acesso aos dados do aplicativo

Este tutorial é a parte três de uma série. Aprenda a proteger o acesso à conta de armazenamento. 

Na terceira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Usar tokens SAS para acessar imagens em miniatura
> * Ativar a criptografia no lado do servidor
> * Habilitar o transporte somente para HTTPS

O [Armazenamento de Blobs do Azure](../common/storage-introduction.md#blob-storage) fornece um serviço robusto para armazenar arquivos de aplicativos. Este tutorial estende o [tópico anterior][previous-tutorial] para mostrar como proteger o acesso à sua conta de armazenamento de um aplicativo Web. Quando você terminar as imagens são criptografadas e o aplicativo Web usa tokens SAS seguros para acessar as imagens em miniatura.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você deve ter concluído o tutorial anterior de Armazenamento: [Automatizar o redimensionamento de imagens carregadas usando a Grade de Eventos][previous-tutorial].

## <a name="set-container-public-access"></a>Configurar o acesso público ao contêiner

Nesta parte da série de tutoriais, os tokens SAS são usadas para acessar as miniaturas. Nesta etapa, você define o acesso público do contêiner de *miniaturas* para `off`.

```bash
blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
    --account-name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \
    --account-name $blobStorageAccount \
    --account-key $blobStorageAccountKey \
    --name thumbnails \
    --public-access off
```

```powershell
$blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup `
    --account-name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission `
    --account-name $blobStorageAccount `
    --account-key $blobStorageAccountKey `
    --name thumbnails `
    --public-access off
```

## <a name="configure-sas-tokens-for-thumbnails"></a>Configurar tokens SAS para miniaturas

Na parte um dessa série de tutoriais, o aplicativo Web estava mostrando imagens de um contêiner público. Nesta parte da série, você usa tokens de SAS (assinaturas de acesso compartilhado) para recuperar as imagens em miniatura. Os tokens SAS permitem que você forneça acesso restrito a um contêiner ou blob com base em IP, protocolo, intervalo de tempo ou direitos permitidos. Para obter mais informações sobre SAS, confira [Conceder acesso limitado a recursos de Armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](../common/storage-sas-overview.md).

Neste exemplo, o repositório de código-fonte usa o branch `sasTokens`, que tem um exemplo de código atualizado. Exclua a implantação existente do GitHub com o [az webapp deployment source delete](/cli/azure/webapp/deployment/source). Em seguida, configure a implantação do GitHub para o aplicativo Web com o comando [az webapp deployment source config](/cli/azure/webapp/deployment/source).

No comando a seguir, `<web-app>` é o nome do seu aplicativo Web.

```bash
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
    --resource-group myResourceGroup --branch sasTokens --manual-integration \
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

```powershell
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> `
    --resource-group myResourceGroup --branch sasTokens --manual-integration `
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

O branch `sasTokens` do repositório atualiza o arquivo `StorageHelper.cs`. Ele substitui a tarefa `GetThumbNailUrls` com o exemplo de código abaixo. A tarefa atualizada recupera as URLs de miniatura usando um [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) para especificar a hora de início, a hora de expiração e as permissões para o token SAS. Uma vez implantado, o aplicativo Web agora recupera as miniaturas com uma URL usando um token SAS. A tarefa atualizada é mostrada no exemplo a seguir:

```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create a URI to the storage account
    Uri accountUri = new Uri("https://" + _storageConfig.AccountName + ".blob.core.windows.net/");

    // Create BlobServiceClient from the account URI
    BlobServiceClient blobServiceClient = new BlobServiceClient(accountUri);

    // Get reference to the container
    BlobContainerClient container = blobServiceClient.GetBlobContainerClient(_storageConfig.ThumbnailContainer);

    if (container.Exists())
    {
        // Set the expiration time and permissions for the container.
        // In this case, the start time is specified as a few 
        // minutes in the past, to mitigate clock skew.
        // The shared access signature will be valid immediately.
        BlobSasBuilder sas = new BlobSasBuilder
        {
            Resource = "c",
            BlobContainerName = _storageConfig.ThumbnailContainer,
            StartsOn = DateTimeOffset.UtcNow.AddMinutes(-5),
            ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
        };

        sas.SetPermissions(BlobContainerSasPermissions.All);

        // Create StorageSharedKeyCredentials object by reading
        // the values from the configuration (appsettings.json)
        StorageSharedKeyCredential storageCredential =
            new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

        // Create a SAS URI to the storage account
        UriBuilder sasUri = new UriBuilder(accountUri);
        sasUri.Query = sas.ToSasQueryParameters(storageCredential).ToString();

        foreach (BlobItem blob in container.GetBlobs())
        {
            // Create the URI using the SAS query token.
            string sasBlobUri = container.Uri + "/" +
                                blob.Name + sasUri.Query;

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(sasBlobUri);
        }
    }
    return await Task.FromResult(thumbnailUrls);
}
```

As classes, propriedades e métodos a seguir são usados na tarefa anterior:

| Classe | Propriedades | Métodos |
|-------|------------|---------|
|[StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) |  |  |
|[BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) |  |[GetBlobContainerClient](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainerclient) |
|[BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) | [Uri](/dotnet/api/azure.storage.blobs.blobcontainerclient.uri) |[Exists](/dotnet/api/azure.storage.blobs.blobcontainerclient.exists) <br> [GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) |
|[BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) |  | [SetPermissions](/dotnet/api/azure.storage.sas.blobsasbuilder.setpermissions) <br> [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) |
|[BlobItem](/dotnet/api/azure.storage.blobs.models.blobitem) | [Nome](/dotnet/api/azure.storage.blobs.models.blobitem.name) |  |
|[UriBuilder](/dotnet/api/system.uribuilder) | [Consulta](/dotnet/api/system.uribuilder.query) |  |
|[Lista](/dotnet/api/system.collections.generic.list-1) | | [Adicionar](/dotnet/api/system.collections.generic.list-1.add) |

## <a name="azure-storage-encryption"></a>Criptografia de Armazenamento do Azure

A [Criptografia do Armazenamento do Azure](../common/storage-service-encryption.md) ajuda a proteger e resguardar seus dados criptografando-os em repouso e manipulando a criptografia e a descriptografia. Todos os dados são criptografados usando a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)de 256 bits, um dos codificadores de blocos mais potentes.

A Microsoft pode gerenciar as chaves de criptografia ou você pode trazer suas próprias chaves com chaves gerenciadas pelo cliente armazenadas no Azure Key Vault ou no HSM (modelo de segurança de hardware) do Key Vault (versão prévia). Para obter mais informações, confira [Chaves gerenciadas pelo cliente para criptografia do Armazenamento do Azure](../common/customer-managed-keys-overview.md).

A criptografia do Armazenamento do Azure criptografa automaticamente os dados em todos os níveis de desempenho (Standard e Premium), em todos os modelos de implantação (Azure Resource Manager e Clássico) e em todos os serviços do Armazenamento do Azure (Blobs, Filas, Tabelas e Arquivos).

## <a name="enable-https-only"></a>Habilitar o HTTPS apenas

Para garantir que as solicitações de dados de e para uma conta de armazenamento são seguras, você pode limitar solicitações para HTTPS apenas. Atualize o protocolo necessário da conta de armazenamento usando o comando [az storage account update](/cli/azure/storage/account).

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
