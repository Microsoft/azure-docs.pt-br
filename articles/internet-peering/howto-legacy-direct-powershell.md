---
title: Converter um emparelhamento direto herdado para um recurso do Azure usando o PowerShell
titleSuffix: Azure
description: Converter um emparelhamento direto herdado para um recurso do Azure usando o PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d3e7cdf11e1e1e033b4e72b9579d8c63b28e6c88
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89071679"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-powershell"></a>Converter um emparelhamento direto herdado para um recurso do Azure usando o PowerShell

Este artigo descreve como converter um emparelhamento direto herdado existente para um recurso do Azure usando cmdlets do PowerShell.

Se preferir, você pode concluir este guia usando o [portal](howto-legacy-direct-portal.md)do Azure.

## <a name="before-you-begin"></a>Antes de começar
* Examine os [pré-requisitos](prerequisites.md) e o [Guia passo a passo de emparelhamento Direto](walkthrough-direct-all.md) antes de começar a configuração.

### <a name="work-with-azure-powershell"></a>Trabalhar com Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Converter um emparelhamento direto herdado em um recurso do Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Entre na sua conta do Azure e selecione sua assinatura
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-a-legacy-direct-peering-for-conversion"></a><a name= get></a>Obter um emparelhamento direto herdado para conversão
Este exemplo mostra como obter um emparelhamento direto herdado no local de emparelhamento de Seattle.

```powershell
$legacyPeering = Get-AzLegacyPeering `
    -Kind Direct -PeeringLocation "Seattle"
$legacyPeering
```

Eis uma resposta de exemplo:
```powershell
Name                       :
Sku                        : Basic_Direct_Free
Kind                       : Direct
PeeringLocation            : Seattle
UseForPeeringService       : False
PeerAsn.Id                 :
Connection                 : ------------------------
PeeringDBFacilityId        : 71
SessionPrefixIPv4          : 4.71.156.72/30
PeerSessionIPv4Address     : 4.71.156.73
MicrosoftIPv4Address       : 4.71.156.74
SessionStateV4             : Established
MaxPrefixesAdvertisedV4    : 20000
SessionPrefixIPv6          : 2001:1900:2100::1e10/126
MaxPrefixesAdvertisedV6    : 2000
ConnectionState            : Active
BandwidthInMbps            : 0
ProvisionedBandwidthInMbps : 20000
Connection                 : ------------------------
PeeringDBFacilityId        : 71
SessionPrefixIPv4          : 4.68.70.140/30
PeerSessionIPv4Address     : 4.68.70.141
MicrosoftIPv4Address       : 4.68.70.142
SessionStateV4             : Established
MaxPrefixesAdvertisedV4    : 20000
SessionPrefixIPv6          : 2001:1900:4:3::cc/126
PeerSessionIPv6Address     : 2001:1900:4:3::cd
MicrosoftIPv6Address       : 2001:1900:4:3::ce
SessionStateV6             : Established
MaxPrefixesAdvertisedV6    : 2000
ConnectionState            : Active
BandwidthInMbps            : 0
ProvisionedBandwidthInMbps : 20000
ProvisioningState          : Succeeded
```

### <a name="convert-a-legacy-direct-peering"></a>Converter um emparelhamento direto herdado

&nbsp;
> [!IMPORTANT]
> Quando você converte um emparelhamento herdado em um recurso do Azure, não há suporte para modificações. &nbsp;

Use este comando para converter um emparelhamento direto herdado para um recurso do Azure:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleDirectPeering" `
    -ResourceGroupName "PeeringResourceGroup" `

```

Eis uma resposta de exemplo:

```powershell
Name                 : SeattleDirectPeering
Sku.Name             : Basic_Direct_Free
Kind                 : Direct
Connections          : {11, 11}
PeerAsn.Id           : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{asnNumber}
UseForPeeringService : False
PeeringLocation      : Seattle
ProvisioningState    : Succeeded
Location             : centralus
Id                   : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleDirectPeering
Type                 : Microsoft.Peering/peerings
Tags                 : {}
```

## <a name="additional-resources"></a>Recursos adicionais
Você pode obter descrições detalhadas de todos os parâmetros executando este comando:

```powershell
Get-Help Get-AzPeering -detailed
```

Para obter mais informações, confira [Perguntas frequentes sobre emparelhamento de Internet](faqs.md).

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar um emparelhamento direto usando o PowerShell](howto-direct-powershell.md)
