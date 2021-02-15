---
title: Projetando tabelas
description: Introdução à criação de tabelas usando o pool dedicado do SQL no Azure Synapse Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/15/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: c55edbd24553189c11070999ddc5d3b3516f2d97
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737924"
---
# <a name="design-tables-using-dedicated-sql-pool-in-azure-synapse-analytics"></a>Projetar tabelas usando o pool SQL dedicado no Azure Synapse Analytics

Este artigo fornece os principais conceitos introdutórios para a criação de tabelas no pool SQL dedicado.

## <a name="determine-table-category"></a>Determinar a categoria da tabela

Um [esquema em estrela](https://en.wikipedia.org/wiki/Star_schema) organiza dados em tabelas de fatos e dimensões. Algumas tabelas são usadas para dados de integração ou de preparo antes de movê-las para uma tabela de fatos ou dimensões. Ao criar uma tabela, decida se os dados da tabela pertencem a uma tabela de integração, de dimensão ou de fato. Essa decisão informa a distribuição e a estrutura da tabela apropriadas.

- As **tabelas de fatos** contêm dados quantitativos que normalmente são gerados em um sistema transacional e, em seguida, são carregados no pool SQL dedicado. Por exemplo, uma empresa de varejo gera transações de vendas todos os dias e, em seguida, carrega os dados em uma tabela de fatos dedicada do pool SQL para análise.

- As **Tabelas de dimensões** contêm dados de atributo que podem ser alterados, mas essas alterações são raras. Por exemplo, um nome e endereço do cliente são armazenados em uma tabela de dimensões e atualizados somente quando o perfil do cliente é alterado. Para minimizar o tamanho de uma tabela de fatos grande, o nome e o endereço do cliente não precisam estar em todas as linhas de uma tabela de fatos. Em vez disso, a tabela de fatos e a tabela de dimensões podem compartilhar uma ID do cliente. Uma consulta pode unir as duas tabelas para associar o perfil e as transações de um cliente.

- As **Tabelas de integrações** oferecem um local para dados de preparo ou integração. Você pode criar uma tabela de integração como uma tabela regular, uma tabela externa ou uma tabela temporária. Por exemplo, é possível carregar dados em uma tabela de preparo, executar transformações nos dados de preparo e, em seguida, inserir os dados em uma tabela de produção.

## <a name="schema-and-table-names"></a>Nomes de tabelas e esquemas

Os esquemas são uma boa maneira de agrupar tabelas, usadas de maneira semelhante, em conjunto.  Se você estiver migrando vários bancos de dados de uma solução local para um pool SQL dedicado, funcionará melhor migrar todas as tabelas de fatos, dimensões e integração para um esquema em um pool SQL dedicado.

Por exemplo, você pode armazenar todas as tabelas no exemplo [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) do pool de SQL dedicado em um esquema chamado WWI. O código a seguir cria um [esquema definido pelo usuário](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) chamado WWI.

```sql
CREATE SCHEMA wwi;
```

Para mostrar a organização das tabelas no pool SQL dedicado, você pode usar fact, Dim e int como prefixos para os nomes de tabela. A tabela a seguir mostra alguns dos nomes de tabelas e esquema para WideWorldImportersDW.  

| WideWorldImportersDW table  | Tipo de tabela | Pool de SQL dedicado |
|:-----|:-----|:------|:-----|
| City | Dimensão | wwi.DimCity |
| Ordem | Fato | wwi.FactOrder |

## <a name="table-persistence"></a>Persistência da tabela

As tabelas armazenam dados permanentemente no armazenamento do Azure, temporariamente no armazenamento do Azure ou em um armazenamento de dados externo ao pool do SQL dedicado.

### <a name="regular-table"></a>Tabela regular

Uma tabela regular armazena dados no armazenamento do Azure como parte do pool SQL dedicado. A tabela e os dados persistem independentemente se há uma sessão aberta.  O exemplo a seguir cria uma tabela regular com duas colunas.

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Tabela temporária

Uma tabela temporária só existe durante a sessão. Você pode usar uma tabela temporária para impedir que outros usuários vejam resultados temporários e também para reduzir a necessidade de limpeza.  

As tabelas temporárias utilizam o armazenamento local para oferecer um desempenho rápido.  Para obter mais informações, confira [Tabelas temporárias](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Tabela externa

Uma tabela externa aponta para dados localizados no Azure Storage Blob ou Azure Data Lake Store. Quando usado em conjunto com a instrução CREATE TABLE AS SELECT, a seleção de uma tabela externa importa dados para o pool SQL dedicado.

Dessa forma, as tabelas externas são úteis para carregar dados. Para obter um tutorial de carregamento, consulte [usar o polybase para carregar dados do armazenamento de BLOBs do Azure](./load-data-from-azure-blob-storage-using-copy.md).

## <a name="data-types"></a>Tipos de dados

O pool SQL dedicado oferece suporte aos tipos de dados usados com mais frequência. Para obter uma lista dos tipos de dados com suporte, consulte [tipos de dados na referência CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json?view=azure-sqldw-latest&preserve-view=true#datatypes) na instrução CREATE TABLE. Para obter diretrizes sobre o uso dos tipos de dados, consulte [Tipos de dados](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Tabelas distribuídas

Um recurso fundamental do pool dedicado do SQL é a maneira como ele pode armazenar e operar em tabelas entre [distribuições](massively-parallel-processing-mpp-architecture.md#distributions).  O pool SQL dedicado dá suporte a três métodos de distribuição de dados: Round Robin (padrão), hash e replicado.

### <a name="hash-distributed-tables"></a>Tabelas distribuídas em hash

Uma tabela distribuída por hash distribui linhas com base no valor na coluna de distribuição. Uma tabela distribuída por hash foi projetada para alcançar alto desempenho para consultas em tabelas grandes. Há vários fatores a serem considerados ao escolher uma coluna de distribuição.

Para obter mais informações, consulte [Criação de diretrizes para tabelas distribuídas](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Tabelas replicadas

Uma tabela replicada possui uma cópia completa da tabela disponível em cada nó de Computação. As consultas são executadas rapidamente em tabelas replicadas, pois as junções em tabelas replicadas não exigem a movimentação de dados. No entanto, a replicação requer armazenamento extra e não é prática para tabelas grandes.

Para obter mais informações, confira [Criação de diretrizes para tabelas replicadas](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Tabelas round robin

Uma tabela round robin distribui linhas de tabela uniformemente em todas as distribuições. As linhas são distribuídas aleatoriamente. Carregar dados em uma tabela round robin é rápido.  Tenha em mente que as consultas podem exigir mais movimentação de dados do que os outros métodos de distribuição.

Para obter mais informações, consulte [Criação de diretrizes para tabelas distribuídas](sql-data-warehouse-tables-distribute.md).

### <a name="common-distribution-methods-for-tables"></a>Métodos de distribuição comuns para tabelas

A categoria da tabela geralmente determina qual opção escolher para a distribuição da tabela.

| Categoria de tabela | Opção de distribuição recomendada |
|:---------------|:--------------------|
| Fato           | Use a distribuição de hash com índice columnstore clusterizado. O desempenho melhora quando duas tabelas de hash são unidas na mesma coluna de distribuição. |
| Dimensão      | Use a replicada para tabelas menores. Se as tabelas forem grandes demais para serem armazenadas em cada nó de computação, use a distribuição de hash. |
| De preparo        | Use um round robin para a tabela de preparo. A carga com CTAS é rápida. Depois que os dados estiverem na tabela de preparo, use INSERT... Selecione para mover os dados para tabelas de produção. |

## <a name="table-partitions"></a>Partições de tabela

Uma tabela particionada armazena e executa operações nas linhas da tabela de acordo com os intervalos de dados. Por exemplo, uma tabela pode ser particionada por dia, mês ou ano. Você pode melhorar o desempenho de consultas através da eliminação da partição, o que limita a verificação de uma consulta para dados dentro de uma partição. Você também pode manter os dados por meio de alternância de partição. Como os dados no pool SQL já estão distribuídos, muitas partições podem reduzir o desempenho da consulta. Para saber mais informações, confira [Diretrizes de particionamento](sql-data-warehouse-tables-partition.md).  Quando a partição muda para partições de tabela que não estão vazias, considere usar a opção TRUNCATE_TARGET na instrução [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) se os dados existentes forem truncados. O código abaixo alterna os dados diários transformados para o SalesFact, substituindo os dados existentes.

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

## <a name="columnstore-indexes"></a>Índices columnstore

Por padrão, o pool SQL dedicado armazena uma tabela como um índice columnstore clusterizado. Essa forma de armazenamento de dados atinge a alta compactação de dados e o desempenho de consultas em tabelas grandes.  

Normalmente, o índice columnstore clusterizado é a melhor opção, mas existem alguns casos onde um índice clusterizado ou um heap são estruturas de armazenamento mais adequadas.  

> [!TIP]
> Uma tabela de heap pode ser especialmente útil para carregar dados transitórios, como uma tabela de preparo que é transformada em uma tabela final.

Para obter uma lista de recursos columnstore, confira [Quais são as novidades dos índices columnstores](/sql/relational-databases/indexes/columnstore-indexes-what-s-new?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Para melhorar o desempenho do índice columnstore, confira [Como maximizar a qualidade do rowgroup para índices columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

## <a name="statistics"></a>Estatísticas

O otimizador de consulta usa estatísticas de nível de coluna quando cria o plano para executar uma consulta.

Para melhorar o desempenho da consulta, é importante ter estatísticas em colunas individuais, especialmente colunas usadas em junções de consulta. A [criação de estatísticas](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic) ocorre automaticamente.  

A atualização de estatísticas não ocorre automaticamente. Atualize as estatísticas depois que um número significativo de linhas for adicionado ou alterado. Por exemplo, atualize as estatísticas depois de uma carga. Para obter mais informações, confira [Diretrizes sobre estatísticas](sql-data-warehouse-tables-statistics.md).

## <a name="primary-key-and-unique-key"></a>Chave primária e chave exclusiva

Só há suporte para a chave primária quando não CLUSTERIZAdo e não imposto são usados.  Só há suporte para a restrição UNIQUE quando não imposta é usado.  Verifique as [restrições de tabela do pool SQL dedicado](sql-data-warehouse-table-constraints.md).

## <a name="commands-for-creating-tables"></a>Comandos para a criação de tabelas

Você pode criar uma tabela como uma nova tabela vazia. Você também pode criar e popular uma tabela com os resultados de uma instrução de seleção. A seguir estão os comandos T-SQL para criar uma tabela.

| Instrução T-SQL | Descrição |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Cria uma tabela vazia com a definição de todas as opções e colunas da tabela. |
| [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Cria uma tabela externa. A definição da tabela é armazenada no pool do SQL dedicado. Os dados da tabela são armazenados no Armazenamento de Blobs do Azure ou do Azure Data Lake Store. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Popula uma tabela nova com os resultados de uma instrução selecionada. Os tipos de dados e colunas de tabela baseiam-se nos resultados da instrução selecionada. Para importar dados, essa instrução pode selecionar de uma tabela externa. |
| [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Cria uma tabela externa nova exportando os resultados de uma instrução selecionada para um local externo.  O local é o armazenamento de Blobs do Azure ou o Azure Data Lake Store. |

## <a name="aligning-source-data-with-dedicated-sql-pool"></a>Alinhando dados de origem com o pool do SQL dedicado

As tabelas dedicadas do pool SQL são preenchidas por meio do carregamento de dados de outra fonte de dados. Para executar uma carga bem-sucedida, o número e os tipos de dados das colunas nos dados de origem devem ser alinhados com a definição de tabela no pool do SQL dedicado. Obter os dados para alinhar pode ser a parte mais difícil da criação de tabelas.

Se os dados forem provenientes de vários armazenamentos de dados, você carregará os dados no pool do SQL dedicado e os armazenará em uma tabela de integração. Depois que os dados estiverem na tabela de integração, você poderá usar o poder do pool dedicado do SQL para executar operações de transformação. Quando os dados estiverem preparados, será possível inseri-los nas tabelas de produção.

## <a name="unsupported-table-features"></a>Recursos da tabela sem suporte

O pool SQL dedicado oferece suporte a muitos, mas não a todos, dos recursos de tabela oferecidos por outros bancos de dados.  A lista a seguir mostra alguns dos recursos de tabela que não têm suporte no pool SQL dedicado:

- Chave estrangeira, verificar [restrições de tabela](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Colunas computadas](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Exibições indexadas](/sql/relational-databases/views/create-indexed-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Sequência](/sql/t-sql/statements/create-sequence-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Colunas esparsas](/sql/relational-databases/tables/use-sparse-columns?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- Chaves substitutas. Implementar com [identidade](sql-data-warehouse-tables-identity.md).
- [Sinônimos](/sql/t-sql/statements/create-synonym-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Gatilhos](/sql/t-sql/statements/create-trigger-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Índices exclusivos](/sql/t-sql/statements/create-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Tipos definidos pelo usuário](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="table-size-queries"></a>Consultas do tamanho da tabela

Uma maneira simples de identificar o espaço e as linhas consumidas por uma tabela em cada uma das 60 distribuições é usar [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

No entanto, usar comandos DBCC pode ser bastante limitado.  Exibições de gerenciamento dinâmico (DMVs) mostram mais detalhes que os comandos DBCC. Comece criando esta exibição:

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
WHERE pn.[type] = 'COMPUTE'
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

Esta consulta retorna as linhas e o espaço por tabela.  Ele permite que você veja quais tabelas são suas maiores tabelas e se elas são de rodízio, replicadas ou distribuídas por hash.  Nas tabelas distribuídas em hash, a consulta exibe a coluna de distribuição.  

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

Depois de criar as tabelas para seu pool SQL dedicado, a próxima etapa é carregar dados na tabela.  Para obter um tutorial de carregamento, consulte [carregando dados no pool dedicado do SQL](load-data-wideworldimportersdw.md).