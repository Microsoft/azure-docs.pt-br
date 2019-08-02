---
title: Tutorial – Gerenciar discos do Azure com o Azure PowerShell | Microsoft Docs
description: Neste tutorial, você aprenderá a usar o Azure PowerShell para criar e gerenciar discos do Azure para máquinas virtuais
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.subservice: disks
ms.openlocfilehash: b6098d6a8752737ef0bffaf35c8ba4b6e5223d22
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707979"
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

**Disco do sistema operacional** - Os discos do sistema operacional podem ser dimensionados para até 4 terabytes e hospedar o sistema operacional das máquinas virtuais.  O disco do SO é atribuído à letra da unidade *C:* por padrão. A configuração de cache do disco do SO é otimizada para desempenho do SO. O disco do SO **não deve** hospedar aplicativos nem dados. Para aplicativos e dados, use um disco de dados, que é detalhado posteriormente neste artigo.

**Disco temporário**: discos temporários usam uma unidade de estado sólido localizada no mesmo host do Azure que a VM. Os discos temporários são altamente eficazes e podem ser usados para operações como o processamento de dados temporário. No entanto, se a VM for movida para um novo host, todos os dados armazenados em um disco temporário serão removidos. O tamanho do disco temporário é determinado pelo [tamanho da máquina virtual](sizes.md). Os discos temporários são atribuídos à letra da unidade *D:* por padrão.

## <a name="azure-data-disks"></a>Discos de dados do Azure

Os discos de dados extras podem ser adicionados para instalação de aplicativos e armazenamento de dados. Os discos de dados devem ser usados em qualquer situação onde o armazenamento de dados durável responsivo é necessário. O tamanho da máquina virtual determina quantos discos de dados podem ser anexados a uma VM. Para cada vCPU da VM, podem ser anexados quatro discos de dados.

## <a name="vm-disk-types"></a>Tipos de disco da máquina virtual

O Azure fornece dois tipos de discos.

**Discos Standard** - apoiados por HDDs e oferecem armazenamento econômico e eficaz. Os discos Standard são ideais para uma carga de trabalho econômica de desenvolvimento e teste.

**Discos Premium** - apoiados por disco de baixa latência e alto desempenho baseado em SSD. Perfeitos para VMs que executam carga de trabalho de produção. O Armazenamento Premium dá suporte às VMs das séries DS, DSv2, GS e FS. Os discos Premium são apresentados em cinco tipos (P10, P20, P30, P40, P50), o tamanho do disco determina o tipo de disco. Na escolha do tamanho de um disco, o valor é arredondado para o próximo tipo. Por exemplo, se o tamanho estiver abaixo de 128 GB o tipo de disco é P10 ou entre 129 GB e 512 GB de disco é P20.

### <a name="premium-disk-performance"></a>Desempenho do disco Premium
[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

Embora a tabela acima identifique a IOPS máxima por disco, um nível mais alto de desempenho pode ser obtido com a distribuição de vários discos de dados. Por exemplo, 64 discos de dados podem ser anexados à VM Standard_GS5. Se cada um desses discos for dimensionado como um P30, será possível chegar a um máximo de 80.000 IOPS. Para obter informações detalhadas sobre o máximo de IOPS por VM, veja [Tipos e tamanhos de VM](./sizes.md).

## <a name="create-and-attach-disks"></a>Criar e anexar discos

Para concluir o exemplo neste tutorial, você deverá ter uma máquina virtual. Se necessário, crie uma máquina virtual com os seguintes comandos.

Defina o nome de usuário e a senha necessários para a conta de administrador na máquina virtual com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):


Crie a máquina virtual com [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). Você será solicitado a inserir um nome de usuário e senha para a conta de administradores para a VM.

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


Crie a configuração inicial com [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig). O exemplo a seguir configura um disco com tamanho de 128 gigabytes.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

Crie o disco de dados com o comando [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk).

```azurepowershell-interactive
$dataDisk = New-AzDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

Obtenha a máquina virtual à qual deseja adicionar o disco de dados com o comando [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm).

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

Adicione o disco de dados à configuração da máquina virtual com o comando [Add-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/add-azvmdatadisk).

```azurepowershell-interactive
$vm = Add-AzVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

Atualize a máquina virtual com o comando [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/add-azvmdatadisk).

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
