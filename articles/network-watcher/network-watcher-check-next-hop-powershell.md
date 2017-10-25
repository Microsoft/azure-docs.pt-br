---
title: "Localizar próximo salto com o Próximo Salto do Observador de Rede do Azure - PowerShell | Microsoft Docs"
description: "Este artigo descreve como você pode encontrar o que é o tipo do próximo salto e o endereço ip com o próximo salto usando o PowerShell."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 6a656c55-17bd-40f1-905d-90659087639c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: ef559fbbd3e8448d64167552cacee04790418343
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-powershell"></a>Descubra qual o tipo do próximo salto é usando o recurso de próximo salto no Observador de Rede do Azure usando o PowerShell

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [API REST do Azure](network-watcher-check-next-hop-rest.md)

O Próximo salto é um recurso do Observador de Rede fornece o capacidade de obter o tipo do próximo salto e o endereço IP com base em uma máquina virtual especificada. Esse recurso é útil para determinar se o tráfego deixar uma máquina virtual atravessa um gateway, internet ou redes virtuais para chegar ao seu destino.

## <a name="before-you-begin"></a>Antes de começar

Nesse cenário, você usará o Portal do Azure para localizar o tipo do próximo salto e o endereço IP.

Este cenário pressupõe que você seguiu as etapas em [Criação de um Observador de Rede](network-watcher-create.md) para criar um Observador de Rede. O cenário também pressupõe que exista um grupo de recursos com uma máquina virtual válida a ser usada.

## <a name="scenario"></a>Cenário

O cenário abordado neste artigo usa o próximo salto, um recurso do Observador de Rede que localiza o tipo do próximo salto e o endereço IP para um recurso. Para saber mais sobre o Próximo Salto, visite [Visão geral do próximo salto](network-watcher-next-hop-overview.md).

## <a name="retrieve-network-watcher"></a>Recuperar o Observador de Rede

A primeira etapa é recuperar a instância do Observador de Rede. A variável `$networkWatcher` é passada para o cmdlet de verificação de próximo salto.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-virtual-machine"></a>Obter uma máquina virtual

Próximo salto retorna o próximo salto e o endereço IP do próximo salto de uma máquina virtual. Uma Id de uma máquina virtual é necessária para o cmdlet. Se você já souber a ID da máquina virtual para usar, você pode ignorar esta etapa.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

> [!NOTE]
> O próximo salto exige a alocação do recurso de VM para ser executado.

## <a name="get-the-network-interfaces"></a>Obter as interfaces de rede

O endereço IP de uma NIC na máquina virtual é necessária neste exemplo, recuperamos as NICs em uma máquina virtual. Se você já souber o endereço IP que você deseja testar na máquina virtual, você pode ignorar esta etapa.

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkProfile.NetworkInterfaces.Id.ForEach({$_})}
```

## <a name="get-next-hop"></a>Obter o próximo salto

Agora, chamamos o cmdlet `Get-AzureRmNetworkWatcherNextHop`. Passamos o cmdlet, o Observador de Rede, a ID da máquina virtual, o endereço IP de origem e o endereço IP de destino. Neste exemplo, o endereço IP de destino é uma VM em outra rede virtual. Há um gateway de rede virtual entre as duas redes virtuais.

```powershell
Get-AzureRmNetworkWatcherNextHop -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id -SourceIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress  -DestinationIPAddress 10.0.2.4 
```

## <a name="review-results"></a>Analisar resultados

Ao concluir, os resultados são fornecidos. O próximo endereço IP for retornado, bem como o tipo de recurso é. Nesse cenário, é o endereço IP público do gateway de rede virtual.

```
NextHopIpAddress NextHopType           RouteTableId 
---------------- -----------           ------------ 
13.78.238.92     VirtualNetworkGateway Gateway Route
```

A lista a seguir mostra os valores de NextHopType disponíveis no momento:

**Tipo do próximo salto**

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* Nenhum

## <a name="next-steps"></a>Próximas etapas

Aprenda a analisar as configurações de Grupo de Segurança de Rede por meio de programação visitando [NSG auditoria com o Observador de Rede](network-watcher-nsg-auditing-powershell.md)

















