---
title: Coletar e analisar logs de atividades do Azure no espaço de trabalho do Log Analytics | Microsoft Docs
description: Você pode usar a solução de Logs de atividade do Azure para analisar e pesquisar o log de atividades do Azure em todas as suas assinaturas do Azure.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: magoedte
ms.openlocfilehash: 4476bb0a5a343fd43ce5ed70cf0e493d0ccae0e9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741204"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Coletar e analisar logs de atividades do Azure no espaço de trabalho do Log Analytics no Azure Monitor

![Símbolo dos logs de atividades do Azure](./media/collect-activity-logs/activity-log-analytics.png)

A solução Análise do Log de Atividades o ajuda a analisar e pesquisar o [Log de atividades do Azure](activity-logs-overview.md) em todas as suas assinaturas do Azure. O Log de Atividades do Azure é um log que fornece análise das operações executadas nos recursos em sua assinatura. O Log de atividades era conhecido como *Logs de Auditoria* ou *Logs Operacionais*, pois relata eventos de suas assinaturas.

Usando o Log de atividades, você pode determinar o *quê*, *quem* e *quando* para qualquer operação de gravação (PUT, POST, DELETE) feitas para os recursos em sua assinatura. Também é possível compreender o status da operação e outras propriedades relevantes. O Log de atividades não inclui operações de leitura (GET) ou operações para recursos que usam o modelo de implantação Clássico.

Quando você conectar seus logs de atividades do Azure para um espaço de trabalho do Log Analytics, você pode:

- Analisar os logs de atividade com exibições predefinidas
- Analisar e pesquisar os logs de atividade de várias assinaturas do Azure
- Manter logs de atividade por mais de 90 dias<sup>1</sup>
- Correlacionar os logs de atividade com outra plataforma do Azure e dados do aplicativo
- Consultar atividades operacionais agregadas por status
- Exibir tendências de atividades que acontecem em cada serviço do Azure
- Relatar alterações de autorização em todos os recursos do Azure
- Identificar problemas de integridade ou interrupção de serviço que afetam os recursos
- Usar pesquisa de logs para correlacionar atividades do usuário, operações de dimensionamento automático, alterações de autorização e integridade do serviço a outros logs ou métricas do seu ambiente

<sup>1</sup>por padrão, Monitor do Azure mantém seus logs de atividades do Azure em um espaço de trabalho do Log Analytics por 90 dias, mesmo se você estiver usando a camada gratuita. Ou, se você tiver uma configuração de retenção de workspace inferior a 90 dias. Se o workspace tiver retenção com mais de 90 dias, os logs de atividades serão mantidos com base no período de retenção do workspace.

O espaço de trabalho do Log Analytics coleta os logs de atividade gratuitamente e armazena os logs por 90 dias gratuitamente. Se você armazenar os logs por mais de 90 dias, incorrerá em encargos de retenção de dados para os dados armazenados por mais de 90 dias.

Quando você está no tipo de preço Gratuito, os logs de atividade não se aplicam ao consumo de dados diário.

## <a name="connected-sources"></a>Fontes conectadas

Ao contrário da maioria das outras soluções do Azure Monitor, os dados não são coletados para logs de atividade por agentes. Todos os dados usados pela solução vêm diretamente do Azure.

| Fonte Conectada | Com suporte | DESCRIÇÃO |
| --- | --- | --- |
| [Agentes do Windows](agent-windows.md) | Não  | A solução não coleta informações de agentes do Windows. |
| [Agentes do Linux](../learn/quick-collect-linux-computer.md) | Não  | A solução não coleta informações de agentes do Linux. |
| [Grupo de gerenciamento do System Center Operations Manager](om-agents.md) | Não  | A solução não coleta informações de agentes subordinados a um grupo de gerenciamento do Operations Manager. |
| [Conta de armazenamento do Azure](collect-azure-metrics-logs.md) | Não  | A solução não coleta informações de armazenamento do Azure. |

## <a name="prerequisites"></a>Pré-requisitos

Para acessar informações do log de atividades do Azure, você deve ter uma assinatura do Azure.

A solução também requer que os seguintes dois provedores de recursos são registrados em sua assinatura:

1. Microsoft.OperationalInsights
2. Microsoft.OperationsManagement

Para saber como registrar ou verificar se eles são registrados, consulte [tipos e provedores de recursos do Azure](../../azure-resource-manager/resource-manager-supported-services.md)

## <a name="configuration"></a>Configuração

Execute as seguintes etapas para configurar a solução Análise do Log de Atividades para seus workspaces.

1. Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

2. Habilite a solução Log Analytics da atividade no [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActivityOMS?tab=Overview) ou usando o processo descrito em [Adicionar soluções Log Analytics por meio da Galeria de soluções](../insights/solutions.md).

2. Configure os logs da atividade para ir para o espaço de trabalho do Log Analytics.
    1. No portal do Azure, selecione seu workspace e clique em **Log de Atividades do Azure**.
    2. Para cada assinatura, clique no nome da assinatura.  
        
        ![adicionar assinatura](./media/collect-activity-logs/add-subscription.png)
    
    3. Na folha *SubscriptionName*, clique em **Conectar**.  
    
        ![Conectar assinatura](./media/collect-activity-logs/subscription-connect.png)

## <a name="using-the-solution"></a>Usando a solução

Quando você adiciona a solução Análise do Log de Atividades da atividade ao workspace, o bloco **Logs de Atividade do Azure** é adicionado ao painel Visão geral. Esse bloco exibe uma contagem do número de registros de atividade do Azure para as assinaturas do Azure às quais a solução tem acesso.

![Bloco de logs de atividade do Azure](./media/collect-activity-logs/azure-activity-logs-tile.png)

### <a name="view-azure-activity-logs"></a>Exibir logs de atividade do Azure

Clique no bloco **Logs de Atividade do Azure** para abrir o painel **Logs de Atividade do Azure**. O painel inclui as folhas na tabela a seguir. Cada folha lista os 10 principais itens que correspondem aos critérios da folha para o escopo e o intervalo de tempo especificados. É possível executar uma pesquisa de logs que retorna todos os registros clicando em **Ver todos** na parte inferior da folha ou clicando no cabeçalho de folha.

Os dados do log de atividades aparecem apenas *depois* que você os configura para ir para a solução, portanto você não pode ver os dados antes disso.

| Folha | DESCRIÇÃO |
| --- | --- |
| Entradas de log de atividades do Azure | Mostra um gráfico de barras na parte superior dos totais de registro da entrada de log de atividades do Azure para o intervalo de datas selecionado e uma lista dos principais 10 chamadores da atividade. Clique no gráfico de barras para executar uma pesquisa de logs para <code>AzureActivity</code>. Clique em um item do chamador para executar uma pesquisa de logs que retorna todas as entradas de log de atividades desse item. |
| Logs de atividade por status | Mostra um gráfico de rosca do status do log de atividades do Azure para o intervalo de datas que você selecionou. Também mostra uma lista dos dez principais registros de status. Clique no gráfico para executar uma pesquisa de logs para <code>AzureActivity &#124; summarize AggregatedValue = count() by ActivityStatus</code>. Clique em um item do chamador para executar uma pesquisa de logs que retorna todas as entradas de log de atividades desse registro de status. |
| Logs de atividade por recurso | Mostra o número total de recursos com logs de atividade e lista os dez principais recursos com contagem de registro para cada recurso. Clique na área total para executar uma pesquisa de logs de <code>AzureActivity &#124; summarize AggregatedValue = count() by Resource</code>, que mostra todos os recursos do Azure disponíveis para a solução. Clique em um recurso para executar uma pesquisa de logs que retorna que todos os registros de atividades desse recurso. |
| Logs de atividade por provedor de recursos | Mostra o número total de provedores de recursos que produzem logs da atividade e lista os dez principais. Clique na área total para executar uma pesquisa de logs de <code>AzureActivity &#124; summarize AggregatedValue = count() by ResourceProvider</code>, que mostra todos os provedores de recursos do Azure. Clique em um provedor de recursos para executar uma pesquisa de logs que retorna todos os registros de atividades desse provedor. |

![Painel Logs de Atividade do Azure](./media/collect-activity-logs/activity-log-dash.png)

## <a name="next-steps"></a>Próximas etapas

- Crie um [alerta](../platform/alerts-metric.md) quando ocorrer uma atividade específica.
- Use a [Pesquisa de Logs](../log-query/log-query-overview.md) para exibir informações detalhadas de seus logs de atividade.
