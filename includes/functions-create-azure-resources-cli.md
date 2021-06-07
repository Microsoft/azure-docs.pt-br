---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 99ae35aca485ac928f7c5ef9f98295eed4bc1245
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99500422"
---
## <a name="create-supporting-azure-resources-for-your-function"></a>Criar recursos de suporte do Azure para a função

Antes de poder implantar o código da função no Azure, você precisa criar três recursos:

- Um [grupo de recursos](../articles/azure-resource-manager/management/overview.md), que é um contêiner lógico para recursos relacionados.
- Uma [conta de armazenamento](../articles/storage/common/storage-account-create.md), que é usada para manter o estado e outras informações sobre suas funções.
- Um aplicativo de funções, que fornece o ambiente para a execução do código de função. Um aplicativo de funções é mapeado para seu projeto de função local e permite agrupar funções como uma unidade lógica para facilitar o gerenciamento, a implantação e o compartilhamento de recursos.

Use os comandos a seguir para criar esses itens. Tanto a CLI do Azure quanto o PowerShell são compatíveis.

1. Se você ainda não tiver feito isso, entre no Azure:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    ```azurecli
    az login
    ```

    O comando [az login](/cli/azure/reference-index#az_login) conecta você à conta do Azure.

    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    O cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) conecta você à conta do Azure.

    ---

1. Crie um grupo de recursos chamado `AzureFunctionsQuickstart-rg` na região `westeurope`:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    O comando [az group create](/cli/azure/group#az_group_create) cria um grupo de recursos. De modo geral, você cria o grupo de recursos e os recursos em uma região próxima a você usando uma região disponível retornada do comando `az account list-locations`.

    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    O comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cria um grupo de recursos. De modo geral, você cria o grupo de recursos e os recursos em uma região próxima a você usando uma região disponível retornada do cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

    ---

1. Crie uma conta de armazenamento para uso geral no grupo de recursos e na região:

    # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    O comando [az storage account create](/cli/azure/storage/account#az_storage_account_create) cria a conta de armazenamento. 

    # <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    O cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cria a conta de armazenamento.

    ---

    No exemplo anterior, substitua `<STORAGE_NAME>` por um nome que seja apropriado para você e exclusivo no Armazenamento do Azure. Os nomes devem conter de 3 a 24 caracteres e podem conter somente números e letras minúsculas. `Standard_LRS` especifica uma conta de uso geral, que é [compatível com o Functions](../articles/azure-functions/storage-considerations.md#storage-account-requirements).
