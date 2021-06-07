---
title: Listar BLOBs com APIs de armazenamento do Azure
description: Saiba como listar BLOBs em sua conta de armazenamento usando o usando as bibliotecas de cliente de armazenamento do Azure. Os exemplos de código mostram como listar blobs em uma listagem plana ou como listá-los hierarquicamente, como se eles fossem organizados em diretórios ou pastas.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/24/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: ff20b8bd0aab94cadadddbb7a4b7b32b1db1ee85
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046935"
---
# <a name="list-blobs-with-azure-storage-client-libraries"></a>Listar BLOBs com bibliotecas de cliente de armazenamento do Azure

Ao listar blobs do seu código, você pode especificar várias opções para gerenciar o modo como os resultados são retornados do Armazenamento do Azure. Você pode especificar o número de resultados a serem retornados em cada conjunto de resultados e, em seguida, recuperar os conjuntos subsequentes. Você pode especificar um prefixo para retornar os blobs cujos nomes começam com esse caractere ou cadeia de caracteres. Além disso, você pode listar os blobs em uma estrutura de listagem plana ou hierarquicamente. Uma listagem hierárquica retorna blobs como se eles estivessem organizados em pastas.

## <a name="understand-blob-listing-options"></a>Noções básicas sobre as opções de listagem de blobs

Para listar os blobs em uma conta de armazenamento, chame um destes métodos:

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

- [BlobContainerClient. getblobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs)
- [BlobContainerClient.GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync)
- [BlobContainerClient.GetBlobsByHierarchy](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchy)
- [BlobContainerClient.GetBlobsByHierarchyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchyasync)

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

- [CloudBlobClient.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

Para listar os blobs em um contêiner, chame um destes métodos:

- [CloudBlobContainer.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

# <a name="python-v12"></a>[Python v12](#tab/python)

- [ContainerClient.list_blobs](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-)

---

### <a name="manage-how-many-results-are-returned"></a>Gerenciar quantos resultados são retornados

Por padrão, uma operação de listagem retorna até 5000 resultados por vez, mas você pode especificar o número de resultados que você deseja que cada operação de listagem retorne. Os exemplos apresentados neste artigo mostram como retornar resultados em páginas.

### <a name="filter-results-with-a-prefix"></a>Filtrar resultados com um prefixo

Para filtrar a lista de BLOBs, especifique uma cadeia de caracteres para o `prefix` parâmetro. A cadeia de caracteres de prefixo pode incluir um ou mais caracteres. O Armazenamento do Azure então retorna somente os blobs cujos nomes começam com esse prefixo.

### <a name="return-metadata"></a>Retornar metadados

Você pode retornar metadados de blob com os resultados.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Especifique o valor de **metadados** para a enumeração [BlobTraits](/dotnet/api/azure.storage.blobs.models.blobtraits) .

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Especifique o valor de **metadados** para a enumeração [BlobListingDetails](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) . O Armazenamento do Azure inclui metadados com cada blob retornado, portanto, você não precisa chamar nenhum dos métodos **FetchAttributes** neste contexto para recuperar os metadados do blob.

# <a name="python-v12"></a>[Python v12](#tab/python)

Especifique `metadata` para o `include=` parâmetro ao chamar [list_blobs](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-).

---

### <a name="list-blob-versions-or-snapshots"></a>Listar versões de BLOB ou instantâneos

- Para listar versões de BLOB ou instantâneos com a biblioteca de cliente .NET V12, especifique o parâmetro [BlobStates](/dotnet/api/azure.storage.blobs.models.blobstates) com a **versão** ou o campo de **instantâneo** . As versões e os instantâneos são listados do mais antigo para o mais recente. Para obter mais informações sobre versões de listagem, consulte [listar versões de blob](versioning-enable.md#list-blob-versions).

- Para listar o número de instantâneos com a biblioteca de cliente do Python V12, especifique `num_snapshots` no `include=` parâmetro ao chamar [list_blobs](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-).

### <a name="flat-listing-versus-hierarchical-listing"></a>Listagem plana versus listagem hierárquica

Os blobs no Armazenamento do Azure são organizados em um paradigma simples em vez de um paradigma hierárquico (como um sistema de arquivos clássico). No entanto, você pode organizar blobs em *diretórios virtuais* para imitar uma estrutura de pastas. Um diretório virtual faz parte do nome do blob e é indicado pelo caractere delimitador.

Para organizar blobs em diretórios virtuais, use um caractere delimitador no nome do blob. O caractere delimitador padrão é uma barra (/), mas você pode especificar qualquer caractere como o delimitador.

Se você nomear seus blobs usando um delimitador, poderá optar por listar os blobs hierarquicamente. Para uma operação de listagem hierárquica, o Armazenamento do Azure retornará os diretórios virtuais e blobs que estiverem abaixo do objeto pai. Você pode chamar a operação de listagem recursivamente para percorrer a hierarquia, semelhante ao modo como você percorreria um sistema de arquivos clássico programaticamente.

## <a name="use-a-flat-listing"></a>Usar uma listagem plana

Por padrão, uma operação de listagem retorna blobs em uma listagem plana. Em uma listagem plana, os blobs não são organizados por diretório virtual.

O exemplo a seguir lista os blobs no contêiner especificado usando uma listagem plana, com um tamanho de segmento opcional especificado, e grava o nome do blob em uma janela de console.

Se você tiver habilitado o recurso de namespace hierárquico em sua conta, os diretórios não serão virtuais. Em vez disso, eles são objetos concretos e independentes. Portanto, os diretórios aparecem na lista como BLOBs de comprimento zero.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobsFlatListing":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Se uma operação de listagem retornar mais de 5000 BLOBs ou se o número de BLOBs disponíveis exceder o número especificado, o armazenamento do Azure retornará um token de *continuação* com a lista de BLOBs. Um token de continuação é um valor opaco que você pode usar para recuperar o próximo conjunto de resultados do Armazenamento do Azure.

Em seu código, verifique o valor do token de continuação para determinar se ele é nulo. Quando o token de continuação é nulo, significa que o conjunto de resultados está concluído. Se o token de continuação não for nulo, chame a operação de listagem novamente, passando o token de continuação para recuperar o próximo conjunto de resultados até que o token de continuação seja nulo.

```csharp
private static async Task ListBlobsFlatListingAsync(CloudBlobContainer container, int? segmentSize)
{
    BlobContinuationToken continuationToken = null;
    CloudBlob blob;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned
        // and execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(string.Empty,
                true, BlobListingDetails.Metadata, segmentSize, continuationToken, null, null);

            foreach (var blobItem in resultSegment.Results)
            {
                blob = (CloudBlob)blobItem;

                // Write out some blob properties.
                Console.WriteLine("Blob name: {0}", blob.Name);
            }

            Console.WriteLine();

           // Get the continuation token and loop until it is null.
           continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/list_blobs.py" id="Snippet_ListBlobs":::

---

A saída de exemplo deverá ser semelhante a:

```console
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

## <a name="use-a-hierarchical-listing"></a>Usar uma listagem hierárquica

Quando você chama uma operação de listagem hierarquicamente, o Armazenamento do Azure retorna os diretórios virtuais e os blobs no primeiro nível da hierarquia.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Para listar os BLOBs hierarquicamente, chame o método [BlobContainerClient. GetBlobsByHierarchy](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchy)ou [BlobContainerClient. GetBlobsByHierarchyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchyasync) .

O exemplo a seguir lista os BLOBs no contêiner especificado usando uma listagem hierárquica, com um tamanho de segmento opcional especificado, e grava o nome do blob na janela do console.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobsHierarchicalListing":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

A propriedade [Prefix](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) de cada diretório virtual é definida para que você passe o prefixo em uma chamada recursiva para recuperar o próximo diretório.

Para listar blobs hierarquicamente, defina o parâmetro `useFlatBlobListing` do método de listagem como **false**.

O exemplo a seguir lista os blobs no contêiner especificado usando uma listagem plana, com um tamanho de segmento opcional especificado, e grava o nome do blob na janela de console.

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken = null;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, continuationToken, null, null);
            foreach (var blobItem in resultSegment.Results)
            {
                // A hierarchical listing may return both virtual directories and blobs.
                if (blobItem is CloudBlobDirectory)
                {
                    dir = (CloudBlobDirectory)blobItem;

                    // Write out the prefix of the virtual directory.
                    Console.WriteLine("Virtual directory prefix: {0}", dir.Prefix);

                    // Call recursively with the prefix to traverse the virtual directory.
                    await ListBlobsHierarchicalListingAsync(container, dir.Prefix);
                }
                else
                {
                    // Write out the name of the blob.
                    blob = (CloudBlob)blobItem;

                    Console.WriteLine("Blob name: {0}", blob.Name);
                }
                Console.WriteLine();
            }

            // Get the continuation token and loop until it is null.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

# <a name="python-v12"></a>[Python v12](#tab/python)

Para listar BLOBs hierarquicamente, chame o método [walk_blobs](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.containerclient#walk-blobs-name-starts-with-none--include-none--delimiter--------kwargs-) .

O exemplo a seguir lista os BLOBs no contêiner especificado usando uma listagem hierárquica, com um tamanho de segmento opcional especificado, e grava o nome do blob na janela do console.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/list_blobs.py" id="Snippet_WalkHierarchy":::

---

A saída de exemplo deverá ser semelhante a:

```console
Virtual directory prefix: FolderA/
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt

Virtual directory prefix: FolderA/FolderB/
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt

Virtual directory prefix: FolderA/FolderB/FolderC/
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

> [!NOTE]
> Os instantâneos de blob não podem ser listados em uma operação de listagem hierárquica.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Próximas etapas

- [Listar Blobs](/rest/api/storageservices/list-blobs)
- [Enumerar recursos de blob](/rest/api/storageservices/enumerating-blob-resources)