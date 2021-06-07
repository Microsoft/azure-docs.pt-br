---
title: 'Início rápido: Biblioteca do Armazenamento de Blobs do Azure v12 – Python'
description: Neste guia de início rápido, você aprenderá a usar a biblioteca de clientes do Armazenamento de Blobs do Azure versão 12 para Python a fim de criar um contêiner e um blob no Armazenamento de blobs (objeto). Em seguida, você aprenderá como baixar o blob para seu computador local e como listar todos os blobs em um contêiner.
author: twooley
ms.author: twooley
ms.date: 01/28/2021
ms.topic: quickstart
ms.service: storage
ms.subservice: blobs
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 9a6ccbe5482ac8ffe484c3155334cba1b50ba0dc
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535645"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Início Rápido: Gerenciar blobs com o SDK do Python v12

Neste início rápido, você aprenderá a gerenciar blobs usando o Python. Os blobs são objetos que podem conter grandes quantidades de texto ou dados binários, incluindo imagens, documentos, mídia de streaming e dados de arquivos. Você carregará, baixará e listará os blobs e criará e excluirá contêineres.

Mais recursos:

* [Documentação de referência da API](/python/api/azure-storage-blob)
* [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob)
* [Pacote (Índice de Pacotes do Python)](https://pypi.org/project/azure-storage-blob/)
* [Amostras](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Uma conta de armazenamento do Azure. [Criar uma conta de armazenamento](../common/storage-account-create.md).
- O [Python](https://www.python.org/downloads/) 2.7 ou 3.6+.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Configurando

Esta seção fornece instruções sobre como preparar um projeto para funcionar com a biblioteca de clientes do Armazenamento de Blobs do Azure v12 para Python.

### <a name="create-the-project"></a>Criar o projeto

Crie um aplicativo Python chamado *blob-quickstart-v12*.

1. Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para o projeto.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Alterne para o diretório *blob-quickstart-v12* recém-criado.

    ```console
    cd blob-quickstart-v12
    ```

1. No diretório *blob-quickstart-v12*, crie outro diretório chamado *dados*. Esse diretório é o local em que os arquivos de dados de blob serão criados e armazenados.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Instalar o pacote

Ainda no diretório do aplicativo, instale a biblioteca de clientes do Armazenamento de Blobs do Azure para o pacote Python usando o comando `pip install`.

```console
pip install azure-storage-blob
```

Esse comando instala a biblioteca de clientes do Armazenamento de Blobs do Azure para o pacote Python e todas as bibliotecas das quais ela depende. Nesse caso, essa é apenas a biblioteca principal do Azure para Python.

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

No diretório do projeto:

1. Abra um arquivo de texto novo no editor de código
1. Adicione instruções `import`
1. Crie a estrutura do programa, incluindo um tratamento de exceções básico

    O código é o seguinte:

    :::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/app_framework.py":::

1. Salve o novo arquivo como *blob-quickstart-v12.py* no diretório *blob-quickstart-v12*.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O Armazenamento de Blobs do Azure é otimizado para armazenar grandes quantidades de dados não estruturados. Dados não estruturados são dados que não estão de acordo com uma definição ou um modelo de dados específico, como texto ou dados binários. O Armazenamento de Blobs oferece três tipos de recursos:

* A conta de armazenamento
* Um contêiner na conta de armazenamento
* Um blob no contêiner

O diagrama a seguir mostra a relação entre esses recursos.

![Diagrama da arquitetura de Armazenamento de Blobs](./media/storage-blobs-introduction/blob1.png)

Use as classes Python a seguir para interagir com esses recursos:

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient): a classe `BlobServiceClient` permite manipular os recursos do Armazenamento do Azure e os contêineres do blob.
* [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient): a classe `ContainerClient` permite manipular os contêineres do Armazenamento do Azure e seus blobs.
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient): a classe `BlobClient` permite manipular os blobs do Armazenamento do Azure.

## <a name="code-examples"></a>Exemplos de código

Estes exemplos de snippets de código mostram como realizar as seguintes tarefas com a biblioteca de clientes do Armazenamento de Blobs do Azure para Python:

* [Obter a cadeia de conexão](#get-the-connection-string)
* [Criar um contêiner](#create-a-container)
* [Carregar blobs em um contêiner](#upload-blobs-to-a-container)
* [Listar os blobs em um contêiner](#list-the-blobs-in-a-container)
* [Baixar blobs](#download-blobs)
* [Excluir um contêiner](#delete-a-container)

### <a name="get-the-connection-string"></a>Obter a cadeia de conexão

O código abaixo recupera a cadeia de conexão da conta de armazenamento com base na variável de ambiente criada na seção [Configurar a cadeia de conexão do armazenamento](#configure-your-storage-connection-string).

Adicione esse código ao bloco `try`:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Criar um contêiner

Escolha um nome para o novo contêiner. O código abaixo anexa um valor de UUID ao nome do contêiner para garantir que ele seja exclusivo.

> [!IMPORTANT]
> Os nomes de contêiner devem estar em minúsculas. Para saber mais sobre como nomear contêineres e blobs, veja [Nomenclatura e referência de contêineres, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Crie uma instância da classe [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) chamando o método [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-). Depois chame o método [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) para realmente criar o contêiner em sua conta de armazenamento.

Adicione este código ao final do bloco `try`:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Carregar blobs em um contêiner

O trecho de código a seguir:

1. Cria um diretório local para armazenar os arquivos de dados.
1. Cria um arquivo de texto no diretório local.
1. Obtém a referência para um objeto [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) chamando o método [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) em [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) da seção [Criar um contêiner](#create-a-container).
1. Carrega o arquivo de texto local para o blob chamando o método [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-).

Adicione este código ao final do bloco `try`:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Lista os blobs no contêiner chamando o método [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-). Nesse caso, apenas um blob foi adicionado ao contêiner, portanto, a operação de listagem retorna apenas esse blob.

Adicione este código ao final do bloco `try`:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Baixar blobs

Baixa o blob criado anteriormente chamando o método [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-). O código de exemplo adiciona o sufixo "DOWNLOAD" ao nome do blob para que você possa ver os dois arquivos no sistema de arquivos local.

Adicione este código ao final do bloco `try`:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>Excluir um contêiner

O código a seguir limpa os recursos que o aplicativo criou ao remover todo o contêiner usando o método [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-). Você também pode excluir arquivos locais se desejar.

O aplicativo pausa a entrada do usuário chamando `input()` antes de excluir o blob, o contêiner e os arquivos locais. Verifique se os recursos foram criados corretamente antes de serem excluídos.

Adicione este código ao final do bloco `try`:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CleanUp":::

## <a name="run-the-code"></a>Executar o código

Este aplicativo cria um arquivo de teste na pasta local e o carrega no Armazenamento de Blobs do Azure. Depois, o exemplo lista os blobs no contêiner e baixa o arquivo com um novo nome. Você pode comparar os arquivos novos e antigos.

Navegue até o diretório que contém o arquivo *blob-quickstart-v12.py* e execute o comando `python` a seguir para executar o aplicativo.

```console
python blob-quickstart-v12.py
```

A saída do aplicativo é semelhante ao seguinte exemplo:

```output
Azure Blob Storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Antes de iniciar o processo de limpeza, verifique se os dois arquivos estão na pasta *data*. Você pode abri-los e observar se eles são idênticos.

Depois de verificar os arquivos, pressione a tecla **Enter** para excluir os arquivos de teste e concluir a demonstração.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido você aprendeu a carregar, baixar e listar blobs usando Python.

Para ver os aplicativos de exemplo de armazenamento de blobs, acesse:

> [!div class="nextstepaction"]
> [Exemplos do SDK do Armazenamento de Blobs do Azure v12 para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Para saber mais, confira as [bibliotecas de cliente do Armazenamento do Azure para Python](/azure/developer/python/sdk/storage/overview).
* Para acessar tutoriais, exemplos, inícios rápidos e outras documentações, acesse [Azure para Desenvolvedores de Python](/azure/python/).
