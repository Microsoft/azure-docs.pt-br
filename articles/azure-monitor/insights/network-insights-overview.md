---
title: Azure Monitor para Redes
description: Uma visão geral de Azure Monitor para redes, que fornece uma visão abrangente da integridade e das métricas para todos os recursos de rede implantados sem nenhuma configuração.
ms.subservice: ''
ms.topic: conceptual
author: KumudD
ms.author: kumud
ms.date: 11/25/2020
ms.openlocfilehash: 4a273170a1f3842a9a11a61d41d4150eb98eace4
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734133"
---
# <a name="azure-monitor-for-networks"></a>Azure Monitor para Redes

O Azure Monitor para redes fornece uma visão abrangente da [integridade](../../service-health/resource-health-checks-resource-types.md) e das [métricas](../platform/metrics-supported.md) para todos os recursos de rede implantados, sem a necessidade de nenhuma configuração. Ele também fornece acesso a recursos de monitoramento de rede, como o [Monitor de conexão](../../network-watcher/connection-monitor-overview.md), o [log de fluxo para NSGs (grupos de segurança de rede)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)e [análise de tráfego](../../network-watcher/traffic-analytics.md). E fornece outros recursos de [diagnóstico](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) de rede.

Azure Monitor para redes é estruturada com base nesses principais componentes do monitoramento:
- [Métricas e integridade da rede](#networkhealth)
- [Conectividade](#connectivity)
- [Tráfego](#traffic)
- [Kit de ferramentas de diagnóstico](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>Métricas e integridade da rede

A página **visão geral** do Azure monitor for Networks fornece uma maneira fácil de visualizar o inventário de seus recursos de rede, juntamente com a integridade e os alertas de recursos. Ele é dividido em quatro áreas funcionais principais: pesquisa e filtragem, integridade de recursos e métricas, alertas e exibição de dependência.

[![Captura de tela mostrando a página Visão geral](media/network-insights-overview/overview.png)](media/network-insights-overview/overview.png#lightbox)

### <a name="search-and-filtering"></a>Pesquisa e filtragem
Você pode personalizar a exibição de integridade e alertas de recursos usando filtros como **assinatura**, **grupo de recursos** e **tipo**.

Você pode usar a caixa Pesquisar para procurar recursos e seus recursos associados. Por exemplo, um IP público é associado a um gateway de aplicativo. Uma pesquisa pelo nome DNS do IP público retornará o IP público e o gateway de aplicativo associado:

[![Captura de tela que mostra Azure Monitor para os resultados da pesquisa de redes.](media/network-insights-overview/search.png)](media/network-insights-overview/search.png#lightbox)


### <a name="resource-health-and-metrics"></a>Integridade e métrica de recursos
No exemplo a seguir, cada bloco representa um tipo de recurso. O bloco exibe o número de instâncias desse tipo de recurso implantadas em todas as assinaturas selecionadas. Ele também exibe o status de integridade do recurso. Neste exemplo, há 105 conexões de ER e VPN implantadas. 103 estão íntegros e 2 estão indisponíveis.

![Captura de tela que mostra a integridade do recurso e as métricas em Azure Monitor para redes.](media/network-insights-overview/resource-health.png)

Se você selecionar as conexões de ER e VPN indisponíveis, verá uma exibição de métrica: 

![Captura de tela que mostra a exibição de métrica em Azure Monitor para redes.](media/network-insights-overview/metric-view.png)

Você pode selecionar qualquer item no modo de exibição de grade. Selecione o ícone na coluna **integridade** para obter a integridade do recurso para essa conexão. Selecione o valor na coluna **alerta** para ir para a página de alertas e métricas para a conexão. 

### <a name="alerts"></a>Alertas
A caixa de **alerta** no lado direito da página fornece uma exibição de todos os alertas gerados para os recursos selecionados em todas as assinaturas. Selecione o alerta conta para ir para uma página de alertas detalhada.

### <a name="dependency-view"></a>Exibição de dependência
A exibição de dependência ajuda a visualizar como um recurso é configurado. A exibição de dependência está disponível atualmente para Aplicativo Azure gateway, WAN virtual do Azure e Azure Load Balancer. Por exemplo, para o gateway de aplicativo, você pode acessar a exibição de dependência selecionando o nome do recurso do gateway de aplicativo na exibição de grade de métricas. Você pode fazer a mesma coisa para WAN virtual e Load Balancer.

![Sreenshot que mostra a exibição do gateway de aplicativo em Azure Monitor para redes.](media/network-insights-overview/application-gateway.png)

A exibição de dependência do gateway de aplicativo fornece uma visão simplificada de como os IPs de front-end são conectados aos ouvintes, às regras e ao pool de back-end. As linhas de conexão são codificadas por cores e fornecem detalhes adicionais com base na integridade do pool de back-end. A exibição também fornece uma exibição detalhada das métricas e métricas do gateway de aplicativo para todos os pools de back-end relacionados, como o conjunto de dimensionamento de máquinas virtuais e as instâncias de VM.

[![Captura de tela que mostra a exibição de dependência em Azure Monitor para redes.](media/network-insights-overview/dependency-view.png)](media/network-insights-overview/dependency-view.png#lightbox)

O grafo de dependência fornece navegação fácil para as definições de configuração. Clique com o botão direito do mouse em um pool de back-end para acessar outras informações. Por exemplo, se o pool de back-end for uma VM, você poderá acessar diretamente as informações da VM e a solução de problemas de conexão do observador de rede do Azure para identificar problemas de conectividade:

![Captura de tela que mostra o menu de exibição de dependência em Azure Monitor para redes.](media/network-insights-overview/dependency-view-menu.png)

A barra de pesquisa e filtro na exibição de dependência fornece uma maneira fácil de Pesquisar no grafo. Por exemplo, se você Pesquisar **AppGWTestRule** no exemplo anterior, a exibição reduzirá verticalmente para todos os nós conectados por meio de AppGWTestRule:

![Captura de tela que mostra um exemplo de uma pesquisa em Azure Monitor para redes.](media/network-insights-overview/search-example.png)

Vários filtros ajudam você a reduzir verticalmente para um caminho e estado específicos. Por exemplo, selecione somente não **íntegro** na lista **status de integridade** para mostrar todas as bordas para as quais o estado não está íntegro.

Selecione **Exibir métricas detalhadas** para abrir uma pasta de trabalho pré-configurada que fornece métricas detalhadas para o gateway de aplicativo, todos os recursos de pool de back-end e IPS de front-end. 

## <a name="connectivity"></a><a name="connectivity"></a>Conectividade

A guia **conectividade** fornece uma maneira fácil de Visualizar todos os testes configurados por meio do [Monitor de conexão](../../network-watcher/connection-monitor-overview.md) e do monitor de conexão (clássico) para o conjunto de assinaturas selecionado.

![Captura de tela que mostra a guia conectividade em Azure Monitor para redes.](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

Os testes são agrupados por blocos de **origens** e **destinos** e exibem o status de acessibilidade de cada teste. As configurações acessíveis fornecem acesso fácil às configurações para seus critérios de acessibilidade, com base nas verificações com falha (%) e RTT (MS). Depois de definir os valores, o status de cada atualização de teste com base nos critérios de seleção.

[![Captura de tela que mostra os testes de conectividade em Azure Monitor para redes.](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png#lightbox)

Você pode selecionar qualquer bloco de origem ou de destino para abrir uma exibição de métrica:

[![Captura de tela que mostra as métricas de conectividade em Azure Monitor para redes.](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png#lightbox)


Você pode selecionar qualquer item no modo de exibição de grade. Selecione o ícone na coluna **acessibilidade** para acessar a página do portal do monitor de conexão e exibir a topologia de salto a salto e a conectividade que afeta os problemas identificados. Selecione o valor na coluna **alerta** para ir para alertas. Selecione os grafos nas colunas **percentual de falhas** e **tempo de viagem de ida e volta (MS)** para ir para a página métricas do monitor de conexão selecionado.

A caixa de **alerta** no lado direito da página fornece uma exibição de todos os alertas gerados para os testes de conectividade configurados em todas as assinaturas. Selecione o alerta conta para ir para uma página de alertas detalhada.

## <a name="traffic"></a><a name="traffic"></a>Tráfego
A guia **tráfego** fornece acesso a todos os NSGs configurados para [logs de fluxo do NSG](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) e [análise de tráfego](../../network-watcher/traffic-analytics.md) para o conjunto selecionado de assinaturas, agrupadas por local. A funcionalidade de pesquisa fornecida nesta guia permite que você identifique o NSGs configurado para o endereço IP pesquisado. Você pode procurar qualquer endereço IP em seu ambiente. O modo de exibição regional xadrez exibirá todos os NSGs juntamente com os logs de fluxo NSG e o status de configuração Análise de Tráfego.

[![Captura de tela que mostra a guia tráfego em Azure Monitor para redes.](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png#lightbox)

Se você selecionar qualquer bloco de região, um modo de exibição de grade será exibido. A grade fornece logs de fluxo NSG e Análise de Tráfego em uma exibição fácil de ler e configurar:  

[![Captura de tela que mostra a exibição de região de tráfego em Azure Monitor para redes.](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png#lightbox)

Você pode selecionar qualquer item no modo de exibição de grade. Selecione o ícone na coluna **status da configuração do log** para editar o log de fluxo do NSG e análise de tráfego configuração. Selecione o valor na coluna **alerta** para ir para os alertas de tráfego configurados para o NSG selecionado. Da mesma forma, você pode ir para a exibição Análise de Tráfego selecionando o **espaço de trabalho análise de tráfego**.  

A caixa de **alerta** no lado direito da página fornece uma exibição de todos os análise de tráfego alertas baseados em espaço de trabalho em todas as assinaturas. Selecione o alerta conta para ir para uma página de alertas detalhada.

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> Kit de ferramentas de diagnóstico
O Diagnostic Toolkit fornece acesso a todos os recursos de diagnóstico disponíveis para a solução de problemas da rede. Você pode usar essa lista suspensa para acessar recursos como captura de [pacotes](../../network-watcher/network-watcher-packet-capture-overview.md), [solução de problemas de VPN](../../network-watcher/network-watcher-troubleshoot-overview.md), solução de problemas de [conexão](../../network-watcher/network-watcher-connectivity-overview.md), [próximo salto](../../network-watcher/network-watcher-next-hop-overview.md)e [verificação de fluxo de IP](../../network-watcher/network-watcher-ip-flow-verify-overview.md):

![Captura de tela que mostra a guia kit de ferramentas de diagnóstico.](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="troubleshooting"></a>Solução de problemas 

Para obter diretrizes gerais de solução de problemas, consulte o artigo de solução de [problemas](troubleshoot-workbooks.md)de informações baseadas na pasta de trabalho dedicada.

Esta seção o ajudará a diagnosticar e solucionar alguns problemas comuns que você pode encontrar ao usar o Azure Monitor para redes. 

### <a name="how-do-i-resolve-performance-problems-or-failures"></a>Como fazer resolver problemas ou falhas de desempenho?

Para saber mais sobre como solucionar problemas relacionados à rede que você identifica com o Azure Monitor para redes, consulte a documentação de solução de problemas para o recurso que está funcionando incorretamente. 

Aqui estão alguns links para artigos de solução de problemas para serviços usados com frequência. Para obter mais artigos de solução de problemas sobre esses serviços, consulte os outros artigos na seção de solução de problemas do Sumário do serviço.
* [Rede Virtual do Azure](../../virtual-network/virtual-network-troubleshoot-peering-issues.md)
* [Gateway de Aplicativo do Azure](../../application-gateway/create-gateway-internal-load-balancer-app-service-environment.md)
* [Gateway de VPN do Azure](../../vpn-gateway/vpn-gateway-troubleshoot.md)
* [Azure ExpressRoute](../../expressroute/expressroute-troubleshooting-expressroute-overview.md) 
* [Azure Load Balancer](../../load-balancer/load-balancer-troubleshoot.md) 

### <a name="why-dont-i-see-the-resources-for-all-the-subscriptions-ive-selected"></a>Por que não vejo os recursos para todas as assinaturas que selecionei?

Azure Monitor para redes podem mostrar recursos para apenas cinco assinaturas de cada vez. 

### <a name="how-do-i-make-changes-or-add-visualizations-to-azure-monitor-for-networks"></a>Como fazer fazer alterações ou adicionar visualizações a Azure Monitor para redes?

Para fazer alterações, selecione o **modo de edição** para modificar a pasta de trabalho. Você pode salvar suas alterações como uma nova pasta de trabalho vinculada a uma assinatura designada e a um grupo de recursos.

### <a name="whats-the-time-grain-after-i-pin-any-part-of-the-workbooks"></a>Qual é o intervalo de tempo depois de fixar qualquer parte das pastas de trabalho?

O Azure Monitor para redes usa o intervalo de tempo **automático** , portanto, o detalhamento de tempo é baseado no período de tempo selecionado.

### <a name="whats-the-time-range-when-any-part-of-a-workbook-is-pinned"></a>Qual é o intervalo de tempo quando qualquer parte de uma pasta de trabalho é fixada?

O intervalo de tempo depende das configurações do painel.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-azure-monitor-for-networks"></a>E se eu quiser ver outros dados ou fazer minhas próprias visualizações? Como fazer alterações em Azure Monitor para redes?

Você pode editar a pasta de trabalho exibida em qualquer exibição de métrica de painel lateral ou detalhada usando o modo de edição. Você pode salvar suas alterações como uma nova pasta de trabalho.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o monitoramento de rede: [o que é o observador de rede do Azure?](../../network-watcher/network-watcher-monitoring-overview.md)
- Conheça os cenários em que as pastas de trabalho foram projetadas para dar suporte, como criar relatórios e personalizar relatórios existentes e muito mais: [criar relatórios interativos com pastas de trabalho do Azure monitor](../platform/workbooks-overview.md)