---
title: Alterar chaves de acesso da conta de armazenamento
titleSuffix: Azure Machine Learning service
description: Saiba como alterar as chaves de acesso para a conta de armazenamento do Azure usada pelo seu espaço de trabalho. Azure Machine Learning serviço usa uma conta de armazenamento do Azure para armazenar dados e modelos. Ao regenerar a chave de acesso para a conta de armazenamento, você deve atualizar o serviço de Azure Machine Learning para usar as novas chaves.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 05/17/2019
ms.openlocfilehash: 0721542811709e9b938fea3f31bc2a0a28ecdc74
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358763"
---
# <a name="regenerate-storage-account-access-keys"></a>Regenerar chaves de acesso da conta de armazenamento

Saiba como alterar as chaves de acesso para contas de armazenamento do Azure usadas pelo serviço de Azure Machine Learning. Azure Machine Learning pode usar contas de armazenamento para armazenar dados ou modelos treinados.

Para fins de segurança, talvez seja necessário alterar as chaves de acesso de uma conta de armazenamento do Azure. Quando você regenera a chave de acesso, Azure Machine Learning deve ser atualizado para usar a nova chave. Azure Machine Learning pode estar usando a conta de armazenamento para o armazenamento de modelo e como um repositório de armazenamento.

## <a name="prerequisites"></a>Pré-requisitos

* Um workspace de serviço do Azure Machine Learning. Para obter mais informações, consulte o artigo [criar um espaço de trabalho](setup-create-workspace.md) .

* O [SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* A [extensão da CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md).

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>O que precisa ser atualizado

As contas de armazenamento podem ser usadas pelo espaço de trabalho do Azure Machine Learning Service (armazenando logs, modelos, instantâneos, etc.) e como um repositório de armazenamento. O processo para atualizar o espaço de trabalho é um único CLI do Azure comando e pode ser executado após a atualização da chave de armazenamento. O processo de atualização de repositórios de armazenamento é mais envolvido e requer a descoberta de quais armazenamentos de armazenamento estão usando a conta de armazenagem no momento e, em seguida, o registro deles novamente.

> [!IMPORTANT]
> Atualize o espaço de trabalho usando o CLI do Azure e os repositórios de armazenamento que usam o Python, ao mesmo tempo. Atualizar apenas um ou outro não é suficiente e pode causar erros até que ambos sejam atualizados.

Para descobrir as contas de armazenamento que são usadas por seus repositórios de armazenamento, use o seguinte código:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob" or ds.datastore_type == "AzureFile":
        print("datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

Esse código procura por todos os armazenamentos de dados registrados que usam o armazenamento do Azure e lista as seguintes informações:

* Nome do repositório de armazenamento: O nome do armazenamento de datastore no qual a conta de armazenamento está registrada.
* Nome da conta de armazenamento: O nome da conta de armazenamento do Azure.
* Container O contêiner na conta de armazenamento que é usado por esse registro.

Se existir uma entrada para a conta de armazenamento para a qual você planeja regenerar chaves de acesso, salve o nome do repositório de armazenamento, o nome da conta e o nome do contêiner.

## <a name="update-the-access-key"></a>Atualizar a chave de acesso

Para atualizar Azure Machine Learning serviço para usar a nova chave, use as seguintes etapas:

> [!IMPORTANT]
> Execute todas as etapas, atualizando o espaço de trabalho usando a CLI e os repositórios de armazenamento usando o Python. Atualizar apenas um ou outro pode causar erros até que ambos sejam atualizados.

1. Gere novamente a chave. Para obter informações sobre como regenerar uma chave de acesso, consulte o artigo [gerenciar uma conta de armazenamento](/azure/storage/common/storage-account-manage#access-keys) . Salve a nova chave.

1. Para atualizar o espaço de trabalho para usar a nova chave, use as seguintes etapas:

    1. Para entrar na assinatura do Azure que contém seu espaço de trabalho usando o seguinte comando de CLI do Azure:

        ```azurecli-interactive
        az login
        ```

    1. Para instalar a extensão de Azure Machine Learning, use o seguinte comando:

        ```azurecli-interactive
        az extension add -n azure-cli-ml 
        ```

    1. Para atualizar o espaço de trabalho para usar a nova chave, use o comando a seguir. Substitua `myworkspace` pelo nome do espaço de trabalho Azure Machine Learning e `myresourcegroup` substitua pelo nome do grupo de recursos do Azure que contém o espaço de trabalho.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        Esse comando sincroniza automaticamente as novas chaves para a conta de armazenamento do Azure usada pelo espaço de trabalho.

1. Para registrar novamente os repositórios de os que usam a conta de armazenamento, use os valores da seção [o que precisa ser atualizado](#whattoupdate) e a chave da etapa 1 com o seguinte código:

    ```python
    ds = Datastore.register_azure_blob_container(workspace=ws, 
                                              datastore_name='your datastore name', 
                                              container_name='your container name',
                                              account_name='your storage account name', 
                                              account_key='new storage account key',
                                              overwrite=True)
    ```

    Como `overwrite=True` é especificado, esse código substitui o registro existente e o atualiza para usar a nova chave.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como registrar armazenamentos de dados, [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) consulte a referência de classe.
