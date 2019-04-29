---
title: Desenvolvimento para Arquivos do Azure com Python | Microsoft Docs
description: Saiba como desenvolver aplicativos e serviços Python que usam os Arquivos do Azure para armazenar dados de arquivo.
services: storage
author: roygara
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 12/14/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ecb3ef82196c3b6febd44850b47f467ba37facc2
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763536"
---
# <a name="develop-for-azure-files-with-python"></a>Desenvolvimento para o Arquivos do Azure com Python
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

Este tutorial demonstrará as noções básicas do uso do Python para desenvolver aplicativos ou serviços que usam os Arquivos do Azure para armazenar dados de arquivo. Neste tutorial, criaremos um aplicativo de console simples e mostraremos como executar ações básicas com Python e os Arquivos do Azure:

* Criar Compartilhamentos de Arquivos do Azure
* Criar diretórios
* Enumerar arquivos e diretórios em um Compartilhamento de Arquivos do Azure
* Carregar, baixar e excluir um arquivo

> [!Note]  
> Como os Arquivos do Azure podem ser acessados via SMB, é possível criar aplicativos simples que acessam o Compartilhamento de Arquivos do Azure usando as classes e funções padrão de E/S do Python. Este artigo descreverá como criar aplicativos que usam o SDK do Python do Armazenamento do Azure, que usa a [API REST dos Arquivos do Azure](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) para se comunicar com os Arquivos do Azure.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Baixar e instalar o SDK do Armazenamento do Azure para Python

O [SDK do Armazenamento do Azure para Python](https://github.com/azure/azure-storage-python) requer o Python 2.7, 3.3, 3.4, 3.5 ou 3.6.
 
## <a name="install-via-pypi"></a>Instalar por meio de PyPi

Para instalar por meio do Índice de Pacote do Python (PyPI), digite:

```bash
pip install azure-storage-file
```

> [!NOTE]
> Se você estiver fazendo upgrade do SDK de Armazenamento do Azure para Python versão 0.36 ou anterior, desinstale o SDK mais antigo usando `pip uninstall azure-storage` antes de instalar o pacote mais recente.

Para métodos de instalação alternativos, visite o [SDK do Armazenamento do Microsoft Azure para Python no GitHub](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Visualizar o aplicativo de exemplo
f Para exibir e executar um aplicativo de exemplo que mostra como usar o Python com Arquivos do Azure, consulte [Armazenamento do Azure: Introdução aos Arquivos do Azure no Python](https://github.com/Azure-Samples/storage-file-python-getting-started). 

Para executar o aplicativo de exemplo, verifique se você instalou ambos os pacotes `azure-storage-file` e `azure-storage-common`.

## <a name="set-up-your-application-to-use-azure-files"></a>Configurar seu aplicativo para usar os Arquivos do Azure
Adicione o seguinte próximo à parte superior de qualquer arquivo de origem Python no qual você deseja acessar o Armazenamento do Azure com programação.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Configurar uma conexão com os Arquivos do Azure 
O objeto `FileService` permite que você trabalhe com compartilhamentos, diretórios e arquivos. O código a seguir cria um objeto `FileService` usando o nome da conta de armazenamento e a chave de conta. Substitua `<myaccount>` e `<mykey>` pelo nome e pela chave da sua conta.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Criar um compartilhamento de arquivos do Azure
No exemplo de código a seguir, é possível usar um objeto `FileService` para criar o compartilhamento, se ele não existir.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Criar um diretório
Você também pode organizar o armazenamento colocando arquivos em subdiretórios em vez de manter todos eles no diretório raiz. Os Arquivos do Azure permitem que você crie quantos diretórios a conta permitir. O código a seguir criará um subdiretório chamado **sampledir** no diretório raiz.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerar arquivos e diretórios em um Compartilhamento de Arquivos do Azure
Para listar os arquivos e diretórios em um compartilhamento, use o método **list\_directories\_and\_files**. Esse método retorna um gerador. O código a seguir produz o **nome** de cada arquivo e diretório em um compartilhamento para o console.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Carregar um arquivo 
Um Compartilhamento de Arquivos do Azure contém, no mínimo, um diretório raiz em que os arquivos podem residir. Nesta seção, você aprenderá a carregar um arquivo do armazenamento local para o diretório raiz de um compartilhamento.

Para criar um arquivo e carregar dados, use os métodos `create_file_from_path`, `create_file_from_stream`, `create_file_from_bytes` ou `create_file_from_text`. Esses são métodos de alto nível que realizam a separação por partes necessária quando o tamanho do arquivo excede 64 MB.

`create_file_from_path` carrega o conteúdo de um arquivo do caminho especificado e `create_file_from_stream` carrega o conteúdo de um arquivo/fluxo já aberto. `create_file_from_bytes` carrega uma matriz de bytes e `create_file_from_text` carrega o valor do texto especificado usando a codificação especificada (padronizada para UTF-8).

O exemplo a seguir carrega o conteúdo do arquivo **sunset.png** no arquivo **myfile**.

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None, # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

## <a name="download-a-file"></a>Baixar um arquivo
Para baixar dados de um arquivo, use `get_file_to_path`, `get_file_to_stream`, `get_file_to_bytes` ou `get_file_to_text`. Esses são métodos de alto nível que realizam a separação por partes necessária quando o tamanho do arquivo excede 64 MB.

O exemplo a seguir demonstra como usar `get_file_to_path` para baixar o conteúdo do arquivo **myfile** e armazená-lo no arquivo **out-sunset.png**.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Excluir um arquivo
Por fim, para excluir um arquivo, chame `delete_file`.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot"></a>Criar instantâneo de compartilhamento
Você pode criar uma cópia de ponto no tempo do seu compartilhamento de arquivo inteiro.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Criar instantâneo de compartilhamento com metadados**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Listar compartilhamentos e instantâneos 
Você pode listar todos os instantâneos para um determinado compartilhamento.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Procurar instantâneo de compartilhamento
Você pode procurar o conteúdo de cada instantâneo de compartilhamento para recuperar arquivos e diretórios desse ponto no tempo.

```python
directories_and_files = list(file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Obter arquivo de instantâneo de compartilhamento
Você pode baixar um arquivo de um instantâneo de compartilhamento para seu cenário de restauração.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Excluir um único instantâneo de compartilhamento  
Você pode excluir um único instantâneo de compartilhamento.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Excluir compartilhamento quando existem instantâneos de compartilhamento
Um compartilhamento que contém instantâneos não pode ser excluído, a menos que todos os instantâneos sejam excluídos primeiro.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu como manipular s Arquivos do Azure com o Python, siga estes links para saber mais.

* [Centro de desenvolvedores do Python](https://azure.microsoft.com/develop/python/)
* [API REST de serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355)
* [SDK do Armazenamento do Microsoft Azure para Python](https://github.com/Azure/azure-storage-python)
