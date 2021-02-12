---
title: Classificação de carga de trabalho para o pool SQL dedicado
description: Orientação para usar a classificação para gerenciar a simultaneidade de consulta, a importância e os recursos de computação para o pool SQL dedicado no Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 7cd3619aa60f1bd8ac13ff767857b44348989285
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678416"
---
# <a name="workload-classification-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Classificação de carga de trabalho para o pool SQL dedicado no Azure Synapse Analytics

Este artigo explica o processo de classificação de carga de trabalho de atribuição de um grupo de carga de trabalho e a importância para solicitações de entrada com pools SQL dedicados no Azure Synapse.

## <a name="classification"></a>classificação

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

A classificação de gerenciamento de carga de trabalho permite que as políticas de carga de trabalho sejam aplicadas a solicitações por meio da atribuição de [classes de recursos](resource-classes-for-workload-management.md#what-are-resource-classes) e [importância](sql-data-warehouse-workload-importance.md).

Embora haja muitas maneiras de classificar cargas de trabalho de data warehouse, a classificação mais simples e mais comum é a carga e a consulta. Você carrega dados com instruções INSERT, Update e Delete.  Você consulta os dados usando selects. Uma solução de data warehouse geralmente terá uma política de carga de trabalho para carregar a atividade, como atribuir uma classe de recursos mais alta com mais recursos. Uma política de carga de trabalho diferente pode ser aplicada a consultas, como importância menor em comparação com as atividades de carregamento.

Você também pode subclassificar suas cargas de trabalho de carregamento e consulta. A subclasse oferece mais controle sobre suas cargas de trabalho. Por exemplo, as cargas de trabalho de consulta podem consistir em atualizações de cubo, consultas de painel ou consultas ad hoc. Você pode classificar cada uma dessas cargas de trabalho de consulta com diferentes classes de recursos ou configurações de importância. A carga também pode se beneficiar da subclasse. Transformações grandes podem ser atribuídas a classes de recursos maiores. A maior importância pode ser usada para garantir que os dados de vendas de chave sejam carregador antes dos dados meteorológicos ou de um feed de dados social.

Nem todas as instruções são classificadas, pois não exigem recursos ou precisam de importância para influenciar a execução.  Os comandos DBCC, as instruções BEGIN, COMMIT e ROLLBACK TRANSACTION não são classificados.

## <a name="classification-process"></a>Processo de classificação

A classificação do pool SQL dedicado é obtida hoje atribuindo usuários a uma função que tenha uma classe de recurso correspondente atribuída a ele usando [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). A capacidade de caracterizar solicitações além de um logon para uma classe de recurso é limitada a esse recurso. Um método mais rico para classificação agora está disponível com a sintaxe de [criar classificação de carga de trabalho](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Com essa sintaxe, os usuários do pool do SQL dedicados podem atribuir importância e quantos recursos do sistema são atribuídos a uma solicitação por meio do `workload_group` parâmetro.

> [!NOTE]
> A classificação é avaliada em uma base por solicitação. Várias solicitações em uma única sessão podem ser classificadas de forma diferente.

## <a name="classification-weighting"></a>Peso da classificação

Como parte do processo de classificação, o peso está em vigor para determinar qual grupo de carga de trabalho é atribuído.  O peso é o seguinte:

|Parâmetro do classificador |Peso   |
|---------------------|---------|
|MEMBERNAME: USUÁRIO      |64       |
|MEMBERNAME: FUNÇÃO      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

O `membername` parâmetro é obrigatório.  No entanto, se o MemberName especificado for um usuário de banco de dados em vez de uma função de banco de dados, o peso do usuário será maior e, portanto, esse classificador será escolhido.

Se um usuário for um membro de várias funções com classes de recursos diferentes atribuídos ou correspondentes em vários classificadores, o usuário receberá a atribuição de classe de recurso mais alta.  Esse comportamento é consistente com o comportamento de atribuição de classe de recurso existente.

## <a name="system-classifiers"></a>Classificadores do sistema

Classificação de carga de trabalho tem classificadores de carga de trabalho do sistema. Os classificadores de sistema mapeiam associações de função de classe de recurso existentes para alocações de recursos de classe de recurso com importância normal. Classificadores de sistema não podem ser descartados. Para exibir classificadores de sistema, você pode executar a consulta abaixo:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Mesclando atribuições de classe de recurso com classificadores

Classificadores de sistema criados em seu nome fornecem um caminho fácil para migrar para a classificação de carga de trabalho. O uso de mapeamentos de função de classe de recurso com precedência de classificação pode levar a uma classificação incorreta conforme você começa a criar novos classificadores com importância.

Considere o seguinte cenário:

- Um data warehouse existente tem um usuário de banco de dados DBAUser atribuído à função de classe de recurso largerc. A atribuição de classe de recurso foi feita com sp_addrolemember.
- O data warehouse agora é atualizado com o gerenciamento de carga de trabalho.
- Para testar a nova sintaxe de classificação, a função de banco de dados DBARole (que DBAUser é membro de), tem um classificador criado para que elas sejam mapeadas para mediumrc e alta importância.
- Quando o DBAUser faz logon e executa uma consulta, a consulta será atribuída a largerc. Porque um usuário tem precedência sobre uma associação de função.

Para simplificar a solução de problemas de classificação indesejada, recomendamos que você remova os mapeamentos de função de classe de recurso ao criar classificadores de carga de trabalho.  O código a seguir retorna associações de função de classe de recurso existentes.  Execute [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para cada nome de membro retornado da classe de recurso correspondente.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember '[Resource Class]', membername
```

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre como criar um classificador, consulte [criar classificação de carga de trabalho (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  
- Consulte o início rápido sobre como criar um classificador de carga de trabalho [criar um classificador de carga de trabalho](quickstart-create-a-workload-classifier-tsql.md).
- Consulte os artigos de instruções para [Configurar a importância da carga de trabalho](sql-data-warehouse-how-to-configure-workload-importance.md) e como [gerenciar e monitorar o gerenciamento de carga de trabalho](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Confira [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para exibir consultas e a importância atribuída.
