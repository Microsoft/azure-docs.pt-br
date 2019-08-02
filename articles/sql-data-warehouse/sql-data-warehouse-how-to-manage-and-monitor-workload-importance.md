---
title: Gerenciar e monitorar a importância da carga de trabalho no Azure SQL Data Warehouse | Microsoft Docs
description: Saiba como gerenciar e monitorar a importância de nível de solicitação.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 30afe1805748012b0a137c865c799580f79d31d8
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588654"
---
# <a name="manage-and-monitor-workload-importance-in-azure-sql-data-warehouse"></a>Gerenciar e monitorar a importância da carga de trabalho no Azure SQL Data Warehouse

Gerenciar e monitorar a importância de nível de solicitação no Azure SQL Data Warehouse usando exibições de catálogo e DMVs.

## <a name="monitor-importance"></a>Importância do monitor

Monitorar a importância usando a nova coluna de importância na [DM pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) exibição de gerenciamento dinâmico.
O abaixo de consulta mostra a hora de enviar e hora de início para consultas de monitoramento. Examine o tempo de envio e hora junto com a importância para ver como a importância influenciou agendamento de início.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Para pesquisar ainda mais em como as consultas estão sendo agenda, use as exibições do catálogo.

## <a name="manage-importance-with-catalog-views"></a>Gerenciar a importância com exibições de catálogo

A exibição do catálogo sys.workload_management_workload_classifiers contém informações sobre os classificadores na sua instância do SQL Data Warehouse do Azure. Para excluir os classificadores definida pelo sistema que são mapeados para classes de recursos execute o seguinte código:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

O modo de exibição do catálogo [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest), contém informações sobre os parâmetros usados na criação do classificador.  O abaixo consulta mostra que ExecReportsClassifier foi criado o ```membername``` parâmetro para valores com ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![Resultados da consulta](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Para simplificar a classificação incorreta de solução de problemas, recomendamos que você remover mapeamentos de função de classe de recurso que você crie classificadores de carga de trabalho. O código a seguir retorna as associações de função de classe de recurso existente. Execute sp_droprolemember para cada ```membername``` retornado da classe de recurso correspondente.
Abaixo está um exemplo de verificação de existência antes de descartar um classificador de carga de trabalho:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Próximas etapas
- Para obter mais informações sobre classificação, consulte [carga de trabalho de classificação](sql-data-warehouse-workload-classification.md).
- Para obter mais informações sobre a importância, consulte [importância da carga de trabalho](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Ir para configurar a importância da carga de trabalho](sql-data-warehouse-how-to-configure-workload-importance.md)
