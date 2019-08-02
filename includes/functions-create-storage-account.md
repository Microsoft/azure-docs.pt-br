---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 25cfcefb600bc12de3dad5b6fe2bcb76d00f0198
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444106"
---
## <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

O Functions usa uma conta de propósito geral do Armazenamento do Azure para manter o estado e outras informações sobre suas funções. Crie uma conta de propósito geral de armazenamento no grupo de recursos que você criou ao utilizar o comando [az storage account create](/cli/azure/storage/account).

No comando a seguir, substitua um nome da conta de armazenamento globalmente exclusivo quando você vir o espaço reservado `<storage_name>`. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```
