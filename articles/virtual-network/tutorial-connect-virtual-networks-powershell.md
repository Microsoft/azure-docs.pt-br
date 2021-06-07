---
title: Conectar redes virtuais com emparelhamento VNet-Azure PowerShell
description: Neste artigo, você aprende a conectar redes virtuais com o emparelhamento de rede virtual, usando o Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d93efaedbb0596382b0601a17098311e075618b7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791973"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Conectar redes virtuais com o emparelhamento de rede virtual usando PowerShell

Você pode conectar redes virtuais entre si ao emparelhamento de rede virtual. Depois que as redes virtuais são emparelhadas, os recursos de ambas as redes virtuais podem se comunicar entre si, com a mesma latência e largura de banda como se os recursos estivessem na mesma rede virtual. Neste artigo, você aprenderá como:

* Criar duas redes virtuais
* Conectar duas redes virtuais a um emparelhamento de rede virtual
* Implementar uma VM (máquina virtual) em cada rede virtual
* Comunicação entre VMs

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o módulo do Azure PowerShell versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="create-virtual-networks"></a>Criar redes virtuais

Antes de criar uma rede virtual, será necessário criar um grupo de recursos para a rede virtual e todos os outros recursos criados neste artigo. Crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Crie uma rede virtual com [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). O exemplo a seguir cria uma rede virtual nomeada *myVirtualNetwork1* com o prefixo de endereço *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork1 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

Crie uma configuração de sub-rede com [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). O exemplo a seguir cria uma configuração de sub-rede com um prefixo de endereço 10.0.0.0/24:

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Grave a configuração de sub-rede na rede virtual com [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork), que cria a sub-rede:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzVirtualNetwork
```

Crie uma rede virtual com um prefixo de endereço 10.1.0.0/16 e uma sub-rede:

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzVirtualNetwork
```

## <a name="peer-virtual-networks"></a>Emparelhar redes virtuais

Crie um emparelhamento com [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering). Os pares de exemplo a seguir *myVirtualNetwork1* a *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

Na saída retornada após a execução do comando anterior, você verá que o **PeeringState** é *iniciado*. O emparelhamento permanece no estado *Iniciado* até que você crie o emparelhamento de *myVirtualNetwork2* para *myVirtualNetwork1*. Crie um emparelhamento de *myVirtualNetwork2* para *myVirtualNetwork1*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

Na saída retornada após a execução do comando anterior, você verá que o **PeeringState** está *conectado*. O Azure também alterou o estado de emparelhamento do emparelhamento *myVirtualNetwork1-myVirtualNetwork2* para *Conectado*. Confirme se o estado de emparelhamento do emparelhamento *myVirtualNetwork1-myVirtualNetwork2* foi alterado para *conectado* com [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Os recursos em uma rede virtual não podem se comunicar com recursos na outra rede virtual até que o **PeeringState** para os emparelhamentos em ambas as redes virtuais esteja *conectado*.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie uma VM em cada rede virtual para que seja possível comunicar-se entre elas em uma etapa posterior.

### <a name="create-the-first-vm"></a>Criar a primeira VM

Crie uma VM com [New-AzVM](/powershell/module/az.compute/new-azvm). O exemplo a seguir cria uma VM nomeada *myVm1* na rede virtual *myVirtualNetwork1*. A opção `-AsJob` cria a VM em segundo plano, para que você possa prosseguir para a próxima etapa. Quando solicitado, digite o nome de usuário e senha com os quais você deseja fazer logon na VM.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

### <a name="create-the-second-vm"></a>Criar a segunda VM

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

A VM demora alguns minutos para criar. Não continue com as etapas posteriores até que o Azure crie a VM e retorne a saída para o PowerShell.

## <a name="communicate-between-vms"></a>Comunicação entre VMs

É possível conectar ao endereço IP público de uma VM a partir da internet. Use [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) para retornar o endereço IP público de uma VM. O exemplo a seguir retorna o endereço IP público da VM *myVm1*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Use o comando a seguir para criar uma sessão de área de trabalho remota com a VM *myVm1* do seu computador local. Substitua `<publicIpAddress>` pelo o endereço IP retornado do comando anterior.

```
mstsc /v:<publicIpAddress>
```

Um arquivo .rdp (protocolo RDP) é criado, baixado para seu computador e aberto. Insira o nome de usuário e senha (talvez seja necessário selecionar **Mais escolhas**, em seguida, **Usar uma conta diferente**, para especificar as credenciais inseridas ao criar a VM) e clique em **OK**. Você pode receber um aviso do certificado durante o processo de logon. Clique em **Sim** ou em **Continuar** para prosseguir com o processo de conexão.

Sobre a VM *myVm1*, habilite o ICMP por meio do Firewall do Windows para que você possa executar ping nessa VM pela *myVm2* em uma etapa posterior, usando o PowerShell:

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

Embora o ping seja usado para realizar a comunicação entre VMs neste artigo, não é recomendável ICMP através do Firewall do Windows para implantações de produção.

Para conectar a VM *myVm2*, insira o seguinte comando a partir de um prompt de comando na VM *myVm1*:

```
mstsc /v:10.1.0.4
```

Uma vez habilitado o ping na *myVm1*, será possível executar ping por endereço IP a partir de um prompt de comando na VM *myVm2*:

```
ping 10.0.0.4
```

Você receberá quatro respostas. Desconecte as sessões RDP para ambas *myVm1* e *myVm2*.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, use [Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a conectar duas redes, na mesma região do Azure, com o emparelhamento de rede virtual. Você também pode emparelhar redes virtuais em diferentes [regiões com suporte](virtual-network-manage-peering.md#cross-region), em [diferentes assinaturas do Azure](create-peering-different-subscriptions.md#powershell) e também criar [designs de rede de hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) com emparelhamento. Para saber mais sobre o emparelhamento de rede virtual, consulte [Visão geral de emparelhamento de rede virtual](virtual-network-peering-overview.md) e [Gerenciamento de emparelhamentos de rede virtual](virtual-network-manage-peering.md).

Você pode [conectar seu próprio computador a uma rede virtual](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) por meio de uma VPN e interagir com os recursos de uma rede virtual ou de redes virtuais emparelhadas. Para obter amostras de scripts reutilizáveis para concluir muitas das tarefas abordadas nos artigos sobre redes virtuais, consulte [amostras de script](powershell-samples.md).
