---
title: Criar um gateway da NAT – Azure PowerShell
titlesuffix: Azure Virtual Network NAT
description: O início rápido mostra como criar um gateway da NAT usando o Azure PowerShell
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 54ec676da3311473f86a47aae600c2ec29192bb2
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935123"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell"></a>Tutorial: Criar um gateway da NAT usando o Azure PowerShell

Este tutorial mostra como usar o serviço NAT da Rede Virtual do Azure. Você criará um gateway da NAT para fornecer conectividade de saída para uma máquina virtual no Azure. 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Você pode concluir este tutorial usando o Azure Cloud Shell ou executar os comandos localmente.  Caso não tenha usado o Azure Cloud Shell, [entre agora](https://shell.azure.com).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O seguinte exemplo cria um grupo de recursos chamado **myResourceGroupNAT** na localização **eastus2**:

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
  
New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>Criar o gateway da NAT

As opções de IP Público para o gateway da NAT são:

* **Endereços IP públicos**
* **Prefixos IP públicos**

Ambos podem ser usados com o gateway da NAT.

Adicionaremos um endereço IP público e um prefixo IP público a esse cenário para demonstrar.

### <a name="create-a-public-ip-address"></a>Criar um endereço IP público

Para acessar a Internet, você precisa de um ou mais endereços IP públicos para o gateway da NAT. Use [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para criar um recurso de endereço IP público chamado **myPublicIP** em **myResourceGroupNAT**. O resultado desse comando será armazenado em uma variável **$publicIP** para uso posterior.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pbnm = 'myPublicIP'
  
$publicIP = 
New-AzPublicIpAddress -Name $pbnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Criar um prefixo IP público

Use [New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix) para criar um recurso de prefixo IP público chamado **myPublicIPprefix** em **myResourceGroupNAT**.  O resultado desse comando será armazenado em uma variável chamada **$publicIPPrefix** para uso posterior.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pxnm = 'myPublicIPprefix'

$publicIPPrefix = 
New-AzPublicIpPrefix -Name $pxnm -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Criar um recurso de gateway da NAT

Esta seção fornece detalhes sobre como criar e configurar os seguintes componentes do serviço NAT usando o recurso de gateway da NAT:
  - Um pool de IPs públicos e o prefixo IP público a serem usados para fluxos de saída convertidos pelo recurso de Gateway da NAT.
  - Altere o tempo limite de ociosidade do padrão de 4 minutos para 10 minutos.

Crie um gateway da NAT global do Azure com [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway). O resultado desse comando criará um recurso de gateway chamado **myNATgateway** que usa o endereço IP público **myPublicIP** e o prefixo IP público **myPublicIPprefix**. O tempo limite de ociosidade está definido como 10 minutos.  O resultado desse comando será armazenado em uma variável chamada **$natGateway** para uso posterior.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$gnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $gnm -ResourceGroupName $rsg -PublicIpAddress $publicIP -PublicIpPrefix $publicIPPrefix -Location $loc -Sku $sku -IdleTimeoutInMinutes 10
  ```

Neste momento, o gateway da NAT está funcional e tudo o que está faltando é configurar quais sub-redes de uma rede virtual devem usá-lo.

## <a name="configure-virtual-network"></a>Configurar rede virtual

Crie a rede virtual e associe a sub-rede ao gateway.

Crie uma rede virtual chamada **myVnet** com uma sub-rede chamada **mySubnet** usando [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) no **myResourceGroup** usando [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O espaço de endereços IP da rede virtual é **192.168.0.0/16**. A sub-rede dentro da rede virtual é **192.168.0.0/24**.  O resultado dos comandos serão armazenados em variáveis chamadas **$subnet** e **$vnet** para uso posterior.

```azurepowershell-interactive
$sbnm = 'mySubnet'
$vnnm = 'myVnet'
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pfxsub = '192.168.0.0/24'
$pfxvn = '192.168.0.0/16'

$subnet = 
New-AzVirtualNetworkSubnetConfig -Name $sbnm -AddressPrefix $pfxsub -NatGateway $natGateway

$vnet = 
New-AzVirtualNetwork -Name $vnnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $pfxvn -Subnet $subnet
```

Todo o tráfego de saída para os destinos da Internet agora usa o serviço NAT.  Não é necessário configurar uma UDR.

## <a name="create-a-vm-to-use-the-nat-service"></a>Criar uma VM para usar o serviço NAT

Agora criaremos uma VM para usar o serviço NAT.  Essa VM tem um IP público para ser usado como um IP público em nível de instância para que você possa acessar a VM.  O serviço NAT reconhece a direção do fluxo e substituirá o destino da Internet padrão em sua sub-rede. O endereço IP público da VM não será usado para conexões de saída.

### <a name="create-public-ip-for-source-vm"></a>Criar IP público para a VM de origem

Criamos um IP público para ser usado para acessar a VM.  Use [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para criar um recurso de endereço IP público chamado **myPublicIPVM** em **myResourceGroupNAT**.  O resultado desse comando será armazenado em uma variável chamada **$publicIpVM** para uso posterior.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$ipnm = 'myPublicIPVM'
$sku = 'Standard'

$publicIpVM = 
New-AzPublicIpAddress -Name $ipnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Criar um NSG e expor um ponto de extremidade SSH para a VM

Os endereços IP públicos Standard são "seguros por padrão". Precisamos criar um NSG para permitir o acesso de entrada para SSH. Use [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) para criar um recurso NSG chamado **myNSG**. Use [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) para criar uma regra NSG para acesso SSH chamada **ssh** em **myResourceGroupNAT**.  O resultado desse comando será armazenado em uma variável chamada **$nsg** para uso posterior.

```azurepowershell-interactive
$rnm = 'ssh'
$rdesc = 'SSH access'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '100'
$prt = '22'
$rsg = 'myResourceGroupNAT'
$rnm = 'myNSG'
$loc = 'eastus2'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdesc -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange $prt

$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $rnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-vm"></a>Criar NIC para a VM

Crie um adaptador de rede com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) chamado **myNic**. Esse comando associa o endereço IP Público e o grupo de segurança de rede. O resultado desse comando será armazenado em uma variável chamada **$nic** para uso posterior.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myNic'
$loc = 'eastus2'

$nic = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nmn -NetworkSecurityGroupID $nsg.Id -PublicIPAddressID $publicIPVM.Id -SubnetID $vnet.Subnets[0].Id -Location $loc
```

### <a name="create-vm"></a>Criar VM

#### <a name="create-ssh-key-pair"></a>Criar o par de chaves SSH

Você precisa de um par de chaves SSH para concluir este início rápido. Se você já tiver um par de chaves SSH, você pode ignorar esta etapa.

Use o ssh-keygen para criar um par de chaves SSH.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Para obter mais informações sobre como criar pares de chave SSH, incluindo o uso de PuTTy, consulte [Como usar chaves SSH com o Windows](../virtual-machines/linux/ssh-from-windows.md).

Se você criar o par de chaves SSH usando o Cloud Shell, esse par será armazenado em uma imagem de contêiner. Essa [conta de armazenamento é criada automaticamente](../cloud-shell/persisting-shell-storage.md). Não exclua a conta de armazenamento ou o compartilhamento de arquivo dentro dela até que você tenha recuperado suas chaves.

#### <a name="create-vm-configuration"></a>Criar a Configuração da VM

Para criar uma VM no PowerShell, você deve criar uma configuração que tem configurações para as opções imagem a ser usada, tamanho e autenticação. A configuração é usada para criar a VM.

Defina as credenciais do SSH, as informações do sistema operacional e o tamanho de VM. Neste exemplo, a chave SSH é armazenada em ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
#Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force

$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vnm = 'myVM'
$vsz = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$sku = '18.04-LTS'
$ver = 'latest'

$vmConfig = 
New-AzVMConfig -VMName $vnm -VMSize $vsz

Set-AzVMOperatingSystem -VM $vmConfig -Linux -ComputerName $vnm -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfig -PublisherName $pub -Offer $off -Skus $sku -Version $ver

Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Combine as definições de configuração para criar uma VM chamada **myVM** com [New-AzVM](/powershell/module/az.compute/new-azvm) em **myResourceGroupNAT**.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmconfig
```

Aguarde até que a VM se prepare para ser implantada e continue com o restante das etapas.

## <a name="discover-the-ip-address-of-the-vm"></a>Descobrir o endereço IP da VM

Primeiro precisamos descobrir o endereço IP da VM criada. Para obter o endereço IP público da VM, use o [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myPublicIPVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $nmn | select IpAddress
``` 

>[!IMPORTANT]
>Copie o endereço IP público e cole-o em um bloco de notas para poder usá-lo a fim de acessar a VM.

### <a name="sign-in-to-vm"></a>Entrar na VM

As credenciais do SSH devem ser armazenadas no Cloud Shell da operação anterior.  Abra um [Azure Cloud Shell](https://shell.azure.com) no navegador. Use o endereço IP recuperado na etapa anterior para se conectar por SSH à máquina virtual.

```bash
ssh azureuser@<ip-address-destination>
```

Agora você está pronto para usar o serviço NAT.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, você poderá usar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos contidos nele.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um gateway da NAT e uma VM para usá-lo. 

Examine as métricas no Azure Monitor para verificar a operação do serviço NAT. Diagnostique problemas como o esgotamento de recursos das portas SNAT disponíveis.  A exaustão de recursos de portas SNAT é resolvida adicionando mais recursos de endereço IP público, de prefixo IP público ou ambos.


- Saiba mais sobre a [NAT de Rede Virtual do Azure](./nat-overview.md)
- Saiba mais sobre o [recurso de gateway da NAT](./nat-gateway-resource.md).
- Início Rápido para implantar o [recurso de gateway da NAT usando a CLI do Azure](./quickstart-create-nat-gateway-cli.md).
- Início Rápido para implantar o [recurso de gateway da NAT usando o Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Início Rápido para implantar o [recurso de gateway da NAT usando o portal do Azure](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]
