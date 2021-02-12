---
title: Visualizando dados do Azure Monitor | Microsoft Docs
description: Fornece um resumo dos métodos disponíveis para visualizar os dados de métrica e log armazenados no Azure Monitor.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/25/2021
ms.openlocfilehash: bc59c639856de6a5ce5adda1f78266eb7e4d5dd3
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881224"
---
# <a name="visualizing-data-from-azure-monitor"></a>Visualizando dados do Monitor do Azure
Este artigo fornece um resumo dos métodos disponíveis para visualizar os dados de log e métrica armazenados no Azure Monitor.

Visualizações como tabelas e gráficos podem ajudá-lo a analisar seus dados de monitoramento para detalhar problemas e identificar padrões. Dependendo da ferramenta que você usa, você também pode ter a opção de compartilhar visualizações com outros usuários dentro e fora de sua organização.

## <a name="workbooks"></a>Pastas de trabalho
[As pastas de trabalho](./platform/workbooks-overview.md) são documentos interativos que fornecem informações detalhadas sobre seus dados, investigação e colaboração dentro da equipe. Exemplos específicos em que as pastas de trabalho são úteis são guias de solução de problemas e postmortem de incidentes.

![O diagrama mostra capturas de tela de várias páginas de uma pasta de trabalho, incluindo a análise de exibições de página, uso e tempo gasto na página.](media/visualizations/workbook.png)

### <a name="advantages"></a>Vantagens
- Suporta métricas e logs.
- Suporta parâmetros que permitem relatórios interativos em que a seleção de um elemento em uma tabela atualiza dinamicamente gráficos e visualizações associadas.
- Semelhante a documentos de fluxo.
- Opção para pastas de trabalho pessoais ou compartilhadas.
- Experiência de criação fácil e colaborativa amigável.
- Modelos dão suporte a Galeria de modelos pública baseada no GitHub.

### <a name="limitations"></a>Limitações
- Nenhuma atualização automática.
- Nenhum layout denso, como painéis, o que torna as pastas de trabalho menos úteis como um único painel de vidro. A finalidade mais para fornecer informações mais aprofundadas.


## <a name="azure-dashboards"></a>Painéis do Azure
Os [painéis do Azure](../azure-portal/azure-portal-dashboards.md) são a principal tecnologia de painéis do Azure. Eles são particularmente úteis para fornecer um único painel de controle sobre sua infraestrutura e serviços do Azure, permitindo que você identifique rapidamente problemas importantes.

![Captura de tela mostra um exemplo de um painel do Azure com informações personalizáveis.](media/visualizations/dashboard.png)

Aqui está uma explicação em vídeo sobre como criar painéis.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AslH]

### <a name="advantages"></a>Vantagens
- Integração profunda no Azure. As visualizações podem ser fixadas em painéis de várias páginas do Azure, incluindo [Metrics Explorer](platform/metrics-charts.md), [log Analytics](log-query/log-analytics-overview.md)e [Application insights](app/app-insights-overview.md).
- Suporta métricas e logs.
- Combine dados de várias fontes, incluindo saída de [Metrics Explorer](platform/metrics-charts.md), [consultas de log](log-query/log-query-overview.md)e [mapas](app/app-map.md) e disponibilidade no [Application insights](app/app-insights-overview.md).
- Opção para os painéis de pessoais ou compartilhadas. Integrado com o Azure [RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md).
- Atualização automática. A atualização de métricas depende do intervalo de tempo com um mínimo de cinco minutos. Os logs são atualizados a cada hora, com uma opção de atualização manual sob demanda clicando no ícone “atualizar” em uma determinada visualização ou atualizando o painel completo.
- Painéis de métricas parametrizadas com carimbo de data/hora e parâmetros personalizados.
- Opções de layout flexível.
- Modo de tela inteira.


### <a name="limitations"></a>Limitações
- Controle limitado sobre as visualizações de log sem suporte para tabelas de dados. O número total de séries de dados é limitado a 50 com mais séries de dados agrupadas em _outro_ Bucket.
- Sem suporte para parâmetros personalizados em gráficos de log.
- Os gráficos de log estão limitados aos últimos 30 dias.
- Os gráficos de log só podem ser fixados em painéis compartilhados.
- Nenhuma interatividade com dados do painel.
- Detalhamento contextual limitado.


## <a name="power-bi"></a>Power BI
[O Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) é particularmente útil para criar painéis e relatórios centrados nos negócios, bem como relatórios que analisam tendências de KPI de longo prazo. Você pode [importar os resultados de uma consulta de log](platform/powerbi.md) para um conjunto de dados do Power BI para aproveitar seus recursos, como combinar dados de diferentes fontes e compartilhar relatórios na Web e em dispositivos móveis.

![Power BI](media/visualizations/power-bi.png)

### <a name="advantages"></a>Vantagens
- Visualizações ricas.
- Interatividade extensa, incluindo zoom e filtragem cruzada.
- Fácil de compartilhar em toda a sua organização.
- Integração com outros dados de várias fontes de dados.
- Melhor desempenho com resultados armazenados em cache em um cubo.


### <a name="limitations"></a>Limitações
- Suporta logs, mas não métricas.
- Nenhuma integração do Azure. Não é possível gerenciar painéis e modelos por meio do Azure Resource Manager.
- Os resultados da consulta precisam ser importados para o modelo do Power BI para serem configurados. Limitação no tamanho do resultado e atualização.
- Atualização de dados limitada de oito vezes por dia.


## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) é uma plataforma aberta que se destaca em painéis operacionais. É particularmente útil para detectar, isolar e triar incidentes operacionais. Você pode adicionar [o plug-in da fonte de dados do Grafana Azure Monitor](platform/grafana-plugin.md) à sua assinatura do Azure para que ela visualize seus dados de métricas do Azure.

![Captura de tela mostra visualizações Grafana.](media/visualizations/grafana.png)

### <a name="advantages"></a>Vantagens
- Visualizações ricas.
- Ecossistema rico de origens de dados.
- Dados interatividade incluindo ampliar.
- Dá suporte a parâmetros.

### <a name="limitations"></a>Limitações
- Nenhuma integração do Azure. Não é possível gerenciar painéis e modelos por meio do Azure Resource Manager.
- Custo para suportar infraestrutura adicional da Grafana ou custo adicional para a Grafana Cloud.


## <a name="build-your-own-custom-application"></a>Crie seu próprio aplicativo personalizado
Você pode acessar os dados nos dados de log e métrica do Azure Monitor por meio de sua API usando qualquer cliente REST, o que permite que você crie seus próprios sites e aplicativos personalizados.

### <a name="advantages"></a>Vantagens
- Flexibilidade total na interface do usuário, visualização, interatividade e recursos.
- Combine métricas e registre dados com outras fontes de dados.

### <a name="disadvantages"></a>Desvantagens
- Esforço significativo de engenharia necessários.


## <a name="azure-monitor-views"></a>Modos de exibição do Monitor do Azure

> [!IMPORTANT]
> Exibições estão no processo de serem preteridas. Consulte o [guia de transição do designer do Azure Monitor View para pastas de trabalho](platform/view-designer-conversion-overview.md) para obter diretrizes sobre como converter exibições em pastas de trabalho.

As [exibições no Azure Monitor](platform/view-designer.md) permitem que você crie visualizações personalizadas com os dados de log. Elas são usadas pelas [soluções de monitoramento](insights/solutions.md) para apresentar os dados coletados.


![Captura de tela mostra um bloco de solução de monitoramento de contêiner e a exibição detalhada de Azure Monitor que é aberta quando você o seleciona.](media/visualizations/view.png)

### <a name="advantages"></a>Vantagens
- Visualizações ricas para dados de log.
- Exporte e importe visualizações para transferi-las para outros grupos de recursos e assinaturas.
- Integra-se no modelo de gerenciamento do Azure Monitor com workspaces e soluções de monitoramento.
- [Filtros](platform/view-designer-filters.md) para parâmetros personalizados.
- Interativo, suporta drill-in de vários níveis (veja as brocas em outra visão)

### <a name="limitations"></a>Limitações
- Suporta logs, mas não métricas.
- Não há exibições pessoais. Disponível para todos os usuários com acesso ao workspace.
- Nenhuma atualização automática.
- Opções de layout limitado.
- Sem suporte para consulta em vários workspaces ou em aplicativos do Application Insights.
- As consultas são limitadas no tamanho da resposta para 8 MB e no tempo de execução da consulta de 110 segundos.

## <a name="next-steps"></a>Próximas etapas
- Aprenda sobre os [dados coletados pelo Azure Monitor](platform/data-platform.md).
- Saiba mais sobre os [painéis do Azure](../azure-portal/azure-portal-dashboards.md).
- Saiba mais sobre o [Metrics Explorer](platform/metrics-getting-started.md)
- Saiba mais sobre [Pastas de trabalho](./platform/workbooks-overview.md).
- Saiba mais sobre [importar dados de log no Power BI](./platform/powerbi.md).
- Saiba mais sobre o [plug-in de fonte de dados do Azure Monitor do Grafana](./platform/grafana-plugin.md).
- Saiba mais sobre [modos de exibição no Azure Monitor](platform/view-designer.md).

