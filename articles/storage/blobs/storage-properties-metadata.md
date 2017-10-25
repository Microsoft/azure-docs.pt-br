---
title: Definir e recuperar as propriedades e os metadados para objetos no Armazenamento do Azure | Microsoft Docs
description: Armazene metadados personalizados em objetos no Armazenamento do Azure e defina e recupere propriedades do sistema.
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 036f9006-273e-400b-844b-3329045e9e1f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: tamram
ms.openlocfilehash: a3eb598b2dabd4986c72b8814926eb0944707050
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-and-retrieve-properties-and-metadata"></a>Definir e recuperar as propriedades e os metadados

Os objetos no armazenamento do Azure oferecem suporte às propriedades do sistema e metadados definidos pelo usuário, além dos dados que eles contêm. Este artigo discute o gerenciamento das propriedades do sistema e dos metadados definidos pelo usuário com a [Biblioteca de clientes de armazenamento do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.Storage/).

* **Propriedades do sistema**: existem propriedades do sistema em cada recurso de armazenamento. Algumas podem ser lidas ou definidas, enquanto outras são de somente leitura. Internamente, algumas propriedades do sistema correspondem a certos cabeçalhos HTTP padrão. A biblioteca de cliente do armazenamento do Azure os mantém para você.

* **Metadados definidos pelo usuário**: Os metadados definidos pelo usuário são metadados que você especifica em um determinado recurso, na forma de um par de nome-valor. Você pode usar metadados para armazenar valores adicionais com um recurso de armazenamento. Esses valores de metadados adicionais são apenas para suas próprias finalidades e não afetam o comportamento do recurso.

Recuperar os valores da propriedade e dos metadados para um recurso de armazenamento é um processo de duas etapas. Antes de ler esses valores, é preciso buscá-los explicitamente chamando o método **FetchAttributesAsync**.

> [!IMPORTANT]
> Os valores de propriedade e metadados para um recurso de armazenamento não são preenchidos, a menos que você chame um dos métodos **FetchAttributesAsync**.
>
> Você receberá um `400 Bad Request` se quaisquer pares de nome/valor contiverem caracteres não ASCII. Pares de nome/valor de metadados são cabeçalhos HTTP válidos e, portanto, devem cumprir todas as restrições que regem cabeçalhos HTTP. Portanto, recomendamos o uso da codificação de URL ou da codificação de Base64 para nomes e valores que contêm caracteres não ASCII.
>

## <a name="setting-and-retrieving-properties"></a>Configurando e recuperando as propriedades
Para recuperar os valores da propriedade, chame o método **FetchAttributesAsync** no blob ou no contêiner para preencher as propriedades e leia os valores.

Para definir as propriedades em um objeto, especifique o valor da propriedade, em seguida, chame o método **SetProperties** .

O exemplo de código a seguir cria um contêiner e escreve alguns dos valores da propriedade em uma janela do console.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

//Create the service client object for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it does not already exist.
container.CreateIfNotExists();

// Fetch container properties and write out their values.
await container.FetchAttributesAsync();
Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
Console.WriteLine("ETag: {0}", container.Properties.ETag);
Console.WriteLine();
```

## <a name="setting-and-retrieving-metadata"></a>Configurando e recuperando os metadados
Você pode especificar os metadados como um ou mais pares de nome-valor em um recurso de contêiner ou blob. Para definir os metadados, adicione pares de nome-valor à coleção **Metadados** no recurso, em seguida, chame o método **SetMetadata** para salvar os valores no serviço.

> [!NOTE]
> O nome dos metadados deve estar em conformidade com as convenções de nomenclatura para os identificadores de C#.
>
>

O exemplo de código a seguir define os metadados em um contêiner. Um valor é definido usando o método **Add** da coleção. O outro valor é definido usando a sintaxe implícita de chave/valor. Ambos são válidos.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    // Add some metadata to the container.
    container.Metadata.Add("docType", "textDocuments");
    container.Metadata["category"] = "guidance";

    // Set the container's metadata.
    await container.SetMetadataAsync();
}
```

Para recuperar os metadados, chame o método **FetchAttributes** no blob ou no contêiner para preencher a coleção **Metadados**, em seguida, leia os valores, conforme mostrado no exemplo abaixo.

```csharp
public static async Task ListContainerMetadataAsync(CloudBlobContainer container)
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
```

## <a name="next-steps"></a>Próximas etapas
* [Biblioteca do cliente de armazenamento do Azure para a referência do .NET](/dotnet/api/?term=Microsoft.WindowsAzure.Storage)
* [Biblioteca do cliente de armazenamento do Azure para o pacote NuGet do .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
