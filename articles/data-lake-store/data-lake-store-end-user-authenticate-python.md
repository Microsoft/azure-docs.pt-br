---
title: Autenticação do usuário final – Python com Data Lake Storage Gen1-Azure
description: Aprenda como obter autenticação de usuário final com o Azure Data Lake Storage Gen1 usando o Active Directory do Azure com o Python
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: has-adal-ref, devx-track-python
ms.openlocfilehash: c7ee58dec4e8bc04eb07695378e0d4dc973b6705
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92109078"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-python"></a>Autenticação de usuário final com o Azure Data Lake Storage Gen1 usando Python
> [!div class="op_single_selector"]
> * [Usando Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Usando o SDK .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Usando Python](data-lake-store-end-user-authenticate-python.md)
> * [Usando a API REST](data-lake-store-end-user-authenticate-rest-api.md)
>
>

Neste artigo, você aprende sobre como usar o SDK do Python para fazer a autenticação do usuário final com o Armazenamento de Data Lake do Azure Gen1. A autenticação do usuário final pode ser adicionalmente dividida em duas categorias:

* Autenticação do usuário final sem autenticação multifator
* Autenticação do usuário final com autenticação multifator

Essas duas opções são discutidas neste artigo. Para autenticação de serviço a serviço com o Data Lake Storage Gen1 usando Python, consulte [Autenticação de serviço a serviço com o Data Lake Storage Gen1 usando Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Python**. Você pode baixar o Python [aqui](https://www.python.org/downloads/). Este artigo usa o Python 3.6.2.

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar um aplicativo "Nativo" do Azure Active Directory**. Você deve ter concluído as etapas em [Autenticação de usuário final com Data Lake Storage Gen1 usando Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar com o Data Lake Storage Gen1 usando o Python, você precisa instalar três módulos.

* O módulo `azure-mgmt-resource`, que inclui módulos do Azure para o Active Directory etc.
* O módulo `azure-mgmt-datalake-store`, que inclui as operações de gerenciamento de conta do Azure Data Lake Storage Gen1. Para obter mais informações sobre esse módulo, consulte [Referência do módulo de gerenciamento do Azure Data Lake Storage Gen1](/python/api/azure-mgmt-datalake-store/).
* O módulo `azure-datalake-store`, que inclui as operações do sistema de arquivos do Azure Data Lake Storage Gen1. Para obter mais informações sobre esse módulo, consulte [referência do módulo do sistema de arquivos Azure-datalake-Store](/python/api/azure-datalake-store/azure.datalake.store.core/).

Use os comandos a seguir para instalar os módulos.

```console
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

1. No IDE de sua escolha, crie um novo aplicativo Python, por exemplo, **MySample.py**.

2. Adicione o seguinte snippet para importar os módulos necessários

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    import adal
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, pprint, uuid, time
    ```

3. Salve as alterações a mysample.py.

## <a name="end-user-authentication-with-multi-factor-authentication"></a>Autenticação do usuário final com autenticação multifator

### <a name="for-account-management"></a>Para gerenciamento de contas

Use o seguinte snippet para autenticar com o Azure AD para operações de gerenciamento de contas em uma conta do Data Lake Storage Gen1. O snippet a seguir pode ser usado para autenticar seu aplicativo usando a autenticação multifator. Forneça os valores abaixo para um aplicativo **nativo** existente do Azure AD.

```python
authority_host_url = "https://login.microsoftonline.com"
tenant = "FILL-IN-HERE"
authority_url = authority_host_url + '/' + tenant
client_id = 'FILL-IN-HERE'
redirect = 'urn:ietf:wg:oauth:2.0:oob'
RESOURCE = 'https://management.core.windows.net/'

context = adal.AuthenticationContext(authority_url)
code = context.acquire_user_code(RESOURCE, client_id)
print(code['message'])
mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
armCreds = AADTokenCredentials(mgmt_token, client_id, resource = RESOURCE)
```

### <a name="for-filesystem-operations"></a>Para operações de sistema de arquivos

Use isso para autenticar com o Azure AD para operações do sistema de arquivos em uma conta do Data Lake Storage Gen1. O snippet a seguir pode ser usado para autenticar seu aplicativo usando a autenticação multifator. Forneça os valores abaixo para um aplicativo **nativo** existente do Azure AD.

```console
adlCreds = lib.auth(tenant_id='FILL-IN-HERE', resource = 'https://datalake.azure.net/')
```

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Autenticação do usuário final sem autenticação multifator

Isso foi preterido. Para obter mais informações, consulte [Autenticação do Azure usando o SDK do Python](/azure/python/python-sdk-azure-authenticate).

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar a autenticação do usuário final para autenticar com o Armazenamento de Dados do Azure Data Lake Gen1 usando o Python. Agora você pode ver os seguintes artigos que falam sobre como usar o Python para trabalhar com o Azure Data Lake Storage Gen1.

* [Operações de gerenciamento de conta em Data Lake Storage Gen1 usando Python](data-lake-store-get-started-python.md)
* [Operações de dados no Data Lake Storage Gen1 usando Python ](data-lake-store-data-operations-python.md)