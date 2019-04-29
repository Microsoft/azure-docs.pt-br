---
title: Conectar-se ao Azure Data Explorer a partir do Azure Databricks usando Python
description: Este tópico mostra como usar uma biblioteca do Python no Azure Databricks para acessar dados do Azure Data Explorer usando um dos dois métodos de autenticação.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 55257d441916971b505432247f28033d6222c3be
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60861193"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-by-using-python"></a>Conectar-se ao Azure Data Explorer a partir do Azure Databricks usando Python

O [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) é uma plataforma de análise baseada no Apache Spark otimizada para a plataforma do Microsoft Azure. Este artigo mostra como usar uma biblioteca do Python no Azure Databricks para acessar dados do Azure Data Explorer. Há várias maneiras de se autenticar com o Azure Data Explorer, incluindo o login do dispositivo e o aplicativo Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Pré-requisitos

- [Criar um cluster e um banco de dados no Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal).
- [Criar um espaço de trabalho no Azure Databricks](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace). Em **Serviço do Azure Databricks**, na lista suspensa **Tipo de preço**, selecione **Premium**. Essa seleção permite que você use os segredos do Azure Databricks para armazenar suas credenciais e mencioná-las em notebooks e trabalhos.

- [Crie um cluster](https://docs.azuredatabricks.net/user-guide/clusters/create.html) em bancos de dados do Azure Databricks com as seguintes especificações (configurações mínimas necessárias para executar os blocos de notas de amostra):

   ![Especificações para a criação de um cluster](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-the-python-library-on-your-azure-databricks-cluster"></a>Instale a biblioteca do Python em seu cluster do Azure Databricks

Como instalar a [biblioteca do Python](/azure/kusto/api/python/kusto-python-client-library) em seu cluster do Azure Databricks:

1. Acesse o espaço de trabalho do Azure Databricks e [crie uma biblioteca](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library).
2. [Carregue um pacote de PyPI Python ou Python Egg](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg).
   - Carregue, instale e anexe a biblioteca ao seu cluster do Databricks.
   - Digite o nome do PyPi: **azure-kusto-data**.

## <a name="connect-to-azure-data-explorer-by-using-a-device-login"></a>Conectar-se ao Azure Data Explorer usando um logon de dispositivo

[Importe um notebook](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) usando o notebook [Query-ADX-device-login](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb). Posteriormente, você pode conectar-se ao Azure Data Explorer usando suas credenciais.

## <a name="connect-to-adx-by-using-an-azure-ad-app"></a>Conectar-se ao ADX usando o Aplicativo Azure AD

1. Crie o aplicativo Azure AD [provisionando um aplicativo do Azure AD](/azure/kusto/management/access-control/how-to-provision-aad-app).
1. Conceda acesso ao seu aplicativo Azure AD em seu banco de dados do Azure Data Explorer da seguinte maneira:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | o nome do seu banco de dados |
    | ```AAD App ID``` | sua ID do aplicativo Azure AD |
    | ```AAD Tenant ID``` | sua ID de locatário do Azure AD |

### <a name="find-your-azure-ad-tenant-id"></a>Encontrar sua ID de locatário do Azure AD

Para autenticar um aplicativo, o Data Explorer do Azure usa seu ID de locatário do Microsoft Azure AD. Para localizar sua ID de locatário, use a URL a seguir. Substitua o domínio por *SeuDomínio*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Por exemplo, se o seu domínio for *contoso.com*, a URL será: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Selecione essa URL para ver os resultados. A primeira linha tem a seguinte aparência: 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Seu locatário ID é `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Armazenar e proteger sua ID e chave do aplicativo Azure AD 

Armazene e proteja a ID e a chave do aplicativo Azure AD usando os segredos do [Azure Databricks](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) da seguinte forma:
1. [Configurar a CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli).
1. [Instalar a CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 
1. [Configurar a autenticação](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Configurar os [segredos](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) usando os comandos de exemplo a seguir:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Importar um notebook
[Importe um notebook](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) usando o notebook [Query-ADX-AAD-App](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) para se conectar ao Azure Data Explorer. Atualize os valores de espaço reservado com seu nome de cluster, nome de banco de dados e sua ID de locatário do Azure AD.
