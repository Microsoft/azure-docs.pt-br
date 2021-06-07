---
title: Copiar um blob com APIs de armazenamento do Azure
description: Saiba como copiar um BLOB usando as bibliotecas de cliente de armazenamento do Azure.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/08/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 28b23555f97e58dde038c5e3e12a4b741b81776d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98059034"
---
# <a name="copy-a-blob-with-azure-storage-client-libraries"></a>Copiar um blob com bibliotecas de cliente de armazenamento do Azure

Este artigo demonstra como copiar um blob em uma conta de armazenamento do Azure. Ele também mostra como anular uma operação de cópia assíncrona. O código de exemplo usa as bibliotecas de cliente de armazenamento do Azure.

## <a name="about-copying-blobs"></a>Sobre a cópia de BLOBs

Quando você copia um blob dentro da mesma conta de armazenamento, é uma operação síncrona. Quando você copia entre contas, é uma operação assíncrona.

O blob de origem para uma operação de cópia pode ser um blob de blocos, um blob de acréscimo, um blob de páginas ou um instantâneo. Se o blob de destino já existir, ele deverá ser do mesmo tipo do blob de origem. Um blob de destino existente será substituído.

O blob de destino não pode ser modificado enquanto uma operação de cópia está em andamento. Um blob de destino só pode ter uma operação de cópia pendente. Em outras palavras, um BLOB não pode ser o destino para várias operações de cópia pendentes.

Todo o blob de origem ou arquivo é sempre copiado. Não há suporte para a cópia de um intervalo de bytes ou um conjunto de blocos.

Quando um blob é copiado, as propriedades do sistema são copiadas para o blob de destino com os mesmos valores.

Uma operação de cópia pode ter qualquer uma das seguintes formas:

- Copie um blob de origem para um blob de destino com um nome diferente. O blob de destino pode ser um blob existente do mesmo tipo de BLOB (bloco, acrescentar ou página) ou pode ser um novo BLOB criado pela operação de cópia.
- Copie um blob de origem para um blob de destino com o mesmo nome, substituindo efetivamente o blob de destino. Essa operação de cópia remove todos os blocos não confirmados e substitui os metadados do blob de destino.
- Copie um arquivo de origem no serviço de arquivo do Azure para um blob de destino. O blob de destino pode ser um blob de blocos existente ou pode ser um novo BLOB de blocos criado pela operação de cópia. Não há suporte para a cópia de arquivos para BLOBs de páginas ou BLOBs de acréscimo.
- Copie um instantâneo em seu blob de base. Promovendo um instantâneo para a posição do blob de base, você pode restaurar uma versão anterior de um blob.
- Copie um instantâneo para um blob de destino com um nome diferente. O blob de destino resultante é um blob gravável e não um instantâneo.

## <a name="copy-a-blob"></a>Copiar um blob

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Para copiar um blob, chame um dos seguintes métodos:

- [StartCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuri)
- [StartCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuriasync)

Os `StartCopyFromUri` `StartCopyFromUriAsync` métodos e retornam um objeto [CopyFromUriOperation](/dotnet/api/azure.storage.blobs.models.copyfromurioperation) que contém informações sobre a operação de cópia.

O exemplo de código a seguir obtém um [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) que representa um blob criado anteriormente e o copia para um novo BLOB no mesmo contêiner:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_CopyBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Para copiar um blob, chame um dos seguintes métodos:

- [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy)
- [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync)

Os `StartCopy` `StartCopyAsync` métodos e retornam um valor de ID de cópia usado para verificar o status ou anular a operação de cópia.

O exemplo de código a seguir obtém uma referência a um blob criado anteriormente e o copia para um novo BLOB no mesmo contêiner:

```csharp
private static async Task CopyBlockBlobAsync(CloudBlobContainer container)
{
    CloudBlockBlob sourceBlob = null;
    CloudBlockBlob destBlob = null;
    string leaseId = null;

    try
    {
        // Get a block blob from the container to use as the source.
        sourceBlob = container.ListBlobs().OfType<CloudBlockBlob>().FirstOrDefault();

        // Lease the source blob for the copy operation 
        // to prevent another client from modifying it.
        // Specifying null for the lease interval creates an infinite lease.
        leaseId = await sourceBlob.AcquireLeaseAsync(null);

        // Get a reference to a destination blob (in this case, a new blob).
        destBlob = container.GetBlockBlobReference("copy of " + sourceBlob.Name);

        // Ensure that the source blob exists.
        if (await sourceBlob.ExistsAsync())
        {
            // Get the ID of the copy operation.
            string copyId = await destBlob.StartCopyAsync(sourceBlob);

            // Fetch the destination blob's properties before checking the copy state.
            await destBlob.FetchAttributesAsync();

            Console.WriteLine("Status of copy operation: {0}", destBlob.CopyState.Status);
            Console.WriteLine("Completion time: {0}", destBlob.CopyState.CompletionTime);
            Console.WriteLine("Bytes copied: {0}", destBlob.CopyState.BytesCopied.ToString());
            Console.WriteLine("Total bytes: {0}", destBlob.CopyState.TotalBytes.ToString());
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        // Break the lease on the source blob.
        if (sourceBlob != null)
        {
            await sourceBlob.FetchAttributesAsync();

            if (sourceBlob.Properties.LeaseState != LeaseState.Available)
            {
                await sourceBlob.BreakLeaseAsync(new TimeSpan(0));
            }
        }
    }
}
```

# <a name="python-v12"></a>[Python v12](#tab/python)

Para copiar um blob, chame o método [start_copy_from_url](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#start-copy-from-url-source-url--metadata-none--incremental-copy-false----kwargs-) . O `start_copy_from_url` método retorna um dicionário que contém informações sobre a operação de cópia.

O exemplo de código a seguir obtém um [BlobClient](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient) que representa um blob criado anteriormente e o copia para um novo BLOB no mesmo contêiner:

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_BlobCopy":::

---

## <a name="abort-a-copy-operation"></a>Anular uma operação de cópia

A anulação de uma operação de cópia resulta em um blob de destino de comprimento zero. No entanto, os metadados para o blob de destino terão os novos valores copiados do blob de origem ou definidos explicitamente durante a operação de cópia. Para manter os metadados originais antes da cópia, crie um instantâneo do blob de destino antes de chamar um dos métodos de cópia.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Verifique a propriedade [BlobProperties. CopyStatus](/dotnet/api/azure.storage.blobs.models.blobproperties.copystatus) no blob de destino para obter o status da operação de cópia. O blob final será confirmado quando a cópia for concluída.

Quando você anula uma operação de cópia, o status de cópia do blob de destino é definido como [CopyStatus. Aborted](/dotnet/api/microsoft.azure.storage.blob.copystatus).

Os métodos [AbortCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuri) e [AbortCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuriasync) cancelam uma operação de cópia em andamento.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_StopBlobCopy":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Verifique a propriedade [CopyState. status](/dotnet/api/microsoft.azure.storage.blob.copystate.status) no blob de destino para obter o status da operação de cópia. O blob final será confirmado quando a cópia for concluída.

Quando você anula uma operação de cópia, o status de cópia do blob de destino é definido como [CopyStatus. Aborted](/dotnet/api/microsoft.azure.storage.blob.copystatus).

Os métodos [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy) e [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync) cancelam uma operação de cópia em andamento.

```csharp
// Fetch the destination blob's properties before checking the copy state.
await destBlob.FetchAttributesAsync();

// Check the copy status. If it is still pending, abort the copy operation.
if (destBlob.CopyState.Status == CopyStatus.Pending)
{
    await destBlob.AbortCopyAsync(copyId);
    Console.WriteLine("Copy operation {0} has been aborted.", copyId);
}
```

# <a name="python-v12"></a>[Python v12](#tab/python)

Verifique a entrada "status" no dicionário [CopyProperties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties) retornado pelo método [get_blob_properties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#get-blob-properties---kwargs-) para obter o status da operação de cópia. O blob final será confirmado quando a cópia for concluída.

Quando você anula uma operação de cópia, o [status](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties) é definido como "abortado".

O método [abort_copy](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#abort-copy-copy-id----kwargs-) cancela uma operação de cópia em andamento.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_StopBlobCopy":::

---

## <a name="azure-sdks"></a>SDKs do Azure

Obtenha mais informações sobre SDKs do Azure:

 - [SDK do Azure para .NET](https://github.com/azure/azure-sdk-for-net)
 - [SDK do Azure para Java](https://github.com/azure/azure-sdk-for-java)
 - [SDK do Azure para Python](https://github.com/azure/azure-sdk-for-python)
 - [SDK do Azure para JavaScript](https://github.com/azure/azure-sdk-for-js)

## <a name="next-steps"></a>Próximas etapas

Os tópicos a seguir contêm informações sobre como copiar BLOBs e anular operações de cópia em andamento usando as APIs REST do Azure.

- [Copiar blob](/rest/api/storageservices/copy-blob)
- [Anular copiar Blob](/rest/api/storageservices/abort-copy-blob)
