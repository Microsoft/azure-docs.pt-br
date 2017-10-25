---
title: "Gerenciamento de estatísticas em tabelas no SQL Data Warehouse | Microsoft Docs"
description: "Introdução às estatísticas em tabelas no Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: jhubbard
editor: 
ms.assetid: faa1034d-314c-4f9d-af81-f5a9aedf33e4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 10/31/2016
ms.author: shigu;barbkess
ms.openlocfilehash: 1d5ded69e394643ddfc3de0c6d30dbd30c8e848f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="managing-statistics-on-tables-in-sql-data-warehouse"></a>Gerenciamento de estatísticas em tabelas no SQL Data Warehouse
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

Quanto mais o SQL Data Warehouse sabe sobre seus dados, mais rápido ele pode executar consultas neles.  A maneira com a qual você informa o SQL Data Warehouse sobre seus dados é coletando estatísticas sobre seus dados.  Ter estatísticas sobre seus dados é uma das coisas mais importantes que você pode fazer para otimizar as consultas.  As estatísticas ajudam o SQL Data Warehouse a criar o plano ideal para suas consultas.  Isso porque o otimizador de consulta do SQL Data Warehouse é um otimizador baseado em custo.  Ou seja, ele compara o custo de vários planos de consulta e depois escolhe o plano com o menor custo, que também deverá ser o plano com execução mais rápida.

As estatísticas podem ser criadas em uma única coluna, várias colunas ou em um índice de uma tabela.  As estatísticas são armazenadas em um histograma que captura o intervalo e a seletividade dos valores.  Isso é particularmente interessante quando o otimizador precisa avaliar cláusulas JOIN, GROUP BY, HAVING e WHERE em uma consulta.  Por exemplo, se o otimizador estimar que a data usada para filtrar sua consulta retornará 1 linha, ele poderá escolher um plano muito diferente do que se estimar que a data selecionada retornará 1 milhão de linhas.  Embora a criação de estatísticas seja extremamente importante, é igualmente importante que as estatísticas reflitam com *precisão* o estado atual da tabela.  Ter estatísticas atualizadas garante a seleção de um bom plano pelo otimizador.  Os planos criados pelo otimizador são tão bons quanto as estatísticas sobre seus dados.

NO momento, o processo de criação e atualização de estatísticas é um processo manual, mas é muito simples.  Isso é diferente do SQL Server, que cria e atualiza automaticamente as estatísticas em colunas e índices únicos.  Ao usar as informações a seguir, você pode automatizar bastante o gerenciamento das estatísticas em seus dados. 

## <a name="getting-started-with-statistics"></a>Introdução às estatísticas
 Criar estatísticas de exemplo em cada coluna é uma maneira fácil de começar a usar as estatísticas.  Como é igualmente importante manter estatísticas atualizadas, pode ser uma abordagem conservadora atualizar as estatísticas diariamente ou após cada carregamento. Sempre há compensações entre o desempenho e o custo para a criação e a atualização das estatísticas.  Se você achar que está demorando muito para manter todas as estatísticas, convém tentar ser mais seletivo sobre quais colunas têm estatísticas ou quais colunas precisam de uma atualização frequente.  Por exemplo, convém atualizar diariamente as colunas de datas, pois novos valores podem ser adicionados, e não após cada carregamento. Novamente, você terá mais benefícios se tiver estatísticas em colunas envolvidas em cláusulas JOINs, GROUP BY, HAVING e WHERE.  Se você tiver uma tabela com muitas colunas que são usadas somente na cláusula SELECT, as estatísticas nessas colunas não serão úteis, e gastar um pouco mais de esforço para identificar apenas as colunas nas quais as estatísticas serão úteis pode reduzir o tempo de manutenção das estatísticas.

## <a name="multi-column-statistics"></a>Estatísticas de várias colunas
Além de criar estatísticas em colunas únicas, talvez você perceba que suas consultas se beneficiariam de estatísticas em várias colunas.  Estatísticas de várias colunas são estatísticas criadas em uma lista de colunas.  Elas incluem estatísticas sobre uma única coluna na primeira coluna da lista, além de algumas informações de correlação entre colunas chamadas de densidades.  Por exemplo, se você tiver uma tabela que se une a outras duas colunas, você perceberá que o SQL Data Warehouse pode otimizar ainda mais o plano se ele compreender a relação entre as duas colunas.   As estatísticas de várias colunas podem melhorar o desempenho de consulta de algumas operações como junções compostas e agrupar por.

## <a name="updating-statistics"></a>Atualização de estatísticas
Atualizar as estatísticas é uma parte importante de sua rotina de gerenciamento de banco de dados.  Quando a distribuição dos dados no banco de dados é alterada, as estatísticas precisam ser atualizadas.  Estatísticas desatualizadas resultarão em um desempenho de consulta abaixo do ideal.

Uma prática recomendada é atualizar as estatísticas em colunas de data por dia à medida que novas datas são adicionadas.  Sempre que há um carregamento de novas linhas no data warehouse, novas datas de carga ou datas de transação são adicionadas. Isso muda a distribuição de dados e desatualiza as estatísticas. Por outro lado, as estatísticas em uma coluna de país em uma tabela de cliente talvez nunca tenham de ser atualizadas, já que a distribuição de valores geralmente não é alterada. Supondo que a distribuição seja constante entre os clientes, adicionar novas linhas à variação de tabela não alterará a distribuição dos dados. No entanto, se seu data warehouse contiver apenas um país e se você exibir dados de um país, resultando em dados de vários países sendo armazenados, definitivamente será necessário atualizar as estatísticas na coluna país.

Uma das primeiras perguntas a serem feitas durante a solução de uma consulta é, "As estatísticas estão atualizadas?"

Essa questão não pode ser respondida pela idade dos dados. Um objeto de estatísticas atualizado pode ser muito antigo se não haja nenhuma alteração material nos dados subjacentes. Quando o número de linhas tiver mudado substancialmente ou se houver uma alteração material na distribuição dos valores de uma determinada coluna, *será necessário* atualizar as estatísticas.  

Para referência, o **SQL Server** (não o SQL Data Warehouse) atualiza automaticamente as estatísticas para estas situações:

* Se você não tiver nenhuma linha na tabela, quando adicionar linhas, obterá uma atualização automática das estatísticas
* Quando você adicionar mais de 500 linhas a uma tabela, começando com menos de 500 linhas (por exemplo, no início você tem 499 e, em seguida, adiciona 500 linhas para ter um total de 999 linhas), obterá uma atualização automática 
* Quando você tiver mais de 500 linhas, terá de adicionar outras 500 linhas mais 20% do tamanho da tabela antes de ver uma atualização automática nas estatísticas

Como não há nenhuma DMV para determinar se os dados da tabela foram alterados desde que a última vez em que as estatísticas foram atualizadas, saber a idade das estatísticas pode fornecer uma parte da cena.  Você pode usar a consulta a seguir para determinar a última vez que suas estatísticas foram atualizadas em cada tabela.  

> [!NOTE]
> Lembre-se de que se houver uma mudança substancial na distribuição dos valores para uma determinada coluna, você deverá atualizar as estatísticas, independentemente da última vez em que elas foram atualizadas.  
> 
> 

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

As colunas de data em um data warehouse, por exemplo, normalmente precisam de atualizações frequentes de estatísticas. Sempre que há um carregamento de novas linhas no data warehouse, novas datas de carga ou datas de transação são adicionadas. Isso muda a distribuição de dados e desatualiza as estatísticas.  Por outro lado, talvez as estatísticas em uma coluna de gênero de uma tabela de clientes nunca precisem ser atualizadas. Supondo que a distribuição seja constante entre os clientes, adicionar novas linhas à variação de tabela não alterará a distribuição dos dados. No entanto, se o data warehouse contiver apenas um gênero, e um novo requisito resultar em vários gêneros, será definitivamente necessário atualizar as estatísticas na coluna gênero.

Para obter mais explicações, veja [Estatísticas][Statistics] no MSDN.

## <a name="implementing-statistics-management"></a>Implementação do gerenciamento de estatísticas
Geralmente, convém estender os processos de carregamento de dados a fim de garantir que as estatísticas estejam atualizadas ao final do carregamento. É no carregamento de dados que as tabelas frequentemente mudam de tamanho e/ou distribuição de valores. Portanto, esse é um momento lógico para implementar alguns processos de gerenciamento.

Veja abaixo alguns princípios importantes para atualizar as estatísticas durante o processo de carregamento:

* Certifique-se de que cada tabela carregada tenha pelo menos um objeto de estatísticas atualizado. Isso atualiza as informações de tamanho da tabela (contagem de linhas e contagem de páginas) como parte da atualização de estatísticas.
* Concentre-se em colunas que participam de cláusulas JOIN, GROUP BY, ORDER BY e DISTINCT
* Considere uma atualização mais frequentes das colunas de "chave crescente", por exemplo, datas de transação, pois esses valores não serão incluídos no histograma de estatísticas.
* Considere atualizar as colunas de distribuição estática com menos frequência.
* Lembre-se de que cada objeto de estatística é atualizado em série. Simplesmente implementar `UPDATE STATISTICS <TABLE_NAME>` talvez não seja ideal, especialmente para tabelas mais amplas com muitos objetos de estatísticas.

> [!NOTE]
> Para obter mais detalhes sobre [chave crescente], confira o white paper sobre modelo de estimativa de cardinalidade do SQL Server 2014.
> 
> 

Para obter mais explicações, veja [Estimativa de cardinalidade][Cardinality Estimation] no MSDN.

## <a name="examples-create-statistics"></a>Exemplos: criar estatísticas
Estes exemplos mostram como usar várias opções para a criação de estatísticas. As opções usadas para cada coluna dependem das características dos dados e de como a coluna será usada em consultas.

### <a name="a-create-single-column-statistics-with-default-options"></a>R. Criar estatísticas de coluna única com opções padrão
Para criar estatísticas em uma coluna, basta fornecer um nome para o objeto de estatísticas e o nome da coluna.

Esta sintaxe usa todas as opções padrão. Por padrão, o SQL Data Warehouse utiliza uma amostragem de 20 por cento da tabela ao criar estatísticas.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Por exemplo:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="b-create-single-column-statistics-by-examining-every-row"></a>B. Criar estatísticas de coluna única examinando cada linha
A taxa de amostragem padrão de 20 por cento é suficiente para a maioria das situações. No entanto, você pode ajustar essa taxa de amostragem.

Para usar toda a tabela como amostragem, use a seguinte sintaxe:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Por exemplo:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="c-create-single-column-statistics-by-specifying-the-sample-size"></a>C. Criar estatísticas de coluna única, especificando o tamanho da amostra
Como alternativa, você pode especificar o tamanho da amostra como uma porcentagem:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="d-create-single-column-statistics-on-only-some-of-the-rows"></a>D. Criar estatísticas de coluna única em apenas algumas das linhas
Outra opção é criar estatísticas em uma parte das linhas na tabela. Isso é chamado de estatística filtrada.

Por exemplo, ao planejar consultar uma partição específica de uma grande tabela particionada, você pode usar as estatísticas filtradas. Ao criar estatísticas apenas nos valores de partição, a precisão das estatísticas melhora e, portanto, o desempenho da consulta também.

Este exemplo cria estatísticas em um intervalo de valores. Os valores podem ser facilmente definidos de acordo com o intervalo de valores em uma partição.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Para que o otimizador de consulta considere usar estatísticas filtradas ao escolher o plano de consulta distribuída, a consulta deve ser adequada à definição do objeto de estatísticas. Usando o exemplo anterior, a cláusula WHERE da consulta precisa especificar valores col1 entre 2000101 e 20001231.
> 
> 

### <a name="e-create-single-column-statistics-with-all-the-options"></a>E. Criar estatísticas de coluna única com todas as opções
Obviamente, você pode combinar as opções. O exemplo abaixo cria um objeto de estatísticas filtradas com um tamanho de amostra personalizado:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Para obter a referência completa, veja [CREATE STATISTICS][CREATE STATISTICS] no MSDN.

### <a name="f-create-multi-column-statistics"></a>F. Criar estatísticas de várias colunas
Para criar estatísticas de várias colunas, basta usar os exemplos anteriores, mas especificar mais colunas.

> [!NOTE]
> O histograma, que é usado para estimar o número de linhas no resultado da consulta, só está disponível para a primeira coluna listada na definição do objeto da estatística.
> 
> 

Neste exemplo, o histograma está em *product\_category*. As estatísticas entre colunas são calculadas em *product\_category* e *product\_sub_c\ategory*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Como há uma correlação entre *product\_category* e *product\_sub\_category*, uma estatística de várias colunas pode ser útil se essas colunas forem acessadas ao mesmo tempo.

### <a name="g-create-statistics-on-all-the-columns-in-a-table"></a>G. Criar estatísticas em todas as colunas em uma tabela
É uma maneira de criar estatísticas é emitir comandos CREATE STATISTICS depois de criar a tabela.

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

### <a name="h-use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>H. Use um procedimento armazenado para criar estatísticas em todas as colunas em um banco de dados
O SQL Data Warehouse não tem um procedimento armazenado no sistema equivalente a [sp_create_stats][] no SQL Server. Esse procedimento armazenado cria um objeto de estatísticas de coluna única em todas as colunas do banco de dados que ainda não tenham estatísticas.

Isso ajudará você a começar com o design do banco de dados. Fique à vontade para adaptá-lo às suas necessidades.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

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
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+@sample_pct+'PERCENT' AS VARCHAR(8000))
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

Para criar estatísticas em todas as colunas na tabela com esse procedimento, simplesmente chame o procedimento.

```sql
prc_sqldw_create_stats;
```

## <a name="examples-update-statistics"></a>Exemplos: atualizar as estatísticas
Para atualizar as estatísticas, você pode:

1. Atualizar um objeto de estatísticas. Especifique o nome do objeto de estatísticas que você deseja atualizar.
2. Atualizar todos os objetos de estatísticas em uma tabela. Especifique o nome da tabela em vez de um objeto de estatísticas específico.

### <a name="a-update-one-specific-statistics-object"></a>R. Atualizar um objeto de estatísticas específico
Use a sintaxe a seguir para atualizar um objeto de estatísticas específico:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Por exemplo:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Ao atualizar objetos de estatísticas específicos, você pode minimizar o tempo e os recursos necessários para o gerenciamento de estatísticas. No entanto, isso exige um pouco de planejamento para escolher os melhores objetos de estatísticas para atualizar.

### <a name="b-update-all-statistics-on-a-table"></a>B. Atualizar todas as estatísticas em uma tabela
O código abaixo mostra um método simples para atualizar todos os objetos de estatísticas em uma tabela.

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Por exemplo:

```sql
UPDATE STATISTICS dbo.table1;
```

Esta instrução é fácil de usar. Lembre-se de que isso atualizará todas as estatísticas na tabela e, portanto, pode executar mais trabalho do que o necessário. Se o desempenho não for um problema, essa é definitivamente a maneira mais fácil e completa de garantir a atualização das estatísticas.

> [!NOTE]
> Ao atualizar todas as estatísticas em uma tabela, o SQL Data Warehouse realiza uma verificação da tabela para coletar amostragens para cada estatística. Se a tabela for grande, tiver muitas colunas e estatísticas, talvez seja mais eficiente para atualizar estatísticas individuais com base na necessidade.
> 
> 

Para a implementação de um procedimento `UPDATE STATISTICS`, leia o artigo [Tabelas Temporárias][Temporary]. O método de implementação é ligeiramente diferente para o procedimento `CREATE STATISTICS` acima, mas o resultado final é o mesmo.

Para obter a sintaxe completa, veja [Atualizar estatísticas][Update Statistics] no MSDN.

## <a name="statistics-metadata"></a>Metadados de estatísticas
Há várias funções e exibições do sistema que você pode usar para localizar informações sobre estatísticas. Por exemplo, você pode ver se um objeto de estatísticas está desatualizado usando a função stats-date para ver quando as estatísticas foram criadas ou atualizadas pela última vez.

### <a name="catalog-views-for-statistics"></a>Exibições de catálogo para as estatísticas
Essas exibições do sistema fornecem informações sobre estatísticas:

| Exibição de catálogo | Descrição |
|:--- |:--- |
| [sys.columns][sys.columns] |Uma linha para cada coluna. |
| [sys.objects][sys.objects] |Uma linha para cada objeto no banco de dados. |
| [sys.schemas][sys.schemas] |Uma linha para cada esquema no banco de dados. |
| [sys.stats][sys.stats] |Uma linha para cada objeto de estatísticas. |
| [sys.stats_columns][sys.stats_columns] |Uma linha para cada coluna no objeto de estatísticas. Conecta novamente a sys.columns. |
| [sys.tables][sys.tables] |Uma linha para cada tabela (inclui tabelas externas). |
| [sys.table_types][sys.table_types] |Uma linha para cada tipo de dados. |

### <a name="system-functions-for-statistics"></a>Funções de sistema para estatísticas
Essas funções de sistema são úteis para trabalhar com estatísticas:

| Função de sistema | Descrição |
|:--- |:--- |
| [STATS_DATE][STATS_DATE] |Data da última atualização do objeto de estatísticas. |
| [DBCC SHOW_STATISTICS][DBCC SHOW_STATISTICS] |Fornece informações detalhadas e resumidas sobre a distribuição de valores, conforme entendido pelo objeto de estatísticas. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Combinar colunas de estatísticas e funções em uma exibição
Essa exibição une as colunas relacionadas às estatísticas e os resultados da função [STATS_DATE()][].

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
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

## <a name="dbcc-showstatistics-examples"></a>Exemplos de DBCC SHOW_STATISTICS()
DBCC SHOW_STATISTICS() mostra os dados contidos em um objeto de estatísticas. Esses dados estão divididos em três partes.

1. Cabeçalho
2. Vetor de densidade
3. Histograma

Os metadados de cabeçalho sobre as estatísticas. O histograma exibe a distribuição de valores na primeira coluna de chave do objeto de estatísticas. O vetor de densidade mede a correlação entre colunas. O SQLDW calcula as estimativas de cardinalidade com os dados no objeto de estatísticas.

### <a name="show-header-density-and-histogram"></a>Mostrar cabeçalho, densidade e histograma
Este exemplo simples mostra as três partes de um objeto de estatísticas.

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Por exemplo:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>Mostrar uma ou mais partes de DBCC SHOW_STATISTICS();
Se você só estiver interessado em ver partes específicas, use a cláusula `WITH` e especifique quais partes deseja ver:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Por exemplo:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>Diferenças do DBCC SHOW_STATISTICS()
DBCC SHOW_STATISTICS() é implementado mais estritamente no SQL Data Warehouse comparado ao SQL Server.

1. Não há suporte para recursos não documentados
2. Não é possível usar Stats_stream
3. Não é possível associar os resultados para subconjuntos específicos de dados de estatísticas, por exemplo, (STAT_HEADER JOIN DENSITY_VECTOR)
4. NO_INFOMSGS não pode ser definido para a supressão de mensagem
5. Não é possível usar colchetes em nomes de estatísticas
6. Não é possível usar nomes de coluna para identificar objetos de estatísticas
7. Não há suporte para o erro personalizado 2767

## <a name="next-steps"></a>Próximas etapas
Para obter mais detalhes, veja [DBCC SHOW_STATISTICS][DBCC SHOW_STATISTICS] no MSDN.  Para saber mais, consulte os artigos sobre [Visão geral da tabela][Overview], [Tipos de dados da tabela][Data Types], [Distribuição de uma tabela][Distribute], [Indexação de uma tabela][Index], [Particionamento de uma tabela][Partition] e [Tabelas temporárias][Temporary].  Para saber mais sobre as práticas recomendadas, consulte [Práticas Recomendadas do SQL Data Warehouse][SQL Data Warehouse Best Practices].  

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

<!--MSDN references-->  
[Cardinality Estimation]: https://msdn.microsoft.com/library/dn600374.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC SHOW_STATISTICS]:https://msdn.microsoft.com/library/ms174384.aspx
[Statistics]: https://msdn.microsoft.com/library/ms190397.aspx
[STATS_DATE]: https://msdn.microsoft.com/library/ms190330.aspx
[sys.columns]: https://msdn.microsoft.com/library/ms176106.aspx
[sys.objects]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.schemas]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.stats]: https://msdn.microsoft.com/library/ms177623.aspx
[sys.stats_columns]: https://msdn.microsoft.com/library/ms187340.aspx
[sys.tables]: https://msdn.microsoft.com/library/ms187406.aspx
[sys.table_types]: https://msdn.microsoft.com/library/bb510623.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx

<!--Other Web references-->  
