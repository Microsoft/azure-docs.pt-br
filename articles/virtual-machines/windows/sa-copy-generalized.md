---
title: Criar uma imagem não gerenciada de uma VM generalizada no Azure | Microsoft Docs
description: Criar uma imagem não gerenciada de uma VM Windows generalizada para usar ao criar várias cópias de uma VM no Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 929dd5bdb01adeaa7b1332bd7a5e6d823edba34a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710403"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Como criar uma imagem de VM não gerenciada a partir de uma VM do Azure

Este artigo abrange o uso de contas de armazenamento. É recomendável que você utilize discos gerenciados e imagens gerenciadas em vez de uma conta de armazenamento. Para obter mais informações, consulte [Capture uma imagem gerenciada de uma VM generalizada no Azure](capture-image-resource.md).

Este artigo mostra como usar o Azure PowerShell para criar uma imagem de uma VM Azure generalizada utilizando uma conta de armazenamento. Depois você pode usar a imagem para criar outra VM. Esta imagem inclui o disco do SO e os discos de dados anexados à máquina virtual. A imagem não inclui os recursos de rede virtual, de modo que você precisará configurar esses recursos quando criar a nova VM. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="generalize-the-vm"></a>Generalizar a VM 
Esta seção mostra como generalizar a máquina virtual do Windows para usar como uma imagem. Generalizar uma VM remove todas as informações pessoais da conta, entre outros itens, e prepara o computador para ser utilizado como uma imagem. Para obter detalhes sobre o Sysprep, confira [Como usar o Sysprep: Um introdução](https://technet.microsoft.com/library/bb457073.aspx).

Verifique se as funções de servidor em execução no computador são suportadas pelo Sysprep. Para obter mais informações, consulte [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Se você estiver enviando seu VHD para o Azure pela primeira vez, certifique-se de que [preparou sua VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de executar o Sysprep. 
> 
> 

Você também pode generalizar uma VM Linux usando `sudo waagent -deprovision+user` e depois o PowerShell para capturar a VM. Para obter informações sobre como usar a CLI para capturar uma VM, consulte [como generalizar e capturar uma máquina virtual do Linux usando a CLI do Azure](../linux/capture-image.md).


1. Entre na máquina virtual Windows.
2. Abra uma janela de prompt de comando como administrador. Altere o diretório para **%windir%\system32\sysprep** e, a seguir, execute`sysprep.exe`.
3. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, selecione **Entrar na Configuração Inicial pelo Usuário do Sistema (OOBE)** e verifique se a caixa de seleção **Generalizar** está marcada.
4. Em **Opções de Desligamento**, selecione **Desligar**.
5. Clique em **OK**.
   
    ![Inicie o Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Quando o Sysprep for concluído, desligará a máquina virtual. 

> [!IMPORTANT]
> Só reinicie a VM quando concluir o carregamento do VHD no Azure ou a criação de uma imagem da VM. Se a VM for reiniciada acidentalmente, execute o Sysprep para generalizá-la novamente.
> 
> 

## <a name="log-in-to-azure-powershell"></a>Fazer logon no Azure PowerShell
1. Abra o Azure PowerShell e conecte-se à sua conta do Azure.
   
    ```powershell
    Connect-AzAccount
    ```
   
    Uma janela pop-up é aberta para inserir as credenciais da conta do Azure.
2. Obtenha as IDs de assinatura das assinaturas disponíveis.
   
    ```powershell
    Get-AzSubscription
    ```
3. Defina a assinatura correta usando a ID da assinatura.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Desalocar a VM e definir o estado como generalizada

> [!IMPORTANT] 
> Você não pode adicionar, editar ou remover as marcas de uma VM quando ela está marcada como generalizada. Se você desejar adicionar uma marca à VM, certifique-se de adicionar as marcas antes de marcá-la como generalizada.
> 

1. Desaloque os recursos da VM.
   
    ```powershell
    Stop-AzVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    O *Status* da VM no Portal do Azure muda de **Parado** para **Parado (desalocado)** .
2. Defina o status da máquina virtual como **Generalizado**. 
   
    ```powershell
    Set-AzVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Verifique o status da VM. A seção **OSState/generalizado** da VM deve ter **DisplayStatus** definido como **VM generalizada**.  
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Criar a imagem

Crie uma imagem da máquina virtual não gerenciada no contêiner de armazenamento de destino utilizando esse comando. A imagem é criada na mesma conta de armazenamento que a máquina virtual original. O parâmetro `-Path` salva uma cópia do modelo JSON para a VM de origem em seu computador local. O parâmetro `-DestinationContainerName` é o nome do contêiner em que você quer manter as imagens. Se o contêiner não existir, ele será criado para você.
   
```powershell
Save-AzVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
Você pode obter a URL da imagem no modelo do arquivo JSON. Vá para a seção **recursos** > **storageProfile** > **osDisk** > **image** > **uri** para obter o caminho completo da imagem. A URL da imagem parece com esta: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
Você também pode verificar o URI no portal. A imagem é copiada em um contêiner denominado **sistema** em sua conta de armazenamento. 

## <a name="create-a-vm-from-the-image"></a>Criar uma VM a partir da imagem

Agora, você pode criar uma ou mais VMs a partir da imagem não gerenciada.

### <a name="set-the-uri-of-the-vhd"></a>Definir o URI do VHD

O URI do VHD a usar está no formato: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd. Neste exemplo, o VHD chamado **myVHD** está na conta de armazenamento **mystorageaccount** no contêiner **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie a vNet e a sub-rede da [rede virtual](../../virtual-network/virtual-networks-overview.md).

1. Crie a sub-rede. O exemplo a seguir cria uma sub-rede chamada **mySubnet** no grupo de recursos **myResourceGroup** com o prefixo de endereço **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Crie a rede virtual. O exemplo a seguir cria uma rede virtual chamada **myVnet** na localização **Oeste dos EUA** com o prefixo de endereço **10.0.0.0/16**.  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Criar um endereço IP público e um adaptador de rede
Para habilitar a comunicação com a máquina virtual na rede virtual, são necessários um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e um adaptador de rede.

1. Criar um endereço IP público. Este exemplo cria um endereço IP público chamado **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Crie a NIC. Este exemplo cria uma NIC chamada **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP
Para fazer logon em sua VM usando RDP, é preciso ter uma regra de segurança que permita acesso RDP na porta 3389. 

Este exemplo cria um NSG chamado **myNsg** que contém uma regra chamada **myRdpRule** que permite tráfego RDP pela porta 3389. Para obter mais informações sobre NSGs, consulte [Abrir portas para uma VM no Azure usando PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Criar uma variável para a rede virtual
Crie uma variável para a rede virtual concluída. 

```powershell
$vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Criar a VM
O seguinte PowerShell completa as configurações da máquina virtual e usa a imagem não gerenciada como fonte para a nova instalação.

</br>

```powershell
    # Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

### <a name="verify-that-the-vm-was-created"></a>Verificar se a VM foi criada
Ao concluir, você deverá ver a VM recém-criada no [portal do Azure](https://portal.azure.com) em **Navegar** > **Máquinas virtuais** ou usando os seguintes comandos do PowerShell:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Próximas etapas
Para gerenciar sua nova máquina virtual com o Azure PowerShell, consulte [Gerenciar máquinas virtuais usando o PowerShell e o Azure Resource Manager](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


