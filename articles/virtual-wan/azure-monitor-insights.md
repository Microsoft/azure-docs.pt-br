---
title: Monitorando a WAN virtual usando o Azure Monitor insights
description: Neste artigo, você aprenderá sobre como monitorar a WAN virtual do Azure usando o Azure Monitor insights.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: d14ae86d41caec3c1bd897c2c81bee748dcc312c
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050877"
---
# <a name="azure-monitor-insights-for-virtual-wan"></a>Azure Monitor insights para WAN virtual

[Azure monitor insights](../azure-monitor/insights/network-insights-overview.md) para a WAN virtual do Azure fornece aos usuários e operadores a capacidade de exibir o estado e o status de uma WAN virtual, apresentada por meio de um mapa topológica autodescoberto. As sobreposições de estado e status do recurso no mapa fornecem uma exibição de instantâneo da integridade geral da WAN virtual. Você pode navegar pelos recursos no mapa por meio de acesso de um clique às páginas de configuração de recurso do portal de WAN virtual.

As métricas de nível de recurso da WAN virtual são coletadas e apresentadas por meio de uma pasta de trabalho de métricas de WAN virtual predefinida. A pasta de trabalho mostra as métricas nos níveis de WAN virtual, Hub, gateway e conexão. Este artigo explica as etapas para usar Azure Monitor informações para a WAN virtual para exibir sua topologia e métricas de WAN virtual em um único local.

> [!NOTE]
> A opção de menu **insights** está no portal de WAN virtual em **monitoramento**. Você também pode acessar a topologia de WAN virtual e a pasta de trabalho de métricas usando Azure Monitor para redes. Para obter mais informações, consulte [Azure monitor para redes](../azure-monitor/insights/network-insights-overview.md). 
>

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste artigo, você precisa ter uma WAN virtual com um ou mais hubs. Para criar uma WAN virtual e um Hub, siga as etapas nestes artigos:

* [Criação de uma WAN virtual](virtual-wan-site-to-site-portal.md#openvwan)
* [Criação de um hub](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>Exibir topologia VWAN

Vá para **portal do Azure**  >  **Wan virtual**. No menu **monitorar** no painel esquerdo, selecione **insights (versão prévia)**. A exibição **insights** é exibida. Ele mostra o mapa de dependência de WAN virtual e a mini pasta de trabalho de **métricas** de alto nível.

**Figura 1: monitorar o menu do > insights**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="Captura de tela que mostra a exibição das informações (visualização)." lightbox="./media/azure-monitor-insights/monitor-menu.png":::

Na exibição do **insights** , você pode exibir os recursos da WAN virtual descoberta automática. Esses recursos incluem hubs, gateways, firewalls, conexões e redes virtuais spoke, NVAs de terceiros e ramificações em uma WAN virtual de ponta a ponta. Para obter um exemplo, consulte a **Figura 2**.

O estado do recurso e o status são codificados por cor e sobrepostos nos ícones de recurso no mapa. As métricas de WAN virtual de alto nível, como capacidades de Hub e utilização de gateway, aparecem no lado direito da janela em uma mini pasta de trabalho.

**Figura 2: exibição do insights**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="Captura de tela que mostra a exibição de informações." lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>Exibição de dependência

A exibição de **dependência** para WAN virtual ajuda a visualizar a exibição interconectada de todos os recursos de WAN virtual amplamente organizados em uma arquitetura de Hub e spoke.

**Figura 3: exibição de dependência VWAN**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="Captura de tela que mostra a exibição de dependência." lightbox="./media/azure-monitor-insights/dependency-map.png":::

O mapa da exibição de **dependência** exibe os seguintes recursos como um grafo conectado:

* Hubs de WAN virtual em várias regiões do Azure.
* Redes virtuais spoke conectadas diretamente ao Hub.
* Sites de filial e de Azure ExpressRoute e P2S usuários que estão conectados a cada Hub por meio de suas respectivas conexões de ExpressRoute, S2S e P2S e gateways de rede virtual.
* Firewalls do Azure (incluindo proxies de firewall de terceiros) implantados em um Hub (Hub protegido).
* NVAs de terceiros (dispositivos de rede virtual) que são implantados em redes virtuais spoke.

O mapa de dependências também exibe redes virtuais conectadas indiretamente (redes virtuais emparelhadas com redes virtuais de spoke de WAN virtual).

O mapa de dependências permite uma fácil navegação para as definições de configuração de cada recurso. Por exemplo, você pode passar o mouse sobre o recurso de Hub para exibir a configuração básica de recursos, como a região do Hub e o prefixo do Hub. Clique com o botão direito do mouse para acessar a página de portal do Azure do recurso de Hub.

**Figura 4: navegar para informações específicas do recurso**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="Captura de tela que mostra como navegar para informações específicas do recurso.":::

A barra de pesquisa e filtro na exibição de **dependência** fornece uma maneira fácil de Pesquisar no grafo. Vários filtros fornecem ajuda para restringir a pesquisa a um caminho e estado específicos.

**Figura 5: pesquisa e filtragem**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="Captura de tela que mostra a barra de pesquisa e de filtro." lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>Métricas detalhadas

Você pode selecionar **Exibir métricas detalhadas** para acessar a página de **métricas** detalhadas. A página **métricas** é um painel pré-configurado com guias separadas. Essas guias fornecem informações sobre a capacidade, o desempenho e a utilização do recurso de WAN virtual no nível do Hub e do ponto de WAN virtual e no nível de conexões individuais.

**Figura 6: painel de métricas detalhado**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="Captura de tela que mostra o painel de métricas detalhado." lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>Próximas etapas

* Para saber mais, confira [métricas em Azure monitor](../azure-monitor/platform/data-platform-metrics.md).
* Para obter uma descrição completa de todas as métricas de WAN virtual, consulte [métricas e logs de WAN virtual](logs-metrics.md).
