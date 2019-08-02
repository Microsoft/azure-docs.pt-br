---
title: Notas de versão do SQL Data Warehouse do Azure de junho de 2018 | Microsoft Docs
description: Notas de versão do SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/23/2018
ms.author: anjangsh
ms.reviewer: jrasnick
ms.openlocfilehash: 4348a634fd5b2b33f36d8e79f28caf659b82ccf4
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626143"
---
# <a name="whats-new-in-azure-sql-data-warehouse-june-2018"></a>O que há de novo no SQL Data Warehouse do Azure? Junho de 2018
O SQL Data Warehouse do Azure recebe melhorias continuamente. Este artigo descreve os novos recursos e alterações que foram introduzidas em junho de 2018. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="user-defined-restore-points"></a>Pontos de Restauração Definido Pelo Usuário
O SQL Data Warehouse cria automaticamente instantâneos de seu data warehouse a cada oito horas, garantindo um RPO (objetivo de ponto de recuperação) de oito horas. Embora esses instantâneos automatizados facilitem o gerenciamento da execução de seu data warehouse, é necessário tirar instantâneos em momentos críticos com base em suas necessidades comerciais. Por exemplo, tirar um instantâneo imediatamente antes de um carregamento considerável de dados ou da implantação de novos scripts no data warehouse para habilitar um ponto de restauração logo antes da operação. 

SQL Data Warehouse agora dá suporte a [pontos de restauração definido pelo usuário](https://azure.microsoft.com/blog/quick-recovery-time-with-sql-data-warehouse-using-user-defined-restore-points/) por meio de [AzSqlDatabaseRestorePoint New](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint) cmdlet.

```powershell
New-AzSqlDatabaseRestorePoint
    -ResourceGroupName $ResourceGroupName
    -ServerName $ServerName
    -DatabaseName $DatabaseName
    -RestorePointLabel $RestorePointName
```

## <a name="column-level-security"></a>Segurança no nível da coluna
O gerenciamento de acesso a dados e segurança em seu data warehouse é essencial para a criação de uma relação de confiança com seus clientes e parceiros. Agora, o SQL Data Warehouse [dá suporte a CLS (segurança no nível da coluna)](https://azure.microsoft.com/blog/column-level-security-is-now-supported-in-azure-sql-data-warehouse/) que permite o ajuste de permissões para exibir dados confidenciais, limitando o acesso do usuário a colunas específicas em suas tabelas sem precisar refazer o design de seu data warehouse.

A CLS permite que você controle o acesso às colunas da tabela com base no contexto de execução do usuário ou na associação de grupo usando a instrução T-SQL padrão [GRANT](https://docs.microsoft.com/azure/sql-data-warehouse/column-level-security). A lógica de restrição de acesso está localizada na camada do banco de dados, em vez de estar longe dos dados em outro aplicativo, simplificando a implementação de segurança geral.


```sql
CREATE USER Nurse WITHOUT LOGIN;   
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO Nurse;   
EXECUTE AS USER = 'Nurse';
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12 
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="objectschemaname"></a>OBJECT_SCHEMA_NAME
A função [OBJECT_SCHEMA_NAME()](https://docs.microsoft.com/sql/t-sql/functions/object-schema-name-transact-sql) retorna o nome do esquema de banco de dados para objetos no escopo do esquema. Essa função se tornou comum em ferramentas de ETL ao realizar a validação de esquema de objeto. 

```sql
SELECT
    OBJECT_SCHEMA_NAME([object_id]) [table_schema]
    , [name]                        [table_name]
FROM
    [sys].[tables];
```

**Exemplo de resultados**
```
table_schema    | table_name
-----------------------------
dbo               customer
dbo               lineitem
dbo               nation
dbo               orders
```

## <a name="support-for-the-systimezoneinfo-view"></a>Suporte para o modo de exibição sys.time_zone_info
O modo de exibição [sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) retorna informações sobre os fusos horários com suporte no Azure SQL Data Warehouse.

```sql
SELECT * FROM [sys].[time_zone_info];
```

**Exemplo de resultados**
```
name                            | current_utc_offset | is_currently_dst
-------------------------------------------------------------------------
Pacific Standard Time (Mexico)    -07:00               1
US Mountain Standard Time         -07:00               0
Mountain Standard Time (Mexico)   -06:00               1
Central Standard Time             -05:00               1
```

## <a name="auto-stats-operations-appear-in-sysdmpdwexecrequests-behavior-change"></a>Operações de estatísticas automáticas aparecem em sys.dm_pdw_exec_requests (Alteração de comportamento)

Com a introdução de [Criar Estatísticas Automaticamente](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistic), o Azure SQL Data Warehouse gerará estatísticas para otimizar a execução da consulta. A versão de junho de 2018 adiciona a capacidade de monitorar quando as estatísticas são geradas automaticamente adicionando um registro ao modo de exibição [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) sempre que alguma operação [CREATE STATISTICS](https://docs.microsoft.com/sql/t-sql/statements/create-statistics-transact-sql) for executada.

```sql
SELECT
    [start_time]
    , [end_time]
    , [command]
FROM
    [sys].[dm_pdw_exec_requests]
WHERE
    [command] LIKE 'CREATE STATISTICS _WA_Sys%';
```
**Exemplo de resultados**
```
start_time                | end_time                | command
------------------------------------------------------------------------------------------------------------------------------
2018-06-06 19:06:26.173    2018-06-06 19:06:26.173    CREATE STATISTICS _WA_Sys_00000001_63D998CC ON dbo.LineItem(l_orderkey);
```

## <a name="next-steps"></a>Próximas etapas
Agora que você sabe um pouco sobre o SQL Data Warehouse, saiba como rapidamente [criar um SQL Data Warehouse][create a SQL Data Warehouse] . If you are new to Azure, you may find the [Azure glossary][Azure glossary] úteis à medida que encontrar nova terminologia. Ou, dê uma olhada em alguns desses outros Recursos do SQL Data Warehouse.  

* [Histórias de sucesso de clientes]
* [Blogs]
* [Solicitações de recursos]
* [Vídeos]
* [Blogs da Equipe Consultoria para Clientes]
* [Fórum Stack Overflow]
* [Twitter]


[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs da Equipe Consultoria para Clientes]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Histórias de sucesso de clientes]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Solicitações de recursos]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
