---
title: Rede do Azure | Microsoft Docs
description: Saiba sobre os recursos e serviços de rede do Azure.
services: networking
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: jdial
ms.openlocfilehash: 02db9f2b8cb2ec71d23ad077b90eeacb905d2a16
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565751"
---
# <a name="azure-networking"></a>Rede do Azure

O Azure fornece uma variedade de recursos de rede que podem ser usados juntos ou separadamente. Clique em qualquer um dos seguintes recursos principais para saber mais sobre eles:
- [Conectividade entre recursos do Azure](#connectivity): conecte recursos do Azure juntos em uma rede virtual segura e privada na nuvem.
- [Conectividade com a Internet](#internet-connectivity): comunique-se com recursos do Azure pela Internet.
- [Conectividade local](#on-premises-connectivity): conecte uma rede local a recursos do Azure por meio de uma VPN (rede virtual privada) pela Internet ou por meio de uma conexão dedicada com o Azure.
- [Direção do tráfego e balanceamento de carga](#load-balancing): faça o balanceamento de carga do tráfego para servidores no mesmo local e direcione o tráfego para servidores em locais diferentes.
- [Segurança](#security): filtre o tráfego de rede entre sub-redes ou VMs (máquinas virtuais) da rede.
- [Roteamento](#routing): use o roteamento padrão ou controle totalmente o roteamento entre seus recursos do Azure e recursos locais.
- [Capacidade de gerenciamento](#manageability): monitore e gerencie os recursos de rede do Azure.
- [Ferramentas de implantação e configuração](#tools): use um portal baseado na Web ou ferramentas de linha de comando de plataforma cruzada para implantar e configurar recursos de rede.

## <a name="connectivity"></a>Conectividade entre recursos do Azure

Os recursos do Azure, as Máquinas Virtuais, os Serviços de Nuvem, os Conjuntos de Dimensionamento de Máquinas Virtuais e os Ambientes do Serviço de Aplicativo do Azure, podem se comunicar entre si de forma privada por meio de uma VNet (rede virtual) do Azure. Uma VNet é um isolamento lógico da nuvem do Azure dedicada à sua [assinatura](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fnetworking%2ftoc.json). É possível implementar várias VNets dentro de cada assinatura e [região](https://azure.microsoft.com/regions) do Azure. Cada VNet é isolada de outras VNets. Para cada VNet, você pode:

- Especificar um espaço de endereço IP privado personalizado usando endereços públicos e privados (RFC 1918). O Azure atribui aos recursos conectados à VNet um endereço IP privado do espaço de endereço que você atribuiu.
- Segmentar a VNet em uma ou mais sub-redes e alocar uma parte do espaço de endereço da VNet a cada sub-rede.
- Usar a resolução de nomes fornecida pelo Azure ou especificar seu próprio servidor DNS que será usado pelos recursos conectados a uma VNet.

Para saber mais sobre o serviço de Rede Virtual do Azure, consulte o artigo [Visão geral da rede virtual](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json). Você pode conectar as VNets entre si, permitindo que os recursos conectados a qualquer VNet se comuniquem entre si através das VNets. Use uma ou as duas opções a seguir para conectar VNets entre si:

- **Emparelhamento:** permite que recursos conectados a diferentes VNets do Azure na mesma região do Azure se comuniquem entre si. A largura de banda e a latência entre as VNets são as mesmas se os recursos estivessem conectados à mesma VNet. Para saber mais sobre o emparelhamento, leia o artigo [Visão geral do emparelhamento de rede virtual](../virtual-network/virtual-network-peering-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Gateway de VPN:** permite que recursos conectados a diferentes VNets do Azure em diferentes regiões do Azure se comuniquem entre si. O tráfego entre as VNets flui por meio de um Gateway de VPN do Azure. A largura de banda entre as VNets é limitada à largura de banda do gateway. Para saber mais sobre como conectar VNets a um Gateway de VPN, leia o artigo [Configurar uma conexão de VNet a VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="internet-connectivity"></a>Conectividade com a Internet

Por padrão, todos os recursos do Azure conectados a uma VNet têm conectividade de saída com a Internet. O endereço IP privado do recurso passa por SNAT (conversão do endereço de rede de origem) para um endereço IP público pela infraestrutura do Azure. Para saber mais sobre conectividade de saída na Internet, leia o artigo [Noções básicas das conexões de saída no Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fnetworking%2ftoc.json).

Para comunicar-se internamente com os recursos do Azure da Internet, ou para comunicar-se externamente com a Internet sem SNAT, é necessário atribuir um endereço IP público ao recurso. Para saber mais sobre endereços IP públicos, leia o artigo [Endereços IP públicos](../virtual-network/virtual-network-public-ip-address.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="on-premises-connectivity"></a>Conectividade local

Você pode acessar os recursos de sua VNet com segurança por meio de uma conexão VPN ou de uma conexão privada direta. Para enviar o tráfego de rede entre sua rede virtual do Azure e sua rede local, você deve criar um gateway de rede virtual. Defina as configurações do gateway para criar o tipo de conexão que você deseja, seja ela por VPN ou ExpressRoute.

Você pode conectar sua rede local a uma VNet usando qualquer combinação das seguintes opções:

**Ponto a site (VPN sobre SSTP)**

A imagem a seguir mostra diferentes conexões de ponto a site entre vários computadores e uma VNet:

![Point-to-site](./media/networking-overview/point-to-site.png)

Essa conexão é estabelecida entre um único computador e uma VNet. Esse tipo de conexão é ótimo se você estiver começando a usar o Azure, ou para os desenvolvedores, pois exige pouca ou nenhuma alteração em sua rede existente. Ele também é conveniente quando você está se conectando de um local remoto, como uma conferência ou sua casa. Conexões de ponto a site frequentemente são combinadas a uma conexão site a site por meio do mesmo gateway de rede virtual. A conexão usa o protocolo SSTP para fornecer comunicação criptografada pela Internet entre o computador e a VNet. A latência de uma VPN ponto a site é imprevisível, pois o tráfego atravessa a Internet.

**Site a site (túnel VPN IPsec/IKE)**

![Site a site](./media/networking-overview/site-to-site.png)

Essa conexão é estabelecida entre o dispositivo VPN local e um Gateway de VPN do Azure. Esse tipo de conexão permite que qualquer recurso local autorizado acesse a VNet. A conexão é uma VPN IPSec/IKE que fornece comunicação criptografada pela Internet entre o dispositivo local e o Gateway de VPN do Azure. É possível conectar vários sites locais ao mesmo gateway de VPN. O dispositivo VPN local de cada site deve ter um endereço IP público voltado para o exterior que não esteja atrás de um NAT. A latência de uma conexão site a site é imprevisível, pois o tráfego atravessa a Internet.

**ExpressRoute (conexão privada dedicada)**

![ExpressRoute](./media/networking-overview/expressroute.png)

Esse tipo de conexão é estabelecida entre sua rede e o Azure, por meio de um parceiro do ExpressRoute. Essa conexão é privada. O tráfego não atravessa a Internet. A latência de uma conexão de ExpressRoute é previsível, pois o tráfego não atravessa a Internet. O ExpressRoute pode ser combinado a uma conexão site a site.

Para saber mais sobre todas as opções de conexão anteriores, leia o artigo [Diagramas de topologia de conexão](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="load-balancing"></a>Direção do tráfego e balanceamento de carga

O Microsoft Azure fornece vários serviços para gerenciar a forma como o tráfego de rede é distribuído e como sua carga é balanceada. É possível usar qualquer um dos recursos a seguir separadamente ou em conjunto:

**Balanceamento de carga DNS**

O serviço do Gerenciador de Tráfego do Azure fornece balanceamento de carga DNS global. O Gerenciador de Tráfego responde aos clientes com o endereço IP de um ponto de extremidade íntegro, com base em um dos seguintes métodos de roteamento:
- **Geográfico:** os clientes são direcionados para pontos de extremidade específicos (Azure, externo ou aninhado) com base no local geográfico em que as respectivas consultas DNS são originadas. Esse método habilita cenários em que conhecer a região geográfica do cliente e roteá-lo com base nisso, é importante. Os exemplos incluem conformidade com normas de soberania de dados, localização de conteúdo e experiência do usuário, bem como medição do tráfego de diferentes regiões.
- **Desempenho:** o endereço IP retornado ao cliente é o "mais próximo" ao cliente. O ponto de extremidade “mais próximo” não é necessariamente o mais próximo em termos de distância geográfica. Em vez disso, esse método determina o ponto de extremidade mais próximo medindo a latência de rede. O Gerenciador de Tráfego mantém uma tabela de latência da Internet para monitorar o tempo da viagem de ida e volta entre intervalos de endereços IP e em cada data center do Azure.
- **Prioridade:** o tráfego é direcionado para o ponto de extremidade primário (com a prioridade mais alta). Se o ponto de extremidade primário não estiver disponível, o Gerenciador de Tráfego encaminhará o tráfego para o segundo ponto de extremidade. Se os pontos de extremidade primário e secundário não estiverem disponíveis, o tráfego passará para o terceiro e assim por diante. A disponibilidade do ponto de extremidade é baseada no status configurado (habilitado ou desabilitado) e no monitoramento contínuo do ponto de extremidade.
- **Round robin ponderado:** para cada solicitação, o Gerenciador de Tráfego escolhe aleatoriamente um ponto de extremidade disponível. A probabilidade de escolher um ponto de extremidade baseia-se nos pesos atribuídos a todos os pontos de extremidade disponíveis. Usar o mesmo peso em todos os pontos de extremidade resulta em uma distribuição uniforme do tráfego. Usar pesos maiores ou menores em pontos de extremidade específicos faz com que esses pontos de extremidade sejam retornados com maior ou menor frequência nas respostas DNS.

A figura a seguir mostra uma solicitação para um aplicativo Web direcionada para um ponto de extremidade do aplicativo Web. Os pontos de extremidade também podem ser outros serviços do Azure, como VMs e serviços de nuvem.

![Gerenciador de Tráfego](./media/networking-overview/traffic-manager.png)

O cliente se conecta diretamente ao ponto de extremidade. O Gerenciador de Tráfego do Azure detecta quando um ponto de extremidade não está íntegro e redireciona os clientes para outro ponto de extremidade íntegro. Para saber mais sobre o Gerenciador de Tráfego, leia o artigo [Visão Geral do Gerenciador de Tráfego do Azure](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

**Balanceamento de carga do aplicativo**

O serviço de Gateway de Aplicativo do Azure fornece o ADC (controlador de entrega de aplicativo) como um serviço. O Gateway de Aplicativo oferece vários recursos de balanceamento de carga de Camada 7 (HTTP/HTTPS) para seus aplicativos, incluindo um firewall de aplicativo Web para proteger seus aplicativos Web contra vulnerabilidades e explorações. O Gateway de Aplicativo também permite otimizar a produtividade do Web farm descarregando a terminação SSL com uso intensivo de CPU para o Gateway de Aplicativo. 

Outros recursos de roteamento de camada 7 incluem distribuição round robin do tráfego de entrada, afinidade de sessão, roteamento com base no caminho de URL e capacidade de hospedar vários sites com um único Gateway de Aplicativo baseado em cookie. O Gateway de Aplicativo pode ser configurado como um gateway voltado para a Internet, um gateway apenas interno ou uma combinação de ambos. O Gateway de Aplicativo é totalmente gerenciado pelo Azure, escalonável e altamente disponível. Ele fornece um conjunto avançado de recursos de log e diagnósticos para melhor capacidade de gerenciamento. Para saber mais sobre o Gateway de Aplicativo, leia o artigo [Visão geral do Gateway de Aplicativo](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json).

A figura abaixo mostra o roteamento baseado em caminho do URL com o Gateway de Aplicativo:

![Gateway de Aplicativo](./media/networking-overview/application-gateway.png)

**Balanceamento de carga de rede**

O Azure Load Balancer fornece balanceamento de carga de Camada 4 de baixa latência e alto desempenho para todos os protocolos TCP e UDP. Ele gerencia conexões de entrada e saída. Você pode configurar pontos de extremidade com balanceamento de carga públicos e internos. Você também pode definir regras para mapear conexões de entrada para destinos de pool de back-end usando opções de investigação de integridade TCP e HTTP para gerenciar a disponibilidade do serviço. Para saber mais sobre o balanceador de carga, leia o artigo [Visão geral sobre o balanceador de carga](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

A imagem a seguir mostra um aplicativo de várias camadas voltado para a Internet que usa balanceadores de carga internos e externos:

![Balanceador de carga](./media/networking-overview/load-balancer.png)

## <a name="security"></a>Segurança

É possível filtrar o tráfego de e para recursos do Azure usando as seguintes opções:

- **Rede:** é possível implementar NSGs (Grupos de Segurança de Rede) do Azure para filtrar o tráfego de entrada e de saída para recursos do Azure. Cada NSG contém uma ou mais regras de entrada e saída. Cada regra especifica os endereços IP de origem, os endereços IP de destino, a porta e o protocolo com que o tráfego é filtrado. Os NSGs podem ser aplicados a sub-redes e a VMs individuais. Para saber mais sobre os NSGs, leia o artigo [Visão geral dos grupos de segurança de rede](../virtual-network/security-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Aplicativo:** usando um Gateway de Aplicativo com o firewall do aplicativo Web, você pode proteger seus aplicativos Web contra vulnerabilidades e explorações. Exemplos comuns são ataques de injeção SQL, scripts entre sites e cabeçalhos mal formados. O gateway de aplicativo filtra esse tráfego e o impede de chegar a seus servidores Web. É possível configurar quais regras você deseja que sejam habilitadas. A capacidade de configurar políticas de negociação SSL é fornecida para permitir que determinadas políticas sejam desabilitadas. Para saber mais sobre o firewall do aplicativo Web, leia o artigo [Firewall do aplicativo Web](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

Se precisar de uma funcionalidade de rede que o Azure não fornece ou se quiser usar aplicativos de rede que você usa localmente, você pode implementar os produtos em VMs e conectá-las à sua VNet. O [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) contém várias VMs diferentes pré-configuradas com aplicativos de rede que você pode usar atualmente. Essas VMs pré-configuradas normalmente são conhecidas como NVAs (soluções de virtualização de rede). As NVAs estão disponíveis com aplicativos como otimização de WAN e firewall.

## <a name="routing"></a>Roteamento

O Azure cria tabelas de rotas padrão que permitem aos recursos conectados a qualquer sub-rede e em qualquer VNet a comunicação mútua. Você pode implementar um dos tipos de rota a seguir, ou ambos, para substituir as rotas padrão criadas pelo Azure:
- **Definido pelo usuário:** é possível criar tabelas de rotas personalizadas com rotas que controlam para que local o tráfego será roteado para cada sub-rede. Para saber mais sobre as rotas definidas pelo usuário, leia o artigo [Rotas definidas pelo usuário](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Protocolo BGP (Border Gateway Protocol):** se você conectar sua VNet à rede local usando um Gateway de VPN do Azure ou conexão de ExpressRoute, poderá propagar as rotas BGP às suas VNets. O BGP é o protocolo de roteamento padrão usado na Internet para a troca de informações de roteamento e acessibilidade entre duas ou mais redes. Quando usado no contexto das Redes Virtuais do Azure, o BGP habilita os Gateways de VPN do Azure e os dispositivos de VPN locais, chamados de vizinhos ou pares de BGP, a trocar "rotas" que informam os dois gateways sobre a disponibilidade e a acessibilidade para que esses prefixos percorram os gateways ou os roteadores envolvidos. O BGP também pode habilitar o roteamento de trânsito entre várias redes propagando as rotas que um gateway BGP obtém de um par no nível de protocolo BGP para todos os outros pares do BGP. Para saber mais sobre o BGP, consulte o artigo [Visão geral do BGP com Gateways de VPN do Azure](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="manageability"></a>Capacidade de gerenciamento

O Azure fornece as seguintes ferramentas para monitorar e gerenciar a rede:
- **Logs de atividade:** todos os recursos do Azure têm logs de atividades que fornecem informações sobre operações ocorridas, status das operações e quem iniciou a operação. Para saber mais sobre os logs de atividades, leia o artigo [Visão geral dos logs de atividades](../azure-monitor/platform/activity-logs-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Logs de diagnóstico:** Eventos periódicos e espontâneas são criados pelos recursos da rede e registrados nas contas de armazenamento do Azure, enviados para um Hub de eventos do Azure, ou enviados para os logs do Azure Monitor. Os logs de diagnóstico fornecem informações sobre a integridade de um recurso. Os logs de diagnóstico são fornecidos para o Balanceador de Carga (voltado para a Internet), Grupos de Segurança de Rede, rotas e o Gateway de Aplicativo. Para saber mais sobre os logs de diagnóstico, leia o artigo [Visão geral dos logs de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Métricas:** as métricas são contadores e medidas de desempenho coletados durante um período. Elas podem ser usadas para disparar alertas com base em limites. Atualmente, as métricas estão disponíveis para o Gateway de Aplicativo. Para saber mais sobre as métricas, leia o artigo [Visão geral das métricas](../monitoring-and-diagnostics/monitoring-overview-metrics.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Solução de problemas:** informações sobre solução de problemas podem ser acessadas diretamente no portal do Azure. As informações ajudam a diagnosticar problemas comuns com o ExpressRoute, o Gateway de VPN, o Gateway de Aplicativo, os Logs de Segurança da Rede, as Rotas, o DNS, o Balanceador de Carga e o Gerenciador de Tráfego.
- **RBAC (Controle de Acesso Baseado em Função):** controle quem pode criar e gerenciar recursos de rede com o RBAC (controle de acesso baseado em função). Saiba mais sobre o RBAC lendo o artigo [Introdução ao RBAC](../role-based-access-control/overview.md?toc=%2fazure%2fnetworking%2ftoc.json). 
- **Captura de pacotes:** o serviço de Observador de Rede do Azure possibilita executar uma captura de pacote em uma VM por meio de uma extensão na VM. Essa funcionalidade está disponível para VMs Linux e Windows. Para saber mais sobre a captura de pacote, leia o artigo [Visão geral da captura de pacotes](../network-watcher/network-watcher-packet-capture-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Verificar fluxos de IP:** o Observador de Rede permite verificar os fluxos de IP entre uma VM do Azure e um recurso remoto para determinar se os pacotes são permitidos ou negados. Essa funcionalidade possibilita que os administradores diagnostiquem rapidamente problemas de conectividade. Para saber mais sobre como verificar fluxos de IP, leia o artigo [Visão geral da verificação de fluxo de IP](../network-watcher/network-watcher-ip-flow-verify-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Solucionar problemas de conectividade de VPN:** a capacidade de solução de problemas de VPN do Observador de Rede possibilita consultar uma conexão ou gateway e verificar a integridade dos recursos. Para saber mais sobre como solucionar problemas de conexões VPN, leia o artigo [Visão geral da solução de problemas de conectividade de VPN](../network-watcher/network-watcher-troubleshoot-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Exibir a topologia de rede:** exiba uma representação gráfica dos recursos de rede em uma VNet com o Observador de Rede. Para saber mais sobre a exibição da topologia de rede, leia o artigo [Visão geral da topologia](../network-watcher/network-watcher-topology-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="tools"></a>Ferramentas de implantação e configuração

É possível implantar e configurar recursos de rede do Azure com qualquer uma das seguintes ferramentas:

- **Portal do Azure:** uma interface gráfica do usuário que é executada em um navegador. Abra o [Portal do Azure](https://portal.azure.com).
- **Azure PowerShell:** ferramentas de linha de comando para gerenciar o Azure de computadores Windows. Saiba mais sobre o Azure PowerShell lendo o artigo [Visão geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fnetworking%2ftoc.json).
- **CLI (interface de linha de comando) do Azure**: ferramentas de linha de comando para gerenciar o Azure de computadores Windows, Linux ou macOS. Saiba mais sobre a CLI do Azure lendo o artigo [Visão geral da CLI do Azure](/cli/azure/get-started-with-azure-cli?toc=%2fazure%2fnetworking%2ftoc.json).
- **Modelos do Azure Resource Manager:** um arquivo (no formato JSON) que define a infraestrutura e a configuração de uma solução do Azure. Usando um modelo, você pode implantar a solução repetidamente em todo seu ciclo de vida e com a confiança de que seus recursos serão implantados em um estado consistente. Para saber mais sobre a criação de modelos, leia o artigo [Práticas recomendadas para a criação de modelos](../azure-resource-manager/resource-manager-template-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json). Modelos podem ser implantados com o PowerShell, a CLI e o Portal do Azure. Para começar a usar os modelos imediatamente, implante um dos muitos modelos pré-configurados na biblioteca [Modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?term=network). 

## <a name="pricing"></a>Preços

Alguns dos serviços de rede do Azure têm um encargo, enquanto outros são gratuitos. Exiba as páginas com o preços da [Rede virtual](https://azure.microsoft.com/pricing/details/virtual-network), do [Gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway), do [Gateway de Aplicativo](https://azure.microsoft.com/pricing/details/application-gateway/), do [Balanceador de Carga](https://azure.microsoft.com/pricing/details/load-balancer), do [Observador de Rede](https://azure.microsoft.com/pricing/details/network-watcher), do [DNS](https://azure.microsoft.com/pricing/details/dns), do [Gerenciador de Tráfego](https://azure.microsoft.com/pricing/details/traffic-manager) e do [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute).

## <a name="next-steps"></a>Próximas etapas

- Criar sua primeira VNet e conecte algumas VMs a ela executando as etapas no artigo [Criar sua primeira rede virtual](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Conecte seu computador a uma VNet executando as etapas no artigo [Configurar uma conexão ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Faça o balanceamento de carga do tráfego de Internet para servidores públicos concluindo as etapas em [Criar um balanceador de carga voltado para a Internet](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
