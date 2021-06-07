---
title: Gerenciar uma conta de Azure Data Lake Storage Gen1 com o Python
description: Saiba como usar o SDK do Python para Azure Data Lake Storage Gen1 operações de gerenciamento de conta.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-python
ms.openlocfilehash: 35e71b80c6f47bb13f7a2b490b493b0cb42acf04
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92103502"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Operações de gerenciamento de conta no Azure Data Lake Storage Gen1 usando Python
> [!div class="op_single_selector"]
> * [SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Saiba como usar o SDK do Python para Azure Data Lake Storage Gen1 executar operações básicas de gerenciamento de conta, como criar uma conta de Data Lake Storage Gen1, listar as contas de Data Lake Storage Gen1, etc. Para obter instruções sobre como executar operações de sistema de arquivos em Data Lake Storage Gen1 usando o Python, consulte [operações de sistema de arquivos em data Lake Storage Gen1 usando Python](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Python**. Você pode baixar o Python [aqui](https://www.python.org/downloads/). Este artigo usa o Python 3.6.2.

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Um grupo de recursos do Azure**. Para obter instruções, veja [Criar um grupo de recursos do Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar com o Data Lake Storage Gen1 usando o Python, você precisa instalar três módulos.

* O módulo `azure-mgmt-resource`, que inclui módulos do Azure para o Active Directory etc.
* O módulo `azure-mgmt-datalake-store`, que inclui as operações de gerenciamento de conta do Azure Data Lake Storage Gen1. Para obter mais informações sobre esse módulo, consulte [Referência do módulo de gerenciamento do Azure Data Lake Storage Gen1](/python/api/azure-mgmt-datalake-store/).
* O módulo `azure-datalake-store`, que inclui as operações do sistema de arquivos do Azure Data Lake Storage Gen1. Para obter mais informações sobre esse módulo, consulte [referência do módulo filesystem azure-datalake-store](/python/api/azure-datalake-store/azure.datalake.store.core/).

Use os comandos a seguir para instalar os módulos.

```console
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

1. Use o IDE de sua escolha para criar um novo aplicativo Python, por exemplo, **mysample.py**.

2. Adicione o seguinte snippet para importar os módulos necessários

    ```python
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Salve as alterações a mysample.py.

## <a name="authentication"></a>Autenticação

Nesta seção, falamos sobre as diferentes maneiras de autenticação com o Azure AD. As opções disponíveis são:

* Para autenticação do usuário final para seu aplicativo, consulte [Autenticação de usuário final com o Data Lake Storage Gen1 usando o Python](data-lake-store-end-user-authenticate-python.md).
* Para autenticação de serviço a serviço do aplicativo, consulte [Autenticação de serviço a serviço com o Data Lake Storage Gen1 usando o Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-client-and-data-lake-storage-gen1-account"></a>Criar cliente e conta do Data Lake Storage Gen1

O snippet a seguir primeiro cria o cliente da conta do Data Lake Storage Gen1. Ele usa o objeto cliente para criar uma conta do Data Lake Storage Gen1. Finalmente, o snippet de código cria um objeto de cliente do sistema de arquivos.

```python
## Declare variables
subscriptionId = 'FILL-IN-HERE'
adlsAccountName = 'FILL-IN-HERE'
resourceGroup = 'FILL-IN-HERE'
location = 'eastus2'

## Create Data Lake Storage Gen1 account management client object
adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

## Create a Data Lake Storage Gen1 account
adlsAcctResult = adlsAcctClient.account.create(
    resourceGroup,
    adlsAccountName,
    DataLakeStoreAccount(
        location=location
    )
).wait()
```

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>Listar as contas do Data Lake Storage Gen1

```python
## List the existing Data Lake Storage Gen1 accounts
result_list_response = adlsAcctClient.account.list()
result_list = list(result_list_response)
for items in result_list:
    print(items)
```

## <a name="delete-the-data-lake-storage-gen1-account"></a>Excluir a conta do Data Lake Storage Gen1

```python
## Delete an existing Data Lake Storage Gen1 account
adlsAcctClient.account.delete(adlsAccountName)
```
    

## <a name="next-steps"></a>Próximas etapas
* [Operações de filesystem no Data Lake Storage Gen1 usando Python](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Confira também

* [Referência do Python (Filesystem) do azure-datalake-store](/python/api/azure-datalake-store/azure.datalake.store.core)
* [Aplicativos de Big Data de software livre com Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)