---
title: Configurar a preferência de roteamento de uma VM – Azure PowerShell
description: Saiba como criar uma VM com um endereço IP público com opção de preferência de roteamento usando o Azure PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.openlocfilehash: 8ffa7ef7b3539e3cefc4e2f8621d7c67cd3b56b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101672956"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-powershell"></a>Configurar a preferência de roteamento de uma VM usando o Azure PowerShell

Este artigo mostra como configurar a preferência de roteamento para uma máquina virtual. O tráfego associado à Internet que parte da VM será roteado por meio da rede do ISP quando você escolher **Internet** como opção de preferência de roteamento. O roteamento padrão é feito por meio da rede global da Microsoft.

Este artigo mostra como criar uma máquina virtual com um IP público definido para rotear o tráfego pela rede do ISP usando o Azure PowerShell.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
1. Se usar o Cloud Shell, vá para a etapa 2. Abra uma sessão de comando e entre no Azure com `Connect-AzAccount`.
2. Crie um grupo de recursos com o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos na região Leste do Azure dos EUA:

    ```azurepowershell
    $rg = New-AzResourceGroup -Name MyResourceGroup -Location EastUS
    ```

## <a name="create-a-public-ip-address"></a>Criar um endereço IP público

Para acessar suas máquinas virtuais na Internet, você precisa de endereços IP públicos. Crie endereços IP públicos com [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). O exemplo a seguir cria um endereço IP público IPv4 chamado *MyPublicIP* com preferência de tipo de roteamento *Internet* no grupo de recursos *MyResourceGroup* da região *Leste dos EUA*:

```azurepowershell-interactive
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

## <a name="create-network-resources"></a>Criar recursos da rede

Antes de implantar uma VM, você deve criar recursos da rede de suporte: grupo de segurança de rede, rede virtual e NIC virtual.

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Crie um grupo de segurança de rede com [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). O exemplo a seguir cria um NSG chamado *myNSG*.

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myNSG"
```

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo a seguir cria uma rede virtual chamada *myVNET* com uma sub-rede chamada *mySubNet*:

### <a name="create-a-subnet"></a>Criar uma sub-rede

```azurepowershell
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "mySubnet" `
-AddressPrefix "10.0.0.0/24"
```

```azurepowershell
# Create a virtual network
$vnet = New-AzVirtualNetwork `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myVNET" `
-AddressPrefix "10.0.0.0/16" `
-Subnet $subnet
```

### <a name="create-a-nic"></a>Criar uma NIC

Criar NICs virtuais com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface. O exemplo a seguir cria uma NIC virtual.

```azurepowershell
# Create an IP Config
$ipconfig=New-AzNetworkInterfaceIpConfig `
-Name myIpConfig `
-Subnet $vnet.subnets[0] `
-PrivateIpAddressVersion IPv4 `
-PublicIpAddress  $publicIp

# Create a NIC
$nic = New-AzNetworkInterface `
-Name "mynic" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-NetworkSecurityGroupId $nsg.Id `
-IpConfiguration $ipconfig 
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Defina o nome de usuário e a senha de um administrador para as VMs com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell
 $cred = get-credential -Message "Routing Preference SAMPLE:  Please enter the Administrator credential to log into the VM."
```

Agora você pode criar a VM com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo a seguir cria duas VMs e os componentes de rede virtual necessários, caso ainda não existam.

```azurepowershell
 $vmsize = "Standard_A2"
 $ImagePublisher = "MicrosoftWindowsServer"
 $imageOffer = "WindowsServer"
 $imageSKU = "2019-Datacenter"

 $vmName= "myVM"
 $vmconfig = New-AzVMConfig -VMName $vmName -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption "FromImage" | Add-AzVMNetworkInterface -Id $nic.Id 
 $VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $vmconfig
```

## <a name="allow-network-traffic-to-the-vm"></a>Permitir o tráfego de rede para a VM

Para poder se conectar ao endereço IP público da Internet, verifique se você tem as portas necessárias abertas em qualquer grupo de segurança de rede associado ao adaptador de rede, se há uma sub-rede na qual o adaptador de rede está ou ambos. Você pode ver as regras de segurança ativas de uma interface de rede e sua sub-rede usando [Portal](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli) ou [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, você poderá usar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, a VM e todos os recursos relacionados.

 ```azurepowershell
 Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [preferência de roteamento em endereços IP públicos](routing-preference-overview.md).
* Saiba mais sobre os [endereços IP públicos](./public-ip-addresses.md#public-ip-addresses) no Azure.
* Saiba mais sobre as [configurações de endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address).