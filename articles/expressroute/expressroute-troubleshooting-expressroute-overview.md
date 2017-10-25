---
title: "Verificando a conectividade: Guia de Solução de Problemas do Azure ExpressRoute | Microsoft Docs"
description: "Esta página fornece instruções sobre solução de problemas e validação de conectividade de ponta a ponta de um circuito ExpressRoute."
documentationcenter: na
services: expressroute
author: rambk
manager: tracsman
editor: 
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: cherylmc
ms.openlocfilehash: 2bcd95da36cd577ece8ad87d00c3e276f28ad340
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="verifying-expressroute-connectivity"></a>Verificando a conectividade do ExpressRoute
O ExpressRoute, que estende uma rede local para a Microsoft Cloud por uma conexão privada que é facilitada por um provedor de conectividade, envolve as três seguintes zonas da rede distintas:

-   Rede do Cliente
-   Rede do Provedor
-   Microsoft Datacenter

A finalidade deste documento é ajudar os usuários a identificar onde (ou mesmo se) existe um problema de conectividade e em qual zona, para assim buscar ajuda da equipe apropriada para resolver o problema. Se o suporte da Microsoft for necessário para resolver um problema, abra um tíquete de suporte com [Suporte da Microsoft][Support].

> [!IMPORTANT]
> Este documento tem como intenção ajudar você a diagnosticar e corrigir problemas simples. Ela não deve ser usado como uma substituição ao suporte da Microsoft. Abra um tíquete de suporte com o [Suporte da Microsoft][Support] se não conseguir resolver o problema usando as diretrizes fornecidas.
>
>

## <a name="overview"></a>Visão geral
O diagrama a seguir mostra a conectividade lógica de uma rede do cliente de rede da Microsoft usando o ExpressRoute.
[![1]][1]

No diagrama acima, os números indicam os principais pontos de rede. Os pontos de rede são referenciados com frequência neste artigo por seu número associado.

Dependendo do modelo de conectividade do ExpressRoute (colocalização do Cloud Exchange, Conexão de Ethernet ponto a ponto ou IPVPN (de qualquer para qualquer)), os pontos de rede 3 e 4 podem ser comutadores (dispositivos de Camada 2). Os principais pontos de rede ilustrados são os seguintes:

1.  Dispositivo de computação de cliente (por exemplo, um servidor ou PC)
2.  CEs: roteadores de borda do cliente 
3.  PEs (voltados para CE): roteadores/comutadores de borda do provedor que estão voltados para os roteadores de borda do cliente. Mencionados como PE CEs neste documento.
4.  PEs (voltados para MSEE): roteadores/comutadores de borda do provedor que estão voltados para MSEEs. Mencionados como PE-MSEEs neste documento.
5.  MSEEs: roteadores ExpressRoute do MSEE (Microsoft Enterprise Edge)
6.  Gateway de Rede Virtual (VNet)
7.  Dispositivo de computação na VNet do Azure

Se os modelos de conectividade de colocalização do Cloud Exchange, Conexão de Ethernet ponto a ponto forem usados, o roteador de borda do cliente (2) poderá estabelecer emparelhamento via protocolo BGP com MSEEs (5). Os pontos de rede 3 e 4 ainda existiriam, mas seriam um tanto transparentes como dispositivos de rede de Camada 2.

Se o modelo de conectividade de IPVPN (de qualquer para qualquer) for usado, os PEs (voltados para MSEEs) (4) poderão estabelecer emparelhamento via protocolo BGP com MSEEs (5). As rotas seriam então propagadas de volta para rede do cliente através da rede do provedor de serviços IPVPN.

>[!NOTE]
>Para alta disponibilidade do ExpressRoute, a Microsoft exige um par redundante de sessões BGP entre MSEEs (5) e PE-MSEEs (4). Recomenda-se também mobilizar um par redundante de caminhos de rede entre a rede do cliente e PE-CEs. No entanto, no modelo de conexão IPVPN (de qualquer para qualquer), um único dispositivo CE (2) pode estar conectado a um ou mais PEs (3).
>
>

Para validar um circuito ExpressRoute, as etapas a seguir são abordadas (com o ponto de rede indicado pelo número associado):
1. [Validar o estado e o provisionamento do circuito (5)](#validate-circuit-provisioning-and-state)
2. [Validar que pelo menos um emparelhamento do ExpressRoute está configurado (5)](#validate-peering-configuration)
3. [Validar ARP entre a Microsoft e o provedor de serviços (link entre 4 e 5)](#validate-arp-between-microsoft-and-the-service-provider)
4. [Validar o BGP e rotas no MSEE (BGP entre 4 a 5 ou então 5 a 6 se houver uma VNet conectada)](#validate-bgp-and-routes-on-the-msee)
5. [Verificar as estatísticas de tráfego (tráfego passando por 5)](#check-the-traffic-statistics)

Mais verificações e validações serão adicionadas no futuro, retorne e verifique mensalmente!

##<a name="validate-circuit-provisioning-and-state"></a>Validar o estado e o provisionamento do circuito
Independentemente do modelo de conectividade, um circuito do ExpressRoute deve ser criado e, portanto, uma chave de serviço gerada para o provisionamento do circuito. O provisionamento de um circuito do ExpressRoute estabelece uma conexão de Camada 2 redundante entre PE-MSEEs (4) e MSEEs (5). Para obter mais informações sobre como criar, modificar, provisionar e verificar um circuito do ExpressRoute, consulte o artigo [Create and modify an ExpressRoute circuit][CreateCircuit] (Criar e modificar um circuito do ExpressRoute).

>[!TIP]
>Uma chave de serviço identifica exclusivamente um circuito do ExpressRoute. Essa chave é necessária para a maioria dos comandos do PowerShell mencionadas neste documento. Além disso, caso você necessite de assistência da Microsoft ou de um parceiro do ExpressRoute para solucionar um problema do ExpressRoute, forneça a chave de serviço para identificar prontamente o circuito.
>
>

###<a name="verification-via-the-azure-portal"></a>Verificação por meio do Portal do Azure
No Portal do Azure, o status de um circuito do ExpressRoute pode ser verificado selecionando ![2][2] na barra de menu do lado esquerdo e selecionando o circuito do ExpressRoute. Selecionar um circuito do ExpressRoute listado em "Todos os recursos" abrirá a folha do circuito do ExpressRoute. Na seção ![3][3] da folha, as informações gerais sobre o ExpressRoute são listadas conforme mostrado na seguinte captura de tela:

![4][4]    

Nas Informações Gerais do ExpressRoute, o *Status do circuito* indica o status do circuito no lado da Microsoft. *Status do provedor* indica se o circuito foi *Provisionado/Não provisionado* no lado do provedor de serviços. 

Para que um circuito do ExpressRoute esteja operacional, o *Status do circuito* deve ser *Habilitado* e o *Status do provedor* deve ser *Provisionado*.

>[!NOTE]
>Se o *Status do circuito* não for habilitado, entre em contato com o [Suporte da Microsoft][Support]. Se o *Status do provedor* não for provisionado, entre em contato com seu provedor de serviços.
>
>

###<a name="verification-via-powershell"></a>Verificação por meio do PowerShell
Para listar todos os circuitos do ExpressRoute em um grupo de recursos, use o seguinte comando:

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Você pode obter o nome do seu grupo de recursos por meio do Azure. Consulte a subseção anterior deste documento e observe que o nome do grupo de recursos está listado na captura de tela de exemplo.
>
>

Para selecionar um circuito do ExpressRoute específico em um grupo de recursos, use o seguinte comando:

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Uma resposta de exemplo é:

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

Para confirmar se um circuito do ExpressRoute está funcionando, preste atenção especial aos seguintes campos:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

>[!NOTE]
>Se o *CircuitProvisioningState* não for habilitado, entre em contato com o [Suporte da Microsoft][Support]. Se o *ServiceProviderProvisioningState* não for provisionado, entre em contato com seu provedor de serviços.
>
>

###<a name="verification-via-powershell-classic"></a>Verificação por meio do PowerShell (Clássico)
Para listar todos os circuitos do ExpressRoute em uma assinatura, use o seguinte comando:

    Get-AzureDedicatedCircuit

Para selecionar um circuito do ExpressRoute específico, use o seguinte comando:

    Get-AzureDedicatedCircuit -ServiceKey **************************************

Uma resposta de exemplo é:

    andwidth                         : 100
    BillingType                      : UnlimitedData
    CircuitName                      : Test-ER-Ckt
    Location                         : westus2
    ServiceKey                       : **************************************
    ServiceProviderName              : ****
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Para confirmar se um circuito do ExpressRoute está funcionando, preste atenção especial aos seguintes campos: ServiceProviderProvisioningState : Provisioned Status                           : Enabled

>[!NOTE]
>Se o *Status* não for habilitado, entre em contato com o [Suporte da Microsoft][Support]. Se o *ServiceProviderProvisioningState* não for provisionado, entre em contato com seu provedor de serviços.
>
>

##<a name="validate-peering-configuration"></a>Validar Configuração de Emparelhamento
Depois que o provedor de serviços concluir o provisionamento do circuito do ExpressRoute, uma configuração de roteamento poderá ser criada no circuito do ExpressRoute entre MSEE-PRs (4) e MSEEs (5). Cada circuito do ExpressRoute pode ter um, dois ou três contextos de roteamento habilitados: emparelhamento privado do Azure (tráfego para redes virtuais privadas no Azure), emparelhamento público do Azure (tráfego para endereços IP públicos no Azure) e emparelhamento da Microsoft (tráfego para Office 365 e Dynamic 365). Para obter mais informações sobre como criar e modificar a configuração de roteamento, consulte o artigo [Create and modify routing for an ExpressRoute circuit][CreatePeering] (Criar e modificar o roteamento para um circuito do ExpressRoute).

###<a name="verification-via-the-azure-portal"></a>Verificação por meio do Portal do Azure

>[!NOTE]
>Se a camada 3 for fornecida pelo provedor de serviços e os emparelhamentos estiverem em branco no portal, atualize a configuração Circuito usando o botão Atualizar no portal. Essa operação aplicará a configuração de roteamento correta em seu circuito. 
>
>

No Portal do Azure, o status de um circuito do ExpressRoute pode ser verificado selecionando ![2][2] na barra de menu do lado esquerdo e selecionando o circuito do ExpressRoute. Selecionar um circuito do ExpressRoute listado em "Todos os recursos" abriria a folha do circuito do ExpressRoute. Na seção ![3][3] da folha, as informações gerais sobre o ExpressRoute seriam listadas conforme mostrado na seguinte captura de tela:

![5][5]

No exemplo anterior, conforme observado, o contexto de roteamento de emparelhamento privado do Azure está habilitado, enquanto os contextos de roteamento de emparelhamento público do Azure e da Microsoft não estão habilitados. Um contexto de emparelhamento habilitado com êxito também teria as sub-redes primárias e secundárias ponto a ponto (necessário para BGP) listadas. As sub-redes /30 são usadas para o endereço IP da interface dos MSEEs e PE-MSEEs. 

>[!NOTE]
>Se um emparelhamento não estiver habilitado, verifique se as sub-redes primárias e secundárias atribuídas corresponderão à configuração nos PE-MSEEs. Caso contrário, para alterar a configuração em roteadores MSEE, consulte [Create and modify routing for an ExpressRoute circuit (Criar e modificar o roteamento para um circuito do ExpressRoute)][CreatePeering]
>
>

###<a name="verification-via-powershell"></a>Verificação por meio do PowerShell
Para obter os detalhes de configuração de emparelhamento privado do Azure, use os seguintes comandos:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt

Uma resposta de exemplo para um emparelhamento privado configurado com êxito é:

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : ####
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 300
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 Um contexto de emparelhamento habilitado com êxito teria os prefixos dos endereços primário e secundário listados. As sub-redes /30 são usadas para o endereço IP da interface dos MSEEs e PE-MSEEs.

Para obter os detalhes de configuração de emparelhamento público do Azure, use os seguintes comandos:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt

Para obter os detalhes de configuração de emparelhamento da Microsoft, use os seguintes comandos:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -Circuit $ckt

Se um emparelhamento não for configurado, haverá uma mensagem de erro. Uma resposta de exemplo, quando o emparelhamento indicado (emparelhamento público do Azure neste exemplo) não está configurado no circuito:

    Get-AzureRmExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzureRmExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


<p/>
>[!NOTE]
>Se um emparelhamento não estiver habilitado, verifique se as sub-redes primárias e secundárias atribuídas correspondem à configuração no PE-MSEE vinculado. Verifique também se os *VlanId*, *AzureASN* e *PeerASN* corretos estão sendo usados nos MSEEs e se esses valores são mapeados para aqueles usados no PE-MSEE vinculado. Se o hash MD5 é escolhido, a chave compartilhada deve ser igual em um par MSEE e PE-MSEE. Para alterar a configuração nos roteadores MSEE, consulte [Criar e modificar o roteamento para um circuito do ExpressRoute][CreatePeering].  
>
>

### <a name="verification-via-powershell-classic"></a>Verificação por meio do PowerShell (Clássico)
Para obter os detalhes de configuração de emparelhamento privado do Azure, use o seguinte comando:

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

Uma resposta de exemplo para um emparelhamento privado configurado com êxito é:

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : ####
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100

Um contexto de emparelhamento habilitado com êxito teria as subredes emparelhadas primárias e secundárias listadas. As sub-redes /30 são usadas para o endereço IP da interface dos MSEEs e PE-MSEEs.

Para obter os detalhes de configuração de emparelhamento público do Azure, use os seguintes comandos:

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

Para obter os detalhes de configuração de emparelhamento da Microsoft, use os seguintes comandos:

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

>[!IMPORTANT]
>Se os emparelhamentos de camada 3 tiverem sido definidos pelo provedor de serviços, definir os emparelhamentos ExpressRoute por meio do portal ou do PowerShell substituirá as configurações do provedor de serviços. Redefinir as configurações de emparelhamento de lado do provedor requer o suporte do provedor de serviços. Modifique os emparelhamentos do ExpressRoute apenas se você tiver certeza de que o provedor de serviços está fornecendo apenas serviços de camada 2!
>
>

<p/>
>[!NOTE]
>Se um emparelhamento não estiver habilitado, verifique se as sub-redes emparelhadas primárias e secundárias atribuídas correspondem à configuração no PE-MSEE vinculado. Verifique também se os *VlanId*, *AzureAsn* e *PeerAsn* corretos estão sendo usados nos MSEEs e se esses valores são mapeados para aqueles usados no PE-MSEE vinculado. Para alterar a configuração nos roteadores MSEE, consulte [Criar e modificar o roteamento para um circuito do ExpressRoute][CreatePeering].
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>Validar ARP entre a Microsoft e o provedor de serviços
Esta seção usa comandos do PowerShell (Clássico). Se você usa comandos do Azure Resource Manager do PowerShell, certifique-se de que você tem acesso de administrador/coadministrador para a assinatura por meio do [Portal Clássico do Azure][OldPortal]. Para solucionar o problema usando comandos do Azure Resource Manager, consulte o documento [Obtenção de tabelas ARP no modelo de implantação do Resource Manager][ARP].

>[!NOTE]
>Para obter o ARP, o Portal do Azure e os comandos do PowerShell do Azure Resource Manager podem ser usados. Se forem encontrados erros com os comandos do PowerShell do Azure Resource Manager os comandos clássicos do PowerShell deverão funcionar, do mesmo modo que comandos clássicos do PowerShell também funcionam com circuitos do ExpressRoute do Azure Resource Manager.
>
>

Para obter a tabela ARP do roteador MSEE primário para o emparelhamento privado, use o seguinte comando:

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Uma resposta de exemplo para o comando, no cenário de êxito:

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

De forma similar, você pode verificar a tabela ARP do MSEE no caminho *Primário*/*Secundário*, para emparelhamentos *Privados*/*Públicos*/*da Microsoft*.

O exemplo a seguir mostra que a resposta do comando para um emparelhamento não existe.

    ARP Info:
       
>[!NOTE]
>Se a tabela ARP não tiver endereços IP das interfaces mapeados para endereços MAC, examine as seguintes informações:
>1. Se o primeiro endereço IP da sub-rede /30 atribuído para o link entre o MSEE-PR e MSEE é usado na interface do MSEE-PR. O Azure sempre usa o segundo endereço IP para MSEEs.
>2. Verifique se as marcações de VLAN do cliente (C-Tag) e de serviços (S-Tag) correspondem às duas no par MSEE-PR e MSEE.
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>Validar BGP e rotas no MSEE
Esta seção usa comandos do PowerShell (Clássico). Se você usa comandos do Azure Resource Manager do PowerShell, certifique-se de que você tem acesso de administrador/coadministrador para a assinatura por meio do [Portal Clássico do Azure][OldPortal]

>[!NOTE]
>Para obter informações de BGP, o Portal do Azure e os comandos do PowerShell do Azure Resource Manager podem ser usados. Se forem encontrados erros com os comandos do PowerShell do Azure Resource Manager os comandos clássicos do PowerShell deverão funcionar, do mesmo modo que comandos clássicos do PowerShell também funcionam com circuitos do ExpressRoute do Azure Resource Manager.
>
>

Para obter o resumo da tabela de roteamento (vizinho BGP) para um determinado contexto de roteamento, use o seguinte comando:

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

Uma resposta de exemplo é:

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

Conforme mostrado no exemplo anterior, o comando é útil para determinar por quanto tempo o contexto de roteamento foi estabelecido. Ele também indica o número de prefixos de rota anunciados pelo roteador de emparelhamento.

>[!NOTE]
>Se o estado no momento for Ativo ou Ocioso, verifique se as sub-redes emparelhadas primárias e secundárias atribuídas correspondem à configuração no PE-MSEE vinculado. Verifique também se os *VlanId*, *AzureAsn* e *PeerAsn* corretos estão sendo usados nos MSEEs e se esses valores são mapeados para aqueles usados no PE-MSEE vinculado. Se o hash MD5 é escolhido, a chave compartilhada deve ser igual em um par MSEE e PE-MSEE. Para alterar a configuração nos roteadores MSEE, consulte [Create and modify routing for an ExpressRoute circuit (Criar e modificar o roteamento para um circuito do ExpressRoute)][CreatePeering].
>
>

<p/>
>[!NOTE]
>Se determinados destinos não estiverem acessíveis por meio de um emparelhamento específico, verifique a tabela de rotas dos MSEEs que pertencem ao contexto de emparelhamento específico. Se um prefixo correspondente (pode ser IP com NAT) estiver presente na tabela de roteamento, verifique se há firewalls/NSG/ACLs no caminho e se eles permitem o tráfego.
>
>

Para obter a tabela de roteamento completa do MSEE no caminho *Primário* para o contexto de roteamento *privado* específico, use o seguinte comando:

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Um exemplo de resultado bem-sucedido para o comando é:

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

De forma similar, você pode verificar a tabela de roteamento do MSEE no caminho *Primário*/*Secundário*, para um contexto de emparelhamento *Privado*/*Público*/*da Microsoft*.

O exemplo a seguir mostra que a resposta do comando para um emparelhamento não existe:

    Route Table Info:

##<a name="check-the-traffic-statistics"></a>Verifique as Estatísticas de Tráfego
Para obter as estatísticas de tráfego combinadas dos caminhos primário e secundário – bytes em entrada e em saída – de um contexto de emparelhamento, use o seguinte comando:

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

Uma saída de exemplo do comando é:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Uma saída de exemplo do comando para um emparelhamento inexistente é:

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

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
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[OldPortal]: https://manage.windowsazure.com
[ARP]: https://docs.microsoft.com/en-us/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






