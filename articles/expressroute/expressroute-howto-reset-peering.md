---
title: 'Azure ExpressRoute: redefinir emparelhamento de circuito'
description: Saiba como habilitar e desabilitar emparelhamentos para um circuito do Azure ExpressRoute usando Azure PowerShell.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 12/15/2020
ms.author: duau
ms.openlocfilehash: 0bde96ae5f4a9aff6f4a16a4f1544d9b39e5cb66
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97559566"
---
# <a name="reset-expressroute-circuit-peerings"></a>Redefinir emparelhamentos de circuitos do ExpressRoute

Este artigo descreve como habilitar e desabilitar emparelhamentos de um circuito do ExpressRoute usando o PowerShell. Os emparelhamentos são habilitados por padrão quando você os cria. Quando você desabilita um emparelhamento, a sessão BGP nas conexões primária e secundária do circuito do ExpressRoute será desligada. Você perderá a conectividade para esse emparelhamento para a Microsoft. Quando você habilita um emparelhamento, a sessão BGP na conexão primária e secundária do circuito do ExpressRoute será estabelecida. A conectividade com a Microsoft será restaurada para esse emparelhamento. Você pode habilitar e desabilitar o emparelhamento para o emparelhamento da Microsoft e o emparelhamento privado do Azure de forma independente no circuito do ExpressRoute.

Há dois cenários em que você pode achar útil redefinir os emparelhamentos do ExpressRoute.
* Se você quiser testar o design e a implementação da recuperação de desastres. Por exemplo, você tem dois circuitos ExpressRoute. Você pode desabilitar os emparelhamentos em um circuito e forçar o failover do tráfego de rede para o outro circuito.
* Habilite a detecção de encaminhamento bidirecional (BFD) no emparelhamento privado do Azure ou no emparelhamento da Microsoft do circuito do ExpressRoute. O BFD é habilitado por padrão no emparelhamento privado do Azure se você criou o circuito do ExpressRoute após 1º de agosto de 2018 e para o emparelhamento da Microsoft após 10 de janeiro de 2020. Se o circuito foi criado antes da data listada, você precisará redefinir o emparelhamento para habilitar BFD. 

### <a name="working-with-azure-powershell"></a>Trabalhando com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Redefinir um emparelhamento

1. Se você estiver executando o PowerShell localmente, abra o console do PowerShell com privilégios elevados e conecte-se à sua conta. Use o exemplo a seguir para ajudar a se conectar:

   ```azurepowershell
   Connect-AzAccount
   ```
2. Se você tiver várias assinaturas do Azure, verifique as assinaturas para a conta.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
3. Especifique a assinatura que você deseja usar.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
4. Execute os seguintes comandos para recuperar seu circuito da Rota Expressa.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. Identifique o emparelhamento que você deseja desativar ou ativar. *Emparelhamentos* é uma matriz. No exemplo a seguir, Peerings [0] é Peering e Peerings privados do Azure [1] Microsoft Peering.

   ```azurepowershell-interactive
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"cd011bef-dc79-49eb-b4c6-81fb6ea5d178"
   ProvisioningState                : Succeeded
   Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             :
   ServiceProviderProperties        : {
                                     "ServiceProviderName": "Coresite",
                                     "PeeringLocation": "Los Angeles",
                                     "BandwidthInMbps": 50
                                   }
   ServiceKey                       : ########-####-####-####-############
   Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "10.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "10.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 789,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "NotConfigured",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     },
                                     {
                                       "Name": "MicrosoftPeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/MicrosoftPeering",
                                       "PeeringType": "MicrosoftPeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "3.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "3.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 345,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [
                                           "3.0.0.3/32"
                                         ],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "ValidationNeeded",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
   Authorizations                   : []
   AllowClassicOperations           : False
   GatewayManagerEtag               :
   ```
6. Execute os seguintes comandos para alterar o estado do peering.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   O emparelhamento deve estar em um estado que você definir. 

## <a name="next-steps"></a>Próximas etapas
Se precisar de ajuda para solucionar um problema de ExpressRoute, consulte os seguintes artigos:
* [Verificando a conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Solucionando problemas de desempenho de rede](expressroute-troubleshooting-network-performance.md)
