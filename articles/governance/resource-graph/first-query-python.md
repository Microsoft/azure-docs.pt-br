---
title: 'Início Rápido: Sua primeira consulta Python'
description: Neste início rápido, você seguirá as etapas para habilitar a biblioteca do Resource Graph para Python e executará sua primeira consulta.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 0bae0639e3f9913bc47b18fbc0b1d3d9ef1ad324
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533014"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-python"></a>Início Rápido: Executar a primeira consulta ao Resource Graph usando o Python

A primeira etapa para usar o Azure Resource Graph é verificar se essas bibliotecas necessárias para Python estão instaladas. Este início rápido orienta você no processo de adição das bibliotecas à instalação do Python.

No fim desse processo, você terá adicionado as bibliotecas à sua instalação do Python e executará a primeira consulta ao Resource Graph.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Adicionar a biblioteca do Resource Graph

Para habilitar o Python para consultar o Azure Resource Graph, a biblioteca deve ser adicionada. Essa biblioteca funciona onde quer que o Python possa ser usado, incluindo o [bash no Windows 10](/windows/wsl/install-win10) ou instalado localmente.

1. Verifique se o Python mais recente está instalado (pelo menos a versão **3.8**). Se ele ainda não tiver sido instalado, baixe-o em [Python.org](https://www.python.org/downloads/).

1. Verifique se a CLI do Azure mais recente está instalada (pelo menos a versão **2.5.1**). Se ela ainda não tiver sido instalada, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

   > [!NOTE]
   > A CLI do Azure é necessária para habilitar o Python a usar a **autenticação baseada na CLI** do nos exemplos a seguir. Para obter informações sobre outras opções, confira [Autenticar-se usando as bibliotecas de gerenciamento do Azure para Python](/azure/developer/python/azure-sdk-authenticate).

1. Autentique-se por meio da CLI do Azure.

   ```azurecli
   az login
   ```

1. No ambiente do Python de sua escolha, instale as bibliotecas necessárias para o Azure Resource Graph:

   ```bash
   # Add the Resource Graph library for Python
   pip install azure-mgmt-resourcegraph

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Se o Python estiver instalado para todos os usuários, esse comando precisará ser executado em um console com privilégios elevados.

1. Confirme se as bibliotecas foram instaladas. `azure-mgmt-resourcegraph` deve ser **2.0.0** ou mais recente, `azure-mgmt-resource` deve ser **9.0.0** ou mais recente e `azure-cli-core` deve ser **2.5.0** ou mais recente.

   ```bash
   # Check each installed library
   pip show azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
   ```

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta ao Resource Graph

Com as bibliotecas do Python adicionadas ao ambiente de sua escolha, é hora de experimentar uma consulta simples ao Resource Graph. A consulta retorna os cinco primeiros recursos do Azure com o **Nome** e o **Tipo de Recurso** de cada um.

1. Execute a primeira consulta ao Azure Resource Graph usando as bibliotecas instaladas e o método `resources`:

   ```python
   # Import Azure Resource Graph library
   import azure.mgmt.resourcegraph as arg
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import SubscriptionClient
   
   # Wrap all the work in a function
   def getresources( strQuery ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))
       
       # Create Azure Resource Graph client and set options
       argClient = get_client_from_cli_profile(arg.ResourceGraphClient)
       argQueryOptions = arg.models.QueryRequestOptions(result_format="objectArray")
       
       # Create query
       argQuery = arg.models.QueryRequest(subscriptions=subsList, query=strQuery, options=argQueryOptions)
       
       # Run query
       argResults = argClient.resources(argQuery)
   
       # Show Python object
       print(argResults)
   
   getresources("Resources | project name, type | limit 5")
   ```

   > [!NOTE]
   > Devido a essa consulta de exemplo não fornecer um modificador de classificação, tal como `order by`, executar essa consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação.

1. Atualize a chamada a `getresources` e altere a consulta para `order by` a propriedade **Name**:

   ```python
   getresources("Resources | project name, type | limit 5 | order by name asc")
   ```

   > [!NOTE]
   > Assim como ocorre com a primeira consulta, executar esta consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação. A ordem dos comandos de consulta é importante. Neste exemplo, o `order by` vem após o `limit`. Essa ordem de comando limita primeiro os resultados da consulta e os ordena.

1. Atualize a chamada a `getresources` e altere a consulta para, primeiro, `order by` a propriedade **Name** e depois `limit` conforme os primeiros cinco resultados:

   ```python
   getresources("Resources | project name, type | order by name asc | limit 5")
   ```

Quando a consulta final for executada várias vezes, supondo que nada em seu ambiente esteja sendo alterado, os resultados retornados serão consistentes e ordenados segundo a propriedade **Name**, mas ainda limitados aos cinco primeiros resultados.

## <a name="clean-up-resources"></a>Limpar os recursos

Se desejar remover as bibliotecas instaladas do seu ambiente do Python, você poderá fazer isso usando o seguinte comando:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você adicionou as bibliotecas do Resource Graph ao seu ambiente do Python e executou sua primeira consulta. Para saber mais sobre a linguagem do Resource Graph, prossiga para a página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obter mais informações sobre a linguagem de consulta](./concepts/query-language.md)
