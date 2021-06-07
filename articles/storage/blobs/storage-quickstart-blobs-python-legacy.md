---
title: 'Início Rápido: Biblioteca de clientes do Armazenamento de Blobs do Azure v2.1 para Python'
description: Neste início rápido, você criará uma conta de armazenamento e um contêiner no armazenamento de objeto (Blob). Em seguida, você deve usar a biblioteca de clientes de armazenamento v2.1 para Python a fim de carregar um blob no Armazenamento do Azure, baixar um blob e listar os blobs em um contêiner.
author: twooley
ms.author: twooley
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 01ca2ee1bcd551baa471dda64d636e077f6e1a82
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275440"
---
# <a name="quickstart-manage-blobs-with-python-v21-sdk"></a>Início Rápido: Gerenciar blobs com o SDK do Python v2.1

Neste início rápido, você aprenderá a gerenciar blobs usando o Python. Os blobs são objetos que podem conter grandes quantidades de texto ou dados binários, incluindo imagens, documentos, mídia de streaming e dados de arquivos. Você carregará, baixará e listará os blobs e criará e excluirá contêineres.

> [!NOTE]
> Este guia de início rápido usa uma versão herdada da biblioteca de clientes do Armazenamento de Blobs do Azure. Para começar a usar a última versão, confira [Guia de Início Rápido: Gerencie blobs com o SDK do Python v12](storage-quickstart-blobs-python.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Uma conta de armazenamento do Azure. [Criar uma conta de armazenamento](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/).
- [SDK do Armazenamento do Azure para Python](https://github.com/Azure/azure-sdk-for-python).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="download-the-sample-application"></a>Baixar o aplicativo de exemplo

O [aplicativo de exemplo](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) deste início rápido é um aplicativo Python.  

Use o comando [git](https://git-scm.com/) a seguir para baixar o aplicativo em seu ambiente de desenvolvimento. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Para examinar o programa em Python, abra o arquivo *example.py* na raiz do repositório.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento

No aplicativo, forneça o nome da conta de armazenamento e a chave de conta para criar um objeto `BlockBlobService`.

1. Abra o arquivo *example.py* no Gerenciador de Soluções na sua IDE.

1. Substitua os valores `accountname` e `accountkey` pelo nome da conta de armazenamento e chave:

    ```python
    block_blob_service = BlockBlobService(
        account_name='accountname', account_key='accountkey')
    ```

1. Salve e feche o arquivo.

## <a name="run-the-sample"></a>Execute o exemplo

O programa de exemplo cria um arquivo de teste na pasta *Documentos*, carrega o arquivo no Armazenamento de Blobs, lista os blobs no arquivo e baixa o arquivo com um novo nome.

1. Instalar as dependências:

    ```console
    pip install azure-storage-blob==2.1.0
    ```

1. Vá para o aplicativo de exemplo:

    ```console
    cd storage-blobs-python-quickstart
    ```

1. Execute o exemplo:

    ```console
    python example.py
    ```

    Você verá mensagens semelhantes à seguinte saída:
  
    ```output
    Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    List blobs in the container
             Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Downloading blob to     C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
    ```

1. Antes de continuar, acesse a pasta *Documentos* e verifique os dois arquivos.

    * *QuickStart_\<universally-unique-identifier\>*
    * *QuickStart_\<universally-unique-identifier\>_DOWNLOADED*

1. Você pode abri-los e ver que eles são idênticos.

    Você também pode usar uma ferramenta como o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com). É bom exibir os arquivos no Armazenamento de Blobs. O Gerenciador de Armazenamento do Azure é uma ferramenta multiplataforma gratuita que dá acesso às informações da conta de armazenamento. 

1. Depois de verificar os arquivos, pressione qualquer tecla para concluir o exemplo e excluir os arquivos de teste.

## <a name="learn-about-the-sample-code"></a>Aprenda sobre o código de exemplo

Agora que você sabe o que o exemplo faz, abra o arquivo *example.py* para examinar o código.

### <a name="get-references-to-the-storage-objects"></a>Obter referências a objetos de armazenamento

Nesta seção, você instancia os objetos, cria um novo contêiner e, em seguida, define as permissões no contêiner para que os blobs sejam públicos. Você chamará o contêiner `quickstartblobs`. 

```python
# Create the BlockBlockService that the system uses to call the Blob service for the storage account.
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')

# Create a container called 'quickstartblobs'.
container_name = 'quickstartblobs'
block_blob_service.create_container(container_name)

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(
    container_name, public_access=PublicAccess.Container)
```

A primeira coisa a fazer é criar as referências aos objetos usados para acessar e gerenciar o Armazenamento de Blobs. Esses objetos dependem uns dos outros, e cada um é usado pelo próximo na lista.

* Crie uma instância do objeto **BlockBlobService**, que aponta para o serviço Blob na sua conta de armazenamento. 

* Crie uma instância do objeto **CloudBlobContainer**, que representa o contêiner que você está acessando. O sistema usa contêineres para organizar seus blobs da mesma forma que você usa pastas no computador para organizar arquivos.

Depois de obter o contêiner de Blob de Nuvem, crie uma instância do objeto **CloudBlockBlob** que aponta para o blob específico desejado. Você pode carregar, baixar e copiar o blob conforme a necessidade.

> [!IMPORTANT]
> Os nomes de contêiner devem estar em minúsculas. Para obter mais informações sobre nomes de contêiner e de blobs, consulte [Nomeando e referenciando contêineres, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contêiner

O Armazenamento de Blobs dá suporte a blobs de blocos, blobs de acréscimo e blobs de páginas. Os blobs de bloco podem ter até 4,7 TB e podem ser qualquer coisa desde planilhas do Excel até arquivos de vídeo grandes. Use os blobs de acréscimo para registro em log quando quiser gravar em um arquivo e depois adicionar mais informações. Os blobs de páginas são usados principalmente para os arquivos VHD (disco rígido virtual) que dão suporte às VMs de IaaS (máquinas virtuais de infraestrutura como serviço). Blobs de bloco são os mais usados. Este início rápido usa blobs de blocos.

Para carregar um arquivo em um blob, obtenha o caminho do arquivo unindo o nome do diretório ao nome do arquivo na unidade local. Em seguida, você pode carregar o arquivo no caminho especificado usando o método `create_blob_from_path`. 

O código de exemplo cria um arquivo local que o sistema usa para upload e download, armazenando o arquivo a ser carregado como *caminho_completo_para_o_arquivo* e o nome do blob como *nome_do_arquivo_local*. Esse exemplo carrega o arquivo no seu contêiner chamado `quickstartblobs`:

```python
# Create a file in Documents to test the upload and download.
local_path = os.path.expanduser("~\Documents")
local_file_name = "QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file = os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file, 'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name.
block_blob_service.create_blob_from_path(
    container_name, local_file_name, full_path_to_file)
```

Há vários métodos de upload que você pode usar com o Armazenamento de Blobs. Por exemplo, se você tem um fluxo de memória, pode usar o método `create_blob_from_stream` em vez de `create_blob_from_path`. 

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

O código a seguir cria um `generator` para o método `list_blobs`. O código percorre a lista de blobs no contêiner e imprime seus nomes no console.

```python
# List the blobs in the container.
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Baixar os blobs


Baixe os blobs para o disco local usando o método `get_blob_to_path`.
O código a seguir baixa o blob carregado anteriormente. O sistema acrescenta *_DOWNLOADED* ao nome do blob para que você possa ver ambos os arquivos no disco local.

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, local_file_name.replace(
   '.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(
    container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Limpar os recursos
Se você não precisar mais dos blobs carregados neste início rápido, poderá excluir todo o contêiner usando o método `delete_container`. Para excluir arquivos individuais, use o método `delete_blob`.

```python
# Clean up resources. This includes the container and the temp files.
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="resources-for-developing-python-applications-with-blobs"></a>Recursos para desenvolvimento de aplicativos Python com blobs

Para obter mais informações sobre o desenvolvimento em Python com o Armazenamento de Blobs, confira estes recursos adicionais:

### <a name="binaries-and-source-code"></a>Binários e código-fonte

- Veja, baixe e instale o [código-fonte da biblioteca do cliente Python](https://github.com/Azure/azure-storage-python) para o Armazenamento do Azure no GitHub.

### <a name="client-library-reference-and-samples"></a>Exemplos e referência da biblioteca de clientes

- Para obter mais informações sobre a biblioteca de clientes do Python, confira as [bibliotecas de Armazenamento do Azure para Python](/python/api/overview/azure/storage).
- Explore [exemplos de armazenamento de Blobs](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob) gravados usando a biblioteca de clientes de Python.

## <a name="next-steps"></a>Próximas etapas
 
Nesse guia de início rápido, você aprendeu a transferir arquivos entre o disco local e o Armazenamento de Blobs do Azure usando Python. 

Para obter mais informações sobre o Gerenciador de Armazenamento e os Blobs, confira [Gerenciar os recursos de Armazenamento de Blobs do Azure com o Gerenciador de Armazenamento](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).