---
title: Upload de um VHD generalizado para criar várias VMs no Azure | Microsoft Docs
description: Carregar um VHD generalizado para uma conta de armazenamento do Azure para criar uma VM Windows para utilizar com o modelo de implantação do Resource Manager.
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
ms.date: 05/18/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: bdfcb729aed88dd93a2ce61a8d28f34a5c327713
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710376"
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Upload de um VHD generalizado para o Azure para criar uma nova VM

Este tópico abrange o upload de um disco não gerenciado generalizado para uma conta de armazenamento e, em seguida, criar uma nova VM utilizando o disco carregado. Uma imagem VHD generalizada teve todas as informações da sua conta pessoal removidas usando o Sysprep. 

Se desejar criar uma VM com base em um VHD especializado em uma conta de armazenamento, consulte [Criar uma VM com base em um VHD especializado](sa-create-vm-specialized.md).

Este tópico aborda o uso de contas de armazenamento, mas é recomendável que os clientes mudem para utilizar o Managed Disks. Para um completo passo a passo de como preparar, carregar e criar uma nova VM usando discos gerenciados, consulte [Criar uma nova VM a partir de um VHD generalizado carregado para o Azure utilizando o Managed Disks](upload-generalized-managed.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="prepare-the-vm"></a>Preparar a VM

Um VHD generalizado teve todas as informações da sua conta pessoal removidas usando o Sysprep. Se você pretende criar novas VMs usando o VHD como uma imagem, você deve:
  
  * [Preparar um VHD do Windows para carregar no Azure](prepare-for-upload-vhd-image.md). 
  * Generalize a máquina virtual utilizando o Sysprep

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Generalizar uma máquina virtual do Windows usando o Sysprep
Esta seção mostra como generalizar a máquina virtual do Windows para usar como uma imagem. O Sysprep remove todas as informações pessoais da conta, entre outros itens, e prepara o computador para ser utilizado como uma imagem. Para obter detalhes sobre o Sysprep, confira [Como usar o Sysprep: Um introdução](https://technet.microsoft.com/library/bb457073.aspx).

Verifique se as funções de servidor em execução no computador são suportadas pelo Sysprep. Para obter mais informações, consulte [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Se você estiver executando o Sysprep antes de carregar o VHD para o Azure pela primeira vez, verifique se você [preparou sua VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de executar o Sysprep. 
> 
> 

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


## <a name="upload-the-vhd"></a>Carregar o VHD

Faça upload do VHD para uma conta de armazenamento Azure.

### <a name="log-in-to-azure"></a>Fazer logon no Azure
Se você ainda não tiver a versão 1.4 ou superior do PowerShell instalada, leia [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

1. Abra o Azure PowerShell e conecte-se à sua conta do Azure. Uma janela pop-up é aberta para inserir as credenciais da conta do Azure.
   
    ```powershell
    Connect-AzAccount
    ```
2. Obtenha as IDs de assinatura das assinaturas disponíveis.
   
    ```powershell
    Get-AzSubscription
    ```
3. Defina a assinatura correta usando a ID da assinatura. Substitua `<subscriptionID>` com a ID da assinatura correta.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>Obter a conta de armazenamento
Você precisa de uma conta de armazenamento no Azure para armazenar a imagem da VM carregada. Você pode usar uma conta de armazenamento existente ou criar uma nova. 

Para exibir as contas de armazenamento disponíveis, digite:

```powershell
Get-AzStorageAccount
```

Se você quiser usar uma conta de armazenamento existente, vá para a seção Carregar a imagem da VM.

Se você precisa criar uma conta de armazenamento, siga estas etapas:

1. Você precisa do nome do grupo de recursos no qual a conta de armazenamento deve ser criada. Para saber quais são todos os grupos de recursos que estão em sua assinatura, digite:
   
    ```powershell
    Get-AzResourceGroup
    ```

    Para criar um grupo de recursos denominado **myResourceGroup** na região **Oeste dos EUA**, digite:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Crie uma conta de armazenamento com o nome **mystorageaccount** neste grupo de recursos usando o cmdlet [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount):
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
 
### <a name="start-the-upload"></a>Inicie o upload 

Use o cmdlet [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) para carregar a imagem em um contêiner na conta de armazenamento. Este exemplo carrega o arquivo **myVHD.vhd** de `"C:\Users\Public\Documents\Virtual hard disks\"` em uma conta de armazenamento denominada **mystorageaccount** no grupo de recursos **myResourceGroup**. O arquivo será colocado em um contêiner chamado **mycontainer** e o novo nome do arquivo será **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Se o comando tiver êxito, você receberá uma resposta semelhante a esta:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Dependendo da conexão de rede e do tamanho do arquivo VHD, esse comando poderá demorar um pouco para concluir.


## <a name="create-a-new-vm"></a>Criar uma nova VM 

Agora você pode utilizar o VHD carregado para criar uma nova VM. 

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
    $location = "WestUS"
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
O script do PowerShell a seguir mostra como definir as configurações da máquina virtual e usar a imagem da VM carregada como a fonte da nova instalação.



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

## <a name="verify-that-the-vm-was-created"></a>Verificar se a VM foi criada
Ao concluir, você deverá ver a VM recém-criada no [portal do Azure](https://portal.azure.com) em **Navegar** > **Máquinas virtuais** ou usando os seguintes comandos do PowerShell:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Próximas etapas
Para gerenciar sua nova máquina virtual com o Azure PowerShell, consulte [Gerenciar máquinas virtuais usando o PowerShell e o Azure Resource Manager](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


