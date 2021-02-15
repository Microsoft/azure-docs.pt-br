---
title: Configurar a importância da carga de trabalho para o pool do SQL dedicado
description: Saiba como definir a importância do nível de solicitação no Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 38fb842cf90c110266f53b79a9ab2ef6157025b4
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98681278"
---
# <a name="configure-workload-importance-in-dedicated-sql-pool-for-azure-synapse-analytics"></a>Configurar a importância da carga de trabalho no pool de SQL dedicado para o Azure Synapse Analytics

A definição da importância no pool SQL dedicado para o Azure Synapse permite que você influencie o agendamento de consultas. As consultas com importância mais alta serão agendadas para serem executadas antes das queries com importância mais baixa. Para atribuir importância às consultas, é necessário criar um classificador de carga de trabalho.

## <a name="create-a-workload-classifier-with-importance"></a>Criar um Classificador de Carga de Trabalho com Importância

Normalmente, em um cenário de data warehouse, há usuários em um sistema ocupado que precisam realizar consultas com rapidez.  O usuário pode ser um executivo da empresa que precisa fazer relatórios ou um analista fazendo uma consulta adhoc. Para atribuir importância, você cria um classificador de carga de trabalho e a importância é atribuída a uma consulta.  Os exemplos abaixo usam a sintaxe [criar classificador de força de trabalho](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para criar dois classificadores. `Membername` pode ser um único usuário ou um grupo.  Para localizar usuários do pool SQL dedicados existentes, execute:

```sql
Select name from sys.sysusers
```

Para criar um classificador de carga de trabalho para um usuário com importância mais alta, execute:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = above_normal);
```

Para criar um classificador de carga de trabalho para um usuário realizando consultas adhoc com importância mais baixa, execute:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = below_normal);
```

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre o gerenciamento de carga de trabalho, consulte [Classificação de Carga de Trabalho](sql-data-warehouse-workload-classification.md)
- Para obter mais informações sobre Importância, consulte [Importância de Carga de Trabalho](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Acesse Gerenciar e monitorar a Importância da Carga de Trabalho](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
