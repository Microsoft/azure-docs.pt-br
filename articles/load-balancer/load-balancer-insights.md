---
title: Informações para Azure Load Balancer
description: Use as informações do balanceador de carga para obter uma rápida localização de falhas e decisões de design informadas
services: load-balancer
documentationcenter: na
author: erichrt
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: errobin
ms.openlocfilehash: 190a9e431dedfb0f0fe6077e903174f253f5c773
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589103"
---
# <a name="using-insights-to-monitor-and-configure-your-azure-load-balancer"></a>Usando informações para monitorar e configurar seu Azure Load Balancer

Por meio de Azure Monitor para redes, você recebe visualizações de dependência funcional e um painel de métricas pré-configurado para seus balanceadores de carga. Esses visuais ajudam a capacitar você a tomar decisões de design informadas e localizar rapidamente, diagnosticar e resolver quaisquer falhas.

>[!NOTE] 
>Observe que esse recurso está em visualização e a exibição de dependência funcional e o painel pré-configurado podem mudar para melhorar essa experiência

>[!IMPORTANT]
>O Standard Load Balancer é necessário para ver as métricas do namespace Load Balancer no painel de métricas pré-configurado. Você ainda poderá ver as métricas da VM, do conjunto de dimensionamento de máquinas virtuais e dos namespaces do monitor de conexão, no entanto, recomendamos [atualizar para Standard](./upgrade-basic-standard.md) para qualquer carga de trabalho de produção para aproveitar o conjunto robusto de métricas de Load Balancer.

## <a name="functional-dependency-view"></a>Exibição de dependência funcional

A exibição de dependência funcional permitirá que você Picturee até mesmo as configurações do balanceador de carga mais complexas. Com os comentários visuais sobre a configuração de Load Balancer mais recente, você pode fazer atualizações e, ao mesmo tempo, manter sua configuração em mente.

Você pode acessar essa exibição visitando a folha de informações de seu recurso de Load Balancer no Azure.

:::image type="content" source="./media/load-balancer-insights/load-balancer-functional-dependency-visual.png" alt-text="Depecition da exibição de dependência funcional. O front-end do balanceador de carga pode ser visto se conectando aos membros do pool de back-end por meio das regras configuradas. Por Standard Load Balancer, as linhas das regras de balanceamento de carga para as instâncias do pool de back-end são codificadas por cor com base no status da investigação de integridade." border="true":::

Para balanceadores de carga padrão, seus recursos de pool de back-end são codificados por cores com o status de investigação de integridade, indicando a disponibilidade atual do pool de back-end para atender ao tráfego. Junto com a topologia acima, você verá um grafo pontual de status de integridade, dando uma visão instantânea da integridade do seu aplicativo.

## <a name="metrics-dashboard"></a>Dashboard de métricas

Na folha de informações do seu Load Balancer, você pode selecionar métricas mais detalhadas para exibir uma pasta de trabalho pré-configurada do [Azure monitor](../azure-monitor/visualize/workbooks-overview.md) contendo elementos visuais de métricas relevantes para aspectos específicos do seu Load Balancer. Este painel mostrará o status da Load Balancer e os links para a documentação relevante na parte superior da página.

Inicialmente, você verá a guia Visão geral. Você pode navegar pelas guias disponíveis, cada uma delas contendo visuais relevantes para um aspecto específico de seu Load Balancer. As diretrizes explícitas para cada um estão disponíveis no painel na parte inferior de cada guia.

As guias de painel disponíveis atualmente são:
* Visão geral
* Disponibilidade de Front-end e Back-end
* Taxa de Transferência de Dados
* Distribuição de Fluxo
* Monitores de Conexão
* Definições de métrica 

### <a name="overview-tab"></a>Guia Visão Geral
A guia Visão geral contém uma grade pesquisável com a disponibilidade geral do caminho de dados e o status da investigação de integridade para cada um dos IPs de front-end anexados à sua Load Balancer. Essas métricas indicam se o IP de front-end está respondendo e se as instâncias de computação em seu pool de back-end são individualmente responsivas a conexões de entrada.

Você também pode exibir a taxa de transferência de dados geral para cada IP de front-end nesta página para ter uma noção de se você está produzindo e recebendo os níveis de tráfego esperados. As diretrizes na parte inferior da página direcionarão você para a guia apropriada, caso você veja quaisquer valores irregulares.

### <a name="frontend-and-backend-availability-tab"></a>Guia de disponibilidade de front-end e backend
As guias de disponibilidade de front-end e backend mostram a taxa de transferência de caminho de dados e as métricas de status de investigação de integridade apresentadas em algumas exibições úteis. O primeiro gráfico mostra o valor agregado para que você possa determinar se há um problema. O restante dos grafos mostra essas métricas divididas por várias dimensões para que você possa solucionar problemas e identificar as fontes de qualquer problema de disponibilidade de entrada.

Um fluxo de trabalho para exibir esses gráficos é fornecido na parte inferior da página com causas comuns para vários sintomas. 

### <a name="data-throughput-tab"></a>Guia taxa de transferência de dados
A guia taxa de transferência de dados permite que você examine sua taxa de transferência de entrada e saída para identificar se os padrões de tráfego são os esperados. Ele mostrará a taxa de transferência de dados de entrada e saída dividida por IP de front-end e porta de front-end para que você possa identificar se os serviços que você está executando estão executando individualmente.

### <a name="flow-distribution"></a>Distribuição de Fluxo
A guia distribuição de fluxo ajudará você a visualizar e gerenciar o número de fluxos que suas instâncias de back-end estão recebendo e produzindo. Ele mostra a taxa de criação de fluxo e a contagem de fluxo para tráfego de entrada e saída, bem como o tráfego de rede que cada VM e instância do conjunto de dimensionamento de máquinas virtuais está recebendo. 

Esses modos de exibição podem fornecer comentários sobre se sua configuração de Load Balancer ou os padrões de tráfego estão levando a tráfego desbalanceado. Por exemplo, se você tiver a afinidade de sessão configurada e um único cliente estiver fazendo um número desproporcional de solicitações. Ele também informará se você está se aproximando do [limite de fluxo por VM](../virtual-network/virtual-machine-network-throughput.md#flow-limits-and-active-connections-recommendations) para o tamanho do seu computador.

### <a name="connection-monitors"></a>Monitores de Conexão
A guia monitores de conexão mostrará a latência de ida e volta em um mapa global para todos os [monitores de conexão](../network-watcher/connection-monitor.md)  que você configurou. Esses elementos visuais fornecem informações úteis para serviços com requisitos estritos de latência. Para atender aos seus requisitos, talvez seja necessário adicionar implantações regionais adicionais ou mover para um modelo de [balanceamento de carga entre regiões](./cross-region-overview.md)

### <a name="metric-definitions"></a>Definições de métrica
A guia definições de métrica contém todas as informações mostradas no [artigo métricas multidimensionais](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics).

## <a name="next-steps"></a>Próximas etapas
* Examine o painel e forneça comentários usando o link abaixo se houver algo que possa ser melhorado
* [Examine a documentação de métricas para garantir que você entenda como cada métrica é calculada](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics)
* [Criar monitores de conexão para seu Load Balancer](../network-watcher/connection-monitor.md)
* [Crie suas próprias pastas de trabalho](../azure-monitor/visualize/workbooks-overview.md), você pode fazer a inspiração clicando no botão Editar em seu painel de métricas detalhado
