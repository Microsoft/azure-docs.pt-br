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
ms.openlocfilehash: dbaa0b5fc87cb5393b323b8a9b7a38b72efe9518
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "81680812"
---
O cmdlet **Get-AzPeeringLocation** do PowerShell retorna uma lista de locais de emparelhamento com o parâmetro obrigatório `Kind` , que você usará em etapas posteriores.

```powershell
Get-AzPeeringLocation -Kind Direct
```

Os locais de emparelhamento direto contêm os seguintes campos:
* PeeringLocation 
* País
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandwidthOffers

Valide se você está presente na instalação de emparelhamento desejada fazendo referência a [PeeringDB](https://wwww.peeringdb.com).

Este exemplo mostra como usar Seattle como o local de emparelhamento para criar um emparelhamento direto.

```powershell
$peeringLocations = Get-AzPeeringLocation -Kind Direct
$peeringLocation = $peeringLocations | where {$_.PeeringLocation -contains "Seattle"}
$peeringLocation

PeeringLocation       : Seattle
Address               : 2001 Sixth Avenue
Country               : US
PeeringDBFacilityId   : 71
PeeringDBFacilityLink : https://www.peeringdb.com/fac/71
BandwidthOffers       : {10Gbps, 100Gbps}
```