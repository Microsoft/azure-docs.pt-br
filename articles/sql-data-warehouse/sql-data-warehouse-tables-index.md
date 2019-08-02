---
title: Indexando tabelas no SQL Data Warehouse do Azure | Microsoft Azure
description: Recomendações e exemplos para indexação de tabelas no SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019
ms.openlocfilehash: 4d51bd6906a8299a25fe50ca817b1a2b6082ab91
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479853"
---
# <a name="indexing-tables-in-sql-data-warehouse"></a>Indexando tabelas no SQL Data Warehouse

Recomendações e exemplos para indexação de tabelas no SQL Data Warehouse do Azure.

## <a name="index-types"></a>Tipos de índice

O SQL Data Warehouse oferece várias opções de indexação, incluindo [índices columnstore clusterizados](/sql/relational-databases/indexes/columnstore-indexes-overview), [índices clusterizados e índices não clusterizados](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described) e uma opção de não indexação também conhecida como [heap](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes).  

Para criar uma tabela com um índice, consulte a documentação [CRIAR TABELA (SQL Data Warehouse do Azure)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse).

## <a name="clustered-columnstore-indexes"></a>Índice columnstore clusterizado

Por padrão, o SQL Data Warehouse cria um índice columnstore clusterizado quando nenhuma opção de índice é especificada em uma tabela. As tabelas columnstore clusterizadas oferecem o nível mais alto de compactação de dados e o melhor desempenho de consulta geral.  As tabelas columnstore clusterizadas geralmente superam as tabelas de índice clusterizado ou de heap e geralmente são a melhor opção para tabelas grandes.  Por esses motivos, columnstore clusterizado é a melhor opção para começar quando você não tem certeza de como indexar sua tabela.  

Para criar uma tabela columnstore clusterizada, basta especificar CLUSTERED COLUMNSTORE INDEX na cláusula WITH, ou não incluir a cláusula WITH:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Há alguns cenários em que columnstore clusterizado pode não ser uma boa opção:

- Tabelas ColumnStore não dão suporte a varchar (max), nvarchar (max) e varbinary (max). Considere o heap ou índice clusterizado.
- As tabelas ColumnStore podem ser menos eficientes para dados transitórios. Considere a possibilidade de tabelas heap ou até mesmo temporárias.
- Tabelas pequenas com menos de 60 milhões linhas. Considere as tabelas de heap.

## <a name="heap-tables"></a>Tabelas de heap

Quando você estiver temporariamente destinando dados no SQL Data Warehouse, talvez você ache que usar uma tabela de heap torna o processo geral mais rápido. Isso ocorre porque carregamentos de heaps são mais rápidos que as tabelas de índice e, em alguns casos, a leitura subsequente pode ser feita no cache.  Se estiver carregando os dados apenas para prepará-los antes de executar mais transformações, carregar a tabela na tabela de heap é muito mais rápido que carregar os dados em uma tabela columnstore clusterizado. Além disso, o carregamento de dados em uma [tabela temporária](sql-data-warehouse-tables-temporary.md) carrega mais rapidamente do que o carregamento de uma tabela em um armazenamento permanente.  

Para tabelas de pesquisa pequenas, menos de 60 milhões linhas, muitas vezes as tabelas de heap fazem sentido.  As tabelas columnstore do cluster começam a alcançar uma compactação ideal quando há mais de 60 milhões linhas.

Para criar uma tabela de heap, basta especificar HEAP na cláusula WITH:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Índices clusterizados e não clusterizados

Os índices clusterizados podem superar o desempenho de tabelas columnstore clusterizadas quando uma única linha precisa ser recuperada rapidamente. Para consultas em que é necessário pesquisar poucas vezes ou somente uma vez para desempenho com extrema velocidade, considere um índice de cluster ou um índice secundário não clusterizado. A desvantagem de usar um índice clusterizado é que apenas as consultas que se beneficiam são aquelas que usam um filtro altamente seletivo na coluna de índice clusterizado. Para melhorar o filtro em outras colunas, um índice não clusterizado pode ser adicionado a outras colunas. No entanto, cada índice adicionado a uma tabela adiciona espaço e tempo de processamento às cargas.

Para criar uma tabela de índice clusterizado, basta especificar o CLUSTERED INDEX na cláusula WITH:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Para adicionar um índice não clusterizado em uma tabela, use a seguinte sintaxe:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Otimizando índices columnstore clusterizados

As tabelas columnstore clusterizadas são organizadas em dados em segmentos.  Para segmentos de boa qualidade, é essencial obter um desempenho ideal em uma tabela columnstore.  A qualidade de segmento pode ser medida pelo número de linhas em um grupo de linhas compactado.  A qualidade do segmento é ideal quando há pelo menos cem mil linhas por grupo de linha compactado e ganho de desempenho conforme o número de linhas por grupo de linhas se aproxima das 1.048.576 linhas, que é a maior quantidade de linhas que um grupo de linhas pode conter.

A exibição abaixo pode ser criada e usada em seu sistema para calcular a média de linhas por grupo de linhas e identificar índices columnstore de cluster abaixo do ideal.  A última coluna nessa exibição gera uma instrução SQL que pode ser usada para recriar os índices.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,    COUNT(DISTINCT rg.[partition_number])                    AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,    CEILING    ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Agora que você criou o modo de exibição, execute essa consulta para identificar tabelas com grupos de linhas com menos de cem mil linhas. Claro, convém aumentar o limite de cem mil se você deseja aumentar a qualidade do segmento.

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Depois de executar a consulta, você poderá começar a analisar os dados e seus resultados. Esta tabela explica o que procurar em sua análise do rowgroup.

| Coluna | Como usar esses dados |
| --- | --- |
| [table_partition_count] |Se a tabela for particionada, espere ver contagens mais altas de grupos de linhas abertos. Cada partição na distribuição poderia, na teoria, ter um grupo de linhas aberto associado a ela. Fatore isso na sua análise. Uma pequena tabela que foi particionada poderia ser otimizada com a remoção completa do particionamento, pois isso aprimoraria a compactação. |
| [row_count_total] |Contagem total de linhas para a tabela. Por exemplo, você pode usar este valor para calcular a porcentagem de linhas no estado compactado. |
| [row_count_per_distribution_MAX] |Se todas as linhas forem distribuídas uniformemente, esse valor será o número alvo de linhas por distribuição. Compare esse valor com compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |Número total de linhas no formato columnstore para a tabela. |
| [COMPRESSED_rowgroup_rows_AVG] |Se o número médio de linhas for significativamente menor do que o número máximo de linhas para um grupo de linhas, considere usar CTAS ou ALTER INDEX REBUILD para compactar novamente os dados |
| [COMPRESSED_rowgroup_count] |Número de grupos de linhas no formato columnstore. Se esse número for muito alto em relação à tabela, isso significa que a densidade de columnstore é baixa. |
| [COMPRESSED_rowgroup_rows_DELETED] |Linhas são excluídas de forma lógica no formato columnstore. Se o número for alto em relação ao tamanho da tabela, considere recriar a partição ou refazer o índice, pois isso as remove fisicamente. |
| [COMPRESSED_rowgroup_rows_MIN] |Use em conjunto com as colunas AVG e MAX para entender o intervalo de valores dos grupos de linhas no columnstore. Um número baixo acima do limite de carga (102.400 por distribuição alinhada por partição) sugere que as otimizações estão disponíveis na carga de dados |
| [COMPRESSED_rowgroup_rows_MAX] |Como acima |
| [OPEN_rowgroup_count] |Grupos de linhas abertos são normais. Seria razoável esperar um grupo de linhas ABERTO de acordo com a distribuição de tabela (60). Números excessivos sugerem carregamento de dados nas partições. Verifique a estratégia de particionamento para garantir que ela esteja correta |
| [OPEN_rowgroup_rows] |Cada grupo de linhas pode ter 1.048.576 linhas, no máximo. Use esse valor para ver quão completo estão os grupos de linhas no momento |
| [OPEN_rowgroup_rows_MIN] |Os grupos abertos indicam que os dados estão sendo carregados lentamente na tabela ou que a carga anterior foi despejada sobre as linhas restantes nesse grupo de linhas. Use as colunas MIN, MAX, AVG para ver a quantidade de dados colocada nos grupos de linhas ABERTOS. Em tabelas pequenas, esse valor pode ser de 100% de dados! Nesse caso, use ALTER INDEX REBUILD para forçar os dados para columnstore. |
| [OPEN_rowgroup_rows_MAX] |Como acima |
| [OPEN_rowgroup_rows_AVG] |Como acima |
| [CLOSED_rowgroup_rows] |Analise as linhas do grupo de linhas fechado como uma verificação de integridade. |
| [CLOSED_rowgroup_count] |O número de grupos de linhas fechados deverá ser baixo se algum for visto. Os grupos de linhas fechados podem ser convertidos em grupos de linhas compactados usando a instrução ALTER INDEX... Comando REORGANIZE. No entanto, normalmente isso não é obrigatório. Os grupos fechados são convertidos automaticamente em grupos de linhas columnstore pelo processo em segundo plano "motor de tupla". |
| [CLOSED_rowgroup_rows_MIN] |Os grupos de linhas fechados devem ter uma taxa de preenchimento alta. Se a taxa de preenchimento de um grupo de linhas fechado for baixa, será necessário fazer outra análise do columnstore. |
| [CLOSED_rowgroup_rows_MAX] |Como acima |
| [CLOSED_rowgroup_rows_AVG] |Como acima |
| [Rebuild_Index_SQL] |SQL para recriar o índice columnstore para uma tabela |

## <a name="causes-of-poor-columnstore-index-quality"></a>Causas de má qualidade de índice columnstore

Se você tiver identificado tabelas com segmentos de má qualidade, deseja identificar a causa raiz.  A seguir estão algumas causas comuns de segmentos de má qualidade:

1. Pressão de memória quando o índice foi criado
2. Alto volume de operações DML
3. Operações de carregamento pequenas ou lentas
4. Número excessivo de partições

Esses fatores podem fazer com que um índice columnstore tenha menos que o ideal de um milhão de linhas por grupo de linhas. Eles também podem colocar as linhas no rowgroup delta, em vez do rowgroup compactado.

### <a name="memory-pressure-when-index-was-built"></a>Pressão de memória quando o índice foi criado

O número de linhas por grupo de linhas compactado está diretamente relacionado à largura da linha e à quantidade de memória disponível para processar o grupo de linhas.  Quando as linhas são gravadas nas tabelas columnstore sob pressão da memória, a qualidade do segmento columnstore pode ficar prejudicada.  Portanto, a prática recomendada é fornecer à sessão que está gravando o acesso de tabelas de índice columnstore o máximo de memória possível.  Como há uma compensação entre a memória e simultaneidade, a orientação sobre a alocação de memória correta depende dos dados em cada linha da tabela, das unidades de data warehouse alocadas para o seu sistema, e o número de slots de simultaneidade que pode ser dado à sessão que está gravando dados em sua tabela.

### <a name="high-volume-of-dml-operations"></a>Alto volume de operações DML

Um alto volume de operações DML pesadas que atualizam e excluem linhas pode causar ineficiência ao columnstore. Isso acontece principalmente quando a maioria das linhas em um rowgroup é modificada.

- A exclusão de uma linha de um rowgroup compactado somente marca logicamente a linha como excluída. A linha permanece no rowgroup compactado até que a partição ou a tabela seja recriada.
- A inserção de uma linha a adiciona a uma tabela interna da rowstore chamada de rowgroup delta. A linha inserida não é convertida para a columnstore, até que o rowgroup delta esteja cheio e marcado como fechado. Rowgroups são fechados quando atingem a capacidade máxima de 1.048.576 linhas.
- A atualização de uma linha no formato columnstore é processada como uma exclusão lógica e, em seguida, como uma inserção. A linha inserida pode ser armazenada no repositório delta.

As operações de atualização e inserção em lote que excedem o limite em massa de 102.400 linhas por distribuição alinhada em partição vão diretamente no formato columnstore. No entanto, supondo a ocorrência de uma distribuição uniforme, seria necessário modificar mais de 6.144 milhões de linhas em uma única operação para que isso ocorresse. Se o número de linhas de uma determinada distribuição alinhada por partição for menor que 102.400, as linhas vão para o repositório Delta e permanecem lá até que as linhas suficientes tenham sido inseridas ou modificadas para fechar o grupo de linhas ou o índice tenha sido recriado.

### <a name="small-or-trickle-load-operations"></a>Operações de carregamento pequenas ou lentas

Às vezes, pequenas cargas que fluem para o SQL Data Warehouse também são chamadas de cargas lentas. Normalmente, elas representam um fluxo quase constante de dados que estão sendo incluídos pelo sistema. No entanto, como esse fluxo é quase contínuo, o volume de linhas não é grande. Frequentemente, os dados ficam consideravelmente abaixo do limite necessário para um carregamento direto no formato columnstore.

Nessas situações, é melhor levar os dados primeiro ao armazenamento de blobs do Azure e deixá-los se acumularem antes do carregamento. Essa técnica é conhecida normalmente como *micro envio em lote*.

### <a name="too-many-partitions"></a>Número excessivo de partições

Outra coisa a considerar é o impacto de particionamento de suas tabelas columnstore clusterizadas.  Antes do particionamento, o SQL Data Warehouse já divide seus dados em 60 bancos de dados.  O particionamento divide ainda mais seus dados.  Se particionar seus dados, considere que **cada** partição precisa ter pelo menos um milhão de linhas para se beneficiar de um índice columnstore clusterizado.  Se você particionar sua tabela em 100 partições, sua tabela precisará de pelo menos 6.000.000.000 linhas para se beneficiar de um índice columnstore clusterizado (60 distribuições *100 partições* 1 milhão linhas). Se a tabela de cem partições não tiver seis bilhões de linhas, reduza o número de partições ou considere usar uma tabela de heap.

Quando as tabelas tiverem sido carregadas com alguns dados, siga as etapas abaixo para identificar e recriar tabelas com índices columnstore clusterizados abaixo do ideal.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Recriando índices para melhorar a qualidade de segmento

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Etapa 1: Identificar ou criar o usuário que usa a classe de recurso correta

Uma maneira rápida de melhorar a qualidade do segmento imediatamente é recriar o índice.  O SQL retornado pela exibição acima retorna uma instrução ALTER INDEX REBUILD, que pode ser usada para recriar os índices. Ao recriar os índices, não deixe de alocar memória suficiente para a sessão que recria o índice.  Para fazer isso, aumente a classe de recurso de um usuário que tem permissões para recriar o índice nessa tabela para o mínimo recomendado.

Abaixo está um exemplo de como alocar mais memória para um usuário aumentando sua classe de recurso. Para trabalhar com classes de recurso, consulte [Classes de recurso para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md).

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Etapa 2: Recompilar índices columnstore clusterizados com usuário de classe de recurso superior

Entre como o usuário da etapa 1 (por exemplo, loaduser), que agora está usando uma classe de recurso mais alta e execute as instruções ALTER INDEX. Verifique se esse usuário tem a permissão ALTER para as tabelas em que o índice está sendo recriado. Estes exemplos mostram como recriar todo o índice columnstore e como recriar uma partição única. Em tabelas grandes, é mais prático recriar índices, uma partição por vez.

Como alternativa, em vez de recriar o índice, é possível copiar a tabela para uma nova tabela [usando CTAS](sql-data-warehouse-develop-ctas.md). Qual é a melhor opção? Para grandes volumes de dados, CTAS é geralmente mais rápido do que [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql). Para volumes menores de dados, ALTER INDEX é mais fácil de usar e não exige a troca da tabela.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

A recriação de um índice no SQL Data Warehouse é uma operação offline.  Para obter mais informações sobre como recompilar índices, consulte a seção ALTER INDEX REBUILD em [Desfragmentação dos índices columnstore](/sql/relational-databases/indexes/columnstore-indexes-defragmentation) e [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql).

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Etapa 3: Verificar se melhorou a qualidade do segmento columnstore clusterizado

Execute novamente a consulta que identificou a tabela com segmentos de má qualidade e verifique se a qualidade melhorou.  Se a qualidade do segmento não melhorou, é possível que as linhas da tabela sejam muito amplas.  Considere usar uma classe de recurso maior ou mais DWU durante a recriação de índices.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Recriando índices com CTAS e alternância de partição

Este exemplo usa a instrução [CRIAR TABELAS COMO SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) e alternância de partição para recriar uma partição de tabela.

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
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
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Switch IN the rebuilt data with TRUNCATE_TARGET option
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2 WITH (TRUNCATE_TARGET = ON);
```

Para obter mais detalhes sobre como recriar partições usando CTAS, consulte [Usando partições no SQL Data Warehouse](sql-data-warehouse-tables-partition.md).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como desenvolver tabelas, consulte [Desenvolvendo tabelas](sql-data-warehouse-tables-overview.md).
