---
title: Rede do Azure | Microsoft Docs
description: Saiba mais sobre os serviços de rede no Azure e seus recursos.
services: networking
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2019
ms.author: kumud
ms.openlocfilehash: 9fb7fc9b4f0e5af0847876ff41b6a307f8a09749
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348020"
---
# <a name="azure-networking"></a>Rede do Azure

Os serviços de rede no Azure fornecem uma variedade de recursos de rede que podem ser usados juntos ou separados. Clique em qualquer um dos seguintes recursos principais para saber mais sobre eles:
- [**Serviços de conectividade**](#connect): Conecte recursos do Azure e recursos locais usando qualquer uma delas ou uma combinação desses serviços de rede no Azure – rede virtual (VNet), WAN virtual, ExpressRoute, gateway de VPN, DNS do Azure ou bastiões do Azure.
- [**Serviços de proteção de aplicativo**](#protect) Proteja seus aplicativos usando qualquer um ou uma combinação desses serviços de rede no Azure – proteção contra DDoS, firewall, grupos de segurança de rede, firewall de aplicativo Web ou pontos de extremidade de rede virtual.
- [**Serviços de entrega de aplicativos**](#deliver) Entregue aplicativos na rede do Azure usando qualquer um ou uma combinação desses serviços de rede no Azure – CDN (rede de distribuição de conteúdo), serviço de porta frontal do Azure, Gerenciador de tráfego, gateway de aplicativo ou Load Balancer.
- [**Monitoramento de rede**](#monitor) – monitore seus recursos de rede usando qualquer um ou uma combinação desses serviços de rede no Azure-observador de rede, monitor de ExpressRoute, Azure monitor ou ponto de acesso de terminal de VNET (toque).

## <a name="connect"></a>Serviços de conectividade
 
Esta seção descreve os serviços que fornecem conectividade entre os recursos do Azure, a conectividade de uma rede local para recursos do Azure e a conectividade de Branch para Branch no Azure-rede virtual, ExpressRoute, gateway de VPN, WAN virtual, DNS e Azure Bastiões.

|Serviço|Por que usar?|Cenários|
|---|---|---|
|[Rede virtual](#vnet)|Permite que os recursos do Azure se comuniquem com segurança entre si, Internet e redes locais.| <p>[Filtrar tráfego](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[Rotear o tráfego de rede](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[Restringir o acesso à rede para os recursos](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[Conectar redes virtuais](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[ExpressRoute](#expressroute)|Estende suas redes locais para a nuvem da Microsoft por uma conexão privada, facilitada por um provedor de conectividade.|<p>[Criar e modificar um circuito do ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[Criar e modificar o emparelhamento de um circuito do ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[Vincular uma rede virtual a um circuito do ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[Configurar e gerenciar filtros de rota para circuitos do ExpressRoute](../expressroute/how-to-routefilter-portal.md)</p>|
|[Gateway de VPN](#vpngateway)|Envia o tráfego criptografado entre uma rede virtual do Azure e uma localização local pela Internet pública.|<p>[Conexões site a site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[Conexões VNet a VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[Conexões ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[WAN virtual](#virtualwan)|Otimiza e automatiza a conectividade de ramificação para, e por meio do Azure. As regiões do Azure servem como hubs para os quais é possível optar por conectar suas ramificações.|<p>[Conexões site a site](../virtual-wan/virtual-wan-site-to-site-portal.md), conexões do [ExpressRoute](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[DNS do Azure](#dns)|Hospeda domínios DNS que fornecem resolução de nomes usando Microsoft Azure infraestrutura.|<p>[Hospede seu domínio no DNS do Azure](../dns/dns-delegate-domain-azure-dns.md)</p><p>[Criar registros DNS para um aplicativo Web](../dns/dns-web-sites-custom-domain.md)</p> <p>[Criar um registro de alias para o Gerenciador de tráfego](../dns/tutorial-alias-tm.md)</p> <p>[Criar um registro de alias para o endereço IP público](../dns/tutorial-alias-pip.md)</p> <p>[Criar um registro de alias para o registro de recurso de zona](../dns/tutorial-alias-rr.md)</p>|
|[Bastiões do Azure (versão prévia)](#bastion)|Ele fornece conectividade de RDP/SSH contínua e segura a suas máquinas virtuais, diretamente no portal do Azure, usando SSL. Quando você se conecta por meio de bastiões do Azure, suas máquinas virtuais não precisam de um endereço IP público|<p>[Criar um host de bastiões do Azure](../bastion/bastion-create-host-portal.md)</p><p>[Conectar-se usando SSH em uma VM Linux](../bastion/bastion-connect-vm-ssh.md)</p><p>[Conectar-se usando o RDP em uma VM do Windows](/bastion/bastion-connect-vm-rdp.md)</p>|
||||


### <a name="vnet"></a>Rede virtual

A Rede Virtual do Azure (VNet) é o bloco de construção fundamental de sua rede privada no Azure. Você pode usar um VNets para:
- **Comunicar-se entre os recursos do Azure**: você pode implantar VMs e vários outros tipos de recursos do Azure em uma rede virtual, tais como Ambientes de Serviço de Aplicativo do Azure, o Serviço de Kubernetes do Azure (AKS) e Conjuntos de Dimensionamento de Máquinas Virtuais do Azure. Para exibir uma lista completa de recursos do Azure que podem ser implantados em uma rede virtual, confira [Integração de serviços de rede virtual](../virtual-network/virtual-network-for-azure-services.md).
- **Comunicar entre**si: É possível conectar redes virtuais umas às outras, permitindo que os recursos em uma rede virtual se comuniquem com os de qualquer outra por meio de emparelhamento de rede virtual. As redes virtuais que você conecta podem estar na mesma região ou em regiões diferentes do Azure. Para obter mais informações, consulte [Emparelhamento de rede virtual do Azure](../virtual-network/virtual-network-peering-overview.md).
- **Comunicar-se com a Internet**: Por padrão, todos os recursos em uma VNet podem se comunicar na saída com a Internet. Você pode se comunicar na entrada com um recurso, atribuindo um endereço IP público ou um Load Balancer público. Você também pode usar [endereços IP públicos](../virtual-network/virtual-network-public-ip-address.md) ou [Load Balancer](../load-balancer/load-balancer-overview.md) públicos para gerenciar suas conexões de saída.
- **Comunicar-se com redes locais**: Você pode conectar seus computadores e redes locais a uma rede virtual usando o [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) ou o [ExpressRoute](../expressroute/expressroute-introduction.md).

Para obter mais informações, consulte [o que é a rede virtual do Azure?](../virtual-network/virtual-networks-overview.md).

### <a name="expressroute"></a>ExpressRoute
O ExpressRoute permite que você estenda suas redes locais para a nuvem da Microsoft por uma conexão privada, facilitada por um provedor de conectividade. Essa conexão é privada. O tráfego não passa pela Internet. Com o ExpressRoute, você pode estabelecer conexões com os serviços de nuvem da Microsoft, como o Microsoft Azure, o Office 365 e o Dynamic 365.  Para obter mais informações, consulte [o que é o ExpressRoute?](../expressroute/expressroute-introduction.md).

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpngateway"></a>Gateway de VPN
O gateway de VPN ajuda a criar conexões criptografadas entre locais com sua rede virtual a partir de locais ou criar conexões criptografadas entre VNets. Há diferentes configurações disponíveis para conexões de gateway de VPN, como site a site, ponto a site ou VNet para VNet.
O diagrama a seguir ilustra várias conexões VPN site a site para a mesma rede virtual.

![Conexões de gateway de VPN do Azure site a site](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

Para obter mais informações sobre diferentes tipos de conexões VPN, consulte [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="virtualwan"></a>WAN virtual
WAN Virtual do Azure é um serviço de rede que fornece conectividade de ramificação otimizada e automatizada para e por meio do Azure. As regiões do Azure servem como hubs para os quais é possível optar por conectar suas ramificações. Você pode aproveitar o backbone do Azure para também conectar as ramificações e aproveitar a conectividade de ramificação a VNet. A WAN virtual do Azure reúne muitos serviços de conectividade de nuvem do Azure, como VPN site a site, ExpressRoute, VPN de usuário ponto a site em uma única interface operacional. A conectividade com VNets do Azure é estabelecida usando conexões de rede virtual. Para obter mais informações, consulte [o que é a WAN virtual do Azure?](../virtual-wan/virtual-wan-about.md).

![Diagrama de WAN virtual](./media/networking-overview/virtualwan1.png)

### <a name="dns"></a>DNS do Azure
O DNS do Azure é um serviço de hospedagem para domínios DNS que fornece a resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e faturamento que os outros serviços do Azure. Para obter mais informações, consulte [o que é o DNS do Azure?](../dns/dns-overview.md).

### <a name="bastion"></a>Bastiões do Azure (versão prévia)
O serviço Azure Bastion é um novo serviço de PaaS, totalmente gerenciado pela plataforma e provisionado dentro de sua rede virtual. Ele fornece conectividade de RDP/SSH contínua e segura a suas máquinas virtuais, diretamente no portal do Azure, usando SSL. Quando você se conecta usando o Azure Bastion, suas máquinas virtuais não precisam de um endereço IP público. Para obter mais informações, consulte [o que é a bastiões do Azure?](/bastion/bastion-overview.md).

![Arquitetura de bastiões do Azure](./media/networking-overview/architecture.png)


## <a name="protect"></a>Serviços de proteção de aplicativo

Esta seção descreve os serviços de rede no Azure que ajudam a proteger os recursos de rede – proteção contra DDoS, firewall do aplicativo Web, firewall do Azure, grupos de segurança de rede e pontos de extremidade de serviço.

|Serviço|Por que usar?|Cenário|
|---|---|---|
|[Proteção contra DDoS](#ddosprotection) |Alta disponibilidade para seus aplicativos com proteção contra encargos de tráfego IP em excesso|[Gerenciar a proteção contra DDoS do Azure](../virtual-network/manage-ddos-protection.md)|
|[Firewall do Aplicativo Web](#waf)|<p>O [Azure WAF com o gateway de aplicativo](../application-gateway/waf-overview.md) fornece proteção regional para entidades no espaço de endereço público e privado</p><p>O [Azure WAF com a porta frontal](../frontdoor/waf-overview.md) fornece proteção na borda da rede para pontos de extremidade públicos.</p>|<p>[Configurar regras de proteção de bot](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[Configurar código de resposta personalizado](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[Configurar regras de restrição de IP](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[Configurar regra de limite de taxa](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Firewall do Azure](#firewall)|Firewall do Azure é um serviço de segurança de rede gerenciado e baseado em nuvem que protege seus recursos de Rede Virtual do Azure. É um firewall como serviço totalmente com estado com alta disponibilidade interna e escalabilidade de nuvem irrestrita.|<p>[Implantar um firewall do Azure em uma vnet](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[-Implantar um firewall do Azure em uma rede híbrida](../firewall/tutorial-hybrid-ps.md)</p> <p>[Filtrar o tráfego de entrada com o Firewall do Azure DNAT](../firewall/tutorial-firewall-dnat.md)</p>|
|[Grupos de segurança de rede](#nsg)|Controle completo de nó final distribuído granular na VM/sub-rede para todos os fluxos de tráfego de rede|[Filtrar o tráfego de rede usando grupos de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)|
|[Pontos de extremidade de serviço de rede virtual](#serviceendpoints)|Permite limitar o acesso à rede para alguns recursos de serviço do Azure a uma sub-rede de rede virtual|[Restringir o acesso à rede para recursos PaaS](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
|||
### <a name="ddosprotection"></a>Proteção contra DDoS 
A [proteção contra DDoS do Azure](../virtual-network/manage-ddos-protection.md) fornece medidas defensivas contra as ameaças de DDoS mais sofisticadas. O serviço fornece recursos aprimorados de mitigação de DDoS para seu aplicativo e recursos implantados em suas redes virtuais. Além disso, os clientes que usam a proteção contra DDoS do Azure têm acesso ao suporte de resposta rápida a DDoS para envolver especialistas em DDoS durante um ataque ativo.

![Proteção contra DDoS](./media/networking-overview/ddos-protection.png)

### <a name="waf"></a>Firewall do aplicativo Web

O WAF (firewall do aplicativo Web) do Azure fornece proteção aos seus aplicativos Web contra explorações e vulnerabilidades comuns da Web, como injeção de SQL e script entre sites. O Azure WAF fornece proteção pronta para uso das 10 principais vulnerabilidades do OWASP por meio de regras gerenciadas. Além disso, os clientes também podem configurar regras personalizadas, que são regras gerenciadas pelo cliente para fornecer proteção adicional com base no intervalo de IP de origem e atributos de solicitação, como cabeçalhos, cookies, campos de dados de formulário ou parâmetros de cadeia de caracteres de consulta.

Os clientes podem optar por implantar o [Azure WAF com o gateway de aplicativo](../application-gateway/waf-overview.md) , que fornece proteção regional para entidades no espaço de endereço público e privado. Os clientes também podem optar por implantar o [Azure WAF com a porta frontal](../frontdoor/waf-overview.md) , que fornece proteção na borda da rede para pontos de extremidade públicos.

![Firewall de Aplicativo Web](./media/networking-overview/waf-overview.png)


### <a name="firewall"></a>Firewall do Azure
Firewall do Azure é um serviço de segurança de rede gerenciado e baseado em nuvem que protege seus recursos de Rede Virtual do Azure. Usando o Firewall do Azure, você pode criar, impor e registrar políticas de conectividade de aplicativo e de rede centralmente em assinaturas e redes virtuais. O Firewall do Azure usa um endereço IP público estático para seus recursos de rede virtual, permitindo que firewalls externos identifiquem o tráfego originário de sua rede virtual. 

Para obter mais informações sobre o Firewall do Azure, consulte a [documentação do firewall do Azure](../firewall/overview.md).

![Visão geral do firewall](./media/networking-overview/firewall-threat.png)

### <a name="nsg"></a>Grupos de segurança de rede
Você pode filtrar o tráfego de rede de e para recursos do Azure em uma rede virtual do Azure com um grupo de segurança de rede. Para obter mais informações, consulte [visão geral de segurança](../virtual-network/security-overview.md).

### <a name="serviceendpoints"></a>Pontos de extremidade de serviço
Os pontos de extremidade de serviço de VNet (rede virtual) estendem o espaço de endereço privado e a identidade da sua rede virtual para os serviços do Azure por meio de uma conexão direta. Os pontos de extremidade permitem que você possa garantir os recursos essenciais do serviço do Azure somente para suas redes virtuais. O tráfego de sua rede virtual para o serviço do Azure sempre permanece na rede de backbone do Microsoft Azure. Para obter mais informações, consulte [Pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md).

![Pontos de extremidade de serviço de rede virtual](./media/networking-overview/vnet-service-endpoints-overview.png)

## <a name="deliver"></a>Serviços de entrega de aplicativos

Esta seção descreve os serviços de rede no Azure que ajudam a fornecer aplicativos – CDN (rede de distribuição de conteúdo), serviço de porta frontal do Azure, Gerenciador de tráfego, gateway de aplicativo e Load Balancer.

|Serviço|Por que usar?|Cenário|
|---|---|---|
|[Rede de distribuição de conteúdo](#cdn)|Fornece conteúdo de alta largura de banda aos usuários. O CDNs armazena o conteúdo em cache em servidores de borda em locais POP (ponto de presença) que estão próximos aos usuários finais, para minimizar a latência|<p>[Adicionar CDN a um aplicativo Web](../cdn/cdn-add-to-web-app.md)</p> <p>[-Acessar blobs de armazenamento usando um domínio personalizado da CDN do Azure por HTTPS](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[Adicionar um domínio personalizado ao ponto de extremidade da CDN do Azure](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[Configure HTTPS em um domínio personalizado da CDN do Azure](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate)</p>|
|[Serviço de porta frontal do Azure](#frontdoor)|Permite que você defina, gerencie e monitore o roteamento global para o tráfego da Web otimizando para melhor desempenho e failover global instantâneo para alta disponibilidade.|<p>[Adicionar um domínio personalizado ao serviço de porta frontal do Azure](../frontdoor/front-door-custom-domain.md)</p> <p>[Configurar HTTPS em um domínio personalizado de porta frontal](../frontdoor/front-door-custom-domain-https.md)</p><p>[Configurar a política de firewall do aplicativo Web de filtragem geográfica](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[Gerenciador de Tráfego](#trafficmanager)|Distribui o tráfego com base no DNS para serviços em regiões globais do Azure, fornecendo alta disponibilidade e capacidade de resposta|<p> [Rotear tráfego de baixa latência](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[Rotear tráfego para um ponto de extremidade de prioridade](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [Controlar o tráfego com pontos de extremidade ponderados](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[Rotear o tráfego com base na localização geográfica do ponto de extremidade](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [Rotear o tráfego com base na sub-rede do usuário](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[Balanceador de Carga](#loadbalancer)|Fornece balanceamento de carga regional roteando o tráfego entre zonas de disponibilidade e em seu VNets. Fornece balanceamento de carga interno roteando o tráfego entre e entre seus recursos para criar seu aplicativo regional.|<p> [Balancear tráfego de carga da internet para VMs](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[Balancear a carga de tráfego entre VMs dentro de uma rede virtual](../load-balancer/tutorial-load-balancer-basic-internal-portal.md)<p>[Tráfego de encaminhamento de porta para uma porta específica em VMs específicas](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [Configurar o balanceamento de carga e as regras de saída](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[Gateway de Aplicativo](#applicationgateway)|O Gateway de Aplicativo do Azure é um balanceador de carga do tráfego da Web que permite que você gerencie o tráfego para seus aplicativos Web.|<p>[Tráfego direto da Web com o gateway de Aplicativo Azure](../application-gateway/quick-create-portal.md)</p><p>[Configurar um gateway de aplicativo com terminação SSL](../application-gateway/create-ssl-portal.md)</p><p>[Criar um gateway de aplicativo com o redirecionamento baseado em caminhos de URL](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="cdn"></a>Rede de distribuição de conteúdo
A CDN oferece aos desenvolvedores uma solução global de fornecimento rápido de conteúdo de alta largura de banda para usuários armazenando em cache o conteúdo em nós físicos estrategicamente posicionados em todo o mundo. Para obter mais informações sobre a CDN do Azure, consulte [rede de distribuição de conteúdo do Azure](../cdn/cdn-overview.md)

![Azure CDN](./media/networking-overview/cdn-overview.png)

### <a name="frontdoor"></a>Serviço de porta frontal do Azure
O Azure Front Door Service permite que você defina, gerencie e monitore o roteamento global para seu tráfego da Web otimizando para melhor desempenho e failover global instantâneo para ter alta disponibilidade. Com o Front Door, é possível transformar seus aplicativos consumidores e empresariais globais (de várias regiões) em modernos aplicativos robustos altamente personalizados e com alto desempenho, APIs e conteúdo que alcançam um público global com o Azure. Para obter mais informações, consulte [Azure front door](../frontdoor/front-door-overview.md).


### <a name="trafficmanager"></a>Gerenciador de tráfego

O Gerenciador de tráfego do Azure é um balanceador de carga de tráfego baseado em DNS que permite que você distribua o tráfego de maneira ideal para serviços em todas as regiões globais do Azure, fornecendo alta disponibilidade e capacidade de resposta. O Gerenciador de tráfego fornece uma variedade de métodos de roteamento de tráfego para distribuir tráfego, como prioridade, peso, desempenho, geográfico, vários valores ou sub-rede. Para obter mais informações sobre métodos de roteamento de tráfego, consulte [métodos de roteamento do Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

O diagrama a seguir mostra o roteamento baseado em prioridade de ponto de extremidade com o Gerenciador de tráfego:

![Método de roteamento de tráfego por “Prioridade” do Gerenciador de Tráfego do Azure](./media/networking-overview/priority.png)

Para obter mais informações sobre o Gerenciador de tráfego, consulte [o que é o Gerenciador de tráfego do Azure?](../traffic-manager/traffic-manager-overview.md)

### <a name="loadbalancer"></a>Load Balancer
O Azure Load Balancer fornece balanceamento de carga de Camada 4 de baixa latência e alto desempenho para todos os protocolos TCP e UDP. Ele gerencia conexões de entrada e saída. Você pode configurar pontos de extremidade com balanceamento de carga públicos e internos. Você também pode definir regras para mapear conexões de entrada para destinos de pool de back-end usando opções de investigação de integridade TCP e HTTP para gerenciar a disponibilidade do serviço. Para saber mais sobre o balanceador de carga, leia o artigo [Visão geral sobre o balanceador de carga](../load-balancer/load-balancer-overview.md).

A imagem a seguir mostra um aplicativo de várias camadas voltado para a Internet que usa balanceadores de carga internos e externos:

![Exemplo de Azure Load Balancer](./media/networking-overview/IC744147.png)


### <a name="applicationgateway"></a>Gateway de aplicativo
O Gateway de Aplicativo do Azure é um balanceador de carga do tráfego da Web que permite que você gerencie o tráfego para seus aplicativos Web. É um ADC (controlador de entrega de aplicativos) como um serviço, oferecendo vários recursos de balanceamento de carga de camada 7 para seus aplicativos. Para obter mais informações, consulte [o que é aplicativo Azure gateway?](../application-gateway/overview.md).

O diagrama a seguir mostra o roteamento baseado em caminho de URL com o gateway de aplicativo.

![Exemplo de gateway de aplicativo](./media/networking-overview/figure1-720.png)

## <a name="monitor"></a>Serviços de monitoramento de rede
Esta seção descreve os serviços de rede no Azure que ajudam a monitorar seus recursos de rede-observador de rede, monitor de ExpressRoute, Azure Monitor e toque de rede virtual.

|Serviço|Por que usar?|Cenário|
|---|---|---|
|[Observador de Rede](#networkwatcher)|Ajuda a monitorar e solucionar problemas de conectividade, ajuda a diagnosticar problemas de VPN, NSG e roteamento, capturar pacotes em sua VM, automatiza o disparo de ferramentas de diagnóstico usando Azure Functions e aplicativos lógicos|<p>[Diagnosticar problema de filtro de tráfego de VM](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[Diagnosticar problema de roteamento de VM](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[Monitorar comunicações entre VMs](../network-watcher/connection-monitor.md)</p><p>[Diagnosticar um problema de comunicação entre redes](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[Registrar o tráfego de rede de e para uma VM](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[ExpressRoute Monitor](#expressroutemonitor)|Fornece monitoramento em tempo real de desempenho, disponibilidade e utilização da rede, ajuda com a descoberta automática da topologia de rede, fornece um isolamento de falhas mais rápido, detecta problemas de rede transitórios, ajuda a analisar o desempenho histórico da rede características, suporte a várias assinaturas|<p>[Configurar o Monitor de Desempenho de Rede para ExpressRoute](../expressroute/how-to-npm.md)</p><p>[Monitoramento, métricas e alertas do ExpressRoute](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Azure Monitor](#azuremonitor)|Ajuda a entender como os aplicativos estão sendo executados e identifica de forma proativa os problemas que os afetam e os recursos dos quais eles dependem.|<p>[Métricas e alertas do Gerenciador de tráfego](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[Diagnóstico do Azure monitor para Standard Load Balancer](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[Monitorar os logs e as métricas do firewall do Azure](../firewall/tutorial-diagnostics.md)</p><p>[Firewall do aplicativo web do Azure de monitoramento e registro em log](../frontdoor/waf-front-door-monitor.md)</p>|
|[TOQUE da rede virtual](#vnettap)|Fornece streaming contínuo de tráfego de rede de máquina virtual para coletor de pacotes, permite soluções de gerenciamento de desempenho de aplicativos e de rede e ferramentas de análise de segurança|[Criar um recurso de toque de VNet](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="networkwatcher"></a>Observador de rede
Observador de Rede do Azure fornece ferramentas para monitorar, diagnosticar, exibir métricas e ativar ou desativar os logs de recursos em uma rede virtual do Azure. Para obter mais informações, consulte [o que é o observador de rede?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="expressroutemonitor"></a>Monitor do ExpressRoute
Para saber mais sobre como exibir as métricas de circuito do ExpressRoute, logs e alertas de diagnóstico, consulte [monitoramento, métricas e alertas do expressroute](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="azuremonitor"></a>Azure Monitor
O Azure Monitor maximiza a disponibilidade e o desempenho de seus aplicativos fornecendo uma solução abrangente para coletar, analisar e agir em relação a dados telemétricos de seus ambientes locais e de nuvem. Ele ajuda a entender o desempenho de seus aplicativos, além de identificar de maneira proativa os problemas que os estão afetando e os recursos dos quais eles dependem. Para obter mais informações, consulte [Azure monitor visão geral](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="vnettap"></a>TOQUE da rede virtual
O TAP (Ponto de Acesso do Terminal) de rede virtual do Azure permite que você transmita o tráfego de rede por streaming continuamente da máquina virtual para uma ferramenta de coleta de pacotes ou de análise de rede. A ferramenta de análise ou de coleta é fornecida por um parceiro de [solução de virtualização de rede](https://azure.microsoft.com/solutions/network-appliances/). 

A imagem seguir mostra como o TAP de rede virtual funciona. 

![Como o TAP de rede virtual funciona](./media/networking-overview/virtual-network-tap-architecture.png)

Para obter mais informações, consulte [o que é toque de rede virtual](../virtual-network/virtual-network-tap-overview.md).

## <a name="next-steps"></a>Próximas etapas

- Criar sua primeira VNet e conecte algumas VMs a ela executando as etapas no artigo [Criar sua primeira rede virtual](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Conecte seu computador a uma VNet concluindo as etapas no [artigo configurar uma conexão ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Faça o balanceamento de carga do tráfego de Internet para servidores públicos concluindo as etapas em [Criar um balanceador de carga voltado para a Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
 
 
   
