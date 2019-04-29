---
title: Registra em log no Azure Monitor | Microsoft Docs
description: Descreve os logs no Azure Monitor que são usados para análise avançada de dados de monitoramento.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 0203/26/2019
ms.author: bwren
ms.openlocfilehash: ec037b16840afe669ac3934beaa832f850cdcfb0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60809232"
---
# <a name="logs-in-azure-monitor"></a>Logs no Azure Monitor

> [!NOTE]
> Todos os dados coletados pelo Azure Monitor se encaixa em um dos dois tipos fundamentais, métricas e Logs. Este artigo descreve os Logs. Consulte a [métricas no Azure Monitor](data-platform-metrics.md) para obter uma descrição detalhada de métricas e a [os dados de monitoramento coletados pelo Azure Monitor](data-platform.md) para obter uma comparação dos dois.

Os logs no Azure Monitor são especialmente úteis para a realização de análises de dados de uma variedade de fontes. Este artigo descreve como os Logs são estruturadas no Azure Monitor, o que você pode fazer com os dados e identifica diferentes fontes de dados que armazenam dados em Logs.

> [!NOTE]
> É importante distinguir entre Logs do Azure Monitor e fontes de dados de log no Azure. Por exemplo, os eventos de nível de assinatura no Azure são gravados para uma [log de atividades](activity-logs-overview.md) que podem ser exibidos no menu do Azure Monitor. A maioria dos recursos será grava informações operacionais para uma [log de diagnóstico](diagnostic-logs-overview.md) que você pode encaminhar para locais diferentes. Os Logs do Azure Monitor é uma plataforma de dados de log que coleta os logs de atividade e logs de diagnóstico junto com outros dados de monitoramento para fornecer análise profunda de todo o conjunto de recursos.

## <a name="what-are-azure-monitor-logs"></a>Quais são os Logs do Azure Monitor?

Os logs no Azure Monitor contenham diferentes tipos de dados organizados em registros com diferentes conjuntos de propriedades para cada tipo. Logs podem conter valores numéricos, como as métricas do Azure Monitor, mas geralmente contêm dados de texto com descrições detalhadas. Ainda mais diferem dos dados de métrica em que elas variam em sua estrutura e geralmente não são coletadas em intervalos regulares. Telemetria, como eventos e rastreamentos são armazenados Logs do Azure Monitor, bem como dados de desempenho para que ele possa todas ser combinados para análise.

Um tipo comum de entrada de log é um evento, que é coletado esporadicamente. Eventos são criados por um aplicativo ou serviço e normalmente incluem informações suficientes para fornecer o contexto completo por conta própria. Por exemplo, um evento pode indicar que um recurso específico foi criado ou modificado, um novo host é iniciado em resposta ao maior tráfego ou um erro foi detectado em um aplicativo.

 Como o formato dos dados pode variar, os aplicativos podem criar logs personalizados usando a estrutura que eles exigem. Dados de métrica ainda podem ser armazenados nos Logs para combiná-los com outros dados de monitoramento de tendências e outras análises de dados.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>O que você pode fazer com Logs do Azure Monitor?
A tabela a seguir lista as diferentes maneiras que você pode usar os Logs no Azure Monitor.


|  |  |
|:---|:---|
| Analise | Use [do Log Analytics](../log-query/get-started-portal.md) no portal do Azure para gravar [registrar consultas](../log-query/log-query-overview.md) e analisar interativamente os dados de log usando o poderoso mecanismo de análise do Data Explorer.<br>Use o [console do Application Insights analytics](../app/analytics.md) no portal do Azure para escrever consultas de log e analisar interativamente os dados de log do Application Insights. |
| Visualizar | Fixar os resultados da consulta processados como tabelas ou gráficos para uma [painel do Azure](../../azure-portal/azure-portal-dashboards.md).<br>Criar uma [pasta de trabalho](../app/usage-workbooks.md) para combinar com vários conjuntos de dados em um relatório interativo. <br>Exportar os resultados de uma consulta para o [Power BI](powerbi.md) a fim de usar visualizações diferentes e compartilhar com usuários fora do Azure.<br>Exportar os resultados de uma consulta para [Grafana](grafana-plugin.md) aproveitar seus painéis e combine com outras fontes de dados.|
| Alerta | Configurar uma [regra de alerta de log](alerts-log.md) que envia uma notificação ou realiza [ação automatizada](action-groups.md) quando os resultados da consulta correspondem a um resultado específico.<br>Configurar uma [regra de alerta de métrica](alerts-metric-logs.md) em determinados registros de dados de log extraídos como métricas. |
| Recuperar | Resultados da consulta de log de acesso de uma linha de comando usando [CLI do Azure](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Resultados da consulta de log de acesso de uma linha de comando usando [cmdlets do PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights).<br>Acessar resultados de consulta do log de um aplicativo personalizado usando [API REST](https://dev.loganalytics.io/). |
| Exportação | Criar um fluxo de trabalho para recuperar dados de log e copie-o para um local externo usando [aplicativos lógicos](~/articles/logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>É como dados em Logs do Azure Monitor estruturada?
Os dados coletados pelos Logs do Azure Monitor são armazenados em uma [espaço de trabalho do Log Analytics](../platform/manage-access.md). Você pode [criar vários espaços de trabalho](manage-access.md#determine-the-number-of-workspaces-you-need) em sua assinatura para gerenciar diferentes conjuntos de dados de log. Cada espaço de trabalho contém várias tabelas que armazenam dados de uma fonte específica. Enquanto todas as tabelas compartilham [algumas propriedades comuns](log-standard-properties.md), cada um tem um conjunto exclusivo de propriedades dependendo do tipo de dados que ele armazena. Um novo espaço de trabalho terão o conjunto padrão de tabelas e mais tabelas serão adicionadas por diferentes soluções de monitoramento e outros serviços que gravam no espaço de trabalho.

Dados de log do Application Insights usam o mesmo mecanismo de análise de Log como espaços de trabalho, mas ele é armazenado separadamente para cada aplicativo monitorado. Cada aplicativo tem um conjunto padrão de tabelas para armazenar dados como exibições de página, exceções e solicitações do aplicativo.

Consultas de log serão a usar dados de um espaço de trabalho do Log Analytics ou um aplicativo de Application Insights. Você pode usar um [consulta de recursos cruzados](../log-query/cross-workspace-query.md) para analisar os dados de aplicativo junto com outros dados de log ou para criar consultas, incluindo vários espaços de trabalho ou aplicativos.

![Workspaces](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Consultas de logs
Dados em Logs do Azure Monitor são recuperados usando uma [consulta de log](../log-query/log-query-overview.md) escrito com o [linguagem de consulta Kusto](../log-query/get-started-queries.md), que permite que você recupere rapidamente, consolidar e analisar os dados coletados. Use [do Log Analytics](../log-query/portals.md) escrever e testar as consultas de log no portal do Azure. Ele permite que você trabalhar com os resultados de forma interativa ou fixá-los em um painel para exibi-los com outras visualizações.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Abra [Log Analytics do Application Insights](../app/analytics.md) para analisar dados do Application Insights.

![Análise do Application Insights](media/data-platform-logs/app-insights-analytics.png)

Você também pode recuperar dados de log usando o [API do Log Analytics](https://dev.loganalytics.io/documentation/overview) e o [API REST do Application Insights](https://dev.applicationinsights.io/documentation/overview).


## <a name="sources-of-azure-monitor-logs"></a>Fontes de Logs do Azure Monitor
O Azure Monitor pode coletar dados de log de várias origens no Azure e de recursos locais. As tabelas a seguir listam as diferentes fontes de dados disponíveis em diferentes recursos que gravam dados em Logs do Azure Monitor. Cada um tem um link para obter detalhes sobre qualquer configuração necessária.

### <a name="azure-tenant-and-subscription"></a>Assinatura e locatário do azure

| Dados | DESCRIÇÃO |
|:---|:---|
| Logs de auditoria do Azure Active Directory | Configurado por meio de configurações de diagnóstico para cada diretório. Ver [logs de integração do Azure AD com os logs do Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Logs de atividade | Armazenados separadamente, por padrão e pode ser usado para alertas quase em tempo real. Instale a solução de análise de Log de atividades para gravar no espaço de trabalho do Log Analytics. Ver [coletar e analisar logs de atividades do Azure no Log Analytics](collect-activity-logs.md). |

### <a name="azure-resources"></a>Recursos do Azure

| Dados | DESCRIÇÃO |
|:---|:---|
| Diagnóstico do recurso | Configure configurações de diagnóstico para gravar dados de diagnóstico, incluindo métricas para um espaço de trabalho do Log Analytics. Ver [Stream Logs de diagnóstico do Azure para Log Analytics](diagnostic-logs-stream-log-store.md). |
| Soluções de monitoramento | Soluções de monitoramento de gravam os dados coletados para seu espaço de trabalho do Log Analytics. Ver [detalhes de coleta de dados para soluções de gerenciamento do Azure](../insights/solutions-inventory.md) para obter uma lista de soluções. Ver [monitorando as soluções no Azure Monitor](../insights/solutions.md) para obter detalhes sobre como instalar e usar soluções. |
| Métricas | Envio de métricas de plataforma para os recursos do Azure Monitor para um espaço de trabalho do Log Analytics para manter os dados de log por períodos mais longos e executar análises complexas com outros tipos de dados usando o [linguagem de consulta Kusto](/azure/kusto/query/). Ver [Stream Logs de diagnóstico do Azure para Log Analytics](diagnostic-logs-stream-log-store.md). |
| Armazenamento de tabelas do Azure | Coletar dados do armazenamento do Azure em que alguns recursos do Azure é gravar os dados de monitoramento. Ver [armazenamento de BLOBs do Azure Use IIS e o Azure para armazenamento de tabelas para eventos com o Log Analytics](azure-storage-iis-table.md). |

### <a name="virtual-machines"></a>Máquinas Virtuais

| Dados | DESCRIÇÃO |
|:---|:---|
|  Fontes de dados de agente | Fontes de dados coletados [Windows](agent-windows.md) e [Linux](../learn/quick-collect-linux-computer.md) agentes incluem eventos, dados de desempenho e logs personalizados. Ver [fontes de dados do agente no Azure Monitor](data-sources.md) para obter uma lista de fontes de dados e os detalhes sobre a configuração. |
| Soluções de monitoramento | Soluções de monitoramento de gravam os dados coletados dos agentes para seu espaço de trabalho do Log Analytics. Ver [detalhes de coleta de dados para soluções de gerenciamento do Azure](../insights/solutions-inventory.md) para obter uma lista de soluções. Ver [monitorando as soluções no Azure Monitor](../insights/solutions.md) para obter detalhes sobre como instalar e usar soluções. |
| System Center Operations Manager | Conecte-se o grupo de gerenciamento do Operations Manager para o Azure Monitor para coletar dados de desempenho e eventos de agentes locais nos logs. Ver [conectar o Operations Manager ao Log Analytics](om-agents.md) para obter detalhes sobre essa configuração. |


### <a name="applications"></a>Aplicativos

| Dados | DESCRIÇÃO |
|:---|:---|
| Solicitações e exceções | Dados detalhados sobre exceções e solicitações de aplicativo estão na _solicitações_, _pageViews_, e _exceções_ tabelas. Chamadas para [componentes externos](../app/asp-net-dependencies.md) estão na _dependências_ tabela. |
| Uso e desempenho | Desempenho do aplicativo está disponível na _solicitações_, _browserTimings_ e _performanceCounters_ tabelas. Dados para [métricas personalizadas](../app/api-custom-events-metrics.md#trackevent) está no _customMetrics_ tabela.|
| Dados de rastreamento | Resulta da [rastreamento distribuído](../app/distributed-tracing.md) são armazenadas em do _rastreamentos_ tabela. |
| Testes de disponibilidade | Dados de resumo de [testes de disponibilidade](../app/monitor-web-app-availability.md) é armazenado na _availabilityResults_ tabela. Dados detalhados desses testes estão em armazenamento separado e acessado do Application Insights no portal do Azure. |

### <a name="insights"></a>Insights

| Dados | DESCRIÇÃO |
|:---|:---|
| Azure Monitor para contêineres | Dados de inventário e de desempenho coletados pelo [do Azure Monitor para contêineres](../insights/container-insights-overview.md). Ver [detalhes de coleta de dados do contêiner](../insights/container-insights-log-search.md#container-records) para obter uma lista das tabelas. |
| Azure Monitor para VMs | Mapa e dados de desempenho coletados pelo [do Azure Monitor para VMs](../insights/vminsights-overview.md). Ver [como consultar os logs do Azure Monitor para VMs](../insights/vminsights-log-search.md) para obter detalhes sobre como consultar esses dados. |

### <a name="custom"></a>Personalizado 

| Dados | DESCRIÇÃO |
|:---|:---|
| API REST | Grave dados em um espaço de trabalho do Log Analytics de qualquer cliente REST. Ver [enviar dados de log para o Azure Monitor com a API do coletor de dados HTTP](data-collector-api.md) para obter detalhes.
| Aplicativo Lógico | Gravar todos os dados em um espaço de trabalho do Log Analytics de um fluxo de trabalho do aplicativo lógico com o **coletor de dados do Azure Log Analytics** ação. |

### <a name="security"></a>Segurança

| Dados | DESCRIÇÃO |
|:---|:---|
| Central de Segurança do Azure | [A Central de segurança do Azure](/azure/security-center/) armazena dados que coleta em um espaço de trabalho do Log Analytics onde ele pode ser analisado com outros dados de log. Ver [coleta de dados na Central de segurança do Azure](../../security-center/security-center-enable-data-collection.md) para obter detalhes sobre a configuração do espaço de trabalho. |
| Azure Sentinel | [Azure sentinela](/azure/sentinel/) armazena dados de fontes de dados em um espaço de trabalho do Log Analytics. Ver [conectar fontes de dados](/azure/sentinel/connect-data-sources).  |


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [plataforma de dados do Azure Monitor](data-platform.md).
- Saiba mais sobre [métricas no Azure Monitor](data-platform-metrics.md).
- Saiba mais sobre os [dados de monitoramento disponíveis](data-sources.md) para diferentes recursos no Azure.
