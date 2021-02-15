---
title: Implantar o Firewall do Azure com vários endereços IP públicos usando Azure PowerShell
description: Implantar um firewall com vários endereços IP públicos para proteger um hub virtual
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 652c7cbfbe63ef2ae9a0d54e05407152ea300f1d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87006935"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses"></a>Implantar um firewall do Azure com vários endereços IP públicos

Se você quiser proteger um hub virtual usando o Firewall do Azure, poderá implantar o firewall com vários endereços IP públicos usando Azure PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-the-firewall"></a>Implantar o firewall

Use o exemplo a seguir Azure PowerShell para implantar um firewall do Azure com vários endereços IP públicos para proteger um hub virtual.

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID> 

$rgName = <resource group name> 
$vHub = Get-AzVirtualHub -Name <hub name> 
$vHubId = $vHub.Id 
$fwpips = New-AzFirewallHubPublicIpAddress -Count 3
$hubIpAddresses = New-AzFirewallHubIpAddress -PublicIPs $fwpips 
$fw = New-AzFirewall -Name <firewall name> -ResourceGroupName $rgName `
     -Location <location> -Sku AZFW_Hub -HubIPAddresses $hubIpAddresses `
     -VirtualHubId $vHubId 
```

### <a name="update-a-public-ip-address"></a>Atualizar um endereço IP público

Você pode usar Azure PowerShell para atualizar um endereço IP público para um firewall do Azure. O exemplo a seguir exclui um endereço IP público de um firewall. Ele começa com três endereços IP públicos.

```azurepowershell
Select-AzSubscription -SubscriptionId <subscription ID>

$azfw = get-azfirewall -Name <firewall name> -ResourceGroupName <resource group name>
$ip1 = New-AzFirewallPublicIpAddress -Address <first ip address to keep>
$ip2 = New-AzFirewallPublicIpAddress -Address <second ip address to keep>
$ipAddresses = $ip1,$ip2
$azfw.HubIPAddresses.publicIPs.Addresses = $ipAddresses
$azfw.HubIPAddresses.publicIPs.count = 2
Set-AzFirewall -AzureFirewall $azfw
```

## <a name="next-steps"></a>Próximas etapas

[O que é um hub virtual seguro?](secured-virtual-hub.md)