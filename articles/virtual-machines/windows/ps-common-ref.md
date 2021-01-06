---
title: Comandos comuns do PowerShell para máquinas virtuais do Azure
description: Comandos comuns do PowerShell para ajudá-lo a começar a criar e gerenciar VMs no Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 3bf73e7c907c6d464fb6b6bfb3b507e6d12e0788
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97914868"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Comandos comuns do PowerShell para criar e gerenciar Máquinas Virtuais do Azure

Este artigo aborda alguns dos comandos do Azure PowerShell que você pode usar para criar e gerenciar máquinas virtuais em sua assinatura do Azure.  Para obter ajuda mais detalhada com opções de linha de comando específicas, você pode usar o *comando* **Get-Help** .

 

Essas variáveis poderão ser úteis para você se estiver executando mais de um dos comandos descritos neste artigo:

- $location – o local da máquina virtual. Você pode usar [Get-AzLocation](/powershell/module/az.resources/get-azlocation) para encontrar uma [região geográfica](https://azure.microsoft.com/regions/) que funciona para você.
- $myResourceGroup – o nome do grupo de recursos que contém a máquina virtual.
- $myVM – o nome da máquina virtual.

## <a name="create-a-vm---simplified"></a>Criar uma VM - simplificada

| Tarefa | Comando |
| ---- | ------- |
| Crie uma VM simples | [New-AzVM](/powershell/module/az.compute/new-azvm) -Name $myVM <BR></BR><BR></BR> New-AzVM tem um conjunto de parâmetros *simplificados*, em que apenas um nome é necessário. O valor de - nome será usado como o nome para todos os recursos necessários para criar uma nova VM. Você pode especificar mais, mas isso é tudo o que é necessário.|
| Criar uma VM por meio de uma imagem personalizada | New-AzVm -ResourceGroupName $myResourceGroup -Name $myVM ImageName "myImage" -Location $location  <BR></BR><BR></BR>Você precisa já tiver criado suas próprias [imagem gerenciada](capture-image-resource.md). Você pode usar uma imagem para fazer várias VMs idênticas. |



## <a name="create-a-vm-configuration"></a>Criar uma configuração de VM

| Tarefa | Comando |
| ---- | ------- |
| Criar uma configuração de VM |$vm = [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>A configuração da VM é usada para definir ou atualizar as configurações da VM. A configuração é inicializada com o nome da VM e seu [tamanho](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Adicionar definições de configuração |$vm = [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>As configurações do sistema operacional, incluindo as [credenciais](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-5.1&preserve-view=true), são adicionadas ao objeto de configuração que você criou usando New-AzVMConfig. |
| Adicionar uma interface de rede |$vm = [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) -VM $vm -Id $nic.Id<BR></BR><BR></BR>Uma VM deve ter um [adaptador de rede](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json) para se comunicar em uma rede virtual. Também é possível usar [Get-AzNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) para recuperar um objeto de adaptador de rede existente. |
| Especificar uma imagem de plataforma |$vm = [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) -VM $vm -PublisherName "publisher_name" -Offer "publisher_offer" -Skus "product_sku" -Version "latest"<BR></BR><BR></BR>As [informações da imagem](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) são adicionadas ao objeto de configuração que você criou usando New-AzVMConfig. O objeto retornado por este comando só é usado quando você configura o disco do sistema operacional para usar uma imagem de plataforma. |
| Criar uma máquina virtual |[New-AzVM](/powershell/module/az.compute/new-azvm) -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>Todos os recursos são criados em um [grupo de recursos](../../azure-resource-manager/management/manage-resource-groups-powershell.md). Antes de executar esse comando, execute New-AzVMConfig, Set-AzVMOperatingSystem, Set-AzVMSourceImage, Add-AzVMNetworkInterface e Set-AzVMOSDisk. |
| Atualizar uma VM |[Update-AzVM](/powershell/module/az.compute/update-azvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Obtenha a configuração atual da VM usando Get-AzVM, altere as configurações no objeto da VM e, em seguida, execute este comando. |

## <a name="get-information-about-vms"></a>Obter informações sobre VMs

| Tarefa | Comando |
| ---- | ------- |
| Listar VMs em uma assinatura |[Get-AzVM](/powershell/module/az.compute/get-azvm) |
| Listar VMs em um grupo de recursos |Get-AzVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Para obter uma lista dos grupos de recursos em sua assinatura, use [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup). |
| Obter informações sobre uma VM |Get-AzVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Gerenciar VMs
| Tarefa | Comando |
| --- | --- |
| Iniciar uma VM |[Start-AzVM](/powershell/module/az.compute/start-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Parar uma VM |[Stop-AzVM](/powershell/module/az.compute/stop-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Reiniciar uma VM em execução |[Restart-AzVM](/powershell/module/az.compute/restart-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Excluir uma VM |[Remove-AzVM](/powershell/module/az.compute/remove-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |


## <a name="next-steps"></a>Próximas etapas
* Veja as etapas básicas para criar uma máquina virtual em [Criar uma VM do Windows utilizando o Resource Manager e o PowerShell](./quick-create-powershell.md?toc=/azure/virtual-machines/windows/toc.json).
