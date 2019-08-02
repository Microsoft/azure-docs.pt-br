---
title: 'Interoperabilidade em recursos de conectividade de back-end do Azure: Detalhes de configuração | Microsoft Docs'
description: Este artigo descreve detalhes de configuração para configuração do teste que você pode usar para analisar a interoperabilidade entre o ExpressRoute, uma VPN site a site e o emparelhamento de rede virtual no Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 9c4a57111566248d3537cab0d9d85c0c3be874a1
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335934"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Interoperabilidade em recursos de conectividade de back-end do Azure: Detalhes da configuração de teste

Este artigo descreve os detalhes de configuração da [configuração de teste][Setup]. A configuração do teste ajuda a analisar como os serviços de rede do Azure interoperam no nível do plano de controle e no nível do plano de dados.

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Conectividade de VNet do spoke usando emparelhamento VNet

A figura a seguir mostra os detalhes de emparelhamento DE Rede Virtual do Azure de uma VNet (rede virtual) do spoke. Para saber como configurar o emparelhamento entre duas VNets, consulte [gerenciar emparelhamento VNet][VNet-Config]. Se você quiser que a VNet do spoke use os gateways conectados à VNet do hub, selecione **Usar gateways remotos**.

[![1]][1]

A figura a seguir mostra os detalhes de emparelhamento VNet da VNet do hub. Se você quiser que a VNet do Hub permita que a VNet do spoke use os gateways do Hub, selecione **Permitir trânsito do gateway**.

[![2]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>Conectividade da VNet do branch usando uma VPN site a site

Configure a conectividade VPN site a site entre as VNets de branch e hub, usando gateways de VPN no Gateway de VPN do Azure. Por padrão, os gateways de VPN e gateways do Azure ExpressRoute usam um valor de ASN (número do sistema autônomo) privado de **65515**. É possível alterar o valor do ASN no Gateway de VPN. Na configuração do teste, o valor do ASN do gateway de VPN da VNet de branch é alterado para **65516** para dar suporte ao roteamento de eBGP entre as VNets de branch e hub.


[![3]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>Conectividade de Localização 1 local usando ExpressRoute e uma VPN site to site

### <a name="expressroute-1-configuration-details"></a>Detalhes de configuração do ExpressRoute 1

A figura a seguir mostra a configuração do circuito do ExpressRoute da Região 1 do Azure em direção aos roteadores de borda do cliente (CE) da Localização 1 local:

[![4]][4]

A figura a seguir mostra a configuração da conexão entre o circuito do ExpressRoute 1 e a VNet do hub:

[![5]][5]

A lista a seguir mostra a configuração do roteador CE primário para conectividade de emparelhamento privado do ExpressRoute. (Roteadores Cisco ASR1000 são usados como roteadores de CE na configuração do teste.) Quando os circuitos do ExpressRoute e VPN site a site forem configurados em paralelo para conectar uma rede local ao Azure, o Azure priorizará o circuito do ExpressRoute por padrão. Para evitar o roteamento assimétrico, a rede local também deverá priorizar a conectividade do ExpressRoute sobre conectividade VPN site a site. A configuração a seguir estabelece a priorização usando o atributo de BGP **local-preference**:

    interface TenGigabitEthernet0/0/0.300
     description Customer 30 private peering to Azure
     encapsulation dot1Q 30 second-dot1q 300
     ip vrf forwarding 30
     ip address 192.168.30.17 255.255.255.252
    !
    interface TenGigabitEthernet1/0/0.30
     description Customer 30 to south bound LAN switch
     encapsulation dot1Q 30
     ip vrf forwarding 30
     ip address 192.168.30.0 255.255.255.254
     ip ospf network point-to-point
    !
    router ospf 30 vrf 30
     router-id 10.2.30.253
     redistribute bgp 65021 subnets route-map BGP2OSPF
     network 192.168.30.0 0.0.0.1 area 0.0.0.0
    default-information originate always
     default-metric 10
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 192.168.30.18 remote-as 12076
      neighbor 192.168.30.18 activate
      neighbor 192.168.30.18 next-hop-self
      neighbor 192.168.30.18 soft-reconfiguration inbound
      neighbor 192.168.30.18 route-map prefer-ER-over-VPN in
      neighbor 192.168.30.18 prefix-list Cust30_to_Private out
     exit-address-family
    !
    route-map prefer-ER-over-VPN permit 10
     set local-preference 200
    !
    ip prefix-list Cust30_to_Private seq 10 permit 10.2.30.0/25
    !

### <a name="site-to-site-vpn-configuration-details"></a>Detalhes de configuração de VPN site a Site

A lista a seguir mostra a configuração do roteador de CE primário para conectividade VPN site a site:

    crypto ikev2 proposal Cust30-azure-proposal
     encryption aes-cbc-256 aes-cbc-128 3des
     integrity sha1
     group 2
    !
    crypto ikev2 policy Cust30-azure-policy
     match address local 66.198.12.106
     proposal Cust30-azure-proposal
    !
    crypto ikev2 keyring Cust30-azure-keyring
     peer azure
      address 52.168.162.84
      pre-shared-key local IamSecure123
      pre-shared-key remote IamSecure123
    !
    crypto ikev2 profile Cust30-azure-profile
     match identity remote address 52.168.162.84 255.255.255.255
     identity local address 66.198.12.106
     authentication local pre-share
     authentication remote pre-share
     keyring local Cust30-azure-keyring
    !
    crypto ipsec transform-set Cust30-azure-ipsec-proposal-set esp-aes 256 esp-sha-hmac
     mode tunnel
    !
    crypto ipsec profile Cust30-azure-ipsec-profile
     set transform-set Cust30-azure-ipsec-proposal-set
     set ikev2-profile Cust30-azure-profile
    !
    interface Loopback30
     ip address 66.198.12.106 255.255.255.255
    !
    interface Tunnel30
     ip vrf forwarding 30
     ip address 10.2.30.125 255.255.255.255
     tunnel source Loopback30
     tunnel mode ipsec ipv4
     tunnel destination 52.168.162.84
     tunnel protection ipsec profile Cust30-azure-ipsec-profile
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 10.10.30.254 remote-as 65515
      neighbor 10.10.30.254 ebgp-multihop 5
      neighbor 10.10.30.254 update-source Tunnel30
      neighbor 10.10.30.254 activate
      neighbor 10.10.30.254 soft-reconfiguration inbound
     exit-address-family
    !
    ip route vrf 30 10.10.30.254 255.255.255.255 Tunnel30

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>Conectividade de Localização 2 local usando ExpressRoute

Um segundo circuito do ExpressRoute, mais próximo à Localização 2 local, conecta a Localização 2 local à VNet do hub. A figura a seguir mostra a segunda configuração do ExpressRoute:

[![6]][6]

A figura a seguir mostra a configuração de conexão entre o segundo circuito da ExpressRoute e a VNet do hub:

[![7]][7]

O ExpressRoute 1 conecta a VNet do hub e a Localização 1 local a uma VNet remota em uma região do Azure diferente:

[![8]][8]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Conectividade do ExpressRoute e VPN site a site em conjunto

###  <a name="site-to-site-vpn-over-expressroute"></a>VPN site a site pelo ExpressRoute

É possível configurar uma VPN site a site, usando emparelhamento do Microsoft ExpressRoute para trocar dados em modo privado entre a rede local e as VNets do Azure. Com essa configuração, é possível trocar dados com confidencialidade, autenticidade e integridade. A troca de dados também é antirreprodução. Para obter mais informações sobre como configurar uma VPN IPsec site a site no modo de túnel usando o emparelhamento da Microsoft do ExpressRoute, consulte [VPN site a site sobre o emparelhamento da Microsoft do expressroute][S2S-Over-ExR]. 

A principal limitação da configuração de uma VPN site a site que usa o emparelhamento da Microsoft é a taxa de transferência. A taxa de transferência no túnel IPsec é limitada pela capacidade do gateway de VPN. A taxa de transferência do gateway de VPN é menor que a taxa de transferência do ExpressRoute. Nesse cenário, o uso do túnel IPsec para tráfego altamente seguro e o uso de emparelhamento privado para todos os outros tráfegos ajuda a otimizar a utilização da largura de banda do ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN site a site como um caminho de failover seguro para o ExpressRoute

O ExpressRoute funciona como um par de circuitos redundantes para garantir alta disponibilidade. Você pode configurar a conectividade de ExpressRoute geo-redundante em diferentes regiões do Azure. Além disso, conforme demonstrado em nossa configuração do teste, em uma região do Azure, é possível usar uma VPN site a site para criar um caminho de failover para a conectividade do ExpressRoute. Quando os mesmos prefixos são anunciados sobre o ExpressRoute e uma VPN site a site, o Azure prioriza o ExpressRoute. Para evitar roteamento assimétrico entre o ExpressRoute e a VPN site a site, a configuração de rede local também deverá retribuir usando a conectividade do ExpressRoute antes de usar a conectividade VPN site a site.

Para obter mais informações sobre como configurar conexões coexistentes para o ExpressRoute e uma VPN site a site, consulte [ExpressRoute e coexistência site a site][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Estender conectividade de back-end para VNets do spoke e localizações de branch

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Conectividade de VNet do spoke usando emparelhamento VNet

A arquitetura da VNet do hub e do spoke é amplamente usada. O hub é uma VNet no Azure que funciona como um ponto central de conectividade entre as VNets do spoke e a rede local. Os spokes são VNets que emparelham-se com o hub e que podem ser utilizadas para isolar as cargas de trabalho. O tráfego flui entre o datacenter local e o hub por meio de uma conexão de VPN ou ExpressRoute. Para obter mais informações sobre a arquitetura, consulte [implementar uma topologia de rede hub-spoke no Azure][Hub-n-Spoke].

Em emparelhamento VNet dentro de uma região, as VNets do spoke podem usar gateways de VNet do hub (ambos gateways de VPN e ExpressRoute) para comunicarem-se com redes remotas.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Conectividade da VNet do branch usando VPN site a site

Talvez você queira que as VNets de branch, que estão em regiões diferentes, e as redes locais comuniquem-se entre si por meio de uma VNet de hub. A solução nativa do Azure para essa configuração é a conectividade de VPN site a site usando uma VPN. Uma alternativa é usar uma NVA (solução de virtualização de rede) para roteamento no hub.

Para obter mais informações, consulte [o que é o gateway de VPN?][VPN] and [Deploy a highly available NVA][Deploy-NVA].

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [análise do plano de controle][Control-Analysis] da configuração do teste e as exibições de diferentes VNETS ou VLANs na topologia.

Saiba mais sobre a [análise do plano de dados][Data-Analysis] da configuração de teste e as exibições de recurso de monitoramento de rede do Azure.

Consulte as [perguntas frequentes sobre o ExpressRoute][ExR-FAQ] para:
-   Saber quantos circuitos do ExpressRoute podem ser conectados a um gateway do ExpressRoute.
-   Saiba quantos gateways do ExpressRoute podem ser conectados a um circuito do ExpressRoute.
-   Saber mais sobre outros limites de escala do ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "Emparelhamento da rede virtual Spoke da rede virtual"
[2]: ./media/backend-interoperability/HubVNet-peering.png "Emparelhamento da rede virtual Hub da rede virtual"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "Configuração do Gateway de VPN de uma VNet de branch"
[4]: ./media/backend-interoperability/ExR1.png "Configuração do ExpressRoute 1"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "Configuração de conexão do ExpressRoute 1 para um gateway ExR de VNet de hub"
[6]: ./media/backend-interoperability/ExR2.png "Configuração do ExpressRoute 2"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "Configuração de conexão do ExpressRoute 2 para um gateway ExR de VNet de hub"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "Configuração de conexão do ExpressRoute 2 para um gateway ExR de VNet remota"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering


