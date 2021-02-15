---
title: Sobre o roteamento de hub virtual
titleSuffix: Azure Virtual WAN
description: Este artigo descreve o roteamento de Hub virtual
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 51480a49aab2c1277eeb846c593fcb2bc858d1f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983715"
---
# <a name="about-virtual-hub-routing"></a>Sobre o roteamento de hub virtual

Os recursos de roteamento em um hub virtual são fornecidos por um roteador que gerencia todo o roteamento entre gateways usando Border Gateway Protocol (BGP). Um hub virtual pode conter vários gateways, como um gateway de VPN site a site, gateway de ExpressRoute, gateway de ponto a site, firewall do Azure. Esse roteador também fornece conectividade de trânsito entre redes virtuais que se conectam a um hub virtual e podem dar suporte a uma taxa de transferência agregada de 50 Gbps. Esses recursos de roteamento se aplicam aos clientes de WAN virtual padrão. 

Para configurar o roteamento, consulte [como configurar o roteamento de Hub virtual](how-to-virtual-hub-routing.md).

## <a name="routing-concepts"></a><a name="concepts"></a>Conceitos de roteamento

As seções a seguir descrevem os principais conceitos no roteamento de Hub virtual.

### <a name="hub-route-table"></a><a name="hub-route"></a>Tabela de rotas do Hub

Uma tabela de rotas do Hub virtual pode conter uma ou mais rotas. Uma rota inclui seu nome, um rótulo, um tipo de destino, uma lista de prefixos de destino e informações do próximo salto para um pacote a ser roteado. Uma **conexão** normalmente terá uma configuração de roteamento associada ou propagada para uma tabela de rotas

### <a name="connection"></a><a name="connection"></a>Conexão

Conexões são recursos do Resource Manager que têm uma configuração de roteamento. Os quatro tipos de conexões são:

* **Conexão VPN**: conecta um site VPN a um gateway de VPN de Hub virtual.
* **Conexão do expressroute**: conecta um circuito do expressroute a um gateway de expressroute do Hub virtual.
* **Conexão de configuração do P2S**: conecta uma configuração de VPN de usuário (ponto a site) a um gateway de VPN de usuário de Hub virtual (ponto a site).
* **Conexão de rede virtual do Hub**: conecta redes virtuais a um hub virtual.

Você pode definir a configuração de roteamento para uma conexão de rede virtual durante a instalação. Por padrão, todas as conexões são associadas e propagadas para a tabela de rotas padrão.

### <a name="association"></a><a name="association"></a>Associação

Cada conexão é associada a uma tabela de rotas. Associar uma conexão a uma tabela de rotas permite que o tráfego seja enviado para o destino indicado como rotas na tabela de rotas. A configuração de roteamento da conexão mostrará a tabela de rotas associada.  Várias conexões podem ser associadas à mesma tabela de rotas. Todas as conexões VPN, ExpressRoute e VPN de usuário são associadas à mesma tabela de rotas (padrão).

Por padrão, todas as conexões são associadas a uma **tabela de rotas padrão** em um hub virtual. Cada Hub virtual tem sua própria tabela de rotas padrão, que pode ser editada para adicionar uma rota estática (s). As rotas adicionadas estaticamente têm precedência sobre rotas aprendidas dinamicamente para os mesmos prefixos.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-association.png" alt-text="Associação":::

### <a name="propagation"></a><a name="propagation"></a>Propagação

As conexões propagam dinamicamente as rotas para uma tabela de rotas. Com uma conexão VPN, conexão do ExpressRoute ou conexão de configuração P2S, as rotas são propagadas do Hub virtual para o roteador local usando o BGP. As rotas podem ser propagadas para uma ou várias tabelas de rotas.

Uma **tabela de rota None** também está disponível para cada Hub virtual. A propagação para a tabela de rotas None implica que nenhuma rota deve ser propagada da conexão. As conexões VPN, ExpressRoute e VPN de usuário propagam rotas para o mesmo conjunto de tabelas de rotas.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-propagation.png" alt-text="Associação":::

### <a name="labels"></a><a name="static"></a>Rótulos
Os rótulos fornecem um mecanismo para agrupar logicamente as tabelas de rotas. Isso é especialmente útil durante a propagação de rotas de conexões com várias tabelas de rotas. Por exemplo, a tabela de rotas padrão tem um rótulo interno chamado ' default '. Quando os usuários propagam rotas de conexão para o rótulo ' default ', ele se aplica automaticamente a todas as tabelas de rotas padrão em todos os hubs na WAN virtual. 

### <a name="configuring-static-routes-in-a-virtual-network-connection"></a><a name="static"></a>Configurando rotas estáticas em uma conexão de rede virtual

A configuração de rotas estáticas fornece um mecanismo para direcionar o tráfego por meio de um IP do próximo salto, que pode ser de uma NVA (solução de virtualização de rede) provisionada em uma VNet do spoke conectada a um hub virtual. A rota estática é composta por um nome de rota, uma lista de prefixos de destino e um IP do próximo salto.

## <a name="reset-hub"></a><a name="route"></a>Redefinir Hub
Disponível apenas no portal do Azure, essa opção fornece ao usuário um meio para trazer todos os recursos com falha, como tabelas de rotas, roteador de Hub ou o próprio recurso de Hub virtual para seu estado de provisionamento. Essa é uma opção adicional para o usuário considerar antes de entrar em contato com a Microsoft para obter suporte. Esta operação não redefine nenhum dos gateways em um hub virtual. 

## <a name="route-tables-in-basic-and-standard-virtual-wans-prior-to-the-feature-set-of-association-and-propagation"></a><a name="route"></a>Direcionar tabelas em WANs virtuais básicas e padrão antes do conjunto de recursos de associação e propagação

As tabelas de rotas agora têm recursos para associação e propagação. Uma tabela de rotas preexistente é uma tabela de rotas que não tem esses recursos. Se você tiver rotas preexistentes no Roteamento de Hub e quiser usar as novas funcionalidades, considere o seguinte:

* **Clientes de WAN virtual padrão com rotas pré-existentes no Hub virtual**:

Se você tiver rotas pré-existentes na seção de roteamento para o Hub no portal do Azure, você precisará primeiro excluí-las e, em seguida, tentar criar novas tabelas de rotas (disponíveis na seção de tabelas de rotas para o Hub em portal do Azure)

* **Clientes básicos de WAN virtual com rotas pré-existentes no Hub virtual**: se você tiver rotas pré-existentes na seção de roteamento para o hub no portal do Azure, será necessário primeiro excluí-las e, em seguida, **Atualizar** sua Wan virtual básica para a WAN virtual padrão. Confira [Atualizar uma WAN Virtual de Básica para Standard](upgrade-virtual-wan.md).

## <a name="virtual-wan-routing-considerations"></a><a name="considerations"></a>Considerações sobre roteamento de WAN virtual

Considere o seguinte ao configurar o roteamento de WAN virtual:

* Todas as conexões de ramificação (ponto a site, site a site e ExpressRoute) precisam ser associadas à tabela de rotas padrão. Dessa forma, todas as ramificações aprenderão os mesmos prefixos.
* Todas as conexões de ramificação precisam propagar suas rotas para o mesmo conjunto de tabelas de rotas. Por exemplo, se você decidir que as ramificações devem ser propagadas para a tabela de rotas padrão, essa configuração deverá ser consistente em todas as ramificações. Como resultado, todas as conexões associadas à tabela de rotas padrão poderão alcançar todas as ramificações.
* No momento, não há suporte para Branch a Branch por meio do firewall do Azure.
* Ao usar o Firewall do Azure em várias regiões, todas as redes virtuais spoke devem ser associadas à mesma tabela de rotas. Por exemplo, ter um subconjunto do VNets passando pelo firewall do Azure enquanto outros VNets ignoram o Firewall do Azure no mesmo Hub virtual não é possível.
* Um único IP do próximo salto pode ser configurado por conexão VNet.
* O Hub virtual não dá suporte à rota estática para 0.0.0.0/0 e conexão de rede virtual do próximo salto (ou um IP de um dispositivo na conexão VNet)
* Todas as informações pertencentes à rota 0.0.0.0/0 são confinadas na tabela de rotas de um hub local. Essa rota não se propaga entre hubs.

## <a name="next-steps"></a>Próximas etapas

Para configurar o roteamento, consulte [como configurar o roteamento de Hub virtual](how-to-virtual-hub-routing.md).

Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).
