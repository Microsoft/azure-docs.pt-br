---
title: Acesse contêineres públicos e blobs anonimamente com o .NET
titleSuffix: Azure Storage
description: Use a biblioteca de cliente de armazenamento do Azure para .NET para acessar contêineres e blobs públicos anonimamente.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/02/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 2437c5b3272163b3931d7417c84e761c591aec85
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90088809"
---
# <a name="access-public-containers-and-blobs-anonymously-with-net"></a>Acesse contêineres públicos e blobs anonimamente com o .NET

O armazenamento do Azure dá suporte ao acesso de leitura público opcional para contêineres e blobs. Os clientes podem acessar contêineres públicos e blobs anonimamente usando as bibliotecas de cliente de armazenamento do Azure, bem como usando outras ferramentas e utilitários que dão suporte ao acesso a dados para o armazenamento do Azure.

Este artigo mostra como acessar um contêiner público ou BLOB do .NET. Para obter informações sobre como configurar o acesso de leitura anônimo em um contêiner, consulte [Configurar acesso de leitura público anônimo para contêineres e blobs](anonymous-read-access-configure.md). Para obter informações sobre como impedir todo acesso anônimo a uma conta de armazenamento, consulte [impedir acesso de leitura público anônimo a contêineres e blobs](anonymous-read-access-prevent.md).

Um cliente que acessa contêineres e blobs anonimamente pode usar construtores que não necessitam de credenciais. Os exemplos a seguir mostram algumas maneiras diferentes de referenciar contêineres e blobs anonimamente.

## <a name="create-an-anonymous-client-object"></a>Criar um objeto de cliente anônimo

Você pode criar um novo objeto de cliente de serviço para acesso anônimo fornecendo o ponto de extremidade de armazenamento de BLOBs para a conta. No entanto, você também precisa saber o nome de um contêiner dessa conta que esteja disponível para acesso anônimo.

# <a name="net-v12-sdk"></a>[\.SDK do NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_CreateAnonymousBlobClient":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint for your account.
    CloudBlobClient blobClient = new CloudBlobClient(
        new Uri(@"https://storagesamples.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. 
    // Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

---

## <a name="reference-a-container-anonymously"></a>Fazer referência a um contêiner anonimamente

Se tiver a URL para um contêiner que está disponível de forma anônima, você pode usá-lo para fazer referência diretamente ao contêiner.

# <a name="net-v12-sdk"></a>[\.SDK do NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_ListBlobsAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    // Note this is only possible when the container supports full public read access.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

---

## <a name="reference-a-blob-anonymously"></a>Fazer referência a um blob anonimamente

Se tiver a URL para um blob que está disponível para acesso anônimo, você pode fazer referência ao blob diretamente usando esta URL:

# <a name="net-v12-sdk"></a>[\.SDK do NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_DownloadBlobAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

---

## <a name="next-steps"></a>Próximas etapas

- [Configurar acesso de leitura público anônimo para contêineres e blobs](anonymous-read-access-configure.md)
- [Impedir acesso de leitura público anônimo a contêineres e blobs](anonymous-read-access-prevent.md)
- [Autorizar acesso ao Armazenamento do Microsoft Azure](../common/storage-auth.md)
