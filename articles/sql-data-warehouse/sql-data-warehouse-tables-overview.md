---
title: "Visão geral das tabelas no SQL Data Warehouse | Microsoft Docs"
description: "Introdução às Tabelas do SQL Data Warehouse do Azure."
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: jhubbard
editor: 
ms.assetid: 2114d9ad-c113-43da-859f-419d72604bdf
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 06/29/2016
ms.author: shigu;jrj
ms.openlocfilehash: c16fef2f302dbc56f257eaf2f0d2b68b6a3c1852
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-tables-in-sql-data-warehouse"></a>Visão geral das tabelas no SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Visão geral][Overview]
> * [Tipos de Dados][Data Types]
> * [Distribuir][Distribute]
> * [Índice][Index]
> * [Partição][Partition]
> * [Estatísticas][Statistics]
> * [Temporário][Temporary]
> 
> 

A introdução à criação de tabelas no SQL Data Warehouse é simples.  A sintaxe básica de [CREATE TABLE][CREATE TABLE] segue a sintaxe comum que provavelmente você já conhece de trabalhar com outros bancos de dados.  Para criar uma tabela, basta nomear sua tabela, nomear suas colunas e definir os tipos de dados para cada coluna.  Se você criou tabelas em outros bancos de dados, isso deve ser bastante familiar.

```sql  
CREATE TABLE Customers (FirstName VARCHAR(25), LastName VARCHAR(25))
 ``` 

O exemplo acima cria uma tabela denominada Clientes com duas colunas, FirstName e LastName.  Cada coluna é definida com um tipo de dados VARCHAR (25), que limita os dados a 25 caracteres.  Esses atributos fundamentais de uma tabela, bem como outros, são em grande parte os mesmos dos outros bancos de dados.  Os tipos de dados são definidos para cada coluna e garantem a integridade dos dados.  Os índices podem ser adicionados para melhorar o desempenho reduzindo a E/S.  O particionamento pode ser adicionado para melhorar o desempenho quando você precisa modificar os dados.

[Renomear][RENAME] uma tabela do SQL Data Warehouse fica assim:

```sql  
RENAME OBJECT Customer TO CustomerOrig; 
 ```

## <a name="distributed-tables"></a>Tabelas distribuídas
Um novo atributo fundamental introduzido pelos sistemas distribuídos, como o SQL Data Warehouse, é a **coluna de distribuição**.  A coluna de distribuição é o que parece ser.  É a coluna que determina como distribuir ou dividir seus dados nos bastidores.  Quando você cria uma tabela sem especificar a coluna de distribuição, a tabela é automaticamente distribuída usando **round robin**.  Embora as tabelas round robin possam ser suficientes em alguns cenários, definir colunas de distribuição pode reduzir muito a movimentação dos dados durante as consultas, otimizando o desempenho.  Em situações em que há um pequeno volume de dados em uma tabela, optar por criar a tabela com o tipo de distribuição de **replicação** significa copiar dados para cada nó de computação e salvar a movimentação dos dados no tempo de execução da consulta. Confira [Distribuir uma Tabela][Distribute] para saber mais sobre como selecionar uma coluna de distribuição.

## <a name="indexing-and-partitioning-tables"></a>Indexação e particionamento de tabelas
Conforme você se tornar mais avançado usando o SQL Data Warehouse e quiser otimizar o desempenho, desejará saber mais sobre o Design da Tabela.  Para saber mais, consulte os artigos sobre [Tipos de dados da tabela][Data Types], [Distribuir uma tabela][Distribute], [Indexar uma tabela][Index] e [Particionar uma tabela][Partition].

## <a name="table-statistics"></a>Estatísticas da tabela
As estatísticas são extremamente importantes para obter o melhor desempenho do SQL Data Warehouse.  Já que o SQL Data Warehouse ainda não cria nem atualiza automaticamente as estatísticas tal como você talvez já espere que ocorra no Banco de Dados SQL do Azure, a leitura de nosso artigo em [Estatísticas][Statistics] poderá ser uma das mais importantes para garantir que você obtenha o melhor desempenho de suas consultas.

## <a name="temporary-tables"></a>Tabelas temporárias
Tabelas temporárias são as tabelas que só existem durante o logon e não podem ser vistas por outros usuários.  As tabelas temporárias podem ser uma boa maneira de evitar que outras pessoas vejam os resultados temporários e também reduzem a necessidade de limpeza.  Como as tabelas temporárias também utilizam o armazenamento local, podem oferecer um desempenho mais rápido para algumas operações.  Confira o artigo [Tabelas Temporárias][Temporary] para obter mais detalhes sobre elas.

## <a name="external-tables"></a>Tabelas externas
Tabelas externas, também conhecidas como tabelas Polybase, são tabelas que podem ser consultadas no SQL Data Warehouse, mas apontam para os dados externos do SQL Data Warehouse.  Por exemplo, você pode criar uma tabela externa que aponta para os arquivos no Armazenamento de Blobs do Azure.  Para obter mais detalhes sobre como criar e consultar uma tabela externa, confira [Carregar dados com o Polybase][Load data with Polybase].  

## <a name="unsupported-table-features"></a>Recursos da tabela sem suporte
Embora o SQL Data Warehouse contenha muitos dos mesmos recursos da tabela oferecidos por outros bancos de dados, há alguns recursos que ainda não têm suporte.  Abaixo, está uma lista de alguns dos recursos da tabela que ainda não têm suporte.

| Recursos sem suporte |
| --- |
| Chave primária, Chaves estrangeiras, Exclusiva e Verificação [Restrições da Tabela][Table Constraints] |
| [Índices Exclusivos][Unique Indexes] |
| [Colunas Computadas][Computed Columns] |
| [Colunas Esparsas][Sparse Columns] |
| [Tipos Definidos pelo Usuário][User-Defined Types] |
| [Sequência][Sequence] |
| [Gatilhos][Triggers] |
| [Exibições Indexadas][Indexed Views] |
| [Sinônimos][Synonyms] |

## <a name="table-size-queries"></a>Consultas do tamanho da tabela
Uma maneira simples de identificar o espaço e as linhas consumidas por uma tabela em cada uma das 60 distribuições é usar [DBCC PDW_SHOWSPACEUSED][DBCC PDW_SHOWSPACEUSED].

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

No entanto, usar comandos DBCC pode ser bastante limitado.  As exibições de gerenciamento dinâmico (DMVs) permitirão que você confira muito mais detalhes, bem como oferecem muito mais controle sobre os resultados da consulta.  Comece criando essa exibição, que será referenciada por muitos dos nossos exemplos neste e em outros artigos.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT 
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count] 
    + nps.[row_overflow_used_page_count] 
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count] 
 - (nps.[reserved_page_count] - nps.[used_page_count]) 
 - ([in_row_data_page_count] 
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from 
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT * 
FROM size
;
```

### <a name="table-space-summary"></a>Resumo do espaço da tabela
Esta consulta retorna as linhas e o espaço por tabela.  Essa é uma ótima consulta ver quais tabelas são as maiores e se elas são distribuídas em round robin, replicadas ou em hash.  Para as tabelas de hash distribuídas, também mostra a coluna de distribuição.  Na maioria dos casos, suas tabelas maiores devem ser distribuídas em hash com um índice columnstore clusterizado.

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM 
    dbo.vTableSizes
GROUP BY 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Espaço da tabela pelo tipo de distribuição
```sql
SELECT 
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Espaço da tabela pelo tipo de índice
```sql
SELECT 
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Resumo do espaço de distribuição
```sql
SELECT 
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte os artigos [Table Data Types][Data Types] (Tipos de Dados da Tabela), [Distributing a Table][Distribute] (Distribuir uma Tabela), [Indexing a Table][Index] (Indexar uma Tabela), [Partitioning a Table][Partition] (Particionar uma Tabela), [Maintaining Table Statistics][Statistics] (Manter Estatísticas de Tabela) e [Temporary Tables][Temporary] (Tabelas Temporárias).  Para saber mais sobre as práticas recomendadas, consulte [Práticas Recomendadas do SQL Data Warehouse][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md
[Load data with Polybase]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md

<!--MSDN references-->
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[Table Constraints]: https://msdn.microsoft.com/library/ms188066.aspx
[Computed Columns]: https://msdn.microsoft.com/library/ms186241.aspx
[Sparse Columns]: https://msdn.microsoft.com/library/cc280604.aspx
[User-Defined Types]: https://msdn.microsoft.com/library/ms131694.aspx
[Sequence]: https://msdn.microsoft.com/library/ff878091.aspx
[Triggers]: https://msdn.microsoft.com/library/ms189799.aspx
[Indexed Views]: https://msdn.microsoft.com/library/ms191432.aspx
[Synonyms]: https://msdn.microsoft.com/library/ms177544.aspx
[Unique Indexes]: https://msdn.microsoft.com/library/ms188783.aspx

<!--Other Web references-->
