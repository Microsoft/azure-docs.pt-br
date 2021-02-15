---
title: Criar e atualizar estatísticas em tabelas
description: Recomendações e exemplos para criar e atualizar estatísticas de otimização de consulta em tabelas no pool SQL dedicado.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/09/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3ade41c51cbb8065734e8957cfc8b9f0c22b2df3
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98673359"
---
# <a name="table-statistics-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Estatísticas de tabela para o pool SQL dedicado no Azure Synapse Analytics

Neste artigo, você encontrará recomendações e exemplos para criar e atualizar estatísticas de otimização de consulta em tabelas no pool SQL dedicado.

## <a name="why-use-statistics"></a>Por que usar estatísticas

O pool de SQL mais dedicado sabe sobre seus dados, quanto mais rápido ele pode executar consultas em relação a ele. Depois de carregar dados no pool SQL dedicado, coletar estatísticas sobre seus dados é uma das coisas mais importantes que você pode fazer para otimizar suas consultas.

O otimizador de consulta do pool do SQL dedicado é um otimizador baseado em custo. Ele compara o custo de vários planos de consulta e, em seguida, escolhe o plano com o menor custo. Na maioria dos casos, ele escolhe o plano que será executado mais rapidamente.

Por exemplo, se o otimizador estimar que a data em que a consulta está filtrando retornará uma linha, ela escolherá um plano. Se ele estimar que a data selecionada retornará 1 milhão de linhas, ele retornará um plano diferente.

## <a name="automatic-creation-of-statistic"></a>Criação automática de estatística

Quando a opção de AUTO_CREATE_STATISTICS do banco de dados está ativada, o pool SQL dedicado analisa as consultas de usuário de entrada para obter as estatísticas ausentes.

Se faltarem estatísticas, o otimizador de consulta cria estatísticas em colunas individuais no predicado de consulta ou na condição de junção para melhorar as estimativas da cardinalidade para o plano de consulta.

> [!NOTE]
> Criação automática de estatísticas está atualmente ativada por padrão.

Você pode verificar se o pool do SQL dedicado tem AUTO_CREATE_STATISTICS configurado executando o seguinte comando:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Se o seu pool SQL dedicado não tiver AUTO_CREATE_STATISTICS configurado, recomendamos que você habilite essa propriedade executando o seguinte comando:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Essas instruções irão disparar a criação automática de estatísticas:

- SELECT
- INSERT-SELECT
- CTAS
- UPDATE
- Delete (excluir)
- EXPLAIN quando houver uma junção ou a presença de um predicado for detectada

> [!NOTE]
> Criação automática de estatísticas não é criada em tabelas temporárias ou externas.

A criação automática de estatísticas é feita de forma síncrona. Portanto, você pode ter um desempenho de consulta ligeiramente degradado se suas colunas estiverem faltando estatísticas. O tempo para criar estatísticas para uma única coluna depende do tamanho da tabela.

Para evitar a degradação mensurável do desempenho, verifique se as estatísticas foram criadas primeiro executando a carga de trabalho de parâmetros de comparação antes de criar um perfil do sistema.

> [!NOTE]
> A criação de estatísticas será registrada [Sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) em um contexto de usuário diferente.

Quando são criadas estatísticas automáticas, terão o formato: _WA_Sys_<8 digit column id in Hex>_<8 digit table id in Hex>. Você pode exibir estatísticas que já foram criadas executando o comando [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) :

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

O table_name é o nome da tabela que contém as estatísticas a serem exibidas. Esta tabela não pode ser uma tabela externa. O destino é o nome do índice, as estatísticas ou a coluna de destino cujas informações de estatísticas serão exibidas.

## <a name="update-statistics"></a>Atualizar estatísticas

Uma prática recomendada é atualizar as estatísticas em colunas de data por dia à medida que novas datas são adicionadas. Cada vez que novas linhas são carregadas no pool SQL dedicado, novas datas de carregamento ou datas de transação são adicionadas. Essas adições alteram a distribuição de dados e torna as estatísticas desatualizadas.

As estatísticas em uma coluna de país/região em uma tabela de cliente talvez nunca precisem ser atualizadas, pois a distribuição de valores geralmente não é alterada. Supondo que a distribuição seja constante entre os clientes, adicionar novas linhas à variação de tabela não alterará a distribuição dos dados.

No entanto, se o pool SQL dedicado contiver apenas um país/região e você inserir dados de um novo país/região, resultando em dados de vários países/regiões que estão sendo armazenados, você precisará atualizar as estatísticas na coluna país/região.

O seguinte são recomendações atualizando estatísticas:

|||
|-|-|
| **Frequência de atualizações de estatísticas**  | Conservadora: Diário </br> Depois de carregar ou transformar os dados |
| **Amostragem** |  Menos de 1 bilhão de linhas, usar a amostragem padrão (20%). </br> Com mais de 1 bilhão de linhas, use a amostragem de dois por cento. |

Uma das primeiras perguntas a serem feitas quando você estiver solucionando problemas em uma consulta é, **"As estatísticas estão atualizadas?"**

Essa questão não pode ser respondida pela idade dos dados. Um objeto de estatísticas atualizado pode ser antigo se não houver nenhuma alteração importante nos dados subjacentes. Quando o número de linhas mudar substancialmente, ou houver uma alteração material na distribuição de valores para uma coluna, *então*, significa que é hora de atualizar as estatísticas. 

Não há nenhuma exibição de gerenciamento dinâmico para determinar se os dados dentro da tabela foram alterados desde a última atualização das estatísticas.  As duas consultas a seguir podem ajudá-lo a determinar se suas estatísticas estão obsoletas.

**Consulta 1:**  Descubra a diferença entre a contagem de linhas das estatísticas (**stats_row_count**) e a contagem real de linhas (**actual_row_count**). 

```sql
select 
objIdsWithStats.[object_id], 
actualRowCounts.[schema], 
actualRowCounts.logical_table_name, 
statsRowCounts.stats_row_count, 
actualRowCounts.actual_row_count,
row_count_difference = CASE
    WHEN actualRowCounts.actual_row_count >= statsRowCounts.stats_row_count THEN actualRowCounts.actual_row_count - statsRowCounts.stats_row_count
    ELSE statsRowCounts.stats_row_count - actualRowCounts.actual_row_count
END,
percent_deviation_from_actual = CASE
    WHEN actualRowCounts.actual_row_count = 0 THEN statsRowCounts.stats_row_count
    WHEN statsRowCounts.stats_row_count = 0 THEN actualRowCounts.actual_row_count
    WHEN actualRowCounts.actual_row_count >= statsRowCounts.stats_row_count THEN CONVERT(NUMERIC(18, 0), CONVERT(NUMERIC(18, 2), (actualRowCounts.actual_row_count - statsRowCounts.stats_row_count)) / CONVERT(NUMERIC(18, 2), actualRowCounts.actual_row_count) * 100)
    ELSE CONVERT(NUMERIC(18, 0), CONVERT(NUMERIC(18, 2), (statsRowCounts.stats_row_count - actualRowCounts.actual_row_count)) / CONVERT(NUMERIC(18, 2), actualRowCounts.actual_row_count) * 100)
END
from
(
    select distinct object_id from sys.stats where stats_id > 1
) objIdsWithStats
left join
(
    select object_id, sum(rows) as stats_row_count from sys.partitions group by object_id
) statsRowCounts
on objIdsWithStats.object_id = statsRowCounts.object_id 
left join
(
    SELECT sm.name [schema] ,
    tb.name logical_table_name ,
    tb.object_id object_id ,
    SUM(rg.row_count) actual_row_count
    FROM sys.schemas sm
    INNER JOIN sys.tables tb ON sm.schema_id = tb.schema_id
    INNER JOIN sys.pdw_table_mappings mp ON tb.object_id = mp.object_id
    INNER JOIN sys.pdw_nodes_tables nt ON nt.name = mp.physical_name
    INNER JOIN sys.dm_pdw_nodes_db_partition_stats rg
    ON rg.object_id = nt.object_id
    AND rg.pdw_node_id = nt.pdw_node_id
    AND rg.distribution_id = nt.distribution_id
    WHERE 1 = 1
    GROUP BY sm.name, tb.name, tb.object_id
) actualRowCounts
on objIdsWithStats.object_id = actualRowCounts.object_id

```

**Consulta 2:** Descubra a idade de suas estatísticas verificando a última vez em que suas estatísticas foram atualizadas em cada tabela. 

> [!NOTE]
> Se houver uma alteração importante na distribuição de valores para uma coluna, você deverá atualizar as estatísticas independentemente da última vez que foram atualizadas.

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

As **colunas de data** em um pool SQL dedicado, por exemplo, geralmente precisam de atualizações de estatísticas frequentes. Cada vez que novas linhas são carregadas no pool SQL dedicado, novas datas de carregamento ou datas de transação são adicionadas. Essas adições alteram a distribuição de dados e torna as estatísticas desatualizadas.

Por outro lado, as estatísticas de uma coluna de gênero em uma tabela de clientes talvez nunca precisem ser atualizadas. Supondo que a distribuição seja constante entre os clientes, adicionar novas linhas à variação de tabela não alterará a distribuição dos dados.

Se o seu pool SQL dedicado contiver apenas um gênero e um novo requisito resultar em vários gêneros, você precisará atualizar as estatísticas na coluna sexo.

Para obter mais informações, consulte as diretrizes gerais para [Estatísticas](/sql/relational-databases/statistics/statistics?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="implementing-statistics-management"></a>Implementação do gerenciamento de estatísticas

Geralmente, é uma boa ideia estender o processo de carregamento de dados para garantir que as estatísticas sejam atualizadas no final da carga para evitar/minimizar a contenção de recursos ou bloqueio entre consultas simultâneas.  

É no carregamento de dados que as tabelas frequentemente mudam de tamanho e/ou distribuição de valores. O carregamento de dados é um local lógico para implementar alguns processos de gerenciamento.

Os seguintes princípios orientadores são fornecidos para atualizar suas estatísticas:

- Certifique-se de que cada tabela carregada tenha pelo menos um objeto de estatísticas atualizado. Isso atualiza as informações do tamanho da tabela (contagem de linhas e contagem de páginas) como parte da atualização de estatísticas.
- Concentre-se em colunas que participam de cláusulas JOIN, GROUP BY, ORDER BY e DISTINCT.
- Considere uma atualização mais frequentes das colunas de "chave crescente", por exemplo, datas de transação, porque esses valores não serão incluídos no histograma de estatísticas.
- Considere atualizar as colunas de distribuição estática com menos frequência.
- Lembre-se, cada objeto estatístico é atualizado em sequência. Simplesmente implementar `UPDATE STATISTICS <TABLE_NAME>` nem sempre é ideal, especialmente para tabelas amplas com muitos objetos de estatística.

Para obter mais informações, consulte [Estimativa de cardinalidade](/sql/relational-databases/performance/cardinality-estimation-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="examples-create-statistics"></a>Exemplos: Criar estatísticas

Estes exemplos mostram como usar várias opções para a criação de estatísticas. As opções usadas para cada coluna dependem das características dos dados e de como a coluna será usada em consultas.

### <a name="create-single-column-statistics-with-default-options"></a>Criar estatísticas de coluna única com opções padrão

Para criar estatísticas em uma coluna, forneça um nome para o objeto de estatísticas e o nome da coluna.

Esta sintaxe usa todas as opções padrão. Por padrão, **20 por cento** da tabela é amostrado ao criar estatísticas.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Por exemplo:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Criar estatísticas de coluna única examinando cada linha

A taxa de amostragem padrão de 20 por cento é suficiente para a maioria das situações. No entanto, você pode ajustar essa taxa de amostragem.

Para usar toda a tabela como amostragem, use a seguinte sintaxe:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Por exemplo:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Criar estatísticas de coluna única, especificando o tamanho da amostra

Como alternativa, você pode especificar o tamanho da amostra como uma porcentagem:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Criar estatísticas de coluna única em apenas algumas das linhas

Também é possível criar estatísticas em uma parte das linhas na tabela. Isso é chamado de estatística filtrada.

Por exemplo, é possível usar estatísticas filtradas quando você planeja consultar uma partição específica de uma tabela particionada grande. Ao criar estatísticas apenas nos valores de partição, a precisão das estatísticas melhora e, portanto, o desempenho da consulta também.

Este exemplo cria estatísticas em um intervalo de valores. Os valores podem ser facilmente definidos para corresponder ao intervalo de valores em uma partição.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Para que o otimizador de consulta considere usar estatísticas filtradas ao escolher o plano de consulta distribuída, a consulta deve ser adequada à definição do objeto de estatísticas. Usando o exemplo anterior, a cláusula WHERE da consulta precisa especificar valores col1 entre 2000101 e 20001231.

### <a name="create-single-column-statistics-with-all-the-options"></a>Criar estatísticas de coluna única com todas as opções

Também é possível combinar as opções juntas. O exemplo a seguir cria um objeto estatístico filtrado com um tamanho de amostra personalizado:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Para obter a referência completa, consulte [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="create-multi-column-statistics"></a>Criar estatísticas de várias colunas

Para criar um objeto estatístico de várias colunas, use os exemplos anteriores, mas especifique mais colunas.

> [!NOTE]
> O histograma, que é usado para estimar o número de linhas no resultado da consulta, está disponível apenas para a primeira coluna listada na definição do objeto estatístico.

Neste exemplo, o histograma está em *product\_category*. As estatísticas entre colunas são calculadas em *product\_category* e *product\_sub_category*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Como há uma correlação entre a *categoria do\_produto* e a *sub\_categoria do\_produto*, um objeto de estatística de colunas múltiplas poderá ser útil se essas colunas forem acessadas ao mesmo tempo.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Criar estatísticas em todas as coluna em uma tabela

É uma maneira de criar estatísticas é emitir comandos CREATE STATISTICS depois de criar a tabela:

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-sql-pool"></a>Usar um procedimento armazenado para criar estatísticas em todas as colunas em um pool do SQL

O pool SQL dedicado não tem um procedimento armazenado do sistema equivalente a sp_create_stats em SQL Server. Esse procedimento armazenado cria um objeto de estatísticas de coluna única em cada coluna em um pool SQL que ainda não tem estatísticas.

O exemplo a seguir ajudará você a começar a usar seu design de pool do SQL. Fique à vontade para adaptá-lo às suas necessidades.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Para criar estatísticas em todas as colunas da tabela usando os padrões, realize o procedimento armazenado.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Para criar estatísticas em todas as colunas na tabela usando um FULLSCAN, chame esse procedimento.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Para criar estatísticas de amostra em todas as colunas da tabela, insira 3 e o percentual da amostra. Este procedimento usa uma taxa de amostragem de 20%.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

## <a name="examples-update-statistics"></a>Exemplos: Atualizar estatísticas

Para atualizar as estatísticas, você pode:

- Atualizar um objeto de estatísticas. Especifique o nome do objeto de estatísticas que você deseja atualizar.
- Atualizar todos os objetos de estatísticas em uma tabela. Especifique o nome da tabela em vez de um objeto de estatísticas específico.

### <a name="update-one-specific-statistics-object"></a>Atualizar um objeto de estatísticas específico

Use a sintaxe a seguir para atualizar um objeto de estatísticas específico:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Por exemplo:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Ao atualizar objetos de estatísticas específicos, você pode minimizar o tempo e os recursos necessários para o gerenciamento de estatísticas. Fazer isso requer algumas idéias para escolher os melhores objetos de estatísticas a serem atualizados.

### <a name="update-all-statistics-on-a-table"></a>Atualizar todas as estatísticas de uma tabela

Um método simples para atualizar todos os objetos de estatísticas em uma tabela é:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Por exemplo:

```sql
UPDATE STATISTICS dbo.table1;
```

A instrução UPDATE STATISTICS é fácil de usar. Lembre-se de que isso atualizará *todas* as estatísticas na tabela e, portanto, poderá executar mais trabalho do que o necessário. Se o desempenho não for um problema, essa é a maneira mais fácil e completa de garantir que as estatísticas estejam atualizadas.

> [!NOTE]
> Ao atualizar todas as estatísticas em uma tabela, o pool SQL dedicado faz uma verificação para obter uma amostra da tabela para cada objeto de estatísticas. Se a tabela for grande e tiver muitas colunas e muitas estatísticas, talvez seja mais eficiente atualizar estatísticas individuais com base na necessidade.

Para obter uma implementação de um `UPDATE STATISTICS` procedimento, consulte [tabelas temporárias](sql-data-warehouse-tables-temporary.md). O método de implementação é ligeiramente diferente do procedimento anterior `CREATE STATISTICS`, mas o resultado é o mesmo.

Para obter a sintaxe completa, consulte [atualizar estatísticas](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="statistics-metadata"></a>Metadados de estatísticas

Há várias exibições e funções do sistema que podem ser utilizadas para localizar informações sobre estatísticas. Por exemplo, você pode ver se um objeto de estatísticas está desatualizado usando a função stats-date para ver quando as estatísticas foram criadas ou atualizadas pela última vez.

### <a name="catalog-views-for-statistics"></a>Exibições de catálogo para as estatísticas

Essas exibições do sistema fornecem informações sobre estatísticas:

| Exibição de catálogo | Descrição |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Uma linha para cada coluna. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Uma linha para cada objeto no banco de dados. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Uma linha para cada esquema no banco de dados. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Uma linha para cada objeto de estatísticas. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Uma linha para cada coluna no objeto de estatísticas. Conecta novamente a sys.columns. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Uma linha para cada tabela (inclui tabelas externas). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Uma linha para cada tipo de dados. |

### <a name="system-functions-for-statistics"></a>Funções de sistema para estatísticas

Essas funções de sistema são úteis para trabalhar com estatísticas:

| Função do sistema | Descrição |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Data da última atualização do objeto de estatísticas. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Nível de resumo e informações detalhadas sobre a distribuição de valores conforme entendido pelo objeto de estatísticas. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Combinar colunas de estatísticas e funções em uma exibição

Essa exibição une as colunas relacionadas às estatísticas e os resultados da função STATS_DATE() em conjunto.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-show_statistics-examples"></a>Exemplos de DBCC SHOW_STATISTICS()

DBCC SHOW_STATISTICS() mostra os dados contidos em um objeto de estatísticas. Esses dados estão divididos em três partes:

- Cabeçalho
- Vetor de densidade
- Histograma

Os metadados de cabeçalho sobre as estatísticas. O histograma exibe a distribuição de valores na primeira coluna de chave do objeto de estatísticas. O vetor de densidade mede a correlação entre colunas.

> [!NOTE]
> O pool do SQL dedicado computa estimativas de cardinalidade com qualquer um dos dados no objeto de estatísticas.

### <a name="show-header-density-and-histogram"></a>Mostrar cabeçalho, densidade e histograma

Este exemplo simples mostra as três partes de um objeto de estatísticas:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Por exemplo:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Mostrar uma ou mais partes de DBCC SHOW_STATISTICS()

Se você estiver interessado apenas em visualizar partes específicas, use a cláusula `WITH` e especifique quais partes deseja ver:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Por exemplo:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-show_statistics-differences"></a>Diferenças do DBCC SHOW_STATISTICS()

O DBCC SHOW_STATISTICS () é mais estritamente implementado no pool SQL dedicado em comparação com SQL Server:

- Não há suporte para recursos não documentados.
- Não é possível usar Stats_stream.
- Não é possível unir resultados para subconjuntos específicos de dados estatísticos. Por exemplo, STAT_HEADER JOIN DENSITY_VECTOR.
- NO_INFOMSGS não pode ser definido para a supressão de mensagem.
- Não é possível usar colchetes em nomes de estatísticas.
- Não é possível usar nomes de coluna para identificar objetos de estatísticas.
- Não há suporte para o erro personalizado 2767.

## <a name="next-steps"></a>Próximas etapas

Para melhorar ainda mais o desempenho da consulta, veja [Monitorar sua carga de trabalho](sql-data-warehouse-manage-monitor.md)
