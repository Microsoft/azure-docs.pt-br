---
title: Desenvolvimento para Arquivos do Azure com Python | Microsoft Docs
description: Saiba como desenvolver aplicativos e serviços Python que usam os Arquivos do Azure para armazenar dados de arquivo.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/08/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-python
ms.openlocfilehash: d45ce3a782d7ee145f769283b82e34647c78f26e
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799863"
---
# <a name="develop-for-azure-files-with-python"></a>Desenvolvimento para o Arquivos do Azure com Python

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Aprenda as noções básicas do uso do Python para desenvolver aplicativos ou serviços que usam os arquivos do Azure para armazenar dados de arquivo. Crie um aplicativo de console simples e saiba como executar ações básicas com o Python e os arquivos do Azure:

- Criar Compartilhamentos de Arquivos do Azure
- Criar diretórios
- Enumerar arquivos e diretórios em um Compartilhamento de Arquivos do Azure
- Carregar, baixar e excluir um arquivo
- Criar backups de compartilhamento de arquivos usando instantâneos

> [!NOTE]
> Como os Arquivos do Azure podem ser acessados via SMB, é possível criar aplicativos simples que acessam o Compartilhamento de Arquivos do Azure usando as classes e funções padrão de E/S do Python. Este artigo descreverá como escrever aplicativos que usam o SDK do Python de armazenamento de arquivos do Azure, que usa a [API REST de arquivos do](/rest/api/storageservices/file-service-rest-api) Azure para se comunicar com os arquivos do Azure.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Baixar e instalar o SDK do Armazenamento do Azure para Python

> [!NOTE]
> Se você estiver fazendo upgrade do SDK de Armazenamento do Azure para Python versão 0.36 ou anterior, desinstale o SDK mais antigo usando `pip uninstall azure-storage` antes de instalar o pacote mais recente.

# <a name="python-v12"></a>[Python v12](#tab/python)

A [biblioteca de cliente de armazenamento de arquivos do Azure V12. x para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share) requer Python 2,7 ou 3.6 +.

# <a name="python-v2"></a>[Python v2](#tab/python2)

O [SDK do armazenamento do Azure para Python](https://github.com/azure/azure-storage-python) requer Python 2,7 ou 3.6 +.

---

## <a name="install-via-pypi"></a>Instalar via PyPI

Para instalar por meio do Índice de Pacote do Python (PyPI), digite:

# <a name="python-v12"></a>[Python v12](#tab/python)

```console
pip install azure-storage-file-share
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-file
```

### <a name="view-the-sample-application"></a>Visualizar o aplicativo de exemplo

Para exibir e executar um aplicativo de exemplo que mostra como usar o Python com os arquivos do Azure, consulte [armazenamento do Azure: introdução com os arquivos do Azure no Python](https://github.com/Azure-Samples/storage-file-python-getting-started).

Para executar o aplicativo de exemplo, verifique se você instalou os `azure-storage-file` pacotes e `azure-storage-common` .

---

## <a name="set-up-your-application-to-use-azure-files"></a>Configurar seu aplicativo para usar os Arquivos do Azure

Adicione o seguinte próximo à parte superior de um arquivo de origem Python para usar os trechos de código neste artigo.

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_Imports":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
from azure.storage.file import FileService
```

---

## <a name="set-up-a-connection-to-azure-files"></a>Configurar uma conexão com os Arquivos do Azure

# <a name="python-v12"></a>[Python v12](#tab/python)

O [ShareServiceClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareserviceclient) permite que você trabalhe com compartilhamentos, diretórios e arquivos. O código a seguir cria um `ShareServiceClient` objeto usando a cadeia de conexão da conta de armazenamento.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateShareServiceClient":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

O objeto [fileservice](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) permite que você trabalhe com compartilhamentos, diretórios e arquivos. O código a seguir cria um objeto `FileService` usando o nome da conta de armazenamento e a chave de conta. Substitua `<myaccount>` e `<mykey>` pelo nome e pela chave da sua conta.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

---

## <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivo do Azure

# <a name="python-v12"></a>[Python v12](#tab/python)

O exemplo de código a seguir usa um objeto [ShareClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient) para criar o compartilhamento, caso ele não exista.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateFileShare":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

O exemplo de código a seguir usa um objeto [fileservice](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) para criar o compartilhamento, caso ele não exista.

```python
file_service.create_share('myshare')
```

---

## <a name="create-a-directory"></a>Criar um diretório

Também é possível organizar o armazenamento colocando arquivos em subdiretórios em vez de manter todos eles no diretório raiz.

# <a name="python-v12"></a>[Python v12](#tab/python)

O método a seguir cria um diretório na raiz do compartilhamento de arquivos especificado usando um objeto [ShareDirectoryClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharedirectoryclient) .

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateDirectory":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

O código abaixo irá criar um subdiretório chamado *sampledir* sob o diretório raiz.

```python
file_service.create_directory('myshare', 'sampledir')
```

---

## <a name="upload-a-file"></a>Fazer upload de um arquivo

Nesta seção, você aprenderá a carregar um arquivo do armazenamento local no armazenamento de arquivos do Azure.

# <a name="python-v12"></a>[Python v12](#tab/python)

O método a seguir carrega o conteúdo do arquivo especificado no diretório especificado no compartilhamento de arquivos do Azure especificado.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_UploadFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Um compartilhamento de arquivos do Azure contém, pelo menos, um diretório raiz onde os arquivos podem residir. Para criar um arquivo e carregar dados, use os métodos [create_file_from_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-path-share-name--directory-name--file-name--local-file-path--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object---timeout-none-), [create_file_from_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-stream-share-name--directory-name--file-name--stream--count--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--), [create_file_from_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-bytes-share-name--directory-name--file-name--file--index-0--count-none--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--)ou [create_file_from_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-text-share-name--directory-name--file-name--text--encoding--utf-8---content-settings-none--metadata-none--validate-content-false--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--) . Eles são métodos de alto nível que executam o agrupamento necessário quando o tamanho dos dados exceder 64 MB.

`create_file_from_path` carrega o conteúdo de um arquivo do caminho especificado e `create_file_from_stream` carrega o conteúdo de um arquivo/fluxo já aberto. `create_file_from_bytes` carrega uma matriz de bytes e `create_file_from_text` carrega o valor do texto especificado usando a codificação especificada (padronizada para UTF-8).

O exemplo a seguir carrega o conteúdo do arquivo *sunset.png* no arquivo **myfile**.

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this file in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerar arquivos e diretórios em um Compartilhamento de Arquivos do Azure

# <a name="python-v12"></a>[Python v12](#tab/python)

Para listar os arquivos e diretórios em um subdiretório, use o método [list_directories_and_files](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#list-directories-and-files-directory-name-none--name-starts-with-none--marker-none----kwargs-) . Esse método retorna um iterável de paginação automática. O código a seguir gera o **nome** de cada arquivo e subdiretório no diretório especificado para o console.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListFilesAndDirs":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Para listar os arquivos e diretórios em um compartilhamento, use o método [list_directories_and_files](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#list-directories-and-files-share-name--directory-name-none--num-results-none--marker-none--timeout-none--prefix-none--snapshot-none-) . Esse método retorna um gerador. O código a seguir produz o **nome** de cada arquivo e diretório em um compartilhamento para o console.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

---

## <a name="download-a-file"></a>Baixar um arquivo

# <a name="python-v12"></a>[Python v12](#tab/python)

Para baixar dados de um arquivo, use [download_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#download-file-offset-none--length-none----kwargs-).

O exemplo a seguir demonstra `download_file` como usar o para obter o conteúdo do arquivo especificado e armazená-lo localmente com o **downloaded-** preceded ao nome do arquivo.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Para baixar dados de um arquivo, use [get_file_to_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-path-share-name--directory-name--file-name--file-path--open-mode--wb---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-), [get_file_to_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-stream-share-name--directory-name--file-name--stream--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-), [get_file_to_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-bytes-share-name--directory-name--file-name--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-)ou [get_file_to_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-text-share-name--directory-name--file-name--encoding--utf-8---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-). Eles são métodos de alto nível que executam o agrupamento necessário quando o tamanho dos dados exceder 64 MB.

O exemplo a seguir demonstra como usar `get_file_to_path` para baixar o conteúdo do arquivo **myfile** e armazená-lo no arquivo *out-sunset.png*.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

---

## <a name="create-a-share-snapshot"></a>Criar um instantâneo de compartilhamento

Você pode criar uma cópia de ponto no tempo do seu compartilhamento de arquivo inteiro.

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateSnapshot":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Criar instantâneo de compartilhamento com metadados**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

---

## <a name="list-shares-and-snapshots"></a>Listar compartilhamentos e instantâneos

Você pode listar todos os instantâneos para um determinado compartilhamento.

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListSharesAndSnapshots":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

---

## <a name="browse-share-snapshot"></a>Procurar instantâneo de compartilhamento

Você pode procurar cada instantâneo de compartilhamento para recuperar arquivos e diretórios desse ponto no tempo.

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_BrowseSnapshotDir":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

---

## <a name="get-file-from-share-snapshot"></a>Obter arquivo de instantâneo de compartilhamento

Você pode baixar um arquivo de um instantâneo de compartilhamento. Isso permite que você restaure uma versão anterior de um arquivo.

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadSnapshotFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

---

## <a name="delete-a-single-share-snapshot"></a>Excluir um único instantâneo de compartilhamento
Você pode excluir um único instantâneo de compartilhamento.

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteSnapshot":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

---

## <a name="delete-a-file"></a>Excluir um arquivo

# <a name="python-v12"></a>[Python v12](#tab/python)

Para excluir um arquivo, chame [Delete_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#delete-file---kwargs-).

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Para excluir um arquivo, chame [Delete_file](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#delete-file-share-name--directory-name--file-name--timeout-none-).

```python
file_service.delete_file('myshare', None, 'myfile')
```

---

## <a name="delete-share-when-share-snapshots-exist"></a>Excluir compartilhamento quando existem instantâneos de compartilhamento

# <a name="python-v12"></a>[Python v12](#tab/python)

Para excluir um compartilhamento que contém instantâneos, chame [delete_share](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#delete-share-delete-snapshots-false----kwargs-) com `delete_snapshots=True` .

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteShare":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Um compartilhamento que contém instantâneos não pode ser excluído, a menos que todos os instantâneos sejam excluídos primeiro.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

---

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como manipular s Arquivos do Azure com o Python, siga estes links para saber mais.

- [Centro de desenvolvedores do Python](/azure/developer/python/)
- [API REST de serviços de armazenamento do Azure](/rest/api/azure/)
- [SDK do Armazenamento do Microsoft Azure para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)
