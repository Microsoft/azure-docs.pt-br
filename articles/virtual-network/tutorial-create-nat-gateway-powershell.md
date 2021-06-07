---
title: 'Tutorial: criar um gateway da NAT – PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Comece a criar um gateway da NAT usando o Azure PowerShell.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 884697cee84c05916fe19fb8f9435de86bda291e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102619871"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell"></a>Tutorial: Criar um gateway da NAT usando o Azure PowerShell

Este tutorial mostra como usar o serviço NAT da Rede Virtual do Azure. Você criará um gateway da NAT para fornecer conectividade de saída para uma máquina virtual no Azure. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Crie uma rede virtual.
> * Crie uma máquina virtual.
> * Crie um gateway da NAT e o associe à rede virtual.
> * Conecte-se à máquina virtual e verifique o endereço IP de NAT.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell instalado localmente ou Azure Cloud Shell

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o módulo do Azure PowerShell versão 5.4.1 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O seguinte exemplo cria um grupo de recursos chamado **myResourceGroupNAT** na localização **eastus2**:

```azurepowershell-interactive
$rsg = @{
    Name = 'myResourceGroupNAT'
    Location = 'eastus2'
}
New-AzResourceGroup @rsg
```
## <a name="create-the-nat-gateway"></a>Criar o gateway da NAT

Nesta seção, criamos o gateway da NAT e os recursos de suporte.

* Para acessar a Internet, você precisa de um ou mais endereços IP públicos para o gateway da NAT. Use [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para criar um recurso de endereço IP público chamado **myPublicIP** em **myResourceGroupNAT**. 

* Crie um gateway da NAT global do Azure com [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway). O resultado desse comando criará um recurso de gateway chamado **myNATgateway**, que usa o endereço IP público **myPublicIP**. O tempo limite de ociosidade está definido como 10 minutos.  

* Crie uma rede virtual chamada **myVnet** com uma sub-rede chamada **mySubnet** usando [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) no **myResourceGroup** usando [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O espaço de endereços IP da rede virtual é **10.1.0.0/16**. A sub-rede dentro da rede virtual é **10.1.0.0/24**.

* Crie um host do Azure Bastion chamado **myBastionHost** para acessar a máquina virtual. Use [New-AzBastion](/powershell/module/az.network/new-azbastion) para criar o bastion host. Crie um endereço IP público para o bastion host com [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell-interactive
## Create public IP address for NAT gateway ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create NAT gateway resource ##
$nat = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myNATgateway'
    IdleTimeoutInMinutes = '10'
    Sku = 'Standard'
    Location = 'eastus2'
    PublicIpAddress = $publicIP
}
$natGateway = New-AzNatGateway @nat

## Create subnet config and associate NAT gateway to subnet##
$subnet = @{
    Name = 'mySubnet'
    AddressPrefix = '10.1.0.0/24'
    NatGateway = $natGateway
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

```

## <a name="virtual-machine"></a>Máquina virtual

Nesta seção, você criará uma máquina virtual para testar um gateway da NAT, bem como verificará o endereço IP público da conexão de saída.

* Crie uma interface de rede com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface).

* Defina o nome de usuário e a senha de um administrador para a VM com [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential).

* Crie a máquina virtual com:
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)
    
```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'myResourceGroupNAT'

## Create network interface for virtual machine. ##
$nic = @{
    Name = "myNicVM"
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Subnet = $vnet.Subnets[0]
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM"
    VMSize = 'Standard_DS1_v2'  
}
$vmos = @{
    ComputerName = "myVM"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    VM = $vmConfig
}
New-AzVM @vm

```

Aguarde a conclusão da criação da máquina virtual antes de passar para a próxima seção.

## <a name="test-nat-gateway"></a>Testar um gateway da NAT

Nesta seção, testaremos um gateway da NAT. Primeiro, descobriremos o IP público do gateway da NAT. Depois nos conectaremos à máquina virtual de teste e verificaremos a conexão de saída por meio do gateway da NAT.
    
1. Entre no [Portal do Azure](https://portal.azure.com)

1. Localize o endereço IP público do gateway da NAT na tela **Visão Geral**. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myPublicIP**.

2. Anote o endereço IP público:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Descobrir o endereço IP público do gateway da NAT" border="true":::

3. Selecione **Todos os serviços** no menu esquerdo, depois **Todos os recursos**. Além disso, na lista de recursos, selecione a opção **myVM** que está localizada no grupo de recursos **myResourceGroupNAT**.

4. Na página **Visão Geral**, selecione **Conectar** e **Bastion**.

5. Selecione o botão azul **Usar Bastion**.

6. Insira o nome de usuário e a senha fornecidos durante a criação da VM.

7. Abra o **Internet Explorer** em **myTestVM**.

8. Digite **https://whatsmyip.com** na barra de endereços.

9. Verifique se o endereço IP exibido corresponde ao endereço do gateway da NAT anotado na etapa anterior:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="O Internet Explorer mostrando um IP de saída externo" border="true":::

## <a name="clean-up-resources"></a>Limpar os recursos

Caso não pretenda continuar usando esse aplicativo, exclua a rede virtual, a máquina virtual e o gateway da NAT usando as seguintes etapas:

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupNAT' -Force
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a NAT da Rede Virtual do Azure, confira:
> [!div class="nextstepaction"]
> [Visão geral da NAT da Rede Virtual](nat-overview.md)
