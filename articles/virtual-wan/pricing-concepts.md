---
title: Sobre os preços da WAN virtual
titleSuffix: Azure Virtual WAN
description: Este artigo descreve as perguntas comuns sobre preços de WAN virtual
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: reyandap
ms.custom: references_pricing
ms.openlocfilehash: 13451291544f704000ab61d41ed5014fa69298e4
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517681"
---
# <a name="about-virtual-wan-pricing"></a>Sobre os preços da WAN virtual

A WAN virtual do Azure traz vários serviços de rede e segurança juntos em uma estrutura unificada. Ele se baseia em uma arquitetura de Hub e spoke, em que os hubs são gerenciados pela Microsoft com vários serviços fornecidos no Hub, como VPN, ExpressRoute, VPN de usuário (ponto a site), firewall, roteamento, etc.

Cada serviço na WAN virtual é cobrado. Portanto, sugerir um preço único não é aplicável à WAN virtual. A [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) fornece um mecanismo para derivar o custo, que se baseia nos serviços provisionados em uma WAN virtual. Este artigo aborda as perguntas mais frequentes sobre os preços da WAN virtual.

>[!NOTE]
>Para obter informações de preços atuais, consulte [preços de WAN virtual](https://azure.microsoft.com/pricing/details/virtual-wan/). Os encargos entre hubs (Hub para Hub) não são mostrados na página de preços da WAN virtual, pois estão sujeitos a Inter-Region (intra/inter-continental) encargos de [transferência de dados do Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

## <a name="common-pricing-questions"></a><a name="questions"></a>Perguntas comuns sobre preços

### <a name="what-is-a-scale-unit"></a><a name="scale-unit"></a>O que é uma unidade de escala?

Uma **unidade de escala** fornece a unidade para a capacidade de agregação de S2S (site a site), ponto a site (P2S) e EXPRESSROUTE (er) em um hub virtual. Por exemplo:

* **1 unidade de escala de VPN S2S** implica uma capacidade total de gateway de vpn de 500 Mbps (instâncias duplas implantadas para resiliência) em um hub virtual custando $0.361/hora.
* **1 unidade de escala er** implica um total de 2 Gbps de gateway er no Hub virtual custando $0,42/hr.
* **5 unidades de escala de er** implicariam um total de 10 Gbps de gateway de er dentro de uma VNet de Hub virtual com preço de US $0,42 * 5/hr. ER incrementa $0,25/h da sexta para 10º unidade de escala.

### <a name="what-is-a-connection-unit"></a><a name="connection-unit"></a>O que é uma unidade de conexão?

Uma **unidade de conexão** se aplica a qualquer ponto de extremidade local/não-Microsoft que se conecte aos gateways do Azure. Para VPN site a site, esse valor implica ramificações. Para VPN de usuário (ponto a site), esse valor implica usuários remotos. Para o ExpressRoute, esse valor implica conexões de circuito do ExpressRoute.<br>Por exemplo:

* Uma conexão de ramificação conectando-se à VPN do Azure em um hub virtual custa US $0,05/h. Portanto, as conexões de ramificação 100 que se conectam a um hub virtual do Azure custarão US $0,05 * 100/hr.

* Duas conexões de circuito do ExpressRoute conectando-se a um hub virtual custam US $0,05 * 2/hr.

* Três conexões de usuário remotas conectando-se ao gateway P2S do Hub virtual do Azure custarão $0,03 * 3/hr.

### <a name="how-are-data-transfer-charges-calculated"></a><a name="data-transfer"></a>Como as cobranças de transferência de dados são calculadas?

* Qualquer tráfego que insira o Azure não será cobrado. O tráfego que sai do Azure (via VPN, ExpressRoute ou conexões de VPN de usuário ponto a site) está sujeito aos [encargos de transferência de dados do Azure](https://azure.microsoft.com/pricing/details/bandwidth/)padrão.

* Para encargos de transferência de dados entre um hub de WAN virtual e um hub remoto de WAN virtual ou VNet em uma região diferente do hub de origem, as tarifas de transferência de dados se aplicam para o tráfego que sai de um Hub. Exemplo: o tráfego que sai de um hub leste dos EUA será cobrado $0,02/GB para um hub oeste dos EUA. Não há nenhum encargo para o tráfego entrando no Hub oeste dos EUA. Todo o tráfego do hub para o Hub está sujeito a Inter-Region (intra/inter-continental) encargos de [transferência de dados do Azure](https://azure.microsoft.com/pricing/details/bandwidth/). 

### <a name="what-is-the-difference-between-a-standard-hub-fee-and-a-standard-hub-processing-fee"></a><a name="fee"></a>Qual é a diferença entre uma taxa de Hub padrão e uma taxa de processamento de Hub padrão?

A WAN virtual vem em dois tipos:

* Uma **Wan virtual básica**, em que os usuários podem implantar vários hubs e aproveitar a conectividade VPN site a site. Uma WAN virtual básica não tem recursos avançados, como hubs totalmente entrelaçados, conectividade de ExpressRoute, conectividade de VPN de ponto a site/VPN de usuário, conectividade transitiva de VNet para VNet, conectividade de trânsito de VPN e ExpressRoute ou firewall do Azure, etc. Não há nenhuma taxa base ou taxa de processamento de dados para hubs em uma WAN virtual básica.

* Uma **Wan virtual padrão** fornece recursos avançados, como hubs totalmente entrelaçados, conectividade de expressroute, conectividade VPN de usuário/ponto a site, conectividade transitiva de VNet para vnet, conectividade de trânsito de VPN e ExpressRoute, firewall do Azure, etc. Todo o roteamento de Hub virtual é fornecido por um roteador que habilita vários serviços em um hub virtual. Há uma taxa base para o Hub, cujo preço é de $0,25/hr. Também há um encargo para o processamento de dados no roteador do Hub virtual para conectividade de trânsito de VNet para VNet. Consulte a figura a seguir.

 Na figura de **exemplo** abaixo, há dois VNETS, vnet 1 e vnet 2. Vamos supor que há 1 Gbps de dados sendo enviados de uma VM na VNET 1 para outra VM na VNET 2. As tarifas a seguir se aplicam:

* Taxa base do Hub virtual $0,25/hora

* Taxa de processamento de dados de Hub virtual de $0,02/GB para 1 Gbps

**Exemplo**

   :::image type="content" source="./media/pricing-concepts/figure-1.png" alt-text="Exemplo":::

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).

* Para obter os preços atuais, consulte [preços de WAN virtual](https://azure.microsoft.com/pricing/details/virtual-wan/).
