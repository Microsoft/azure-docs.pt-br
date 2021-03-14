---
title: Alterar chaves de acesso da conta de armazenamento
titleSuffix: Azure Machine Learning
description: Saiba como alterar as chaves de acesso para a conta de armazenamento do Azure usada pelo seu espaço de trabalho. Azure Machine Learning usa uma conta de armazenamento do Azure para armazenar dados e modelos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/19/2020
ms.openlocfilehash: 787f7727e18b7055055ed2b764ddce5be20f5afc
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503429"
---
# <a name="regenerate-storage-account-access-keys"></a>Regenerar chaves de acesso da conta de armazenamento


Saiba como alterar as chaves de acesso para contas de armazenamento do Azure usadas pelo Azure Machine Learning. Azure Machine Learning pode usar contas de armazenamento para armazenar dados ou modelos treinados.

Para fins de segurança, talvez seja necessário alterar as chaves de acesso de uma conta de armazenamento do Azure. Quando você regenera a chave de acesso, Azure Machine Learning deve ser atualizado para usar a nova chave. Azure Machine Learning pode estar usando a conta de armazenamento para o armazenamento de modelo e como um repositório de armazenamento.

> [!IMPORTANT]

> As credenciais registradas com repositórios de armazenamento são salvas em seu Azure Key Vault associado ao espaço de trabalho. Se você tiver a [exclusão reversível](../key-vault/general/soft-delete-overview.md) habilitada para seu Key Vault, este artigo fornecerá instruções para atualizar as credenciais. Se você cancelar o registro do repositório de armazenamento e tentar registrá-lo novamente com o mesmo nome, essa ação falhará. Consulte [Ativar exclusão reversível para um cofre de chaves existente]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault) para saber como habilitar a exclusão reversível neste cenário.

## <a name="prerequisites"></a>Pré-requisitos

* Um Workspace do Azure Machine Learning. Para obter mais informações, consulte o artigo [criar um espaço de trabalho](how-to-manage-workspace.md) .

* O [SDK do Azure Machine Learning](/python/api/overview/azure/ml/install).

* A [extensão da CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md).

> [!NOTE]
> Os trechos de código neste documento foram testados com a versão 1.0.83 do SDK do Python.

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>O que precisa ser atualizado

As contas de armazenamento podem ser usadas pelo espaço de trabalho Azure Machine Learning (armazenando logs, modelos, instantâneos, etc.) e como um repositório de armazenamento. O processo para atualizar o espaço de trabalho é um único CLI do Azure comando e pode ser executado após a atualização da chave de armazenamento. O processo de atualização de repositórios de armazenamento é mais envolvido e requer a descoberta de quais armazenamentos de armazenamento estão usando a conta de armazenagem no momento e, em seguida, o registro deles novamente.

> [!IMPORTANT]
> Atualize o espaço de trabalho usando o CLI do Azure e os repositórios de armazenamento que usam o Python, ao mesmo tempo. Atualizar apenas um ou outro não é suficiente e pode causar erros até que ambos sejam atualizados.

Para descobrir as contas de armazenamento que são usadas por seus repositórios de armazenamento, use o seguinte código:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + default_ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob":
        print("Blob store - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
    if ds.datastore_type == "AzureFile":
        print("File share - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

Esse código procura por todos os armazenamentos de dados registrados que usam o armazenamento do Azure e lista as seguintes informações:

* Nome do repositório de armazenamento: o nome do armazenamento de datastore no qual a conta de repositório está registrada.
* Nome da conta de armazenamento: o nome da conta de armazenamento do Azure.
* Contêiner: o contêiner na conta de armazenamento que é usado por esse registro.

Ele também indica se o repositório de armazenamento é para um blob do Azure ou um compartilhamento de arquivos do Azure, já que há métodos diferentes para registrar novamente cada tipo de repositório de armazenamento.

Se existir uma entrada para a conta de armazenamento para a qual você planeja regenerar chaves de acesso, salve o nome do repositório de armazenamento, o nome da conta e o nome do contêiner.

## <a name="update-the-access-key"></a>Atualizar a chave de acesso

Para atualizar Azure Machine Learning para usar a nova chave, use as seguintes etapas:

> [!IMPORTANT]
> Execute todas as etapas, atualizando o espaço de trabalho usando a CLI e os repositórios de armazenamento usando o Python. Atualizar apenas um ou outro pode causar erros até que ambos sejam atualizados.

1. Gere novamente a chave. Para obter informações sobre como regenerar uma chave de acesso, consulte [gerenciar chaves de acesso da conta de armazenamento](../storage/common/storage-account-keys-manage.md). Salve a nova chave.

1. O espaço de trabalho Azure Machine Learning sincronizará automaticamente a nova chave e começará a usá-la após uma hora. Para forçar o espaço de trabalho a sincronizar a nova chave imediatamente, use as seguintes etapas:

    1. Para entrar na assinatura do Azure que contém seu espaço de trabalho usando o seguinte comando de CLI do Azure:

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. Para atualizar o espaço de trabalho para usar a nova chave, use o comando a seguir. Substitua `myworkspace` pelo nome do espaço de trabalho Azure Machine Learning e substitua `myresourcegroup` pelo nome do grupo de recursos do Azure que contém o espaço de trabalho.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        Esse comando sincroniza automaticamente as novas chaves para a conta de armazenamento do Azure usada pelo espaço de trabalho.

1. Você pode registrar novamente os armazenamentos de os (s) que usam a conta de armazenamento por meio do SDK ou [do Azure Machine Learning Studio](https://ml.azure.com).
    1. **Para registrar novamente os repositórios de armazenamento por meio do SDK do Python**, use os valores da seção [o que precisa ser atualizado](#whattoupdate) e a chave da etapa 1 com o código a seguir. 
    
        Como `overwrite=True` é especificado, esse código substitui o registro existente e o atualiza para usar a nova chave.
    
        ```python
        # Re-register the blob container
        ds_blob = Datastore.register_azure_blob_container(workspace=ws,
                                                  datastore_name='your datastore name',
                                                  container_name='your container name',
                                                  account_name='your storage account name',
                                                  account_key='new storage account key',
                                                  overwrite=True)
        # Re-register file shares
        ds_file = Datastore.register_azure_file_share(workspace=ws,
                                              datastore_name='your datastore name',
                                              file_share_name='your container name',
                                              account_name='your storage account name',
                                              account_key='new storage account key',
                                              overwrite=True)
        
        ```
    
    1. **Para registrar novamente os repositórios de armazenamento por meio do estúdio**, selecione **repositórios de armazenamento** no painel esquerdo do estúdio. 
        1. Selecione qual repositório de armazenamento você deseja atualizar.
        1. Selecione o botão **Atualizar credenciais** na parte superior esquerda. 
        1. Use a nova chave de acesso da etapa 1 para preencher o formulário e clique em **salvar**.
        
            Se você estiver atualizando as credenciais para seu **repositório de armazenamento padrão**, conclua esta etapa e repita a etapa 2B para ressincronizar sua nova chave com o repositório de armazenamento padrão do espaço de trabalho. 

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como registrar armazenamentos de dados, consulte a [`Datastore`](/python/api/azureml-core/azureml.core.datastore%28class%29) referência de classe.