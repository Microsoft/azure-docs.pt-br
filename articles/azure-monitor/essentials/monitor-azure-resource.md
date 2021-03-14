---
title: Monitorar recursos do Azure com o Azure Monitor | Microsoft Docs
description: Descreve como coletar e analisar dados de monitoramento de máquinas virtuais no Azure usando o Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: af2803a945821a23ff59f196967d792a6593009f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033377"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Monitorar recursos do Azure com o Azure Monitor
Quando você tem aplicativos e processos de negócios críticos que dependem de recursos do Azure, recomendamos monitorar a disponibilidade, o desempenho e a operação desses recursos. Este artigo descreve os dados de monitoramento gerados pelos recursos do Azure e como você pode usar os recursos do Azure Monitor para analisar e alertar sobre esses dados.

> [!IMPORTANT]
> Este artigo se aplica a todos os serviços no Azure que usam Azure Monitor. Os recursos de computação, incluindo VMs e serviço de aplicativo, geram os mesmos dados de monitoramento descritos aqui, mas também têm um sistema operacional convidado que também pode gerar logs e métricas. Consulte a documentação de monitoramento para esses serviços para obter detalhes sobre como coletar e analisar esses dados.

## <a name="what-is-azure-monitor"></a>O que é o Azure Monitor?
Azure Monitor é um serviço de monitoramento de pilha completo no Azure que fornece um conjunto completo de recursos para monitorar seus recursos do Azure, além de recursos em outras nuvens e locais. A [plataforma de dados Azure monitor](../data-platform.md) coleta dados em [logs](../logs/data-platform-logs.md) e [métricas](../essentials/data-platform-metrics.md) onde eles podem ser analisados juntos usando um conjunto completo de ferramentas de monitoramento. Veja a lista completa de aplicativos e serviços que podem ser monitorados pelo Azure Monitor em [o que é monitorado pelo Azure monitor?](../monitor-reference.md).

Assim que você criar um recurso do Azure, o Azure Monitor será habilitado e começará a coletar métricas e logs de atividade que você pode [exibir e analisar no portal do Azure](#monitoring-in-the-azure-portal). Com algumas configurações, você pode coletar dados de monitoramento adicionais e habilitar recursos adicionais. Consulte os [Dados de monitoramento](#monitoring-data) abaixo para obter detalhes sobre quaisquer requisitos de configuração.


## <a name="costs-associated-with-monitoring"></a>Custos associados ao monitoramento
Não há nenhum custo para analisar dados de monitoramento que são coletados por padrão. Isso inclui o seguinte:

- Coletar métricas de plataforma e analisá-lo com o Metrics Explorer.
- Coletar o log de atividades e analisá-lo no portal do Azure.
- Criar uma regra de um alerta do log de atividades.

Não há custos do Azure Monitor para coletar e exportar logs e métricas, mas pode haver custos relacionados associados ao destino:

- Custos associados à ingestão de dados e retenção ao coletar logs e métricas no workspace do Log Analytics. Consulte [Preços do Azure Monitor para Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).
- Os custos associados ao armazenamento de dados ao coletar logs e métricas para uma conta de armazenamento do Azure. Confira [Preços do Armazenamento do Azure para armazenamento de blob](https://azure.microsoft.com/pricing/details/storage/blobs/).
- Os custos associados ao streaming do hub de eventos ao encaminhar os logs e as métricas para os hubs de eventos do Azure. Consulte [preços dos Hubs de Eventos do Azure](https://azure.microsoft.com/pricing/details/event-hubs/).

Pode haver custos do Azure Monitor associados ao seguinte. Consulte [Preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/):

- Executando uma consulta de log.
- Criando uma regra de alerta de consulta de métrica ou log.
- Enviando uma notificação de qualquer regra de alerta.
- Acessando métricas por meio de API.

## <a name="monitoring-data"></a>Dados de monitoramento
Recursos no Azure geram [logs](../logs/data-platform-logs.md) e [métricas](../essentials/data-platform-metrics.md) exibidas no diagrama a seguir. Consulte a documentação de cada serviço do Azure para obter os dados específicos que eles geram e quaisquer soluções ou informações adicionais que eles fornecem.

![Visão geral](media/monitor-azure-resource/logs-metrics.png)



- [Métricas de plataforma](../essentials/data-platform-metrics.md) - Valores numéricos que são coletados automaticamente em intervalos regulares e descrevem algum aspecto de um recurso em um determinado momento. 
- [Logs de recurso](./platform-logs-overview.md) - Fornecem informações sobre as operações executadas dentro de um recurso do Azure (o plano de dados), por exemplo, obtenção de um segredo de um Key Vault ou uma solicitação para um banco de dados. O conteúdo e a estrutura desses logs de recurso variam de acordo com o tipo de recurso e serviço do Azure.
- [Log de atividades](./platform-logs-overview.md) - Fornece informações sobre as operações em cada recurso do Azure na assinatura desde a parte exterior (o plano de gerenciamento), criando um novo recurso ou iniciando uma máquina virtual, por exemplo. Estas são informações sobre o que, quem e quando para quaisquer operações de gravação (PUT, POST, DELETE) executadas nos recursos em sua assinatura.


## <a name="configuration-requirements"></a>Requisitos de configuração

### <a name="configure-monitoring"></a>Configurar monitoramento
Alguns dados de monitoramento são coletados automaticamente, mas talvez seja necessário executar algumas configurações dependendo de suas necessidades. Consulte as informações abaixo para obter informações específicas para cada tipo de dados de monitoramento.

- [Métricas de plataforma](../essentials/data-platform-metrics.md) - As métricas de plataforma são coletadas automaticamente em [métricas de Azure Monitor](../essentials/data-platform-metrics.md) sem configuração necessária. Crie uma configuração de diagnóstico para enviar entradas para logs do Azure Monitor ou para encaminhá-las para fora do Azure.
- [Logs de recursos](./platform-logs-overview.md) - Os logs de recursos são gerados automaticamente pelos recursos do Azure, mas não são coletados sem uma configuração de diagnóstico.  Crie uma configuração de diagnóstico para enviar entradas para logs do Azure Monitor ou para encaminhá-las para fora do Azure.
- [Log de atividades](./platform-logs-overview.md) – O Log de atividades é coletado automaticamente sem nenhuma configuração necessária e pode ser exibido na portal do Azure. Crie uma configuração de diagnóstico para copiá-las para Logs do Azure Monitor ou para encaminhá-las para fora do Azure.

### <a name="log-analytics-workspace"></a>Espaço de trabalho do Log Analytics
A coleta de dados em logs de Azure Monitor requer um workspace do Log Analytics. Você pode começar a monitorar seu serviço rapidamente criando um novo espaço de trabalho, mas pode haver um valor no uso de um espaço de trabalho que está coletando dados de outros serviços. Consulte [Criar um workspace do Log Analytics no portal do Azure](../logs/quick-create-workspace.md) para obter detalhes sobre como criar um espaço de trabalho e [Criar sua implantação de Logs do Azure Monitor](../logs/design-logs-deployment.md) para ajudar a determinar o melhor design de espaço de trabalho para seus requisitos. Se você usar um espaço de trabalho existente em sua organização, precisará de permissões apropriadas, conforme descrito em [Gerenciar o acesso a dados de log e espaços de trabalho no Azure Monitor](../logs/manage-access.md). 





## <a name="diagnostic-settings"></a>Configurações de Diagnóstico
As configurações de diagnóstico definem para que local os logs de recursos e métricas de um recurso específico devem ser enviados. Os possíveis destinos são:

- [Workspace do Log Analytics](./resource-logs.md#send-to-log-analytics-workspace), que permite analisar dados com outros dados de monitoramento coletados por Azure Monitor usando consultas de log potentes e também para aproveitar outros recursos de Azure Monitor, como alertas de log e visualizações. 
- [Hubs de eventos](./resource-logs.md#send-to-azure-event-hubs) para transmitir dados para sistemas externos, como SIEMs de terceiros e outras soluções de análise logs. 
- [A conta de armazenamento do Azure](./resource-logs.md#send-to-azure-storage) que é útil para auditoria, análise estática ou backup.

Siga o procedimento em [Criar configuração de diagnóstico para coletar logs e métricas de plataforma no Azure](../essentials/diagnostic-settings.md) para criar e gerenciar configurações de diagnóstico por meio do portal do Azure. Consulte [Criar configuração de diagnóstico no Azure usando um modelo do Resource Manager](./resource-manager-diagnostic-settings.md) para defini-la em um modelo e habilitar o monitoramento completo de um recurso quando ele for criado.


## <a name="monitoring-in-the-azure-portal"></a>Monitoramento no portal do Azure
 Você pode acessar os dados de monitoramento para a maioria dos recursos do Azure no menu do recurso na portal do Azure. Isso dará acesso a dados de um único recurso usando ferramentas de Azure Monitor padrão. Alguns serviços do Azure fornecerão opções diferentes, portanto, você deve consultar a documentação desse serviço para obter informações adicionais. Use o menu **Azure Monitor** para analisar dados de todos os recursos monitorados. 

### <a name="overview"></a>Visão geral
Muitos serviços incluirão dados de monitoramento na página de **Visão Geral** como uma visualização rápida da operação. Normalmente, isso será baseado em um subconjunto de métricas de plataforma armazenado nas Métricas do Azure Monitor. Outras opções de monitoramento normalmente estarão disponíveis em uma seção **monitoramento** do menu do serviço.

![Página de visão geral](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>Insights e soluções 
Alguns serviços fornecerão ferramentas além dos recursos padrão do Azure Monitor. [Insights](../monitor-reference.md) fornecem uma experiência de monitoramento personalizada criada na plataforma de dados Azure Monitor e nos recursos padrão. As [soluções](../insights/solutions.md) fornecem uma lógica de monitoramento predefinida com base em Logs de Azure Monitor. 

Se um serviço tiver um insight do Azure Monitor, você poderá acessá-lo de **Monitoramento** no menu de cada recurso. Acesse todas as informações e soluções no menu **Azure Monitor**.

![Informações no portal do Azure](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>Métricas
Analise as métricas no portal do Azure usando o [Metrics Explorer](./metrics-getting-started.md) que está disponível no item de menu **Métricas** para a maioria dos serviços. Essa ferramenta permite que você trabalhe com métricas individuais ou combine várias para identificar correlações e tendências. 

- Consulte [Introdução ao Azure Metrics Explorer](./metrics-getting-started.md) para obter detalhes sobre como usar essa ferramenta.
- Consulte [recursos avançados do Azure Metrics Explorer](../essentials/metrics-charts.md) para recursos avançados do Metrics Explorer, como o uso de várias métricas e a aplicação de filtros e divisão.

![Gerenciador de métricas no portal do Azure](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>Log de atividades 
Exiba entradas no log de atividades no portal do Azure com o filtro inicial definido como o recurso atual. Copie o log de atividades em um espaço de trabalho Log Analytics para acessá-lo para usá-lo em consultas de log e pastas de trabalho. 

- Consulte [Exibir e recuperar eventos do log de atividades do Azure](../essentials/activity-log.md#view-the-activity-log) para obter detalhes sobre como exibir o log de atividades e recuperar entradas usando uma variedade de métodos.
- Consulte a documentação do serviço do Azure para obter os eventos específicos que são registrados.

![Log de Atividade](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Logs do Azure Monitor
Os logs de Azure Monitor consolidam os logs e as métricas de vários serviços e outras fontes de dados para análise com uma poderosa ferramenta de consulta. Conforme descrito acima, crie uma configuração de diagnóstico para coletar métricas de plataforma, log de atividades e logs de recursos em um espaço de trabalho Log Analytics no Azure Monitor.

O [Log Analytics](../logs/log-analytics-tutorial.md) permite que você trabalhe com [consultas de log](../logs/log-query-overview.md), que é um recurso poderoso do Azure Monitor que permite executar uma análise avançada dos dados de log usando uma linguagem de consulta totalmente em destaque. Abra Log Analytics de **Logs** no menu **Monitoramento** de um recurso do Azure para trabalhar com consultas de log usando o recurso como de [escopo de consulta](../logs/scope.md#query-scope). Isso permite que você analise dados em várias tabelas apenas para esse recurso. Use os **Logs** no menu Azure Monitor para acessar os logs de todos os recursos. 

- Confira [Introdução às consultas de log no Azure Monitor](../logs/get-started-queries.md) para ver um tutorial sobre como escrever consultas de log.
- Consulte [Coletar logs de recursos do Azure no espaço de trabalho Log Analytics no Azure Monitor](./resource-logs.md#send-to-log-analytics-workspace) para obter mais informações sobre como os logs de recursos são coletados nos logs do Azure Monitor e detalhes sobre como acessá-los em uma consulta.
- Consulte [Modo de coleta](./resource-logs.md#send-to-log-analytics-workspace) para obter uma explicação de como os dados do log de recursos são estruturados nos Logs de Azure Monitor.
- Consulte a documentação de cada serviço do Azure para saber mais detalhes sobre sua tabela em logs de Azure Monitor.

![Log Analytics no portal do Azure](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>Monitorando a partir da linha de comando
Você pode acessar os dados de monitoramento coletados do recurso de uma linha de comando ou incluir em um script usando [Azure PowerShell](/powershell/azure/) ou [Interface de linha de comando do Azure](/cli/azure/). 

- Consulte [referência de métricas da CLI](/cli/azure/monitor/metrics) para acessar dados de métrica da CLI.
- Consulte [a referência de interface de linha de comando](/cli/azure/ext/log-analytics/monitor/log-analytics) para acessar dados de Logs do Azure Monitor usando uma consulta de log da CLI.
- Consulte [Referência de métricas Azure PowerShell](/powershell/module/azurerm.insights/get-azurermmetric) para acessar dados de métrica do Azure PowerShell.
- Consulte [Referência de consulta de log Azure PowerShell](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) para acessar dados de logs do Azure Monitor usando uma consulta de log do Azure PowerShell.

## <a name="monitoring-from-rest-api"></a>Monitorando do API REST
Inclua dados de monitoramento coletados de seu recurso em um aplicativo personalizado usando uma API REST.

- Confira o [Passo a passos da API REST de monitoramento do Azure](./rest-api-walkthrough.md) para obter detalhes sobre como acessar métricas da API REST do Azure Monitor.
- Consulte [API REST do Azure Log Analytics](https://dev.loganalytics.io/) obter informações para acessar dados de logs do Azure Monitor usando uma consulta de log do Azure PowerShell.

## <a name="alerts"></a>Alertas
Os [alertas](../alerts/alerts-overview.md) trabalham de forma proativa, mandando notificações e potencialmente tomando medidas quando encontram condições importantes em seus dados de monitoramento. Você cria uma regra de alerta que define um destino para o alerta, as condições para se criar um alerta e as ações a serem executadas em resposta.

Tipos diferentes de dados de monitoramento são usados para diferentes tipos de regras de alerta.

- [Alerta do log de atividades](../alerts/alerts-activity-log.md) - Crie um alerta quando uma entrada for criada no log de atividades que corresponde a critérios específicos. Isso permite que você seja notificado por exemplo quando um determinado tipo de recurso é criado ou se uma alteração de configuração falhar.
- [Alerta de métrica](../alerts/alerts-metric.md) - Crie um alerta quando um valor de métrica exceder um limite específico. Os alertas de métrica são mais responsivos do que outros alertas e podem ser resolvidos automaticamente quando o problema é corrigido.
- [Alerta de consulta de log](../alerts/alerts-log.md) – Execute uma consulta de log em intervalos regulares e crie um alerta se uma determinada condição for encontrada. Isso permite que você execute análises complexas em vários conjuntos de dados e .

Use **Alertas** no menu de um recurso para exibir alertas e gerenciar regras de alerta para esse recurso. Somente alertas de log de atividades e alertas de métrica usam recursos individuais do Azure como um destino. Alertas de consulta de log usam um espaço de trabalho Log Analytics como um destino e se baseiam em uma consulta que pode acessar quaisquer logs armazenados nesse espaço de trabalho. Use o menu Azure Monitor para exibir e gerenciar alertas para todos os recursos e para gerenciar as regras de alerta de consulta de log.

- Consulte os artigos para conhecer os diferentes tipos de alertas acima para obter detalhes sobre como criar regras de alerta.
- Consulte [Criar e gerenciar grupos de ações no portal do Azure](../alerts/action-groups.md) para obter detalhes sobre como criar um grupo de ações que permite gerenciar respostas a alertas.



## <a name="next-steps"></a>Próximas etapas

* Confira [Serviços, esquemas e categorias compatíveis com logs de recursos do Azure](./resource-logs-schema.md) para ver detalhes dos logs de recursos de diferentes serviços do Azure.