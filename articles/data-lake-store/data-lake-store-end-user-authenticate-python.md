---
title: "Autenticação do usuário final: Python com o Data Lake Store usando o Azure Active Directory | Microsoft Docs"
description: "Saiba como obter a autenticação do usuário final com o Data Lake Store usando o Azure Active Directory com o Python"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/29/2017
ms.author: nitinme
ms.openlocfilehash: 27fe69753acc6fa047b5791a583d70e80318288a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="end-user-authentication-with-data-lake-store-using-python"></a>Autenticação do usuário final com o Data Lake Store usando o Python
> [!div class="op_single_selector"]
> * [Usando o Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Usar o SDK .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Usando o Python](data-lake-store-end-user-authenticate-python.md)
> * [Usar a API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
> 

Neste artigo, você aprenderá como usar o SDK do Python para fazer a autenticação do usuário final com o Azure Data Lake Store. A autenticação do usuário final pode ser adicionalmente dividida em duas categorias:

* Autenticação do usuário final sem autenticação multifator
* Autenticação do usuário final com autenticação multifator

Essas duas opções são discutidas neste artigo. Para saber sobre autenticação serviço a serviço com o Data Lake Store usando o Python, consulte [Autenticação serviço a serviço com Data Lake Store usando o Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Python**. Você pode baixar o Python [aqui](https://www.python.org/downloads/). Este artigo usa o Python 3.6.2.

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar um aplicativo "Nativo" do Azure Active Directory**. Você deve ter concluído as etapas em [Autenticação do usuário final com o Data Lake Store usando o Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar com o Data Lake Store usando o Python, você precisa instalar três módulos.

* O módulo `azure-mgmt-resource`, que inclui módulos do Azure para o Active Directory etc.
* O módulo `azure-mgmt-datalake-store`, que inclui as operações de gerenciamento de contas do Azure Data Lake Store. Para obter mais informações sobre esse módulo, consulte [Referência do módulo de gerenciamento do Azure Data Lake Store](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html).
* O módulo `azure-datalake-store`, que inclui as operações do sistema de arquivos do Azure Data Lake Store. Para obter mais informações sobre esse módulo, consulte [Referência do módulo de Sistema de Arquivos do Azure Data Lake Store](http://azure-datalake-store.readthedocs.io/en/latest/).

Use os comandos a seguir para instalar os módulos.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

1. No IDE à sua escolha, crie um novo aplicativo Python, por exemplo, **mysample.py**.

2. Adicione o seguinte trecho para importar os módulos necessários

    ```
    ## Use this for Azure AD authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
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

Use o seguinte trecho para se autenticar com o Azure AD para operações de gerenciamento de conta em uma conta do Data Lake Store. O trecho a seguir pode ser usado para autenticar seu aplicativo usando a autenticação multifator. Forneça os valores abaixo para um aplicativo **nativo** existente do Azure AD.

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
    credentials = AADTokenCredentials(mgmt_token, client_id)

### <a name="for-filesystem-operations"></a>Para operações de sistema de arquivos

Use isto para se autenticar no Azure AD para operações do sistema de arquivos em uma conta do Data Lake Store. O trecho a seguir pode ser usado para autenticar seu aplicativo usando a autenticação multifator. Forneça os valores abaixo para um aplicativo **nativo** existente do Azure AD.

    token = lib.auth(tenant_id='FILL-IN-HERE')

## <a name="end-user-authentication-without-multi-factor-authentication"></a>Autenticação do usuário final sem autenticação multifator

Isso foi preterido. Para obter mais informações, consulte [Autenticação do Azure usando o SDK do Python](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python#mgmt-auth-token).
   
## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar a autenticação do usuário final para se autenticar no Azure Data Lake Store usando o Python. Agora você pode examinar os seguintes artigos que discorrem sobre como usar o Python para trabalhar com o Azure Data Lake Store.

* [Operações de gerenciamento de contas no Data Lake Store usando o Python](data-lake-store-get-started-python.md)
* [Operações de dados no Data Lake Store usando Python](data-lake-store-data-operations-python.md)

