---
title: 'Início rápido: Biblioteca do Armazenamento de Blobs do Azure v12 – .NET'
description: Neste guia de início rápido, você aprenderá a usar a biblioteca de clientes do Armazenamento de Blobs do Azure versão 12 para .NET a fim de criar um contêiner e um blob no Armazenamento de Blobs (objeto). Em seguida, você aprenderá como baixar o blob para seu computador local e como listar todos os blobs em um contêiner.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: f8f27743d8680f5e73e1f7bb7a3f7bd6ff2e0464
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054713"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-net"></a>Início rápido: Biblioteca de clientes do Armazenamento de Blobs do Azure v12 para .NET

Introdução à biblioteca de clientes do Armazenamento de Blobs do Azure v12 para .NET. O Armazenamento de Blobs do Azure é uma solução de armazenamento de objetos da Microsoft para a nuvem. Siga as etapas para instalar o pacote e testar o código de exemplo para tarefas básicas. O Armazenamento de Blobs é otimizado para armazenar grandes quantidades de dados não estruturados.

Use a biblioteca de clientes do Armazenamento de Blobs do Azure v12 para .NET para:

* Criar um contêiner
* Carregar um blob para o Armazenamento do Azure
* Listar todos os blobs em um contêiner
* Baixar o blob em seu computador local
* Excluir um contêiner

Recursos adicionais:

* [Documentação de referência da API](/dotnet/api/azure.storage.blobs)
* [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)
* [Pacote (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)
* [Amostras](../common/storage-samples-dotnet.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
* Conta de armazenamento do Azure – [criar uma conta de armazenamento](../common/storage-account-create.md)
* O [SDK do .NET Core](https://dotnet.microsoft.com/download/dotnet-core) atual para o seu sistema operacional. Obtenha o SDK e não o runtime.

## <a name="setting-up"></a>Configurando

Esta seção explica como preparar um projeto para funcionar com a biblioteca de clientes do Armazenamento de Blobs do Azure v12 para .NET.

### <a name="create-the-project"></a>Criar o projeto

Crie um aplicativo .NET Core chamado *BlobQuickstartV12*.

1. Em uma janela do console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome *BlobQuickstartV12*. Esse comando cria um projeto simples C# "Olá, Mundo" com um arquivo de origem único: *Program.cs*.

   ```console
   dotnet new console -n BlobQuickstartV12
   ```

1. Alterne para o diretório *BlobQuickstartV12* recém-criado.

   ```console
   cd BlobQuickstartV12
   ```

1. No diretório *BlobQuickstartV12*, crie outro diretório chamado *dados*. É nele que os arquivos de dados de blob serão criados e armazenados.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Instalar o pacote

Ainda no diretório do aplicativo, instale a biblioteca de clientes do Armazenamento de Blobs do Azure para .NET usando o comando `dotnet add package`.

```console
dotnet add package Azure.Storage.Blobs
```

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

No diretório do projeto:

1. Abra o arquivo *Program.cs* em seu editor
1. Remova a instrução `Console.WriteLine("Hello World!");`
1. Adicione diretivas `using`
1. Atualize a declaração do método `Main` para dar suporte ao código assíncrono

O código é o seguinte:

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System;
using System.IO;
using System.Threading.Tasks;

namespace BlobQuickstartV12
{
    class Program
    {
        static async Task Main()
        {
        }
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O Armazenamento de Blobs do Azure é otimizado para armazenar grandes quantidades de dados não estruturados. Dados não estruturados são dados que não estão de acordo com uma definição ou um modelo de dados específico, como texto ou dados binários. O Armazenamento de Blobs oferece três tipos de recursos:

* A conta de armazenamento
* Um contêiner na conta de armazenamento
* Um blob no contêiner

O diagrama a seguir mostra a relação entre esses recursos.

![Diagrama da arquitetura de Armazenamento de Blobs](./media/storage-blobs-introduction/blob1.png)

Use as seguintes classes .NET para interagir com esses recursos:

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient): a classe `BlobServiceClient` permite manipular os recursos do Armazenamento do Azure e os contêineres do blob.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): a classe `BlobContainerClient` permite manipular os contêineres do Armazenamento do Azure e seus blobs.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): a classe `BlobClient` permite manipular os blobs do Armazenamento do Azure.
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): a classe `BlobDownloadInfo` representa as propriedades e o conteúdo retornados do download de um blob.

## <a name="code-examples"></a>Exemplos de código

Estes exemplos de snippets de código mostram como executar o seguinte com a biblioteca de clientes do Armazenamento de Blobs do Azure para .NET:

* [Obter a cadeia de conexão](#get-the-connection-string)
* [Criar um contêiner](#create-a-container)
* [Carregar blobs em um contêiner](#upload-blobs-to-a-container)
* [Listar os blobs em um contêiner](#list-the-blobs-in-a-container)
* [Baixar blobs](#download-blobs)
* [Excluir um contêiner](#delete-a-container)

### <a name="get-the-connection-string"></a>Obter a cadeia de conexão

O código a seguir recupera a cadeia de conexão da conta de armazenamento a partir da variável de ambiente criada na seção [Configurar a cadeia de conexão do armazenamento](#configure-your-storage-connection-string).

Adicione esse código dentro do método `Main`:

```csharp
Console.WriteLine("Azure Blob Storage v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-container"></a>Criar um contêiner

Escolha um nome para o novo contêiner. O código abaixo anexa um valor GUID ao nome do contêiner para garantir que ele seja exclusivo.

> [!IMPORTANT]
> Os nomes de contêiner devem estar em minúsculas. Para saber mais sobre como nomear contêineres e blobs, veja [Nomenclatura e referência de contêineres, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Crie uma instância da classe [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient). Em seguida, chame o método [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) para criar o contêiner em sua conta de armazenamento.

Adicione este código ao final do método `Main`:

```csharp
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

//Create a unique name for the container
string containerName = "quickstartblobs" + Guid.NewGuid().ToString();

// Create the container and return a container client object
BlobContainerClient containerClient = await blobServiceClient.CreateBlobContainerAsync(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Carregar blobs em um contêiner

O trecho de código a seguir:

1. Cria um arquivo de texto no diretório de *dados* local.
1. Obtém uma referência para um objeto [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) chamando o método [GetBlobClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobclient) no contêiner da seção [Criar um contêiner](#create-a-container).
1. Carrega o arquivo de texto local para o blob chamando o método [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync#Azure_Storage_Blobs_BlobClient_UploadAsync_System_IO_Stream_System_Boolean_System_Threading_CancellationToken_). Esse método criará o blob se ele ainda não existir e o substituirá se já existir.

Adicione este código ao final do método `Main`:

```csharp
// Create a local file in the ./data/ directory for uploading and downloading
string localPath = "./data/";
string fileName = "quickstart" + Guid.NewGuid().ToString() + ".txt";
string localFilePath = Path.Combine(localPath, fileName);

// Write text to the file
await File.WriteAllTextAsync(localFilePath, "Hello, World!");

// Get a reference to a blob
BlobClient blobClient = containerClient.GetBlobClient(fileName);

Console.WriteLine("Uploading to Blob storage as blob:\n\t {0}\n", blobClient.Uri);

// Open the file and upload its data
using FileStream uploadFileStream = File.OpenRead(localFilePath);
await blobClient.UploadAsync(uploadFileStream, true);
uploadFileStream.Close();
```

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Liste os blobs no contêiner chamando o método [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync). Nesse caso, apenas um blob foi adicionado ao contêiner, portanto, a operação de listagem retorna apenas esse blob.

Adicione este código ao final do método `Main`:

```csharp
Console.WriteLine("Listing blobs...");

// List all blobs in the container
await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
{
    Console.WriteLine("\t" + blobItem.Name);
}
```

### <a name="download-blobs"></a>Baixar blobs

Baixe o blob criado anteriormente chamando o método [DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync). O código de exemplo adiciona um sufixo "DOWNLOADED" ao nome do arquivo para que você possa ver os dois arquivos no sistema de arquivos local.

Adicione este código ao final do método `Main`:

```csharp
// Download the blob to a local file
// Append the string "DOWNLOADED" before the .txt extension 
// so you can compare the files in the data directory
string downloadFilePath = localFilePath.Replace(".txt", "DOWNLOADED.txt");

Console.WriteLine("\nDownloading blob to\n\t{0}\n", downloadFilePath);

// Download the blob's contents and save it to a file
BlobDownloadInfo download = await blobClient.DownloadAsync();

using (FileStream downloadFileStream = File.OpenWrite(downloadFilePath))
{
    await download.Content.CopyToAsync(downloadFileStream);
    downloadFileStream.Close();
}
```

### <a name="delete-a-container"></a>Excluir um contêiner

O código a seguir limpa os recursos que o aplicativo criou ao excluir todo o contêiner usando [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync). Ele também exclui os arquivos locais criados pelo aplicativo.

O aplicativo pausa a entrada do usuário chamando `Console.ReadLine` antes de excluir o blob, o contêiner e os arquivos locais. Essa é uma boa chance de verificar se os recursos foram realmente criados corretamente antes de serem excluídos.

Adicione este código ao final do método `Main`:

```csharp
// Clean up
Console.Write("Press any key to begin clean up");
Console.ReadLine();

Console.WriteLine("Deleting blob container...");
await containerClient.DeleteAsync();

Console.WriteLine("Deleting the local source and downloaded files...");
File.Delete(localFilePath);
File.Delete(downloadFilePath);

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>Executar o código

Este aplicativo cria um arquivo de teste na pasta *dados* local e o carrega no Armazenamento de Blobs. Em seguida, o exemplo lista os blobs no contêiner e baixa o arquivo com um novo nome para que você possa comparar os arquivos novos e antigos.

Navegue até seu diretório de aplicativo e, em seguida, crie e execute o aplicativo.

```console
dotnet build
```

```console
dotnet run
```

A saída do aplicativo é semelhante ao seguinte exemplo:

```output
Azure Blob Storage v12 - .NET quickstart sample

Uploading to Blob storage as blob:
         https://mystorageacct.blob.core.windows.net/quickstartblobs60c70d78-8d93-43ae-954d-8322058cfd64/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Listing blobs...
        quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Downloading blob to
        ./data/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31DOWNLOADED.txt

Press any key to begin clean up
Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Antes de iniciar o processo de limpeza, verifique se os dois arquivos estão na pasta *dados*. Você pode abri-los e ver se eles são idênticos.

Depois de verificar os arquivos, pressione a tecla **Enter** para excluir os arquivos de teste e concluir a demonstração.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido você aprendeu a carregar, baixar e listar blobs usando .NET.

Para ver os aplicativos de exemplo de armazenamento de blobs, acesse:

> [!div class="nextstepaction"]
> [Exemplos do SDK do Armazenamento de Blobs do Azure v12 para .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

* Para acessar tutoriais, exemplos, inícios rápidos e outras documentações, visite [Azure para Desenvolvedores de .NET e .NET Core](/dotnet/azure/).
* Para saber mais sobre o núcleo do .NET, confira [Introdução ao .NET em 10 minutos](https://www.microsoft.com/net/learn/get-started/).
