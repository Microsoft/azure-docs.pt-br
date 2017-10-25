---
title: Exibir logs de atividades do Azure com o Log Analytics | Microsoft Docs
description: "Você pode usar a solução de Logs de atividade do Azure para analisar e pesquisar o log de atividades do Azure em todas as suas assinaturas do Azure."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: banders
ms.openlocfilehash: 1ad56a54f094f3c314596b3a7c9fecd09647d065
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="view-azure-activity-logs"></a>Exibir logs de atividade do Azure

![Símbolo dos logs de atividades do Azure](./media/log-analytics-activity/activity-log-analytics.png)

A solução Análise do Log de Atividades o ajuda a analisar e pesquisar o [Log de atividades do Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) em todas as suas assinaturas do Azure. O Log de Atividades do Azure é um log que fornece análise das operações executadas nos recursos em sua assinatura. O Log de atividades era conhecido como *Logs de Auditoria* ou *Logs Operacionais*, pois relata eventos de suas assinaturas.

Usando o Log de atividades, você pode determinar o *quê*, *quem* e *quando* para qualquer operação de gravação (PUT, POST, DELETE) feitas para os recursos em sua assinatura. Também é possível compreender o status da operação e outras propriedades relevantes. O Log de atividades não inclui operações de leitura (GET) ou operações para recursos que usam o modelo de implantação Clássico.

Quando você conecta os logs de atividades do Azure ao Log Analytics, é possível:

- Analisar os logs de atividade com exibições predefinidas
- Analisar e pesquisar logs de atividade de várias assinaturas do Azure
- Manter logs de atividade por mais de 90 dias<sup>1</sup>
- Correlacionar os logs de atividade com outra plataforma do Azure e dados do aplicativo
- Consultar atividades operacionais agregadas por status
- Exibir tendências de atividades que acontecem em cada serviço do Azure
- Relatar alterações de autorização em todos os recursos do Azure
- Identificar problemas de integridade ou interrupção de serviço que afetam os recursos
- Usar pesquisa de logs para correlacionar atividades do usuário, operações de dimensionamento automático, alterações de autorização e integridade do serviço a outros logs ou métricas do seu ambiente

<sup>1</sup>Por padrão, o Log Analytics mantém os logs de atividade do Azure por 90 dias, mesmo que você esteja na camada gratuita. Ou, se você tiver uma configuração de retenção de espaço de trabalho inferior a 90 dias. Se o seu espaço de trabalho tiver retenção superior a 90 dias, os logs da atividade serão mantidos durante o período de retenção do espaço de trabalho.

O Log Analytics coleta os logs de atividade e os armazena por 90 dias gratuitamente. Se você armazenar os logs por mais de 90 dias, incorrerá em encargos de retenção de dados para os dados armazenados por mais de 90 dias.

Quando você está no tipo de preço Gratuito, os logs de atividade não se aplicam ao consumo de dados diário.

## <a name="connected-sources"></a>Fontes conectadas

Ao contrário da maioria das outras soluções do Log Analytics, dados não são coletados para logs de atividade por agentes. Todos os dados usados pela solução vêm diretamente do Azure.

| Fonte Conectada | Suportado | Descrição |
| --- | --- | --- |
| [Agentes do Windows](log-analytics-windows-agents.md) | Não | A solução não coleta informações de agentes do Windows. |
| [Agentes do Linux](log-analytics-linux-agents.md) | Não | A solução não coleta informações de agentes do Linux. |
| [Grupo de gerenciamento do SCOM](log-analytics-om-agents.md) | Não | A solução não coleta informações de agentes em um grupo de gerenciamento de SCOM conectado. |
| [Conta de armazenamento do Azure](log-analytics-azure-storage.md) | Não | A solução não coleta informações de armazenamento do Azure. |

## <a name="prerequisites"></a>Pré-requisitos

- Para acessar informações do log de atividades do Azure, você deve ter uma assinatura do Azure.

## <a name="configuration"></a>Configuração

Execute as seguintes etapas para configurar a solução Log Analytics da atividade para seus espaços de trabalho.

1. Habilite a solução Log Analytics da atividade no [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActivityOMS?tab=Overview) ou usando o processo descrito em [Adicionar soluções Log Analytics por meio da Galeria de soluções](log-analytics-add-solutions.md).
2. Configure os logs da atividade para ir para o espaço de trabalho do Log Analytics.
    1. No portal do Azure, selecione seu espaço de trabalho e clique em **Log de Atividades do Azure**.
    2. Para cada assinatura, clique no nome da assinatura.  
        ![adicionar assinatura](./media/log-analytics-activity/add-subscription.png)
    3. Na folha *SubscriptionName*, clique em **Conectar**.  
        ![conectar assinatura](./media/log-analytics-activity/subscription-connect.png)

Se você adicionar a solução usando o portal do OMS, verá o seguinte bloco. Entre no portal do Azure para conectar uma assinatura do Azure ao seu espaço de trabalho.  
![executar avaliação](./media/log-analytics-activity/tile-performing-assessment.png)

## <a name="using-the-solution"></a>Usando a solução

Quando você adiciona a solução Log Analytics da atividade ao espaço de trabalho, o bloco **Logs de Atividade do Azure** é adicionado ao painel Visão geral. Esse bloco exibe uma contagem do número de registros de atividade do Azure para as assinaturas do Azure às quais a solução tem acesso.

![Bloco de logs de atividade do Azure](./media/log-analytics-activity/azure-activity-logs-tile.png)

### <a name="view-azure-activity-logs"></a>Exibir logs de atividade do Azure

Clique no bloco **Logs de Atividade do Azure** para abrir o painel **Logs de Atividade do Azure**. O painel inclui as folhas na tabela a seguir. Cada folha lista os 10 principais itens que correspondem aos critérios da folha para o escopo e o intervalo de tempo especificados. É possível executar uma pesquisa de logs que retorna todos os registros clicando em **Ver todos** na parte inferior da folha ou clicando no cabeçalho de folha.

Os dados do log de atividades aparecem apenas *depois* que você os configura para ir para a solução, portanto você não pode ver os dados antes disso.

| Folha | Descrição |
| --- | --- |
| Entradas de log de atividades do Azure | Mostra um gráfico de barras na parte superior dos totais de registro da entrada de log de atividades do Azure para o intervalo de datas selecionado e uma lista dos principais 10 chamadores da atividade. Clique no gráfico de barras para executar uma pesquisa de logs para <code>Type=AzureActivity</code>. Clique em um item do chamador para executar uma pesquisa de logs que retorna todas as entradas de log de atividades desse item. |
| Logs de atividade por status | Mostra um gráfico de rosca do status do log de atividades do Azure para o intervalo de datas que você selecionou. Também mostra uma lista dos dez principais registros de status. Clique no gráfico para executar uma pesquisa de logs para <code>Type=AzureActivity &#124; measure count() by ActivityStatus</code>. Clique em um item do chamador para executar uma pesquisa de logs que retorna todas as entradas de log de atividades desse registro de status. |
| Logs de atividade por recurso | Mostra o número total de recursos com logs de atividade e lista os dez principais recursos com contagem de registro para cada recurso. Clique na área total para executar uma pesquisa de logs de <code>Type=AzureActivity &#124; measure count() by Resource</code>, que mostra todos os recursos do Azure disponíveis para a solução. Clique em um recurso para executar uma pesquisa de logs que retorna que todos os registros de atividades desse recurso. |
| Logs de atividade por provedor de recursos | Mostra o número total de provedores de recursos que produzem logs da atividade e lista os dez principais. Clique na área total para executar uma pesquisa de logs de <code>Type=AzureActivity &#124; measure count() by ResourceProvider</code>, que mostra todos os provedores de recursos do Azure. Clique em um provedor de recursos para executar uma pesquisa de logs que retorna todos os registros de atividades desse provedor. |

![Painel Logs de Atividade do Azure](./media/log-analytics-activity/activity-log-dash.png)

## <a name="next-steps"></a>Próximas etapas

- Crie um [alerta](log-analytics-alerts-creating.md) quando ocorrer uma atividade específica.
- Use a [Pesquisa de Logs](log-analytics-log-searches.md) para exibir informações detalhadas de seus logs de atividade.
