---
title: Criar uma VM do Windows a partir de um VHD especializado no Azure
description: Crie uma nova VM do Windows anexando um disco gerenciado especializado como o disco do sistema operacional usando o modelo de implantação do Gerenciador de Recursos.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: 642a28d5eea6a89de31247ed715c0b352b9ff7d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552211"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Criar uma VM do Windows a partir de um disco especializado usando o PowerShell

Crie uma nova VM anexando um disco gerenciado especializado como o disco do sistema operacional. Um disco especializado é uma cópia de um disco rígido virtual (VHD) de uma VM existente que contém as contas de usuário, aplicativos e outros dados de estado de sua VM original. 

Você tem várias opções:
* [Use um disco gerenciado existente](#option-1-use-an-existing-disk). Essa opção é útil se você tiver uma VM que não esteja funcionando corretamente. Você pode excluir a VM e reutilizar o disco gerenciado para criar uma nova VM. 
* [Carregar um VHD](#option-2-upload-a-specialized-vhd) 
* [Copie uma VM do Azure existente usando snapshots](#option-3-copy-an-existing-azure-vm)

Você também pode usar o portal do Azure para [criar uma nova VM a partir de um VHD especializado](create-vm-specialized-portal.md).

Este artigo mostra como usar discos gerenciados. Se você tiver uma implantação legada que exija o uso de uma conta de armazenamento, consulte [Criar uma VM a partir de um VHD especializado em uma conta de armazenamento](/previous-versions/azure/virtual-machines/windows/sa-create-vm-specialized).

> [!IMPORTANT]
> 
> Quando você usa um disco especializado para criar uma nova VM, a nova VM retém o nome do computador da VM original. Outras informações específicas do computador (por exemplo, CMID) também são mantidas e, em alguns casos, essas informações duplicadas podem causar problemas. Ao copiar uma VM, esteja ciente de quais tipos de informações específicas do computador seus aplicativos dependem.  
> Portanto, não use um disco especializado se desejar criar várias VMs. Em vez disso, para implantações maiores, [criar uma imagem](capture-image-resource.md) e, em seguida [usar essa imagem para criar várias VMs](create-vm-generalized-managed.md).

É recomendável que você limite o número de implantações simultâneas para 20 VMs a partir de um único VHD ou instantâneo. 

## <a name="option-1-use-an-existing-disk"></a>Opção 1: usar um disco existente

Se você tinha uma VM que foi excluída e quer reutilizar o disco do sistema operacional para criar uma nova, use [Get-AzDisk](/powershell/module/az.compute/get-azdisk).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Agora você pode anexar esse disco como o disco do sistema operacional para uma [nova VM](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Opção 2: carregar um VHD especializado

Você pode carregar o VHD de uma VM especializada criado com uma ferramenta de virtualização local, como o Hyper-V, ou em uma VM exportada de outra nuvem.

### <a name="prepare-the-vm"></a>Preparar a VM
Use o VHD como-é criar uma nova VM. 
  
  * [Preparar um VHD do Windows para carregar no Azure](prepare-for-upload-vhd-image.md). **Não** generalize a VM usando o Sysprep.
  * Remova quaisquer ferramentas e agentes de virtualização de convidados instalados na VM (como ferramentas VMware).
  * Verifique se a VM está configurada para obter o endereço IP e as configurações de DNS do DHCP. Isso garante que o servidor obtenha um endereço IP dentro da rede virtual quando for inicializado. 


### <a name="upload-the-vhd"></a>Carregar o VHD

Agora você pode carregar um VHD diretamente em um disco gerenciado. Para obter instruções, confira [Carregar um VHD no Azure usando o Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).

## <a name="option-3-copy-an-existing-azure-vm"></a>Opção 3: Copiar uma VM existente do Azure

Você pode criar uma cópia de uma VM que usa discos gerenciados, tirando um instantâneo da VM e usando esse instantâneo para criar um novo disco gerenciado e uma nova VM.

Se você quiser copiar uma VM existente para outra região, talvez queira usar azcopy para [criar uma cópia de um disco em outra região](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk). 

### <a name="take-a-snapshot-of-the-os-disk"></a>Tirar um instantâneo do disco do sistema operacional

Você pode tirar um instantâneo de uma VM inteira (incluindo todos os discos) ou de apenas um único disco. As etapas a seguir mostram como tirar um instantâneo apenas do disco do sistema operacional da VM com o cmdlet [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot). 

Primeiro, defina alguns parâmetros. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Obtenha o objeto da VM.

```powershell
$vm = Get-AzVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Obtenha o nome de disco do sistema operacional.

 ```powershell
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Crie a configuração do instantâneo. 

 ```powershell
$snapshotConfig =  New-AzSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

Crie o instantâneo.

```powershell
$snapShot = New-AzSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Para usar o instantâneo e criar uma VM que precisa ser de alto desempenho, adicione o parâmetro `-AccountType Premium_LRS` ao comando New-AzSnapshotConfig. Esse parâmetro cria o instantâneo para que seja armazenado como um disco gerenciado Premium. Os discos gerenciados premium são mais caros que o padrão, portanto, verifique se você precisa do Premium antes de usar esse parâmetro.

### <a name="create-a-new-disk-from-the-snapshot"></a>Criar um novo disco a partir do instantâneo

Crie um disco gerenciado com base no instantâneo usando [New-AzDisk](/powershell/module/az.compute/new-azdisk). Este exemplo usa *myOSDisk* para o nome do disco.

Criar um novo grupo de recursos para a nova VM.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Defina o nome de disco do sistema operacional. 

```powershell
$osDiskName = 'myOsDisk'
```

Criar o disco gerenciado.

```powershell
$osDisk = New-AzDisk -DiskName $osDiskName -Disk `
    (New-AzDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Crie a nova VM 

Crie rede e outros recursos de máquina virtual a ser usado pela nova VM.

### <a name="create-the-subnet-and-virtual-network"></a>Crie a sub-rede e a rede virtual

Criar a [rede virtual](../../virtual-network/virtual-networks-overview.md) e uma sub-rede para a VM.

1. Crie a sub-rede. Este exemplo cria uma sub-rede chamada *mySubNet* no grupo de recursos *myDestinationResourceGroup* e defina o prefixo de endereço como *10.0.0.0/24*.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Crie a rede virtual. Este exemplo define o nome da rede virtual como *myVnetName*, o local como *West US* e o prefixo de endereço da rede virtual como *10.0.0.0/16*. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP
Para poder entrar em sua VM com o RDP (Remote Desktop Protocol), você precisará ter uma regra de segurança que permita acesso RDP na porta 3389. Em nosso exemplo, o VHD para a nova VM foi criado a partir de uma VM especializada existente, para que você possa usar uma conta que existia na máquina virtual de origem para o RDP.

Este exemplo define o nome do grupo de segurança de rede (NSG) para *myNsg* e o nome da regra de RDP para *myRdpRule*.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

Para obter mais informações sobre pontos de extremidade e regras do NSG, consulte [abrir portas para uma VM no Azure usando o PowerShell](nsg-quickstart-powershell.md).

### <a name="create-a-public-ip-address-and-nic"></a>Criar um endereço IP público e uma NIC
Para permitir a comunicação com a máquina virtual na rede virtual, você precisará de um [endereço IP público](../../virtual-network/public-ip-addresses.md) e uma interface de rede.

1. Crie o endereço IP público. Neste exemplo, o nome do endereço IP público é definido como *myIP*.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Crie a NIC. Neste exemplo, o nome da NIC é definido como *myNicName*.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>Como definir o nome e tamanho da VM

Este exemplo define o nome da VM para *myVM* e o tamanho da VM para *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Como adicionar o NIC
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Adicionar o disco do sistema operacional 

Inclua o disco do sistema operacional na configuração usando [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk). Este exemplo define o tamanho do disco para *128 GB* e anexa o disco gerenciado como um disco do sistema operacional do *Windows*.
 
```powershell
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Concluir a VM 

Crie a VM usando [New-AzVM](/powershell/module/az.compute/new-azvm) com as configurações que acabamos de criar.

```powershell
New-AzVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Se esse comando for bem-sucedido, você verá uma saída como esta:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Verificar se a VM foi criada
Você deve ver a VM recém-criada na [portal do Azure](https://portal.azure.com) sob **procurar** > **máquinas virtuais**, ou usando os seguintes comandos do PowerShell.

```powershell
$vmList = Get-AzVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Próximas etapas
Logue na nova máquina virtual. Para obter mais informações, veja [Como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](connect-logon.md).