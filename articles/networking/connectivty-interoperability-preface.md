---
title: 'Interoperabilidade em recursos de conectividade de back-end do Azure: Configuração do teste | Microsoft Docs'
description: Este artigo descreve uma configuração do teste que é possível utilizar para analisar a interoperabilidade entre o ExpressRoute, uma VPN site a site e o emparelhamento de rede virtual no Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 8be546c5dba4c6c694c8cef03a4bdd6005d68189
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811111"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-setup"></a>Interoperabilidade em recursos de conectividade de back-end do Azure: Configuração de teste

Este artigo descreve uma configuração do teste que é possível utilizar para analisar como os serviços de rede do Azure interoperam no nível do plano de controle e no nível do plano de dados. Examinaremos brevemente os componentes de rede do Azure:

-   **Azure ExpressRoute**: use o emparelhamento privado no Azure ExpressRoute para conectar diretamente espaços IP privados na rede local às implantações da Rede Virtual do Azure. O ExpressRoute pode ajudá-lo a obter maior largura de banda e uma conexão privada. Muitos parceiros eco do ExpressRoute dão suporte para conectividade do ExpressRoute com SLAs. Para saber mais sobre o ExpressRoute e aprender como configurar o ExpressRoute, consulte [Introdução ao ExpressRoute][ExpressRoute].
-   **VPN site a site**: é possível usar o Gateway de VPN do Azure, como uma VPN site a site, para conectar com segurança uma rede local ao Azure pela Internet ou usando o ExpressRoute. Para saber como configurar uma VPN site a site e conectar-se ao Azure, consulte [Configurar Gateway de VPN][VPN].
-   **Emparelhamento VNET**: use emparelhamento VNet (rede virtual) para estabelecer conectividade entre VNets na Rede Virtual do Azure. Para saber mais sobre emparelhamento VNet, consulte o [tutorial sobre emparelhamento VNet][VNet].

## <a name="test-setup"></a>Configuração de teste

A figura a seguir ilustra a configuração do teste:

[![1]][1]

A parte central da configuração do teste é a VNet do hub na Região 1 do Azure. A VNet do hub é conectada a diferentes redes das seguintes maneiras:

-   A VNet do hub é conectada à VNet do spoke, usando emparelhamento VNet. A VNet do spoke tem acesso remoto aos dois gateways na Vnet do hub.
-   A VNet do hub é conectada à VNet de branch, usando VPN site a site. A conectividade usa o eBGP para trocar rotas.
-   A VNet do hub é conectada à rede Localização 1 local, usando emparelhamento privado do ExpressRoute como o caminho principal. Ela usa a conectividade VPN site a site como o caminho de backup. No restante deste artigo, esse circuito do ExpressRoute é referido como ExpressRoute 1. Por padrão, os circuitos do ExpressRoute fornecem conectividade com redundância para alta disponibilidade. No ExpressRoute 1, a subinterface do roteador de borda do cliente (CE) secundário voltado para o MSEE (roteador de borda do Microsoft Enterprise) secundário é desabilitada. Uma linha vermelha sobre a seta de linha dupla na figura anterior representa a subinterface do roteador de CE desabilitada.
-   A VNet do hub é conectada à rede Localização 2 local, usando outro emparelhamento privado do ExpressRoute. No restante deste artigo, esse segundo circuito do ExpressRoute é referido como ExpressRoute 2.
-   O ExpressRoute 1 também conecta ambas as VNet do hub e a rede Localização 1 local a uma VNet remota na Região 2 do Azure.

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute e conectividade VPN site a site em conjunto

###  <a name="site-to-site-vpn-over-expressroute"></a>VPN site a site pelo ExpressRoute

É possível configurar uma VPN site a site, usando emparelhamento do Microsoft ExpressRoute para trocar dados em modo privado entre a rede local e as VNets do Azure. Com essa configuração, é possível trocar dados com confidencialidade, autenticidade e integridade. A troca de dados também é antirreprodução. Para obter mais informações sobre como configurar uma VPN IPsec site a site no modo de túnel usando emparelhamento do ExpressRoute da Microsoft, consulte [VPN site a site sobre emparelhamento do Microsoft Azure ExpressRoute][S2S-Over-ExR]. 

A principal limitação da configuração de uma VPN site a site que usa o emparelhamento da Microsoft é a taxa de transferência. A taxa de transferência no túnel IPsec é limitada pela capacidade do gateway de VPN. A taxa de transferência do gateway de VPN é menor que a taxa de transferência do ExpressRoute. Nesse cenário, o uso do túnel IPsec para tráfego altamente seguro e o uso de emparelhamento privado para todos os outros tráfegos ajuda a otimizar a utilização da largura de banda do ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN site a site como um caminho de failover seguro para o ExpressRoute

O ExpressRoute funciona como um par de circuitos redundantes para garantir alta disponibilidade. Você pode configurar a conectividade de ExpressRoute geo-redundante em diferentes regiões do Azure. Além disso, conforme demonstrado em nossa configuração do teste, em uma região do Azure, é possível usar uma VPN site a site para criar um caminho de failover para a conectividade do ExpressRoute. Quando os mesmos prefixos são anunciados sobre o ExpressRoute e uma VPN site a site, o Azure prioriza o ExpressRoute. Para evitar roteamento assimétrico entre o ExpressRoute e a VPN site a site, a configuração de rede local também deverá retribuir usando a conectividade do ExpressRoute antes de usar a conectividade VPN site a site.

Para obter mais informações sobre como configurar conexões coexistentes para o ExpressRoute e uma VPN site a site, consulte [ExpressRoute e coexistência site a site][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Estender conectividade de back-end para VNets do spoke e localizações de branch

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Conectividade de VNet do spoke usando emparelhamento VNet

A arquitetura da VNet do hub e do spoke é amplamente usada. O hub é uma VNet no Azure que funciona como um ponto central de conectividade entre as VNets do spoke e a rede local. Os spokes são VNets que emparelham-se com o hub e que podem ser utilizadas para isolar as cargas de trabalho. O tráfego flui entre o datacenter local e o hub por meio de uma conexão de VPN ou ExpressRoute. Para obter mais informações sobre a arquitetura, confira [Implementar uma topologia de rede hub-spoke no Azure][Hub-n-Spoke].

Em emparelhamento VNet dentro de uma região, as VNets do spoke podem usar gateways de VNet do hub (ambos gateways de VPN e ExpressRoute) para comunicarem-se com redes remotas.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Conectividade da VNet do branch usando VPN site a site

Talvez você queira que as VNets de branch, que estão em regiões diferentes, e as redes locais comuniquem-se entre si por meio de uma VNet de hub. A solução nativa do Azure para essa configuração é a conectividade de VPN site a site usando uma VPN. Uma alternativa é usar uma NVA (solução de virtualização de rede) para roteamento no hub.

Para obter mais informações, consulte [O que é Gateway de VPN? ][VPN] e [Implantar um NVA altamente disponível][Deploy-NVA].

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os [detalhes de configuração][Configuration] para a topologia de teste.

Saiba mais sobre a [análise de plano de controle][Control-Analysis] da configuração de teste e as exibições das diferentes VNets ou VLANs na topologia.

Saiba mais sobre a [análise de plano de dados][Data-Analysis] da configuração do teste e das exibições do recurso de monitoramento de rede do Azure.

Consulte as [Perguntas frequentes sobre o ExpressRoute][ExR-FAQ] para:
-   Saber quantos circuitos do ExpressRoute podem ser conectados a um gateway do ExpressRoute.
-   Saiba quantos gateways do ExpressRoute podem ser conectados a um circuito do ExpressRoute.
-   Saiba mais sobre outros limites de escala do ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "Diagrama da topologia de teste"

<!--Link References-->
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: connectivty-interoperability-configuration.md
[Control-Analysis]: connectivty-interoperability-control-plane.md
[Data-Analysis]: connectivty-interoperability-data-plane.md
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha


