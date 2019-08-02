---
title: Gerenciar Propriedades e metadados para um contêiner de blob com .NET-armazenamento do Azure
description: Saiba como definir e recuperar propriedades do sistema e armazenar metadados personalizados em contêineres de BLOB em sua conta de armazenamento do Azure usando a biblioteca de cliente .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/10/2019
ms.author: tamram
ms.openlocfilehash: bd745c54fb659729b1e31f9975b2e4e0b6a97a83
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68235243"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>Gerenciar Propriedades de contêiner e metadados com o .NET

Os contêineres de blob dão suporte a propriedades do sistema e metadados definidos pelo usuário, além dos dados que eles contêm. Este artigo mostra como gerenciar Propriedades do sistema e metadados definidos pelo usuário com a [biblioteca de cliente de armazenamento do Azure para .net](/dotnet/api/overview/azure/storage/client).

## <a name="about-properties-and-metadata"></a>Sobre propriedades e metadados

- **Propriedades do sistema**: As propriedades do sistema existem em cada recurso de armazenamento de BLOBs. Algumas podem ser lidas ou definidas, enquanto outras são de somente leitura. Internamente, algumas propriedades do sistema correspondem a certos cabeçalhos HTTP padrão. A biblioteca de cliente de armazenamento do Azure para .NET mantém essas propriedades para você.

- **Metadados definidos pelo usuário**: Os metadados definidos pelo usuário consistem em um ou mais pares de nome-valor que você especifica para um recurso de armazenamento de BLOBs. Você pode usar metadados para armazenar valores adicionais com o recurso. Os valores de metadados são apenas para suas próprias finalidades e não afetam o comportamento do recurso.

A recuperação de valores de propriedade e metadados para um recurso de armazenamento de BLOBs é um processo de duas etapas. Antes de poder ler esses valores, será necessário buscá-los explicitamente, chamando o método **FetchAttributes** ou **FetchAttributesAsync**. A exceção a essa regra é que os  métodos Exists e **ExistsAsync** chamam o  método fetchattributes apropriado nos bastidores. Ao chamar um desses métodos, você também não precisa chamar **fetchattributes**.

> [!IMPORTANT]
> Se você achar que a propriedade ou os valores de metadados de um recurso de armazenamento não foram preenchidos, verifique se o código chama o método **FetchAttributes** ou **FetchAttributesAsync**.

Os pares de nome/valor de metadados são cabeçalhos HTTP válidos e, portanto, devem aderir a todas as restrições que regem os cabeçalhos HTTP. Os nomes de metadados devem ser nomes de cabeçalho HTTP C# válidos e identificadores válidos, podem conter apenas caracteres ASCII e devem ser tratados como não diferenciando maiúsculas de minúsculas. Os valores de metadados que contêm caracteres não ASCII devem ser codificados em base64 ou codificados por URL.

## <a name="retrieve-container-properties"></a>Recuperar propriedades do contêiner

Para recuperar as propriedades do contêiner, chame um dos seguintes métodos:

- [FetchAttributes](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchAttributesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

O exemplo de código a seguir busca as propriedades do sistema de um contêiner e grava alguns valores de propriedade em uma janela de console:

```csharp
private static async Task ReadContainerPropertiesAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch some container properties and write out their values.
        await container.FetchAttributesAsync();
        Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri);
        Console.WriteLine("Public access level: {0}", container.Properties.PublicAccess);
        Console.WriteLine("Last modified time in UTC: {0}", container.Properties.LastModified);
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

## <a name="set-and-retrieve-metadata"></a>Definir e recuperar metadados

Você pode especificar os metadados como um ou mais pares de nome-valor em um recurso de contêiner ou blob. Para definir metadados, adicione pares de nome-valor à coleção de **metadados** no recurso e, em seguida, chame um dos seguintes métodos para gravar os valores:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

O nome dos metadados deve estar em conformidade com as convenções de nomenclatura para os identificadores de C#. Os nomes de metadados preservam o caso em que foram criados, mas não diferenciam maiúsculas de minúsculas quando definidos ou lidos. Se dois ou mais cabeçalhos de metadados com o mesmo nome forem enviados para um recurso, o armazenamento de blob retornará o código de erro HTTP 400 (solicitação inadequada).

O exemplo de código a seguir define os metadados em um contêiner. Um valor é definido usando o método **Add** da coleção. O outro valor é definido usando a sintaxe implícita de chave/valor. Ambos são válidos.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        // Set the container's metadata.
        await container.SetMetadataAsync();
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

Para recuperar metadados, chame o método **FetchAttributes** ou **FetchAttributesAsync** no blob ou contêiner para preencher a coleção de **Metadados** e leia os valores, conforme mostrado no exemplo abaixo.

```csharp
public static async Task ReadContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch container attributes in order to populate the container's properties and metadata.
        await container.FetchAttributesAsync();

        // Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
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

[!INCLUDE [storage-blob-dotnet-resources](../../../includes/storage-blob-dotnet-resources.md)]

## <a name="see-also"></a>Consulte também

- [Operação de obtenção de propriedades de contêiner](/rest/api/storageservices/get-container-properties)
- [Definir operação de metadados de contêiner](/rest/api/storageservices/set-container-metadata)
- [Operação de obtenção de metadados de contêiner](/rest/api/storageservices/set-container-metadata)
