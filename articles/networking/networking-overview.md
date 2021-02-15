---
title: Visão geral dos serviços de rede do Azure
description: Saiba mais sobre os serviços de rede no Azure, incluindo conectividade, proteção de aplicativo, entrega de aplicativos e serviços de monitoramento de rede.
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: kumud
ms.openlocfilehash: d02f85647d947c2f28527ba6f0397c2a98e73840
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234181"
---
# <a name="azure-networking-services-overview"></a>Visão geral dos serviços de rede do Azure

Os serviços de rede no Azure fornecem uma variedade de recursos de rede que podem ser usados juntos ou separados. Clique em qualquer um dos seguintes recursos principais para saber mais sobre eles:
- [**Serviços de conectividade**](#connect): Conecte recursos do Azure e recursos locais usando qualquer ou uma combinação desses serviços de rede no Azure – rede virtual (VNet), Wan virtual, ExpressRoute, gateway de VPN, gateway de NAT de rede virtual, DNS do Azure, serviço de emparelhamento e bastiões do Azure.
- [**Serviços de proteção de aplicativo**](#protect): Proteja seus aplicativos usando qualquer ou uma combinação desses serviços de rede no Azure – link privado, proteção contra DDoS, firewall, grupos de segurança de rede, firewall do aplicativo Web e pontos de extremidade de rede virtual.
- [**Serviços de entrega de aplicativos**](#deliver): entregue aplicativos na rede do Azure usando qualquer uma delas ou uma combinação desses serviços de rede no Azure – CDN (rede de distribuição de conteúdo), serviço de porta frontal do Azure, Gerenciador de tráfego, gateway de aplicativo, analisador de Internet e Load Balancer.
- [**Monitoramento de rede**](#monitor): Monitore seus recursos de rede usando qualquer ou uma combinação desses serviços de rede no Azure-observador de rede, monitor de ExpressRoute, Azure monitor ou ponto de acesso de terminal de VNET (toque).

## <a name="connectivity-services"></a><a name="connect"></a>Serviços de conectividade
 
Esta seção descreve os serviços que fornecem conectividade entre os recursos do Azure, a conectividade de uma rede local para recursos do Azure e a conectividade de Branch para Branch no Azure – rede virtual (VNet), ExpressRoute, gateway de VPN, WAN virtual, gateway NAT de rede virtual, DNS do Azure, serviço de emparelhamento do Azure e bastiões do Azure.


### <a name="virtual-network"></a><a name="vnet"></a>Rede virtual

A Rede Virtual do Azure (VNet) é o bloco de construção fundamental de sua rede privada no Azure. Você pode usar um VNets para:
- **Comunicar-se entre os recursos do Azure**: você pode implantar VMs e vários outros tipos de recursos do Azure em uma rede virtual, como ambientes de serviço Azure app, o AKs (serviço kubernetes do Azure) e conjuntos de dimensionamento de máquinas virtuais do Azure. Para exibir uma lista completa de recursos do Azure que podem ser implantados em uma rede virtual, confira [Integração de serviços de rede virtual](../virtual-network/virtual-network-for-azure-services.md).
- **Comunicar entre** si: você pode conectar redes virtuais entre si, permitindo que os recursos em qualquer rede virtual se comuniquem entre si, usando o emparelhamento de rede virtual. As redes virtuais que você conecta podem estar na mesma região ou em regiões diferentes do Azure. Para obter mais informações, consulte [Emparelhamento de rede virtual do Azure](../virtual-network/virtual-network-peering-overview.md).
- **Comunicar-se com a Internet**: todos os recursos em uma VNet podem comunicar a saída para a Internet, por padrão. Você pode se comunicar na entrada com um recurso, atribuindo um endereço IP público ou um Load Balancer público. Você também pode usar [endereços IP públicos](../virtual-network/virtual-network-public-ip-address.md) ou [Load Balancer](../load-balancer/load-balancer-overview.md) públicos para gerenciar suas conexões de saída.
- **Comunicar-se com redes locais**: você pode conectar seus computadores e redes locais a uma rede virtual usando o [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou o [ExpressRoute](../expressroute/expressroute-introduction.md).

Para obter mais informações, consulte [o que é a rede virtual do Azure?](../virtual-network/virtual-networks-overview.md).

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
O ExpressRoute permite que você estenda suas redes locais para a nuvem da Microsoft por uma conexão privada, facilitada por um provedor de conectividade. Essa conexão é privada. O tráfego não passa pela Internet. Com o ExpressRoute, é possível estabelecer conexões com os serviços em nuvem da Microsoft, como Microsoft Azure, Microsoft 365 e Dynamics 365.  Para obter mais informações, consulte [o que é o ExpressRoute?](../expressroute/expressroute-introduction.md).

:::image type="content" source="./media/networking-overview/expressroute-connection-overview.png" alt-text="Azure ExpressRoute" border="false":::

### <a name="vpn-gateway"></a><a name="vpngateway"></a>Gateway de VPN
O gateway de VPN ajuda a criar conexões criptografadas entre locais com sua rede virtual a partir de locais ou criar conexões criptografadas entre VNets. Há diferentes configurações disponíveis para conexões de gateway de VPN, como site a site, ponto a site, ou VNet a VNet.
O diagrama a seguir ilustra várias conexões VPN site a site para a mesma rede virtual.

:::image type="content" source="./media/networking-overview/vpngateway-multisite-connection-diagram.png" alt-text="Conexões de gateway de VPN do Azure site a site":::

Para obter mais informações sobre diferentes tipos de conexões VPN, consulte [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="virtual-wan"></a><a name="virtualwan"></a>WAN Virtual
WAN Virtual do Azure é um serviço de rede que fornece conectividade de ramificação otimizada e automatizada para e por meio do Azure. As regiões do Azure servem como hubs para os quais é possível optar por conectar suas ramificações. Você pode aproveitar o backbone do Azure para também conectar as ramificações e aproveitar a conectividade de ramificação a VNet. A WAN virtual do Azure reúne muitos serviços de conectividade de nuvem do Azure, como VPN site a site, ExpressRoute, VPN de usuário ponto a site em uma única interface operacional. A conectividade com VNets do Azure é estabelecida usando conexões de rede virtual. Para obter mais informações, consulte [o que é a WAN virtual do Azure?](../virtual-wan/virtual-wan-about.md).

:::image type="content" source="./media/networking-overview/virtualwan1.png" alt-text="Diagrama de WAN virtual":::

### <a name="azure-dns"></a><a name="dns"></a>DNS do Azure
O DNS do Azure é um serviço de hospedagem para domínios DNS que fornece a resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e faturamento que os outros serviços do Azure. Para obter mais informações, consulte [o que é o DNS do Azure?](../dns/dns-overview.md).

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
O serviço Azure Bastion é um novo serviço de PaaS, totalmente gerenciado pela plataforma e provisionado dentro de sua rede virtual. Ele fornece conectividade de RDP/SSH contínua e segura às suas máquinas virtuais, diretamente no portal do Azure, via TLS. Quando você se conecta usando o Azure Bastion, suas máquinas virtuais não precisam de um endereço IP público. Para obter mais informações, consulte [o que é a bastiões do Azure?](../bastion/bastion-overview.md).

:::image type="content" source="./media/networking-overview/architecture.png" alt-text="Arquitetura de bastiões do Azure":::

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>Gateway NAT da rede virtual
A NAT de Rede Virtual (conversão de endereços de rede) simplifica a conectividade de Internet somente de saída para redes virtuais. Quando configurado em uma sub-rede, toda a conectividade de saída usa seus endereços IP públicos estáticos especificados. A conectividade de saída é possível sem endereços IP públicos ou de balanceador de carga conectados diretamente a máquinas virtuais. Para obter mais informações, consulte [o que é o gateway NAT da rede virtual?](../virtual-network/nat-overview.md).

:::image type="content" source="./media/networking-overview/flow-map.png" alt-text="Gateway NAT da rede virtual":::

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a> Serviço de emparelhamento do Azure
O serviço de emparelhamento do Azure aprimora a conectividade do cliente com os serviços de nuvem da Microsoft, como Microsoft 365, Dynamics 365, serviços SaaS (software como serviço), Azure ou qualquer serviço da Microsoft acessível por meio da Internet pública. Para obter mais informações, consulte [o que é o serviço de emparelhamento do Azure?](../peering-service/about.md).

### <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure Edge Zone

A zona de borda do Azure é uma família de ofertas de Microsoft Azure que permite o processamento de dados próximo ao usuário. Você pode implantar VMs, contêineres e outros serviços do Azure selecionados em zonas de borda para atender aos requisitos de baixa latência e alta taxa de transferência dos aplicativos. Para obter mais informações, consulte [o que são as zonas do Azure Edge?](edge-zones-overview.md).

### <a name="azure-orbital"></a><a name="orbital"></a>Orbital do Azure

O Azure Orbital é uma estação terrestre como serviço baseada em nuvem totalmente gerenciada que permite que você se comunique com sua nave espacial ou constelações de satélites, faça downlink e uplink de dados, processe seus dados na nuvem, encadeie serviços com os serviços do Azure em cenários exclusivos e gere produtos para seus clientes. Esse sistema é criado com base na infraestrutura global e na rede de fibra global de baixa latência do Azure. Para obter mais informações, consulte [o que é o Azure orbital?](azure-orbital-overview.md).

:::image type="content" source="./media/azure-orbital-overview/orbital-communications-use-flow.png" alt-text="Diagrama de comunicações do Azure orbital":::

## <a name="application-protection-services"></a><a name="protect"></a>Serviços de proteção de aplicativo

Esta seção descreve os serviços de rede no Azure que ajudam a proteger seus recursos de rede-proteja seus aplicativos usando qualquer uma das combinações ou uma combinação desses serviços de rede na proteção do Azure-DDoS, link privado, firewall, firewall do aplicativo Web, grupos de segurança de rede e pontos de extremidade de serviço de rede virtual.

### <a name="ddos-protection"></a><a name="ddosprotection"></a>Proteção contra DDoS 
A [proteção contra DDoS do Azure](../ddos-protection/manage-ddos-protection.md) fornece medidas defensivas contra as ameaças de DDoS mais sofisticadas. O serviço fornece recursos aprimorados de mitigação de DDoS para seu aplicativo e recursos implantados em suas redes virtuais. Além disso, os clientes que usam a proteção contra DDoS do Azure têm acesso ao suporte de resposta rápida a DDoS para envolver especialistas em DDoS durante um ataque ativo.

:::image type="content" source="./media/networking-overview/ddos-protection.png" alt-text="Proteção contra DDoS":::

### <a name="azure-private-link"></a><a name="privatelink"></a>Link Privado do Azure
O [link privado do Azure](../private-link/private-link-overview.md) permite que você acesse os serviços de PaaS do Azure (por exemplo, armazenamento do Azure e banco de dados SQL) e serviços de parceiros/clientes do Azure hospedados em um ponto de extremidade privado em sua rede virtual.
O tráfego entre sua rede virtual e o serviço viaja a rede de backbone da Microsoft. Expor seu serviço à Internet pública não é mais necessário. Você pode criar o próprio serviço de link privado em sua rede virtual e oferecê-lo aos seus clientes.

:::image type="content" source="./media/networking-overview/private-endpoint.png" alt-text="Visão geral do ponto de extremidade privado":::

### <a name="azure-firewall"></a><a name="firewall"></a>Firewall do Azure
Firewall do Azure é um serviço de segurança de rede gerenciado e baseado em nuvem que protege seus recursos de Rede Virtual do Azure. Usando o Firewall do Azure, você pode criar, impor e registrar políticas de conectividade de aplicativo e de rede centralmente em assinaturas e redes virtuais. O Firewall do Azure usa um endereço IP público estático para seus recursos de rede virtual, permitindo que firewalls externos identifiquem o tráfego originário de sua rede virtual. 

Para obter mais informações sobre o Firewall do Azure, consulte a [documentação do firewall do Azure](../firewall/overview.md).

:::image type="content" source="./media/networking-overview/firewall-threat.png" alt-text="Visão geral do firewall":::

### <a name="web-application-firewall"></a><a name="waf"></a>Firewall do aplicativo Web
O WAF ( [Firewall do aplicativo Web](../web-application-firewall/overview.md) ) do Azure fornece proteção aos seus aplicativos Web contra explorações e vulnerabilidades comuns da Web, como injeção de SQL e script entre sites. O Azure WAF fornece proteção pronta para uso das 10 principais vulnerabilidades do OWASP por meio de regras gerenciadas. Além disso, os clientes também podem configurar regras personalizadas, que são regras gerenciadas pelo cliente para fornecer proteção adicional com base no intervalo de IP de origem e atributos de solicitação, como cabeçalhos, cookies, campos de dados de formulário ou parâmetros de cadeia de caracteres de consulta.

Os clientes podem optar por implantar o [Azure WAF com o gateway de aplicativo](../web-application-firewall/ag/ag-overview.md) , que fornece proteção regional para entidades no espaço de endereço público e privado. Os clientes também podem optar por implantar o [Azure WAF com a porta frontal](../web-application-firewall/afds/afds-overview.md) , que fornece proteção na borda da rede para pontos de extremidade públicos.

:::image type="content" source="./media/networking-overview/waf-overview.png" alt-text="Firewall do Aplicativo Web":::

### <a name="network-security-groups"></a><a name="nsg"></a>Grupos de Segurança de Rede
Você pode filtrar o tráfego de rede de e para recursos do Azure em uma rede virtual do Azure com um grupo de segurança de rede. Para saber mais, confira [Grupos de segurança de rede](../virtual-network/network-security-groups-overview.md).

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Pontos de extremidade de serviço
Os pontos de extremidade de serviço de VNet (rede virtual) estendem o espaço de endereço privado e a identidade da sua rede virtual para os serviços do Azure por meio de uma conexão direta. Os pontos de extremidade permitem que você possa garantir os recursos essenciais do serviço do Azure somente para suas redes virtuais. O tráfego de sua rede virtual para o serviço do Azure sempre permanece na rede de backbone do Microsoft Azure. Para obter mais informações, consulte [Pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md).

:::image type="content" source="./media/networking-overview/vnet-service-endpoints-overview.png" alt-text="Pontos de extremidade de serviço de rede virtual":::

## <a name="application-delivery-services"></a><a name="deliver"></a>Serviços de entrega de aplicativos

Esta seção descreve os serviços de rede no Azure que ajudam a fornecer aplicativos – rede de distribuição de conteúdo, serviço de porta frontal do Azure, Gerenciador de tráfego, Load Balancer e gateway de aplicativo.

### <a name="content-delivery-network"></a><a name="cdn"></a>Rede de Distribuição de Conteúdo
A CDN oferece aos desenvolvedores uma solução global de fornecimento rápido de conteúdo de alta largura de banda para usuários armazenando em cache o conteúdo em nós físicos estrategicamente posicionados em todo o mundo. Para obter mais informações sobre a CDN do Azure, consulte [rede de distribuição de conteúdo do Azure](../cdn/cdn-overview.md).

:::image type="content" source="./media/networking-overview/cdn-overview.png" alt-text="CDN do Azure":::

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Azure Front Door Service
O Azure Front Door Service permite que você defina, gerencie e monitore o roteamento global para seu tráfego da Web otimizando para melhor desempenho e failover global instantâneo para ter alta disponibilidade. Com o Front Door, é possível transformar seus aplicativos consumidores e empresariais globais (de várias regiões) em modernos aplicativos robustos altamente personalizados e com alto desempenho, APIs e conteúdo que alcançam um público global com o Azure. Para obter mais informações, consulte [Azure front door](../frontdoor/front-door-overview.md).

:::image type="content" source="./media/networking-overview/front-door-visual-diagram.png" alt-text="Visão geral do serviço de porta frontal":::

### <a name="traffic-manager"></a><a name="trafficmanager"></a>Gerenciador de Tráfego

O Gerenciador de tráfego do Azure é um balanceador de carga de tráfego baseado em DNS que permite que você distribua o tráfego de maneira ideal para serviços em todas as regiões globais do Azure, fornecendo alta disponibilidade e capacidade de resposta. O Gerenciador de tráfego fornece uma variedade de métodos de roteamento de tráfego para distribuir tráfego, como prioridade, peso, desempenho, geográfico, vários valores ou sub-rede. Para obter mais informações sobre métodos de roteamento de tráfego, consulte [métodos de roteamento do Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

O diagrama a seguir mostra o roteamento baseado em prioridade de ponto de extremidade com o Gerenciador de tráfego:

:::image type="content" source="./media/networking-overview/priority.png" alt-text="Método de roteamento de tráfego de &quot;prioridade&quot; do Gerenciador de tráfego do Azure":::

Para obter mais informações sobre o Gerenciador de tráfego, consulte [o que é o Gerenciador de tráfego do Azure?](../traffic-manager/traffic-manager-overview.md)

### <a name="load-balancer"></a><a name="loadbalancer"></a>Load Balancer
O Azure Load Balancer fornece balanceamento de carga de Camada 4 de baixa latência e alto desempenho para todos os protocolos TCP e UDP. Ele gerencia conexões de entrada e saída. Você pode configurar pontos de extremidade com balanceamento de carga públicos e internos. Você também pode definir regras para mapear conexões de entrada para destinos de pool de back-end usando opções de investigação de integridade TCP e HTTP para gerenciar a disponibilidade do serviço. Para saber mais sobre o balanceador de carga, leia o artigo [Visão geral sobre o balanceador de carga](../load-balancer/load-balancer-overview.md).

A imagem a seguir mostra um aplicativo de várias camadas voltado para a Internet que usa balanceadores de carga internos e externos:

:::image type="content" source="./media/networking-overview/load-balancer.png" alt-text="Exemplo de Azure Load Balancer":::

### <a name="application-gateway"></a><a name="applicationgateway"></a>Gateway de aplicativo
O Gateway de Aplicativo do Azure é um balanceador de carga do tráfego da Web que permite que você gerencie o tráfego para seus aplicativos Web. É um ADC (controlador de entrega de aplicativos) como um serviço, oferecendo vários recursos de balanceamento de carga de camada 7 para seus aplicativos. Para obter mais informações, consulte [o que é aplicativo Azure gateway?](../application-gateway/overview.md).

O diagrama a seguir mostra o roteamento baseado em caminho de URL com o gateway de aplicativo.

:::image type="content" source="./media/networking-overview/figure1-720.png" alt-text="Exemplo de gateway de aplicativo":::

## <a name="network-monitoring-services"></a><a name="monitor"></a>Serviços de monitoramento de rede
Esta seção descreve os serviços de rede no Azure que ajudam a monitorar os recursos de rede-observador de rede, Azure Monitor para redes, monitor de ExpressRoute, Azure Monitor e toque de rede virtual.

### <a name="network-watcher"></a><a name="networkwatcher"></a>Observador de Rede
Observador de Rede do Azure fornece ferramentas para monitorar, diagnosticar, exibir métricas e ativar ou desativar os logs de recursos em uma rede virtual do Azure. Para obter mais informações, consulte [o que é o observador de rede?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

### <a name="azure-monitor-for-networks-preview"></a>Azure Monitor para visualização de redes
O Azure Monitor para redes fornece uma visão abrangente da integridade e das métricas para todos os recursos de rede implantados, sem a necessidade de nenhuma configuração. Ele também fornece acesso a recursos de monitoramento de rede, como o [Monitor de conexão](../network-watcher/connection-monitor-overview.md), o [log de fluxo para grupos de segurança de rede](../network-watcher/network-watcher-nsg-flow-logging-overview.md)e [análise de tráfego](../network-watcher/traffic-analytics.md). Para obter mais informações, consulte [Azure monitor para visualização de redes](../azure-monitor/insights/network-insights-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>Monitor do ExpressRoute
Para saber mais sobre como exibir as métricas de circuito do ExpressRoute, logs de recursos e alertas, consulte [monitoramento, métricas e alertas do expressroute](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
O Azure Monitor maximiza a disponibilidade e o desempenho de seus aplicativos fornecendo uma solução abrangente para coletar, analisar e agir em relação a dados telemétricos de seus ambientes locais e de nuvem. Ele ajuda a entender o desempenho de seus aplicativos, além de identificar de maneira proativa os problemas que os estão afetando e os recursos dos quais eles dependem. Para obter mais informações, consulte [Azure monitor visão geral](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="virtual-network-tap"></a><a name="vnettap"></a>TOQUE da rede virtual
O TAP (Ponto de Acesso do Terminal) de rede virtual do Azure permite que você transmita o tráfego de rede por streaming continuamente da máquina virtual para uma ferramenta de coleta de pacotes ou de análise de rede. A ferramenta de análise ou de coleta é fornecida por um parceiro de [solução de virtualização de rede](https://azure.microsoft.com/solutions/network-appliances/).

A imagem a seguir mostra como o toque da rede virtual funciona:

:::image type="content" source="./media/networking-overview/virtual-network-tap-architecture.png" alt-text="Como o TAP de rede virtual funciona":::

Para obter mais informações, consulte [o que é toque de rede virtual](../virtual-network/virtual-network-tap-overview.md).

## <a name="next-steps"></a>Próximas etapas

- Crie sua primeira rede virtual e conecte algumas VMs a ela, concluindo as etapas no artigo [criar sua primeira rede virtual](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) .
- Conecte seu computador a uma rede virtual concluindo as etapas no [artigo configurar uma conexão ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Faça o balanceamento de carga do tráfego de Internet para servidores públicos concluindo as etapas em [Criar um balanceador de carga voltado para a Internet](../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).