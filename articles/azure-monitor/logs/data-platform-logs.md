---
title: Logs do Azure Monitor
description: Descreve Azure Monitor logs que são usados para análise avançada de dados de monitoramento.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/22/2020
ms.author: bwren
ms.openlocfilehash: 6cb85bc1da7d98d3e2ad46229588a91d971ce014
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567282"
---
# <a name="azure-monitor-logs-overview"></a>Visão geral dos Logs do Azure Monitor
Os logs de Azure Monitor são um recurso de Azure Monitor que coleta e organiza dados de log e de desempenho de [recursos monitorados](../monitor-reference.md). Dados de fontes diferentes, como [logs de plataforma](../essentials/platform-logs-overview.md) de serviços do Azure, dados de log e de desempenho de [agentes de máquinas virtuais](../agents/agents-overview.md)e dados de uso e desempenho de [aplicativos](../app/app-insights-overview.md) podem ser consolidados em um único espaço de trabalho para que possam ser analisados juntos usando uma linguagem de consulta sofisticada capaz de analisar rapidamente milhões de registros. Você pode executar uma consulta simples que recupera apenas um conjunto específico de registros ou executar uma análise de dados sofisticada para identificar padrões críticos em seus dados de monitoramento. Trabalhar com consultas de log e seus resultados interativamente usando Log Analytics, usá-las em uma regra de alerta para ser notificada proativamente sobre problemas ou visualizar seus resultados em uma pasta de trabalho ou painel.

> [!NOTE]
> Azure Monitor logs é uma metade da plataforma de dados que oferece suporte a Azure Monitor. A outra é [Azure monitor métricas](../essentials/data-platform-metrics.md) que armazena dados numéricos em um banco de dado de série temporal. Isso torna esses dados mais leves do que os dados em logs de Azure Monitor e capazes de dar suporte a cenários quase em tempo real, tornando-os particularmente úteis para alertas e detecção rápida de problemas. No entanto, as métricas só podem armazenar dados numéricos em uma determinada estrutura, enquanto os logs podem armazenar uma variedade de tipos de dados diferentes, cada um com sua própria estrutura. Você também pode executar análise complexa nos dados de logs usando consultas de log que não podem ser usadas para análise de dados de métricas.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>O que você pode fazer com os Logs do Azure Monitor?
A tabela a seguir descreve algumas das diferentes maneiras que você pode usar logs no Azure Monitor:

|  | Description |
|:---|:---|
| **Analisar** | Use [log Analytics](./log-analytics-tutorial.md) no portal do Azure para gravar [consultas de log](./log-query-overview.md) e analisar interativamente os dados de log usando um mecanismo de análise poderoso |
| **Alerta** | Configurar uma [regra de alerta de log](../alerts/alerts-log.md) que envia uma notificação ou realiza [ação automatizada](../alerts/action-groups.md) quando os resultados da consulta correspondem a um resultado específico. |
| **Visualizar** | Fixe os resultados de consultas renderizados como tabelas ou gráficos em um [painel do Azure](../../azure-portal/azure-portal-dashboards.md).<br>Crie uma [pasta de trabalho](../visualize/workbooks-overview.md) para combinar com vários conjuntos de dados em um relatório interativo. <br>Exportar os resultados de uma consulta para o [Power BI](../visualize/powerbi.md) a fim de usar visualizações diferentes e compartilhar com usuários fora do Azure.<br>Exporte os resultados de uma consulta para o [Grafana](../visualize/grafana-plugin.md) a fim de aproveitar o painel da ferramenta e combinar com outras fontes de dados.|
| **Insights** | Suporte a [informações](../monitor-reference.md#insights-and-core-solutions) que fornecem uma experiência de monitoramento Personalizada para aplicativos e serviços específicos.  |
| **Recuperar** | Acesse os resultados da consulta de log em uma linha de comando usando a [CLI do Azure](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Acesse os resultados da consulta de log em uma linha de comando usando os [cmdlets do PowerShell](/powershell/module/az.operationalinsights).<br>Acesse os resultados da consulta de log em um aplicativo personalizado usando a [API REST](https://dev.loganalytics.io/). |
| **Exportar** | Configure a [exportação automatizada de dados de log](./logs-data-export.md) para a conta de armazenamento do Azure ou hubs de eventos do Azure.<br>Crie um fluxo de trabalho para recuperar dados de log e copie-os para uma localização externa usando os [Aplicativos Lógicos](./logicapp-flow-connector.md). |

![Visão geral de logs](media/data-platform-logs/logs-overview.png)


## <a name="data-collection"></a>Coleta de dados
Depois de criar um espaço de trabalho Log Analytics, você deve configurar fontes diferentes para enviar seus dados. Nenhum dado é coletado automaticamente. Essa configuração será diferente dependendo da fonte de dados. Por exemplo, [crie configurações de diagnóstico](../essentials/diagnostic-settings.md) para enviar logs de recursos de recursos do Azure para o espaço de trabalho. [Habilite o Revisions de VM](../vm/vminsights-enable-overview.md) para coletar dados de máquinas virtuais. Configure [fontes de dados no espaço de trabalho](../agents/data-sources.md) para coletar eventos adicionais e dados de desempenho.

- Veja [o que é monitorado pelo Azure monitor?](../monitor-reference.md) para obter uma lista completa das fontes de dados que você pode configurar para enviar dados para Azure monitor logs.


## <a name="log-analytics-workspaces"></a>Workspaces do Log Analytics
Os dados coletados por logs de Azure Monitor são armazenados em um ou mais [espaços de trabalho do log Analytics](./design-logs-deployment.md). O espaço de trabalho define a localização geográfica dos dados, direitos de acesso que definem quais usuários podem acessar dados e definições de configuração, como o tipo de preço e a retenção de dados.  

Você deve criar pelo menos um espaço de trabalho para usar logs de Azure Monitor. Um único espaço de trabalho pode ser suficiente para todos os seus dados de monitoramento ou pode optar por criar vários espaços de trabalho, dependendo de seus requisitos. Por exemplo, você pode ter um espaço de trabalho para seus dados de produção e outro para teste. 

- Consulte [criar um log Analytics espaço de trabalho no portal do Azure](./quick-create-workspace.md) para criar um novo espaço de trabalho.
- Consulte [projetando sua implantação do Azure monitor logs](design-logs-deployment.md) em considerações para criar vários espaços de trabalho.

## <a name="data-structure"></a>Estrutura de dados
As consultas de log recuperam seus dados de um espaço de trabalho Log Analytics. Cada espaço de trabalho contém várias tabelas que são organizadas em colunas separadas com várias linhas de dados. Cada tabela é definida por um conjunto exclusivo de colunas que são compartilhadas pelas linhas de dados fornecidas pela fonte de dados. 

[![Estrutura de logs de Azure Monitor](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Os dados de log de Application Insights também são armazenados em logs de Azure Monitor, mas são armazenados diferentes dependendo de como seu aplicativo é configurado. Para um aplicativo baseado em espaço de trabalho, os dados são armazenados em um espaço de trabalho Log Analytics em um conjunto padrão de tabelas para manter dados, como solicitações de aplicativo, exceções e exibições de página. Vários aplicativos podem usar o mesmo espaço de trabalho. Para um aplicativo clássico, os dados não são armazenados em um espaço de trabalho Log Analytics. Ele usa a mesma linguagem de consulta e você cria e executa consultas usando a mesma ferramenta de Log Analytics no portal do Azure. No entanto, os dados para aplicativos clássicos são armazenados separadamente uns dos outros. Sua estrutura geral é igual a aplicativos baseados em espaço de trabalho, embora os nomes de tabela e coluna sejam diferentes. Consulte [alterações de recursos com base no espaço de trabalho](../app/apm-tables.md) para obter uma comparação detalhada do esquema para aplicativos baseados em espaço de trabalho e clássicos.


> [!NOTE]
> Ainda fornecemos compatibilidade total com versões anteriores para as consultas de recursos clássicos, as pastas de trabalho e os alertas baseados em log do Application Insights na experiência do Application Insights. Para consultar/exibir em relação à [nova estrutura/esquema de tabela baseada em espaço de trabalho](../app/apm-tables.md), você deverá primeiro navegar para seu espaço de trabalho do Log Analytics. Durante a visualização, selecione **Logs** de dentro dos painéis do Application Insights para ter acesso à experiência clássica de consulta do Application Insights. Consulte [escopo da consulta](./scope.md) para obter mais detalhes.


[![Estrutura de logs de Azure Monitor para Application Insights](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)


## <a name="log-queries"></a>Consultas de logs
Os dados são recuperados de um espaço de trabalho Log Analytics usando uma consulta de log que é uma solicitação somente leitura para processar dados e retornar resultados. As consultas de log são escritas em [KQL (linguagem de consulta Kusto)](/azure/data-explorer/kusto/query/), que é a mesma linguagem de consulta usada pelo data Explorer do Azure. Você pode gravar consultas de log em Log Analytics para analisar seus resultados interativamente, usá-los em regras de alerta para ser notificado proativamente sobre problemas ou incluir seus resultados em pastas de trabalho ou Dashboards. As informações incluem consultas predefinidas para dar suporte a suas exibições e pastas de trabalho.

- Consulte [consultas de log em Azure monitor](./log-query-overview.md) para obter uma lista de onde as consultas de log são usadas e referências a tutoriais e a outras documentações para ajudá-lo a começar.

![Log Analytics](media/data-platform-logs/log-analytics.png)

## <a name="log-analytics"></a>Log Analytics
Use Log Analytics, que é uma ferramenta na portal do Azure, para editar e executar consultas de log e analisar interativamente seus resultados. Você pode usar as consultas que você cria para dar suporte a outros recursos no Azure Monitor, como alertas de consulta de log e pastas de trabalho. Acesse Log Analytics na opção **logs** no menu Azure monitor ou na maioria dos outros serviços no portal do Azure.

- Consulte [visão geral de log Analytics no Azure monitor](./log-analytics-overview.md) para obter uma descrição de log Analytics. 
- Consulte [log Analytics tutorial](./log-analytics-tutorial.md) para percorrer o uso de recursos log Analytics para criar uma consulta de log simples e analisar seus resultados.



## <a name="relationship-to-azure-data-explorer"></a>Relação com o Azure Data Explorer
Os logs de Azure Monitor baseiam-se na Data Explorer do Azure. Um espaço de trabalho Log Analytics é aproximadamente o equivalente de um banco de dados no Azure Data Explorer, as tabelas são estruturadas da mesma maneira e ambas usam a mesma linguagem de consulta Kusto (KQL). A experiência de usar o Log Analytics para trabalhar com Azure Monitor consultas no portal do Azure é semelhante à experiência usando a interface do usuário da Web do Azure Data Explorer. Você pode até mesmo [incluir dados de um espaço de trabalho log Analytics em uma consulta de data Explorer do Azure](/azure/data-explorer/query-monitor-data). 


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [consultas de log](./log-query-overview.md) para recuperar e analisar dados de um espaço de trabalho log Analytics.
- Saiba mais sobre as [métricas no Azure Monitor](../essentials/data-platform-metrics.md).
- Saiba mais sobre os [dados de monitoramento disponíveis](../agents/data-sources.md) para diferentes recursos no Azure.