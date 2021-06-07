---
title: Tutorial – Gerenciar discos do Azure com o Azure PowerShell
description: Neste tutorial, você aprenderá a usar o Azure PowerShell para criar e gerenciar discos do Azure para máquinas virtuais
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a61d7425a7a907230008ab1d4f15a836150e1518
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549049"
---
# <a name="tutorial---manage-azure-disks-with-azure-powershell"></a>Tutorial – Gerenciar discos do Azure com o Azure PowerShell

Máquinas virtuais do Azure usam discos para armazenar o sistema operacional de VMs, aplicativos e dados. Ao criar uma VM, é importante escolher um tamanho de disco e a configuração apropriada para a carga de trabalho esperada. Este tutorial aborda a implantação e gerenciamento de discos de VM. Você saberá mais sobre:

> [!div class="checklist"]
> * Discos de sistema operacional e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Anexar e preparar os discos de dados

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell em uma guia separada do navegador indo até [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

## <a name="default-azure-disks"></a>Discos padrão do Azure

Quando uma máquina virtual do Azure é criada, dois discos são automaticamente anexados à máquina virtual. 

**Disco do sistema operacional** - Os discos do sistema operacional podem ser dimensionados para até 4 terabytes e hospedar o sistema operacional das máquinas virtuais. Se você criar uma VM (máquina virtual) com base em uma imagem do [Azure Marketplace](https://azure.microsoft.com/marketplace/), normalmente 127 GB (mas algumas imagens têm tamanhos de disco de sistema operacional menores). O disco do SO é atribuído à letra da unidade *C:* por padrão. A configuração de cache do disco do SO é otimizada para desempenho do SO. O disco do SO **não deve** hospedar aplicativos nem dados. Para aplicativos e dados, use um disco de dados, que é detalhado posteriormente neste artigo.

**Disco temporário** - Discos temporários utilizam uma unidade de estado sólido localizada no mesmo host do Azure que a máquina virtual. Os discos temporários são altamente eficazes e podem ser usados para operações como o processamento de dados temporário. No entanto, se a VM for movida para um novo host, todos os dados armazenados em um disco temporário serão removidos. O tamanho do disco temporário é determinado pelo [tamanho da máquina virtual](../sizes.md). Os discos temporários são atribuídos à letra da unidade *D:* por padrão.

## <a name="azure-data-disks"></a>Discos de dados do Azure

Os discos de dados extras podem ser adicionados para instalação de aplicativos e armazenamento de dados. Os discos de dados devem ser usados em qualquer situação onde o armazenamento de dados durável responsivo é necessário. O tamanho da máquina virtual determina quantos discos de dados podem ser anexados a uma VM.

## <a name="vm-disk-types"></a>Tipos de disco da máquina virtual

O Azure fornece dois tipos de discos.

**Discos Standard** - apoiados por HDDs e oferecem armazenamento econômico e eficaz. Os discos Standard são ideais para uma carga de trabalho econômica de desenvolvimento e teste.

**Discos Premium** – apoiados por disco de baixa latência e alto desempenho baseado em SSD. Perfeitos para VMs que executam carga de trabalho de produção. Os tamanhos de VM com um **S** no [nome do tamanho](../vm-naming-conventions.md) normalmente são compatíveis com o Armazenamento Premium. Por exemplo, as VMs das séries DS, DSv2, GS e FS são compatíveis com o Armazenamento Premium. Ao escolher o tamanho de um disco, o valor é arredondado para o tipo seguinte. Por exemplo, se o tamanho do disco for maior que 64 GB, mas menor que 128 GB, o tipo de disco será P10. 
<br>
[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

Quando você provisiona um disco de armazenamento premium, ao contrário do armazenamento padrão, a capacidade, IOPS e taxa de transferência de disco são garantidos. Por exemplo, se você criar um disco P50, o Azure provisionará uma capacidade de armazenamento de 4.095 GB, 7.500 IOPS e uma taxa de transferência de 250 MB/s para o disco. O aplicativo pode usar a capacidade e o desempenho no todo ou em parte. Os discos SSD Premium são projetados para fornecer as latências baixas de milissegundos de dígito único e a IOPS de destino e a taxa de transferência descritas na tabela anterior 99,9% do tempo.

Embora a tabela acima identifique a IOPS máxima por disco, um nível mais alto de desempenho pode ser obtido com a distribuição de vários discos de dados. Por exemplo, 64 discos de dados podem ser anexados à VM Standard_GS5. Se cada um desses discos for dimensionado como um P30, será possível chegar a um máximo de 80.000 IOPS. Para obter informações detalhadas sobre o máximo de IOPS por VM, veja [Tipos e tamanhos de VM](../sizes.md).

## <a name="create-and-attach-disks"></a>Criar e anexar discos

Para concluir o exemplo neste tutorial, você deverá ter uma máquina virtual. Se necessário, crie uma máquina virtual com os seguintes comandos.

Defina o nome de usuário e a senha necessários para a conta de administrador na máquina virtual com [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):


Crie a máquina virtual com [New-AzVM](/powershell/module/az.compute/new-azvm). Você será solicitado a inserir um nome de usuário e senha para a conta de administradores para a VM.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" 
```


Crie a configuração inicial com [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig). O exemplo a seguir configura um disco com tamanho de 128 gigabytes.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

Crie o disco de dados com o comando [New-AzDisk](/powershell/module/az.compute/new-azdisk).

```azurepowershell-interactive
$dataDisk = New-AzDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

Obtenha a máquina virtual à qual deseja adicionar o disco de dados com o comando [Get-AzVM](/powershell/module/az.compute/get-azvm).

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

Adicione o disco de dados à configuração da máquina virtual com o comando [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk).

```azurepowershell-interactive
$vm = Add-AzVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

Atualize a máquina virtual com o comando [Update-AzVM](/powershell/module/az.compute/add-azvmdatadisk).

```azurepowershell-interactive
Update-AzVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>Preparar discos de dados

Depois que um disco é anexado à máquina virtual, o sistema operacional precisa ser configurado para usar o disco. O exemplo a seguir mostra como configurar manualmente o primeiro disco adicionado à VM. Esse processo também pode ser automatizado usando a [extensão de script personalizado](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Configuração manual

Crie uma conexão RDP com a máquina virtual. Abra o PowerShell e execute este script.

```azurepowershell
Get-Disk | Where partitionstyle -eq 'raw' |
    Initialize-Disk -PartitionStyle MBR -PassThru |
    New-Partition -AssignDriveLetter -UseMaximumSize |
    Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="verify-the-data-disk"></a>Verificar o disco de dados

Para verificar se o disco de dados está anexado, exiba `StorageProfile` para o `DataDisks` anexado.

```azurepowershell-interactive
$vm.StorageProfile.DataDisks
```

A saída deve ser semelhante ao exemplo a seguir:

```
Name            : myDataDisk
DiskSizeGB      : 128
Lun             : 1
Caching         : None
CreateOption    : Attach
SourceImage     :
VirtualHardDisk :
```


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos de discos da VM como:

> [!div class="checklist"]
> * Discos de sistema operacional e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Anexar e preparar os discos de dados

Vá para o próximo tutorial para saber como automatizar a configuração da máquina virtual.

> [!div class="nextstepaction"]
> [Automatizar a configuração da VM](./tutorial-automate-vm-deployment.md)
