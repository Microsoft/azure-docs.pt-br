---
title: arquivo de inclusão
titleSuffix: Azure
description: arquivo de inclusão
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dbaa0b5fc87cb5393b323b8a9b7a38b72efe9518
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
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