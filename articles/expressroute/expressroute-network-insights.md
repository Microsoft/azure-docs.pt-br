---
title: Insights do Azure ExpressRoute usando o Network insights
description: Saiba mais sobre as ideias do Azure ExpressRoute usando o insights de rede.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/23/2021
ms.author: duau
ms.openlocfilehash: 7033ea6a1ba6d85f9aa15e14bb9577b2439c59a8
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105050423"
---
# <a name="azure-expressroute-insights-using-network-insights"></a>Insights do Azure ExpressRoute usando o Network insights

Este artigo explica como o Network insights pode ajudá-lo a exibir suas configurações e métricas de ExpressRoute em um único lugar. Por meio do insights de rede, você pode exibir mapas topológica e painéis de integridade contendo informações importantes do ExpressRoute sem a necessidade de concluir qualquer configuração extra.

:::image type="content" source="./media/expressroute-network-insights/monitor-landing-page.png" alt-text="Captura de tela da página de aterrissagem do monitor do ExpressRoute." lightbox="./media/expressroute-network-insights/monitor-landing-page-expanded.png":::

## <a name="visualize-functional-dependencies"></a>Visualizar dependências funcionais

Para exibir essa solução, navegue até a página *Azure monitor* , selecione *redes* e, em seguida, selecione o cartão de *circuitos do ExpressRoute* . Em seguida, selecione o botão de topologia para o circuito que você deseja exibir.

A exibição de dependência funcional fornece uma visão clara da configuração do ExpressRoute, descrevendo a relação entre diferentes componentes do ExpressRoute (emparelhamentos, conexões, gateways).

:::image type="content" source="./media/expressroute-network-insights/topology-view.png" alt-text="Captura de tela da exibição de topologia para insights de rede." lightbox="./media/expressroute-network-insights/topology-view-expanded.png":::

Passe o mouse sobre qualquer componente no mapa de topologia para exibir as informações de configuração. Por exemplo, passe o mouse sobre um componente de emparelhamento de ExpressRoute para exibir detalhes como largura de banda de circuito e habilitação de Alcance Global.

:::image type="content" source="./media/expressroute-network-insights/topology-hovered.png" alt-text="Captura de tela de focalizar os recursos de exibição de topologia." lightbox="./media/expressroute-network-insights/topology-hovered-expanded.png":::

## <a name="view-a-detailed-and-pre-loaded-metrics-dashboard"></a>Exibir um painel de métricas detalhado e pré-carregado

Depois de examinar a topologia da configuração do ExpressRoute usando a exibição de dependência funcional, selecione **Exibir métricas detalhadas** para navegar até a exibição de métricas detalhadas para entender o desempenho do seu circuito. Essa exibição oferece uma lista organizada de recursos vinculados e um rico painel de métricas importantes do ExpressRoute.

A seção **recursos vinculados** lista os gateways do ExpressRoute conectados e os emparelhamentos configurados, que você pode selecionar para navegar até a página de recursos correspondente.

:::image type="content" source="./media/expressroute-network-insights/linked-resources.png" alt-text="Captura de tela do recurso vinculado na página monitor.":::


A seção de **métricas do ExpressRoute** inclui gráficos de métricas de circuito importantes entre as categorias de **disponibilidade**, **taxa de transferência**, **quedas de pacotes** e **métricas de gateway**.

### <a name="availability"></a>Disponibilidade

A guia *disponibilidade* acompanha a disponibilidade do ARP e do BGP, plotando os dados para o circuito como uma conexão inteira e individual (primária e secundária). 

:::image type="content" source="./media/expressroute-network-insights/arp-bgp-availability.png" alt-text="Captura de tela dos grafos de métrica de disponibilidade." lightbox="./media/expressroute-network-insights/arp-bgp-availability-expanded.png":::

### <a name="throughput"></a>Produtividade

Da mesma forma, a guia *taxa de transferência* plota a taxa de transferência total de tráfego de entrada e saída para o circuito em bits/segundo. Você também pode exibir a taxa de transferência para conexões individuais e cada tipo de emparelhamento configurado.

:::image type="content" source="./media/expressroute-network-insights/throughput.png" alt-text="Captura de tela de grafos de métrica de taxa de transferência." lightbox="./media/expressroute-network-insights/throughput-expanded.png":::

### <a name="packet-drops"></a>Remoções de pacotes

A guia *descartes de pacotes* plota os bits/segundo descartados para o tráfego de entrada e saída por meio do circuito. Essa guia fornece uma maneira fácil de monitorar problemas de desempenho que podem ocorrer se você precisar ou exceder regularmente a largura de banda do circuito.

:::image type="content" source="./media/expressroute-network-insights/dropped-packets.png" alt-text="Captura de tela de gráficos de pacotes soltos." lightbox="./media/expressroute-network-insights/dropped-packets-expanded.png":::

### <a name="gateway-metrics"></a>Métricas de gateway

Por fim, a guia métricas de gateway é preenchida com gráficos de métricas-chave para um gateway de ExpressRoute selecionado (da seção recursos vinculados). Use essa guia quando precisar monitorar a conectividade com redes virtuais específicas.

:::image type="content" source="./media/expressroute-network-insights/gateway-metrics.png" alt-text="Captura de tela da taxa de transferência do gateway e métricas de CPU." lightbox="./media/expressroute-network-insights/gateway-metrics-expanded.png":::

## <a name="next-steps"></a>Próximas etapas

Configurar sua conexão do ExpressRoute.
  
* Saiba mais sobre o [Azure ExpressRoute](expressroute-introduction.md), o [insights de rede](../azure-monitor/insights/network-insights-overview.md)e o [observador de rede](../network-watcher/network-watcher-monitoring-overview.md)
* [Criar e modificar um circuito](expressroute-howto-circuit-arm.md)
* [Criar e modificar a configuração de emparelhamento](expressroute-howto-routing-arm.md)
* [Vincular uma rede virtual a um circuito do ExpressRoute](expressroute-howto-linkvnet-arm.md)
* [Personalizar suas métricas](expressroute-monitoring-metrics-alerts.md) e criar um [Monitor de conexão](../network-watcher/connection-monitor-overview.md)
