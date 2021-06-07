---
title: 'Início Rápido: Biblioteca do Armazenamento de Blobs do Azure v12 - C++'
description: Neste início rápido, você aprenderá a usar a biblioteca de clientes do Armazenamento de Blobs do Azure versão 12 para C++ para criar um contêiner e um blob no Armazenamento de blobs (objeto). Em seguida, você aprenderá como baixar o blob para seu computador local e como listar todos os blobs em um contêiner.
author: twooley
ms.author: twooley
ms.date: 10/21/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: e6832cc835a464a2a3d17d5ed286e71b17bc980e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312273"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-c"></a>Início Rápido: Biblioteca de clientes do Armazenamento de Blobs do Azure v12 para C++

Introdução à biblioteca de clientes do Armazenamento de Blobs do Azure v12 para C++. O Armazenamento de Blobs do Azure é uma solução de armazenamento de objetos da Microsoft para a nuvem. Siga as etapas para instalar o pacote e testar o código de exemplo para tarefas básicas. O Armazenamento de Blobs é otimizado para armazenar grandes quantidades de dados não estruturados.

Use a biblioteca de clientes do Armazenamento de Blobs do Azure v12 para C++ para:

- Criar um contêiner
- Carregar um blob para o Armazenamento do Azure
- Listar todos os blobs em um contêiner
- Baixar o blob em seu computador local
- Excluir um contêiner

Recursos:

- [Documentação de referência da API](https://azure.github.io/azure-sdk-for-cpp/storage.html)
- [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage)
- [Amostras](../common/storage-samples-c-plus-plus.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Assinatura do Azure](https://azure.microsoft.com/free/)
- [Conta de Armazenamento do Azure](../common/storage-account-create.md)
- [Compilador C++](https://azure.github.io/azure-sdk/cpp_implementation.html#supported-platforms)
- [CMake](https://cmake.org/)
- [Gerenciador de pacotes Vcpkg-C e C++](https://github.com/microsoft/vcpkg/blob/master/docs/README.md)
- [LibCurl](https://curl.haxx.se/libcurl/)
- [LibXML2](http://www.xmlsoft.org/)

## <a name="setting-up"></a>Configurando

Esta seção fornece instruções sobre como preparar um projeto para funcionar com a biblioteca de clientes do Armazenamento de Blobs do Azure v12 para C++.

### <a name="install-the-packages"></a>Instalar os pacotes

Se você ainda não fez isso, instale os pacotes LibCurl e LibXML2 usando o comando `vcpkg install`.

```console
vcpkg.exe install libxml2:x64-windows curl:x64-windows
```

Siga as instruções no GitHub para adquirir e compilar o SDK [Azure para C++](https://github.com/Azure/azure-sdk-for-cpp/).

### <a name="create-the-project"></a>Criar o projeto

No Visual Studio, crie um novo aplicativo de console C++ para o Windows chamado *BlobQuickstartV12*.

:::image type="content" source="./media/quickstart-blobs-c-plus-plus/vs-create-project.jpg" alt-text="Caixa de diálogo do Visual Studio para configurar um novo aplicativo de console C++ do Windows":::

Adicione às seguintes bibliotecas ao projeto:

- libcurl.lib
- libxml2.lib
- bcrypt.lib
- Crypt32.Lib
- WS2_32.Lib
- azure-core.lib
- azure-storage-common.lib
- azure-storage-blobs.lib

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O Armazenamento de Blobs do Azure é otimizado para armazenar grandes quantidades de dados não estruturados. Dados não estruturados são dados que não estão de acordo com uma definição ou um modelo de dados específico, como texto ou dados binários. O Armazenamento de Blobs oferece três tipos de recursos:

- A conta de armazenamento
- Um contêiner na conta de armazenamento
- Um blob no contêiner

O diagrama a seguir mostra a relação entre esses recursos.

![Diagrama da arquitetura de Armazenamento de Blobs](./media/storage-blobs-introduction/blob1.png)

Use as seguintes classes C++ para interagir com esses recursos:

- [BlobServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_service_client.html): a classe `BlobServiceClient` permite manipular os recursos do Armazenamento do Azure e os contêineres do blob.
- [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html): a classe `BlobContainerClient` permite manipular os contêineres do Armazenamento do Azure e seus blobs.
- [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html): a classe `BlobClient` permite manipular os blobs do Armazenamento do Azure. É a classe base para todas as classes de blob especializadas.
- [BlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html): A classe `BlockBlobClient` permite manipular os blobs de blocos de Armazenamento do Azure.

## <a name="code-examples"></a>Exemplos de código

Esses snippets de código de exemplo mostram como fazer as seguintes tarefas com a biblioteca de clientes do Armazenamento de Blobs do Azure para C++:

- [Adicionar arquivos de inclusão](#add-include-files)
- [Obter a cadeia de conexão](#get-the-connection-string)
- [Criar um contêiner](#create-a-container)
- [Carregar blobs em um contêiner](#upload-blobs-to-a-container)
- [Listar os blobs em um contêiner](#list-the-blobs-in-a-container)
- [Baixar blobs](#download-blobs)
- [Excluir um contêiner](#delete-a-container)

### <a name="add-include-files"></a>Adicionar arquivos de inclusão

No diretório do projeto:

1. Abra o arquivo da solução *BlobQuickstartV12.sln* no Visual Studio
1. Dentro do Visual Studio, abra o arquivo de origem do *BlobQuickstartV12. cpp*
1. Remova qualquer código dentro de `main` que foi gerado automaticamente
1. Adicione instruções `#include`

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_Includes":::

### <a name="get-the-connection-string"></a>Obtenha a cadeia de conexão

O código a seguir recupera a cadeia de conexão da conta de armazenamento a partir da variável de ambiente criada em [Configurar a cadeia de conexão do armazenamento](#configure-your-storage-connection-string).

Adicione este código a `main()`:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Criar um contêiner

Crie uma instância da classe [BlobServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html) chamando a função [fromConnectionString](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ad103d1e3a7ce7c53a82da887d12ce6fe). Em seguida, chame [Create](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#a22a1e001068a4ec52bb1b6bd8b52c047) para criar o contêiner real em sua conta de armazenamento.

> [!IMPORTANT]
> Os nomes de contêiner devem estar em minúsculas. Para saber mais sobre como nomear contêineres e blobs, veja [Nomenclatura e referência de contêineres, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Adicione este código ao final de `main()`:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Carregar blobs em um contêiner

O trecho de código a seguir:

1. Declara uma string contendo "Olá, Azure!".
1. Obtém uma referência para um objeto [BlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html) chamando o método [GetBlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#acd8c68e3f37268fde0010dd478ff048f) no contêiner da seção [Criar um contêiner](#create-a-container).
1. Carrega a string para o blob chamando a função [UploadFrom](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html#af93af7e37f8806e39481596ef253f93d). Essa operação criará o blob se ele ainda não existir e o substituirá, se já existir.

Adicione este código ao final de `main()`:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_UploadBlob":::

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Liste os blobs no contêiner chamando a função [ListBlobsFlatSegment](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ac7712bc46909dc061d6bf554b496550c). Apenas um blob foi adicionado ao contêiner, portanto, a operação retorna apenas esse blob.

Adicione este código ao final de `main()`:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Baixar blobs

Obtenha as propriedades do blob carregado. Em seguida, declare e redimensione um novo objeto `std::string` usando as propriedades do blob carregado. Baixe o blob criado anteriormente no novo objeto `std::string` chamando a função [Downloadto](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#aa844f37a8c216f3cb0f27912b114c4d2) na classe base [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html). Por fim, exiba os dados de blob baixados.

Adicione este código ao final de `main()`:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DownloadBlob":::

### <a name="delete-a-blob"></a>Excluir um blob

O código a seguir exclui o blob do contêiner de Armazenamento de Blobs do Azure chamando a função [BlobClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#a621eabcc8d23893ca1eb106494198615).

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DeleteBlob":::

### <a name="delete-a-container"></a>Excluir um contêiner

O código a seguir limpa os recursos que o aplicativo criou ao excluir todo o contêiner usando [BlobContainerClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#aa6b1db52697ae92e9a1227e2e02a5178).

Adicione este código ao final de `main()`:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DeleteContainer":::

## <a name="run-the-code"></a>Executar o código

Esse aplicativo cria um contêiner e carrega um arquivo de texto para o armazenamento de Blob do Azure. Em seguida, lista os blobs no contêiner, baixa o arquivo e exibe o conteúdo do arquivo. Por fim, o aplicativo exclui o blob e o contêiner.

A saída do aplicativo é semelhante ao seguinte exemplo:

```output
Azure Blob Storage v12 - C++ quickstart sample
Creating container: myblobcontainer
Uploading blob: blob.txt
Listing blobs...
Blob name: blob.txt
Downloaded blob contents: Hello Azure!
Deleting blob: blob.txt
Deleting container: myblobcontainer
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido você aprendeu a carregar, baixar e listar blobs usando C++. Você também aprendeu a criar e excluir um contêiner de Armazenamento de Blob do Azure.

Para ver o exemplo de armazenamento de blob C++, prossiga para:

> [!div class="nextstepaction"]
> [Exemplos do SDK do Armazenamento de Blobs do Azure v12 para C++](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-blobs/sample)
