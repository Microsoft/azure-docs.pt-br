---
title: Arquitetura de rede de trânsito global de WAN virtual do Azure | Microsoft Docs
description: Saiba mais sobre a arquitetura de rede de trânsito global para WAN virtual
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 07/23/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand global transit network architecture as it relates to Virtual WAN.
ms.openlocfilehash: 2376c77ecc328788c842e045aafb618cbad39b0e
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68421436"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Arquitetura de rede de trânsito global e WAN virtual

A arquitetura de rede de trânsito global está sendo adotada pelas empresas para consolidar, conectar e controlar a superfície de ti moderna empresarial centrada em nuvem. Em uma empresa moderna centrada em nuvem, o tráfego de rede não precisa ser redirecionado para o HQ. A arquitetura de rede de trânsito global baseia-se nos conceitos de rede familiares e nos novos conceitos exclusivos das arquiteturas baseadas em nuvem e em nuvem.

![Arquitetura](./media/virtual-wan-global-transit-network-architecture/architecture2.png)

**Figura 1: Rede de trânsito global com WAN virtual**

As empresas modernas exigem conectividade onipresente entre aplicativos, dados e usuários do Hyper-Distributed na nuvem e no local. A WAN virtual do Azure permite uma arquitetura de rede de trânsito global habilitando uma conectividade onipresente, de qualquer a qualquer entre conjuntos distribuídos globalmente de VNets, sites, aplicativos e usuários. A WAN virtual do Azure é um serviço gerenciado pela Microsoft. Todos os componentes de rede dos quais esse serviço é composto são hospedados e gerenciados pela Microsoft. Para obter mais informações sobre WAN virtual, consulte o artigo [visão geral da WAN virtual](virtual-wan-about.md) .

Na arquitetura de WAN virtual do Azure, as regiões do Azure servem como hubs para os quais você pode optar por conectar seus branches. Depois que as ramificações estiverem conectadas, você poderá aproveitar o backbone do Azure para estabelecer uma conectividade entre ramificação e, opcionalmente, de ramificação para ramificação.

Você pode estabelecer uma WAN virtual criando um único Hub de WAN virtual na região que tem o maior número de spokes (branches, VNets, Users) e, em seguida, conectando os spokes que estão em outras regiões ao Hub. Como alternativa, se os spokes forem distribuídos geograficamente, você também poderá criar uma instância de hubs regionais e interconectar os hubs. Os hubs fazem parte da mesma WAN virtual, mas podem ser associados a diferentes políticas regionais.

## <a name="hub"></a>Trânsito Hub e spoke

A arquitetura de rede de trânsito global é baseada em um modelo de conectividade de Hub e spoke clássico em que o ' hub ' de rede hospedada na nuvem permite a conectividade transitiva entre pontos de extremidade que podem ser distribuídos entre diferentes tipos de ' spokes '.
  
Nesse modelo, um spoke pode ser:

* Rede virtual (VNets)
* Site do Branch físico
* Usuário remoto
* Internet

![diagrama de trânsito global Hub e spoke](./media/virtual-wan-global-transit-network-architecture/architecture.png)

**Figura 2: Hub e spoke**

A Figura 2 mostra a exibição lógica da rede global em que usuários distribuídos geograficamente, sites físicos e VNets são interconectados por meio de um hub de rede hospedado na nuvem. Essa arquitetura permite a conectividade de trânsito de um salto lógico entre os pontos de extremidade de rede. Os spokes são conectados ao Hub por vários serviços de rede do Azure, como ExpressRoute ou site a site-VPN para branches físicos, emparelhamento de VNet para VNets e VPN ponto a site para usuários remotos.

## <a name="crossregion"></a>Conectividade entre regiões

Para uma empresa, uma superfície de nuvem geralmente segue a superfície física. A maioria das empresas acessa a nuvem de uma região mais próxima de seus sites e usuários físicos. Uma das principais entidades da arquitetura de rede global é habilitar a conectividade entre regiões entre entidades de rede e pontos de extremidade. Uma superfície de nuvem pode abranger várias regiões. Isso significa que o tráfego de uma ramificação conectada à nuvem em uma região pode alcançar outro Branch ou uma VNet em uma região diferente usando conectividade de Hub para Hub, que está atualmente em nosso roteiro.

## <a name="any"></a>Conectividade qualquer-para-qualquer

A arquitetura de rede de trânsito global habilita *qualquer conectividade* por meio de um hub de rede central. Essa arquitetura elimina ou reduz a necessidade de malha completa ou modelos de conectividade de malha parcial que são mais complexos de criar e manter. Além disso, o controle de roteamento em vs. redes de malhas é mais fácil de configurar e manter.

Qualquer conectividade, no contexto de uma arquitetura global, permite que uma empresa com usuários distribuídos globalmente, branches, data centers, VNets e aplicativos se conectem entre si por meio do hub de trânsito. O Hub de trânsito atua como o sistema de trânsito global.

![caminhos de tráfego](./media/virtual-wan-global-transit-network-architecture/trafficpath.png)

**Figura 3: Caminhos de tráfego de WAN virtual**

A WAN virtual do Azure dá suporte aos seguintes caminhos de conectividade de trânsito globais. As letras entre parênteses são mapeadas para a Figura 3.

* Ramificação para VNet (a)  
* Ramificação para ramificação (b)
* Usuário remoto para VNet (c)
* Usuário remoto para ramificação (d)
* VNet a VNet usando emparelhamento VNet (e)
* Alcance Global ExpressRoute 

### <a name="branchvnet"></a>Ramificação para VNet

O Branch-to-VNet é o caminho principal com suporte da WAN virtual do Azure. Esse caminho permite que você conecte branches a cargas de trabalho corporativas do Azure IAAS implantadas no Azure VNets. As ramificações podem ser conectadas à WAN virtual via ExpressRoute ou VPN site a site. O tráfego está em trânsito para VNets que estão conectados aos hubs de WAN virtuais por meio de conexões VNet.

### <a name="branchbranch"></a>Ramificação para ramificação

As ramificações podem ser conectadas a um hub de WAN virtual do Azure usando circuitos de ExpressRoute e/ou conexões VPN site a site. Você pode conectar as ramificações ao Hub de WAN virtual que está na região mais próxima da ramificação.

Essa opção permite que as empresas aproveitem o backbone do Azure para conectar branches. No entanto, embora esse recurso esteja disponível, você deve avaliar os benefícios de conectar branches pela WAN virtual do Azure versus usar uma WAN privada.

### <a name="usertovnet"></a>Usuário para VNet remoto

Você pode habilitar o acesso remoto direto e seguro ao Azure usando conexões ponto a site de um cliente de usuário remoto para uma WAN virtual. Os usuários remotos da empresa não precisam mais hairpin-los para a nuvem usando uma VPN corporativa.

### <a name="usertobranch"></a>Usuário remoto para ramificação

O caminho do usuário para a ramificação remota permite que usuários remotos que usam uma conexão ponto a site acessem cargas de trabalho e aplicativos locais de acesso do Azure por meio da nuvem. Esse caminho dá aos usuários remotos a flexibilidade para acessar as cargas de trabalho que são implantadas no Azure e no local. As empresas podem habilitar o serviço de acesso remoto seguro baseado em nuvem central na WAN virtual do Azure.

### <a name="vnetvnet"></a>Trânsito de VNet para VNet usando emparelhamento VNet

Para conectar o VNets entre si para dar suporte a aplicativos de várias camadas que são implementados em vários VNets, use o emparelhamento VNet. Não há suporte para um cenário de trânsito de VNet para VNet por meio da WAN virtual do Azure no momento, mas está no roteiro do Azure. Conectar o VNets por meio do emparelhamento VNet é a solução recomendada para VNets que precisa ser conectada entre si. [Trânsito de gateway](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) (no contexto de emparelhamento VNet) não é necessário para WAN virtual porque a WAN virtual habilita automaticamente o tráfego de gateway.

### <a name="globalreach"></a>Alcance Global ExpressRoute

O ExpressRoute é uma maneira privada e resiliente de conectar suas redes locais ao Microsoft Cloud. O ExpressRoute Alcance Global é um recurso complementar para o ExpressRoute. Com o Alcance Global, você pode vincular circuitos do ExpressRoute juntos para fazer uma rede privada entre suas redes locais. As ramificações que estão conectadas à WAN virtual do Azure usando o ExpressRoute exigem que os Alcance Global do ExpressRoute se comuniquem entre si.

Nesse modelo, cada ramificação conectada ao Hub de WAN virtual usando o ExpressRoute pode se conectar ao VNets usando o caminho de ramificação para VNet. O tráfego de ramificação para ramificação não transita o Hub porque o ExpressRoute Alcance Global permite um caminho mais ideal na WAN do Azure.

## <a name="security"></a>Segurança e controle de política

O Hub de rede virtual interconecta e potencialmente vê todo o tráfego de trânsito. Pode ser o local para hospedar serviços e funções de rede central, como o roteamento de nuvem, a diretiva de rede e a segurança e o controle de acesso à Internet.

## <a name="next-steps"></a>Próximas etapas

Crie uma conexão usando a WAN virtual.

* [Conexões site a site usando a WAN virtual](virtual-wan-site-to-site-portal.md)
* [Conexões do ExpressRoute usando a WAN virtual](virtual-wan-expressroute-portal.md)
