---
title: Particionando tabelas no SQL Data Warehouse | Microsoft Docs
description: "Introdução ao particionamento de tabela no Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: jhubbard
editor: 
ms.assetid: 6cef870c-114f-470c-af10-02300c58885d
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 10/31/2016
ms.author: shigu;barbkess
ms.openlocfilehash: 3edfd34d368228be32afef48688739639a3b03ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="partitioning-tables-in-sql-data-warehouse"></a>Particionando tabelas no SQL Data Warehouse
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

O particionamento tem suporte em todos os tipos de tabela do SQL Data Warehouse, incluindo columnstore clusterizado, índice clusterizado e heap.  O particionamento também tem suporte em todos os tipos de distribuição, incluindo hash ou round robin.  O particionamento permite dividir seus dados em grupos menores de dados e, na maioria dos casos, o particionamento é feito em uma coluna de data.

## <a name="benefits-of-partitioning"></a>Benefícios do particionamento
O particionamento pode melhorar o desempenho da consulta e a manutenção de dados.  Se ele beneficia ambos ou apenas um depende de como os dados são carregados e se a mesma coluna pode ser usada para ambas as finalidades, já que o particionamento só pode ser feito em uma coluna.

### <a name="benefits-to-loads"></a>Benefícios para cargas
A principal vantagem do particionamento no SQL Data Warehouse é melhorar a eficiência e o desempenho de carregamento de dados pelo uso de exclusão, troca e mesclagem de partição.  Na maioria dos casos, os dados são particionados em uma coluna de data que está intimamente ligada à sequência em que os dados são carregados no banco de dados.  Uma das maiores vantagens de usar partições para manter dados é evitar o registro de transações em log.  Embora a simples inserção, atualização ou exclusão de dados possa ser a abordagem mais simples, com um pouco de empenho, o uso de particionamento durante o processo de carregamento pode melhorar consideravelmente o desempenho.

A alternância de partição pode ser usada para remover ou substituir uma seção de uma tabela rapidamente.  Por exemplo, uma tabela de fatos de vendas pode conter apenas dados dos últimos 36 meses.  No final de cada mês, o mês de dados de vendas mais antigo é excluído da tabela.  Esses dados poderiam ser excluídos usando uma instrução delete para excluir os dados do mês mais antigo.  No entanto, a exclusão de uma grande quantidade de dados linha a linha com uma instrução delete pode levar muito tempo e criar o risco de transações grandes, o que poderia levar muito tempo para reverter se algo desse errado.  Uma abordagem melhor é simplesmente remover a partição mais antiga dos dados.  A exclusão de linhas individuais pode levar horas. A exclusão de uma partição inteira pode demorar segundos.

### <a name="benefits-to-queries"></a>Vantagens para consultas
O particionamento também pode ser usado para melhorar o desempenho da consulta.  Se uma consulta aplica um filtro em uma coluna particionada, isso pode limitar a varredura apenas às partições qualificadas que podem ser um subconjunto muito menor de dados, impedindo uma verificação completa.  Com a introdução de índices columnstore clusterizados, os benefícios de desempenho de eliminação de predicado são menores, mas em alguns casos pode haver vantagem para as consultas.  Por exemplo, se a tabela de fatos de vendas for particionada em 36 meses usando o campo de data das vendas, as consultas que forem filtradas pela data da venda podem pular a pesquisa de partições que não correspondam ao filtro.

## <a name="partition-sizing-guidance"></a>Diretrizes de dimensionamento da partição
Embora o particionamento possa ser usado para melhorar o desempenho de alguns cenários, a criação de uma tabela com **muitas** partições pode prejudicar o desempenho em algumas circunstâncias.  Esses problemas são especialmente verdadeiros para tabelas columnstore clusterizadas.  Para que o particionamento seja útil, é importante entender quando usar o particionamento e o número de partições a serem criadas.  Não há nenhuma regra rígida sobre o que é o excesso de partições. Isso depende de seus dados e de quantas partições você está carregando simultaneamente.  Mas, como regra geral, considere adicionar dezenas a centenas de partições, não milhares.

Ao criar o particionamento em tabelas **columnstore clusterizadas** , é importante considerar o número de linhas que vão para cada partição.  Para compactação e desempenho ideais de tabelas columnstore clusterizadas, é necessário um mínimo de um milhão de linhas por distribuição, e também é necessário haver partição.  Antes das partições serem criadas, o SQL Data Warehouse já divide cada tabela em 60 bancos de dados distribuídos.  O particionamento adicionado a uma tabela é além das distribuições criadas nos bastidores.  Usando esse exemplo, se a tabela de fatos de vendas contiver 36 partições mensais, e uma vez que o SQL Data Warehouse tem 60 distribuições, a tabela de fatos de vendas deverá conter 60 milhões de linhas por mês, ou 2.1 bilhões de linhas quando todos os meses forem populados.  Se uma tabela possuir significativamente menos linhas do que o mínimo recomendado, considere usar menos partições para aumentar o número de linhas por partição.  Confira também o artigo [Indexação][Index], que inclui consultas que podem ser executadas no SQL Data Warehouse para avaliar a qualidade dos índices columnstore do cluster.

## <a name="syntax-difference-from-sql-server"></a>Diferença de sintaxe do SQL Server
O SQL Data Warehouse apresenta uma definição simplificada de partições que é ligeiramente diferente do SQL Server.  As funções e esquemas de particionamento não são usados no SQL Data Warehouse como são no SQL Server.  Em vez disso,tudo o que você precisa fazer é identificar a coluna particionada e os pontos delimitadores.  Embora a sintaxe de particionamento possa ser ligeiramente diferente do SQL Server, os conceitos básicos são os mesmos.  O SQL Server e o SQL Data Warehouse dão suporte a uma coluna de partição por tabela, que pode ser partição intervalada.  Para saber mais sobre particionamento, confira [Tabelas e índices particionados][Partitioned Tables and Indexes].

O exemplo de uma instrução particionada [CREATE TABLE][CREATE TABLE] do SQL Data Warehouse particiona a tabela FactInternetSales na coluna OrderDateKey:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Migrando o particionamento do SQL Server
Para migrar definições de partição do SQL Server para o SQL Data Warehouse, basta:

* Elimine o [esquema de partição][partition scheme] do SQL Server.
* Adicione a definição [função de partição][partition function] para CREATE TABLE.

Se você estiver migrando uma tabela particionada em uma instância do SQL Server, o SQL abaixo poderá ajudá-lo a investigar o número de linhas em cada partição.  Tenha em mente que, se a mesma granularidade de particionamento é usada no SQL Data Warehouse, o número de linhas por partição é reduzido por 60.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="workload-management"></a>Gerenciamento de carga de trabalho
Uma questão final a considerar ao tomar a decisão sobre a partição de tabela é o [gerenciamento de carga de trabalho][workload management].  O gerenciamento de carga de trabalho no SQL Data Warehouse é basicamente o gerenciamento de memória e simultaneidade.  No SQL Data Warehouse, a memória máxima alocada para cada distribuição durante a execução da consulta é regida pelas classes de recurso.  O ideal é que suas partições sejam dimensionadas em relação a outros fatores, como as necessidades de memória da criação de índices columnstore clusterizados.  Os índices columnstore clusterizados são melhores quando têm mais memória alocada.  Portanto, você desejará garantir que uma recompilação do índice da partição não fique sem memória. O aumento da quantidade de memória disponível para a sua consulta pode ser obtido ao alternar da função padrão, smallrc, para uma das outras funções, como largerc.

Informações sobre a alocação de memória por distribuição estão disponíveis consultando as exibições de gerenciamento dinâmico do administrador de recursos. Na realidade, a concessão de memória será menor do que apresentada abaixo. No entanto, isso fornece um nível de diretrizes que podem ser usados ao dimensionar suas partições para operações de gerenciamento de dados.  Tente evitar o dimensionamento das partições além da concessão de memória fornecida pela classe de recurso muito grande. Se as partições ultrapassarem este valor, você corre o risco de pressão de memória, que por sua vez, leva à menor compactação ideal.

```sql
SELECT  rp.[name]                                AS [pool_name]
,       rp.[max_memory_kb]                        AS [max_memory_kb]
,       rp.[max_memory_kb]/1024                    AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576                AS [mex_memory_gb]
,       rp.[max_memory_percent]                    AS [max_memory_percent]
,       wg.[name]                                AS [group_name]
,       wg.[importance]                            AS [group_importance]
,       wg.[request_max_memory_grant_percent]    AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups    wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools    rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

## <a name="partition-switching"></a>Alternância de partição
O SQL Data Warehouse dá suporte à divisão, mesclagem e comutação de partição. Todas essas funções são executadas usando a instrução [ALTER TABLE][ALTER TABLE].

Para alternar as partições entre duas tabelas, você deve garantir que as partições alinhem em seus respectivos limites e que correspondam as definições de tabela. Como restrições de verificação não estão disponíveis para impor o intervalo de valores em uma tabela, a tabela de origem deve conter os mesmos limites de partição da tabela de destino. Se este não for o caso, a alternância de partição falhará, já que os metadados da partição não serão sincronizados.

### <a name="how-to-split-a-partition-that-contains-data"></a>Como dividir uma partição que contém dados
O método mais eficiente para dividir uma partição que já contém dados é usar um `CTAS` instrução. Se a tabela particionada é uma columnstore clusterizada, então a partição de tabela deve estar vazia antes que ela possa ser dividida.

Veja abaixo um exemplo de tabela columnstore particionada que contém uma linha em cada partição:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [!NOTE]
> Criando o objeto de estatística garantimos que os metadados de tabela sejam mais precisos. Se omitirmos a criação de estatísticas, o SQL Data Warehouse usará os valores padrão. Para obter detalhes sobre as estatísticas examine [estatísticas][statistics].
> 
> 

Em seguida, podemos consultar a contagem de linhas usando a exibição do catálogo `sys.partitions` :

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

Se tentarmos dividir essa tabela, obteremos um erro:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Msg 35346, Nível 15, Estado 1, linha 44 da cláusula SPLIT da instrução ALTER PARTITION falhou porque a partição não está vazia.  Somente partições vazias podem ser divididas quando existe um índice columnstore na tabela. Considere desabilitar o índice columnstore antes de emitir a instrução ALTER PARTITION, e então recriar o índice columnstore após a instrução ALTER PARTITION estar concluída.

No entanto, podemos usar `CTAS` para criar uma nova tabela para manter nossos dados.

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

Como os limites de partição estão alinhados, uma alternância é permitida. Isso deixará a tabela de origem com uma partição vazia que podemos dividir posteriormente.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Tudo o que resta a fazer é alinhar os dados para os novos limites de partição usando `CTAS` e alternar nossos dados de volta para a tabela principal

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Depois de ter concluído a movimentação dos dados, é uma boa ideia atualizar as estatísticas na tabela de destino para garantir que reflitam com precisão a nova distribuição dos dados em suas respectivas partições:

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>Controle da origem do particionamento da tabela
Para evitar a definição da tabela de **rusting** em seu sistema de controle de origem, você talvez queira considerar a abordagem a seguir:

1. Criar a tabela como uma tabela particionada, mas sem valores de partição

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    ()
                )
)
;
```

1. `SPLIT` a tabela como parte do processo de implantação:

```sql
-- Create a table containing the partition boundaries

CREATE TABLE #partitions
WITH
(
    LOCATION = USER_DB
,   DISTRIBUTION = HASH(ptn_no)
)
AS
SELECT  ptn_no
,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
        ) a
;

-- Iterate over the partition boundaries and split the table

DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
,       @i INT = 1                                 --iterator for while loop
,       @q NVARCHAR(4000)                          --query
,       @p NVARCHAR(20)     = N''                  --partition_number
,       @s NVARCHAR(128)    = N'dbo'               --schema
,       @t NVARCHAR(128)    = N'FactInternetSales' --table
;

WHILE @i <= @c
BEGIN
    SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
    SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

    -- PRINT @q;
    EXECUTE sp_executesql @q;

    SET @i+=1;
END

-- Code clean-up

DROP TABLE #partitions;
```

Com essa abordagem, o código no controle de origem permanece estático e são permitidos valores de limite de particionamento dinâmicos, evoluindo com o depósito ao longo do tempo.

## <a name="next-steps"></a>Próximas etapas
Para saber mais, confira os artigos em [Visão geral da tabela][Overview], [Tipos de dados de tabela][Data Types], [Distribuindo uma tabela][Distribute], [Indexando uma tabela][Index], [Mantendo estatísticas de tabela][Statistics] e [Tabelas temporárias][Temporary].  Para saber mais sobre as práticas recomendadas, consulte [Práticas Recomendadas do SQL Data Warehouse][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[workload management]: ./sql-data-warehouse-develop-concurrency.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!-- MSDN Articles -->
[Partitioned Tables and Indexes]: https://msdn.microsoft.com/library/ms190787.aspx
[ALTER TABLE]: https://msdn.microsoft.com/en-us/library/ms190273.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[partition function]: https://msdn.microsoft.com/library/ms187802.aspx
[partition scheme]: https://msdn.microsoft.com/library/ms179854.aspx


<!-- Other web references -->
