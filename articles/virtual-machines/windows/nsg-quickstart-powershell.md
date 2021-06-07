---
title: Abrir portas para uma VM usando o Azure PowerShell
description: Saiba como abrir uma porta/criar um ponto de extremidade para sua VM usando Azure PowerShell
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8390b5c779e6aa053e1af2754c436dd51e410b06
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550409"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-using-powershell"></a>Como abrir portas e pontos de extremidade para uma VM usando o PowerShell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Comandos rápidos
Para criar um Grupo de Segurança de Rede e as regras de ACL, você precisa [ter a versão mais recente do Azure PowerShell instalada](/powershell/azure/). Você também pode [executar essas etapas usando o Portal do Azure](nsg-quickstart-portal.md).

Faça logon na sua Conta do Azure:

```powershell
Connect-AzAccount
```

Nos exemplos a seguir, substitua os nomes de parâmetro pelos seus próprios valores. Os nomes de parâmetro de exemplo incluem *myResourceGroup*, *myNetworkSecurityGroup* e *myVnet*.

Crie uma regra com [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). O exemplo a seguir cria uma regra chamada *myNetworkSecurityGroupRule* para permitir o tráfego *tcp* na porta *80*:

```powershell
$httprule = New-AzNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" `
    -Access "Allow" `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority "100" `
    -SourceAddressPrefix "Internet" `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80
```

Em seguida, crie seu Grupo de Segurança de Rede com [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) e atribua a regra de HTTP que você acabou de criar conforme descrito a seguir. O exemplo a seguir cria um Grupo de Segurança de Rede chamado *myNetworkSecurityGroup*:

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Agora, vamos atribuir seu Grupo de Segurança de Rede a uma sub-rede. O exemplo a seguir atribui uma rede virtual existente chamada *myVnet* à variável *$vnet* com [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork):

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Associe seu Grupo de Segurança de Rede à sub-rede com [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig). O exemplo a seguir associa a sub-rede chamada *mySubnet* ao seu Grupo de Segurança de Rede:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Por fim, atualize sua rede virtual com [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) para que suas alterações entrem em vigor:

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Mais informações sobre os Grupos de Segurança de Rede
Os comandos rápidos aqui permitem que você coloque tudo em funcionamento com o tráfego que flui para sua VM. Os Grupos de Segurança de Rede fornecem muitos recursos excelentes e granularidade para controlar o acesso aos recursos. Você pode ler mais sobre a [criação de um Grupo de Segurança de Rede e as regras ACL aqui](tutorial-virtual-network.md#secure-network-traffic).

Para aplicativos Web altamente disponíveis, você deve colocar suas VMs atrás de um balanceador de carga do Azure. O balanceador de carga distribui o tráfego para VMs, com um Grupo de Segurança de rede que fornece filtragem. Para saber mais, veja [Como balancear a carga de máquinas virtuais Linux no Azure para criar um aplicativo altamente disponível](tutorial-load-balancer.md).

## <a name="next-steps"></a>Próximas etapas
Neste exemplo, você criou uma regra simples para permitir o tráfego HTTP. Você pode encontrar informações sobre a criação de ambientes mais detalhados nos seguintes artigos:

* [Visão geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md)
* [O que é um grupo de segurança de rede?](../../virtual-network/network-security-groups-overview.md)
* [Visão geral do Azure Load Balancer](../../load-balancer/load-balancer-overview.md)
