---
title: Vários endereços IP para máquinas virtuais do Azure – PowerShell | Microsoft Docs
description: Saiba como atribuir vários endereços IP a uma máquina virtual usando o PowerShell. | Gerenciador de recursos
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: c44ea62f-7e54-4e3b-81ef-0b132111f1f8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/24/2017
ms.author: kumud
ms.reviewer: annahar
ms.openlocfilehash: e9bad6ad614855c543ee6d75d4e6f4dc8e2255aa
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876221"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Atribuir vários endereços IP a máquinas virtuais usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Este artigo explica como criar uma máquina virtual (VM) por meio do Modelo de implantação do Azure Resource Manager usando o PowerShell. Múltiplos endereços IP não podem ser atribuídos a recursos criados por meio do modelo de implantação clássico. Para saber mais sobre modelos de implantação do Azure, leia o artigo [Compreender os modelos de implantação](../resource-manager-deployment-model.md).

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Criar uma VM com vários endereços IP

As etapas a seguir explicam como criar uma VM de exemplo com vários endereços IP, como descrito no cenário. Altere os valores da variável conforme exigido por sua implementação.

1. Abra um prompt de comando do PowerShell e siga as etapas restantes nesta seção dentro de uma única sessão do PowerShell. Se o Azure PowerShell ainda não foi instalado nem configurado, siga as etapas no artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) .
2. Faça logon em sua conta com o comando `Connect-AzAccount`.
3. Substitua *myResourceGroup* e *westus* por um nome e local de sua escolha. Crie um grupos de recursos. Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

   ```powershell
   $RgName   = "MyResourceGroup"
   $Location = "westus"

   New-AzResourceGroup `
   -Name $RgName `
   -Location $Location
   ```

4. Crie uma rede virtual (VNet) e sub-rede no mesmo local que o grupo de recursos:

   ```powershell

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

5. Crie um NSG (grupo de segurança de rede) e uma regra. O NSG protege a VM usando regras de entrada e saída. Nesse caso, uma regra de entrada é criada para a porta 3389, que permite conexões de área de trabalho remota.

    ```powershell
    
    # Create an inbound network security group rule for port 3389

    $NSGRule = New-AzNetworkSecurityRuleConfig `
    -Name MyNsgRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $NSG = New-AzNetworkSecurityGroup `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyNetworkSecurityGroup `
    -SecurityRules $NSGRule
    ```

6. Defina a configuração de IP primário da NIC. Caso você não tenha usado o valor definido anteriormente, altere 10.0.0.4 para um endereço válido na sub-rede que você criou. Antes de atribuir um endereço IP estático, é recomendável que você primeiro confirme que ele ainda não está em uso. Digite o comando `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet`. Se o endereço está disponível, a saída retorna *True*. Se não está disponível, a saída retorna *False* e uma lista de endereços disponíveis. 

    Nos comandos a seguir, **substitua \<o nome exclusivo do Replace-com-seu > pelo nome DNS exclusivo a ser usado.** O nome deve ser exclusivo entre todos os endereços IP públicos dentro de uma região do Azure. Este é um parâmetro opcional. Ele pode ser removido se você deseja conectar-se à VM usando o endereço IP público.

    ```powershell
    
    # Create a public IP address
    $PublicIP1 = New-AzPublicIpAddress `
    -Name "MyPublicIP1" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -DomainNameLabel <replace-with-your-unique-name> `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName1 = "IPConfig-1"
    $IpConfig1     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName1 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.4 `
    -PublicIpAddress $PublicIP1 `
    -Primary
    ```

    Quando você atribuir várias configurações de IP a uma NIC, uma configuração deverá ser atribuída como a *-Primary*.

    > [!NOTE]
    > Endereços IP públicos têm um valor nominal. Para saber mais sobre preços de endereço IP, leia a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Há um limite para o número de endereços IP públicos que podem ser usados em uma assinatura. Para saber mais sobre os limites, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md#networking-limits).

7. Defina as configurações de IP secundário da NIC. Você pode adicionar, remover ou alterar as configurações conforme necessário. Cada configuração de IP deve ter um endereço IP privado atribuído. Cada configuração pode, opcionalmente, ter um endereço IP público atribuído.

    ```powershell
    
    # Create a public IP address
    $PublicIP2 = New-AzPublicIpAddress `
    -Name "MyPublicIP2" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName2 = "IPConfig-2"
    $IpConfig2     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName2 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.5 `
    -PublicIpAddress $PublicIP2
        
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzNetworkInterfaceIpConfig `
    -Name $IPConfigName3 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.6
    ```

8. Criar a NIC e associar as três configurações de IP a ela:

   ```powershell
   $NIC = New-AzNetworkInterface `
   -Name MyNIC `
   -ResourceGroupName $RgName `
   -Location $Location `
   -NetworkSecurityGroupId $NSG.Id `
   -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
   ```

   >[!NOTE]
   >Embora todas as configurações estejam atribuídas a uma NIC neste artigo, você pode atribuir várias configurações de IP a cada NIC anexada à VM. Para saber como criar uma VM com vários NICs, leia o artigo [Criar uma VM com vários NICs](../virtual-machines/windows/multiple-nics.md).

9. Crie a VM digitando os seguintes comandos:

    ```powershell
    
    # Define a credential object. When you run these commands, you're prompted to enter a username and password for the VM you're creating.
    $cred = Get-Credential
    
    # Create a virtual machine configuration
    $VmConfig = New-AzVMConfig `
    -VMName MyVM `
    -VMSize Standard_DS1_v2 | `
    Set-AzVMOperatingSystem -Windows `
    -ComputerName MyVM `
    -Credential $cred | `
    Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
    Add-AzVMNetworkInterface `
    -Id $NIC.Id
    
    # Create the VM
    New-AzVM `
    -ResourceGroupName $RgName `
    -Location $Location `
    -VM $VmConfig
    ```

10. Adicione os endereços IP privados ao sistema operacional da VM executando as etapas para seu sistema operacional na seção [Adicionar endereços IP ao sistema operacional de uma VM](#os-config) deste artigo. Não adicione os endereços IP públicos ao sistema operacional.

## <a name="add"></a>Adicionar endereços IP a uma VM

Você pode adicionar Endereços IP Públicos e privados a um adaptador de rede do Azure executando as etapas a seguir. Os exemplos nas seções a seguir pressupõem que você já tem uma VM com as três configurações de IP descritas no [cenário](#scenario) neste artigo, mas isso não é obrigatório.

1. Abra um prompt de comando do PowerShell e siga as etapas restantes nesta seção dentro de uma única sessão do PowerShell. Se o Azure PowerShell ainda não foi instalado nem configurado, siga as etapas no artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) .
2. Altere os "valores" de $Variables a seguir para o nome do NIC ao qual você deseja adicionar os endereços IP e o grupo de recursos e a localização onde o NIC está:

   ```powershell
   $NicName  = "MyNIC"
   $RgName   = "MyResourceGroup"
   $Location = "westus"
   ```

   Se você não souber o nome da NIC que você deseja alterar, digite os seguintes comandos e altere os valores das variáveis anteriores:

   ```powershell
   Get-AzNetworkInterface | Format-Table Name, ResourceGroupName, Location
   ```

3. Crie uma variável e defina-a para a NIC existente digitando o seguinte comando:

   ```powershell
   $MyNIC = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $RgName
   ```

4. Nos comandos a seguir, mude *MyVNet* e *MySubnet* para os nomes da VNet e da sub-rede às quais a NIC está conectada. Digite os comandos para recuperar os objetos de rede virtual e sub-rede aos quais o NIC está conectado:

   ```powershell
   $MyVNet = Get-AzVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
   $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
   ```

   Se você não souber o nome da rede virtual ou sub-rede à qual o NIC está conectado, digite o seguinte comando:

   ```powershell
   $MyNIC.IpConfigurations
   ```

   Na saída, procure por texto semelhante à saída de exemplo a seguir:

   ```
   "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
   ```

    Nessa saída, *MyVnet* é a VNet e *MySubnet* são, respectivamente, a rede virtual e a sub-rede às quais a NIC está conectada.

5. Conclua as etapas em uma das seções a seguir, com base em seus requisitos:

   **Adicionar um endereço IP privado**

   Para adicionar um endereço IP privado a um NIC, você deverá criar uma configuração de IP. O comando a seguir cria uma configuração com um endereço IP estático 10.0.0.7. Ao especificar um endereço IP estático, ele deve ser um endereço não usado para a sub-rede. É recomendável que você primeiro teste o endereço para garantir que ele está disponível digitando o comando `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet`. Se o endereço IP está disponível, a saída retorna *True*. Se não está disponível, a saída retorna *False* e uma lista de endereços disponíveis.

   ```powershell
   Add-AzNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
   $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
   ```

   Crie quantas configurações forem necessárias, usando nomes de configuração exclusivos e endereços IP privados (para configurações com endereços IP estáticos).

   Adicione o endereço IP privado ao sistema operacional da VM executando as etapas para seu sistema operacional na seção [Adicionar endereços IP ao sistema operacional de uma VM](#os-config) deste artigo.

   **Adicionar um endereço IP público**

   Um endereço IP público é adicionado por meio da associação de um recurso de endereço IP público a uma nova configuração de IP ou a uma configuração de IP existente. Conclua as etapas em uma das seções a seguir, quando você precisar.

   > [!NOTE]
   > Endereços IP públicos têm um valor nominal. Para saber mais sobre preços de endereço IP, leia a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Há um limite para o número de endereços IP públicos que podem ser usados em uma assinatura. Para saber mais sobre os limites, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md#networking-limits).
   >

   **Associar o recurso de endereço IP público a uma nova configuração de IP**

   Sempre que você adicionar um endereço IP público em uma nova configuração de IP, também deverá adicionar um endereço IP privado, pois todas as configurações de IP devem ter um endereço IP privado. Você pode adicionar um recurso de endereço IP público existente ou criar um novo. Para criar um novo, insira o seguinte comando:

   ```powershell
   $myPublicIp3 = New-AzPublicIpAddress `
   -Name "myPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location `
   -AllocationMethod Static
   ```

   Para criar uma nova configuração de IP com um endereço IP privado estático e o recurso de endereço IP público *myPublicIp3* associado, insira o seguinte comando:

   ```powershell
   Add-AzNetworkInterfaceIpConfig `
   -Name IPConfig-4 `
   -NetworkInterface $myNIC `
   -Subnet $Subnet `
   -PrivateIpAddress 10.0.0.7 `
   -PublicIpAddress $myPublicIp3
   ```

   **Associar o recurso de endereço IP público a uma configuração de IP existente**

   Um recurso de endereço IP público só pode ser associado a uma configuração de IP que ainda não tenha um associado. Você pode determinar se uma configuração de IP tem um endereço IP público associado inserindo o seguinte comando:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

   Você verá uma saída semelhante ao seguinte:

   ```
   Name       PrivateIpAddress PublicIpAddress                                           Primary

   IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
   IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
   IpConfig-3 10.0.0.6                                                                     False
   ```

   Como a coluna **PublicIpAddress** para *IpConfig 3* está em branco, nenhum recurso de endereço IP público está associado a ele no momento. Você pode adicionar um recurso de endereço IP público existente como IpConfig-3 ou inserir o seguinte comando para criar um:

   ```powershell
   $MyPublicIp3 = New-AzPublicIpAddress `
   -Name "MyPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location -AllocationMethod Static
   ```

   Insira o comando a seguir para associar o recurso de endereço IP público à configuração de IP existente chamada *IpConfig-3*:

   ```powershell
   Set-AzNetworkInterfaceIpConfig `
   -Name IpConfig-3 `
   -NetworkInterface $mynic `
   -Subnet $Subnet `
   -PublicIpAddress $myPublicIp3
   ```

6. Defina o NIC com a nova configuração de IP digitando o seguinte comando:

   ```powershell
   Set-AzNetworkInterface -NetworkInterface $MyNIC
   ```

7. Exiba os endereços IP privados e o recurso de endereço IP público atribuído à NIC, digitando o seguinte comando:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

8. Adicione o endereço IP privado ao sistema operacional da VM executando as etapas para seu sistema operacional na seção [Adicionar endereços IP ao sistema operacional de uma VM](#os-config) deste artigo. Não adicione o endereço IP público ao sistema operacional.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]