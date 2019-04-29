---
title: Carga de trabalho de monitor - portal do Azure | Microsoft Docs
description: Monitorar o Azure SQL Data Warehouse usando o portal do Azure
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 6c8ce090039e3d5cc85c86d920710294de2165f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60748781"
---
# <a name="monitor-workload---azure-portal"></a>Carga de trabalho de monitor - portal do Azure

Este artigo descreve como usar o portal do Azure para monitorar sua carga de trabalho. Isso inclui configurar Logs do Azure Monitor para investigar as tendências de carga de trabalho e a execução de consulta usando o log analytics para [do Azure SQL Data Warehouse](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- SQL Data Warehouse do Azure: Podemos coletará os logs para um SQL data warehouse. Se você não tiver provisionado um SQL data warehouse, consulte as instruções em [criar um SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial).

## <a name="create-a-log-analytics-workspace"></a>Criar um espaço de trabalho do Log Analytics

Navegue até a folha Procurar para espaços de trabalho do Log Analytics e crie um espaço de trabalho 

![Workspaces do Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![Adicionar espaço de trabalho de análise](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![Adicionar espaço de trabalho de análise](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

Para obter mais detalhes sobre espaços de trabalho, visite o seguinte [documentação](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#create-a-workspace).

## <a name="turn-on-diagnostic-logs"></a>Ativar os logs de diagnóstico 

Defina configurações de diagnóstico para emitir logs do seu SQL data warehouse. Logs consistem em modos de exibição de telemetria do seu data warehouse equivalente ao desempenho mais comumente usado DMVs de solução de problemas do SQL Data Warehouse. Atualmente, há suporte para as seguintes exibições:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![Habilitar logs de diagnóstico](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

Os logs podem ser emitidos para o armazenamento do Azure, o Stream Analytics ou o Log Analytics. Para este tutorial, selecione o Log Analytics.

![Especificar os logs](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Executar consultas no Log Analytics

Navegue até seu espaço de trabalho do Log Analytics em que você pode fazer o seguinte:

- Analisar logs usando consultas de log e salvar consultas para reutilização
- Salvar consultas para reutilização
- Criar alertas do log
- Resultados da consulta PIN em um painel

Para obter detalhes sobre os recursos de consultas de log, visite o seguinte [documentação](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

![Editor de espaço de trabalho do log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Consultas de espaço de trabalho de análise de log](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Exemplos de consultas de log



```Kusto
//List all queries 
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```
```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart 
```
```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits" 
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```
## <a name="next-steps"></a>Próximas etapas

Agora que você tiver instalado e configurado logs do Azure monitor [personalizar painéis do Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) para compartilhar com sua equipe.
