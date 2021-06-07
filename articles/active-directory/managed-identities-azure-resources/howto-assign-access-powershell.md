---
title: Atribuir um acesso de identidade gerenciada a um recurso usando o PowerShell-Azure AD
description: Instruções passo a passo para atribuir uma identidade gerenciada em um recurso, acesso a outro recurso, usando o PowerShell.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf1a7a608df7a2b752d9a6bed52a4024fd776c5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97592493"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Atribuir um acesso de identidade gerenciada a um recurso usando o PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Após configurar um recurso do Azure com uma identidade gerenciada, será possível conceder o acesso de identidade gerenciada a outro recurso, assim como qualquer entidade de segurança. Este exemplo mostra como conceder acesso de uma identidade gerenciada da máquina virtual do Azure para uma conta de armazenamento do Azure usando o PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md). **Revise a [diferença entre uma identidade gerenciada atribuída ao sistema e atribuída ao usuário](overview.md#managed-identity-types)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os scripts de exemplo, você tem duas opções:
    - Use o [Azure Cloud Shell](../../cloud-shell/overview.md), que você pode abrir usando o botão **Experimentar** no canto superior direito dos blocos de código.
    - Execute os scripts localmente instalando a versão mais recente do [Azure PowerShell](/powershell/azure/install-az-ps) e, em seguida, entre no Azure usando `Connect-AzAccount`. 

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Usar o RBAC do Azure para atribuir um acesso de identidade gerenciada a outro recurso

1. Habilite a identidade gerenciada em um recurso do Azure, [como uma VM do Azure](qs-configure-powershell-windows-vm.md).

1. Neste exemplo, fornecemos um acesso da VM do Azure para uma conta de armazenamento. Primeiro, usamos [Get-AzVM](/powershell/module/az.compute/get-azvm) para obter a entidade de serviço da VM chamada `myVM`, que foi criada quando habilitamos a identidade gerenciada. Em seguida, usamos [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) para fornecer à VM o acesso de **Leitor** em uma conta de armazenamento chamada `myStorageAcct`:

    ```azurepowershell-interactive
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Próximas etapas

- [Identidade gerenciada para visão geral dos recursos do Azure](overview.md)
- Para habilitar a identidade gerenciada em uma máquina virtual do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando o PowerShell](qs-configure-powershell-windows-vm.md).
