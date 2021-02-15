---
title: Azure Data Lake Storage Gen2 o SDK do Python para arquivos & ACLs
description: Use os diretórios de gerenciamento do Python e ACLs (listas de controle de acesso) de arquivo e diretório em contas de armazenamento que têm o namespace hierárquico (HNS) habilitado.
author: normesta
ms.service: storage
ms.date: 01/22/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: 5036930c7bb49578582fbc1b347b11518579b53e
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740611"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Use o Python para gerenciar diretórios, arquivos e ACLs no Azure Data Lake Storage Gen2

Este artigo mostra como usar o Python para criar e gerenciar diretórios, arquivos e permissões em contas de armazenamento que têm o namespace hierárquico (HNS) habilitado. 

[Pacote (índice do pacote do Python)](https://pypi.org/project/azure-storage-file-datalake/)  |  [Exemplos](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  [Referência](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  de API Mapeamento de Gen1 [para Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Enviar comentários](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento precisa ter o HNS (namespace hierárquico) habilitado. Siga [estas](../common/storage-account-create.md) instruções para criar um.

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

### <a name="connect-by-using-azure-active-directory-ad"></a>Conectar usando o Azure Active Directory (AD)

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

## <a name="manage-access-control-lists-acls"></a>Gerenciar listas de controle de acesso (ACLs)

Você pode obter, definir e atualizar as permissões de acesso de diretórios e arquivos.

> [!NOTE]
> Se você estiver usando Azure Active Directory (Azure AD) para autorizar o acesso, verifique se a entidade de segurança recebeu a [função de proprietário de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos por alterá-las, confira [Controle de acesso no Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="manage-directory-acls"></a>Gerenciar ACLs de diretório

Obtenha a lista de controle de acesso (ACL) de um diretório chamando o método **DataLakeDirectoryClient.get_access_control** e defina a ACL chamando o método **DataLakeDirectoryClient.set_access_control** .

> [!NOTE]
> Se seu aplicativo autorizar o acesso usando Azure Active Directory (Azure AD), verifique se a entidade de segurança que seu aplicativo usa para autorizar o acesso recebeu a [função de proprietário de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos por alterá-las, confira [Controle de acesso no Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

Este exemplo obtém e define a ACL de um diretório chamado `my-directory` . A cadeia de caracteres `rwxr-xrw-` fornece as permissões de leitura, gravação e execução do usuário proprietário, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros permissões de leitura e gravação.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ACLDirectory":::

Você também pode obter e definir a ACL do diretório raiz de um contêiner. Para obter o diretório raiz, chame o método **FileSystemClient._get_root_directory_client** .

### <a name="manage-file-permissions"></a>Gerenciar permissões de arquivo

Obtenha a lista de controle de acesso (ACL) de um arquivo chamando o método **DataLakeFileClient.get_access_control** e defina a ACL chamando o método **DataLakeFileClient.set_access_control** .

> [!NOTE]
> Se seu aplicativo autorizar o acesso usando Azure Active Directory (Azure AD), verifique se a entidade de segurança que seu aplicativo usa para autorizar o acesso recebeu a [função de proprietário de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos por alterá-las, confira [Controle de acesso no Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

Este exemplo obtém e define a ACL de um arquivo chamado `my-file.txt` . A cadeia de caracteres `rwxr-xrw-` fornece as permissões de leitura, gravação e execução do usuário proprietário, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros permissões de leitura e gravação.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_FileACL":::

### <a name="set-an-acl-recursively"></a>Definir uma ACL recursivamente

Você pode adicionar, atualizar e remover ACLs recursivamente nos itens filho existentes de um diretório pai sem precisar fazer essas alterações individualmente para cada item filho. Para obter mais informações, consulte [definir listas de controle de acesso (ACLs) recursivamente para Azure data Lake Storage Gen2](recursive-access-control-lists.md).

## <a name="see-also"></a>Confira também

* [Documentação de referência da API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
* [Pacote (Índice de Pacotes do Python)](https://pypi.org/project/azure-storage-file-datalake/)
* [Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Mapeamento de Gen1 para Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Enviar comentários](https://github.com/Azure/azure-sdk-for-python/issues)