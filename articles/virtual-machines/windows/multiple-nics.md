---
title: "Crie e gerencie as VMs do Windows no Azure que usam várias NICs | Microsoft Docs"
description: "Saiba como criar e gerenciar uma VM do Windows que tem várias NICs anexadas usando o Azure PowerShell ou modelos do Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: iainfou
ms.openlocfilehash: 9e84e18c5d6c205d320c6c9c565ce6723d8fa6e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Cria e gerencia uma máquina virtual do Windows que tem várias NICs
As máquinas virtuais (VMs) no Azure podem ter várias placas de interface de rede virtual (NICs) anexadas a elas. Um cenário comum é ter sub-redes diferentes para conectividade de front-end e de back-end ou uma rede dedicada a uma solução de monitoramento ou de backup. Este artigo fornece detalhes sobre como criar uma VM que tem várias NICs anexadas. Você também aprenderá a adicionar ou remover as NICs de uma VM existente. Diferentes [tamanhos de VM](sizes.md) dão suporte a um número variável de NICs, sendo assim, dimensione sua VM adequadamente.

Para obter informações detalhadas, incluindo como criar várias NICs dentro de seus próprios scripts do PowerShell, consulte [implantação de VMs com várias NICs](../../virtual-network/virtual-network-deploy-multinic-arm-ps.md).

## <a name="prerequisites"></a>Pré-requisitos
Verifique se você tem [ a versão mais recente do Azure PowerShell instalada e configurada](/powershell/azure/overview).

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem *myResourceGroup*, *myVnet* e *myVM*.


## <a name="create-a-vm-with-multiple-nics"></a>Criar uma VM com diversos NICs
Em primeiro lugar, crie um grupo de recursos. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *EastUs*:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Crie a rede virtual e as sub-redes
Um cenário comum é para uma rede virtual ter duas ou mais sub-redes. Uma sub-rede pode ser usada para o tráfego de front-end, a outra para o tráfego de back-end. Para se conectar a ambas as sub-redes, você usa várias NICs em sua VM.

1. Defina duas sub-redes de rede virtual com [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). O exemplo a seguir define as sub-redes, para *mySubnetFrontEnd* e *mySubnetBackEnd*:

    ```powershell
    $mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Crie uma rede virtual e sub-redes com [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). O seguinte exemplo cria uma rede virtual chamada *myVnet*:

    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Crie múltiplas NICs
Crie duas NICs com [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). Anexe uma NIC à sub-rede de front-end e uma NIC à sub-rede de back-end. O exemplo a seguir cria NICs, chamadas *myNic1* e *myNic2*:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

Normalmente, você também criaria um [grupo de segurança de rede](../../virtual-network/virtual-networks-nsg.md) ou [balanceador de carga](../../load-balancer/load-balancer-overview.md) para ajudar a gerenciar e distribuir o tráfego entre suas VMs. O artigo [mais detalhado sobre VMs com várias NICs](../../virtual-network/virtual-network-deploy-multinic-arm-ps.md) o orientará na criação de um grupo de segurança de rede e na atribuição de NICs.

### <a name="create-the-virtual-machine"></a>Criar a máquina virtual
Agora, comece a criar sua configuração de VM. Cada tamanho de VM tem um limite para o número total de NICs que podem ser adicionada a uma VM. Para obter mais informações, consulte [Tamanhos da VM no Windows](sizes.md) .

1. Defina suas credenciais de VM para a variável `$cred` da seguinte maneira:

    ```powershell
    $cred = Get-Credential
    ```

2. Defina sua VM com [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). O exemplo a seguir define uma VM chamada *myVM* e usa um tamanho de VM que dá suporte a até duas NICs (*Standard_DS3_v2*):

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Cria o restante da sua configuração de VM com [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) e [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage). O exemplo a seguir cria uma VM do Windows Server 2016:

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
   ```

4. Anexe as duas NICs que você criou anteriormente com [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Finalmente, crie sua VM com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

    ```powershell
    New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

## <a name="add-a-nic-to-an-existing-vm"></a>Adicionar uma NIC a uma VM existente
Para adicionar uma NIC virtual a uma VM existente, você desalocar a VM, adiciona a NIC virtual e inicia a VM. Diferentes [tamanhos de VM](sizes.md) dão suporte a um número variável de NICs, sendo assim, dimensione sua VM adequadamente. Se necessário, é possível [redimensionar uma VM](resize-vm.md).

1. Desaloque a VM com o [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). O seguinte exemplo desaloca a VM chamada *myVM* no *myResourceGroup*:

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Obtenha a configuração existente da VM com [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm). O exemplo a seguir obtém informações sobre a VM chamada *myVM* no *myResourceGroup*:

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. O exemplo a seguir cria uma NIC virtual com [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) chamada *myNic3* que está anexada ao *mySubnetBackEnd*. A NIC virtual é anexada a VM chamada *myVM* no *myResourceGroup* com [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId | Update-AzureRmVm -ResourceGroupName "myResourceGroup"
    ```

    ### <a name="primary-virtual-nics"></a>NICs virtuais primárias
    Uma das NICs em uma VM com várias NICs precisa ser primária. Se uma das NICs virtuais existentes na VM já estiver definida como primária, você pode ignorar esta etapa. O exemplo a seguir presume que duas NICs virtuais agora estão presentes em uma VM e você deseja adicionar a primeira NIC (`[0]`) como a primária:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Inicie a VM com [Start-AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm):

    ```powershell
    Start-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

## <a name="remove-a-nic-from-an-existing-vm"></a>Remover uma NIC de uma VM existente
Para remover uma NIC virtual de uma VM existente, você desaloca a VM, remove a NIC virtual e inicia a VM.

1. Desaloque a VM com o [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). O seguinte exemplo desaloca a VM chamada *myVM* no *myResourceGroup*:

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Obtenha a configuração existente da VM com [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm). O exemplo a seguir obtém informações sobre a VM chamada *myVM* no *myResourceGroup*:

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Obter informações sobre a remoção de NIC com [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface). O seguinte exemplo obtém informações sobre *myNic3*:

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Remova a NIC com [Remove-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface) e, em seguida, atualize a VM com [Update-AzureRmVm](/powershell/module/azurerm.compute/update-azurermvm). O exemplo a seguir remove *myNic3* conforme obtidas pelo `$nicId` na etapa anterior:

    ```powershell
    Remove-AzureRmVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzureRmVm -ResourceGroupName "myResourceGroup"
    ```   

5. Inicie a VM com [Start-AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm):

    ```powershell
    Start-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Criar várias NICs com modelos
Os modelos do Azure Resource Manager oferecem uma maneira de criar várias instâncias de um recurso durante a implantação, como a criação de várias NICs. Os modelos do Resource Manager usam arquivos JSON declarativos para definir o seu ambiente. Para saber mais, consulte [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Você usa *copiar* para especificar o número de instâncias a serem criadas:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

Para obter mais informações, consulte [criando várias instâncias usando *cópia*](../../resource-group-create-multiple.md). 

Você também pode usar `copyIndex()` para acrescentar um número a um nome de recurso. Você pode criar *myNic1*, *MyNic2* e assim por diante. O código a seguir mostra um exemplo de como acrescentar o valor de índice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Você pode ler um exemplo completo em [criando várias NICs usando modelos do Resource Manager](../../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Próximas etapas
Analise os [tamanhos de VM do Windows](sizes.md) quando estiver tentando criar uma VM que tem várias NICs. Preste atenção ao número máximo de NICs a que cada VM dá suporte. 


