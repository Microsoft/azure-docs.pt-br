---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 44ee258567ca357687feb24337f2d5974e2532b0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172536"
---
## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com o comando [az group create](/cli/azure/group). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

```azurecli-interactive
az group create \
    --name myResourceGroup \
    --location eastus
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma conta de armazenamento de uso geral com o comando [az storage account create](/cli/azure/storage/account). A conta de armazenamento de uso geral pode ser usada para todos os quatro serviços: blobs, arquivos, tabelas e filas. 

```azurecli-interactive
az storage account create \
    --name mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus \
    --sku Standard_LRS \
    --encryption blob
```

## <a name="specify-storage-account-credentials"></a>Especificar as credenciais da conta de armazenamento

A CLI do Azure precisa das suas credenciais da conta de armazenamento para a maioria dos comandos neste tutorial. Embora haja várias opções para fazer isso, uma das maneiras mais fáceis de fornecê-las é definir as variáveis de ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_KEY`.

Primeiro, exiba as chaves da conta de armazenamento usando o comando [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive
az storage account keys list \
    --account-name mystorageaccount \
    --resource-group myResourceGroup \
    --output table
```

Agora, defina as variáveis de ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_KEY`. Você pode fazer isso no shell do Bash usando o comando `export`:

```bash
export AZURE_STORAGE_ACCOUNT="mystorageaccountname"
export AZURE_STORAGE_KEY="myStorageAccountKey"
```
