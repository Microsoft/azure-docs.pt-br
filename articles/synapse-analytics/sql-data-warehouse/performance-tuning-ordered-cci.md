---
title: Ajuste de desempenho com o índice columnstore clusterizado ordenado
description: Recomendações e considerações que você deve saber ao usar o índice columnstore clusterizado ordenado para melhorar seu desempenho de consulta em pools SQL dedicados.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: afb6efcee2ad4f5cf25a411eed353ff2fc27d75c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96460784"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Ajuste de desempenho com o índice columnstore clusterizado ordenado  

Quando os usuários consultam uma tabela columnstore no pool SQL dedicado, o otimizador verifica os valores mínimo e máximo armazenados em cada segmento.  Os segmentos que estão fora dos limites do predicado de consulta não são lidos do disco para a memória.  Uma consulta pode obter um desempenho mais rápido se o número de segmentos a serem lidos e seu tamanho total forem pequenos.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Índice columnstore clusterizado versus não ordenado

Por padrão, para cada tabela criada sem uma opção de índice, um componente interno (Construtor de índice) cria um CCI (índice columnstore clusterizado) não ordenado.  Os dados em cada coluna são compactados em um segmento de rowgroup de CCI separado.  Há metadados no intervalo de valores de cada segmento, de modo que os segmentos que estão fora dos limites do predicado de consulta não são lidos do disco durante a execução da consulta.  O CCI oferece o nível mais alto de compactação de dados e reduz o tamanho dos segmentos a serem lidos para que as consultas possam ser executadas mais rapidamente. No entanto, como o construtor de índice não classifica os dados antes de compactá-los em segmentos, os segmentos com intervalos de valores sobrepostos podem ocorrer, fazendo com que as consultas leiam mais segmentos do disco e demorem mais para serem concluídas.  

Ao criar um CCI ordenado, o mecanismo do pool de SQL dedicado classifica os dados existentes na memória pelas chaves de ordem antes que o construtor de índice os compacte em segmentos de índice.  Com os dados classificados, a sobreposição de segmento é reduzida, permitindo que as consultas tenham uma eliminação de segmento mais eficiente e um desempenho mais rápido, pois o número de segmentos a serem lidos do disco é menor.  Se todos os dados puderem ser classificados na memória de uma vez, a sobreposição de segmento poderá ser evitada.  Devido a grandes tabelas em data warehouses, esse cenário não acontece com frequência.  

Para verificar os intervalos de segmento de uma coluna, execute o seguinte comando com o nome da tabela e o nome da coluna:

```sql
SELECT o.name, pnp.index_id, 
cls.row_count, pnp.data_compression_desc, 
pnp.pdw_node_id, pnp.distribution_id, cls.segment_id, 
cls.column_id, 
cls.min_data_id, cls.max_data_id, 
cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table Name>' and cols.name = '<Column Name>'  and TMap.physical_name  not like '%HdTable%'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id 


```

> [!NOTE] 
> Em uma tabela de CCI ordenada, os novos dados resultantes do mesmo lote de operações DML ou de carregamento de dados são classificados dentro desse lote, não há nenhuma classificação global em todos os dados na tabela.  Os usuários podem recriar o CCI ordenado para classificar todos os dados na tabela.  No pool SQL dedicado, a recompilação do índice columnstore é uma operação offline.  Para uma tabela particionada, a recompilação é feita uma partição por vez.  Os dados na partição que está sendo recriada são "offline" e indisponíveis até que a recompilação seja concluída para essa partição. 

## <a name="query-performance"></a>Desempenho de consulta

O lucro de desempenho de uma consulta de um CCI ordenado depende dos padrões de consulta, do tamanho dos dados, da forma como os dados são classificados, da estrutura física dos segmentos e do DWU e da classe de recursos escolhidos para a execução da consulta.  Os usuários devem revisar todos esses fatores antes de escolher as colunas de ordenação ao criar uma tabela de CCI ordenada.

Consultas com todos esses padrões normalmente são executadas mais rapidamente com o CCI ordenado.  
1. As consultas têm predicados de igualdade, desigualdade ou intervalo
1. As colunas de predicado e as colunas de CCI ordenadas são as mesmas.  
1. As colunas de predicado são usadas na mesma ordem que o ordinal de coluna de colunas CCI ordenadas.  
 
Neste exemplo, a tabela T1 tem um índice columnstore clusterizado ordenado na sequência de Col_C, Col_B e Col_A.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

O desempenho da consulta 1 pode se beneficiar mais do CCI ordenado do que as outras três consultas. 

```sql
-- Query #1: 

SELECT * FROM T1 WHERE Col_C = 'c' AND Col_B = 'b' AND Col_A = 'a';

-- Query #2

SELECT * FROM T1 WHERE Col_B = 'b' AND Col_C = 'c' AND Col_A = 'a';

-- Query #3
SELECT * FROM T1 WHERE Col_B = 'b' AND Col_A = 'a';

-- Query #4
SELECT * FROM T1 WHERE Col_A = 'a' AND Col_C = 'c';

```

## <a name="data-loading-performance"></a>Desempenho do carregamento de dados

O desempenho do carregamento de dados em uma tabela de CCI ordenada é semelhante a uma tabela particionada.  O carregamento de dados em uma tabela de CCI ordenada pode levar mais tempo do que uma tabela CCI não ordenada devido à operação de classificação de dados, no entanto, as consultas podem ser executadas mais rapidamente depois com um CCI ordenado.  

Aqui está um exemplo de comparação de desempenho de carregamento de dados em tabelas com esquemas diferentes.

![Gráfico de barras que mostra a comparação de desempenho do carregamento de dados em tabelas com esquemas diferentes.](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


Aqui está um exemplo de comparação de desempenho de consulta entre CCI e CCI ordenado.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>Reduzir a sobreposição de segmento

O número de segmentos sobrepostos depende do tamanho dos dados a serem classificados, da memória disponível e da configuração de MAXDOP (grau máximo de paralelismo) durante a criação de CCI ordenada. Abaixo estão as opções para reduzir o segmento se sobrepondo ao criar um CCI ordenado.

- Use a classe de recurso xlargerc em um DWU superior para permitir mais memória para classificação de dados antes que o construtor de índice compacte os dados em segmentos.  Uma vez em um segmento de índice, o local físico dos dados não pode ser alterado.  Não há nenhuma classificação de dados dentro de um segmento ou entre segmentos.  

- Crie um CCI ordenado com MAXDOP = 1.  Cada thread usado para a criação de CCI ordenada funciona em um subconjunto de dados e o classifica localmente.  Não há nenhuma classificação global entre os dados classificados por threads diferentes.  O uso de threads paralelos pode reduzir o tempo de criação de um CCI ordenado, mas irá gerar mais segmentos sobrepostos do que usar um único thread.  Atualmente, há suporte para a opção MAXDOP apenas na criação de uma tabela CCI ordenada usando CREATE TABLE comando de seleção.  A criação de um CCI ordenado por meio de comandos CREATE INDEX ou CREATE TABLE não oferece suporte à opção MAXDOP. Por exemplo,

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```

- Classifique previamente os dados pelas chaves de classificação antes de carregá-los em tabelas.

Aqui está um exemplo de uma distribuição de tabela CCI ordenada que tem um segmento zero sobreposto após as recomendações acima. A tabela CCI ordenada é criada em um banco de dados DWU1000c por meio de CTAS de uma tabela de heap de 20 GB usando MAXDOP 1 e xlargerc.  O CCI é ordenado em uma coluna BIGINT sem duplicatas.  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>Criar CCI ordenado em tabelas grandes

A criação de um CCI ordenado é uma operação offline.  Para tabelas sem partições, os dados não poderão ser acessados pelos usuários até que o processo de criação de CCI ordenado seja concluído.   Para tabelas particionadas, como o mecanismo cria a partição de CCI ordenada por partição, os usuários ainda podem acessar os dados em partições em que a criação de CCI ordenada não está em processo.   Você pode usar essa opção para minimizar o tempo de inatividade durante a criação ordenada de CCI em tabelas grandes: 

1.    Crie partições na tabela de destino grande (chamada Table_A).
2.    Crie uma tabela de CCI ordenada vazia (chamada Table_B) com a mesma tabela e esquema de partição que a tabela A.
3.    Mude uma partição da tabela A para a tabela B.
4.    Execute ALTER INDEX <Ordered_CCI_Index> em <Table_B> REBUILD PARTITION = <Partition_ID> na tabela B para recompilar a partição alternada.  
5.    Repita as etapas 3 e 4 para cada partição em Table_A.
6.    Depois que todas as partições forem alternadas de Table_A para Table_B e tiverem sido recriadas, descartar Table_A e renomear Table_B para Table_A. 

## <a name="examples"></a>Exemplos

**Um. Para verificar as colunas ordenadas e o ordinal do pedido:**

```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. Para alterar a coluna ordinal, adicione ou remova colunas da lista Order ou altere de CCI para CCI ordenado:**

```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).
