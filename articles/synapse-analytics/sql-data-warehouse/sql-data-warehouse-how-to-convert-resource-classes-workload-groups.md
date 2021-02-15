---
title: Converter classe de recurso a um grupo de carga de trabalho
description: Saiba como criar um grupo de carga de trabalho semelhante a uma classe de recurso em um pool SQL dedicado.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 1207f4856882d8aa0e6d1e41712071536bfecf29
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98728549"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Converter classes de recursos em grupos de cargas de trabalho

Grupos de carga de trabalho fornecem um mecanismo para isolar e conter recursos do sistema.  Além disso, os grupos de carga de trabalho permitem definir regras de execução para as solicitações em execução nelas.  Uma regra de execução de tempo limite de consulta permite que consultas sem controle sejam canceladas sem intervenção do usuário.  Este artigo explica como usar uma classe de recurso existente e criar um grupo de carga de trabalho com uma configuração semelhante.  Além disso, uma regra de tempo limite de consulta opcional é adicionada.

> [!NOTE]
> Consulte a seção [Misturar atribuições de classe de recurso com classificadores](sql-data-warehouse-workload-classification.md#mixing-resource-class-assignments-with-classifiers) no documento de conceito [Classificação de carga de trabalho](sql-data-warehouse-workload-classification.md) para obter orientação sobre como usar grupos de carga de trabalho e classes de recursos ao mesmo tempo.

## <a name="understanding-the-existing-resource-class-configuration"></a>Reconhecimento da configuração da classe de recurso existente

Os grupos de carga de trabalho exigem um parâmetro chamado `REQUEST_MIN_RESOURCE_GRANT_PERCENT` que especifica a porcentagem de recursos gerais do sistema alocados por solicitação.  A alocação de recurso é feita para [classes de recursos](resource-classes-for-workload-management.md#what-are-resource-classes) alocando slots de simultaneidade.  Para determinar o valor a ser especificado para `REQUEST_MIN_RESOURCE_GRANT_PERCENT`, use o DMV sys.dm_workload_management_workload_groups_stats<link tbd>.  Por exemplo, a consulta de consulta abaixo retorna um valor que pode ser usado para o parâmetro `REQUEST_MIN_RESOURCE_GRANT_PERCENT` para criar um grupo de carga de trabalho semelhante a staticrc40.

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> Os grupos de carga de trabalho operam com base na porcentagem de recursos gerais do sistema.  

Como os grupos de carga de trabalho operam com base na porcentagem de recursos gerais do sistema, à medida que você escala e reduz verticalmente, a porcentagem de recursos alocados para classes de recursos estáticos em relação aos recursos gerais do sistema muda.  Por exemplo, staticrc40 em DW1000c aloca 19,2% dos recursos gerais do sistema.  Em DW2000c, 9,6% são alocados.  Esse modelo é semelhante se você quiser escalar verticalmente para simultaneidade em vez de alocar mais recursos por solicitação.

## <a name="create-workload-group"></a>Criar grupo de carga de trabalho

Com o `REQUEST_MIN_RESOURCE_GRANT_PERCENT` conhecido, você pode usar a sintaxe CREATE WORKLOAD GROUP <link> para criar o grupo de carga de trabalho.  Opcionalmente, é possível especificar um `MIN_PERCENTAGE_RESOURCE` maior que zero para isolar recursos para o grupo de carga de trabalho.  Além disso, você tem a opção de especificar `CAP_PERCENTAGE_RESOURCE` menor que 100 para limitar a quantidade de recursos que o grupo de carga de trabalho pode consumir.  

Usando mediumrc como base para um exemplo, o código abaixo define o `MIN_PERCENTAGE_RESOURCE` para dedicar 10% dos recursos do sistema ao `wgDataLoads` e garante que uma consulta poderá ser executada o tempo todo.  Além disso, `CAP_PERCENTAGE_RESOURCE` é definido como 40% e limita esse grupo de cargas de trabalho a quatro solicitações simultâneas.  Ao definir o parâmetro `QUERY_EXECUTION_TIMEOUT_SEC` como 3600, qualquer consulta executada por mais de 1 hora será cancelada automaticamente.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 10
 ,MIN_PERCENTAGE_RESOURCE = 10
 ,CAP_PERCENTAGE_RESOURCE = 40
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Crie o classificador

Anteriormente, o mapeamento de consultas para classes de recursos era feito com [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Para obter a mesma funcionalidade e mapear solicitações para grupos de carga de trabalho, use a sintaxe [CREATE WORKLOAD CLASSIFIER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  O uso de sp_addrolemember só permitia mapear recursos para uma solicitação com base em um logon.  Um classificador fornece opções adicionais além do logon, como:
    - label
    - sessão
    - hora O exemplo abaixo atribui consultas do logon `AdfLogin` que também têm o [OPTION LABEL](sql-data-warehouse-develop-label.md) definido como `factloads` para o grupo de carga de trabalho `wgDataLoads` criado acima.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## <a name="test-with-a-sample-query"></a>Teste com uma consulta de amostra

Veja abaixo uma consulta de amostra e uma consulta DMV para garantir que o grupo de carga de trabalho e o classificador estejam configurados corretamente.

```sql
SELECT SUSER_SNAME() --should be 'AdfLogin'

--change to a valid table AdfLogin has access to
SELECT TOP 10 *
  FROM nation
  OPTION (label='factloads')

SELECT request_id, [label], classifier_name, group_name, command
  FROM sys.dm_pdw_exec_requests
  WHERE [label] = 'factloads'
  ORDER BY submit_time DESC
```

## <a name="next-steps"></a>Próximas etapas

- [Isolamento de carga de trabalho](sql-data-warehouse-workload-isolation.md)
- [Como criar um grupo de carga de trabalho](quickstart-configure-workload-isolation-tsql.md)
- [CREATE WORKLOAD CLASSIFIER (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql??view=azure-sqldw-latest&preserve-view=true)
- [CREATE WORKLOAD GROUP (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest&preserve-view=true)
