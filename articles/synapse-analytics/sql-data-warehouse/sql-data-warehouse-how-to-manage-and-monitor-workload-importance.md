---
title: Gerenciar e monitorar a importância da carga de trabalho no pool SQL dedicado
description: Saiba como gerenciar e monitorar o pool SQL dedicado de importância de nível de solicitação para a análise de Synapse do Azure.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 12b75ad3746cd0f54e27e474e0fd13bb0bba0e05
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685235"
---
# <a name="manage-and-monitor-workload-importance-in-dedicated-sql-pool-for-azure-synapse-analytics"></a>Gerenciar e monitorar a importância da carga de trabalho no pool SQL dedicado para o Azure Synapse Analytics

Gerencie e monitore a importância do nível de solicitação do pool SQL dedicado no Azure Synapse usando DMVs e exibições de catálogo.

## <a name="monitor-importance"></a>Importância do monitor

Monitore a importância usando a coluna nova importância na exibição de gerenciamento dinâmico [Sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .
A consulta de monitoramento abaixo mostra o tempo de envio e a hora de início das consultas. Examine a hora de envio e a hora de início junto com a importância para ver como a importância influencia o agendamento.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Para ver mais detalhadamente como as consultas estão sendo agendadas, use as exibições do catálogo.

## <a name="manage-importance-with-catalog-views"></a>Gerenciar importância com exibições de catálogo

A exibição de catálogo sys.workload_management_workload_classifiers contém informações sobre classificadores. Para excluir os classificadores definidos pelo sistema que são mapeados para classes de recurso, execute o seguinte código:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

A exibição de catálogo, [Sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), contém informações sobre os parâmetros usados na criação do classificador.  A consulta abaixo mostra que ExecReportsClassifier foi criado no ```membername``` parâmetro para valores com ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![resultados da consulta](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Para simplificar a solução de problemas de classificação indesejada, recomendamos que você remova os mapeamentos de função de classe de recurso ao criar classificadores de carga de trabalho. O código a seguir retorna associações de função de classe de recurso existentes. Execute sp_droprolemember para cada ```membername``` retorno da classe de recurso correspondente.
Abaixo está um exemplo de verificação de existência antes de remover um classificador de carga de trabalho:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre classificação, consulte [classificação de carga de trabalho](sql-data-warehouse-workload-classification.md).
- Para obter mais informações sobre Importância, consulte [Importância de Carga de Trabalho](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Ir para configurar a importância da carga de trabalho](sql-data-warehouse-how-to-configure-workload-importance.md)
