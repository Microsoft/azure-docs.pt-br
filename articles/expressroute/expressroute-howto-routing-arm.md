---
title: 'Tutorial: Configurar o emparelhamento para o circuito do ExpressRoute – Azure PowerShell'
description: Esse tutorial mostra como criar e gerenciar a configuração de roteamento de um circuito do ExpressRoute no Modelo de implantação do Resource Manager usando o PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.openlocfilehash: 7cfd378ae621192cd98b482b66c85c3dcd3ca454
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101721932"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-powershell"></a>Tutorial: Criar e modificar o emparelhamento de um circuito do ExpressRoute usando o PowerShell

Esse tutorial ajuda você a criar e gerenciar a configuração de roteamento de um circuito do ExpressRoute no Modelo de implantação do Resource Manager usando o PowerShell. Você também pode verificar o status, atualizar ou excluir e desprovisionar emparelhamentos de um circuito do ExpressRoute. Se quiser usar um método diferente para trabalhar com seu circuito, selecione um artigo na lista a seguir:

> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI do Azure](howto-routing-cli.md)
> * [Emparelhamento público](about-public-peering.md)
> * [Vídeo – Emparelhamento privado](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo – Emparelhamento da Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clássico)](expressroute-howto-routing-classic.md)
> 

Estas instruções se aplicam apenas a circuitos criados com provedores de serviço que oferecem serviços de conectividade de Camada 2. Se você estiver usando um provedor de serviços que ofereça serviços gerenciados de Camada 3 (normalmente um IPVPN, como MPLS), seu provedor de conectividade configurará e gerenciará o roteamento para você.

> [!IMPORTANT]
> Atualmente, não anunciamos emparelhamentos configurados pelos provedores de serviço por meio do portal de gerenciamento de serviço. Estamos trabalhando para habilitar esse recurso em breve. Verifique com seu provedor de serviços antes de configurar os emparelhamentos via protocolo BGP.
> 

Você pode configurar o emparelhamento privado e o emparelhamento da Microsoft para um circuito do ExpressRoute (o emparelhamento público do Azure está preterido para novos circuitos). Os emparelhamentos poderão ser configurados em qualquer ordem que você escolher. No entanto, você deve concluir a configuração de um emparelhamento por vez. Para obter mais informações sobre o roteamento de domínios e emparelhamentos, consulte [Domínios de roteamento do ExpressRoute](expressroute-circuit-peerings.md). Para obter informações sobre o emparelhamento público, confira [Emparelhamento público do ExpressRoute](about-public-peering.md).

Neste tutorial, você aprenderá a:
> [!div class="checklist"]
> - Configurar, atualizar e excluir o emparelhamento da Microsoft para um circuito
> - Configurar, atualizar e excluir o emparelhamento privado do Azure para um circuito

## <a name="prerequisites"></a>Pré-requisitos

* Verifique se você analisou as seguintes páginas antes de começar a configuração:
    * [Pré-requisitos](expressroute-prerequisites.md) 
    * [Requisitos de roteamento](expressroute-routing.md)
    * [Fluxos de trabalho](expressroute-workflows.md)
* Você deve ter um circuito do ExpressRoute ativo. Antes de continuar, siga as instruções para [Criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md) e para que o circuito seja habilitado pelo provedor de conectividade. O circuito do ExpressRoute deve estar em estado provisionado e habilitado para que você execute os cmdlets deste artigo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="microsoft-peering"></a><a name="msft"></a>Emparelhamento da Microsoft

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento da Microsoft para um circuito do ExpressRoute.

> [!IMPORTANT]
> O emparelhamento da Microsoft de circuitos do ExpressRoute configurados antes de 1º de agosto de 2017 terá todos os prefixos de serviço anunciados através do emparelhamento da Microsoft, mesmo que os filtros de rota não estejam definidos. O emparelhamento da Microsoft de circuitos de ExpressRoute configurados em ou após 1º de agosto de 2017 não terá nenhum prefixo anunciado até que um filtro de rota seja anexado ao circuito. Para obter mais informações, consulte [Configurar um filtro de rota para o emparelhamento da Microsoft](how-to-routefilter-powershell.md).
> 

### <a name="to-create-microsoft-peering"></a>Criar emparelhamento da Microsoft

1. Entrar e selecione sua assinatura.

   Se você instalou o PowerShell localmente, entre. Se estiver usando o Azure Cloud Shell, você poderá ignorar esta etapa.

   ```azurepowershell
   Connect-AzAccount
   ```

   Selecionar a assinatura na qual você deseja criar um circuito do ExpressRoute.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "<subscription ID>"
   ```
2. Criar um circuito do ExpressRoute.

   Siga as instruções para criar um [circuito do ExpressRoute](expressroute-howto-circuit-arm.md) e para que o circuito seja provisionado pelo provedor de conectividade. Caso seu provedor de conectividade ofereça serviços gerenciados de camada 3, você pode solicitar a ele a habilitação do emparelhamento da Microsoft. Não será necessário seguir as instruções listadas nas próximas seções. No entanto, se o provedor de conectividade não gerenciar o roteamento para você, após a criação do circuito, prossiga com a configuração usando as próximas etapas. 

3. Verifique se o circuito do ExpressRoute está provisionado e também habilitado. Use o seguinte exemplo:

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
4. Configurar o emparelhamento da Microsoft para o circuito. Verifique se você tem as informações a seguir antes de continuar.

   * Uma sub-rede /30 ou /126 para o link principal. O bloco de endereços precisa ser um prefixo IPv4 ou IPv6 público válido próprio e registrado em um RIR/IRR.
   * Uma sub-rede /30 ou /126 para o link secundário. O bloco de endereços precisa ser um prefixo IPv4 ou IPv6 público válido próprio e registrado em um RIR/IRR.
   * Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN.
   * Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes.
   * Prefixos anunciados: forneça uma lista com todos os prefixos que planeja anunciar na sessão do BGP. Somente prefixos de endereços IP públicos são aceitos. Caso você planeje enviar um conjunto de prefixos, poderá enviar uma lista separada por vírgulas. Esses prefixos devem ser registrados em seu nome em um RIR/IRR. Sessões BGP IPv4 exigem que prefixos IPv4 anunciados, e sessões BGP IPv6 exigem prefixos IPv6 anunciados. 
   * Nome do registro de roteamento: você pode especificar o RIR/IRR com base no qual o número de AS e os prefixos estão registrados.
   * Opcional:
     * ASN do cliente: se você estiver anunciando prefixos não registrados com o número AS de emparelhamento, especifique o número AS com o qual eles estão registrados.
     * Um Hash MD5, se você optar por usar um.

> [!IMPORTANT]
> A Microsoft verifica se os "Prefixos públicos publicados" e o "ASN de ponto" (ou "cliente ASN de cliente") especificados estão atribuídos a você no registro de roteamento da Internet. Se você estiver recebendo os prefixos públicos de outra entidade e se a atribuição não for registrada com o registro de roteamento, a validação automática não será concluída e exigirá validação manual. Se a validação automática falhar, você verá "AdvertisedPublicPrefixesState" definido como "Requer validação" na saída do "Get-AzExpressRouteCircuitPeeringConfig" (confira "Para obter os detalhes do emparelhamento da Microsoft" na seção a seguir). 
> 
> Se você vir a mensagem "Requer validação", colete os documentos que mostram que os prefixos públicos foram atribuídos à organização pela entidade que está listada como proprietária dos prefixos no registro de roteamento e envie esses documentos para validação manual, abrindo um tíquete de suporte. 
> 

   Use o exemplo a seguir para configurar o emparelhamento da Microsoft para seu circuito:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Add-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

### <a name="to-get-microsoft-peering-details"></a><a name="getmsft"></a>Obter detalhes de emparelhamento da Microsoft

Você pode obter detalhes de configuração usando o exemplo a seguir:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Atualizar a configuração de emparelhamento da Microsoft

Você pode atualizar qualquer parte da configuração usando o exemplo a seguir:

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv4 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PeerAddressType IPv6 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "3FFE:FFFF:0:CD31::/120" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-private-peering"></a><a name="private"></a>Emparelhamento privado do Azure

Esta seção ajuda você a criar, obter, atualizar e excluir a configuração de emparelhamento privado do Azure para um circuito do ExpressRoute.

> [!IMPORTANT]
> A compatibilidade com IPv6 para emparelhamento privado está atualmente em **Versão Prévia Pública**. 
> 
> 

### <a name="to-create-azure-private-peering"></a>Criar um emparelhamento privado do Azure

1. Importe o módulo do PowerShell para ExpressRoute.

   Instale o mais recente instalador do PowerShell da [Galeria do PowerShell](https://www.powershellgallery.com/). Em seguida, importe os módulos do Azure Resource Manager para a sessão do PowerShell a fim de começar a usar os cmdlets do ExpressRoute. Você precisará executar o PowerShell como Administrador.

   ```azurepowershell-interactive
   Install-Module Az
   ```

   Importe todos os módulos do AZ\* dentro do intervalo de versão semântico conhecido.

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

   Siga as instruções para criar um [circuito do ExpressRoute](expressroute-howto-circuit-arm.md) e para que o circuito seja provisionado pelo provedor de conectividade. Caso seu provedor de conectividade ofereça serviços gerenciados de camada 3, você pode solicitar a ele a habilitação do emparelhamento privado do Azure. Não será necessário seguir as instruções listadas nas próximas seções. No entanto, se o provedor de conectividade não gerenciar o roteamento para você, após a criação do circuito, prossiga com a configuração usando as próximas etapas.

3. Verifique se o circuito do ExpressRoute está provisionado e também habilitado. Use o seguinte exemplo:

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
4. Configure o emparelhamento privado do Azure para o circuito. Verifique se você tem os seguintes itens antes de prosseguir para as próximas etapas:

   * Um par de sub-redes que não fazem parte de nenhum espaço de endereço reservado para redes virtuais. Uma sub-rede será usada para o link principal enquanto a outra será usada para o link secundário. De cada uma dessas sub-redes, você atribuirá o primeiro endereço IP utilizável ao seu roteador, já que a Microsoft usa o segundo IP utilizável para o roteador dela. Você tem três opções para este par de sub-redes:
       * IPv4: duas sub-redes /30.
       * IPv6: duas sub-redes /126.
       * Ambas: duas sub-redes /30 e duas sub-redes /126.
   * Uma ID válida de VLAN para estabelecer esse emparelhamento. Verifique se nenhum outro emparelhamento no circuito usa a mesma ID de VLAN.
   * Número de AS para emparelhamento. Você pode usar um número de AS de 2 e de 4 bytes. Você pode usar um número de AS privado para esse emparelhamento. Verifique se você não está usando 65515.
   * Opcional:
     * Um Hash MD5, se você optar por usar um.

   Use o seguinte exemplo para configurar o emparelhamento privado do Azure para seu circuito:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Se você optar por usar um hash MD5, use o exemplo a seguir:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "3FFE:FFFF:0:CD30::/126" -SecondaryPeerAddressPrefix "3FFE:FFFF:0:CD30::4/126" -VlanId 200 -PeerAddressType IPv6 -SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Especifique o número de AS como um ASN de emparelhamento, não um ASN de cliente.
   > 
   >

### <a name="to-get-azure-private-peering-details"></a><a name="getprivate"></a>Para obter detalhes sobre o emparelhamento privado do Azure

Você pode obter detalhes de configuração usando o exemplo a seguir:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Atualizar a configuração de emparelhamento privado do Azure

Você pode atualizar qualquer parte da configuração usando o exemplo a seguir. Neste exemplo, a ID da VLAN do circuito está sendo atualizada de 200 para 500.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 500

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="clean-up-resources"></a>Limpar recursos

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Excluir emparelhamento da Microsoft

Você pode remover a configuração de emparelhamento executando o seguinte cmdlet:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Excluir um emparelhamento privado do Azure

Você pode remover a configuração de emparelhamento executando o seguinte exemplo:

> [!WARNING]
> Você deve garantir que todas as conexões de Alcance Global do ExpressRoute e redes virtuais são removidas antes de executar este exemplo. 
> 

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="next-steps"></a>Próximas etapas

Após configurar o emparelhamento privado do Azure, você poderá criar um gateway do ExpressRoute para vincular uma rede virtual ao circuito. 

> [!div class="nextstepaction"]
> [Configurar um gateway de rede virtual para ExpressRoute](expressroute-howto-add-gateway-resource-manager.md)
