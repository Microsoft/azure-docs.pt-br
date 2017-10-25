---
title: Criar imagens personalizadas da VM com o Azure PowerShell | Microsoft Docs
description: Tutorial - Criar uma imagem personalizada da VM usando o Azure PowerShell.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/08/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c7cb5353585373af8de0d30b06bcfa49a3278c17
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-custom-image-of-an-azure-vm-using-powershell"></a>Criar uma imagem personalizada de uma VM do Azure usando o PowerShell

Imagens personalizadas são como imagens do marketplace, mas você mesmo as cria. As imagens personalizadas podem ser usadas para configurações de inicialização como o pré-carregamento de aplicativos, configurações de aplicativos e outras configurações do sistema operacional. Neste tutorial, você criará sua própria imagem personalizada de uma máquina virtual do Azure. Você aprenderá como:

> [!div class="checklist"]
> * Aplicar Sysprep e generalizar VMs
> * Criar uma imagem personalizada
> * Criar uma VM por meio de uma imagem personalizada
> * Listar todas as imagens na sua assinatura
> * Excluir uma imagem

Este tutorial requer o módulo do Azure PowerShell, versão 3.6 ou posterior. Execute ` Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="before-you-begin"></a>Antes de começar

As etapas abaixo detalham como pegar uma máquina virtual existente e transformá-la em uma imagem personalizada reutilizável que você pode usar para criar novas instâncias de VM.

Para concluir o exemplo neste tutorial, você deverá ter uma máquina virtual. Se necessário, este [exemplo de script](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) pode criar uma para você. Ao trabalhar com este tutorial, substitua o grupo de recursos e os nomes de VM onde for necessário.

## <a name="prepare-vm"></a>Preparar VM

Para criar uma imagem de uma máquina virtual, você precisará preparar a VM generalizando a VM, desalocando e, em seguida, marcando a VM de origem como generalizada no Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizar a VM Windows usando Sysprep

O Sysprep remove todas as informações pessoais da conta, entre outros itens, e prepara o computador para ser utilizado como uma imagem. Para obter detalhes sobre o Sysprep, consulte [Como usar o Sysprep: uma introdução](http://technet.microsoft.com/library/bb457073.aspx).


1. Conectar-se à máquina virtual.
2. Abra uma janela de prompt de comando como administrador. Altere o diretório para *%windir%\system32\sysprep* e, a seguir, execute *sysprep.exe*.
3. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, selecione *Entrar na Configuração Inicial pelo Usuário do Sistema (OOBE)* e verifique se a caixa de seleção *Generalizar* está marcada.
4. Em **Opções de Desligamento**, selecione *Desligar* e em **OK**.
5. Quando o Sysprep for concluído, desligará a máquina virtual. **Não reinicie a VM**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Desalocar e marcar a VM como generalizada

Para criar uma imagem, a VM precisa ser desalocada e marcada como generalizada no Azure.

Desaloque a VM usando [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm).

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

Defina o status da máquina virtual como `-Generalized` usando [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm). 
   
```powershell
Set-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Generalized
```


## <a name="create-the-image"></a>Criar a imagem

Agora, você pode criar uma imagem da VM usando [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) e [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). O exemplo a seguir cria uma imagem chamada *myImage* por meio de uma VM chamada *myVM*.

Obtenha a máquina virtual. 

```powershell
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
```

Crie a configuração de imagem.

```powershell
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

Crie a imagem.

```powershell
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Criar VMs por meio da imagem

Agora que tem uma imagem, você pode criar uma ou mais VMs novas por meio da imagem. A criação de uma VM por meio de uma imagem personalizada é muito semelhante à criação de uma VM usando uma imagem do Marketplace. Quando usa uma imagem do Marketplace, você precisa obter informações sobre a imagem, o provedor da imagem, a oferta, a SKU e a versão. Com uma imagem personalizada, você apenas precisa fornecer a ID do recurso da imagem personalizada. 

No script a seguir, criamos uma variável *$image* para armazenar informações sobre a imagem personalizada usando [Get-AzureRmImage](/powershell/module/azurerm.compute/get-azurermimage) e, em seguida, usamos [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) e especificamos a ID usando a variável *$image* que acabamos de criar. 

O script cria uma VM chamada *myVMfromImage* por meio de nossa imagem personalizada em um novo grupo de recursos chamado *myResourceGroupFromImage* no local *Oeste dos EUA*.


```powershell
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

New-AzureRmResourceGroup -Name myResourceGroupFromImage -Location EastUS

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

$pip = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name "mypublicdns$(Get-Random)" `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4

  $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

  $nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleRDP

$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id `
    -NetworkSecurityGroupId $nsg.Id

$vmConfig = New-AzureRmVMConfig `
    -VMName myVMfromImage `
    -VMSize Standard_D1 | Set-AzureRmVMOperatingSystem -Windows `
        -ComputerName myComputer `
        -Credential $cred 

# Here is where we create a variable to store information about the image 
$image = Get-AzureRmImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroup

# Here is where we specify that we want to create the VM from and image and provide the image ID
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -Id $image.Id

$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

New-AzureRmVM `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -VM $vmConfig
```

## <a name="image-management"></a>Gerenciamento de imagens 

Estes são alguns exemplos de tarefas comuns de gerenciamento de imagem e como para concluí-las usando o PowerShell.

Liste todas as imagens por nome.

```powershell
$images = Find-AzureRMResource -ResourceType Microsoft.Compute/images 
$images.name
```

Exclua uma imagem. Este exemplo exclui a imagem denominada *myOldImage* do *myResourceGroup*.

```powershell
Remove-AzureRmImage `
    -ImageName myOldImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma imagem de VM personalizada. Você aprendeu como:

> [!div class="checklist"]
> * Aplicar Sysprep e generalizar VMs
> * Criar uma imagem personalizada
> * Criar uma VM por meio de uma imagem personalizada
> * Listar todas as imagens na sua assinatura
> * Excluir uma imagem

Avance para o próximo tutorial para saber mais sobre a alta disponibilidade das máquinas virtuais.

> [!div class="nextstepaction"]
> [Criar VMs altamente disponíveis](tutorial-availability-sets.md)



