---
title: Configurar identidades gerenciadas em conjuntos de dimensionamento de máquinas virtuais usando o PowerShell – Azure AD
description: Instruções passo a passo para configurar identidades gerenciadas atribuídas ao sistema e ao usuário em um conjunto de dimensionamento de máquinas virtuais usando o PowerShell.
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
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eb651df2fe3b9771154f9e188d41c660f7ffd028
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774128"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Configurar identidades gerenciadas para recursos do Azure em conjuntos de escala de máquina virtual usando o PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, usando o PowerShell, você aprende como executar as identidades gerenciadas para operações de recursos do Azure em um conjunto de dimensionamento de máquina virtual:

- Ativar e desativar a identidade gerenciada designada pelo sistema em um conjunto de dimensionamento de máquina virtual
- Adicionar e remover uma identidade gerenciada atribuída pelo usuário em um conjunto de dimensionamento de máquina virtual

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md). **Lembre-se de revisar a [diferença entre uma identidade atribuída designada pelo sistema e gerenciada pelo usuário](overview.md#managed-identity-types)**.

- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

- Para realizar as operações de gerenciamento descritas neste artigo, sua conta precisará das seguintes atribuições de controle de acesso baseado em função do Azure:

    > [!NOTE]
    > Não são necessárias atribuições de função do diretório adicionais do Azure AD.

    - [Colaborador da Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para criar um conjunto de dimensionamento de máquina virtual e habilitar e remover a identidade gerenciada pelo usuário e / ou atribuída pelo usuário de um conjunto de dimensionamento de máquina virtual.
    - [Função de Contratada de Identidade Gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) para criar uma identidade gerenciada atribuída pelo usuário.
    - [Papel de Operador de Identidade Gerenciado](../../role-based-access-control/built-in-roles.md#managed-identity-operator) para atribuir e remover uma identidade gerenciada atribuída pelo usuário de e para um conjunto de dimensionamento de máquina virtual.

- Para executar os scripts de exemplo, você tem duas opções:
    - Use o [Azure Cloud Shell](../../cloud-shell/overview.md), que você pode abrir usando o botão **Experimentar** no canto superior direito dos blocos de código.
    - Execute os scripts localmente instalando a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps) e, em seguida, entre no Azure usando `Connect-AzAccount`. 

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você aprenderá como habilitar e remover uma identidade gerenciada atribuída pelo sistema usando o Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Ativar a identidade gerenciada atribuída pelo sistema durante a criação de um conjunto de dimensionamento da máquina virtual do Azure

Para criar um conjunto de dimensionamento de máquinas virtuais com a identidade gerenciada atribuída ao sistema habilitada:

1. Veja o *Exemplo 1* no artigo de referência do cmdlet [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) para criar um conjunto de dimensionamento de máquinas virtuais com uma identidade gerenciada atribuída ao sistema.  Adicione o parâmetro `-IdentityType SystemAssigned` ao cmdlet `New-AzVmssConfig`:

    ```azurepowershell-interactive
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Ativar identidade gerenciada atribuída pelo sistema em um conjunto de dimensionamento de máquina virtual do Azure existente

Se você precisar ativar uma identidade gerenciada atribuída pelo sistema em um conjunto de dimensionamento de máquina virtual do Azure existente:

1. Verifique se a conta do Azure que você está usando pertence a uma função que fornece permissões de gravação no conjunto de dimensionamento de máquinas virtuais, como "Colaborador da Máquina Virtual".
   
1. Recupere as propriedades do conjunto de dimensionamento de máquinas virtuais usando o cmdlet [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss). Em seguida, para habilitar uma identidade gerenciada atribuída pelo sistema, use a opção `-IdentityType` no cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss):

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Desativar a identidade gerenciada atribuída pelo sistema de um conjunto de dimensionamento da máquina virtual do Azure

Se você tiver um conjunto de dimensionamento de máquina virtual que não precise mais da identidade gerenciada atribuída pelo sistema, mas ainda precisar de identidades gerenciadas atribuídas pelo usuário, use o seguinte cmdlet:

1. Verifique se sua conta pertence a uma função que fornece permissões de gravação no conjunto de dimensionamento de máquinas virtuais, como "Colaborador da Máquina Virtual".

1. Execute o cmdlet a seguir:

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

1. Se você tiver um conjunto de dimensionamento de máquinas virtuais que não precise mais de identidade gerenciada atribuída pelo sistema e não tenha identidades gerenciadas atribuídas pelo usuário, use o seguinte comando:

    ```azurepowershell-interactive
    Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
    ```
    
## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

Nesta seção, você aprenderá como adicionar e remover uma identidade gerenciada atribuída pelo usuário de um conjunto de dimensionamento de máquina virtual usando o Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Atribuir uma identidade gerenciada atribuída pelo usuário durante a criação de um conjunto de dimensionamento da máquina virtual do Azure

Criar uma nova escala de máquina virtual definida com uma identidade gerenciada atribuída pelo usuário não é atualmente suportada pelo PowerShell. Consulte a próxima seção sobre como adicionar uma identidade gerenciada atribuída pelo usuário a um conjunto de dimensionamento de máquina virtual existente. Procure novamente por atualizações.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Atribuir uma identidade gerenciada usuário atribuído a um conjunto de dimensionamento de máquina virtual do Azure existente

Para atribuir uma identidade gerenciada usuário atribuído a um conjunto de dimensionamento de máquina virtual do Azure existente:

1. Verifique se sua conta pertence a uma função que fornece permissões de gravação no conjunto de dimensionamento de máquinas virtuais, como "Colaborador da Máquina Virtual".

1. Recupere as propriedades do conjunto de dimensionamento de máquinas virtuais usando o cmdlet `Get-AzVM`. Em seguida, para atribuir uma identidade gerenciada atribuída pelo usuário ao conjunto de dimensionamento de máquinas virtuais, use as opções `-IdentityType` e `-IdentityID` no cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss). Substitua `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>` e `USER ASSIGNED ID2` pelos seus próprios valores.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Remover uma identidade gerenciada atribuída pelo usuário de um conjunto de dimensionamento da máquina virtual do Azure

Se o seu conjunto de dimensionamento de máquina virtual tiver várias identidades gerenciadas atribuídas pelo usuário, você poderá remover todas, exceto a última, usando os seguintes comandos. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<VIRTUAL MACHINE SCALE SET NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY NAME>` é a propriedade de nome da identidade gerenciada atribuída pelo usuário, que deve permanecer no conjunto de dimensionamento da máquina virtual. Essas informações podem ser encontradas na seção de identidade do conjunto de dimensionamento de máquinas virtuais usando `az vmss show`:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Se o seu conjunto de dimensionamento de máquina virtual não tiver uma identidade gerenciada atribuída pelo sistema e você quiser remover todas as identidades gerenciadas atribuídas pelo usuário, use o seguinte comando:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Se o seu conjunto de dimensionamento de máquina virtual tiver identidades gerenciadas atribuídas pelo sistema e atribuídas pelo usuário, você poderá remover todas as identidades gerenciadas atribuídas pelo usuário alternando para usar somente a identidade gerenciada atribuída pelo sistema.

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Próximas etapas

- [Identidades gerenciadas para visão geral de recursos do Azure](overview.md)
- Para os guias de início rápido completos sobre VM do Azure, consulte:
  
  - [Aprenda rapidamente a criar máquinas virtuais Windows com o PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Aprenda rapidamente a criar máquinas virtuais Linux com o PowerShell](../../virtual-machines/linux/quick-create-powershell.md)
