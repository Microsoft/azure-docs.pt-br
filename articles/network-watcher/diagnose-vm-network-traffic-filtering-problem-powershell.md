---
title: 'Início Rápido: Diagnosticar um problema do filtro de tráfego de rede de VMs – Azure PowerShell'
titleSuffix: Azure Network Watcher
description: Saiba como usar o Azure PowerShell para diagnosticar um problema no filtro de tráfego de rede da máquina virtual usando a funcionalidade de verificação de fluxo de IP do Observador de Rede do Azure.
services: network-watcher
documentationcenter: network-watcher
author: damendo
ms.author: damendo
editor: ''
ms.date: 01/07/2021
ms.assetid: ''
ms.topic: quickstart
ms.service: network-watcher
ms.workload: infrastructure
ms.tgt_pltfrm: network-watcher
ms.devlang: na
tags:
- azure-resource-manager
ms.custom:
- mvc
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: fafe090d72d53b33ddb3e9863b7e62deba1055c5
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535775"
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-powershell"></a>Início Rápido: diagnosticar um problema de filtro de tráfego de rede de máquina virtual – Azure PowerShell

Neste início rápido, você vai implantar uma VM (máquina virtual ) e, em seguida, verificar a comunicação com um endereço IP e uma URL e de um endereço IP. Você determina a causa de uma falha de comunicação e como resolvê-la.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este Início Rápido exigirá o módulo `Az` do Azure PowerShell. Para localizar a versão instalada, execute `Get-Module -ListAvailable Az`. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.



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

Para testar a comunicação de rede com o Observador de Rede, primeiro habilite um observador de rede na região onde está localizada a VM que deseja testar e, em seguida, use a funcionalidade Verificação de fluxo de IP do Observador de Rede para testar a comunicação.

### <a name="enable-network-watcher"></a>Habilitar o observador de rede

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

### <a name="use-ip-flow-verify"></a>Usar verificação de fluxo de IP

Quando você cria uma VM, o Azure permite e nega o tráfego de rede na VM, por padrão. Mais tarde, você pode substituir os padrões do Azure, permitindo ou negando tipos adicionais de tráfego. Para testar se o tráfego é permitido ou negado para diferentes destinos e de um endereço IP de origem, use o comando [Test-AzNetworkWatcherIPFlow](/powershell/module/az.network/test-aznetworkwatcheripflow).

Teste a comunicação de saída da VM para um dos endereços IP para www.bing.com:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 13.107.21.200 `
  -RemotePort 80
```

Após alguns segundos, o resultado retornado informa que o acesso é permitido por uma regra de segurança chamada **AllowInternetOutbound**.

Teste a comunicação de saída da VM em 172.31.0.100:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 80
```

O resultado retornado informa que o acesso é negado por uma regra de segurança chamada **DefaultOutboundDenyAll**.

Teste a comunicação de entrada para a VM em 172.31.0.100:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Inbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 80 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 60000
```

O resultado retornado informa que o acesso é negado devido a uma regra de segurança chamada **DefaultInboundDenyAll**. Agora que você sabe quais regras de segurança permitem ou negam o tráfego bidirecionalmente em uma VM, você pode determinar como resolver os problemas.

## <a name="view-details-of-a-security-rule"></a>Exibir os detalhes de uma regra de segurança

Para determinar por que as regras em [Testar a comunicação na rede](#test-network-communication) permitem ou impedem a comunicação, examine as regras de segurança ativas para o adaptador de rede com [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup):

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup
```

A saída retornada inclui o seguinte texto para a regra **AllowInternetOutbound** que permitiu o acesso de saída a www.bing.com em [Usar verificação de fluxo de IP](#use-ip-flow-verify):

```powershell
{
  "Name":
"defaultSecurityRules/AllowInternetOutBound",
  "Protocol": "All",
  "SourcePortRange": [
    "0-65535"
  ],
  "DestinationPortRange": [
    "0-65535"
  ],
  "SourceAddressPrefix": [
    "0.0.0.0/0"
  ],
  "DestinationAddressPrefix": [
    "Internet"
  ],
  "ExpandedSourceAddressPrefix": [],
  "ExpandedDestinationAddressPrefix": [
    "1.0.0.0/8",
    "2.0.0.0/7",
    "4.0.0.0/6",
    "8.0.0.0/7",
    "11.0.0.0/8",
    "12.0.0.0/6",
    ...
    ],
    "Access": "Allow",
    "Priority": 65001,
    "Direction": "Outbound"
  },
```

Você pode ver na saída que **DestinationAddressPrefix** é **Internet**. No entanto, não ficou claro como 13.107.21.200, o endereço testado em [Usar verificação de fluxo de IP](#use-ip-flow-verify), está relacionado à **Internet**. Você verá vários prefixos de endereços listados em **ExpandedDestinationAddressPrefix**. Um dos prefixos na lista é **12.0.0.0/6**, que abrange o intervalo 12.0.0.1-15.255.255.254 de endereços IP. Como 13.107.21.200 está dentro desse intervalo de endereços, a regra **AllowInternetOutBound** permite o tráfego de saída. Além disso, não existem regras de **prioridade** mais alta (número inferior) listadas na saída retornada por `Get-AzEffectiveNetworkSecurityGroup`, que substituem essa regra. Para negar a comunicação de saída a 13.107.21.200, você pode adicionar uma regra de segurança com uma prioridade mais alta, que nega a porta 80 de saída para o endereço IP.

Quando você executou o comando `Test-AzNetworkWatcherIPFlow` para testar a comunicação de saída para 172.131.0.100 em [Usar verificação de fluxo de IP](#use-ip-flow-verify), a saída informou que a regra **DefaultOutboundDenyAll** negou a comunicação. A regra **DefaultOutboundDenyAll** é equivalente à regra **DenyAllOutBound** listada na seguinte saída do comando `Get-AzEffectiveNetworkSecurityGroup`:

```powershell
{
"Name": "defaultSecurityRules/DenyAllOutBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Outbound"
}
```

A regra lista **0.0.0.0/0** como o **DestinationAddressPrefix**. A regra nega a comunicação de saída para 172.131.0.100, porque o endereço não está dentro do **DestinationAddressPrefix** de uma das outras regras de saída na saída do comando `Get-AzEffectiveNetworkSecurityGroup`. Para permitir a comunicação de saída, você pode adicionar uma regra de segurança de entrada com uma prioridade mais alta que permite o tráfego de entrada para a porta 80 em de 172.131.0.100.

Quando você executou o comando `Test-AzNetworkWatcherIPFlow` para testar a comunicação de entrada de 172.131.0.100 em [Usar verificação de fluxo de IP](#use-ip-flow-verify), a saída informou que a regra **DefaultInboundDenyAll** negou a comunicação. A regra **DefaultInboundDenyAll** é equivalente à regra **DenyAllInBound** listada na seguinte saída do comando `Get-AzEffectiveNetworkSecurityGroup`:

```powershell
{
"Name": "defaultSecurityRules/DenyAllInBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Inbound"
},
```

A regra **DenyAllInBound** é aplicada, porque, conforme mostrado na saída, não há nenhuma outra regra de prioridade mais alta na saída do comando `Get-AzEffectiveNetworkSecurityGroup` que permite a porta 80 de entrada para a VM de 172.131.0.100. Para permitir a comunicação de entrada, adicione uma regra de segurança de entrada com uma prioridade mais alta que permite a porta 80 de entrada de 172.131.0.100.

As verificações deste início rápido testaram a configuração do Azure. Se as verificações retornarem resultados esperados e você ainda tiver problemas de rede, verifique se você não tem um firewall entre a VM e o ponto de extremidade com o qual está se comunicando e se o sistema operacional na VM não tem um firewall que permite ou nega a comunicação.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você poderá usar [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou uma VM e diagnosticou os filtros de tráfego de rede de entrada e saída. Você aprendeu que as regras do grupo de segurança de rede permitem ou negam o tráfego bidirecionalmente em uma VM. Saiba mais sobre [regras de segurança](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e como [criar regras de segurança](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule).

Mesmo com os filtros de tráfego de rede apropriados em vigor, a comunicação com uma VM ainda poderá falhar, devido à configuração de roteamento. Para saber como diagnosticar problemas de roteamento de rede de VMs, consulte [Diagnosticar problemas de roteamento de VM](diagnose-vm-network-routing-problem-powershell.md) ou, para diagnosticar problemas de roteamento de saída, latência e filtragem de tráfego, com uma única ferramenta, consulte [Solução de problemas de conexão](network-watcher-connectivity-powershell.md).
