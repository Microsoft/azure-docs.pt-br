---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 1f8f8d314a8bb37a08b3696f597b395a8a4beb8e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172146"
---
## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre na sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela.

```powershell
Connect-AzAccount
```

Se você não sabe qual localização deseja usar, você pode listar as localizações disponíveis. Exiba a lista de locais usando o seguinte exemplo de código e encontrar aquele que você deseja usar. Esse exemplo usa **eastus**. Armazene o local em uma variável e use a variável para que você possa alterá-la em um único local.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

```powershell
$resourceGroup = "myResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma conta de armazenamento para uso geral padrão com replicação LRS usando [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). A seguir, obtenha o contexto da conta de armazenamento que define a conta de armazenamento que você deseja usar. Ao agir em uma conta de armazenamento, você pode referenciar o contexto em vez de passar repetidamente as credenciais. Use o exemplo a seguir para criar uma conta de armazenamento chamada *mystorageaccount* com armazenamento com redundância local (LRS) e criptografia de blob (habilitada por padrão).

```powershell
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -SkuName Standard_LRS `
  -Location $location `

$ctx = $storageAccount.Context
```
