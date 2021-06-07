---
title: Criar uma SAS de serviço para um contêiner ou BLOB
titleSuffix: Azure Storage
description: Saiba como criar uma assinatura de acesso compartilhado de serviço (SAS) para um contêiner ou BLOB usando a biblioteca de cliente de armazenamento de BLOBs do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/23/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 7bbe19de666fb167297de89e85bf302186a9145e
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024873"
---
# <a name="create-a-service-sas-for-a-container-or-blob"></a>Criar uma SAS de serviço para um contêiner ou BLOB

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Este artigo mostra como usar a chave da conta de armazenamento para criar uma SAS de serviço para um contêiner ou BLOB com a biblioteca de cliente de armazenamento do Azure para armazenamento de BLOBs.

## <a name="create-a-service-sas-for-a-blob-container"></a>Criar uma SAS de serviço para um contêiner de BLOBs

O exemplo de código a seguir cria uma SAS para um contêiner. Se o nome de uma política de acesso armazenada existente for fornecido, essa política está associada com a SAS. Se nenhuma política de acesso armazenada for fornecida, o código criará uma SAS ad hoc no contêiner.

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Uma SAS de serviço é assinada com a chave de acesso da conta. Use a classe [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) para criar a credencial usada para assinar a SAS. Em seguida, crie um novo objeto [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) e chame o [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) para obter a cadeia de caracteres do token SAS.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetServiceSasUriForContainer":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Para criar uma SAS de serviço para um contêiner, chame o método [CloudBlobContainer. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature) .

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, 
                                         string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define
        // the parameters of an ad hoc SAS, and to construct a shared access policy
        // that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed
            // to be the time when the storage service receives the request. Omitting
            // the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container,
        // setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case,
        // all of the constraints for the shared access signature are specified
        // on the stored access policy, which is provided by name. It is also possible
        // to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Uma SAS de serviço é assinada com a chave de acesso da conta. Use a classe [StorageSharedKeyCredential](/javascript/api/@azure/storage-blob/storagesharedkeycredential) para criar a credencial usada para assinar a SAS. Em seguida, chame a função [generateBlobSASQueryParameters](/javascript/api/@azure/storage-blob/#generateBlobSASQueryParameters_BlobSASSignatureValues__StorageSharedKeyCredential_) fornecendo os parâmetros necessários para obter a cadeia de caracteres do token SAS.

:::code language="javascript" source="~/azure-storage-snippets/blobs/howto/JavaScript/NodeJS-v12/SAS.js" id="Snippet_ContainerSAS":::

---

## <a name="create-a-service-sas-for-a-blob"></a>Criar uma SAS de serviço para um blob

O exemplo de código a seguir cria uma SAS em um blob. Se o nome de uma política de acesso armazenada existente for fornecido, essa política está associada com a SAS. Se nenhuma política de acesso armazenada for fornecida, o código criará uma SAS ad hoc no BLOB.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Uma SAS de serviço é assinada com a chave de acesso da conta. Use a classe [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) para criar a credencial usada para assinar a SAS. Em seguida, crie um novo objeto [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) e chame o [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) para obter a cadeia de caracteres do token SAS.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetServiceSasUriForBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Para criar uma SAS de serviço para um blob, chame o método [CloudBlob. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) .

```csharp
private static string GetBlobSasUri(CloudBlobContainer container,
                                    string blobName,
                                    string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters
        // of an ad hoc SAS, and to construct a shared access policy that is saved to
        // the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be
            // the time when the storage service receives the request. Omitting the start time
            // for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read |
                          SharedAccessBlobPermissions.Write |
                          SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob,
        // setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints
        // for the SAS are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Para criar uma SAS de serviço para um blob, chame o método [CloudBlob. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) .

Para criar uma SAS de serviço para um blob, chame a função [generateBlobSASQueryParameters](/javascript/api/@azure/storage-blob/#generateBlobSASQueryParameters_BlobSASSignatureValues__StorageSharedKeyCredential_) fornecendo os parâmetros necessários.

:::code language="javascript" source="~/azure-storage-snippets/blobs/howto/JavaScript/NodeJS-v12/SAS.js" id="Snippet_BlobSAS":::

---

## <a name="create-a-service-sas-for-a-directory"></a>Criar uma SAS de serviço para um diretório

Em uma conta de armazenamento com um namespace hierárquico habilitado, você pode criar uma SAS de serviço para um diretório. Para criar a SAS do serviço, verifique se você instalou a versão 12.5.0 ou posterior do pacote [Azure. Storage. files. datalake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) .

O exemplo a seguir mostra como criar uma SAS de serviço para um diretório com a biblioteca de cliente V12 para .NET:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetServiceSasUriForDirectory":::

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

[!INCLUDE [storage-blob-javascript-resources-include](../../../includes/storage-blob-javascript-resources-include.md)]

## <a name="next-steps"></a>Próximas etapas

- [Conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](../common/storage-sas-overview.md)
- [Criar uma SAS de serviço](/rest/api/storageservices/create-service-sas)
