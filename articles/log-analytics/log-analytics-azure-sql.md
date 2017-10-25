---
title: "Solução de Análise do Azure SQL no Log Analytics | Microsoft Docs"
description: "A solução de Análise do Azure SQL ajuda a gerenciar os bancos de dados do Azure SQL."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: banders
ms.openlocfilehash: 0b0d91b130172eb3506fdebb9547ab6ba5cc3780
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview-in-log-analytics"></a>Monitorar o Banco de Dados SQL do Azure usando a Análise do Azure SQL (Visualização) no Log Analytics

![Símbolo da Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-symbol.png)

A solução Análise de SQL do Azure do Azure Log Analytics coleta e visualiza métricas de desempenho importantes do SQL Azure. Usando as métricas que coleta com a solução, você pode criar alertas e regras de monitoramentos personalizadas. E você pode monitorar o Banco de Dados SQL e métricas de pool elástico em várias assinaturas do Azure e pools elásticos e visualizá-los. A solução também ajuda a identificar problemas em cada camada da pilha do aplicativo.  Ele usa [métricas de Diagnóstico do Azure](log-analytics-azure-storage.md) junto com modos de exibição de Log Analytics para apresentar dados sobre todos os bancos de dados do Azure SQL e pools elásticos em um único espaço de trabalho de Log Analytics.

Atualmente, essa solução de visualização dá suporte a até 5.000 Bancos de Dados do Azure SQL e 150.000 Pools Elásticos por espaço de trabalho.

A solução Análise de SQL do Azure, assim como outras disponíveis para o Log Analytics, ajuda você a monitorar e receber notificações sobre a integridade dos recursos do Azure – neste caso, o Banco de Dados SQL do Azure. O Banco de Dados SQL do Microsoft Azure é um serviço de banco de dados relacional escalonável que fornece recursos semelhantes aos recursos familiares do SQL Server para aplicativos em execução na nuvem do Azure. O Log Analytics ajuda a coletar, correlacionar e visualizar dados estruturados e não estruturados.

## <a name="connected-sources"></a>Fontes conectadas

A solução Análise de SQL do Azure não usa agentes para se conectar ao serviço Log Analytics.

A tabela a seguir descreve as fontes conectadas que têm suporte dessa solução.

| Fonte Conectada | Suporte | Descrição |
| --- | --- | --- |
| [Agentes do Windows](log-analytics-windows-agents.md) | Não | Agentes diretos do Windows não são usados pela solução. |
| [Agentes do Linux](log-analytics-linux-agents.md) | Não | Agentes do Linux diretos não são usados pela solução. |
| [Grupo de gerenciamento do SCOM](log-analytics-om-agents.md) | Não | Uma conexão direta do agente do SCOM ao Log Analytics não é usada pela solução. |
| [Conta de armazenamento do Azure](log-analytics-azure-storage.md) | Não | O Log Analytics não lê os dados pré-existentes de uma conta de armazenamento. |
| [Diagnóstico do Azure](log-analytics-azure-storage.md) | Sim | Os dados de métrica e de log do Azure são enviados ao Log Analytics diretamente pelo Azure. |

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se não tiver uma, você poderá criá-la [grátis](https://azure.microsoft.com/free/).
- Um espaço de trabalho do Log Analytics. Você pode usar um existente ou pode [criar um novo](log-analytics-get-started.md) para começar a usar essa solução.
- Habilite o Diagnóstico do Azure para os bancos de dados do Azure SQL e pools elásticos e [configure-os para enviar os dados para o Log Analytics](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/).

## <a name="configuration"></a>Configuração

Realize as etapas a seguir para adicionar a solução Análise de SQL do Azure ao seu espaço de trabalho.

1. Adicione a solução de análise do SQL do Azure do [marketplace do Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview) ou usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md).
2. No portal do Azure, clique em **Novo** (o símbolo +) e, na lista de recursos, selecione **Monitoramento + Gerenciamento**.  
    ![Monitoramento + Gerenciamento](./media/log-analytics-azure-sql/monitoring-management.png)
3. Na lista **Monitoramento + Gerenciamento**, clique em **Ver todos**.
4. Na lista **Recomendado**, clique em **Mais** e, na nova lista, localize **Análise do Azure SQL (Visualização)** e selecione-a.  
    ![Solução de Análise do Azure SQL](./media/log-analytics-azure-sql/azure-sql-solution-portal.png)
5. No painel **Análise de SQL do Azure (Versão prévia)**, clique em **Criar**.  
    ![Criar](./media/log-analytics-azure-sql/portal-create.png)
6. No painel **Criar nova solução**, selecione o espaço de trabalho ao qual você deseja adicionar a solução e clique em **Criar**.  
    ![adicionar ao espaço de trabalho](./media/log-analytics-azure-sql/add-to-workspace.png)


### <a name="to-configure-multiple-azure-subscriptions"></a>Para configurar várias assinaturas do Azure

Para dar suporte a várias assinaturas, use o script do PowerShell de [Habilitar log de métricas de recursos do Azure usando o PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/). Forneça a ID de recurso do espaço de trabalho como um parâmetro ao executar o script para enviar dados de diagnóstico de recursos em uma assinatura do Azure a um espaço de trabalho em outra assinatura do Azure.

**Exemplo**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>Usando a solução

>[!NOTE]
> Atualize seu espaço de trabalho do Log Analytics para obter a última versão da Análise de SQL do Azure.
>

Quando você adiciona a solução ao espaço de trabalho, o bloco de Análise do Azure SQL é adicionado ao espaço de trabalho e aparece na Visão geral. O bloco mostra o número de bancos de dados do Azure SQL e pools elásticos do Azure SQL aos quais a solução está conectada.

![Bloco de Análise do SQL Azure](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>Exibindo dados da Análise de SQL do Azure

Clique no bloco **Análise de SQL do Azure** para abrir o painel da Análise de SQL do Azure. O painel inclui a visão geral de todos os bancos de dados monitorados por meio de diferentes perspectivas. Para que as diferentes perspectivas funcionem, é necessário permitir que métricas ou logs adequados em seus recursos SQL sejam transmitidos para o espaço de trabalho do Azure Log Analytics. 

![Visão geral da Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

Selecionar qualquer bloco abre um relatório de detalhamento sobre a perspectiva específica.

![Tempos limite da Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-timeouts.png)

Cada perspectiva fornece resumos sobre a assinatura, o servidor, o pool elástico e o nível de banco de dados. Além disso, cada perspectiva mostra o relatório específico da perspectiva à direita. Selecionar a assinatura, o servidor, o pool ou o banco de dados na lista continua o drill down.

| Perspectiva | Descrição |
| --- | --- |
| Recurso por tipo | Perspectiva que conta todos os recursos monitorados. O drill down fornece um resumo das métricas DTU e GB. |
| Insights | Fornece o drill down hierárquico para Insights inteligentes. Saiba mais sobre insights inteligentes. |
| Errors | Fornece drill down hierárquico sobre erros do SQL que ocorreram nos bancos de dados. |
| Tempos limite | Fornece drill down hierárquico sobre tempos limite do SQL que ocorreram nos bancos de dados. |
| Bloqueios | Fornece drill down hierárquico sobre bloqueios do SQL que ocorreram nos bancos de dados. |
| Esperas do banco de dados | Fornece drill down hierárquico sobre estatísticas de espera do SQL sobre o nível do banco de dados. Inclui resumos de tempo de espera total e o tempo de espera por tipo de espera. |
| Duração da consulta | Fornece drill down hierárquico sobre as estatísticas de execução de consulta como duração da consulta, uso da CPU, uso de E/S dos dados, uso de E/S do log. |
| Esperas da consulta | Fornece drill down hierárquico sobre as estatísticas de espera da consulta por categoria de espera. |

### <a name="intelligent-insights-report"></a>Relatório de Insights inteligentes

Todos os insights inteligentes coletados podem ser visualizados e acessados por meio da perspectiva de Insights. [Clique aqui para saber mais sobre Insights inteligentes](../sql-database/sql-database-intelligent-insights.md)

![Insights de Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Relatórios do pool elástico e do banco de dados

Os pools elásticos e os bancos de dados têm seus próprios relatórios específicos que mostram todos os dados coletados para o recurso no horário especificado.

![Banco de dados de Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Pool Elástico de Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Relatórios de consulta

Por meio da duração da consulta e da perspectiva de esperas de consulta, é possível correlacionar o desempenho de qualquer consulta por meio do relatório de consulta. Este relatório compara o desempenho de consultas em bancos de dados diferentes e torna mais fácil identificar bancos de dados que executam a consulta selecionada em comparação com as lentas.

![Consultas de Análise de SQL do Azure](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>Analisar dados e criar alertas

Você pode criar facilmente alertas com os dados provenientes de recursos de Banco de Dados SQL do Azure. Aqui estão algumas das consultas de [pesquisa de logs](log-analytics-log-searches.md) úteis que você pode usar para alertas:

*DTU alta no Banco de Dados SQL do Azure*

```
AzureMetrics | where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/DATABASES/" and MetricName=="dtu_consumption_percent" | summarize avg(Maximum) by ResourceId
```

*Alta DTU no pool elástico do Banco de Dados SQL do Azure*

```
AzureMetrics | where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/ELASTICPOOLS/" and MetricName=="dtu_consumption_percent" | summarize avg(Maximum) by ResourceId
```

Você pode usar essas consultas com base no alerta para alertar sobre limites específicos para o Banco de Dados SQL do Azure e pools elásticos. Para configurar um alerta para seu espaço de trabalho OMS:

#### <a name="to-configure-an-alert-for-your-workspace"></a>Para configurar um alerta para seu espaço de trabalho

1. Acesse o [portal do OMS](http://mms.microsoft.com/) e entre.
2. Abra o espaço de trabalho que você configurou para a solução.
3. Na página Visão geral, clique no bloco **Análise do Azure SQL (Visualização)**.
4. Execute uma das consultas de exemplo.
5. Na Pesquisa de Log, clique em **Alerta**.  
![criar alerta na pesquisa](./media/log-analytics-azure-sql/create-alert01.png)
6. Na página **Adicionar Regra de Alerta**, defina as propriedades adequadas e os limites específicos que você deseja e clique em **Salvar**.  
![adicionar regra de alerta](./media/log-analytics-azure-sql/create-alert02.png)

## <a name="see-also"></a>Consulte também

- Usar [Pesquisas de Log](log-analytics-log-searches.md) no Log Analytics para exibir dados detalhados do Azure SQL.
- [Criar seus próprios painéis](log-analytics-dashboards.md) mostrando os dados do Azure SQL.
- [Criar alertas](log-analytics-alerts.md) quando ocorrerem eventos específicos do Azure SQL.
