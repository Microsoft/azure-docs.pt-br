---
title: Crie uma VM do Windows zoneada - Azure PowerShell | Microsoft Docs
description: Crie uma máquina virtual do Windows em uma zona de Disponibilidade com o Azure PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: fcb83b18267c1b4072ea02bdd4d30356f7f360ed
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849627"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-powershell"></a>Crie uma máquina virtual do Windows em uma zona de Disponibilidade com o PowerShell

Este artigo detalha usando o Azure PowerShell para criar uma máquina virtual do Azure em execução no Windows Server 2016 em uma região de disponibilidade do Azure. Uma [zona de disponibilidade](../../availability-zones/az-overview.md) é uma zona fisicamente separada em uma região do Azure. Use zonas de disponibilidade para proteger seus aplicativos e dados de uma improvável falha ou perda de um datacenter inteiro.

Para usar uma zona de disponibilidade, crie a máquina virtual em uma [região do Azure com suporte](../../availability-zones/az-overview.md#services-support-by-region).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre na sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela.

```powershell
Connect-AzAccount
```

## <a name="check-vm-sku-availability"></a>Verificar a disponibilidade do SKU de VM
A disponibilidade de tamanhos de VM, ou de SKUs, pode variar por região e por zona. Para ajudá-lo a planejar o uso de Zonas de Disponibilidade, você poderá listar os SKUs de VM disponíveis por região e zona do Azure. Essa capacidade garante que você escolha um tamanho adequado de VM e obtenha a resiliência desejada entre as zonas. Para saber mais sobre os diferentes tipos e tamanhos de VM, confira [Visão geral de tamanhos de VM](sizes.md).

É possível exibir os SKUs de VM disponíveis com o comando [Get-AzComputeResourceSku](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku). O exemplo a seguir lista os SKUs de VM disponíveis na região *eastus2*:

```powershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("eastus2")};
```

A saída é semelhante ao exemplo condensado a seguir, que mostra as Zonas de Disponibilidade nas quais cada tamanho de VM está disponível:

```powershell
ResourceType                Name  Location      Zones   [...]
------------                ----  --------      -----
virtualMachines  Standard_DS1_v2   eastus2  {1, 2, 3}
virtualMachines  Standard_DS2_v2   eastus2  {1, 2, 3}
[...]
virtualMachines     Standard_F1s   eastus2  {1, 2, 3}
virtualMachines     Standard_F2s   eastus2  {1, 2, 3}
[...]
virtualMachines  Standard_D2s_v3   eastus2  {1, 2, 3}
virtualMachines  Standard_D4s_v3   eastus2  {1, 2, 3}
[...]
virtualMachines   Standard_E2_v3   eastus2  {1, 2, 3}
virtualMachines   Standard_E4_v3   eastus2  {1, 2, 3}
```


## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos do Azure com [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Neste exemplo, um grupo de recursos denominado *myResourceGroup* é criado na região *eastus2*. 

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS2
```

## <a name="create-networking-resources"></a>Criar recursos de rede

### <a name="create-a-virtual-network-subnet-and-a-public-ip-address"></a>Crie uma rede virtual, sub-rede e um endereço IP público 
Esses recursos são usados para fornecer conectividade de rede para a máquina virtual e conectá-la à Internet. Crie o endereço IP em uma zona de disponibilidade, *2* neste exemplo. Em uma etapa posterior, você criará a VM na mesma zona usada para criar o endereço IP.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myVNet -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address in an availability zone and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName myResourceGroup -Location eastus2 -Zone 2 `
    -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Crie um grupo de segurança de rede e uma regra de grupo de segurança de rede 
O grupo de segurança de rede protege a máquina virtual usando regras de entrada e saída. Nesse caso, uma regra de entrada é criada para a porta 3389, que permite conexões de área de trabalho remota. Também queremos criar uma regra de entrada para a porta 80, que permite o tráfego de entrada da Web.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
    -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Crie uma placa de rede para a máquina virtual 
Crie uma placa de rede com [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) para a máquina virtual. A placa de rede conecta a máquina virtual a uma sub-rede, um grupo de segurança de rede e um endereço IP público.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location eastus2 `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>Criar máquina virtual

Crie uma configuração de máquina virtual. Essa configuração inclui as configurações que são usadas ao implantar a máquina virtual como uma imagem, tamanho e configuração de autenticação de máquina virtual. O tamanho *Standard_DS1_v2* neste exemplo tem suporte nas zonas de disponibilidade. Essa configuração também especifica a zona de disponibilidade definida ao criar o endereço IP. Ao executar esta etapa, credenciais serão solicitadas de você. Os valores que você insere são configurados como o nome de usuário e senha para a máquina virtual.

```powershell
# Define a credential object
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS1_v2 -Zone 2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id
```

Crie a máquina virtual com [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm).

```powershell
New-AzVM -ResourceGroupName myResourceGroup -Location eastus2 -VM $vmConfig
```

## <a name="confirm-zone-for-managed-disk"></a>Confirme a zona do disco gerenciado

Você criou o recurso de endereço IP da VM na mesma região da disponibilidade da VM. O recurso de disco gerenciado para a VM é criado na mesma zona de disponibilidade. É possível verificar isso com [Get-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/get-azdisk):

```powershell
Get-AzDisk -ResourceGroupName myResourceGroup
```

A saída mostra que o disco gerenciado está na mesma região da disponibilidade da VM:

```powershell
ResourceGroupName  : myResourceGroup
AccountType        : PremiumLRS
OwnerId            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
ManagedBy          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx//resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              : {2}
TimeCreated        : 9/7/2017 6:57:26 PM
OsType             : Windows
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 127
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/disks/myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Name               : myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Type               : Microsoft.Compute/disks
Location           : eastus2
Tags               : {}
```


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a criar uma VM em uma zona de disponibilidade. Saiba mais sobre a [disponibilidade](availability.md) de VMs do Azure.
