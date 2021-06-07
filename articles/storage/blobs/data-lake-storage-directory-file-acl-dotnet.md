---
title: Usar o .NET para gerenciar dados no Azure Data Lake Storage Gen2
description: Use a biblioteca de cliente de armazenamento do Azure para .NET para gerenciar diretórios e arquivos em contas de armazenamento que têm o namespace hierárquico habilitado.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 52e993a22a512a94c8b5b8b050205db0c4ce0b1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100650313"
---
# <a name="use-net-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Usar o .NET para gerenciar diretórios e arquivos no Azure Data Lake Storage Gen2

Este artigo mostra como usar o .NET para criar e gerenciar diretórios e arquivos em contas de armazenamento que têm um namespace hierárquico.

Para saber mais sobre como obter, definir e atualizar as listas de controle de acesso (ACL) de diretórios e arquivos, consulte [usar o .net para gerenciar ACLs no Azure data Lake Storage Gen2](data-lake-storage-acl-dotnet.md).

[Pacote (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  [Exemplos](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  [Referência](/dotnet/api/azure.storage.files.datalake)  |  de API Mapeamento de Gen1 [para Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [Enviar comentários](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento que tem o namespace hierárquico habilitado. Siga [estas](create-data-lake-storage-account.md) instruções para criar um.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Para começar, instale o pacote NuGet [do Azure. Storage. files. datalake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) .

Para obter mais informações sobre como instalar pacotes NuGet, consulte [instalar e gerenciar pacotes no Visual Studio usando o Gerenciador de pacotes NuGet](/nuget/consume-packages/install-use-packages-visual-studio).

Em seguida, adicione essas instruções using à parte superior do seu arquivo de código.

```csharp
using Azure;
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;

```

## <a name="connect-to-the-account"></a>Conectar à conta

Para usar os trechos de código neste artigo, você precisará criar uma instância de [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) que representa a conta de armazenamento. 

### <a name="connect-by-using-an-account-key"></a>Conectar-se usando uma chave de conta

Essa é a maneira mais fácil de se conectar a uma conta. 

Este exemplo cria uma instância de [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) usando uma chave de conta.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Conectar-se usando Azure Active Directory (Azure AD)

Você pode usar a [biblioteca de cliente de identidade do Azure para .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) para autenticar seu aplicativo com o Azure AD.

Este exemplo cria uma instância de [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) usando uma ID do cliente, um segredo do cliente e uma ID de locatário.  Para obter esses valores, consulte [adquirir um token do Azure ad para autorizar solicitações de um aplicativo cliente](../common/storage-auth-aad-app.md).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Para obter mais exemplos, consulte a documentação da [biblioteca de clientes do Azure Identity para .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) .

## <a name="create-a-container"></a>Criar um contêiner

Um contêiner atua como um sistema de arquivos para seus arquivos. Você pode criar um chamando o método [DataLakeServiceClient. createfilesystem](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync) .

Este exemplo cria um contêiner chamado `my-file-system` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório chamando o método [DataLakeFileSystemClient. CreateDirectoryAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync) .

Este exemplo adiciona um diretório chamado `my-directory` a um contêiner e, em seguida, adiciona um subdiretório chamado `my-subdirectory` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Renomear ou mover um diretório

Renomeie ou mova um diretório chamando o método [DataLakeDirectoryClient. RenameAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) . Passe o caminho do diretório desejado de um parâmetro. 

Este exemplo renomeia um subdiretório para o nome `my-subdirectory-renamed` .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_RenameDirectory":::

Este exemplo move um diretório chamado `my-subdirectory-renamed` para um subdiretório de um diretório chamado `my-directory-2` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>Excluir um diretório

Exclua um diretório chamando o método [DataLakeDirectoryClient. Delete](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete) .

Este exemplo exclui um diretório chamado `my-directory`.  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Carregar um arquivo em um diretório

Primeiro, crie uma referência de arquivo no diretório de destino criando uma instância da classe [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) . Carregue um arquivo chamando o método [DataLakeFileClient. AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) . Certifique-se de concluir o carregamento chamando o método [DataLakeFileClient. FlushAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) .

Este exemplo carrega um arquivo de texto em um diretório chamado `my-directory` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFile":::

> [!TIP]
> Se o tamanho do arquivo for grande, seu código terá que fazer várias chamadas para o [DataLakeFileClient. AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). Considere usar o método [DataLakeFileClient. UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) em vez disso. Dessa forma, você pode carregar o arquivo inteiro em uma única chamada.
>
> Consulte a próxima seção para ver um exemplo.

## <a name="upload-a-large-file-to-a-directory"></a>Carregar um arquivo grande em um diretório

Use o método [DataLakeFileClient. UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) para carregar arquivos grandes sem precisar fazer várias chamadas para o método [DataLakeFileClient. AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Baixar de um diretório 

Primeiro, crie uma instância de [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) que representa o arquivo que você deseja baixar. Use o método [DataLakeFileClient. ReadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync)  e analise o valor de retorno para obter um objeto de [fluxo](/dotnet/api/system.io.stream) . Use qualquer API de processamento de arquivo .NET para salvar bytes do fluxo em um arquivo. 

Este exemplo usa um [BinaryReader](/dotnet/api/system.io.binaryreader) e um [FileStream](/dotnet/api/system.io.filestream) para salvar bytes em um arquivo. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DownloadBinaryFromDirectory":::

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Liste o conteúdo do diretório chamando o método [FileSystemClient. GetPathsAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) e, em seguida, enumerando pelos resultados.

Este exemplo, imprime os nomes de cada arquivo localizado em um diretório chamado `my-directory` .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Veja também

- [Documentação de referência da API](/dotnet/api/azure.storage.files.datalake)
- [Pacote (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [Amostras](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Mapeamento de Gen1 para Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Envie comentários](https://github.com/Azure/azure-sdk-for-net/issues)