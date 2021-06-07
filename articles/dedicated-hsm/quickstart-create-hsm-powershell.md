---
title: 'Início rápido: Criar um HSM Dedicado do Azure com o Azure PowerShell'
description: Criar um HSM Dedicado do Azure com o Azure PowerShell
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/13/2020
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurepowershell
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: d5b6d0399ceb98caf9bdd7bbd725e6d92af0eaa3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537798"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-with-azure-powershell"></a>Início rápido: Criar um HSM Dedicado do Azure com o Azure PowerShell

Este artigo descreve como você pode criar um HSM Dedicado do Azure usando o módulo [Az.DedicatedHsm](/powershell/module/az.dedicatedhsm) do PowerShell.

## <a name="requirements"></a>Requisitos

* Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Enquanto o módulo **Az.DedicatedHsm** do PowerShell estiver em versão prévia, você precisará instalá-lo separadamente usando o cmdlet `Install-Module`. Depois que esse módulo do PowerShell estiver em disponibilidade geral, ele fará parte das versões futuras do módulo Az PowerShell e estará disponível por padrão no Azure Cloud Shell.

  ```azurepowershell-interactive
  Install-Module -Name Az.DedicatedHsm
  ```

* Se tiver várias assinaturas do Azure, escolha a que for adequada para cobrança do recurso. Selecione uma assinatura específica usando o cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) usando o cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados como um grupo.

O exemplo a seguir cria um grupo de recursos com o nome especificado no local especificado.

```azurepowershell-interactive
New-AzResourceGroup -Name myRG -Location westus
```

## <a name="create-a-dedicated-hsm"></a>Criar um HSM dedicado

Para criar um HSM dedicado, use o cmdlet [New-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/new-azdedicatedhsm). O exemplo a seguir cria um HSM dedicado na assinatura especificada.

```azurepowershell-interactive
$Params = @{
  Name  = 'MyHSM'
  ResourceGroupName = 'myRG'
  Location = 'westus'
  Sku = 'SafeNet Luna Network HSM A790'
  StampId = 'stamp1'
  SubnetId = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myhsm-vnet/subnets/hsmsubnet'
  NetworkInterface = @{PrivateIPAddress = '10.2.1.120'}
}
New-AzDedicatedHsm @Params
```

```Output
Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="get-a-dedicated-hsm"></a>Obter um HSM dedicado

Para recuperar informações sobre um HSM dedicado existente, use o cmdlet [Get-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/get-azdedicatedhsm). O exemplo a seguir obtém o HSM dedicado especificado.

```azurepowershell-interactive
Get-AzDedicatedHsm -Name MyHSM -ResourceGroupName myRG
```

```Output
Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="update-a-dedicated-hsm"></a>Atualizar um HSM dedicado

Para atualizar um HSM dedicado, use o cmdlet [Update-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/update-azdedicatedhsm). O exemplo a seguir atualiza um HSM dedicado na assinatura especificada.

```azurepowershell-interactive
Update-AzDedicatedHsm -Name MyHSM -ResourceGroupName myRG -Tag @{'key1' = '1'; 'key2' = 2; 'key3' = 3}
```

```Output
PS C:\>Update-AzDedicatedHsm -Name  hsm-n7wfxi -ResourceGroupName dedicatedhsm-rg-n359cz -Tag @{'key1' = '1';
'key2' = 2; 'key3' = 3}

Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se os recursos criados neste artigo não forem necessários, você poderá excluí-los executando o exemplo a seguir.

### <a name="remove-a-dedicated-hsm"></a>Remover um HSM dedicado

Para remover um HSM dedicado, use o cmdlet [Remove-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/remove-azdedicatedhsm). O exemplo a seguir exclui o HSM dedicado especificado.

```azurepowershell-interactive
Remove-AzDedicatedHsm -Name hsm-7t2xaf -ResourceGroupName lucas-manual-test
```

### <a name="delete-the-resource-group"></a>Exclua o grupo de recursos

> [!CAUTION]
> O exemplo a seguir exclui o grupo de recursos especificado e todos os recursos contidos nele.
> Se existirem recursos fora do escopo deste artigo no grupo de recursos especificado, eles também serão excluídos.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myRG
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [HSM Dedicado do Azure](overview.md).
