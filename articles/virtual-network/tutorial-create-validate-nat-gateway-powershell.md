---
title: Criar e testar um gateway da NAT – Azure PowerShell
titlesuffix: Azure Virtual Network NAT
description: Este tutorial mostra como criar um gateway da NAT usando o Azure PowerShell e testar o serviço NAT
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2020
ms.author: allensu
ms.openlocfilehash: f319e77bd4801a2844a54ba8eba955c2b062fe7e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98934302"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell-and-test-the-nat-service"></a>Tutorial: Criar um gateway da NAT usando o Azure PowerShell e testar o serviço NAT

Neste tutorial, você criará um gateway da NAT para fornecer conectividade de saída para máquinas virtuais no Azure. Para testar o gateway da NAT, implante uma máquina virtual de origem e de destino. Você testará o gateway da NAT realizando conexões de saída com um endereço IP público. Essas conexões serão provenientes da máquina virtual de origem para a de destino. Este tutorial implanta a origem e o destino em duas redes virtuais diferentes no mesmo grupo de recursos para simplificar.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Você pode concluir este tutorial usando o Azure Cloud Shell ou executar os respectivos comandos localmente.  Caso nunca tenha usado o Azure Cloud Shell, [entre agora](https://shell.azure.com).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O seguinte exemplo cria um grupo de recursos chamado **myResourceGroupNAT** na localização **eastus2**:


```azurepowershell-interactive
$rgname = 'myResourceGroupNAT'
$loc = 'eastus2'

$rg = New-AzResourceGroup -Name $rgname -Location $loc

```

## <a name="create-the-nat-gateway"></a>Criar o gateway da NAT

### <a name="create-a-public-ip-address"></a>Criar um endereço IP público

Para acessar a Internet, você precisa de um ou mais endereços IP públicos para o gateway da NAT. Use [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para criar um recurso de endereço IP público chamado **myPublicIPsource** em **myResourceGroupNAT**. O resultado desse comando será armazenado em uma variável chamada **$publicIPsource** para uso posterior.

```azurepowershell-interactive
$pipname = 'myPublicIPsource'
$alm = 'Static'
$sku = 'Standard'

$publicIPsource = 
New-AzPublicIpAddress -Name $pipname -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $alm -Sku $sku -Location $rg.Location

```

### <a name="create-a-public-ip-prefix"></a>Criar um prefixo IP público

 Use [New-AzPublicIpPrefix](/powershell/module/az.network/new-azpublicipprefix) para criar um recurso de prefixo IP público chamado **myPublicIPprefixsource** em **myResourceGroupNAT**.  O resultado desse comando será armazenado em uma variável chamada **$publicIPPrefixsource** para uso posterior.

```azurepowershell-interactive
$prefixname = 'mypublicIPprefixsource'

$publicIPPrefixsource = 
New-AzPublicIpPrefix -Name $prefixname -ResourceGroupName $rg.ResourceGroupName -PrefixLength 31 -Location $rg.Location

```

### <a name="create-a-nat-gateway-resource"></a>Criar um recurso de gateway da NAT

Esta seção fornece detalhes sobre como criar e configurar os seguintes componentes do serviço NAT usando o recurso de gateway da NAT:
  - Um pool de IPs públicos e o prefixo IP público a serem usados para fluxos de saída convertidos pelo recurso de Gateway da NAT.
  - Altere o tempo limite de ociosidade do padrão de 4 minutos para 10 minutos.

Crie um gateway da NAT global do Azure com [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway). O resultado desse comando criará um recurso de gateway chamado **myNATgateway** que usa o endereço IP público **myPublicIPsource** e o prefixo IP público **myPublicIPprefixsource**. O tempo limite de ociosidade está definido como 10 minutos.  O resultado desse comando será armazenado em uma variável chamada **$natGateway** para uso posterior.

```azurepowershell-interactive
$sku = 'Standard'
$natname = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $natname -ResourceGroupName $rg.ResourceGroupName -PublicIpAddress $publicIPsource -PublicIpPrefix $publicIPPrefixsource -Sku $sku -IdleTimeoutInMinutes 10 -Location $rg.Location

  ```

Neste momento, o gateway da NAT está funcional e tudo o que está faltando é configurar quais sub-redes de uma rede virtual devem usá-lo.

## <a name="prepare-the-source-for-outbound-traffic"></a>Preparar a origem para o tráfego de saída

Orientaremos você pela instalação de um ambiente de teste completo. Você configurará um teste usando ferramentas de software livre para verificar o gateway da NAT. Começaremos com a origem, que usará o gateway da NAT criado anteriormente.

### <a name="configure-virtual-network-for-source"></a>Configurar a rede virtual para a origem

Crie a rede virtual e associe a sub-rede ao gateway.

Crie uma rede virtual chamada **myVnetsource** com uma sub-rede chamada **mySubnetsource** usando [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) no **myResourceGroupNAT** usando [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O espaço de endereços IP da rede virtual é **192.168.0.0/16**. A sub-rede dentro da rede virtual é **192.168.0.0/24**.  O resultado dos comandos serão armazenados em variáveis chamadas **$subnetsource** e **$vnetsource** para uso posterior.

```azurepowershell-interactive
$subnetname = 'mySubnetsource'
$subnetprefix = '192.168.0.0/24'
$vnetname = 'myVnetsource'
$vnetprefix = '192.168.0.0/16'

$subnetsource = 
New-AzVirtualNetworkSubnetConfig -Name $subnetname -AddressPrefix $subnetprefix -NatGateway $natGateway

$vnetsource = 
New-AzVirtualNetwork -Name $vnetname -ResourceGroupName $rg.ResourceGroupName -AddressPrefix $vnetprefix -Subnet $subnetsource -Location $rg.Location

```

Todo o tráfego de saída para os destinos da Internet agora usa o serviço NAT.  Não é necessário configurar uma UDR.

Antes de podermos testar o gateway da NAT, precisamos criar uma VM de origem.  Atribuiremos um recurso de endereço IP público como um IP Público em nível de instância para acessar essa VM de fora. Esse endereço é usado somente para acessá-la para o teste.  Demonstraremos como o serviço NAT tem precedência sobre outras opções de saída.

Você também pode criar essa VM sem um IP público e criar outra VM para usá-la como um jumpbox sem um IP público como um exercício.

### <a name="create-public-ip-for-source-vm"></a>Criar IP público para a VM de origem

Criamos um IP público para ser usado para acessar a VM.  Use [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para criar um recurso de endereço IP público chamado **myPublicIPVM** em **myResourceGroupNAT**.  O resultado desse comando será armazenado em uma variável chamada **$publicIpsourceVM** para uso posterior.

```azurepowershell-interactive
$sku = 'Standard'
$pipvmname = 'myPublicIpsourceVM'
$alm = 'Static'

$publicIpsourceVM = 
New-AzPublicIpAddress -Name $pipvmname -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $alm -sku $sku -Location $rg.Location

```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Criar um NSG e expor um ponto de extremidade SSH para a VM

Como os endereços IP Públicos Standard são "seguros por padrão", criamos um NSG para permitir o acesso de entrada para SSH. O serviço NAT reconhece a direção do fluxo. Este NSG não será usado para saída depois que o gateway da NAT estiver configurado na mesma sub-rede. Use [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) para criar um recurso NSG chamado **myNSGsource**. Use [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) para criar uma regra NSG para acesso SSH chamada **ssh** em **myResourceGroupNAT**. O resultado desse comando será armazenado na variável chamada **$nsgsource** para uso posterior.

```azurepowershell-interactive
$rnm = 'ssh'
$rdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$nsnm = 'myNSGsource'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$nsgsource = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg.ResourceGroupName -Name $nsnm -SecurityRules $sshrule -Location $rg.Location

```

### <a name="create-nic-for-source-vm"></a>Criar NIC para a VM de origem

Crie um adaptador de rede com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) chamado **myNicsource**. Esse comando associará o endereço IP Público e o grupo de segurança de rede. O resultado desse comando será armazenado em uma variável chamada **$nicsource** para uso posterior.

```azurepowershell-interactive
$nin = 'myNicsource'

$nicsource = 
New-AzNetworkInterface -ResourceGroupName $rg.ResourceGroupName -Name $nin -NetworkSecurityGroupID $nsgsource.Id -PublicIPAddressID $publicIPVMsource.Id -SubnetID $vnetsource.Subnets[0].Id -Location $rg.Location

```

### <a name="create-a-source-vm"></a>Criar uma VM de origem

#### <a name="create-ssh-key-pair"></a>Criar o par de chaves SSH

Você precisa de um par de chaves SSH para concluir este início rápido. Se você já tiver um par de chaves SSH, você pode ignorar esta etapa.

Use o ssh-keygen para criar um par de chaves SSH.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048

```
Para obter mais informações sobre como criar pares de chave SSH, incluindo o uso de PuTTy, consulte [Como usar chaves SSH com o Windows](../virtual-machines/linux/ssh-from-windows.md).

Se você criar o par de chaves SSH usando o Cloud Shell, esse par será armazenado em uma imagem de contêiner. Essa [conta de armazenamento é criada automaticamente](../cloud-shell/persisting-shell-storage.md). Não exclua a conta de armazenamento ou o compartilhamento de arquivo dentro dela até que você tenha recuperado suas chaves.

#### <a name="create-vm-configuration"></a>Criar a Configuração da VM

Para criar uma VM no PowerShell, você deve criar uma configuração que tem configurações como a imagem para opções de autenticação, tamanho e uso. Em seguida, a configuração é usada para compilar a VM.

Defina as credenciais do SSH, as informações do sistema operacional e o tamanho de VM. Neste exemplo, a chave SSH é armazenada em ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmn = 'myVMsource'
$vms = 'Standard_DS1_v2'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigsource = 
New-AzVMConfig -VMName $vmn -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigsource -Linux -ComputerName $vmn -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigsource -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigsource -Id $nicsource.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigsource -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Combine as definições de configuração para criar uma VM chamada **myVMsource** com [New-AzVM](/powershell/module/az.compute/new-azvm) em **myResourceGroupNAT**.

```azurepowershell-interactive
New-AzVM -ResourceGroupName $rg.ResourceGroupName -VM $vmConfigsource -Location $rg.Location

```

Embora o comando retorne imediatamente, pode levar alguns minutos para a VM ser implantada.

## <a name="prepare-destination-for-outbound-traffic"></a>Preparar destino para o tráfego de saída

Agora criaremos um destino para o tráfego de saída convertido pelo serviço NAT para permitir que você o teste.

### <a name="configure-virtual-network-for-destination"></a>Configurar a rede virtual para o destino

Precisamos criar uma rede virtual na qual a máquina virtual de destino estará.  Esses comandos são as mesmas etapas para a VM de origem. Pequenas alterações foram adicionadas para expor o ponto de extremidade de destino.

Crie uma rede virtual chamada **myVnetdestination** com uma sub-rede chamada **mySubnetdestination** usando [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) no **myResourceGroupNAT** usando [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O espaço de endereços IP da rede virtual é **192.168.0.0/16**. A sub-rede dentro da rede virtual é **192.168.0.0/24**.  O resultado dos comandos serão armazenados em variáveis chamadas **$subnetdestination** e **$vnetdestination** para uso posterior.

```azurepowershell-interactive
$sbdn = 'mySubnetdestination'
$spfx = '192.168.0.0/24'
$vdn = 'myVnetdestination'
$vpfx = '192.168.0.0/16'

$subnetdestination = 
New-AzVirtualNetworkSubnetConfig -Name $sbdn -AddressPrefix $spfx

$vnetdestination = 
New-AzVirtualNetwork -Name $vdn -ResourceGroupName $rg.ResourceGroupName -AddressPrefix $vpfx -Subnet $subnetdestination -Location $rg.Location

```

### <a name="create-public-ip-for-destination-vm"></a>Criar IP público para a VM de destino

Criamos um IP público a ser usado para acessar a VM de destino.  Use [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para criar um recurso de endereço IP público chamado **myPublicIPdestinationVM** em **myResourceGroupNAT**.  O resultado desse comando será armazenado em uma variável chamada **$publicIpdestinationVM** para uso posterior.

```azurepowershell-interactive
$sku = 'Standard'
$all = 'Static'
$pipd = 'myPublicIPdestinationVM'

$publicIpdestinationVM = 
New-AzPublicIpAddress -Name $pipd -ResourceGroupName $rg.ResourceGroupName -AllocationMethod $all -Sku $sku -Location $rg.Location

```

### <a name="create-an-nsg-and-expose-ssh-and-http-endpoint-for-vm"></a>Criar um NSG e expor um ponto de extremidade SSH e HTTP para a VM

Os endereços IP Públicos Standard são "seguros por padrão". Criamos um NSG para permitir o acesso de entrada para SSH. Use [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) para criar um recurso NSG chamado **myNSGdestination**. Use [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) para criar uma regra NSG para acesso SSH chamada **ssh**.  Use [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) para criar uma regra NSG para acesso HTTP chamada **http**. Ambas as regras serão criadas em **myResourceGroupNAT**. O resultado desse comando será armazenado em uma variável chamada **$nsgdestination** para uso posterior.

```azurepowershell-interactive
$snm = 'ssh'
$sdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$hnm = 'http'
$hdsc = 'HTTP access'
$nsnm = 'myNSGdestination'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $snm -Description $sdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$httprule = 
New-AzNetworkSecurityRuleConfig -Name $hnm -Description $hdsc -Access $acc -Protocol $prt -Direction $dir -Priority 101 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$nsgdestination = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg.ResourceGroupName -Name $nsnm -SecurityRules $sshrule,$httprule -Location $rg.Location

```

### <a name="create-nic-for-destination-vm"></a>Criar NIC para a VM de destino

Crie um adaptador de rede com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) chamado **myNicdestination**. Esse comando será associado ao endereço IP Público e o grupo de segurança de rede. O resultado desse comando será armazenado em uma variável chamada **$nicdestination** para uso posterior.

```azurepowershell-interactive
$nnm = 'myNicdestination'

$nicdestination = 
New-AzNetworkInterface -ResourceGroupName $rg.ResourceGroupName -Name $nnm -NetworkSecurityGroupID $nsgdestination.Id -PublicIPAddressID $publicIPdestinationVM.Id -SubnetID $vnetdestination.Subnets[0].Id -Location $rg.Location

```

### <a name="create-a-destination-vm"></a>Criar uma VM de destino

#### <a name="create-vm-configuration"></a>Criar a Configuração da VM

Para criar uma VM no PowerShell, você deve criar uma configuração que tem configurações como a imagem para opções de autenticação, tamanho e uso. Em seguida, a configuração é usada para compilar a VM.

Defina as credenciais do SSH, as informações do sistema operacional e o tamanho de VM. Neste exemplo, a chave SSH é armazenada em ~/.ssh/id_rsa.pub.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vmd = 'myVMdestination'
$vms = 'Standard_DS1_v2'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigdestination = New-AzVMConfig -VMName $vmd -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigdestination -Linux -ComputerName $vmd -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigdestination -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigdestination -Id $nicdestination.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigdestination -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Combine as definições de configuração para criar uma VM chamada **myVMdestination** com [New-AzVM](/powershell/module/az.compute/new-azvm) em **myResourceGroupNAT**.

```azurepowershell-interactive

New-AzVM -ResourceGroupName $rg.ResourceGroupName -VM $vmConfigdestination -Location $rg.Location

```

Embora o comando retorne imediatamente, pode levar alguns minutos para a VM ser implantada.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Preparar um servidor Web e testar o conteúdo na VM de destino

Primeiro precisamos descobrir o endereço IP da VM de destino.  Para obter o endereço IP público da VM, use o [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress). 

```azurepowershell-interactive
$pipname = 'myPublicIPdestinationVM'
  
$destip = Get-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -Name $pipname | select IpAddress

$destip

``` 

>[!IMPORTANT]
>Copie o endereço IP público e cole-o em um bloco de notas para usá-lo nas etapas seguintes. Indique isso como a máquina virtual de destino.

### <a name="sign-in-to-destination-vm"></a>Entrar na VM de destino

As credenciais do SSH devem ser armazenadas no Cloud Shell da operação anterior.  Abra um [Azure Cloud Shell](https://shell.azure.com) no navegador. Use o endereço IP recuperado na etapa anterior para se conectar por SSH à máquina virtual. 

```azurepowershell-interactive
ssh azureuser@$destip

```

Copie e cole os comandos a seguir depois de fazer logon.  

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100

```

Esses comandos atualizarão sua máquina virtual, instalarão o nginx e criarão um arquivo de 100 KB. Esse arquivo será recuperado da VM de origem usando o serviço NAT.

Feche a sessão SSH com a VM de destino.

## <a name="prepare-test-on-source-vm"></a>Preparar o teste na VM de origem

Primeiro precisamos descobrir o endereço IP da VM de origem.  Para obter o endereço IP público da VM, use o [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress). 

```azurepowershell-interactive
$pipname = 'myPublicIPsourceVM'

$srcip = Get-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -Name $pipname | select IpAddress

$srcip

``` 

>[!IMPORTANT]
>Copie o endereço IP público e cole-o em um bloco de notas para usá-lo nas etapas seguintes. Indique isso como a máquina virtual de origem.

### <a name="log-into-source-vm"></a>Fazer logon na VM de origem

Novamente, as credenciais SSH são armazenadas no Cloud Shell. Abra uma nova guia para o [Azure Cloud Shell](https://shell.azure.com) no navegador.  Use o endereço IP recuperado na etapa anterior para se conectar por SSH à máquina virtual. 

```azurepowershell-interactive
ssh azureuser@$srcip

```

Copie e cole os comandos a seguir para se preparar para testar o serviço NAT.

```bash
sudo apt -y update && \
sudo apt -y upgrade && \
sudo apt install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Esses comandos atualizarão sua máquina virtual, instalarão o go, instalarão o [hey](https://github.com/rakyll/hey) do GitHub e atualizarão seu ambiente shell.

Agora você está pronto para testar o serviço NAT.

## <a name="validate-nat-service"></a>Validar o serviço NAT

Enquanto estiver conectado à VM de origem, você poderá usar o **curl** e o **hey** para gerar solicitações para o endereço IP de destino.

Use curl para recuperar o arquivo de 100 KB.  Substitua **\<ip-address-destination>** no exemplo abaixo pelo endereço IP de destino copiado anteriormente.  O parâmetro **--output** indica que o arquivo recuperado será descartado.

```bash
curl http://<ip-address-destination>/100k --output /dev/null

```

Você também pode gerar uma série de solicitações usando o **hey**. Novamente, substitua **\<ip-address-destination>** pelo endereço IP de destino que você copiou anteriormente.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k

```

Esse comando gerará 100 solicitações, 10 simultaneamente, com um tempo limite de 30 segundos. A conexão TCP não será reutilizada.  Cada solicitação recuperará 100 KB.  No fim da execução, o **hey** relatará algumas estatísticas sobre o desempenho do serviço NAT.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, você poderá usar o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos contidos nele.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name $rg.ResourceGroupName

```

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você criou um gateway da NAT, criou uma VM de origem e destino e, em seguida, testou o gateway da NAT.

Examine as métricas no Azure Monitor para verificar a operação do serviço NAT. Diagnostique problemas como o esgotamento de recursos das portas SNAT disponíveis.  O esgotamento de recursos das portas SNAT é resolvido com facilidade adicionando mais recursos de endereço IP público, recursos de prefixo IP público ou ambos.

- Saiba mais sobre a [NAT de Rede Virtual](./nat-overview.md)
- Saiba mais sobre o [recurso de gateway da NAT](./nat-gateway-resource.md).
- Início Rápido para implantar o [recurso de gateway da NAT usando a CLI do Azure](./quickstart-create-nat-gateway-cli.md).
- Início Rápido para implantar o [recurso de gateway da NAT usando o Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Início Rápido para implantar o [recurso de gateway da NAT usando o portal do Azure](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]
