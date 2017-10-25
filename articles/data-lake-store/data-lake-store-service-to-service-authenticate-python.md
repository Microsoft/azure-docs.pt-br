---
title: "Autenticação serviço a serviço: Python com o Data Lake Store usando o Azure Active Directory | Microsoft Docs"
description: "Saiba como obter a autenticação serviço a serviço com o Data Lake Store usando o Azure Active Directory com o Python"
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
ms.date: 10/05/2017
ms.author: nitinme
ms.openlocfilehash: d884e3ab71ae1967d1676bc0591ef86f64da6c5b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-to-service-authentication-with-data-lake-store-using-python"></a>Autenticação serviço a serviço com o Data Lake Store usando o Python
> [!div class="op_single_selector"]
> * [Usando o Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Usar o SDK .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Usando o Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Usar a API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

Neste artigo, você aprenderá como usar o SDK do Python para fazer a autenticação serviço a serviço com o Azure Data Lake Store. Para a autenticação do usuário final com o Data Lake Store usando o Python, consulte [Autenticação do usuário final com Data Lake Store usando Python](data-lake-store-end-user-authenticate-python.md).


## <a name="prerequisites"></a>Pré-requisitos

* **Python**. Você pode baixar o Python [aqui](https://www.python.org/downloads/). Este artigo usa o Python 3.6.2.

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Criar um aplicativo "Web" do Azure Active Directory**. Você deve ter concluído as etapas em [Autenticação serviço a serviço com o Data Lake Store usando o Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

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

1. Use o IDE de sua escolha para criar um novo aplicativo Python, por exemplo, **mysample.py**.

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
    import logging, getpass, pprint, uuid, time
    ```

3. Salve as alterações a mysample.py.

## <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Autenticação de serviço a serviço com o segredo do cliente para gerenciamento de conta

Use este trecho de código para autenticar no Azure AD para realizar operações de gerenciamento de conta no Data Lake Store tais como criar conta do Data Lake Store, excluir conta do Data Lake Store, etc. O trecho a seguir pode ser usado para autenticar seu aplicativo de forma não interativa, usando segredo do cliente para entidade de serviço/aplicativo de um aplicativo "Web App" existente do Azure AD.

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    resource_uri = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_secret = '<CLIENT_SECRET>'

    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_credentials(resource_uri, client_id, client_secret)
    credentials = AADTokenCredentials(mgmt_token, client_id)

## <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Autenticação de serviço a serviço com o segredo do cliente para operações do sistema de arquivos

Use o trecho a seguir para autenticar no Azure AD para realizar operações de sistema de arquivos no Data Lake Store como criar a pasta, upload de arquivo, etc. O trecho a seguir pode ser usado para autenticar seu aplicativo de forma não interativa, usando segredo do cliente para entidade de segurança/aplicativo. Use-o com o aplicativo "Aplicativo Web" Azure AD existente.

    token = lib.auth(tenant_id = 'FILL-IN-HERE', client_secret = 'FILL-IN-HERE', client_id = 'FILL-IN-HERE')

## <a name="service-to-service-authentication-with-certificate-for-account-management"></a>Autenticação serviço a serviço com certificado para gerenciamento de conta

Use este trecho de código para autenticar no Azure AD para realizar operações de gerenciamento de conta no Data Lake Store tais como criar conta do Data Lake Store, excluir conta do Data Lake Store, etc. O trecho a seguir pode ser usado para autenticar seu aplicativo de forma não interativa, usando o certificado de um aplicativo "Web App" existente do Azure AD. Para obter instruções sobre como criar um aplicativo do Azure AD, consulte [Criar entidade de serviço com certificados](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

    authority_host_uri = 'https://login.microsoftonline.com'
    tenant = '<TENANT>'
    authority_uri = authority_host_uri + '/' + tenant
    resource_uri = 'https://management.core.windows.net/'
    client_id = '<CLIENT_ID>'
    client_cert = '<CLIENT_CERT>'
    client_cert_thumbprint = '<CLIENT_CERT_THUMBPRINT>'

    context = adal.AuthenticationContext(authority_uri, api_version=None)
    mgmt_token = context.acquire_token_with_client_certificate(resource_uri, client_id, client_cert, client_cert_thumbprint)
    credentials = AADTokenCredentials(mgmt_token, client_id)

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar a autenticação serviço a serviço para autenticar no Azure Data Lake Store com o Python. Agora você pode examinar os seguintes artigos que discorrem sobre como usar o Python para trabalhar com o Azure Data Lake Store.

* [Operações de gerenciamento de contas no Data Lake Store usando o Python](data-lake-store-get-started-python.md)
* [Operações de dados no Data Lake Store usando Python](data-lake-store-data-operations-python.md)


