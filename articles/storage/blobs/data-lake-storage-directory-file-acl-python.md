---
title: Usar o Python para gerenciar dados no Azure Data Lake Storage Gen2
description: Use o Python para gerenciar diretórios e arquivos em contas de armazenamento com namespace hierárquico habilitado.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: a143c0aa19241b532cabff95fe6bf85679e4007c
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652285"
---
# <a name="use-python-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Usar o Python para gerenciar diretórios e arquivos no Azure Data Lake Storage Gen2

Este artigo mostra como usar o Python para criar e gerenciar diretórios e arquivos em contas de armazenamento que têm um namespace hierárquico.

Para saber mais sobre como obter, definir e atualizar as listas de controle de acesso (ACL) de diretórios e arquivos, consulte [usar o Python para gerenciar ACLs no Azure data Lake Storage Gen2](data-lake-storage-acl-python.md).

[Pacote (índice do pacote do Python)](https://pypi.org/project/azure-storage-file-datalake/)  |  [Exemplos](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  [Referência](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  de API Mapeamento de Gen1 [para Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Enviar comentários](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento que tem o namespace hierárquico habilitado. Siga [estas](create-data-lake-storage-account.md) instruções para criar um.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Instale a biblioteca de cliente Azure Data Lake Storage para Python usando [Pip](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake
```

Adicione essas instruções de importação na parte superior do seu arquivo de código.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Conectar à conta

Para usar os trechos de código neste artigo, você precisará criar uma instância de **DataLakeServiceClient** que representa a conta de armazenamento. 

### <a name="connect-by-using-an-account-key"></a>Conectar-se usando uma chave de conta

Essa é a maneira mais fácil de se conectar a uma conta. 

Este exemplo cria uma instância de **DataLakeServiceClient** usando uma chave de conta.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::

- Substitua o valor de espaço reservado `storage_account_name` pelo nome da sua conta de armazenamento.

- Substitua o `storage_account_key` valor de espaço reservado pela chave de acesso da conta de armazenamento.

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Conectar-se usando Azure Active Directory (Azure AD)

Você pode usar a [biblioteca de cliente de identidade do Azure para Python](https://pypi.org/project/azure-identity/) para autenticar seu aplicativo com o Azure AD.

Este exemplo cria uma instância de **DataLakeServiceClient** usando uma ID do cliente, um segredo do cliente e uma ID de locatário.  Para obter esses valores, consulte [adquirir um token do Azure ad para autorizar solicitações de um aplicativo cliente](../common/storage-auth-aad-app.md).

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Para obter mais exemplos, consulte a documentação da [biblioteca de cliente de identidade do Azure para Python](https://pypi.org/project/azure-identity/) .

## <a name="create-a-container"></a>Criar um contêiner

Um contêiner atua como um sistema de arquivos para seus arquivos. Você pode criar um chamando o método **FileSystemDataLakeServiceClient.create_file_system** .

Este exemplo cria um contêiner chamado `my-file-system` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório chamando o método **FileSystemClient.create_directory** .

Este exemplo adiciona um diretório chamado `my-directory` a um contêiner. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Renomear ou mover um diretório

Renomeie ou mova um diretório chamando o método **DataLakeDirectoryClient.rename_directory** . Passe o caminho do diretório desejado de um parâmetro. 

Este exemplo renomeia um subdiretório para o nome `my-subdirectory-renamed` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_RenameDirectory":::

## <a name="delete-a-directory"></a>Excluir um diretório

Exclua um diretório chamando o método **DataLakeDirectoryClient.delete_directory** .

Este exemplo exclui um diretório chamado `my-directory`.  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Carregar um arquivo em um diretório

Primeiro, crie uma referência de arquivo no diretório de destino criando uma instância da classe **DataLakeFileClient** . Carregue um arquivo chamando o método **DataLakeFileClient.append_data** . Certifique-se de concluir o carregamento chamando o método **DataLakeFileClient.flush_data** .

Este exemplo carrega um arquivo de texto em um diretório chamado `my-directory` .   

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFile":::

> [!TIP]
> Se o tamanho do arquivo for grande, seu código terá que fazer várias chamadas para o método **DataLakeFileClient.append_data** . Considere usar o método **DataLakeFileClient.upload_data** em vez disso. Dessa forma, você pode carregar o arquivo inteiro em uma única chamada. 

## <a name="upload-a-large-file-to-a-directory"></a>Carregar um arquivo grande em um diretório

Use o método **DataLakeFileClient.upload_data** para carregar arquivos grandes sem precisar fazer várias chamadas para o método **DataLakeFileClient.append_data** .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Baixar de um diretório 

Abra um arquivo local para gravação. Em seguida, crie uma instância de **DataLakeFileClient** que representa o arquivo que você deseja baixar. Chame o **DataLakeFileClient.read_file** para ler bytes do arquivo e, em seguida, grave esses bytes no arquivo local. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DownloadFromDirectory":::

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Liste o conteúdo do diretório chamando o método **FileSystemClient.get_paths** e, em seguida, enumerando pelos resultados.

Este exemplo, imprime o caminho de cada subdiretório e arquivo que está localizado em um diretório chamado `my-directory` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Confira também

- [Documentação de referência da API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [Pacote (Índice de Pacotes do Python)](https://pypi.org/project/azure-storage-file-datalake/)
- [Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Mapeamento de Gen1 para Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Enviar comentários](https://github.com/Azure/azure-sdk-for-python/issues)