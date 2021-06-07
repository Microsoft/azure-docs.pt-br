---
title: Configurar identidades gerenciadas em uma VM do Azure usando PowerShell – Azure AD
description: Instruções passo a passo para configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando PowerShell.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/19/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1eca17106067d964ba2f280d358b2973b41459a6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783261"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código.

Neste artigo, usando o PowerShell, você aprenderá como executar as seguintes identidades gerenciadas para operações de recursos do Azure em uma VM do Azure.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md). **Revise a [diferença entre uma identidade gerenciada atribuída ao sistema e atribuída ao usuário](overview.md#managed-identity-types)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os scripts de exemplo, você tem duas opções:
    - Use o [Azure Cloud Shell](../../cloud-shell/overview.md), que você pode abrir usando o botão **Experimentar** no canto superior direito dos blocos de código.
    - Execute os scripts localmente instalando a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps) e, em seguida, entre no Azure usando `Connect-AzAccount`. 

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você aprenderá como habilitar e desabilitar a identidade gerenciada atribuída ao sistema usando o Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Ativar identidade gerenciada atribuída pelo sistema durante a criação de uma VM do Azure

Para criar uma VM do Azure com a identidade gerenciada atribuída ao sistema habilitada, a conta precisará da atribuição de função [Colaborador da Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. Consulte um dos guias de início rápido de VM do Azure a seguir, concluindo apenas as seções necessárias ("Entrar no Azure", "Criar o grupo de recursos", "Criar grupo de rede", "Criar a máquina virtual").

    Quando você chegar à seção "Criar a VM", faça uma pequena modificação na sintaxe do cmdlet [New-AzVMConfig](/powershell/module/az.compute/new-azvm). Certifique-se de adicionar um parâmetro `-IdentityType SystemAssigned` para provisionar a VM com a identidade atribuída ao sistema habilitada, por exemplo:

    ```azurepowershell-interactive
    $vmConfig = New-AzVMConfig -VMName myVM -IdentityType SystemAssigned ...
    ```

   - [Criar uma máquina virtual do Windows usando o PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Crie uma Máquina Virtual do Linux usando o PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Habilitar identidade gerenciada atribuída ao sistema em uma VM do Azure existente

Para habilitar a identidade gerenciada atribuída ao sistema em uma VM que foi originalmente provisionada sem ela, a conta precisará da atribuição de função [Colaborador da Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. Recupere as propriedades da VM usando o cmdlet `Get-AzVM`. Em seguida, para habilitar uma identidade gerenciada atribuída ao sistema, use a opção `-IdentityType` no cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm):

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType SystemAssigned
   ```

### <a name="add-vm-system-assigned-identity-to-a-group"></a>Adicionar uma identidade atribuída pelo sistema de uma VM a um grupo

Depois de habilitar a identidade atribuída pelo sistema em uma VM, você pode adicioná-la a um grupo.  O procedimento a seguir adiciona uma identidade atribuída pelo sistema de uma VM a um grupo.

1. Recupere e tome nota do `ObjectID` (conforme especificado no campo `Id` dos valores retornados) da entidade de serviço da VM:

   ```azurepowershell-interactive
   Get-AzADServicePrincipal -displayname "myVM"
   ```

1. Recupere e tome nota do `ObjectID` (conforme especificado no campo `Id` dos valores retornados) do grupo:

   ```azurepowershell-interactive
   Get-AzADGroup -searchstring "myGroup"
   ```

1. Adicionar a entidade de serviço da VM ao grupo:

   ```azurepowershell-interactive
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Desativar identidade gerenciada atribuída pelo sistema de uma VM do Azure

Para desabilitar a identidade gerenciada atribuída ao sistema em uma VM, a conta precisará da atribuição de função [Colaborador da Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

Se você tiver uma máquina virtual que não precise mais da identidade gerenciada atribuída ao sistema, mas ainda precisar de identidades gerenciadas atribuídas ao usuário, use o seguinte cmdlet:

1. Recupere as propriedades da VM usando o cmdlet `Get-AzVM` e defina o parâmetro `-IdentityType` como `UserAssigned`:

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Se você tiver uma máquina virtual que não precise mais da identidade gerenciada atribuída ao sistema e não tiver identidades gerenciadas atribuídas ao usuário, use os comandos a seguir:

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

Nesta seção, você aprenderá como adicionar e remover uma identidade atribuída ao usuário de uma VM usando Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Atribuir uma identidade gerenciada atribuída ao usuário a uma VM durante a criação

Para atribuir uma identidade atribuída pelo usuário a uma VM, sua conta precisa das atribuições de função [Contribuidor de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e [Operador de Identidade Gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. Consulte um dos guias de início rápido de VM do Azure a seguir, concluindo apenas as seções necessárias ("Entrar no Azure", "Criar o grupo de recursos", "Criar grupo de rede", "Criar a máquina virtual").

    Quando você chegar à seção "Criar a VM", faça uma pequena modificação na  [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm)sintaxe do cmdlet. Adicione os parâmetros `-IdentityType UserAssigned` e `-IdentityID` para provisionar a VM com uma identidade atribuída ao usuário.  Substitua `<VM NAME>`,`<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores.  Por exemplo:

    ```azurepowershell-interactive
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```

    - [Criar uma máquina virtual do Windows usando o PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Crie uma Máquina Virtual do Linux usando o PowerShell](../../virtual-machines/linux/quick-create-powershell.md)


### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Atribuir uma identidade gerenciada usuário atribuído a uma VM existente do Azure

Para atribuir uma identidade atribuída pelo usuário a uma VM, sua conta precisa das atribuições de função [Contribuidor de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e [Operador de Identidade Gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Nenhuma atribuição adicional de função de diretório do Azure Active Directory é necessária.

1. Crie uma identidade gerenciada atribuída ao usuário, usando o cmdlet [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity).  Observe o `Id` na saída porque isso será necessário na próxima etapa.

   > [!IMPORTANT]
   > A criação de identidades gerenciadas atribuídas ao usuário dá suporte apenas a caracteres alfanuméricos, sublinhado e hífen (0-9 ou a-z ou A-Z, \_ ou -). Além disso, o nome deve ter um limite de 3 a 128 caracteres para que a atribuição a VM/VMSS funcione corretamente. Para mais informações, consulte [Perguntas frequentes e problemas conhecidos](known-issues.md)

   ```azurepowershell-interactive
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
1. Recupere as propriedades da VM usando o cmdlet `Get-AzVM`. Em seguida, para atribuir uma identidade gerenciada atribuída ao usuário à VM do Azure, use a opção `-IdentityType` e `-IdentityID` no cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm).  O valor para o`-IdentityId` parâmetro é o `Id` você anotou na etapa anterior.  Substitua, , ,  e  pelos seus próprios valores.

   > [!WARNING]
   > Para manter qualquer identidades gerenciadas anteriormente atribuído ao usuário atribuídas à VM, consulte o `Identity` propriedade do objeto da VM (por exemplo, `$vm.Identity`).  Se qualquer usuário atribuído identidades gerenciadas são retornadas, incluí-los no comando a seguir, juntamente com o novo usuário atribuído a identidade gerenciada que você deseja atribuir à VM.

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Remover uma identidade gerenciada atribuída pelo usuário de uma VM do Azure

Para remover uma identidade atribuída ao usuário a uma VM, a conta precisará da atribuição de função [Colaborador da Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

Se a VM tiver várias identidades gerenciadas atribuídas ao usuário, será possível remover todas, exceto a última, usando os seguintes comandos. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY NAME>` é a propriedade do nome da identidade gerenciada atribuída ao usuário, que deve permanecer na VM. Essas informações podem ser encontradas ao consultar o `Identity` propriedade do objeto da VM.  Por exemplo `$vm.Identity`:

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Se a VM não tiver uma identidade gerenciada atribuída ao sistema e você quiser remover todas as identidades gerenciadas atribuídas ao usuário, use o comando a seguir:

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Se a VM tiver ambas as identidades gerenciadas atribuídas ao sistema e atribuídas ao usuário, será possível remover todas as identidades gerenciadas atribuídas ao usuário, alternando para usar apenas as identidades gerenciadas atribuídas ao sistema.

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Próximas etapas

- [Identidades gerenciadas para visão geral de recursos do Azure](overview.md)
- Para os guias de início rápido completos sobre VM do Azure, consulte:

  - [Aprenda rapidamente a criar máquinas virtuais Windows com o PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
  - [Aprenda rapidamente a criar máquinas virtuais Linux com o PowerShell](../../virtual-machines/linux/quick-create-powershell.md)
