---
title: 'Visão geral do ExpressRoute: conectar-se por uma conexão privada'
description: A visão geral técnica do ExpressRoute explica como uma conexão do ExpressRoute funciona para estender sua rede local para o Azure por meio de uma conexão privada.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: overview
ms.date: 10/05/2020
ms.author: duau
ms.openlocfilehash: 82562eae748753cd785851c5d91f5f152b4c9960
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92206944"
---
# <a name="what-is-azure-expressroute"></a>O que é o Azure ExpressRoute?
O ExpressRoute permite que você estenda suas redes locais para a nuvem da Microsoft em uma conexão privada com a ajuda de um provedor de conectividade. Com o ExpressRoute, você pode estabelecer conexões com os serviços em nuvem da Microsoft, como o Microsoft Azure e o Microsoft 365.

A conectividade pode ocorrer de uma rede any-to-any (VPN de IP), uma rede Ethernet ponto a ponto ou uma conexão cruzada virtual por meio de um provedor de conectividade em uma colocação. As conexões do ExpressRoute não passam pela Internet pública. Isso permite que as conexões de ExpressRoute ofereçam mais confiabilidade, mais velocidade, latências consistentes e muito mais segurança do que as conexões típicas pela Internet. Para saber mais sobre como conectar sua rede à Microsoft usando ExpressRoute, veja [Modelos de conectividade do ExpressRoute](expressroute-connectivity-models.md).

![Visão geral da conexão do ExpressRoute](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>Principais benefícios

* Conectividade de Camada 3 entre sua rede local e a Microsoft Cloud por meio de um provedor de conectividade. A conectividade pode ocorrer de uma rede “qualquer para qualquer” (IPVPN), de uma conexão Ethernet ponto a ponto ou por meio de uma conexão cruzada virtual via troca Ethernet.
* Conectividade com os serviços de nuvem da Microsoft em todas as regiões da região geopolítica.
* Conectividade global com os serviços da Microsoft em todas as regiões com o complemento premium do ExpressRoute.
* Roteamento dinâmico entre sua rede e a Microsoft por meio do BGP.
* Redundância interna em cada local de emparelhamento para proporcionar maior confiabilidade.
* [SLA](https://azure.microsoft.com/support/legal/sla/)do tempo de atividade da conexão.
* Suporte a QoS para Skype for Business.

Para saber mais, veja as [perguntas frequentes sobre o ExpressRoute](expressroute-faqs.md).

## <a name="features"></a>Recursos

### <a name="layer-3-connectivity"></a>Conectividade de Camada 3
A Microsoft usa BGP, um protocolo padrão do setor para roteamento dinâmico, a fim de trocar rotas entre sua rede local, suas instâncias no Azure e os endereços públicos da Microsoft. Estabelecemos várias sessões BGP com sua rede para perfis de tráfego diferentes. Encontre mais detalhes no artigo [Circuito e domínios de roteamento do ExpressRoute](expressroute-circuit-peerings.md) .

### <a name="redundancy"></a>Redundância
Cada circuito do ExpressRoute é formado por duas conexões com dois roteadores MSEEs (de borda do Microsoft Enterprise) em uma [Localização do ExpressRoute](./expressroute-locations.md#expressroute-locations) no provedor de conectividade/na borda da rede. A Microsoft exige uma conexão BGP dupla do provedor de conectividade/da borda de sua rede, uma para cada MSEE. Você pode optar por não implantar dispositivos redundantes/circuitos de Ethernet em seu lado. No entanto, os provedores de conectividade usam dispositivos redundantes para garantir que as conexões sejam entregues à Microsoft de forma redundante. É obrigatório ter uma configuração de conectividade de Camada 3 redundante para que nosso [SLA](https://azure.microsoft.com/support/legal/sla/) seja válido.

### <a name="connectivity-to-microsoft-cloud-services"></a>Conectividade com serviços de nuvem da Microsoft
As conexões do ExpressRoute permitem o acesso aos seguintes serviços:
* Serviços do Microsoft Azure
* Serviços do Microsoft 365

> [!NOTE]
> [!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]
> 

Para obter uma lista detalhada dos serviços com suporte no ExpressRoute, visite a página de [Perguntas frequentes sobre o ExpressRoute](expressroute-faqs.md).

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Conectividade com todas as regiões em uma região geopolítica
Você pode se conectar à Microsoft em um dos nossos [locais de emparelhamento](expressroute-locations.md) e acessar as regiões dentro da região geopolítica.

Por exemplo, se você se conectar à Microsoft em Amsterdã por meio do ExpressRoute. Terá acesso a todos os serviços em nuvem da Microsoft hospedados no Norte e no Oeste da Europa. Para obter uma visão geral das regiões geopolíticas, das regiões associadas da nuvem da Microsoft e de locais de emparelhamento correspondentes do ExpressRoute, confira o artigo [Parceiros e locais de emparelhamento do ExpressRoute](expressroute-locations.md).

### <a name="global-connectivity-with-expressroute-premium"></a>Conectividade global com o ExpressRoute Premium
Você pode habilitar o [ExpressRoute Premium](expressroute-faqs.md) a fim de estender a conectividade pelas fronteiras geopolíticas. Por exemplo, se você se conectar à Microsoft em Amsterdã por meio do ExpressRoute, terá acesso a todos os serviços em nuvem da Microsoft hospedados em todas as regiões do mundo. Você também pode acessar os serviços implantados na América do Sul ou na Austrália da mesma forma que acessa as regiões do Norte e do Oeste da Europa. As nuvens nacionais estão excluídas.

### <a name="local-connectivity-with-expressroute-local"></a>Conectividade local com o ExpressRoute Local
Você pode transferir dados de maneira econômica habilitando o [SKU Local](expressroute-faqs.md). Com o SKU Local, você pode levar seus dados para uma localização do ExpressRoute próxima à região do Azure desejada. Com o Local, a transferência de dados está incluída no encargo de porta do ExpressRoute. 

### <a name="across-on-premises-connectivity-with-expressroute-global-reach"></a>Em conectividade local com Alcance Global do ExpressRoute
Você pode habilitar o Alcance Global do ExpressRoute para trocar dados entre sites locais conectando seus circuitos do ExpressRoute. Por exemplo, se você tiver uma data center privado na Califórnia conectado a um circuito do ExpressRoute no Vale do Silício e outro data center privado no Texas conectado a um circuito do ExpressRoute em Dallas. Com o Alcance Global do ExpressRoute, você pode conectar seus data centers privados por meio desses dois circuitos do ExpressRoute. O tráfego entre data centers vai percorrer a rede da Microsoft.

Para obter mais informações, veja [Alcance Global do ExpressRoute](expressroute-global-reach.md).
### <a name="rich-connectivity-partner-ecosystem"></a>Ecossistema abundante de parceiros de conectividade
O ExpressRoute tem um ecossistema de provedores de conectividade e parceiros integradores de sistemas em constante crescimento. Para obter as informações mais recentes, consulte [Parceiros do ExpressRoute e locais de emparelhamento](expressroute-locations.md).

### <a name="connectivity-to-national-clouds"></a>Conectividade com nuvens nacionais
A Microsoft opera ambientes de nuvem isolados para regiões geopolíticas e segmentos de clientes especiais. Consulte a página [Parceiros do ExpressRoute e locais de emparelhamento](expressroute-locations.md) para obter uma lista de nuvens e provedores nacionais.

### <a name="expressroute-direct"></a>ExpressRoute Direct
O ExpressRoute Direct fornece aos clientes a oportunidade de conectar-se diretamente à rede global da Microsoft em locais de emparelhamento distribuídos estrategicamente no mundo todo. O ExpressRoute Direct oferece conectividade dupla de 100 Gbps, que suporta conectividade ativa / ativa em escala.

Os principais recursos que o ExpressRoute Direct fornece incluem, mas não estão limitados a:

* Ingestão de dados em massa em serviços como Armazenamento e o Cosmos DB
* Isolamento físico para setores regulamentados e que requerem conectividade dedicada e isolada, como: bancos, governo e varejo
* Controle granular de distribuição de circuito com base em unidade de negócios

Para obter mais informações, veja [Sobre o ExpressRoute Direct](./expressroute-erdirect-about.md).

### <a name="bandwidth-options"></a>Opções de largura de banda
É possível comprar circuitos do ExpressRoute para várias larguras de banda. As larguras de banda compatíveis são listadas a seguir. Não deixe de entrar em contato com seu provedor de conectividade para determinar as larguras de banda com suporte.

* 50 Mbps
* 100 Mbps
* 200 Mbps
* 500 Mbps
* 1 Gbps
* 2 Gbps
* 5 Gbps
* 10 Gbps

### <a name="dynamic-scaling-of-bandwidth"></a>Dimensionamento dinâmico de largura de banda
Você pode aumentar a largura de banda do circuito do ExpressRoute (em uma base de melhor esforço) sem precisar subdividir suas conexões. Para obter mais informações, confira [Como modificar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md#modify).

### <a name="flexible-billing-models"></a>Modelos flexíveis de cobrança
Escolha o modelo de cobrança que funcione melhor para você. Escolha um dos modelos de cobrança listados a seguir. Para obter mais informações, confira [Perguntas frequentes sobre o ExpressRoute](expressroute-faqs.md).

* **Dados ilimitados**. A cobrança é baseada em um valor mensal; todas as transferências de dados de entrada e saída estão incluídas gratuitamente.
* **Dados limitados**. A cobrança é baseada em um valor mensal; todas as transferências de dados de entrada são gratuitas. A cobrança pelas transferências de dados de saída ocorrem de acordo com a quantidade de GB da transferência de dados. As taxas de transferência de dados variam de acordo com a região.
* **Complemento ExpressRoute premium**. O ExpressRoute premium é um complemento do circuito do ExpressRoute. O complemento premium do ExpressRoute fornece os seguintes recursos: 
  * Aumento dos limites de rota para emparelhamento público e privado do Azure de 4.000 para 10.000 rotas.
  * Conectividade global para serviços. Um circuito do ExpressRoute criado em qualquer região (excluindo nuvens nacionais) terá acesso aos recursos em qualquer região do mundo. Por exemplo, uma rede virtual criada na Europa Ocidental pode ser acessada por meio de um circuito do ExpressRoute provisionado no Vale do Silício.
  * Aumento do número de links de Rede Virtual por circuito do ExpressRoute de 10 para um limite maior, dependendo da largura de banda do circuito.

## <a name="faq"></a>Perguntas frequentes
Para ver perguntas frequentes sobre o ExpressRoute, consulte as [Perguntas frequentes sobre o ExpressRoute](expressroute-faqs.md).

## <a name="whats-new"></a><a name="new"></a>Novidades

Assine o RSS feed e veja as atualizações mais recentes dos recursos do ExpressRoute na página [Atualizações do Azure](https://azure.microsoft.com/updates/?category=networking&query=ExpressRoute).

## <a name="next-steps"></a>Próximas etapas
* Certifique-se que todos os pré-requisitos foram atendidos. Consulte [Pré-requisitos do ExpressRoute](expressroute-prerequisites.md).
* Saiba mais sobre [Modelos de conectividade do ExpressRoute](expressroute-connectivity-models.md).
* Encontrar um provedor de serviços. Consulte [Parceiros e locais de emparelhamento do ExpressRoute](expressroute-locations.md).