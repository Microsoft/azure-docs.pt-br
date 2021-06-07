---
title: Filtrar tráfego - Microsoft Azure PowerShell | Microsoft Docs
description: Neste artigo, você aprenderá como filtrar o tráfego para uma sub-rede, com um grupo de segurança de rede, usando o PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 8ab22e7960e233d6ae934fb52989aa73a494b33a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791503"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-powershell"></a>Filtrar tráfego com um grupo de segurança de rede utilizando o PowerShell

É possível filtrar o tráfego para entrada e saída de uma sub-rede de rede virtual com um grupo de segurança de rede. Grupos de segurança de rede contêm regras de segurança que filtram o tráfego por endereço IP, porta e protocolo. As regras de segurança são aplicadas aos recursos implantados em uma sub-rede. Neste artigo, você aprenderá como:

* Criar um grupo de segurança de rede e regras de segurança
* Criar uma rede virtual e associar um grupo de segurança de rede a uma sub-rede
* Implantar VMs (máquinas virtuais) em uma sub-rede
* Testar filtros de tráfego

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o módulo do Azure PowerShell versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Um grupo de segurança de rede contém regras de segurança. As regras de segurança especificam uma origem e um destino. Origens e destinos podem ser grupos de segurança de aplicativos.

### <a name="create-application-security-groups"></a>Criar grupos de segurança de aplicativos

Primeiro, crie um grupo de recursos para todos os recursos criados neste artigo com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos no local *eastus*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Crie um grupo de segurança de aplicativo com [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup). Um grupo de segurança de aplicativos permite agrupar servidores com requisitos de filtragem de porta semelhantes. O exemplo a seguir cria dois grupos de segurança de aplicativos.

```azurepowershell-interactive
$webAsg = New-AzApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgWebServers `
  -Location eastus

$mgmtAsg = New-AzApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgMgmtServers `
  -Location eastus
```

### <a name="create-security-rules"></a>Criar regras de segurança

Crie uma regra de segurança com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). O exemplo a seguir cria uma regra que permite a entrada de tráfego da Internet para o grupo de segurança de aplicativo *myWebServers* pelas portas 80 e 443:

```azurepowershell-interactive
$webRule = New-AzNetworkSecurityRuleConfig `
  -Name "Allow-Web-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $webAsg.id `
  -DestinationPortRange 80,443

The following example creates a rule that allows traffic inbound from the internet to the *myMgmtServers* application security group over port 3389:

$mgmtRule = New-AzNetworkSecurityRuleConfig `
  -Name "Allow-RDP-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 110 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $mgmtAsg.id `
  -DestinationPortRange 3389
```

Neste artigo, o RDP (porta 3389) é exposto à Internet para a VM *myAsgMgmtServers*. Para ambientes de produção, em vez de expor a porta 3389 à Internet, é recomendável que você se conecte aos recursos do Azure que deseja gerenciar usando uma conexão [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou de rede [privada](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Crie um grupo de segurança de rede com [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). O exemplo a seguir cria um grupo de segurança de rede nomeado *myNsg*:

```powershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo a seguir cria uma virtual nomeada *myVirtualNetwork*:

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Crie uma configuração de sub-rede com [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)e, em seguida, grave a configuração de sub-rede na rede virtual com [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork). O exemplo a seguir adiciona uma sub-rede nomeada *mySubnet* à rede virtual e associa o grupo de segurança de rede *myNsg* a ela:

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Antes de criar as VMs, recupere o objeto de rede virtual com a sub-rede com [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork):

```powershell-interactive
$virtualNetwork = Get-AzVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```

Crie um endereço IP público para cada VM com [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress):

```powershell-interactive
$publicIpWeb = New-AzPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmWeb

$publicIpMgmt = New-AzPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmMgmt
```

Crie duas interfaces de rede com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)e atribua um endereço IP público à interface de rede. O exemplo a seguir cria um adaptador de rede, associa o endereço IP público *myVmWeb* a ele e o transforma em um membro do grupo de segurança de aplicativo *myAsgWebServers*:

```powershell-interactive
$webNic = New-AzNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

O exemplo a seguir cria um adaptador de rede, associa o endereço IP público *myVmMgmt* a ele e o transforma em um membro do grupo de segurança de aplicativo *myAsgMgmtServers*:

```powershell-interactive
$mgmtNic = New-AzNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

Crie duas VMs na rede virtual para poder validar a filtragem de tráfego em uma etapa posterior.

Crie uma configuração de VM com [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)e crie a VM com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo a seguir cria uma VM que servirá como um servidor Web. A opção `-AsJob` cria a VM em segundo plano, para que você possa prosseguir para a próxima etapa:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$webVmConfig = New-AzVMConfig `
  -VMName myVmWeb `
  -VMSize Standard_DS1_V2 | `
Set-AzVMOperatingSystem -Windows `
  -ComputerName myVmWeb `
  -Credential $cred | `
Set-AzVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzVMNetworkInterface `
  -Id $webNic.Id
New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $webVmConfig `
  -AsJob
```

Crie uma VM para servir como um servidor de gerenciamento:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create the web server virtual machine configuration and virtual machine.
$mgmtVmConfig = New-AzVMConfig `
  -VMName myVmMgmt `
  -VMSize Standard_DS1_V2 | `
Set-AzVMOperatingSystem -Windows `
  -ComputerName myVmMgmt `
  -Credential $cred | `
Set-AzVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzVMNetworkInterface `
  -Id $mgmtNic.Id
New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $mgmtVmConfig
```

A criação da máquina virtual demora alguns minutos. Não prossiga para a próxima etapa até que o Azure tenha concluído a criação da VM.

## <a name="test-traffic-filters"></a>Testar filtros de tráfego

Use [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para retornar o endereço IP público de uma VM. O exemplo a seguir retorna o endereço IP público da VM *myVmMgmt*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Use o comando a seguir para criar uma sessão de logon remota com a VM *myVmMgmt* do computador local. Substitua `<publicIpAddress>` pelo o endereço IP retornado do comando anterior.

```
mstsc /v:<publicIpAddress>
```

Abra o arquivo RDP baixado. Se solicitado, selecione **Conectar**.

Insira o nome de usuário e senha especificados ao criar a VM (talvez seja necessário selecionar **Mais escolhas**, em seguida, **Usar uma conta diferente**, para especificar as credenciais inseridas ao criar a VM) e selecione **OK**. Você pode receber um aviso do certificado durante o processo de logon. Selecione **Sim** para prosseguir com a conexão.

É uma conexão com êxito porque a porta 3389 tem permissão de entrada da Internet para o grupo de segurança de aplicativo *myAsgMgmtServers* ao qual o adaptador de rede conectado à VM *myVmMgmt* está.

Use o comando a seguir para criar uma conexão de área de trabalho remota para a VM *myVmWeb*, a partir da VM *myVmMgmt* com o seguinte comando, a partir do PowerShell:

``` 
mstsc /v:myvmWeb
```

É uma conexão com êxito porque uma regra de segurança padrão dentro de cada grupo de segurança de rede permite o tráfego em todas as portas entre todos os endereços IP em uma rede virtual. Não é possível criar uma conexão de área de trabalho remota para a VM *myVmWeb* da Internet porque a regra de segurança para *myAsgWebServers* não permite a entrada da porta 3389 da Internet.

Use o comando a seguir para instalar os Serviços de Informações da Internet da Microsoft na VM *myVmWeb* a partir do PowerShell:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Após a conclusão da instalação dos Serviços de Informações da Internet, desconecte-se da VM *myVmWeb*, que direciona para a conexão da área de trabalho remota da VM *myVmMgmt*. Para exibir a tela de boas-vindas do IIS, abra um navegador da Internet e navegue até http: \/ /myVmWeb.

Desconecte da VM *myVmMgmt* .

No seu computador, insira o comando a seguir do PowerShell para recuperar o endereço IP público do servidor *myVmWeb*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Para confirmar se é possível acessar o servidor Web *myVmWeb* de fora do Azure, abra um navegador da Internet no seu computador e navegue até `http://<public-ip-address-from-previous-step>`. É uma conexão com êxito porque a porta 80 tem permissão de entrada da Internet para o grupo de segurança de aplicativo *myAsgWebServers* ao qual o adaptador de rede conectado à VM *myVmWeb* está.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você poderá usar [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um grupo de segurança de rede e o associou a uma sub-rede de rede virtual. Para saber mais sobre grupos de segurança de rede, consulte [Visão geral do grupo de segurança de rede](./network-security-groups-overview.md) e [Gerenciar um grupo de segurança de rede](manage-network-security-group.md).

O Azure roteia o tráfego entre sub-redes por padrão. Em vez disso, é possível escolher rotear o tráfego entre sub-redes por meio de uma VM, servindo como um firewall, por exemplo. Para saber mais, consulte [Criar uma tabela de roteamentos](tutorial-create-route-table-powershell.md).