---
title: "Visão geral do ExpressRoute: estenda suas redes locais para o Azure por meio de uma conexão privada | Microsoft Docs"
description: "Esta visão geral técnica do ExpressRoute explica como uma conexão do ExpressRoute funciona para estender sua rede local para o Azure por meio de uma conexão privada."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: fd95dcd5-df1d-41d6-85dd-e91d0091af05
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: cherylmc
ms.openlocfilehash: 7390462d79506e63989baadac2b2cee00eef325d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="expressroute-overview"></a>Visão geral do ExpressRoute
O Microsoft Azure ExpressRoute permite que você estenda suas redes locais até a nuvem da Microsoft por meio de uma conexão privada, facilitada por um provedor de conectividade. Com o ExpressRoute, você pode estabelecer conexões com os serviços de nuvem da Microsoft, como o Microsoft Azure, o Office 365 e o Dynamic 365.

A conectividade pode ocorrer de uma rede “qualquer para qualquer” (VPN IP), uma rede Ethernet ponto a ponto ou uma conexão cruzada virtual por meio de um provedor de conectividade em uma colocalização. As conexões do ExpressRoute não passam pela Internet pública. Isso permite que as conexões do ExpressRoute ofereçam mais confiabilidade, mais velocidade, latências menores e muito mais segurança do que as conexões típicas pela Internet. Para saber mais sobre como conectar sua rede à Microsoft usando ExpressRoute, veja [Modelos de conectividade do ExpressRoute](expressroute-connectivity-models.md).

![](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>Principais benefícios

* Conectividade de Camada 3 entre sua rede local e a Microsoft Cloud por meio de um provedor de conectividade. A conectividade pode ocorrer de uma rede “qualquer para qualquer” (IPVPN), de uma conexão Ethernet ponto a ponto ou por meio de uma conexão cruzada virtual via troca Ethernet.
* Conectividade com os serviços de nuvem da Microsoft em todas as regiões da região geopolítica.
* Conectividade global com os serviços da Microsoft em todas as regiões com o complemento premium do ExpressRoute.
* Roteamento dinâmico entre sua rede e a Microsoft por meio de protocolos padrão da indústria (BGP).
* Redundância interna em cada local de emparelhamento para proporcionar maior confiabilidade.
* [SLA](https://azure.microsoft.com/support/legal/sla/)do tempo de atividade da conexão.
* Suporte a QoS para Skype for Business.

Para saber mais, veja as [perguntas frequentes sobre o ExpressRoute](expressroute-faqs.md).

## <a name="features"></a>Recursos

### <a name="layer-3-connectivity"></a>Conectividade de Camada 3
A Microsoft usa o protocolo padrão do setor para roteamento dinâmico (BGP) a fim de trocar rotas entre sua rede local, suas instâncias no Azure e os endereços públicos da Microsoft.  Estabelecemos várias sessões BGP com sua rede para perfis de tráfego diferentes. Encontre mais detalhes no artigo [Circuito e domínios de roteamento do ExpressRoute](expressroute-circuit-peerings.md) .

### <a name="redundancy"></a>Redundância
Cada circuito do ExpressRoute é formado por duas conexões com dois roteadores de borda do Microsoft Enterprise (MSEEs) do provedor de conectividade/borda de sua rede. A Microsoft exige uma conexão BGP dupla do provedor de conectividade/seu lado – uma para cada MSEE. Você pode optar por não implantar dispositivos redundantes/circuitos de Ethernet em seu lado. No entanto, os provedores de conectividade usam dispositivos redundantes para garantir que as conexões sejam entregues à Microsoft de forma redundante. É obrigatório ter uma configuração de conectividade de Camada 3 redundante para que nosso [SLA](https://azure.microsoft.com/support/legal/sla/) seja válido.

### <a name="connectivity-to-microsoft-cloud-services"></a>Conectividade com serviços de nuvem da Microsoft
[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

As conexões do ExpressRoute permitem o acesso aos seguintes serviços:

* Serviços do Microsoft Azure
* Serviços do Microsoft Office 365
* Microsoft Dynamics 365

Visite a página de [Perguntas frequentes sobre o ExpressRoute](expressroute-faqs.md) para obter uma lista detalhada dos serviços com suporte por meio do ExpressRoute.

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Conectividade com todas as regiões em uma região geopolítica
Você pode se conectar à Microsoft em um dos nossos [locais de emparelhamento](expressroute-locations.md) e ter acesso a todas as regiões dentro da região geopolítica. 

Por exemplo, se você conectou a Microsoft em Amsterdã por meio do ExpressRoute, terá acesso a todos os serviços de nuvem da Microsoft hospedados no Norte da Europa e na Europa Ocidental. Veja o artigo [Parceiros e locais de emparelhamento do ExpressRoute](expressroute-locations.md) para obter uma visão geral das regiões geopolíticas, regiões associadas da nuvem da Microsoft e locais de emparelhamento correspondentes do ExpressRoute.

### <a name="global-connectivity-with-expressroute-premium-add-on"></a>Conectividade global com o complemento premium do ExpressRoute
Você pode habilitar o complemento do ExpressRoute premium a fim de estender a conectividade pelas fronteiras geopolíticas. Por exemplo, se você estiver conectado à Microsoft em Amsterdã por meio do ExpressRoute, terá acesso a todos os serviços de nuvem da Microsoft hospedados em todas as regiões do mundo (nuvens nacionais são excluídas). Você pode acessar serviços implantados na América do Sul ou na Austrália da mesma maneira que acessa as regiões do Norte da Europa e da Europa Ocidental.

### <a name="rich-connectivity-partner-ecosystem"></a>Ecossistema abundante de parceiros de conectividade
O ExpressRoute tem um ecossistema de provedores de conectividade e parceiros de SI em constante crescimento. Consulte o artigo [Provedores e locais do ExpressRoute](expressroute-locations.md) para obter as informações mais recentes.

### <a name="connectivity-to-national-clouds"></a>Conectividade com nuvens nacionais
A Microsoft opera ambientes de nuvem isolados para regiões geopolíticas e segmentos de clientes especiais. Consulte a página [Provedores e locais de ExpressRoute](expressroute-locations.md) para obter uma lista de nuvens e provedores nacionais.

### <a name="bandwidth-options"></a>Opções de largura de banda
É possível comprar circuitos do ExpressRoute para várias larguras de banda. As larguras de banda com suporte estão listadas abaixo. Não deixe de entrar em contato com seu provedor de conectividade para verificar a lista de larguras de banda com suporte oferecidas.

* 50 Mbps
* 100 Mbps
* 200 Mbps
* 500 Mbps
* 1 Gbps
* 2 Gbps
* 5 Gbps
* 10 Gbps

### <a name="dynamic-scaling-of-bandwidth"></a>Dimensionamento dinâmico de largura de banda
Você pode aumentar a largura de banda do circuito do ExpressRoute (em uma base de melhor esforço) sem precisar subdividir suas conexões. 

### <a name="flexible-billing-models"></a>Modelos flexíveis de cobrança
Escolha o modelo de cobrança que funcione melhor para você. Escolha um dos modelos de cobrança listados abaixo. Para saber mais, veja as [perguntas frequentes sobre o ExpressRoute](expressroute-faqs.md).

* **Dados ilimitados**. O circuito do ExpressRoute é cobrado com base em uma taxa mensal, e todas as transferências de dados de entrada e de saída são incluídas gratuitamente. 
* **Dados limitados**. O circuito do ExpressRoute será cobrado com base em uma taxa mensal. Todas as transferências de dados de entrada são gratuitas. A cobrança pelas transferências de dados de saída ocorrem de acordo com a quantidade de GB da transferência de dados. As taxas de transferência de dados variam de acordo com a região.
* **Complemento ExpressRoute premium**. O ExpressRoute premium é um complemento para o circuito do ExpressRoute. O complemento premium do ExpressRoute fornece os seguintes recursos: 
  * Aumento dos limites de rota para emparelhamento público e privado do Azure de 4.000 para 10.000 rotas.
  * Conectividade global para serviços. Um circuito do ExpressRoute criado em qualquer região (excluindo nuvens nacionais) terá acesso a recursos em qualquer região do mundo. Por exemplo, uma rede virtual criada na Europa Ocidental pode ser acessada por meio de um circuito do ExpressRoute provisionado no Vale do Silício.
  * Aumento do número de links de Rede Virtual por circuito do ExpressRoute de 10 para um limite maior, dependendo da largura de banda do circuito.

## <a name="faq"></a>Perguntas frequentes

Para perguntas frequentes sobre o ExpressRoute, consulte o [Perguntas frequentes sobre o ExpressRoute](expressroute-faqs.md).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Modelos de conectividade do ExpressRoute](expressroute-connectivity-models.md).
* Saiba mais sobre conexões e domínios de roteamento do ExpressRoute. Consulte [Circuitos e domínios de roteamento do ExpressRoute](expressroute-circuit-peerings.md).
* Encontrar um provedor de serviços. Consulte [Parceiros e locais de emparelhamento do ExpressRoute](expressroute-locations.md).
* Verifique se todos os pré-requisitos foram atendidos. Consulte [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).
* Consulte os requisitos para [Roteamento](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
* Configurar sua conexão do ExpressRoute.
  * [Criar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Configurar o emparelhamento para um circuito do ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
  * [Conectar uma rede virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Saiba mais sobre alguns dos outros principais [recursos de rede](../networking/networking-overview.md) do Azure.
