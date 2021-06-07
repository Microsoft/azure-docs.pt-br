---
title: incluir arquivo
titleSuffix: Azure
description: incluir arquivo
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 12d169697a35af446392843eb57e6ec3a5508e45
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "81678627"
---
As seguintes operações de modificação têm suporte para o emparelhamento do Exchange:
* Adicione conexões de emparelhamento do Exchange.
* Remova as conexões de emparelhamento do Exchange.
* Adicione uma sessão IPv4 ou IPv6 em conexões ativas.
* Remova uma sessão IPv4 ou IPv6 em conexões ativas.


### <a name="add-exchange-peering-connections"></a>Adicionar conexões de emparelhamento do Exchange

Este exemplo descreve como adicionar conexões a um emparelhamento do Exchange existente.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.22 `
    -PeerSessionIPv6Address  2001:504:12::22 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `

$exchangePeering.Connections.Add($connection)

$exchangePeering | Update-AzPeering

```

### <a name="remove-exchange-peering-connections"></a>Remover conexões de emparelhamento do Exchange

Este exemplo descreve como remover conexões para um emparelhamento do Exchange existente.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

```

Exiba todas as conexões e selecione a conexão que você deseja remover. 

```powershell

$exchangePeering

Name              : SeattleExchangePeering
Sku.Name          : Basic_Exchange_Free
Kind              : Exchange
Connections       : {11}
PeerAsn.Id        : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{peerAsnName}
PeeringLocation   : Seattle
ProvisioningState : Succeeded
Location          : West US 2
Id                : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleExchangePeering
Type              : Microsoft.Peering/peerings
Tags              : {}

```

No comando a seguir, em vez de 0, insira o número de índice para a conexão que você deseja remover.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Adicionar uma sessão IPv4 ou IPv6 em conexões ativas

Este exemplo descreve como adicionar uma sessão IPv6 a uma conexão do Exchange existente.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Remover uma sessão IPv4 ou IPv6 em conexões ativas

Atualmente, não há suporte para a remoção de uma sessão IPv4 ou IPv6 de uma conexão existente no PowerShell. Para obter mais informações, entre em contato com [o emparelhamento da Microsoft](mailto:peeringexperience@microsoft.com).