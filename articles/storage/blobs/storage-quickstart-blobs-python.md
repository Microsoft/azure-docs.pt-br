---
title: Início Rápido do Azure - Criar um blob no armazenamento de objeto com Python | Microsoft Docs
description: Neste início rápido, você criará uma conta de armazenamento e um contêiner no armazenamento de objeto (Blob). Em seguida, você deve usar a biblioteca de clientes de armazenamento para Python a fim de carregar um blob no Armazenamento do Azure, baixar um blob e listar os blobs em um contêiner.
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 12/14/2018
ms.author: mhopkins
ms.reviewer: seguler
ms.openlocfilehash: f96d1956fececbe00bf9f33bd146056836c2c4c0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564998"
---
# <a name="quickstart-upload-download-and-list-blobs-with-python"></a>Início Rápido: Carregar, baixar e listar blobs com o Python

Neste Início Rápido, você verá como usar o Python para carregar, baixar e listar blobs de blocos em um contêiner no Armazenamento de Blobs do Azure. Blobs são simplesmente objetos que podem armazenar qualquer quantidade de dados de texto ou binários (como imagens, documentos, mídia de streaming, dados de arquivo etc.) e, no Armazenamento do Azure, são diferentes de compartilhamentos de arquivo, tabelas sem esquema e filas de mensagens. (Para obter mais informações, confira [Introdução ao Armazenamento do Azure](/azure/storage/common/storage-introduction).)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Verifique se você tem os pré-requisitos adicionais a seguir instalados:

* [Python](https://www.python.org/downloads/)
* [SDK do Armazenamento do Azure para Python](https://github.com/Azure/azure-sdk-for-python)

## <a name="download-the-sample-application"></a>Baixar o aplicativo de exemplo
O [aplicativo de exemplo](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) deste início rápido é um aplicativo Python.  

Use o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Este comando clona o repositório *Azure-Samples/storage-blobs-python-quickstart* em sua pasta git local. Para executar o programa em Python, abra o arquivo *example.py* na raiz do repositório.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento
No aplicativo, forneça o nome da conta de armazenamento e a chave de conta para criar um objeto `BlockBlobService`. Abra o arquivo *example.py* no Gerenciador de Soluções na sua IDE. Substitua os valores `accountname` e `accountkey` pelo nome de conta e chave. 

```python
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')
```

## <a name="run-the-sample"></a>Execute o exemplo
Este exemplo cria um arquivo de teste na pasta *Documentos*. O programa de exemplo carrega o arquivo de teste no armazenamento de Blobs, lista os blobs no contêiner e baixa o arquivo com um novo nome. 

Primeiro, instale as dependências, executando `pip install`:

```python
    pip install azure-storage-blob
```

Em seguida, execute o exemplo. Você verá mensagens semelhantes à seguinte saída:
  
```output
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Antes de continuar, verifique se os dois documentos estão na pasta *Documentos*. Você pode abri-los e ver que eles são idênticos.

Você também pode usar uma ferramenta como o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com) para exibir os arquivos no Armazenamento de Blobs. O Gerenciador de Armazenamento do Azure é uma ferramenta gratuita de multiplataforma que permite que você acesse as informações da sua conta de armazenamento. 

Depois de verificar os arquivos, pressione qualquer tecla para concluir a demonstração e excluir os arquivos de teste. Agora que você sabe o que o exemplo faz, abra o arquivo *example.py* para examinar o código. 

## <a name="understand-the-sample-code"></a>Entender o código de exemplo

Vamos examinar o código de exemplo para entender como ele funciona.

### <a name="get-references-to-the-storage-objects"></a>Obter referências a objetos de armazenamento
A primeira coisa a fazer é criar as referências aos objetos usados para acessar e gerenciar o Armazenamento de Blobs. Esses objetos dependem uns dos outros, e cada um é usado pelo próximo na lista.

* Crie uma instância do objeto **BlockBlobService**, que aponta para o serviço Blob na sua conta de armazenamento. 

* Crie uma instância do objeto **CloudBlobContainer**, que representa o contêiner que você está acessando. Os contêineres são usados para organizar seus blobs da mesma forma que você usa pastas no seu computador para organizar seus arquivos.

Depois de obter o contêiner de Blob de Nuvem, crie uma instância do objeto **CloudBlockBlob** que aponta para o blob específico desejado. Você pode carregar, baixar e copiar o blob conforme a necessidade.

> [!IMPORTANT]
> Os nomes de contêiner devem estar em minúsculas. Para obter mais informações sobre nomes de contêiner e de blobs, confira [Nomeando e referenciando contêineres, blobs e metadados](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Nesta seção, você instancia os objetos, cria um novo contêiner e, em seguida, define as permissões no contêiner para que os blobs sejam públicos. O contêiner é chamado de **quickstartblobs**. 

```python
# Create the BlockBlockService that is used to call the Blob service for the storage account.
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')

# Create a container called 'quickstartblobs'.
container_name = 'quickstartblobs'
block_blob_service.create_container(container_name)

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(
    container_name, public_access=PublicAccess.Container)
```
### <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contêiner

O Armazenamento de Blobs dá suporte a blobs de blocos, blobs de acréscimo e blobs de páginas. Os blobs de bloco são utilizados com mais frequência e é o que é usado nesse guia de início rápido.  

Para carregar um arquivo em um blob, obtenha o caminho do arquivo unindo o nome do diretório ao nome do arquivo na unidade local. Em seguida, você pode carregar o arquivo no caminho especificado usando o método `create_blob_from_path`. 

O código de exemplo cria um arquivo local a ser usado para upload e download, armazenando o arquivo a ser carregado como *caminho_completo_para_o_arquivo* e o nome do blob como *nome_do_arquivo_local*. O exemplo a seguir carrega o arquivo para seu contêiner chamado **quickstartblobs**.

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

Os blobs de bloco podem ter até 4,7 TB e podem ser qualquer coisa desde planilhas do Excel até arquivos de vídeo grandes. Os blobs de páginas são usados principalmente para os arquivos VHD que auxiliam as VMs IaaS. Os blobs de acréscimo são usados para registro em log, como quando você quer gravar em um arquivo e depois adicionar mais informações. A maioria dos objetos armazenados no Armazenamento de Blobs são blobs de blocos.

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Obtenha uma lista de arquivos no contêiner com o método `list_blobs`. Esse método retorna um gerador. O código a seguir recupera a lista de blobs&mdash;e a percorre&mdash;mostrando os nomes dos blobs encontrados em um contêiner.  

```python
# List the blobs in the container.
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Baixar os blobs

Baixe os blobs para o disco local usando o método `get_blob_to_path`. O código a seguir baixa o blob carregado em uma seção anterior. *_DOWNLOADED* é adicionado como um sufixo ao nome do blob para que você possa ver ambos os arquivos no disco local. 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, string.replace(
    local_file_name, '.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(
    container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Limpar recursos
Se você não precisar mais dos blobs carregados neste início rápido, poderá excluir todo o contêiner usando o método `delete_container`. Para excluir arquivos individuais, use o método `delete_blob`.

```python
# Clean up resources. This includes the container and the temp files.
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```
## <a name="resources-for-developing-python-applications-with-blobs"></a>Recursos para desenvolvimento de aplicativos Python com blobs

Para obter mais informações sobre o desenvolvimento em Python com o armazenamento de blobs, consulte estes recursos adicionais:

### <a name="binaries-and-source-code"></a>Binários e código-fonte

- Veja, baixe e instale o [código-fonte da biblioteca do cliente Python](https://github.com/Azure/azure-storage-python) para o Armazenamento do Azure no GitHub.

### <a name="client-library-reference-and-samples"></a>Exemplos e referência da biblioteca de clientes

- Para saber mais sobre a biblioteca de clientes Python, consulte a [Referência da API Python](https://docs.microsoft.com/python/api/overview/azure/storage).
- Explore [exemplos de armazenamento de Blobs](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob) gravados usando a biblioteca de clientes de Python.

## <a name="next-steps"></a>Próximas etapas
 
Nesse guia de início rápido, você aprendeu a transferir arquivos entre o disco local e o Armazenamento de Blobs do Azure usando Python. Para saber mais sobre como trabalhar com o Armazenamento de Blobs, prossiga para as instruções do Armazenamento de Blobs.

> [!div class="nextstepaction"]
> [Instruções de operações do Armazenamento de Blobs](./storage-python-how-to-use-blob-storage.md)
 
Para obter mais informações sobre o Gerenciador de Armazenamento e os Blobs, consulte [Gerenciar os recursos de Armazenamento de Blobs do Azure com o Gerenciador de Armazenamento](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
