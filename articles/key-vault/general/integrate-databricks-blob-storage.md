---
title: Acessar o Armazenamento de Blobs do Azure usando o Azure Databricks e o Azure Key Vault
description: Neste tutorial, você aprenderá a acessar o Armazenamento de Blobs do Azure por meio do Azure Databricks usando um segredo armazenado no Azure Key Vault
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/16/2020
ms.openlocfilehash: be1b0da23841b5a63ec044e04a5465e29345f9d1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772191"
---
# <a name="tutorial-access-azure-blob-storage-using-azure-databricks-and-azure-key-vault"></a>Tutorial: Acessar o Armazenamento de Blobs do Azure usando o Azure Databricks e o Azure Key Vault

Neste tutorial, você aprenderá a acessar o armazenamento de Blobs do Azure por meio do Azure Databricks usando um segredo armazenado no Azure Key Vault. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento e um contêiner de blobs com a CLI do Azure
> * Criar um Key Vault e definir um segredo
> * Criar um workspace do Azure Databricks e adicionar um escopo de segredo do Key Vault
> * Acessar seu contêiner de blobs por meio do workspace do Azure Databricks

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Antes de iniciar este tutorial, instale a [CLI do Azure](/cli/azure/install-azure-cli-windows).

## <a name="create-a-storage-account-and-blob-container-with-azure-cli"></a>Criar uma conta de armazenamento e um contêiner de blobs com a CLI do Azure

Você precisará criar uma conta de armazenamento para uso geral primeiro a fim de usar os blobs. Caso não tenha um [grupo de recursos](/cli/azure/group#az_group_create), crie um antes de executar o comando. O comando a seguir cria e exibe os metadados do contêiner de armazenamento. Copie a **ID**.

```azurecli
az storage account create --name contosoblobstorage5 --resource-group contosoResourceGroup --location eastus --sku Standard_ZRS --encryption-services blob
```

![Saída do console do comando acima. A ID é realçada em verde para visualização do usuário final.](../media/databricks-command-output-1.png)

Para criar um contêiner no qual o blob será carregado, você precisará atribuir a função [Colaborador de Dados no Blob de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) a si mesmo. Para este exemplo, a função será atribuída à conta de armazenamento criada anteriormente.

```azurecli
az role assignment create --role "Storage Blob Data Contributor" --assignee t-trtr@microsoft.com --scope "/subscriptions/885e24c8-7a36-4217-b8c9-eed31e110504/resourceGroups/contosoResourceGroup5/providers/Microsoft.Storage/storageAccounts/contosoblobstorage5
```

Agora que você atribuiu a função à conta de armazenamento, você poderá criar um contêiner para o blob.

```azurecli
az storage container create --account-name contosoblobstorage5 --name contosocontainer5 --auth-mode login
```

Depois que o contêiner for criado, você poderá carregar um blob (um arquivo de sua escolha) nesse contêiner. Neste exemplo, um arquivo .txt com helloworld é carregado.

```azurecli
az storage blob upload --account-name contosoblobstorage5 --container-name contosocontainer5 --name helloworld --file helloworld.txt --auth-mode login
```

Liste os blobs no contêiner para verificar se o contêiner o tem.

```azurecli
az storage blob list --account-name contosoblobstorage5 --container-name contosocontainer5 --output table --auth-mode login
```

![Saída do console do comando acima. Ela exibe o arquivo que acabou de ser armazenado no contêiner.](../media/databricks-command-output-2.png)

Obtenha o valor **key1** do contêiner de armazenamento usando o comando a seguir. Copie o valor.

```azurecli
az storage account keys list -g contosoResourceGroup5 -n contosoblobstorage5
```

![Saída do console do comando acima. O valor de key1 é realçado em uma caixa verde.](../media/databricks-command-output-3.png)

## <a name="create-a-key-vault-and-set-a-secret"></a>Criar um Key Vault e definir um segredo

Você criará um Key Vault usando o comando a seguir. Esse comando também exibirá os metadados do Key Vault. Copie a **ID** e o **vaultUri**.

```azurecli
az keyvault create --name contosoKeyVault10 --resource-group contosoResourceGroup5 --location eastus
```

![Imagem](../media/databricks-command-output-4.png)
![Saída do console do comando acima. A ID e o vaultUri são realçados em verde para visualização do usuário.](../media/databricks-command-output-5.png)

Para criar o segredo, use o comando a seguir. Defina o valor do segredo com o valor de **key1** na sua conta de armazenamento.

```azurecli
az keyvault secret set --vault-name contosoKeyVault10 --name storageKey --value "value of your key1"
```

## <a name="create-an-azure-databricks-workspace-and-add-key-vault-secret-scope"></a>Criar um workspace do Azure Databricks e adicionar um escopo de segredo do Key Vault

Esta seção não pode ser concluída por meio da linha de comando. Siga este [guia](/azure/databricks/scenarios/store-secrets-azure-key-vault#create-an-azure-databricks-workspace-and-add-a-secret-scope). Você precisará acessar o [portal do Azure](https://ms.portal.azure.com/#home) para:

1. Criar o recurso do Azure Databricks
1. Iniciar o workspace
1. Criar um escopo de segredo com backup do Key Vault

## <a name="access-your-blob-container-from-azure-databricks-workspace"></a>Acessar seu contêiner de blobs por meio do workspace do Azure Databricks

Esta seção não pode ser concluída por meio da linha de comando. Siga este [guia](/azure/databricks/scenarios/store-secrets-azure-key-vault#access-your-blob-container-from-azure-databricks). Você precisará usar o workspace do Azure Databricks para:

1. Criar um **Cluster**
1. Criar um **Notebook**
1. Preencher os campos correspondentes no script do Python
1. Executar o script do Python

```python
dbutils.fs.mount(
source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
mount_point = "/mnt/<mount-name>",
extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})

df = spark.read.text("/mnt/<mount-name>/<file-name>")

df.show()
```

## <a name="next-steps"></a>Próximas etapas

Garantir que o Key Vault seja recuperável:
> [!div class="nextstepaction"]
> [Limpar seus recursos](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell)