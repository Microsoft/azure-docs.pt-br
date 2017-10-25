---
title: "Configurar as conexões de VPN ExpressRoute e Site a Site que possam coexistir: clássico: Azure | Microsoft Docs"
description: "Este artigo o orienta na configuração do ExpressRoute e de uma conexão VPN Site a Site que pode coexistir para o modelo de implantação clássico."
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: dcf1a5af-a289-466a-b812-0bfedbd2bda0
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: charwen
ms.openlocfilehash: 09d1649f0ca0cf4ca464d95b29461cad3fe51788
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-classic"></a>Configurar as conexões coexistentes do ExpressRoute e do Site a Site (clássico)
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell - clássico](expressroute-howto-coexist-classic.md)
> 
> 

Poder configurar a VPN site a site e o ExpressRoute tem várias vantagens. Você pode configurar a VPN Site a Site como um caminho de failover seguro para o ExpressRoute ou usar VPNs Site a Site para se conectar a sites que não estão conectados por meio do ExpressRoute. Neste artigo, analisaremos as etapas para configurar as duas situações. Este artigo se aplica ao modelo de implantação clássico. Essa configuração não está disponível no portal.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Sobre modelos de implantação do Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

> [!IMPORTANT]
> Os circuitos de ExpressRoute devem ser previamente configurados antes de você seguir as instruções abaixo. Verifique se você seguiu as guias para [criar um circuito de ExpressRoute](expressroute-howto-circuit-classic.md) e [configurar o roteamento](expressroute-howto-routing-classic.md) antes de seguir as etapas abaixo.
> 
> 

## <a name="limits-and-limitations"></a>Limites e limitações
* **Não há suporte para o roteamento do tráfego.** Não é possível fazer o roteamento (por meio do Azure) entre sua rede local conectada via VPN Site a Site e sua rede local conectada via ExpressRoute.
* **Não há suporte para o Ponto a site.** Não é possível habilitar conexões VPN de ponto a site para a mesma VNet conectada ao ExpressRoute. A VPN de ponto a site e o ExpressRoute não podem coexistir para a mesma Rede Virtual.
* **Não é possível habilitar o túnel forçado no gateway de VPN de Site a Site.** Você pode apenas "forçar" todo o tráfego direcionado à Internet em sua rede local por meio da ExpressRoute.
* **Não há suporte para o gateway SKU básico.** Você deve usar um gateway SKU não Básico para o [gateway de ExpressRoute](expressroute-about-virtual-network-gateways.md) e o [gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* **Há suporte para apenas um gateway de VPN baseado em rotas.** Você deve usar uma rota baseada no [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* **O roteamento estático deve ser configurado para o gateway de VPN.** Se sua rede local estiver conectada à VPN Site a Site e de ExpressRoute, será necessário ter uma rota estática configurada em sua rede local para rotear a conexão VPN Site a Site para a Internet pública.
* **O gateway de ExpressRoute deve ser configurado primeiro.** Você deve criar o gateway de ExpressRoute primeiro, antes de adicionar o gateway de VPN Site a Site.

## <a name="configuration-designs"></a>Designs de configuração
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Configurar uma VPN site a site como um caminho de failover para o ExpressRoute
Você pode configurar uma conexão VPN site a site como um backup para o ExpressRoute. Isso se aplica apenas às redes virtuais vinculadas ao caminho de emparelhamento privado do Azure. Não há uma solução de failover com base em VPN para serviços acessíveis por meio de emparelhamentos público do Azure e da Microsoft. O circuito do ExpressRoute sempre será o link principal. Os dados só fluirão pelo caminho da VPN site a site se o circuito do ExpressRoute falhar. 

> [!NOTE]
> Embora o circuito ExpressRoute seja preferencial em relação à VPN Site a Site quando ambas as rotas são as mesmas, o Azure usa a correspondência de prefixo mais longa para escolher a rota até o destino do pacote.
> 
> 

![Coexistência](media/expressroute-howto-coexist-classic/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Configurar uma VPN site a site para se conectar a sites não conectados por meio do ExpressRoute
Você pode configurar sua rede de modo que alguns sites se conectem diretamente ao Azure através da VPN site a site, e alguns sites se conectem por meio do ExpressRoute. 

![Coexistência](media/expressroute-howto-coexist-classic/scenario2.jpg)

> [!NOTE]
> Não é possível configurar uma rede virtual como um roteador de trânsito.
> 
> 

## <a name="selecting-the-steps-to-use"></a>Selecionando as etapas de uso
Há dois conjuntos de procedimentos diferentes para configurar as conexões de modo que elas coexistam. O procedimento de configuração selecionado dependerá caso você já tenha uma rede virtual à qual deseja se conectar ou caso queira criar uma nova rede virtual.

* Não tenho uma VNet e preciso criar uma.
  
    Se você ainda não tiver uma rede virtual, esse procedimento explicará isso criando uma nova rede virtual usando o modelo de implantação clássico e criando novas conexões de VPN Site a Site e de ExpressRoute. Para configurar, siga as etapas na seção do artigo [Para criar uma nova rede virtual e conexões coexistentes](#new).
* Eu já tenho uma VNet do modelo de implantação clássico
  
    Talvez você já tenha uma rede virtual implementada com uma conexão de VPN Site a Site ou uma conexão de ExpressRoute existente. A seção do artigo [Para configurar conexões coexistentes para uma VNet já existente](#add) explica como excluir o gateway e criar novas conexões de VPN Site a Site e de ExpressRoute. Observe que, ao criar novas conexões, as etapas devem ser concluídas em uma ordem muito específica. Não use as instruções de outros artigos para criar seus gateways e conexões.
  
    Neste procedimento, a criação de conexões que possam coexistir exigirá que você exclua seu gateway e então configure novos gateways. Isso significa que haverá tempo de inatividade nas suas conexões entre locais durante o processo de exclusão e recriação de seu gateway e conexões, mas você não precisará migrar nenhuma das suas VMs ou serviços para uma nova rede virtual. Suas VMs e serviços ainda poderão se comunicar por meio do balanceador de carga enquanto você configura o seu gateway, se estiverem configurados para fazer isso.

## <a name="new"></a>Para criar uma nova rede virtual e conexões coexistentes
Este procedimento orientará você na criação de uma Rede Virtual, bem como na criação das conexões site a site e de ExpressRoute que coexistirão.

1. Você precisará instalar a versão mais recente dos cmdlets do Azure PowerShell. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para saber mais sobre como instalar os cmdlets do PowerShell. Observe que os cmdlets que você usará para essa configuração podem ser ligeiramente diferentes daqueles com os quais você talvez esteja familiarizado. Certifique-se de usar os cmdlets especificados nestas instruções. 
2. Crie um esquema para a sua rede virtual. Para saber mais sobre o esquema de configuração, confira [Esquema de configuração de Rede Virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).
   
    Quando criar seu esquema, certifique-se de usar os seguintes valores:
   
   * A sub-rede de gateway para a rede virtual deve ser /27 ou um prefixo menor (como /26 ou /25).
   * O tipo de conexão de gateway é "Dedicated".
     
             <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
               <AddressSpace>
                 <AddressPrefix>10.17.159.192/26</AddressPrefix>
               </AddressSpace>
               <Subnets>
                 <Subnet name="Subnet-1">
                   <AddressPrefix>10.17.159.192/27</AddressPrefix>
                 </Subnet>
                 <Subnet name="GatewaySubnet">
                   <AddressPrefix>10.17.159.224/27</AddressPrefix>
                 </Subnet>
               </Subnets>
               <Gateway>
                 <ConnectionsToLocalNetwork>
                   <LocalNetworkSiteRef name="MyLocalNetwork">
                     <Connection type="Dedicated" />
                   </LocalNetworkSiteRef>
                 </ConnectionsToLocalNetwork>
               </Gateway>
             </VirtualNetworkSite>
3. Depois de criar e configurar o arquivo de esquema XML, carregue o arquivo. Isso criará sua rede virtual.
   
    Use o cmdlet a seguir para carregar seu arquivo, substituindo o valor existente pelo seu.
   
        Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'
4. <a name="gw"></a>Crie um gateway de ExpressRoute. Especifique o Gateway SKU como *Standard*, *HighPerformance* ou *UltraPerformance* e o Tipo de Gateway como *DynamicRouting*.
   
    Use o exemplo a seguir, substituindo os valores existentes pelos seus.
   
        New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance
5. Vincule o gateway de ExpressRoute ao circuito de ExpressRoute. Após essa etapa for concluída, a conexão entre sua rede local e o Azure, por meio de ExpressRoute, é estabelecida.
   
        New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET
6. <a name="vpngw"></a>Em seguida, crie seu gateway de VPN Site a Site. O Gateway SKU deve ser *Standard*, *HighPerformance* ou *UltraPerformance* e o Tipo de Gateway deve ser *DynamicRouting*.
   
        New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance
   
    Para obter as configurações de gateway de rede virtual, incluindo a ID do gateway e o IP público, use o cmdlet `Get-AzureVirtualNetworkGateway`.
   
        Get-AzureVirtualNetworkGateway
   
        GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
        GatewayName          : S2SVPN
        LastEventData        :
        GatewayType          : DynamicRouting
        LastEventTimeStamp   : 5/29/2015 4:41:41 PM
        LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
        LastEventID          : 23002
        State                : Provisioned
        VIPAddress           : 104.43.x.y
        DefaultSite          :
        GatewaySKU           : HighPerformance
        Location             :
        VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
        SubnetId             :
        EnableBgp            : False
        OperationDescription : Get-AzureVirtualNetworkGateway
        OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
        OperationStatus      : Succeeded
7. Crie uma entidade de gateway de VPN de site local. Esse comando não configura seu gateway de VPN local. Em vez disso, ele permite que você forneça as configurações de gateway local, como o IP público e o espaço para endereço local, para que o gateway de VPN do Azure possa se conectar a ele.
   
   > [!IMPORTANT]
   > O site local para o VPN Site a site não está definido no netcfg. Em vez disso, você deve usar esse cmdlet para especificar os parâmetros do site local. Você não pode defini-lo usando o portal ou o arquivo netcfg.
   > 
   > 
   
    Use o exemplo a seguir, substituindo os valores existentes pelos seus.
   
        New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <MyLocalGatewayIp> -AddressSpace <MyLocalNetworkAddress>
   
   > [!NOTE]
   > Se sua rede local tiver várias rotas, você poderá passá-las como uma matriz.  $MyLocalNetworkAddress = @("10.1.2.0/24","10.1.3.0/24","10.2.1.0/24")  
   > 
   > 

    Para obter as configurações de gateway de rede virtual, incluindo a ID do gateway e o IP público, use o cmdlet `Get-AzureVirtualNetworkGateway`. Veja os exemplos a seguir.

        Get-AzureLocalNetworkGateway

        GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
        GatewayName          : MyLocalNetwork
        IpAddress            : 23.39.x.y
        AddressSpace         : {10.1.2.0/24}
        OperationDescription : Get-AzureLocalNetworkGateway
        OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
        OperationStatus      : Succeeded


1. Configure seu dispositivo VPN local para conectar-se ao novo gateway. Ao configurar seu dispositivo VPN, use as informações que você recuperou na etapa 6. Para obter mais informações sobre a configuração de dispositivo VPN, consulte [Configuração do Dispositivo VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).
2. Vincule o gateway de VPN Site a Site no Azure ao gateway local.
   
    Neste exemplo, connectedEntityId é a ID de gateway local, que você pode encontrar executando `Get-AzureLocalNetworkGateway`. Você pode encontrar virtualNetworkGatewayId usando o cmdlet `Get-AzureVirtualNetworkGateway` . Após essa etapa, é estabelecida a conexão entre sua rede local e o Azure, por meio da conexão VPN Site a Site.

        New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>

## <a name="add"></a>Para configurar conexões coexistentes para uma VNet já existente
Se você tiver uma rede virtual existente, verifique o tamanho da sub-rede do gateway. Se a sub-rede do gateway é /28 ou /29, você deve primeiro excluir o gateway da rede virtual e aumentar o tamanho de sub-rede do gateway. As etapas nesta seção mostram como fazer isso.

Se a sub-rede do gateway é /27 ou maior e a rede virtual está conectada vio ExpressRoute, você pode ignorar as etapas abaixo e ir para a ["Etapa 6: criar um gateway de VPN Site a Site"](#vpngw) na seção anterior.

> [!NOTE]
> Quando você exclui o gateway existente, suas instalações locais perdem a conexão à sua rede virtual enquanto você está trabalhando nessa configuração.
> 
> 

1. Você precisará instalar a versão mais recente dos cmdlets do PowerShell do Gerenciador de Recursos do Azure. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para saber mais sobre como instalar os cmdlets do PowerShell. Observe que os cmdlets que você usará para essa configuração podem ser ligeiramente diferentes daqueles com os quais você talvez esteja familiarizado. Certifique-se de usar os cmdlets especificados nestas instruções. 
2. Exclua o gateway de ExpressRoute ou gateway de VPN Site a Site existente. Use o cmdlet a seguir, substituindo os valores existentes pelos seus.
   
        Remove-AzureVNetGateway –VnetName MyAzureVNET
3. Exporte o esquema da rede virtual. Use o cmdlet do PowerShell a seguir, substituindo os valores existentes pelos seus.
   
        Get-AzureVNetConfig –ExportToFile “C:\NetworkConfig.xml”
4. Edite o esquema do arquivo de configuração de rede para que a sub-rede de gateway seja /27 ou um prefixo menor (como /26 ou /25). Veja os exemplos a seguir. 
   
   > [!NOTE]
   > Se você não tiver endereços IP suficientes restantes em sua rede virtual para aumentar o tamanho da sub-rede do gateway, precisará adicionar mais espaço de endereço IP. Para saber mais sobre o esquema de configuração, confira [Esquema de configuração de Rede Virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).
   > 
   > 
   
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.17.159.224/27</AddressPrefix>
          </Subnet>
5. Se o seu gateway anterior era de VPN Site a Site, você também deve alterar o tipo de conexão para **Dedicated**.
   
                 <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="MyLocalNetwork">
                      <Connection type="Dedicated" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
6. Neste ponto, você terá uma VNet sem nenhum gateway. Para criar novos gateways e concluir suas conexões, você pode prosseguir com a [Etapa 4: criar um gateway de ExpressRoute](#gw), encontrada no conjunto de etapas anterior.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o ExpressRoute, confira [Perguntas frequentes sobre ExpressRoute](expressroute-faqs.md)

