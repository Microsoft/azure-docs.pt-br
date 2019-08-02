---
title: 'Configurar o emparelhamento para um circuito – ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Este artigo fornece uma orientação sobre as etapas de criação e de provisionamento do emparelhamento público, privado e da Microsoft de um circuito de ExpressRoute. Este artigo também mostra como verificar o status, atualizar ou excluir emparelhamentos de seu circuito.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: e2f38671910be2c7300f39fcbca32e8a1ccd9891
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "64716143"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Criar e modificar o emparelhamento de um circuito do ExpressRoute usando o PowerShell

Esse artigo ajuda você a criar e gerenciar a configuração de roteamento de um circuito do ExpressRoute no modelo de implantação do Resource Manager usando o PowerShell. Você também pode verificar o status, atualizar ou excluir e desprovisionar emparelhamentos de um circuito do ExpressRoute. Se quiser usar um método diferente para trabalhar com seu circuito, selecione um artigo na lista a seguir:

> [!div class="op_single_selector"]
> * [Portal do Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [Vídeo – Emparelhamento privado](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo – Emparelhamento público](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Vídeo – Emparelhamento da Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 


Estas instruções se aplicam apenas a circuitos criados com provedores de serviço que oferecem serviços de conectividade de Camada 2. Se você estiver usando um provedor de serviços que ofereça serviços gerenciados de Camada 3 (normalmente um IPVPN, como MPLS), seu provedor de conectividade configurará e gerenciará o roteamento para você.

> [!IMPORTANT]
> Atualmente, não anunciamos emparelhamentos configurados pelos provedores de serviço por meio do portal de gerenciamento de serviço. Estamos trabalhando para habilitar esse recurso em breve. Verifique com seu provedor de serviços antes de configurar os emparelhamentos via protocolo BGP.
> 
> 

Você pode configurar um, dois ou todos os três emparelhamentos (privado e público do Azure e da Microsoft) para um circuito do ExpressRoute. Você pode configurar emparelhamentos em qualquer ordem escolhida. No entanto, você deve concluir a configuração de um emparelhamento por vez. Para obter mais informações sobre o roteamento de domínios e emparelhamentos, consulte [Domínios de roteamento do ExpressRoute](expressroute-circuit-peerings.md).

## <a name="configuration-prerequisites"></a>Pré-requisitos de configuração

* Verifique se você leu a página de [pré-requisitos](expressroute-prerequisites.md), a página de [requisitos do roteamento](expressroute-routing.md) e a página [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.
* Você deve ter um circuito do ExpressRoute ativo. Antes de continuar, siga as instruções para [criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md) e para que o circuito seja habilitado pelo provedor de conectividade. O circuito do ExpressRoute deve estar em um estado provisionado e habilitado para que você possa executar os cmdlets neste artigo.

### <a name="working-with-azure-powershell"></a>Trabalhando com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="msft"></a>Emparelhamento da Microsoft

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento da Microsoft para um circuito do ExpressRoute.

> [!IMPORTANT]
> O emparelhamento da Microsoft de circuitos do ExpressRoute configurados antes de 1º de agosto de 2017 terá todos os prefixos de serviço anunciados através do emparelhamento da Microsoft, mesmo que os filtros de rota não estejam definidos. O emparelhamento da Microsoft de circuitos de ExpressRoute configurados em ou após 1º de agosto de 2017 não terá nenhum prefixo anunciado até que um filtro de rota seja anexado ao circuito. Para obter mais informações, consulte [Configurar um filtro de rota para o emparelhamento da Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Criar emparelhamento da Microsoft

1. Entrar e selecione sua assinatura.

   Se você instalou o PowerShell localmente, entre. Se você estiver usando o Azure Cloud Shell, ignore esta etapa.

   ```azurepowershell
   Connect-AzAccount
   ```

   Selecionar a assinatura na qual você deseja criar um circuito do ExpressRoute.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Criar um circuito do ExpressRoute.

   Siga as instruções para criar um [circuito do ExpressRoute](expressroute-howto-circuit-arm.md) e para que o circuito seja provisionado pelo provedor de conectividade. Caso seu provedor de conectividade ofereça serviços gerenciados de camada 3, você pode solicitar a ele a habilitação do emparelhamento da Microsoft. Nesse caso, não será necessário seguir as instruções listadas nas seções a seguir. No entanto, se o seu provedor de conectividade não gerenciar o roteamento, após a criação do circuito, continue a configuração executando as próximas etapas. 

3. Verifique se o circuito do ExpressRoute está provisionado e habilitado. Use o seguinte exemplo:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   A resposta é semelhante ao seguinte exemplo:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
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
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. Configurar o emparelhamento da Microsoft para o circuito. Você precisa ter as seguintes informações antes de continuar:

   * Uma sub-rede /30 ou /126 para o link principal. Este valor deve ser um prefixo IPv4 ou IPv6 público válido próprio e registrado em um RIR/IRR.
   * Uma sub-rede /30 ou /126 para o link secundário. Este valor deve ser um prefixo IPv4 ou IPv6 público válido próprio e registrado em um RIR/IRR.
   * Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN.
   * Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes.
   * Prefixos anunciados: você precisa fornecer uma lista com todos os prefixos que planeja anunciar na sessão BGP. Somente prefixos de endereços IP públicos são aceitos. Caso você planeje enviar um conjunto de prefixos, poderá enviar uma lista separada por vírgulas. Esses prefixos devem ser registrados em seu nome em um RIR/IRR. Sessões BGP IPv4 exigem que prefixos IPv4 anunciados, e sessões BGP IPv6 exigem prefixos IPv6 anunciados. 
   * Nome do registro de roteamento: é possível especificar o RIR/IRR no qual o número AS e os prefixos estão registrados.
   * Opcional:
     * ASN do cliente: se você estiver anunciando prefixos não registrados com o número AS de emparelhamento, especifique o número AS com o qual eles estão registrados.
     * Um Hash MD5, se você optar por usar um.

   Use o exemplo a seguir para configurar o emparelhamento da Microsoft para seu circuito:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

### <a name="getmsft"></a>Para obter detalhes de emparelhamento da Microsoft

Você pode obter detalhes de configuração usando o exemplo a seguir:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="updatemsft"></a>Para atualizar a configuração de emparelhamento da Microsoft

Você pode atualizar qualquer parte da configuração usando o exemplo a seguir:

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletemsft"></a>Para excluir emparelhamento da Microsoft

Você pode remover a configuração de emparelhamento executando o seguinte cmdlet:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="private"></a>Emparelhamento privado do Azure

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento privado do Azure para um circuito do ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Criar um emparelhamento privado do Azure

1. Importe o módulo do PowerShell para ExpressRoute.

   Instale o instalador do PowerShell mais recente da [Galeria do PowerShell](https://www.powershellgallery.com/) e importe os módulos do Gerenciador de Recursos do Azure na sessão do PowerShell para começar a usar os cmdlets do ExpressRoute. Você precisará executar o PowerShell como Administrador.

   ```azurepowershell-interactive
   Install-Module Az
   ```

   Importe todos os Az.\* módulos dentro do intervalo de versão semântico conhecido.

   ```azurepowershell-interactive
   Import-Module Az
   ```

   Você também pode importar apenas um módulo selecionado dentro do intervalo de versão semântico conhecido.

   ```azurepowershell-interactive
   Import-Module Az.Network 
   ```

   Entre na sua conta.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Selecionar a assinatura na qual você deseja criar um circuito do ExpressRoute.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Criar um circuito do ExpressRoute.

   Siga as instruções para criar um [circuito do ExpressRoute](expressroute-howto-circuit-arm.md) e para que o circuito seja provisionado pelo provedor de conectividade. Caso seu provedor de conectividade ofereça serviços gerenciados de camada 3, você pode solicitar a ele a habilitação do emparelhamento privado do Azure. Nesse caso, não será necessário seguir as instruções listadas nas seções a seguir. No entanto, se o seu provedor de conectividade não gerenciar o roteamento, após a criação do circuito, continue a configuração executando as próximas etapas.

3. Verifique se o circuito do ExpressRoute está provisionado e habilitado. Use o seguinte exemplo:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   A resposta é semelhante ao seguinte exemplo:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
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
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. Configure o emparelhamento privado do Azure para o circuito. Verifique se você tem os seguintes itens antes de continuar com as próximas etapas:

   * Uma sub-rede /30 para o link principal. A sub-rede não deve fazer parte de nenhum espaço de endereçamento reservado para redes virtuais.
   * Uma sub-rede /30 para o link secundário. A sub-rede não deve fazer parte de nenhum espaço de endereçamento reservado para redes virtuais.
   * Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN.
   * Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes. Você pode usar um número de AS privado para esse emparelhamento. Não use 65515.
   * Opcional:
     * Um Hash MD5, se você optar por usar um.

   Use o seguinte exemplo para configurar o emparelhamento privado do Azure para seu circuito:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Se você optar por usar um hash MD5, use o exemplo a seguir:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Especifique o número de AS como um ASN de emparelhamento, não um ASN de cliente.
   > 
   >

### <a name="getprivate"></a>Para obter detalhes sobre o emparelhamento privado do Azure

Você pode obter detalhes de configuração usando o exemplo a seguir:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="updateprivate"></a>Para atualizar a configuração de emparelhamento privado do Azure

Você pode atualizar qualquer parte da configuração usando o exemplo a seguir. Neste exemplo, a ID da VLAN do circuito está sendo atualizada de 100 para 500.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deleteprivate"></a>Para excluir um emparelhamento privado do Azure

Você pode remover a configuração de emparelhamento executando o seguinte exemplo:

> [!WARNING]
> Você deve garantir que todas as conexões de Alcance Global do ExpressRoute e redes virtuais são removidas antes de executar este exemplo. 
> 
> 

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="public"></a>Emparelhamento público do Azure

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento público do Azure para um circuito do ExpressRoute.

> [!Note]
> Emparelhamento público do Azure foi preterido para circuitos de novo. Para obter mais informações, consulte [emparelhamento de ExpressRoute](expressroute-circuit-peerings.md).
>

### <a name="to-create-azure-public-peering"></a>Criar o emparelhamento público do Azure

1. Importe o módulo do PowerShell para ExpressRoute.

   Instale o instalador do PowerShell mais recente da [Galeria do PowerShell](https://www.powershellgallery.com/) e importe os módulos do Gerenciador de Recursos do Azure na sessão do PowerShell para começar a usar os cmdlets do ExpressRoute. Você precisará executar o PowerShell como Administrador.

   ```azurepowershell-interactive
   Install-Module Az
   ```

   Importe todos os Az.\* módulos dentro do intervalo de versão semântico conhecido.

   ```azurepowershell-interactive
   Import-Module Az
   ```

   Você também pode importar apenas um módulo selecionado dentro do intervalo de versão semântico conhecido.

   ```azurepowershell-interactive
   Import-Module Az.Network
   ```

   Entre na sua conta.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Selecionar a assinatura na qual você deseja criar um circuito do ExpressRoute.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Criar um circuito do ExpressRoute.

   Siga as instruções para criar um [circuito do ExpressRoute](expressroute-howto-circuit-arm.md) e para que o circuito seja provisionado pelo provedor de conectividade. Caso seu provedor de conectividade ofereça serviços gerenciados de camada 3, você pode solicitar a ele a habilitação do emparelhamento público do Azure. Nesse caso, não será necessário seguir as instruções listadas nas seções a seguir. No entanto, se o seu provedor de conectividade não gerenciar o roteamento, após a criação do circuito, continue a configuração executando as próximas etapas.

3. Verifique se o circuito do ExpressRoute está provisionado e habilitado. Use o seguinte exemplo:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   A resposta é semelhante ao seguinte exemplo:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
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
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
4. Configure o emparelhamento público do Azure para o circuito. Verifique se você tem as informações a seguir antes de prosseguir.

   * Uma sub-rede /30 para o link principal. Precisa ser um prefixo IPv4 público válido.
   * Uma sub-rede /30 para o link secundário. Precisa ser um prefixo IPv4 público válido.
   * Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN.
   * Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes.
   * Opcional:
     * Um Hash MD5, se você optar por usar um.

   Executar o seguinte exemplo para configurar o emparelhamento público do Azure para seu circuito

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Se você optar por usar um hash MD5, use o exemplo a seguir:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > Especifique o número de AS como um ASN de emparelhamento, não um ASN de cliente.
   > 
   >

### <a name="getpublic"></a>Para obter detalhes sobre o emparelhamento público do Azure

Você pode obter detalhes de configuração usando o seguinte cmdlet:

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="updatepublic"></a>Para atualizar a configuração de emparelhamento público do Azure

Você pode atualizar qualquer parte da configuração usando o exemplo a seguir. Neste exemplo, a ID da VLAN do circuito está sendo atualizada de 200 para 600.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="deletepublic"></a>Para excluir o emparelhamento público do Azure

Você pode remover a configuração de emparelhamento executando o seguinte exemplo:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>Próximas etapas

A próxima etapa será [Vincular uma Rede Virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Para saber mais sobre fluxos de trabalho do ExpressRoute, confira [Fluxos de trabalho do ExpressRoute](expressroute-workflows.md).
* Para obter mais informações sobre o emparelhamento de circuito, veja [Circuitos e domínios de roteamento do ExpressRoute](expressroute-circuit-peerings.md).
* Para saber mais sobre redes virtuais, confira [Visão geral da rede virtual](../virtual-network/virtual-networks-overview.md).
