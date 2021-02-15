---
title: Configurar Key Vault usando o PowerShell
description: Como configurar Key Vault para uso com uma máquina virtual usando o PowerShell.
author: mimckitt
manager: vashan
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/24/2017
ms.author: mimckitt
ms.openlocfilehash: dd4a6ed5f57c536a9e40b2fd81b96d055702b425
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678317"
---
# <a name="set-up-key-vault-for-virtual-machines-using-azure-powershell"></a>Configurar Key Vault para máquinas virtuais usando Azure PowerShell

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

Na pilha do Azure Resource Manager, os certificados/segredos são modelados como recursos que são fornecidos pelo provedor de recursos do Cofre de Chaves. Para saber mais sobre o Cofre de Chaves, consulte [O que é o Cofre de Chaves do Azure?](../../key-vault/general/overview.md)

> [!NOTE]
> 1. Para que um Cofre de Chaves seja usado com máquinas virtuais do Azure Resource Manager, a propriedade **EnabledForDeployment** no Cofre de Chaves deverá ser definida como true. Você pode fazer isso em vários clientes.
> 2. O Cofre de Chaves precisa ser criado na mesma assinatura e na mesma localização que a Máquina Virtual.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Usar o PowerShell para configurar o Cofre de Chaves
Para criar um key vault usando o PowerShell, consulte [Definir e recuperar um segredo do Azure Key Vault usando o PowerShell](../../key-vault/secrets/quick-create-powershell.md).

Para novos cofres de chaves, você pode usar este cmdlet do PowerShell:

```azurepowershell
New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment
```

Para cofres de chaves existentes, você pode usar este cmdlet do PowerShell:

```azurepowershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment
```

## <a name="use-cli-to-set-up-key-vault"></a>Usar a CLI para configurar o Cofre de Chaves
Para criar um cofre de chaves usando a CLI (interface de linha de comando), consulte [Gerenciar Cofre da Chave usando a CLI](../../key-vault/general/manage-with-cli2.md#create-a-key-vault).

Para a CLI, você precisa criar o cofre de chaves antes de atribuir a política de implantação. Faça isso usando este comando:

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
```

Em seguida, para habilitar Key Vault para uso com a implantação de modelo, execute o seguinte comando:

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
```

## <a name="use-templates-to-set-up-key-vault"></a>Usar modelos para configurar o Cofre de Chaves
Ao usar um modelo, você precisa definir a propriedade `enabledForDeployment` como `true` para o recurso de Cofre de Chaves.

```config
{
  "type": "Microsoft.KeyVault/vaults",
  "name": "ContosoKeyVault",
  "apiVersion": "2015-06-01",
  "location": "<location-of-key-vault>",
  "properties": {
    "enabledForDeployment": "true",
    ....
    ....
  }
}
```

Para obter outras opções que você pode configurar ao criar um cofre de chaves usando modelos, consulte [criar um cofre de chaves](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
