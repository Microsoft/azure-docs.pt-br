---
title: Como configurar o MSI em uma VM do Azure usando o PowerShell
description: "Instruções passo a passo para configurar um MSI (Identidade de Serviço Gerenciada) em uma VM do Azure usando o PowerShell."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.openlocfilehash: 612311f1c4e081e87dde76ce4a1d8efd46428c06
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Configurar o MSI (Identidade de Serviço Gerenciada) de um VM usando o PowerShell

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada automaticamente no Active Directory do Azure. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprenderá a habilitar e a remover o MSI de uma VM do Azure usando o PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

Instale também o [Azure PowerShell versão 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1), se ainda não tiver feito isso.

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Habilitar o MSI durante a criação de uma VM do Azure

Para criar uma VM habilitado MSI:

1. Consulte um dos guias de início rápido de VM do Azure a seguir, concluindo apenas as seções necessárias ("Fazer logon no Azure", "Criar o grupo de recursos", "Criar grupo de rede", "Criar a máquina virtual"). 

   > [!IMPORTANT] 
   > Quando você chegar à seção "Criar a VM", faça uma pequena modificação na sintaxe cmdlet [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm). Certifique-se de adicionar um `-IdentityType "SystemAssigned"` parâmetro para provisionar a VM com um MSI, por exemplo:
   >  
   > `$vmConfig = New-AzureRmVMConfig -VMName myVM -IdentityType "SystemAssigned" ...`

   - [Crie uma Máquina Virtual do Windows usando o PowerShell](../virtual-machines/windows/quick-create-powershell.md)
   - [Crie uma Máquina Virtual do Linux usando o PowerShell](../virtual-machines/linux/quick-create-powershell.md)



2. Adicione a extensão de VM do MSI usando o parâmetro `-Type` no cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Você pode passar "ManagedIdentityExtensionForWindows" ou "ManagedIdentityExtensionForLinux", dependendo do tipo de VM e nomeá-lo usando o `-Name` parâmetro. O parâmetro `-Settings` especifica a porta usada pelo ponto de extremidade do token OAuth para aquisição de token:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Habilitar MSI em uma VM do Azure existente

Se você precisar habilitar o MSI em uma máquina virtual existente:

1. Entre no Azure usando `Login-AzureRmAccount`. Use uma conta que esteja associada com uma assinatura do Azure que contenha uma VM. Verifique também se sua conta pertence a uma função que fornece permissões de gravação na VM, como "Colaborador da Máquina Virtual":

   ```powershell
   Login-AzureRmAccount
   ```

2. Primeiro, recupere as propriedades da VM usando o cmdlet `Get-AzureRmVM`. Para habilitar o MSI, use a opção `-IdentityType` no cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm):

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. Adicione a extensão de VM do MSI usando o parâmetro `-Type` no cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Você pode passar "ManagedIdentityExtensionForWindows" ou "ManagedIdentityExtensionForLinux", dependendo do tipo de VM e nomeá-lo usando o `-Name` parâmetro. O parâmetro `-Settings` especifica a porta usada pelo ponto de extremidade do token OAuth para aquisição de token. Especifique o parâmetro `-Location` correta, correspondendo ao local da VM existente:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Remover o MSI de uma VM do Azure

Se você tiver uma máquina virtual que não precisa mais de um MSI, use o cmdlet `RemoveAzureRmVMExtension` para remover o MSI da VM:

1. Entre no Azure usando `Login-AzureRmAccount`. Use uma conta que esteja associada com uma assinatura do Azure que contenha uma VM. Verifique também se sua conta pertence a uma função que fornece permissões de gravação na VM, como "Colaborador da Máquina Virtual":

   ```powershell
   Login-AzureRmAccount
   ```

2. Use o `-Name` alternar com o cmdlet [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension), especificando o mesmo nome usado quando você adicionou a extensão:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>Conteúdo relacionado

- [Visão geral da Identidade do Serviço Gerenciado](msi-overview.md)
- Para os guias de início rápido completos sobre VM do Azure, consulte:
  
  - [Crie uma máquina virtual Windows com o PowerShell](../virtual-machines/windows/quick-create-powershell.md) 
  - [Crie uma máquina virtual Linux com o PowerShell](../virtual-machines/linux/quick-create-powershell.md) 

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.
















