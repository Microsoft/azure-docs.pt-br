---
title: 'Azure ExpressRoute: verificar conectividade-guia de solução de problemas'
description: Esta página fornece instruções sobre solução de problemas e validação de conectividade de ponta a ponta de um circuito ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 10/31/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: af4ef156cccded6afe2db09628446a6ffe1ad53a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92204632"
---
# <a name="verifying-expressroute-connectivity"></a>Verificando a conectividade do ExpressRoute
Este artigo ajuda você a verificar e solucionar problemas de conectividade do ExpressRoute. O ExpressRoute estende uma rede local para a nuvem da Microsoft por meio de uma conexão privada que é normalmente facilitada por um provedor de conectividade. A conectividade do ExpressRoute tradicionalmente envolve três zonas de rede distintas, da seguinte maneira:

-   Rede do Cliente
-   Rede do Provedor
-   Microsoft Datacenter

> [!NOTE]
> No modelo de conectividade do ExpressRoute Direct (oferecido com largura de banda de 10/100 Gbps), os clientes podem se conectar diretamente à porta dos roteadores do Microsoft Enterprise Edge (MSEE). Portanto, no modelo de conectividade direta, há apenas zonas de rede do cliente e da Microsoft.
>


A finalidade deste documento é ajudar o usuário a identificar se e onde existe um problema de conectividade. Assim, para ajudar a buscar suporte da equipe apropriada para resolver um problema. Se o suporte da Microsoft for necessário para resolver um problema, abra um tíquete de suporte com [Suporte da Microsoft][Support].

> [!IMPORTANT]
> Este documento tem como intenção ajudar você a diagnosticar e corrigir problemas simples. Ela não deve ser usado como uma substituição ao suporte da Microsoft. Abra um tíquete de suporte com o [Suporte da Microsoft][Support] se não conseguir resolver o problema usando as diretrizes fornecidas.
>
>

## <a name="overview"></a>Visão geral
O diagrama a seguir mostra a conectividade lógica de uma rede do cliente de rede da Microsoft usando o ExpressRoute.
[![1]][1]

No diagrama acima, os números indicam os principais pontos de rede. Esses pontos de rede são referenciados neste artigo às vezes por seu número associado. Dependendo do modelo de conectividade do ExpressRoute, colocalização de Exchange de nuvem, conexão Ethernet ponto a ponto ou qualquer para qualquer (IPVPN)--os pontos de rede 3 e 4 podem ser comutadores (dispositivos de camada 2) ou roteadores (dispositivos de camada 3). No modelo de conectividade direta, não há pontos de rede 3 e 4; em vez disso, o CEs (2) está diretamente conectado ao MSEEs por meio de fibra escura. Os principais pontos de rede ilustrados são os seguintes:

1.  Dispositivo de computação de cliente (por exemplo, um servidor ou PC)
2.  CEs: roteadores de borda do cliente 
3.  PEs (voltados para CE): roteadores/comutadores de borda do provedor que estão voltados para os roteadores de borda do cliente. Mencionados como PE CEs neste documento.
4.  PEs (voltados para MSEE): roteadores/comutadores de borda do provedor que estão voltados para MSEEs. Mencionados como PE-MSEEs neste documento.
5.  MSEEs: roteadores ExpressRoute do MSEE (Microsoft Enterprise Edge)
6.  Gateway de Rede Virtual (VNet)
7.  Dispositivo de computação na VNet do Azure

Se os modelos de colocalização de troca de nuvem, Ethernet ponto a ponto ou de conectividade direta forem usados, CEs (2) estabelecerá o emparelhamento via protocolo BGP com MSEEs (5). 

Se o modelo de conectividade de qualquer para qualquer (IPVPN) for usado, PE-MSEEs (4) estabelecer o emparelhamento via protocolo BGP com MSEEs (5). PE-MSEEs propagar as rotas recebidas da Microsoft de volta para a rede do cliente por meio da rede do provedor de serviços do IPVPN.

> [!NOTE]
>Para alta disponibilidade, a Microsoft estabelece uma conectividade paralela totalmente redundante entre pares de MSEEs (5) e PE-MSEEs (4). Um caminho de rede paralelo totalmente redundante também é incentivado entre A rede do cliente e o par de PE-CEs. Para obter mais informações sobre a alta disponibilidade, consulte o artigo [projetando para alta disponibilidade com o ExpressRoute][HA]
>
>

A seguir estão as etapas lógicas, em solução de problemas do circuito do ExpressRoute:

* [Verificar o estado e o provisionamento do circuito](#verify-circuit-provisioning-and-state)
  
* [Validar Configuração de Emparelhamento](#validate-peering-configuration)
  
* [Validar ARP](#validate-arp)
  
* [Validar BGP e rotas no MSEE](#validate-bgp-and-routes-on-the-msee)
  
* [Confirmar o fluxo de tráfego](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>Verificar o estado e o provisionamento do circuito
O provisionamento de um circuito do ExpressRoute estabelece conexões de camada 2 redundantes entre CEs/PE-MSEEs (2)/(4) e MSEEs (5). Para obter mais informações sobre como criar, modificar, provisionar e verificar um circuito do ExpressRoute, consulte o artigo [Create and modify an ExpressRoute circuit][CreateCircuit] (Criar e modificar um circuito do ExpressRoute).

>[!TIP]
>Uma chave de serviço identifica exclusivamente um circuito do ExpressRoute. Caso você precise de assistência da Microsoft ou de um parceiro de ExpressRoute para solucionar um problema de ExpressRoute, forneça a chave de serviço para identificar prontamente o circuito.
>
>

### <a name="verification-via-the-azure-portal"></a>Verificação por meio do Portal do Azure
Na portal do Azure, abra a folha circuito do ExpressRoute. Na seção ![3][3] da folha, o ExpressRoute Essentials é listado conforme mostrado na seguinte captura de tela:

![4][4]    

Nas Informações Gerais do ExpressRoute, o *Status do circuito* indica o status do circuito no lado da Microsoft. *Status do provedor* indica se o circuito foi *Provisionado/Não provisionado* no lado do provedor de serviços. 

Para que um circuito do ExpressRoute esteja operacional, o *Status do circuito* deve ser *Habilitado* e o *Status do provedor* deve ser *Provisionado*.

> [!NOTE]
> Depois de configurar um circuito do ExpressRoute, se o *status do circuito* for com o status não habilitado, entre em contato com [suporte da Microsoft][Support]. Por outro lado, se o *status do provedor* for esbarrado no status não provisionado, entre em contato com seu provedor de serviços.
>
>

### <a name="verification-via-powershell"></a>Verificação por meio do PowerShell
Para listar todos os circuitos do ExpressRoute em um grupo de recursos, use o seguinte comando:

```azurepowershell
Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"
```

>[!TIP]
>Se você estiver procurando o nome de um grupo de recursos, poderá obtê-lo listando todos os grupos de recursos em sua assinatura, usando o comando *Get-AzResourceGroup*
>


Para selecionar um circuito do ExpressRoute específico em um grupo de recursos, use o seguinte comando:

```azurepowershell
Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
```

Uma resposta de exemplo é:

```output
Name                             : Test-ER-Ckt
ResourceGroupName                : Test-ER-RG
Location                         : westus2
Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_UnlimitedData",
                                    "Tier": "Standard",
                                    "Family": "UnlimitedData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             : 
ServiceProviderProperties        : {
                                    "ServiceProviderName": "****",
                                    "PeeringLocation": "******",
                                    "BandwidthInMbps": 100
                                    }
ServiceKey                       : **************************************
Peerings                         : []
Authorizations                   : []
```

Para confirmar se um circuito do ExpressRoute está funcionando, preste atenção especial aos seguintes campos:

```output
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
```

> [!NOTE]
> Depois de configurar um circuito do ExpressRoute, se o *status do circuito* for com o status não habilitado, entre em contato com [suporte da Microsoft][Support]. Por outro lado, se o *status do provedor* for esbarrado no status não provisionado, entre em contato com seu provedor de serviços.
>
>

## <a name="validate-peering-configuration"></a>Validar Configuração de Emparelhamento
Depois que o provedor de serviços tiver concluído o provisionamento do circuito do ExpressRoute, várias configurações de roteamento com base em eBGP poderão ser criadas no circuito do ExpressRoute entre CEs/MSEE-PEs (2)/(4) e MSEEs (5). Cada circuito do ExpressRoute pode ter: emparelhamento privado do Azure (tráfego para redes virtuais privadas no Azure) e/ou emparelhamento da Microsoft (tráfego para pontos de extremidade públicos de PaaS e SaaS). Para obter mais informações sobre como criar e modificar a configuração de roteamento, consulte o artigo [Create and modify routing for an ExpressRoute circuit][CreatePeering] (Criar e modificar o roteamento para um circuito do ExpressRoute).

### <a name="verification-via-the-azure-portal"></a>Verificação por meio do Portal do Azure

> [!NOTE]
> No modelo de conectividade IPVPN, os provedores de serviço lidam com a responsabilidade de configurar os emparelhamentos (serviços de camada 3). Nesse modelo, depois que o provedor de serviços tiver configurado um emparelhamento e se o emparelhamento estiver em branco no portal, tente atualizar a configuração do circuito usando o botão Atualizar no Portal. Esta operação efetuará pull da configuração de roteamento atual do circuito. 
>

No portal do Azure, o status de um emparelhamento de circuito do ExpressRoute pode ser verificado na folha circuito do ExpressRoute. Na seção ![3][3] da folha, os emparelhamentos do ExpressRoute seriam listados, conforme mostrado na seguinte captura de tela:

![5][5]

No exemplo anterior, conforme observado, o emparelhamento privado do Azure é provisionado, enquanto os emparelhamentos do Azure público e da Microsoft não são provisionados. Um contexto de emparelhamento provisionado com êxito também teria as sub-redes ponto a ponto primária e secundária listadas. As sub-redes/30 são usadas para o endereço IP da interface do MSEEs e CEs/PE-MSEEs. Para os emparelhamentos provisionados, a listagem também indica quem modificou a configuração pela última vez. 

> [!NOTE]
> Se a habilitação de um emparelhamento falhar, verifique se as sub-redes primárias e secundárias atribuídas correspondem à configuração no CE/PE-MSEE vinculado. Verifique também se o *vlanid*, o *AzureASN* e o *PeerASN* corretos são usados em MSEEs e se esses valores são mapeados para aqueles usados no CE/PE-MSEE vinculado. Se o hash MD5 for escolhido, a chave compartilhada deverá ser a mesma no par MSEE e PE-MSEE/CE. A chave compartilhada configurada anteriormente não seria exibida por motivos de segurança. Se você precisar alterar qualquer uma dessas configurações em um roteador MSEE, consulte [criar e modificar o roteamento para um circuito do ExpressRoute][CreatePeering].  
>

> [!NOTE]
> Em uma sub-rede/30 atribuída para a interface, a Microsoft escolherá o segundo endereço IP utilizável da sub-rede para a interface MSEE. Portanto, verifique se o primeiro endereço IP utilizável da sub-rede foi atribuído no CE/PE-MSEE emparelhado.
>


### <a name="verification-via-powershell"></a>Verificação por meio do PowerShell
Para obter os detalhes de configuração de emparelhamento privado do Azure, use os seguintes comandos:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

Uma resposta de exemplo para um emparelhamento privado configurado com êxito é:

```output
Name                       : AzurePrivatePeering
Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
Etag                       : W/"################################"
PeeringType                : AzurePrivatePeering
AzureASN                   : 12076
PeerASN                    : 123##
PrimaryPeerAddressPrefix   : 172.16.0.0/30
SecondaryPeerAddressPrefix : 172.16.0.4/30
PrimaryAzurePort           : 
SecondaryAzurePort         : 
SharedKey                  : 
VlanId                     : 200
MicrosoftPeeringConfig     : null
ProvisioningState          : Succeeded
```

 Um contexto de emparelhamento habilitado com êxito teria os prefixos dos endereços primário e secundário listados. As sub-redes/30 são usadas para o endereço IP da interface do MSEEs e CEs/PE-MSEEs.

Para obter os detalhes de configuração de emparelhamento público do Azure, use os seguintes comandos:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
```

Para obter os detalhes de configuração de emparelhamento da Microsoft, use os seguintes comandos:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

Se um emparelhamento não for configurado, haverá uma mensagem de erro. Uma resposta de exemplo, quando o emparelhamento indicado (emparelhamento público do Azure neste exemplo) não está configurado no circuito:

```azurepowershell
Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
At line:1 char:1
    + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
        + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand
```

> [!NOTE]
> Se a habilitação de um emparelhamento falhar, verifique se as sub-redes primárias e secundárias atribuídas correspondem à configuração no CE/PE-MSEE vinculado. Verifique também se o *vlanid*, o *AzureASN* e o *PeerASN* corretos são usados em MSEEs e se esses valores são mapeados para aqueles usados no CE/PE-MSEE vinculado. Se o hash MD5 for escolhido, a chave compartilhada deverá ser a mesma no par MSEE e PE-MSEE/CE. A chave compartilhada configurada anteriormente não seria exibida por motivos de segurança. Se você precisar alterar qualquer uma dessas configurações em um roteador MSEE, consulte [criar e modificar o roteamento para um circuito do ExpressRoute][CreatePeering].  
>
>

> [!NOTE]
> Em uma sub-rede/30 atribuída para a interface, a Microsoft escolherá o segundo endereço IP utilizável da sub-rede para a interface MSEE. Portanto, verifique se o primeiro endereço IP utilizável da sub-rede foi atribuído no CE/PE-MSEE emparelhado.
>

## <a name="validate-arp"></a>Validar ARP

A tabela ARP fornece um mapeamento do endereço IP e do endereço MAC para um emparelhamento específico. A tabela ARP para um emparelhamento de circuito de ExpressRoute fornece as seguintes informações para cada interface (primária e secundária):
* Mapeamento do endereço IP da interface do roteador local para o endereço MAC
* Mapeamento do endereço IP da interface do roteador de ExpressRoute para o endereço MAC
* A idade do mapeamento de tabelas ARP pode ajudar a validar a configuração de camada 2 e solucionar problemas de conectividade de camada 2 básicos.


Consulte [obtendo tabelas ARP no documento do modelo de implantação do Resource Manager][ARP] , para saber como exibir a tabela ARP de um emparelhamento de ExpressRoute e sobre como usar as informações para solucionar problemas de conectividade de camada 2.


## <a name="validate-bgp-and-routes-on-the-msee"></a>Validar BGP e rotas no MSEE

Para obter a tabela de roteamento de MSEE no caminho *primário* para o contexto de roteamento *privado* , use o seguinte comando:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable -DevicePath Primary -ExpressRouteCircuitName ******* -PeeringType AzurePrivatePeering -ResourceGroupName ****
```

Uma resposta de exemplo é:

```output
Network : 10.1.0.0/16
NextHop : 10.17.17.141
LocPrf  : 
Weight  : 0
Path    : 65515

Network : 10.1.0.0/16
NextHop : 10.17.17.140*
LocPrf  : 
Weight  : 0
Path    : 65515

Network : 10.2.20.0/25
NextHop : 172.16.0.1
LocPrf  : 
Weight  : 0
Path    : 123##
```

> [!NOTE]
> Se o estado de um emparelhamento eBGP entre um MSEE e um CE/PE-MSEE estiver ativo ou ocioso, verifique se as sub-redes emparelhadas primárias e secundárias atribuídas correspondem à configuração no CE/PE-MSEE vinculado. Verifique também se o *vlanid*, o *AzureAsn* e o *PeerAsn* corretos são usados em MSEEs e se esses valores são mapeados para aqueles usados no PE-MSEE/CE vinculado. Se o hash MD5 for escolhido, a chave compartilhada deverá ser a mesma no par MSEE e CE/PE-MSEE. Se você precisar alterar qualquer uma dessas configurações em um roteador MSEE, consulte [criar e modificar o roteamento para um circuito do ExpressRoute][CreatePeering].
>


> [!NOTE]
> Se determinados destinos não estiverem acessíveis em um emparelhamento, verifique a tabela de rotas do MSEEs para o contexto de emparelhamento correspondente. Se um prefixo correspondente (pode ser IP de NATe) estiver presente na tabela de roteamento, verifique se há firewalls/NSG/ACLs no caminho que está bloqueando o tráfego.
>


O exemplo a seguir mostra a resposta do comando para um emparelhamento que não existe:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
StatusCode: 400
```

## <a name="confirm-the-traffic-flow"></a>Confirmar o fluxo de tráfego
Para obter as estatísticas de tráfego combinadas dos caminhos primário e secundário – bytes em entrada e em saída – de um contexto de emparelhamento, use o seguinte comando:

```azurepowershell
Get-AzExpressRouteCircuitStats -ResourceGroupName $RG -ExpressRouteCircuitName $CircuitName -PeeringType 'AzurePrivatePeering'
```

Uma saída de exemplo do comando é:

```output
PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
-------------- --------------- ---------------- -----------------
     240780020       239863857        240565035         239628474
```

Uma saída de exemplo do comando para um emparelhamento inexistente é:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
StatusCode: 400
```

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações ou ajuda, confira os seguintes links:

- [Suporte da Microsoft][Support]
- [Criar e modificar um circuito do ExpressRoute][CreateCircuit]
- [Criar e modificar o roteamento de um circuito do ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "Conectividade lógica do ExpressRoute"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Ícone Todos os Recursos"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Ícone Visão Geral"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Captura de tela de exemplo do ExpressRoute Essentials"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Captura de tela de exemplo do ExpressRoute Essentials"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: ./expressroute-howto-circuit-portal-resource-manager.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[ARP]: ./expressroute-troubleshooting-arp-resource-manager.md
[HA]: ./designing-for-high-availability-with-expressroute.md
[DR-Pvt]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md