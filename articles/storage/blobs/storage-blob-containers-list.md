---
title: Listar contêineres de blob com .NET-armazenamento do Azure
description: Saiba como listar contêineres de BLOB em sua conta de armazenamento do Azure usando a biblioteca de cliente .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: a12fc991734fe74e450aa14a477f3a4500ba659c
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937246"
---
# <a name="list-blob-containers-with-net"></a>Listar contêineres de blob com .NET

Ao listar os contêineres em uma conta de armazenamento do Azure do seu código, você pode especificar várias opções para gerenciar como os resultados são retornados do armazenamento do Azure. Este artigo mostra como listar contêineres usando a [biblioteca de cliente de armazenamento do Azure para .net](/dotnet/api/overview/azure/storage).  

## <a name="understand-container-listing-options"></a>Entender as opções de listagem de contêiner

Para listar os contêineres em sua conta de armazenamento, chame um dos seguintes métodos:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

- [GetBlobContainers](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainers)
- [GetBlobContainersAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainersasync)

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

---

As sobrecargas para esses métodos fornecem opções adicionais para o gerenciamento de como os contêineres são retornados pela operação de listagem. Essas opções são descritas nas seções a seguir.

### <a name="manage-how-many-results-are-returned"></a>Gerenciar quantos resultados são retornados

Por padrão, uma operação de listagem retorna até 5.000 resultados por vez. Para retornar um conjunto menor de resultados, forneça um valor diferente de zero para o tamanho da página de resultados a serem retornados.

Se sua conta de armazenamento contiver mais de 5000 contêineres ou se você tiver especificado um tamanho de página, de modo que a operação de listagem retorne um subconjunto de contêineres na conta de armazenamento, o armazenamento do Azure retornará um *token de continuação* com a lista de contêineres. Um token de continuação é um valor opaco que você pode usar para recuperar o próximo conjunto de resultados do Armazenamento do Azure.

Em seu código, verifique o valor do token de continuação para determinar se ele está vazio (para .NET V12) ou NULL (para .NET v11 e anterior). Quando o token de continuação é nulo, significa que o conjunto de resultados está concluído. Se o token de continuação não for nulo, chame o método de listagem novamente, passando o token de continuação para recuperar o próximo conjunto de resultados, até que o token de continuação seja nulo.

### <a name="filter-results-with-a-prefix"></a>Filtrar resultados com um prefixo

Para filtrar a lista de contêineres, especifique uma cadeia de caracteres para o parâmetro `prefix`. A cadeia de caracteres de prefixo pode incluir um ou mais caracteres. Em seguida, o armazenamento do Azure retorna somente os contêineres cujos nomes começam com esse prefixo.

### <a name="return-metadata"></a>Retornar metadados

Para retornar metadados de contêiner com os resultados, especifique o valor de **metadados** para a enumeração [BlobContainerTraits](/dotnet/api/azure.storage.blobs.models.blobcontainertraits) (para .net V12) ou [ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) enum (para .net v11 e anterior). O armazenamento do Azure inclui metadados com cada contêiner retornado, portanto, você não precisa também buscar os metadados do contêiner.

## <a name="example-list-containers"></a>Exemplo: listar contêineres

O exemplo a seguir lista de forma assíncrona os contêineres em uma conta de armazenamento que começam com um prefixo especificado. O exemplo lista os contêineres que começam com o prefixo especificado e retorna o número especificado de resultados por chamada para a operação de listagem. Em seguida, ele usa o token de continuação para obter o próximo segmento de resultados. O exemplo também retorna metadados de contêiner com os resultados.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="Snippet_ListContainers":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix,
                                                        int? segmentSize)
{
    Console.WriteLine("List containers beginning with prefix {0}, plus container metadata:", prefix);

    BlobContinuationToken continuationToken = null;
    ContainerResultSegment resultSegment;

    try
    {
        do
        {
            // List containers beginning with the specified prefix,
            // returning segments of 5 results each.
            // Passing in null for the maxResults parameter returns the maximum number of results (up to 5000).
            // Requesting the container's metadata as part of the listing operation populates the metadata,
            // so it's not necessary to call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, segmentSize, continuationToken, null, null);

            // Enumerate the containers returned.
            foreach (var container in resultSegment.Results)
            {
                Console.WriteLine("\tContainer:" + container.Name);

                // Write the container's metadata keys and values.
                foreach (var metadataItem in container.Metadata)
                {
                    Console.WriteLine("\t\tMetadata key: " + metadataItem.Key);
                    Console.WriteLine("\t\tMetadata value: " + metadataItem.Value);
                }
            }

            // Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);

        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Confira também

- [Listar contêineres](/rest/api/storageservices/list-containers2)
- [Enumerar recursos de blob](/rest/api/storageservices/enumerating-blob-resources)
