---
title: Criar ou excluir um contêiner de blob com .NET-armazenamento do Azure
description: Saiba como criar ou excluir um contêiner de BLOB em sua conta de armazenamento do Azure usando a biblioteca de cliente .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: fbeb6dc425709d26768a6185828b900a37c88fb6
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68235035"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>Criar ou excluir um contêiner no armazenamento do Azure com o .NET

Os BLOBs no armazenamento do Azure são organizados em contêineres. Antes de carregar um blob, você deve primeiro criar um contêiner. Este artigo mostra como criar e excluir contêineres com a [biblioteca de cliente de armazenamento do Azure para .net](/dotnet/api/overview/azure/storage/client).

## <a name="name-a-container"></a>Nomear um contêiner

Um nome de contêiner deve ser um nome DNS válido, pois ele faz parte do URI exclusivo usado para endereçar o contêiner ou seus BLOBs. Siga estas regras ao nomear um contêiner:

- Os nomes de contêiner podem ter entre 3 e 63 caracteres de comprimento.
- Os nomes de contêiner devem começar com uma letra ou número e podem conter apenas letras minúsculas, números e o caractere de traço (-).
- Dois ou mais caracteres de traço consecutivos não são permitidos em nomes de contêineres.

O URI para um contêiner está neste formato:

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Criar um contêiner

Para criar um contêiner, chame um dos seguintes métodos:

- [Criar](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [CreateIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

Os métodos **Create** e createasync lançam uma exceção se já existir um contêiner com o mesmo nome.

Os métodos **CreateIfNotExists** e **CreateIfNotExistsAsync** retornam um valor booliano que indica se o contêiner foi criado. Se já existir um contêiner com o mesmo nome, esses métodos retornarão **false** para indicar que um novo contêiner não foi criado.

Os contêineres são criados imediatamente abaixo da conta de armazenamento. Não é possível aninhar um contêiner abaixo de outro.

O exemplo a seguir cria um contêiner de forma assíncrona:

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```

## <a name="create-the-root-container"></a>Criar o contêiner raiz

Um contêiner raiz serve como um contêiner padrão para sua conta de armazenamento. Cada conta de armazenamento pode ter um contêiner raiz, que deve ser nomeado *$root.* . Você deve criar explicitamente ou excluir o contêiner raiz.

Você pode fazer referência a um blob armazenado no contêiner raiz sem incluir o nome do contêiner raiz. O contêiner raiz permite que você referencie um blob no nível superior da hierarquia da conta de armazenamento. Por exemplo, você pode fazer referência a um blob que reside no contêiner raiz da seguinte maneira:

`https://myaccount.blob.core.windows.net/default.html`

O exemplo a seguir cria o contêiner raiz de forma síncrona:

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

## <a name="delete-a-container"></a>Excluir um contêiner

Para excluir um contêiner no .NET, use um dos seguintes métodos:

- [Excluir](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)

Os métodos **delete** e **DeleteAsync** geram uma exceção se o contêiner não existir.

Os métodos **DeleteIfNotExists** e **DeleteIfNotExistsAsync** retornam um valor booliano que indica se o contêiner foi excluído. Se o contêiner especificado não existir, esses métodos retornarão **false** para indicar que o contêiner não foi excluído.

Depois de excluir um contêiner, você não pode criar um contêiner com o mesmo nome por pelo menos 30 segundos e possivelmente mais longo. Enquanto o contêiner está sendo excluído, uma tentativa de criar um contêiner com o mesmo nome falhará com o código de erro HTTP 409 (conflito). Qualquer outra operação no contêiner ou nos BLOBs que ele contém falhará com o código de erro HTTP 404 (não encontrado) enquanto o contêiner está sendo excluído.

O exemplo a seguir exclui o contêiner especificado e manipula a exceção se o contêiner não existir:

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

O exemplo a seguir mostra como excluir todos os contêineres que começam com um prefixo especificado. O exemplo interromperá a concessão se houver uma concessão existente no contêiner.

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            if (container.Properties.LeaseState == LeaseState.Leased)
            {
                await container.BreakLeaseAsync(null);
            }

            await container.DeleteAsync();
        }

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

[!INCLUDE [storage-blob-dotnet-resources](../../../includes/storage-blob-dotnet-resources.md)]

## <a name="see-also"></a>Consulte também

- [Criar operação de contêiner](/rest/api/storageservices/create-container)
- [Operação Excluir Contêiner](/rest/api/storageservices/delete-container)
