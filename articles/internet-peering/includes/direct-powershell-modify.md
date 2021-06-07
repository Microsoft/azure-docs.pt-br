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
ms.openlocfilehash: b2609a069872ec55ac9068fadcbb3f312d68a630
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "81680754"
---
Esta seção descreve como executar as seguintes operações de modificação para o emparelhamento direto:

* Adicione conexões de emparelhamento direto.
* Remova as conexões de emparelhamento direto.
* Atualizar ou fazer downgrade da largura de banda em conexões ativas.
* Adicionar sessões IPv4 ou IPv6 em conexões ativas.
* Remova sessões IPv4 ou IPv6 em conexões ativas.

### <a name="add-direct-peering-connections"></a>Adicionar conexões de emparelhamento direto

Este exemplo descreve como adicionar conexões ao emparelhamento direto existente.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringDirectConnection `
    -PeeringDBFacilityId $peeringLocation.PeeringDBFacilityId `
    -SessionPrefixV4 "10.22.31.0/31" `
    -SessionPrefixV6 "fe02::3e:0/127" `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000

$directPeering.Connections.Add($connection)

$directPeering | Update-AzPeering
```

### <a name="remove-direct-peering-connections"></a>Remover conexões de emparelhamento direto

Não há suporte para a remoção de uma conexão no momento no PowerShell. Para obter mais informações, entre em contato com [o emparelhamento da Microsoft](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Atualizar ou fazer downgrade da largura de banda em conexões ativas

Este exemplo descreve como adicionar 10 Gbps a uma conexão direta existente.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4-or-ipv6-sessions-on-active-connections"></a>Adicionar sessões IPv4 ou IPv6 em conexões ativas

Este exemplo descreve como adicionar uma sessão IPv6 em uma conexão direta existente somente com uma sessão IPv4. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4-or-ipv6-sessions-on-active-connections"></a>Remover sessões IPv4 ou IPv6 em conexões ativas

Atualmente, não há suporte para a remoção de uma sessão IPv4 ou IPv6 de uma conexão existente no PowerShell. Para obter mais informações, entre em contato com [o emparelhamento da Microsoft](mailto:peeringexperience@microsoft.com).