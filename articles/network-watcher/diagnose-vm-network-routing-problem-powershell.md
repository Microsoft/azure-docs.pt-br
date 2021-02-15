---
title: Diagnosticar um problema de roteamento de rede VM-Azure PowerShell
titleSuffix: Azure Network Watcher
description: Neste artigo, você aprenderá a diagnosticar um problema de roteamento da rede de máquina virtual com a funcionalidade de próximo salto do Observador de Rede do Azure.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 6569a99ec851da478151665921e7689e1c3488f1
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020189"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>Diagnosticar um problema de roteamento de rede de máquina virtual - Azure PowerShell

Neste artigo, você implanta uma VM (máquina virtual ) e, em seguida, verifica a comunicação com um endereço IP e uma URL. Você determina a causa de uma falha de comunicação e como resolvê-la.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o `Az` módulo Azure PowerShell. Para localizar a versão instalada, execute `Get-Module -ListAvailable Az`. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.



## <a name="create-a-vm"></a>Criar uma máquina virtual

Antes de criar uma VM, você deve criar um grupo de recursos para conter a VM. Crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

Crie a VM com [New-AzVM](/powershell/module/az.compute/new-azvm). Ao executar esta etapa, credenciais serão solicitadas de você. Os valores que você inseriu são configurados como o nome de usuário e senha para a VM.

```azurepowershell-interactive
$vM = New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

A VM demora alguns minutos para criar. Não continue com as etapas restantes até que a VM seja criada e o PowerShell retorne a saída.

## <a name="test-network-communication"></a>Testar comunicação de rede

Para testar a comunicação de rede com o Observador de Rede, primeiro habilite um observador de rede na região onde está localizada a VM que deseja testar e, em seguida, use a funcionalidade de próximo salto do Observador de Rede para testar a comunicação.

## <a name="enable-network-watcher"></a>Habilitar o observador de rede

Se você já tiver um observador de rede habilitado na região Leste dos EUA, use [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher) para recuperar o observador de rede. O seguinte exemplo recupera um observador de rede existente chamado *NetworkWatcher_eastus* que está no grupo de recursos *NetworkWatcherRG*:

```azurepowershell-interactive
$networkWatcher = Get-AzNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Se você ainda não tiver um observador de rede habilitado na região Leste dos EUA, use [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher) para criar um observador de rede na região Leste dos EUA:

```azurepowershell-interactive
$networkWatcher = New-AzNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>Usar próximo salto

O Azure cria automaticamente as rotas para destinos padrão. Você pode criar rotas personalizadas que substituem as rotas padrão. Às vezes, as rotas personalizadas podem causar falha na comunicação. Para testar o roteamento de uma VM, use o comando [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) para determinar o próximo salto de roteamento quando o tráfego for destinado a um endereço específico.

Teste a comunicação de saída da VM para um dos endereços IP para www.bing.com:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

Após alguns segundos, a saída informa que o **NextHopType** é **Internet** e que a **RouteTableId** é **Rota do Sistema**. Esse resultado permite que você saiba que há uma rota válida para o destino.

Teste a comunicação de saída da VM em 172.31.0.100:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

A saída retornada informa que **Nenhum** é o **NextHopType** e que a **RouteTableId** também é **Rota do Sistema**. Esse resultado permite que você saiba que, embora haja uma rota do sistema válida para o destino, não há nenhum próximo salto para encaminhar o tráfego para o destino.

## <a name="view-details-of-a-route"></a>Exibir detalhes de uma rota

Para analisar ainda mais o roteamento, examine as rotas efetivas para a interface de rede com o comando [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) :

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

A saída que inclui o seguinte texto é retornada:

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

Como é possível ver na saída anterior, a rota com **AddressPrefix** de **0.0.0.0/0** roteia todo o tráfego não destinado a endereços dentro dos prefixos de endereço de outras rotas com um próximo salto de **Internet**. Como você também pode ver na saída, embora haja uma rota padrão para o prefixo 172.16.0.0/12, que inclui o endereço 172.31.0.100, o **nextHopType** é **Nenhum**. O Azure cria uma rota padrão para 172.16.0.0/12, mas não especifica um tipo de próximo salto até que haja um motivo. Se, por exemplo, você adicionou o intervalo de endereços 172.16.0.0/12 ao espaço de endereço da rede virtual, o Azure alterará o **nextHopType** para **Rede virtual** da rota. Em seguida, uma verificação mostra a **Rede virtual** como o **nextHopType**.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você poderá usar [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou uma VM e o roteamento de rede diagnosticado da VM. Você aprendeu que o Azure cria várias rotas padrão e testou o roteamento para dois destinos diferentes. Saiba mais sobre o [roteamento no Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e como [criar rotas personalizadas](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Para conexões de VM de saída, determine também a latência e o tráfego de rede permitido e negado entre a VM e um ponto de extremidade, usando a funcionalidade [solução de problemas de conexão](network-watcher-connectivity-powershell.md) do Observador de Rede. Você pode monitorar a comunicação entre uma VM e um ponto de extremidade, como um endereço IP ou uma URL, ao longo do tempo usando a funcionalidade de monitor de conexão do Observador de Rede. Para saber como, consulte [Monitorar uma conexão de rede](connection-monitor.md).
