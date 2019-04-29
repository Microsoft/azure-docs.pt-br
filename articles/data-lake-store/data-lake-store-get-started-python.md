---
title: 'Python: Operações de gerenciamento no Azure Data Lake armazenamento Gen1 de conta | Microsoft Docs'
description: Saiba como usar o SDK do Python para trabalhar com as operações de gerenciamento de conta do Azure Data Lake Storage Gen1.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: b6ef5a5c12bb766fb7106d5c7a8189c4b92980d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60877247"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-python"></a>Operações de gerenciamento de conta no Azure Data Lake Storage Gen1 usando Python
> [!div class="op_single_selector"]
> * [SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Saiba como usar o SDK do Python do Azure Data Lake Storage Gen1 para executar operações básicas de gerenciamento de contas como criar uma conta do Data Lake Storage Gen1, listar as contas do Data Lake Storage Gen1, etc. Para obter instruções sobre como executar operações do sistema de arquivos no Data Lake Storage Gen1 usando Python, consulte [Operações do sistema de arquivos no Data Lake Storage Gen1 usando Python](data-lake-store-data-operations-python.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Python**. Você pode baixar o Python [aqui](https://www.python.org/downloads/). Este artigo usa o Python 3.6.2.

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Um grupo de recursos do Azure**. Para obter instruções, veja [Criar um grupo de recursos do Azure](../azure-resource-manager/manage-resource-groups-portal.md).

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar com o Data Lake Storage Gen1 usando o Python, você precisa instalar três módulos.

* O módulo `azure-mgmt-resource`, que inclui módulos do Azure para o Active Directory etc.
* O módulo `azure-mgmt-datalake-store`, que inclui as operações de gerenciamento de conta do Azure Data Lake Storage Gen1. Para obter mais informações sobre esse módulo, consulte [Referência do módulo de gerenciamento do Azure Data Lake Storage Gen1](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* O módulo `azure-datalake-store`, que inclui as operações do sistema de arquivos do Azure Data Lake Storage Gen1. Para obter mais informações sobre esse módulo, consulte [referência do módulo filesystem azure-datalake-store](https://azure-datalake-store.readthedocs.io/en/latest/).

Use os comandos a seguir para instalar os módulos.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

1. Use o IDE de sua escolha para criar um novo aplicativo Python, por exemplo, **mysample.py**.

2. Adicione o seguinte snippet para importar os módulos necessários

    ```
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

## <a name="authentication"></a>Authentication

Nesta seção, falamos sobre as diferentes maneiras de autenticação com o Azure AD. As opções disponíveis são:

* Para autenticação do usuário final para seu aplicativo, consulte [Autenticação de usuário final com o Data Lake Storage Gen1 usando o Python](data-lake-store-end-user-authenticate-python.md).
* Para autenticação de serviço a serviço do aplicativo, consulte [Autenticação de serviço a serviço com o Data Lake Storage Gen1 usando o Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-client-and-data-lake-storage-gen1-account"></a>Criar cliente e conta do Data Lake Storage Gen1

O trecho de código a seguir primeiro cria o cliente da conta do Data Lake Storage Gen1. Ele usa o objeto cliente para criar uma conta do Data Lake Storage Gen1. Finalmente, o snippet de código cria um objeto de cliente do sistema de arquivos.

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

    
## <a name="list-the-data-lake-storage-gen1-accounts"></a>Listar as contas do Data Lake Storage Gen1

    ## List the existing Data Lake Storage Gen1 accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-storage-gen1-account"></a>Excluir a conta do Data Lake Storage Gen1

    ## Delete an existing Data Lake Storage Gen1 account
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>Próximas etapas
* [Operações de filesystem no Data Lake Storage Gen1 usando Python](data-lake-store-data-operations-python.md).

## <a name="see-also"></a>Consulte também

* [Referência do Python (Filesystem) do azure-datalake-store](https://azure-datalake-store.readthedocs.io/en/latest)
* [Aplicativos de Big Data de software livre com Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
