---
title: Segurança de Rede do Azure | Microsoft Docs
description: Aprenda sobre os serviços de computação baseados em nuvem que incluem uma ampla seleção de instâncias e serviços de computação, os quais podem ser escalados verticalmente de forma automática para atender às necessidades de seu aplicativo ou empresa.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: f684a9d7bca77a8aa3aa60f5079dda0ce3b58a1c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587231"
---
# <a name="azure-network-security"></a>Segurança de rede do Azure

Sabemos que a segurança é o primeiro trabalho na nuvem e o quanto é importante que você encontre informações precisas e atualizadas sobre a segurança do Azure. Uma das melhores razões para usar o Azure para seus aplicativos e serviços é aproveitar sua ampla matriz de ferramentas e recursos de segurança. Essas ferramentas e recursos ajudam a tornar possível criar soluções seguras na plataforma do Azure.

O Microsoft Azure fornece confidencialidade, integridade e disponibilidade dos dados do cliente, ao mesmo tempo que também permite uma responsabilidade transparente. Para ajudar você a entender melhor a coleção de controles de segurança de rede implementada no Microsoft Azure da perspectiva do cliente, este artigo, "Segurança de Rede do Azure", foi escrito para fornecer uma visão abrangente dos controles de segurança de rede disponíveis com o Microsoft Azure.

Este artigo é destinado a lhe informar sobre uma ampla variedade de controles de rede que você pode configurar para aumentar a segurança das soluções que você implanta no Azure. Se você estiver interessado em saber o que a Microsoft faz para proteger a malha de rede da plataforma Azure em si, consulte a seção segurança do Azure no [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/cloudservices/azure).

## <a name="azure-platform"></a>Plataforma do Azure

O Azure é uma plataforma de serviço de nuvem pública que dá suporte a uma ampla seleção de sistemas operacionais, linguagens de programação, estruturas, ferramentas, bancos de dados e dispositivos.  Ele pode executar contêineres do Linux com a integração com o Docker, criar aplicativos com JavaScript, Python, .NET, PHP, Java e Node.js e criar back-ends para dispositivos iOS, Android e Windows. Os serviços de nuvem do Azure dão suporte às mesmas tecnologias com as quais milhões de desenvolvedores e profissionais de TI já contam e nas quais confiam.

Ao se basear ou migrar ativos de TI para um provedor de serviços de nuvem pública, você está confiando na capacidade dessa organização de proteger seus aplicativos e dados com os serviços e os controles que ela oferece para gerenciar a segurança de seus ativos baseados em nuvem.

A infraestrutura do Azure foi projetada desde a instalação até os aplicativos para hospedar milhões de clientes simultaneamente e fornece uma base confiável com a qual as empresas podem atender aos seus requisitos de segurança. Além disso, o Azure oferece uma extensa coleção de opções de segurança configuráveis e a capacidade de controlá-las, de forma que você possa personalizar a segurança para atender aos requisitos exclusivos das implantações de sua organização.

## <a name="abstract"></a>Resumo

Os serviços de nuvem pública da Microsoft entregam serviços e estrutura em larga escala, recursos de nível empresarial e várias opções de conectividade híbrida. Você pode optar por acessar esses serviços através da Internet ou com o Azure ExpressRoute, que fornece conectividade de rede privada. A plataforma Microsoft Azure permite que você estenda facilmente sua infraestrutura para a nuvem e criem arquiteturas com várias camadas. Além disso, terceiros podem habilitar recursos avançados oferecendo serviços de segurança e soluções de virtualização.

Os serviços de rede do Azure maximizam a flexibilidade, disponibilidade, resiliência, segurança e integridade por design. Este white paper fornece detalhes sobre as funções de rede do Azure e informações sobre como os clientes podem usar recursos de segurança nativos do Azure para ajudar a proteger seus ativos de informações.

Os públicos-alvo deste white paper incluem:

- Gerentes técnicos, administradores de rede e desenvolvedores que estão procurando soluções de segurança disponíveis e com suporte no Azure.

-   SMEs ou executivos de processo de negócios que desejam obter uma visão geral de tecnologias de rede do Azure e serviços que são relevantes em discussões sobre segurança de rede na nuvem pública do Azure.

## <a name="azure-networking-big-picture"></a>Visão geral de rede do Azure
O Microsoft Azure inclui uma infraestrutura de rede robusta para dar suporte a seus requisitos de conectividade de aplicativo e de serviço. A conectividade de rede é possível entre os recursos localizados no Azure, entre os recursos locais e aqueles hospedados no Azure e de origem e destino à Internet e ao Azure.

![Visão Geral de Rede do Azure](media/azure-network-security/azure-network-security-fig-1.png)

A [infraestrutura de rede do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) permite que você conecte com segurança os recursos do Azure uns com os outros usando redes virtuais (VNets). Uma VNet é uma representação da sua própria rede na nuvem. Uma VNet é um isolamento lógico da rede de nuvem do Azure dedicada à sua assinatura. Você pode conectar VNets às suas redes locais.

O Azure dá suporte a conectividade do link WAN dedicado para sua rede local e a uma Rede Virtual do Azure com o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). O link entre o Azure e seu site utiliza uma conexão dedicada que não passa pela Internet pública. Se seu aplicativo Azure estiver sendo executado em vários datacenters, você poderá usar o [Gerenciador de Tráfego do Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) para rotear solicitações de usuários de maneira inteligente entre as instâncias do aplicativo. Você também pode rotear o tráfego para serviços que não estejam sendo executados no Azure, desde que possam ser acessados pela Internet.

## <a name="enterprise-view-of-azure-networking-components"></a>Exibição para empresa de componentes de rede do Azure
O Azure tem muitos componentes de rede que são relevantes para discussões sobre segurança de rede. Descrevemos esses componentes de rede e nos concentramos nos problemas de segurança relacionados a eles.

> [!Note]
> Nem todos os aspectos de rede do Azure são descritos – abordamos apenas aqueles considerados essenciais para planejar e projetar uma infraestrutura de rede segura em torno dos serviços e aplicativos que você implanta no Azure.

Neste documento, abordaremos os seguintes recursos corporativos de rede do Azure:

-   Conectividade de rede básica

-   Conectividade Híbrida

-   Controles de Segurança

-   Validação de rede

### <a name="basic-network-connectivity"></a>Conectividade de rede básica

O serviço de [Rede Virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) permite que você conecte com segurança os recursos do Azure usando redes virtuais (VNets). Uma VNet é uma representação da sua própria rede na nuvem. Uma VNet é um isolamento lógico da infraestrutura de rede do Azure dedicada à sua assinatura. Você também pode conectar VNets entre si e às suas redes locais usando VPNs site a site e [links WAN](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) dedicados.

![Conectividade de rede básica](media/azure-network-security/azure-network-security-fig-2.png)

Com o entendimento de que você usar máquinas virtuais para hospedar servidores no Azure, a pergunta é como as VMs se conectam a uma rede. A resposta é que as VMs se conectam a uma [Rede Virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Redes Virtuais do Azure são como redes virtuais que você usa localmente com suas próprias soluções de plataforma de virtualização, como o Microsoft Hyper-V ou VMware.

#### <a name="intra-vnet-connectivity"></a>Conectividade entre VNets

Você pode conectar as VNets entre si, permitindo que os recursos conectados a qualquer VNet se comuniquem entre si através das VNets. Use uma ou as duas opções a seguir para conectar VNets entre si:

- **Emparelhamento:** Permite que os recursos conectados a diferentes VNets do Azure na mesma localização do Azure se comuniquem entre si. A largura de banda e a latência entre as VNets são as mesmas que se os recursos estivessem conectados à mesma VNet. Para saber mais sobre emparelhamento, leia [Emparelhamento de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

  ![Emparelhamento](media/azure-network-security/azure-network-security-fig-3.png)

- **Conexão de VNet para VNet:** Permite recursos conectados a uma VNet do Azure diferente dentro dos mesmos locais ou em locais diferentes do Azure. Ao contrário do emparelhamento, a largura de banda é limitada entre as VNets, pois o tráfego deve fluir por um Gateway de VPN do Azure.

![Conexão de VNet para VNet](media/azure-network-security/azure-network-security-fig-4.png)


Para saber mais sobre como conectar VNets com uma conexão VNet para VNet, leia o [artigo Configurar uma conexão de VNet para VNet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="azure-virtual-network-capabilities"></a>Recursos de rede virtual do Azure:

Como você pode ver, uma rede Virtual do Azure fornece máquinas virtuais para se conectar à rede para que elas possam se conectar a outros recursos de rede de forma segura. No entanto, a conectividade básica é apenas o começo. Os recursos do serviço de Rede Virtual do Azure a seguir expõem as características de segurança da Rede Virtual do Azure:

-   Isolamento

-   Conectividade com a Internet

-   Conectividade de recursos do Azure

-   Conectividade de VNet

-   Conectividade local

-   Filtragem de tráfego

-   Roteamento

**Isolamento**

As VNets são [isoladas](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) umas das outras. Crie VNets separadas para desenvolvimento, teste e produção que usem os mesmos blocos de endereços [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). Por outro lado, você pode criar várias VNets que usam blocos de endereço CIDR diferentes e conectam as redes. Você pode segmentar uma VNet em várias sub-redes.

O Azure fornece resolução de nome interna para instâncias de função de VMs e [Serviços de Nuvem](https://azure.microsoft.com/services/cloud-services/) conectadas a uma VNet. Opcionalmente, configure uma VNet para usar seus próprios servidores DNS, em vez de usar a resolução de nome interna do Azure.

É possível implementar várias VNets dentro de cada [assinatura](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) e [região](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure. Cada VNet é isolada de outras VNets. Para cada VNet, você pode:

-   Especificar um espaço de endereço IP privado personalizado usando endereços públicos e privados (RFC 1918). O Azure atribui aos recursos conectados à VNet um endereço IP privado do espaço de endereço que você atribuiu.

-   Segmentar a VNet em uma ou mais sub-redes e alocar uma parte do espaço de endereço da VNet a cada sub-rede.

-   Usar a resolução de nomes fornecida pelo Azure ou especificar seu próprio servidor DNS que será usado pelos recursos conectados a uma VNet. Para saber mais sobre a resolução de nome em uma VNet, leia [Resolução de nomes para VMs e Serviços de Nuvem](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

**Conectividade com a Internet**

Por padrão, todas as instâncias de função de Serviços de Nuvem e [Máquinas Virtuais (VM) do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/) são conectadas a uma VNet que tem acesso à Internet. Você também pode habilitar o acesso de entrada para recursos específicos, conforme necessário. Máquinas virtuais (VMs) e instâncias de função de serviços de nuvem conectadas a uma rede virtual têm acesso à Internet, por padrão. Você também pode habilitar o acesso de entrada a recursos específicos, conforme o necessário.

Por padrão, todos os recursos conectados a uma VNet têm conectividade de saída para a Internet. O endereço IP privado do recurso passa por SNAT (conversão do endereço de rede de origem) para um endereço IP público pela infraestrutura do Azure. Altere a conectividade padrão implementando o roteamento e a filtragem de tráfego personalizado. Para saber mais sobre conectividade de saída na Internet, leia [Noções básicas das conexões de saída no Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections?toc=%2fazure%2fvirtual-network%2ftoc.json).

Para comunicar-se internamente com os recursos do Azure da Internet, ou para comunicar-se externamente com a Internet sem SNAT, é necessário atribuir um endereço IP público ao recurso. Para saber mais sobre endereços IP públicos, leia [Endereços IP públicos](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

**Conectividade de recursos do Azure**

[Recursos do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) como Serviços de Nuvem e VMs podem ser conectados à mesma VNet. Os recursos podem se conectar usando endereços IP privados, mesmo se estiverem em sub-redes diferentes. O Azure fornece roteamento padrão entre sub-redes, VNets e redes locais, portanto você não precisa configurar e gerenciar rotas.

Você pode conectar vários recursos do Azure a uma VNet, como VMs (máquinas virtuais), Serviços de Nuvem, Ambientes de Serviço de Aplicativo e Conjuntos de dimensionamento de máquinas virtuais. As VMs se conectam a uma sub-rede dentro de uma VNet por meio de uma NIC (interface de rede). Para saber mais sobre adaptadores de rede, leia [Interfaces de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface).

**Conectividade de VNet**

As [VNets](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) podem ser conectadas entre si, permitindo que os recursos conectados a qualquer VNet se comuniquem com recursos de outra VNet.

Você pode conectar as VNets entre si, permitindo que os recursos conectados a qualquer VNet se comuniquem entre si através das VNets. Use uma ou as duas opções a seguir para conectar VNets entre si:

- **Emparelhamento:** Permite que os recursos conectados a diferentes VNets do Azure na mesma localização do Azure se comuniquem entre si. A largura de banda e a latência entre as VNets são as mesmas se os recursos estivessem conectados à mesma VNet. Para saber mais sobre emparelhamento, leia o [Emparelhamento de redes virtuais](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

- **Conexão de VNet para VNet:** Permite recursos conectados a uma VNet do Azure diferente dentro dos mesmos locais ou em locais diferentes do Azure. Ao contrário do emparelhamento, a largura de banda é limitada entre as VNets, pois o tráfego deve fluir por um Gateway de VPN do Azure. Para saber mais sobre como conectar VNets com uma conexão VNet para VNet. Para saber mais, leia [Configurar uma conexão VNet para VNet](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Conectividade local**

As VNets podem ser conectadas a redes [locais](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) por meio de conexões de rede privada entre a sua rede e o Azure ou por uma conexão de VPN site a site pela Internet.

Você pode conectar sua rede local a uma VNet usando qualquer combinação das seguintes opções:

- **VPN (rede privada virtual) ponto a site:** Estabelecida entre um único PC conectado à sua rede e a VNet. Esse tipo de conexão é ótimo se você estiver começando a usar o Azure, ou para os desenvolvedores, pois exige pouca ou nenhuma alteração em sua rede existente. A conexão usa o protocolo SSTP para fornecer comunicação criptografada pela Internet entre o PC e a VNet. A latência de uma VPN ponto a site é imprevisível, pois o tráfego atravessa a Internet.

- **VPN site a site:** Estabelecida entre o dispositivo VPN e um Gateway de VPN do Azure. Esse tipo de conexão permite que qualquer recurso local autorizado acesse uma VNet. A conexão é uma VPN IPsec/IKE que fornece comunicação criptografada pela Internet entre o dispositivo local e o Gateway de VPN do Azure. A latência de uma conexão site a site é imprevisível, pois o tráfego atravessa a Internet.

- **Azure ExpressRoute:** estabelecido entre sua rede e o Azure, por meio de um parceiro de ExpressRoute. Essa conexão é privada. O tráfego não atravessa a Internet. A latência de uma conexão de ExpressRoute é previsível, pois o tráfego não atravessa a Internet. Para saber mais sobre todas as opções de conexão anteriores, leia [Diagramas de topologia de conexão](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Filtragem de tráfego**

O [tráfego de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) de instâncias de função de VMs e do Serviços de Nuvem pode ser filtrado na entrada e na saída pelo endereço IP e porta de origem, endereço IP e porta de destino e protocolo.

Filtre o tráfego de rede entre sub-redes usando uma ou as duas opções a seguir:

- **NSG (Grupos de Segurança de Rede):** Cada NSG pode conter várias regras de segurança de entrada e saída que permitem filtrar o tráfego por endereço IP de origem e de destino, porta e protocolo. Aplique um NSG a cada NIC em uma VM. Você também pode aplicar um NSG à sub-rede a qual uma NIC, ou outros recursos do Azure, está conectada. Leia [Grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) para saber mais sobre NSGs.

- **Dispositivos de Rede Virtual:** Um dispositivo de rede virtual é uma VM que executa um software responsável por uma função de rede, como um firewall. Exiba uma lista de NVAs disponíveis no Azure Marketplace. Também há NVAs que fornecem otimização de WAN e outras funções de tráfego de rede. As NVAs são usadas normalmente com rotas BGP ou definidas pelo usuário. Você também pode usar uma NVA para filtrar o tráfego entre VNets.

**Roteamento**

Opcionalmente, você pode substituir o roteamento padrão do Azure configurando suas próprias rotas ou usando rotas BGP por meio de um gateway de rede.

Por padrão, o Azure cria tabelas de rotas que permitem aos recursos conectados a qualquer sub-rede e em qualquer VNet a comunicação mútua. Você pode implementar uma ou as duas opções a seguir para substituir as rotas padrão criadas pelo Azure:

- **Rotas definidas pelo usuário**: é possível criar tabelas de rotas personalizadas com rotas que controlam para que local o tráfego será roteado para cada sub-rede. Para saber mais sobre as rotas definidas pelo usuário, leia [Rotas definidas pelo usuário](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

- **Rotas BGP:** se você conectar sua VNet à rede local usando um Gateway de VPN do Azure ou conexão de ExpressRoute, poderá propagar as rotas BGP às suas VNets.

### <a name="hybrid-internet-connectivity-connect-to-an-on-premises-network"></a>Conectividade com a Internet híbrida: Conectar-se a uma rede local
Você pode conectar sua rede local a uma VNet usando qualquer combinação das seguintes opções:

-   Conectividade com a Internet

-   VPN de ponto a site (VPN P2S)

-   VPN de site a site (VPN S2S)

-   ExpressRoute

#### <a name="internet-connectivity"></a>Conectividade com a Internet

Assim como o nome sugere, a conectividade com a Internet torna suas cargas de trabalho acessíveis pela Internet, fazendo com que você exponha diferentes pontos de extremidade públicos a cargas de trabalho que residem dentro da rede virtual. Essas cargas de trabalho podem ser expostas usando um [Load Balancer voltado para a Internet](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview) ou simplesmente atribuindo um endereço IP público à VM. Dessa forma, é possível que qualquer coisa na Internet possa alcançar essa máquina virtual, desde que um firewall de host, [NSGs (grupos de segurança de rede)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) e [rotas definidas pelo usuário](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) permitam que isso aconteça.

Nesse cenário, você pode expor um aplicativo que precisa ser público à Internet e ser capaz de conectar-se a ele de qualquer lugar ou então de localizações específicas, dependendo da configuração de suas cargas de trabalho.

#### <a name="point-to-site-vpn-or-site-to-site-vpn"></a>VPN de ponto a site ou VPN de site a site
Esses dois se encaixam na mesma categoria. Ambos precisam que sua VNet tenha um Gateway de VPN e você pode conectar-se a ele usando um cliente VPN para sua estação de trabalho como parte da [configuração de ponto a site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) ou então você pode configurar seu [dispositivo VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) local para poder encerrar uma VPN de site a site. Dessa forma, dispositivos locais podem se conectar a recursos na VNet.

Uma configuração Ponto a Site (P2S) permite que você crie uma conexão segura de um computador cliente individual com uma rede virtual. O P2S é uma conexão VPN sobre SSTP (Secure Socket Tunneling Protocol).

![VPN ponto a site](media/azure-network-security/azure-network-security-fig-5.png)

Conexões ponto a site são úteis quando você deseja se conectar à sua VNet de um local remoto, como de casa ou de um centro de conferências ou quando há apenas alguns clientes que precisam se conectar a uma rede virtual.

As conexões ponto a site não exigem um dispositivo VPN ou um endereço IP voltado para o público. Estabeleça a conexão VPN no computador cliente. Portanto, o P2S não é a maneira recomendável de se conectar ao Azure caso você precise de uma conexão persistente de vários computadores e dispositivos locais à sua rede do Azure.

![VPN de site a site](media/azure-network-security/azure-network-security-fig-6.png)

> [!Note]
> Para saber mais sobre conexões de ponto a site, consulte as [Perguntas frequentes sobre ponto a site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal).

Uma conexão de gateway de VPN Site a Site é usada para conectar a rede local a uma rede virtual do Azure por um túnel VPN IPsec/IKE (IKEv1 ou IKEv2).

Esse tipo de conexão exige um dispositivo VPN localizado no local que tenha um endereço IP público voltado para o exterior atribuído a ele. Essa conexão ocorre pela Internet e permite que você envie suas informações por um "túnel" dentro de um link criptografado entre sua rede e o Azure. A VPN site a site é uma tecnologia segura e madura implantada por empresas de todos os portes há décadas. A criptografia de túnel é realizada usando o [modo de túnel IPsec](https://technet.microsoft.com/library/cc786385.aspx).

Embora a VPN site a site seja uma tecnologia confiável e estabelecida, o tráfego no túnel não atravessa a Internet. Além disso, a largura de banda é relativamente restrita a um máximo de aproximadamente 200 Mbps.

Se você precisar de um nível excepcional de segurança ou de desempenho para as conexões entre locais, recomendamos que você use o Azure ExpressRoute para sua conectividade entre locais. O ExpressRoute é um link WAN dedicado entre seu local ou um provedor de hospedagem do Exchange. Como essa é uma conexão de telecomunicações, seus dados não viajam pela Internet e, portanto, não são expostos aos potenciais riscos de comunicações pela Internet.

> [!Note]
> Para obter mais informações sobre os gateways de VPN, veja [Sobre o gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

#### <a name="dedicated-wan-link"></a>Link WAN dedicado
O Microsoft Azure ExpressRoute permite que você estenda suas redes locais para o Azure por meio de uma conexão privada dedicada, facilitada por um provedor de conectividade.

As conexões do ExpressRoute não passam pela Internet pública. Isso permite que as conexões do ExpressRoute ofereçam mais confiabilidade, mais velocidade, latências menores e muito mais segurança do que as conexões típicas pela Internet.

![ Link WAN Dedicado](media/azure-network-security/azure-network-security-fig-7.png)

> [!Note]
> Para saber mais sobre como conectar sua rede à Microsoft usando ExpressRoute, veja [Modelos de conectividade do ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) e [Visão geral técnica do ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

Assim como com as opções de VPN de site a site, o ExpressRoute também permite que você se conecte a recursos que não estão necessariamente em apenas uma VNet. Na verdade, dependendo do SKU, você pode se conectar a 10 VNets. Se você tiver o [complemento Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs), conexões para até 100 VNets são possíveis, dependendo da largura de banda. Para saber mais sobre como é a aparência desses tipos de conexões, leia [Diagramas de topologia de conexão](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="security-controls"></a>Controles de segurança
Uma Rede Virtual do Azure fornece uma rede segura e lógica que é isolada de outras redes virtuais e dá suporte a vários controles de segurança que você pode usar em suas redes locais. Os clientes criam sua própria estrutura usando: sub-redes – eles usam o próprio intervalo de endereços IP privado, configuram tabelas de rotas, grupos de segurança de rede, ACLs (listas de controle de acesso), gateways e dispositivos virtuais para executar as cargas de trabalho deles na nuvem.

A seguir, os controles de segurança que você pode usar em suas Redes Virtuais do Azure:

-   Controles de Acesso à Rede

-   Rotas definidas pelo usuário

-   Solução de segurança de rede

-   Gateway de Aplicativo

-   Firewall do aplicativo Web do Azure

-   Controle de disponibilidade de rede

#### <a name="network-access-controls"></a>Controles de acesso à rede
Enquanto a VNet (rede virtual) do Azure é a base do modelo de rede do Azure e fornece isolamento e proteção, os [NSGs (grupos de segurança de rede)](https://blogs.msdn.microsoft.com/igorpag/2016/05/14/azure-network-security-groups-nsg-best-practices-and-lessons-learned/) são a principal ferramenta usada para impor e controlar as regras de tráfego de rede no nível da rede.

![ Controles de Acesso à Rede](media/azure-network-security/azure-network-security-fig-8.png)


Você pode controlar o acesso permitindo ou negando a comunicação entre as cargas de trabalho em uma rede virtual, de sistemas nas redes do cliente por meio de conectividade entre locais ou comunicação direta via Internet.

No diagrama, tanto VNets e NSGs residem em uma camada específica na pilha de segurança geral do Azure, em que NSGs, UDR e soluções de virtualização de rede podem ser usados para criar limites de segurança para proteger as implantações de aplicativo na rede protegida.

Os NSGs usam cinco tuplas para avaliar o tráfego (e que são usadas nas regras que você configura para o NSG):

-   [Endereço IP de origem e de destino](https://support.microsoft.com/help/969029/the-functionality-for-source-ip-address-selection-in-windows-server-2008-and-in-windows-vista-differs-from-the-corresponding-functionality-in-earlier-versions-of-windows)

-   [Porta de origem e de destino](https://technet.microsoft.com/library/dd197515)

-   Protocolo: [TCP (protocolo TCP)](https://technet.microsoft.com/library/cc940037.aspx) ou [UDP (protocolo UDP)](https://technet.microsoft.com/library/cc940034.aspx)

Isso significa que você pode controlar o acesso entre uma VM individual e um grupo de VMs ou entre uma VM individual e outra VM individual ou ainda entre sub-redes inteiras. Novamente, tenha em mente que isso é simples uma filtragem de pacote com estado simples e não uma inspeção de pacote completa. Não há nenhuma capacidade IPS ou IDS de nível de rede ou de validação de protocolo em um Grupo de Segurança de Rede.

Um NSG vem com algumas regras internas às quais você deve estar atento. Estes são:

-   **Permitir todo o tráfego em uma rede virtual específica:** Todas as VMs em uma mesma Rede Virtual do Azure podem se comunicar entre si.

-   **Permitir a entrada de balanceamento de carga do Azure:** essa regra permite tráfego de qualquer endereço de origem para qualquer endereço de destino para o Azure Load Balancer.

-   **Negar todas as entradas:** essa regra bloqueia todo o fornecimento de tráfego da Internet que você tenha permitido explicitamente.

-   **Permitir todo o tráfego de saída para a Internet:** Essa regra permite que VMs iniciem conexões com a Internet. Se você não quiser que essas conexões sejam iniciadas, você precisará criar uma regra para bloquear as conexões ou aplicar o túnel forçado.

#### <a name="system-routes-and-user-defined-routes"></a>Rotas de sistema e rotas definidas pelo usuário

Ao adicionar VMs (máquinas virtuais) a uma VNet (rede virtual) no Azure, você observará que as VMs podem se comunicar automaticamente com outras VMs na rede. Não é necessário especificar um gateway, mesmo que as VMs estejam em sub-redes diferentes.

O mesmo vale para a comunicação entre as VMs para a Internet pública e até mesmo em suas instalações de rede quando houver uma conexão híbrida do Azure para o seu próprio datacenter.

![Rotas do sistema](media/azure-network-security/azure-network-security-fig-9.png)

Esse fluxo de comunicação é possível porque o Azure usa uma série de rotas do sistema para definir como o tráfego IP flui. As rotas de sistema controlam o fluxo de comunicação nos seguintes cenários:

-   De dentro da mesma sub-rede.

-   De uma sub-rede para outra em uma VNet.

-   De VMs com a Internet.

-   De uma VNet para outra VNet por meio de um gateway VPN.

-   De uma VNet para outra VNet por meio de emparelhamento VNet ([encadeamento de serviços](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)).

-   De uma VNet à sua rede local por meio de um gateway de VPN.

Muitas empresas têm requisitos rigorosos de segurança de conformidade que exigem a inspeção local de todos os pacotes de rede, para impor políticas específicas. O Azure fornece um mecanismo chamado [túnel forçado](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) que direciona o tráfego de VMs para o local, por meio da criação de uma rota personalizada ou de anúncios [BGP (Border Gateway Protocol)](https://docs.microsoft.com/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) por meio de ExpressRoute ou VPN.

O túnel forçado no Azure é configurado por meio de UDR (rotas de definidas pelo usuário) de rede virtual. Redirecionar o tráfego para um site local é expressado como uma Rota Padrão para o gateway de VPN do Azure.

A seção a seguir lista a limitação atual da tabela de roteamento e as rotas para uma Rede Virtual do Azure:

- Cada sub-rede de rede virtual tem uma tabela de roteamento interna do sistema. A tabela de roteamento do sistema tem estes três grupos de rotas:

  -  **Rotas locais de Rede Virtual:** Diretamente para as VMs de destino na mesma rede virtual

  - **Rotas locais:** para um gateway de VPN do Azure

  -  **Rota padrão:** diretamente para a Internet. Os pacotes destinados para os endereços IP privados não cobertos pelas duas rotas anteriores serão removidos.

- Com a liberação de rotas definidas pelo usuário, você poderá criar uma tabela de roteamento para adicionar uma rota padrão e, em seguida, associar a tabela de roteamento às suas sub-redes de VNet para habilitar o túnel forçado nessas sub-redes.

- Você precisa definir um "site padrão" entre sites locais entre locais conectado à rede virtual.

- O túnel forçado deve ser associado a uma Rede Virtual que tem um gateway de VPN de roteamento dinâmico (e não um gateway estático).

- O túnel forçado do ExpressRoute não é configurado por meio deste mecanismo, mas é habilitado por meio do anúncio de uma rota padrão por meio de sessões de emparelhamento via protocolo BGP do ExpressRoute.

> [!Note]
> Para obter mais informações, veja a [Documentação do ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

#### <a name="network-security-appliances"></a>Soluções de segurança de rede
Embora os Grupos de Segurança de Rede e as Rotas Definidas pelo Usuário possam fornecer uma determinada medida de segurança de rede nas camadas de rede e de transporte do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), haverá situações em que você desejará ou precisará habilitar a segurança em níveis mais altos da pilha de rede. Em tais situações, é recomendável que você implante dispositivos de segurança de rede virtual fornecidos por parceiros do Azure.

![Soluções de segurança de rede](./media/azure-network-security/azure-network-security-fig-10.png)

Soluções de segurança de rede do Azure melhoram a segurança e as funções de rede da VNet e elas estão disponíveis por diversos fornecedores por meio do [Azure Marketplace](https://azuremarketplace.microsoft.com). Esses dispositivos de segurança virtual podem ser implantados para fornecer:

-   Firewalls altamente disponíveis

-   Prevenção de intrusões

-   Detecção de intrusões

-   WAFs (firewalls de aplicativo Web)

-   Otimização WAN

-   Roteamento

-   Balanceamento de carga

-   VPN

-   Gerenciamento de certificados

-   Active Directory

-   Autenticação multifator

#### <a name="application-gateway"></a>Gateway de Aplicativo

O [Gateway de Aplicativo do Microsoft Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) é uma solução de virtualização dedicada que fornece um ADC (controlador de entrega de aplicativos) como um serviço.

 ![Gateway de Aplicativo](./media/azure-network-security/azure-network-security-fig-11.png)

O Gateway de Aplicativo permite otimizar o desempenho e disponibilidade do Web farm descarregando a terminação SSL com uso intensivo de CPU para o Gateway de Aplicativo (descarregamento SSL). Ele também fornece outros recursos de roteamento de camada 7, incluindo:

-   Distribuição round robin do tráfego de entrada

-   Afinidade de sessão baseada em cookie

-   Visão geral do roteamento baseado em caminho de URL

-   Capacidade de hospedar vários sites subjacentes a um único Gateway de Aplicativo


Um [WAF (firewall do aplicativo Web)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) também é fornecido como parte do gateway de aplicativo. Isso oferece proteção para aplicativos Web contra explorações e vulnerabilidades comuns da Web. O Gateway de Aplicativo pode ser configurado como um gateway voltado para a Internet, um gateway apenas interno ou uma combinação de ambos.

O WAF do Gateway de Aplicativo pode ser executado no modo de detecção ou de prevenção. Um caso de uso comum é que administradores o executem no modo de detecção para observar o tráfego em busca de padrões mal-intencionados. Após a detecção de explorações potenciais, colocá-lo no modo de prevenção bloqueia o tráfego de entrada suspeito.

 ![Gateway de Aplicativo](./media/azure-network-security/azure-network-security-fig-12.png)

Além disso, o WAF de Gateway de Aplicativo ajuda a monitorar os aplicativos Web contra ataques usando um log WAF em tempo real que é integrado com o [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) e a [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/) para acompanhar alertas do WAF e monitorar as tendências com facilidade.

O log em formato JSON vai diretamente para a conta de armazenamento do cliente. Você tem controle total sobre esses logs e pode aplicar suas próprias políticas de retenção.

Você também pode ingerir esses logs em seu próprio sistema de análise usando a [Integração de Log do Azure](https://aka.ms/AzLog). Logs de WAF também são integrados [registra em log do Azure Monitor](../log-analytics/log-analytics-overview.md) para que você pode usar os logs do Azure Monitor para executar consultas refinadas sofisticadas.

#### <a name="azure-web-application-firewall-waf"></a>WAF (Firewall do aplicativo Web) do Azure

Aplicativos Web são cada vez mais alvo de ataques mal-intencionados, que exploram vulnerabilidades conhecidas comuns como injeção SQL, ataques de script entre sites e outros ataques que aparecem nos [OWASP top 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project). Impedir essas explorações no aplicativo requer manutenção rigorosa, aplicação de patches e monitoramento em várias camadas da topologia do aplicativo.

 ![WAF (Firewall do Aplicativo Web) do Azure](./media/azure-network-security/azure-network-security-fig-13.png)

Um [Firewall do aplicativo Web (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) centralizado pode proteger contra ataques da Web e simplifica o gerenciamento de segurança sem exigir nenhuma alteração de aplicativo.

Uma solução WAF também pode reagir a uma ameaça de segurança mais rapidamente ao aplicar um patch contra uma vulnerabilidade conhecida em um local central do que a proteção de cada um dos aplicativos Web individuais. Os gateways de aplicativos existentes podem ser facilmente convertidos em um gateway de aplicativo com firewall de aplicativo Web.

#### <a name="network-availability-controls"></a>Controles de disponibilidade de rede

Há diferentes opções para distribuir o tráfego de rede usando o Microsoft Azure. Essas opções funcionam de forma diferente uma da outra, com conjuntos diferentes de recursos e permitindo cenários diferentes. Cada uma pode ser usada isoladamente ou em combinação.

A seguir estão os controles de disponibilidade de rede:

-   Azure Load Balancer

-   Gateway de Aplicativo

-   Gerenciador de Tráfego

**Azure Load Balancer**

Entrega alta disponibilidade e desempenho de rede a seus aplicativos. É um balanceador de carga do tipo Camada 4 (TCP, UDP) que distribui o tráfego de entrada entre as instâncias de serviço íntegras definidas em um conjunto de balanceadores de carga.

 ![Azure Load Balancer](media/azure-network-security/azure-network-security-fig-14.png)


O Azure Load Balancer pode ser configurado para:

-   Balancear carga de tráfego de entrada na Internet para máquinas virtuais. Essa configuração é conhecida como [balanceamento de carga voltada para a Internet](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Balanceie o tráfego de carga entre as máquinas virtuais em uma rede virtual, entre as máquinas virtuais nos serviços de nuvem ou entre os computadores locais e as máquinas virtuais em uma rede virtual entre as instalações. Essa configuração é conhecida como [balanceamento de carga interno](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview).

-   Encaminhe o tráfego externo para uma máquina virtual específica.

Todos os recursos na nuvem precisam de um endereço IP público para serem acessíveis pela Internet. A infraestrutura de nuvem no Azure usa endereços IP não roteáveis para seus recursos. O Azure usa a conversão de endereços de rede (NAT) com os endereços IP públicos para se comunicar com a Internet.

 **Gateway de Aplicativo**

 O Gateway de Aplicativo funciona na camada de aplicativo (Camada 7 na pilha de referência de rede OSI). Ele atua como um serviço de proxy inverso, encerrando a conexão do cliente e encaminhando solicitações aos pontos de extremidade de back-end.

 **Gerenciador de tráfego**

O Gerenciador de Tráfego do Microsoft Azure permite controlar a distribuição do tráfego do usuário para pontos de extremidade do serviço em diferentes datacenters. Os pontos de extremidade de serviço com suporte no Gerenciador de Tráfego incluem VMs do Azure, Aplicativos Web e serviços de nuvem. Você também pode usar o Gerenciador de Tráfego com pontos de extremidade externos e não do Azure.

O Gerenciador de Tráfego usa o DNS (Sistema de Nome de Domínio) para direcionar solicitações de cliente para o ponto de extremidade mais apropriado com base em um [método de roteamento de tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) e a integridade dos pontos de extremidade. O Gerenciador de Tráfego oferece uma variedade de métodos de roteamento de tráfego para atender às necessidades de diferentes aplicativo, [monitoramento](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) de integridade do ponto de extremidade e failover automático. O Gerenciador de Tráfego é resistente a falhas, incluindo a falha de toda a região do Azure.

O Gerenciador de Tráfego do Azure permite que você controle a distribuição do tráfego entre os pontos de extremidade do aplicativo. Um ponto de extremidade é qualquer serviço para a Internet hospedado dentro ou fora do Azure.

O Gerenciador de Tráfego oferece dois benefícios principais:

-   Distribuição do tráfego de acordo com um dos vários [métodos de roteamento de tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods).

-   [Monitoramento contínuo de integridade do ponto de extremidade](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) e failover automático quando os pontos de extremidade falharem.

Quando um cliente tenta se conectar a um serviço, ele primeiro deve resolver o nome DNS do serviço para um endereço IP. O cliente se conecta a esse endereço IP para acessar o serviço. O Gerenciador de Tráfego usa o DNS para direcionar clientes para pontos de extremidade de serviço específicos com base nas regras do método de roteamento de tráfego. Os clientes se conectam diretamente ao ponto de extremidade selecionado. O Gerenciador de Tráfego não é um proxy nem um gateway. O Gerenciador de Tráfego não vê o tráfego que passa entre o cliente e o serviço.

### <a name="azure-network-validation"></a>Validação de rede do Azure

A validação de rede do Azure é para assegurar que a rede do Azure esteja funcionando conforme ela é configurada; a validação pode ser feita usando os serviços e recursos disponíveis para monitorar a rede. Com o Observador de Rede do Azure, você pode acessar uma grande quantidade de recursos de registro em log e de diagnóstico que capacitam você com insights para entender a integridade e o desempenho da rede. Esses recursos são acessíveis por meio do Portal, do Power Shell, da CLI, da API Rest e do SDK.

A Segurança Operacional do Azure refere-se a serviços, controles e recursos disponíveis aos usuários para proteger seus dados, aplicativos e outros recursos no Microsoft Azure. A Segurança Operacional do Azure baseia-se em uma estrutura que incorpora o conhecimento adquirido por meio de várias funcionalidades que são exclusivas à Microsoft, incluindo o Microsoft Security Development Lifecycle (SDL), o programa Microsoft Security Response Center e uma conscientização profunda do cenário de ameaças à segurança cibernética.

-   [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)

-   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

-   [Observador de Rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

-   [Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

-   Azure Resource Manager

#### <a name="azure-resource-manager"></a>Azure Resource Manager

As pessoas e processos que operam o Microsoft Azure são talvez o recurso de segurança mais importante da plataforma. Esta seção descreve recursos que ajudam a melhorar e manter a segurança, continuidade e privacidade da infraestrutura de datacenter global da Microsoft.

A infraestrutura do seu aplicativo geralmente é composta de vários componentes; talvez uma máquina virtual, uma conta de armazenamento e uma rede virtual ou aplicativo Web, banco de dados, servidor de banco de dados e serviços de terceiros. Tais componentes não são vistos como entidades separadas, em vez disso, eles são mostrados como partes relacionadas e interdependentes de uma única entidade. Você deseja implantar, gerenciar e monitorá-los como um grupo. O Azure Resource Manager permite trabalhar com os recursos da sua solução como um grupo.

Você pode implantar, atualizar ou excluir todos os recursos da sua solução em uma única operação coordenada. Usar um modelo para a implantação e esse modelo pode ser útil para ambientes diferentes, como teste, preparação e produção. O Gerenciador de Recursos fornece recursos de segurança, auditoria e marcação para ajudá-lo a gerenciar seus recursos após a implantação.

**Os benefícios de usar o Resource Manager**

O Gerenciador de Recursos fornece vários benefícios:

-   Você pode implantar, gerenciar e monitorar todos os recursos da sua solução como um grupo em vez de tratá-los individualmente.

-   Você pode implantar a solução repetidamente em todo seu ciclo de vida de desenvolvimento e com a confiança de que seus recursos serão implantados em um estado consistente.

-   Você pode gerenciar sua infraestrutura por meio de modelos declarativos em vez de scripts.

-   Você pode definir as dependências entre os recursos para que eles sejam implantados na ordem correta.

-   Você pode aplicar o controle de acesso a todos os serviços no grupo de recursos, pois o RBAC (Controle de Acesso Baseado em Função) é integrado nativamente à plataforma de gerenciamento.

-   Você pode aplicar marcas aos recursos para organizar de modo lógico todos os recursos em sua assinatura.

-   Você pode esclarecer a cobrança da sua organização exibindo os custos para um grupo de recursos que compartilham a mesma marcação.

> [!Note]
> O Gerenciador de Recursos fornece uma nova maneira de implantar e gerenciar suas soluções. Se você usou o modelo de implantação anterior e quiser saber mais sobre as alterações, confira [Noções básicas sobre a implantação do Gerenciador de Recursos e a implantação clássica](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="azure-network-logging-and-monitoring"></a>Registro em log e monitoramento de rede do Azure

O Azure oferece várias ferramentas para monitorar, prevenir, detectar e responder a eventos de segurança de rede. Algumas das ferramentas mais avançadas disponíveis nessa área incluem:

-   Observador de Rede

-   Monitoramento no nível do recurso de rede

-   Logs do Azure Monitor

### <a name="network-watcher"></a>Observador de Rede

[Observador de Rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) – o monitoramento baseado em cenários é fornecido com os recursos do Observador de Rede. Esse serviço inclui a captura de pacotes, próximo salto, verificação do fluxo de IP, exibição do grupo de segurança e logs de fluxo de NSG. O monitoramento no nível do cenário fornece uma exibição completa dos recursos de rede em contraste com o monitoramento de recursos de rede individual.

 ![Observador de Rede](./media/azure-network-security/azure-network-security-fig-15.png)

O Observador de Rede é um serviço regional que permite monitorar e diagnosticar as condições em um nível do cenário da rede em, para e a partir do Azure. As ferramentas de diagnóstico e visualização da rede disponíveis com o Observador de Rede ajudam a entender, diagnosticar e ter informações para sua rede no Azure.

O Observador de Rede tem atualmente os seguintes recursos:

#### <a name="topology"></a>Topologia

A [topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) retorna um grafo de recursos de rede em uma rede virtual. O grafo mostra a interconexão entre os recursos para representar a conectividade de rede de ponta a ponta. No portal, a topologia retorna os objetos de recursos de acordo com a rede virtual. As relações são representadas por linhas entre os recursos fora da região do Observador de Rede, mesmo se o grupo de recursos não for exibido. Os recursos retornados na exibição do portal são um subconjunto dos componentes de rede mostrados em grafos. Para ver a lista completa de recursos de rede, você pode usar o [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-powershell) ou o [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-rest).

Conforme os recursos retornam, a conexão entre eles é modelada segundo duas relações.

- **Confinamento** – Exemplo: uma rede virtual contém uma sub-rede que contém um adaptador de rede.

- **Associados** – um adaptador de rede está associado a uma VM.

#### <a name="variable-packet-capture"></a>Captura de pacote variável

A [captura de pacote variável](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) do Observador de Rede permite que você crie sessões de captura de pacote para controlar o tráfego em uma máquina virtual. A captura de pacote ajuda a diagnosticar anomalias de rede reativas e proativas. Outros usos incluem a coleta de estatísticas de rede, obter informações sobre as invasões de rede, para depurar comunicações cliente-servidor e muito mais.

A captura de pacote é uma extensão de máquina virtual iniciada remotamente por meio do Observador de Rede. Esse recurso alivia o transtorno que é executar manualmente uma captura de pacote na máquina virtual desejada, o que economiza um tempo precioso. A captura de pacote pode ser disparada por meio do portal, do PowerShell, da CLI ou da API REST. Os alertas de Máquina Virtual são um exemplo de como a captura de pacote pode ser disparada.

#### <a name="ip-flow-verify"></a>Verificação de fluxo de IP

A [verificação de fluxo de IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) confere se um pacote é permitido ou negado para ou de uma máquina virtual com base nas informações de cinco tuplas. Essas informações consistem em direção, protocolo, IP local, IP remoto, porta local e porta remota. Se o pacote for negado por um grupo de segurança, o nome da regra que negou o pacote será retornado. Embora qualquer IP de origem ou destino possa ser escolhido, esse recurso ajuda os administradores a diagnosticarem rapidamente os problemas de conectividade a partir de ou com a Internet, e a partir de ou com o ambiente local.

A verificação de fluxo de IP é direcionada a um adaptador de rede de uma máquina virtual. O fluxo do tráfego é verificado com base nas configurações feitas para ou a partir dessa interface de rede. Esse recurso é útil ao confirmar se uma regra em um Grupo de Segurança da Rede está bloqueando o tráfego de entrada ou saída para ou a partir de uma máquina virtual.

#### <a name="next-hop"></a>Próximo salto

Determina o [próximo salto](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) para os pacotes serem roteados na Malha de Rede do Azure, permitindo diagnosticar quaisquer rotas definidas pelo usuário mal configuradas. O tráfego de uma VM é enviado para um destino com base nas rotas efetivas associadas a uma NIC. O próximo salto obtém o tipo do próximo salto e o endereço IP de um pacote em uma máquina virtual específica e na NIC. Isso ajuda a determinar se o pacote está sendo direcionado para o destino ou se o tráfego está tornando-se um buraco negro.

O próximo salto também retorna a tabela de rotas associada ao próximo salto. Ao consultar um próximo salto, se a rota for especificada como sendo definida pelo usuário, ela será retornada. Caso contrário, o Próximo salto retornará a "Rota do Sistema".

#### <a name="security-group-view"></a>Exibição de grupo de segurança

Obtém as regras de segurança efetivas e aplicadas que são usadas em uma VM. Os grupos Segurança da Rede podem ser associados no nível da sub-rede ou no nível da NIC. Quando associado no um nível da sub-rede, ele se aplica a todas as instâncias de VM na sub-rede. A [exibição do Grupo de Segurança](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) de Rede retorna todos os NSGs configurados e regras associadas no nível do adaptador de rede e da sub-rede para uma máquina virtual que fornece insights sobre a configuração. Além disso, as regras de segurança efetivas são retornadas para cada NIC em uma VM. Usando a exibição Grupo de Segurança da Rede, você pode avaliar uma VM quanto às vulnerabilidades da rede, como as portas abertas. Também é possível validar se o Grupo de Segurança da Rede está funcionando como o esperado com base em uma [comparação entre as regras de segurança configuradas e as efetivas](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-auditing-powershell).

#### <a name="nsg-flow-logging"></a>Registro em log do fluxo NSG

 Os logs de fluxo para os Grupos de Segurança da Rede permitem capturar os logs relacionados ao tráfego que são permitidos ou negados pelas regras de segurança no grupo. O fluxo é definido por informações com cinco tuplas – IP de Origem, IP de Destino, Porta de Origem, Porta de Destino e Protocolo.

Os [logs de fluxo do Grupo de Segurança de Rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) são um recurso do Observador de Rede que permite exibir informações sobre o tráfego IP de entrada e saída por meio de um Grupo de Segurança de Rede.

#### <a name="virtual-network-gateway-and-connection-troubleshooting"></a>Solução de problemas de conexão e do gateway de rede virtual

O observador de rede oferece muitos recursos que dizem respeito às noções básicas sobre os recursos de rede no Azure. Um desses recursos é a solução de problemas de recursos. A [solução de problemas de recursos](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) pode ser chamada pelo PowerShell, pela CLI ou pela API REST. Quando chamado, o observador de rede inspeciona a integridade de uma conexão ou um gateway de rede virtual e faz um relatório sobre suas descobertas.

Nesta seção, você conhecerá as diferentes tarefas de gerenciamento que estão disponíveis para a solução de problemas de recursos.

-   [Solução de problemas de um gateway de rede virtual](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

-   [Como solucionar problemas de uma conexão](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

#### <a name="network-subscription-limits"></a>Limites de assinatura da rede

Os [limites de assinatura da rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) fornecem a você os detalhes do uso de cada recurso de rede em uma assinatura em uma região em relação ao número máximo de recursos disponíveis.

#### <a name="configuring-diagnostics-log"></a>Configurar o log de diagnóstico

O Observador de Rede fornece uma exibição dos [logs de diagnóstico](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). Essa exibição contém todos os recursos de rede que oferecem suporte ao log de diagnóstico. Nessa exibição, você pode habilitar e desabilitar os recursos de rede de modo rápido e prático.

### <a name="network-resource-level-monitoring"></a>Monitoramento no nível do recurso da rede

Os seguintes recursos estão disponíveis para o monitoramento no nível do recurso:

#### <a name="audit-log"></a>Log de auditoria

As operações executadas como parte da configuração das redes são registradas. Esses logs de auditoria são essenciais para estabelecer diversas conformidades. Esses logs podem ser exibidos no portal do Azure ou recuperados usando as ferramentas da Microsoft, como o Power BI ou ferramentas de terceiros. Os logs de auditoria estão disponíveis por meio do portal, PowerShell, CLI e API Rest.

> [!Note]
> Para obter mais informações sobre os Logs de auditoria, consulte [Operações de auditoria com o Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
Os logs de auditoria estão disponíveis para as operações realizadas em todos os recursos da rede.


#### <a name="metrics"></a>Métricas

As métricas são medidas de desempenho e contadores coletados em um período de tempo. As métricas estão disponíveis atualmente para o Gateway de Aplicativo. As métricas podem ser usadas para disparar alertas com base no limite. Por padrão, o Gateway de Aplicativo do Azure monitora a integridade de todos os recursos em seu pool de back-end e remove automaticamente qualquer recurso do pool que não for considerado íntegro. O Gateway de Aplicativo continua monitorando as instâncias não íntegras e as adiciona de volta ao pool de back-end íntegro depois que elas se tornarem disponíveis e responderem a investigações de integridade. O Gateway de Aplicativo envia as investigações de integridade na mesma porta que é definida nas configurações de HTTP do back-end. A configuração assegura que a investigação teste a mesma porta que os clientes usariam para se conectar ao back-end.

> [!Note]
> Consulte [Diagnóstico do Gateway de Aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview) para ver como as métricas podem ser usadas para criar alertas.

#### <a name="diagnostic-logs"></a>Logs de diagnóstico

Eventos periódicos e espontâneas são criados pelos recursos da rede e registrados nas contas de armazenamento, enviadas para um Hub de eventos ou logs do Azure Monitor. Esses logs fornecem informações sobre a integridade de um recurso. Esses logs podem ser visualizados em ferramentas como logs do Power BI e o Azure Monitor. Para saber como exibir os logs de diagnóstico, visite [registra em log do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

Os logs de diagnóstico estão disponíveis para o [Balanceador de Carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [Grupos de Segurança da Rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), Rotas e [Gateway de Aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

O Observador de Rede fornece uma exibição dos logs de diagnóstico. Essa exibição contém todos os recursos de rede que oferecem suporte ao log de diagnóstico. Nessa exibição, você pode habilitar e desabilitar os recursos de rede de modo rápido e prático.

### <a name="azure-monitor-logs"></a>Logs do Azure Monitor

[Os logs do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) é um serviço no Azure que monitora a sua nuvem e ambientes para manter a disponibilidade e desempenho no local. Ele coleta dados gerados pelos recursos em seus ambientes de nuvem e locais e de outras ferramentas de monitoramento para fornecer análise de várias fontes.

Os logs do Azure Monitor oferece as seguintes soluções para monitorar suas redes:

-   Monitor de Desempenho de Rede (NPM)

-   Análise do Gateway de Aplicativo do Azure

-   Análise do Grupo de Segurança de Rede do Azure

#### <a name="network-performance-monitor-npm"></a>NPM (Monitor de Desempenho de Rede)
A solução de gerenciamento do [Monitor de Desempenho de Rede](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor) é uma solução de monitoramento de redes, que monitora a integridade, a disponibilidade e a acessibilidade das redes.

Ela é usada para monitorar a conectividade entre:

-   nuvem pública e local

-   data centers e locais de usuário (filiais)

-   sub-redes hospeda várias camadas de um aplicativo de várias camadas.


#### <a name="azure-application-gateway-analytics-in-azure-monitor-logs"></a>Análise de gateway de aplicativo do Azure nos logs do Azure Monitor

Nos Gateways de Aplicativo, há suporte para os seguintes logs:

-   ApplicationGatewayAccessLog

-   ApplicationGatewayPerformanceLog

-   ApplicationGatewayFirewallLog

Há suporte para as seguintes métricas nos Gateways de Aplicativo:

-   Taxa de transferência de 5 minutos

#### <a name="azure-network-security-group-analytics-in-azure-monitor-logs"></a>Análise de grupo de segurança de rede do Azure nos logs do Azure Monitor

Nos [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), há suporte para os seguintes logs:

- **NetworkSecurityGroupEvent:** Contém entradas para as regras NSG que são aplicadas às VMs e funções de instância com base no endereço MAC. O status para essas regras é coletado a cada 60 segundos.

- **NetworkSecurityGroupRuleCounter:** Contém entradas de quantas vezes cada regra de NSG é aplicada para negar ou permitir tráfego.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a segurança lendo alguns dos tópicos detalhados sobre segurança:

-   [Logs de Monitor do Azure para grupos de segurança de rede (NSGs)](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)

-   [As inovações de rede que causam a interrupção da nuvem](https://azure.microsoft.com/blog/networking-innovations-that-drive-the-cloud-disruption/)

-   [SONiC: O software de comutador de rede que fortalece a nuvem global da Microsoft](https://azure.microsoft.com/blog/sonic-the-networking-switch-software-that-powers-the-microsoft-global-cloud/)

-   [Como a Microsoft constrói a sua rede global rápida e confiável](https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/)

-   [Esclarecendo a inovação de rede](https://azure.microsoft.com/blog/lighting-up-network-innovation/)
