---
title: Usar o Python para gerenciar ACLs no Azure Data Lake Storage Gen2
description: Use o Python gerenciar listas de controle de acesso (ACL) em contas de armazenamento que têm o namespace hierárquico (HNS) habilitado.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: ba864aa1aa2462f21e05ab5e779c8e715d6bb973
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653962"
---
# <a name="use-python-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Usar o Python para gerenciar ACLs no Azure Data Lake Storage Gen2

Este artigo mostra como usar o Python para obter, definir e atualizar as listas de controle de acesso de diretórios e arquivos. 

A herança de ACL já está disponível para novos itens filho que são criados em um diretório pai. Mas você também pode adicionar, atualizar e remover ACLs recursivamente nos itens filho existentes de um diretório pai sem precisar fazer essas alterações individualmente para cada item filho. 

[Pacote (índice do pacote do Python)](https://pypi.org/project/azure-storage-file-datalake/)  |  [Exemplos](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  Amostras de ACL [recursivas](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)  |  [Referência](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  de API Mapeamento de Gen1 [para Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Enviar comentários](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento precisa ter o HNS (namespace hierárquico) habilitado. Siga [estas](create-data-lake-storage-account.md) instruções para criar um.

- CLI do Azure versão `2.6.0` ou superior

- Uma das seguintes permissões de segurança:

  - Uma [entidade de segurança](../../role-based-access-control/overview.md#security-principal) de Azure Active Directory (AD) provisionada à qual foi atribuída a função de proprietário de dados do [blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) no escopo do contêiner de destino, grupo de recursos pai ou assinatura.  

  - Usuário proprietário do contêiner ou diretório de destino ao qual você planeja aplicar as configurações de ACL. Para definir ACLs recursivamente, isso inclui todos os itens filho no contêiner ou diretório de destino.
  
  - Chave da conta de armazenamento.

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

### <a name="connect-by-using-azure-active-directory-ad"></a>Conectar usando o Azure Active Directory (AD)

> [!NOTE]
> Se você estiver usando Azure Active Directory (Azure AD) para autorizar o acesso, verifique se a entidade de segurança recebeu a [função de proprietário de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos de alterá-las, consulte  [modelo de controle de acesso em Azure data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

Você pode usar a [biblioteca de cliente de identidade do Azure para Python](https://pypi.org/project/azure-identity/) para autenticar seu aplicativo com o Azure AD.

Obtenha uma ID do cliente, um segredo do cliente e uma ID de locatário. Para fazer isso, consulte [adquirir um token do Azure ad para autorizar solicitações de um aplicativo cliente](../common/storage-auth-aad-app.md). Como parte desse processo, você precisará atribuir uma das seguintes funções do Azure [RBAC (controle de acesso baseado em função](../../role-based-access-control/overview.md) do Azure) à sua entidade de segurança. 

|Função|Funcionalidade de configuração de ACL|
|--|--|
|[Proprietário de Dados do Blob de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Todos os diretórios e arquivos na conta.|
|[Colaborador de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Somente diretórios e arquivos de propriedade da entidade de segurança.|

Este exemplo cria uma instância de **DataLakeServiceClient** usando uma ID do cliente, um segredo do cliente e uma ID de locatário.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Para obter mais exemplos, consulte a documentação da [biblioteca de cliente de identidade do Azure para Python](https://pypi.org/project/azure-identity/) .

### <a name="connect-by-using-an-account-key"></a>Conectar-se usando uma chave de conta

Essa é a maneira mais fácil de se conectar a uma conta.

Este exemplo cria uma instância de **DataLakeServiceClient** usando uma chave de conta.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::
 
- Substitua o valor de espaço reservado `storage_account_name` pelo nome da sua conta de armazenamento.

- Substitua o `storage_account_key` valor de espaço reservado pela chave de acesso da conta de armazenamento.

## <a name="set-acls"></a>Definir ACLs

Ao *definir* uma ACL, você **substitui** toda a ACL, incluindo todas as entradas. Se você quiser alterar o nível de permissão de uma entidade de segurança ou adicionar uma nova entidade de segurança à ACL sem afetar outras entradas existentes, deverá *Atualizar* a ACL em vez disso. Para atualizar uma ACL em vez de substituí-la, consulte a seção [Update ACLs](#update-acls-recursively) deste artigo.  

Esta seção mostra como:

- Definir a ACL de um diretório
- Definir a ACL de um arquivo

### <a name="set-the-acl-of-a-directory"></a>Definir a ACL de um diretório

Obtenha a lista de controle de acesso (ACL) de um diretório chamando o método **DataLakeDirectoryClient.get_access_control** e defina a ACL chamando o método **DataLakeDirectoryClient.set_access_control** .

Este exemplo obtém e define a ACL de um diretório chamado `my-directory` . A cadeia de caracteres `rwxr-xrw-` fornece as permissões de leitura, gravação e execução do usuário proprietário, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros permissões de leitura e gravação.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ACLDirectory":::

Você também pode obter e definir a ACL do diretório raiz de um contêiner. Para obter o diretório raiz, chame o método **FileSystemClient._get_root_directory_client** .

### <a name="set-the-acl-of-a-file"></a>Definir a ACL de um arquivo

Obtenha a lista de controle de acesso (ACL) de um arquivo chamando o método **DataLakeFileClient.get_access_control** e defina a ACL chamando o método **DataLakeFileClient.set_access_control** .

Este exemplo obtém e define a ACL de um arquivo chamado `my-file.txt` . A cadeia de caracteres `rwxr-xrw-` fornece as permissões de leitura, gravação e execução do usuário proprietário, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros permissões de leitura e gravação.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_FileACL":::

## <a name="set-acls-recursively"></a>Definir ACLs recursivamente

Ao *definir* uma ACL, você **substitui** toda a ACL, incluindo todas as entradas. Se você quiser alterar o nível de permissão de uma entidade de segurança ou adicionar uma nova entidade de segurança à ACL sem afetar outras entradas existentes, deverá *Atualizar* a ACL em vez disso. Para atualizar uma ACL em vez de substituí-la, consulte a seção [Atualizar ACLs recursivamente](#update-acls-recursively) deste artigo.

Defina ACLs recursivamente chamando o método **DataLakeDirectoryClient.set_access_control_recursive** .

Se você quiser definir uma entrada ACL **padrão** , adicione a cadeia de caracteres `default:` ao início de cada cadeia de caracteres de entrada de ACL.

Este exemplo define a ACL de um diretório chamado `my-parent-directory` .

Esse método aceita um parâmetro booliano chamado `is_default_scope` que especifica se a ACL padrão deve ser definida. Se esse parâmetro for `True` , a lista de entradas de ACL será precedida pela cadeia de caracteres `default:` .

As entradas da ACL fornecem ao usuário proprietário permissões de leitura, gravação e execução, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros sem acesso. A última entrada ACL neste exemplo fornece um usuário específico com a ID de objeto "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "permissões de leitura e execução. Essas entradas fornecem ao usuário proprietário permissões de leitura, gravação e execução, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros sem acesso. A última entrada ACL neste exemplo fornece um usuário específico com a ID de objeto "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "permissões de leitura e execução.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_SetACLRecursively":::

Para ver um exemplo que processa ACLs recursivamente em lotes especificando um tamanho de lote, consulte o [exemplo](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)de Python.

## <a name="update-acls-recursively"></a>Atualizar ACLs recursivamente

Ao *Atualizar* uma ACL, você modifica a ACL em vez de substituir a ACL. Por exemplo, você pode adicionar uma nova entidade de segurança à ACL sem afetar outras entidades de segurança listadas na ACL.  Para substituir a ACL em vez de atualizá-la, consulte a seção [set ACLs](#set-acls) deste artigo.

Para atualizar uma ACL recursivamente, crie um novo objeto ACL com a entrada ACL que você deseja atualizar e, em seguida, use esse objeto em atualizar a operação de ACL. Não obtenha a ACL existente, basta fornecer as entradas de ACL a serem atualizadas. Atualize uma ACL recursivamente chamando o método **DataLakeDirectoryClient.update_access_control_recursive** . Se você quiser atualizar uma entrada de ACL **padrão** , adicione a cadeia de caracteres `default:` ao início de cada cadeia de caracteres de entrada de ACL.

Este exemplo atualiza uma entrada ACL com permissão de gravação.

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . Esse método aceita um parâmetro booliano chamado `is_default_scope` que especifica se a ACL padrão deve ser atualizada. Se esse parâmetro for `True` , a entrada de ACL atualizada será precedida pela cadeia de caracteres `default:` .  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_UpdateACLsRecursively":::

Para ver um exemplo que processa ACLs recursivamente em lotes especificando um tamanho de lote, consulte o [exemplo](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)de Python.

## <a name="remove-acl-entries-recursively"></a>Remover entradas de ACL recursivamente

Você pode remover uma ou mais entradas de ACL. Para remover as entradas de ACL recursivamente, crie um novo objeto ACL para a entrada de ACL a ser removida e, em seguida, use esse objeto em remover a operação de ACL. Não obtenha a ACL existente, basta fornecer as entradas de ACL a serem removidas. 

Remova as entradas ACL chamando o método **DataLakeDirectoryClient.remove_access_control_recursive** . Se você quiser remover uma entrada ACL **padrão** , adicione a cadeia de caracteres `default:` ao início da cadeia de caracteres de entrada ACL. 

Este exemplo remove uma entrada ACL da ACL do diretório chamado `my-parent-directory` . Esse método aceita um parâmetro booliano chamado `is_default_scope` que especifica se a entrada deve ser removida da ACL padrão. Se esse parâmetro for `True` , a entrada de ACL atualizada será precedida pela cadeia de caracteres `default:` . 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

Para ver um exemplo que processa ACLs recursivamente em lotes especificando um tamanho de lote, consulte o [exemplo](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)de Python.

## <a name="recover-from-failures"></a>Recuperar de falhas

Você pode encontrar erros de tempo de execução ou de permissão. Para erros de tempo de execução, reinicie o processo desde o início. Poderão ocorrer erros de permissão se a entidade de segurança não tiver permissão suficiente para modificar a ACL de um diretório ou arquivo que está na hierarquia de diretório que está sendo modificada. Resolva o problema de permissão e, em seguida, escolha retomar o processo a partir do ponto de falha usando um token de continuação ou reiniciar o processo do início. Você não precisará usar o token de continuação se preferir reiniciar desde o início. Você pode reaplicar entradas de ACL sem nenhum impacto negativo.

Este exemplo retorna um token de continuação no caso de uma falha. O aplicativo pode chamar esse método de exemplo novamente depois que o erro for resolvido e passar o token de continuação. Se esse método de exemplo for chamado pela primeira vez, o aplicativo poderá passar um valor de ``None`` para o parâmetro de token de continuação.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ResumeContinuationToken":::

Para ver um exemplo que processa ACLs recursivamente em lotes especificando um tamanho de lote, consulte o [exemplo](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)de Python.

Se você quiser que o processo seja concluído sem interrupções por erros de permissão, poderá especificar isso.

Para garantir que o processo seja concluído sem interrupções, não passe um token de continuação para o método **DataLakeDirectoryClient.set_access_control_recursive** .

Este exemplo define as entradas de ACL recursivamente. Se esse código encontrar um erro de permissão, ele registrará essa falha e continuará a execução. Este exemplo imprime o número de falhas no console.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ContinueOnFailure":::

Para ver um exemplo que processa ACLs recursivamente em lotes especificando um tamanho de lote, consulte o [exemplo](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)de Python.

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Confira também

- [Documentação de referência da API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [Pacote (Índice de Pacotes do Python)](https://pypi.org/project/azure-storage-file-datalake/)
- [Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Mapeamento de Gen1 para Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Enviar comentários](https://github.com/Azure/azure-sdk-for-python/issues)
- [Modelo de controle de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [ACLs (listas de controle de acesso) no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)