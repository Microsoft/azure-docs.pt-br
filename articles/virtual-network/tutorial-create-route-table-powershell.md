---
title: Rotear tráfego de rede - Azure PowerShell | Microsoft Docs
description: Neste artigo, saiba como rotear tráfego com uma tabela de rotas utilizando O PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5bd52e8865bb704497740851f6a0e3c886ed9d6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790143"
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Rotear tráfego com uma tabela de rotas utilizando o PowerShell

Por padrão, o Azure roteia automaticamente o tráfego entre todas as sub-redes dentro de uma rede virtual. É possível criar as próprias rotas para substituir o roteamento padrão do Azure. A capacidade de criar rotas personalizadas será útil, por exemplo, se você quiser rotear o tráfego entre sub-redes por meio de uma NVA (solução de virtualização de rede). Neste artigo, você aprenderá como:

* Criar uma tabela de rotas
* Criar uma rota
* Criar uma rede virtual com várias sub-redes
* Associar uma tabela de rotas a uma sub-rede
* Criar uma NVA que roteia o tráfego
* Implantar VMs (máquinas virtuais) em diferentes sub-redes
* Rotear o tráfego de uma sub-rede para outra por meio de uma NVA

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o módulo do Azure PowerShell versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

Antes de criar uma tabela de rotas, crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* para todos os recursos criados neste artigo.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Crie uma tabela de rotas com [New-AzRouteTable](/powershell/module/az.network/new-azroutetable). O exemplo a seguir cria uma tabela de rotas nomeada *myRouteTablePublic*.

```azurepowershell-interactive
$routeTablePublic = New-AzRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Criar uma rota

Crie uma rota recuperando o objeto de tabela de rotas com [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable), crie uma rota com [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig)e, em seguida, grave a configuração de rota na tabela de rotas com [set-AzRouteTable](/powershell/module/az.network/set-azroutetable).

```azurepowershell-interactive
Get-AzRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzRouteTable
```

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

Antes de poder associar uma tabela de rotas a uma sub-rede, será necessário criar uma rede virtual e uma sub-rede. Crie uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo a seguir cria uma rede virtual nomeada *myVirtualNetwork* com o prefixo de endereço *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Crie três sub-redes criando três configurações de sub-rede com [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). O exemplo a seguir cria três configurações de sub-rede para sub-redes *Pública*, *Privada* e *DMZ*:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

Grave as configurações de sub-rede na rede virtual com [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork), que cria as sub-redes na rede virtual:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

Associe a tabela de rotas *myRouteTablePublic* à sub-rede *pública* com [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) e, em seguida, grave a configuração de sub-rede na rede virtual com [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork).

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $myRouteTablePublic | `
Set-AzVirtualNetwork
```

## <a name="create-an-nva"></a>Criar uma NVA

Uma NVA é uma VM que executa uma função de rede, como roteamento, firewall ou otimização de WAN.

Antes de criar uma VM, crie um adaptador de rede.

### <a name="create-a-network-interface"></a>Criar um adaptador de rede

Antes de criar uma interface de rede, você precisa recuperar a ID de rede virtual com [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork), depois a ID de sub-rede com [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig). Crie uma interface de rede com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) na sub-rede *DMZ* com encaminhamento IP habilitado:

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

### <a name="create-a-vm"></a>Criar uma máquina virtual

Para criar uma VM e anexar uma interface de rede existente a ela, você deve primeiro criar uma configuração de VM com [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). A configuração inclui a interface de rede criada na etapa anterior. Quando for solicitado um nome de usuário e senha, selecione o nome de usuário e senha com os quais você deseja fazer logon na VM.

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the VM."

# Create a VM configuration.
$vmConfig = New-AzVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzVMNetworkInterface -Id $nic.Id
```

Crie a VM usando a configuração de VM com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo a seguir cria uma VM nomeada *myVmNva*.

```azurepowershell-interactive
$vmNva = New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

A opção `-AsJob` cria a VM em segundo plano, para que você possa prosseguir para a próxima etapa.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual para que seja possível validar esse tráfego da sub-rede *Pública* roteada para a sub-rede *Privada* por meio da solução de virtualização de rede em uma etapa posterior.

Crie uma VM na sub-rede *pública* com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo a seguir cria uma VM nomeada *myVmPublic* na sub-rede *Pública* da rede virtual *myVirtualNetwork*.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPublic" `
  -AsJob
```

Crie uma VM na sub-rede *Privada*.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

A VM demora alguns minutos para criar. Não continue com a próxima etapa até que a VM seja criada e o Azure retorne a saída ao PowerShell.

## <a name="route-traffic-through-an-nva"></a>Rotear o tráfego por meio de uma NVA

Use [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para retornar o endereço IP público da VM *myVmPrivate* . O exemplo a seguir retorna o endereço IP público da VM *myVmPrivate*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Use o comando a seguir para criar uma sessão de área de trabalho remota com a VM *myVmPrivate* do seu computador local. Substitua `<publicIpAddress>` pelo o endereço IP retornado do comando anterior.

```
mstsc /v:<publicIpAddress>
```

Abra o arquivo RDP baixado. Se solicitado, selecione **Conectar**.

Insira o nome de usuário e senha especificados ao criar a VM (talvez seja necessário selecionar **Mais escolhas**, em seguida, **Usar uma conta diferente**, para especificar as credenciais inseridas ao criar a VM) e selecione **OK**. Você pode receber um aviso do certificado durante o processo de logon. Selecione **Sim** para prosseguir com a conexão.

Em uma etapa posterior, o `tracert.exe` comando é usado para testar o roteamento. O Tracert usa o protocolo ICMP, que é negado por meio do Firewall do Windows. Habilite o ICMP pelo firewall do Windows inserindo o seguinte comando do PowerShell na VM *myVmPrivate*:

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
```

Embora o rastreamento de rota seja utilizado para testar o roteamento neste artigo, não é recomendável o ICMP através do Firewall do Windows para implantações de produção.

Você habilitou o encaminhamento de IP no Azure para a interface de rede da VM em Habilitar encaminhamento de IP. Dentro da VM, o sistema operacional, ou um aplicativo em execução na VM, também pode ser capaz de encaminhar o tráfego. Habilite o encaminhamento de IP no sistema operacional do *myVmNva*.

Em um prompt de comando na VM *myVmPrivate*, a área de trabalho remota para a *myVmNva*:

``` 
mstsc /v:myvmnva
```

Para habilitar o encaminhamento de IP dentro do sistema operacional, insira o seguinte comando no PowerShell da VM *myVmNva*:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```

Reinicie a VM *myVmNva*, que também desconecta a sessão da área de trabalho remota.

Ainda conectado à VM *myVmPrivate*, crie uma sessão da área de trabalho remota para a VM *myVmPublic* após a reinicialização da VM *myVmNva*:

``` 
mstsc /v:myVmPublic
```

Habilite o ICMP pelo firewall do Windows inserindo o seguinte comando do PowerShell na VM *myVmPublic*:

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

Para testar o roteamento do tráfego para a VM *myVmPrivate* da VM *myVmPublic*, insira o seguinte comando do PowerShell na VM *myVmPublic*:

```
tracert myVmPrivate
```

A resposta é semelhante ao seguinte exemplo:

```
Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:

1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4

Trace complete.
```

Você pode ver que o primeiro salto é 10.0.2.4, que é o endereço IP privado da NVA. O segundo salto é 10.0.1.4, o endereço IP privado da VM *myVmPrivate*. A rota adicionada à tabela de rotas *myRouteTablePublic* e associada à sub-rede *Pública* fez o Azure encaminhar o tráfego através de NVA, em vez de diretamente para a sub-rede *Privada*.

Feche a sessão da área de trabalho remota para a VM *myVmPublic*, que ainda mantém você ainda conectado à VM *myVmPrivate*.

Para testar o roteamento do tráfego para a VM *myVmPublic* da VM *myVmPrivate*, insira o seguinte comando de um prompt de comando na VM *myVmPrivate*:

```
tracert myVmPublic
```

A resposta é semelhante ao seguinte exemplo:

```
Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:

1     1 ms     1 ms     1 ms  10.0.0.4

Trace complete.
```

Você pode ver que o tráfego é roteado diretamente da VM *myVmPrivate* para a VM *myVmPublic*. Por padrão, o Azure roteia o tráfego diretamente entre sub-redes.

Feche a sessão da área de trabalho remota para a VM *myVmPrivate*.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, use [Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou uma tabela de rotas e associou-a a uma sub-rede. Você criou uma solução de virtualização de rede simples que roteou o tráfego de uma sub-rede pública para uma sub-rede privada. Implante uma variedade de soluções de virtualização de rede pré-configurados que executam funções de rede, como firewall e otimização de WAN do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Para saber mais sobre roteamento, consulte [Visão geral de roteamento](virtual-networks-udr-overview.md) e [Gerenciar uma tabela de rotas](manage-route-table.md).

Embora seja possível implantar muitos recursos do Azure dentro de uma rede virtual, os recursos para alguns serviços PaaS do Azure não podem ser implantados em uma rede virtual. Ainda assim, é possível restringir acesso aos recursos de alguns serviços PaaS do Azure somente para tráfego de uma sub-rede de rede virtual. Para saber como, veja [Restringir o acesso à rede para recursos PaaS](tutorial-restrict-network-access-to-resources-powershell.md).
