---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 6d6c4a019e9b0b9bd4ed52990fa8b59e939026f1
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "106073336"
---
Este início rápido usa um Azure Key Vault pré-criado. Você pode criar um cofre de chaves seguindo as etapas no [Início rápido da CLI do Azure](../articles/key-vault/general/quick-create-cli.md), no [Início rápido do Azure PowerShell](../articles/key-vault/general/quick-create-powershell.md) ou no [Início rápido do portal do Azure](../articles/key-vault/general/quick-create-portal.md). 

Como alternativa, você pode simplesmente executar os comandos da CLI do Azure ou o Azure PowerShell abaixo.

> [!Important]
> Cada cofre de chaves deve ter um nome exclusivo. Substitua <seu-nome-de cofre-de-chaves-exclusivo> pelo nome do seu cofre de chaves nos exemplos a seguir.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```
# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azurepowershell)

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location eastus

New-AzKeyVault -Name <your-unique-keyvault-name> -ResourceGroupName myResourceGroup -Location eastus
```
---