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
ms.openlocfilehash: c84cd8b1d7850576eb608d88ba91c3079edb0d68
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81680766"
---
Para obter a lista de emparelhamentos, execute o comando **Get-AzPeering** .

```powershell
$directPeering = Get-AzPeering -ResourceGroupName "PeeringResourceGroup" -Name "SeattleDirectPeering"
```

O exemplo a seguir mostra a resposta quando o provisionamento de ponta a ponta foi concluído com êxito.

```powershell
    Name                 : SeattleDirectPeering
    Sku.Name             : Basic_Direct_Free
    Kind                 : Direct
    Connections          : {71}
    PeerAsn.Id           : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/SeattleDirectPeering
    UseForPeeringService : False
    PeeringLocation      : Seattle
    ProvisioningState    : Succeeded
    Location             : centralus
    Id                   : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleDirectPeering
    Type                 : Microsoft.Peering/peerings
    Tags                 : {}
```