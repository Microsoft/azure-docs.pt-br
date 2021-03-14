---
title: Melhorar o desempenho do índice columnstore
description: Reduza os requisitos de memória ou aumente a memória disponível para maximizar o número de linhas que um índice columnstore compacta em cada rowgroup.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 18350dc39fceaf6f4c50f8e1053a2972bbce7f44
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676636"
---
# <a name="maximize-rowgroup-quality-for-columnstore-index-performance"></a>Maximizar a qualidade do rowgroup para o desempenho do índice columnstore

A qualidade do grupo de linhas é determinada pelo número de linhas em um grupo de linhas. Aumentar a memória disponível pode maximizar o número de linhas que um índice columnstore compacta em cada rowgroup.  Use estes métodos para melhorar as taxas de compactação e o desempenho da consulta em índices columnstore.

## <a name="why-the-rowgroup-size-matters"></a>Por que o tamanho do rowgroup é importante

Como um índice columnstore examina uma tabela com o exame de segmentos de coluna de rowgroups individuais, maximizar o número de linhas em cada rowgroup melhora o desempenho da consulta. Quando os rowgroups têm um número elevado de linhas, a compactação de dados melhora, o que significa que há menos dados para serem lidos do disco.

Para obter mais informações sobre rowgroups, consulte [Guia de índices Columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?view=azure-sqldw-latest&preserve-view=true).

## <a name="target-size-for-rowgroups"></a>Tamanho de destino para rowgroups

Para o melhor desempenho de consulta, o objetivo é maximizar o número de linhas por rowgroup em um índice columnstore. Um rowgroup pode ter, no máximo, 1.048.576 linhas. Não é um problema ter o número máximo de linhas por rowgroup. Os índices Columnstore obtêm um bom desempenho quando os rowgroups têm, pelo menos, 100.000 linhas.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Os rowgroups podem ser cortados durante a compactação

Durante um carregamento em massa ou uma recompilação de índice columnstore, às vezes não há memória suficiente disponível para compactar todas as linhas designadas para cada rowgroup. Quando há pressão de memória, os índices columnstore cortam o tamanho do rowgroup para que a compactação no columnstore possa ser bem-sucedida.

Quando não houver memória suficiente para compactar pelo menos 10.000 linhas em cada rowgroup, um erro será gerado.

Para obter mais informações sobre o carregamento em massa, consulte [Carregamento em massa em um índice columnstore clusterizado](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance?view=azure-sqldw-latest#bulk&preserve-view=true).

## <a name="how-to-monitor-rowgroup-quality"></a>Como monitorar a qualidade do grupo de linhas

O sys.dm_pdw_nodes_db_column_store_row_group_physical_stats DMV ([Sys.dm_db_column_store_row_group_physical_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql?view=azure-sqldw-latest&preserve-view=true) contém a definição da exibição correspondente ao banco de dados SQL) que expõe informações úteis, como o número de linhas em RowGroups, e o motivo para corte, se houver corte. Você pode criar a exibição a seguir como uma maneira útil consultar essa DMV para obter informações sobre a fragmentação do grupo de linhas.

```sql
create view dbo.vCS_rg_physical_stats
as
with cte
as
(
select   tb.[name]                    AS [logical_table_name]
,        rg.[row_group_id]            AS [row_group_id]
,        rg.[state]                   AS [state]
,        rg.[state_desc]              AS [state_desc]
,        rg.[total_rows]              AS [total_rows]
,        rg.[trim_reason_desc]        AS trim_reason_desc
,        mp.[physical_name]           AS physical_name
FROM    sys.[schemas] sm
JOIN    sys.[tables] tb               ON  sm.[schema_id]          = tb.[schema_id]
JOIN    sys.[pdw_table_mappings] mp   ON  tb.[object_id]          = mp.[object_id]
JOIN    sys.[pdw_nodes_tables] nt     ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[dm_pdw_nodes_db_column_store_row_group_physical_stats] rg      ON  rg.[object_id]     = nt.[object_id]
                                                                            AND rg.[pdw_node_id]   = nt.[pdw_node_id]
                                        AND rg.[distribution_id]    = nt.[distribution_id]
)
select *
from cte;
```

O trim_reason_desc informa se o grupo de linhas foi cortado (trim_reason_desc = NO_TRIM implica que não houve corte e grupo de linhas é de melhor qualidade). Os motivos de corte a seguir indicam prematuro corte do grupo de linhas:

- CARREGAMENTO EM MASSA: Esse motivo de corte é usado quando o lote de entrada de linhas para a carga tinha menos de 1 milhão de linhas. O mecanismo criará grupos de linhas compactado se houver mais que 100.000 linhas sendo inseridas (em vez de inserir no repositório delta), mas define o motivo do corte para CARREGAMENTO EM MASSA. Nesse cenário, considere aumentar a carga do lote para incluir mais linhas. Além disso, reavalie o esquema de particionamento para garantir que não está muito granular, já que os grupos de linhas não podem abranger os limites de partição.
- MEMORY_LIMITATION: Para criar grupos de linhas com 1 milhão de linhas, uma determinada quantidade de memória de trabalho é necessária para o mecanismo. Quando a memória disponível da sessão de carregamento é menor do que a memória necessária do trabalho, grupos de linhas são cortados prematuramente. As seções a seguir explicam como estimar a memória necessária e alocar mais memória.
- DICTIONARY_SIZE: Este motivo do corte indica que a fragmentação do grupo de linhas ocorreu devido a pelo menos uma coluna de cadeia de caracteres com cadeias de caracteres ampla e/ou de alta cardinalidade. O tamanho do dicionário está limitado a 16 MB de memória e quando esse limite é atingido o grupo de linhas é compactado. Se você se deparar com essa situação, considere isolar a coluna problemática em uma tabela separada.

## <a name="how-to-estimate-memory-requirements"></a>Como estimar os requisitos de memória

A memória máxima necessária para compactar um rowgroup é, aproximadamente, da seguinte maneira:

- 72 MB +
- \#linhas \* \# colunas \* 8 bytes +
- \#linhas \* \# de cadeia de caracteres curta-colunas \* 32 bytes +
- \#colunas de cadeia de caracteres longa \* 16 MB para o dicionário de compactação

> [!NOTE]
> Onde as colunas de cadeia de caracteres curta usam tipos de dados de cadeia de caracteres de <= 32 bytes e colunas de cadeia de caracteres longa usam tipos de dados de cadeia de caracteres de > 32 bytes.

As cadeias de caracteres longas são compactadas com um método de compactação projetado para a compactação de texto. Esse método de compactação usa um *dicionário* para armazenar os padrões de texto. O tamanho máximo de um dicionário é de 16 MB. Há apenas um dicionário para cada coluna de cadeia de caracteres longa no rowgroup.

Para uma discussão aprofundada sobre os requisitos de memória columnstore, consulte o vídeo [Synapse SQL Scaling: configuração e diretrizes](https://channel9.msdn.com/Events/Ignite/2016/BRK3291).

## <a name="ways-to-reduce-memory-requirements"></a>Maneiras de reduzir os requisitos de memória

Use as técnicas a seguir para reduzir os requisitos de memória para compactar rowgroups em índices columnstore.

### <a name="use-fewer-columns"></a>Usar menos colunas

Se possível, crie a tabela com menos colunas. Quando um rowgroup é compactado no columnstore, o índice columnstore compacta cada segmento de coluna separadamente. Portanto, os requisitos de memória para compactação de um rowgroup aumentam de acordo com o número de colunas.

### <a name="use-fewer-string-columns"></a>Usar menos colunas de cadeia de caracteres

As colunas de tipos de dados de cadeia de caracteres exigem mais memória do que os tipos de dados numéricos e de data. Para reduzir os requisitos de memória, considere remover as colunas de cadeia de caracteres de tabelas de fatos e colocá-las em tabelas de dimensão menores.

Requisitos de memória adicionais para a compactação de cadeia de caracteres:

- Tipos de dados de cadeia de caracteres de até 32 caracteres podem exigir 32 bytes adicionais por valor.
- Tipos de dados de cadeia de caracteres com mais de 32 caracteres são compactados usando métodos de dicionário.  Cada coluna no rowgroup pode exigir até 16 MB adicionais para a criação do dicionário.

### <a name="avoid-over-partitioning"></a>Evitar o excesso de particionamento

Os índices Columnstore criam um ou mais rowgroups por partição. Para data warehousing no Azure Synapse Analytics, o número de partições aumenta rapidamente porque os dados são distribuídos e cada distribuição é particionada. Se a tabela tiver um número excessivo de partições, talvez não haja linhas suficientes para preencher os rowgroups. A falta de linhas não cria a pressão de memória durante a compactação, mas leva a rowgroups que não obtêm o melhor desempenho de consulta de columnstore.

Outro motivo para evitar o excesso de particionamento é que há uma sobrecarga de memória no carregamento de linhas em um índice columnstore em uma tabela particionada. Durante o carregamento, várias partições poderão receber as linhas de entrada, que são mantidas na memória até que cada partição tenha linhas suficientes para ser compactada. Ter um número excessivo de partições cria pressão de memória adicional.

### <a name="simplify-the-load-query"></a>Simplificar a consulta de carga

O banco de dados compartilha a concessão de memória para uma consulta entre todos os operadores na consulta. Quando uma consulta de carga tem classificações e junções complexas, a memória disponível para compactação é reduzida.

Crie a consulta de carga para que ela se concentre apenas no carregamento da consulta. Se você precisar executar transformações nos dados, execute-as separadamente da consulta de carga. Por exemplo, prepare os dados em uma tabela de heap, execute as transformações e carregue a tabela de preparo no índice columnstore. 

### <a name="adjust-maxdop"></a>Ajustar o MAXDOP

Cada distribuição compacta os RowGroups no columnstore em paralelo quando há mais de um núcleo de CPU disponível por distribuição. O paralelismo exige recursos de memória adicionais, o que pode levar à pressão de memória e ao corte de rowgroup.

Para reduzir a pressão de memória, use a dica de consulta MAXDOP para forçar a operação de carregamento a ser executada em modo serial em cada distribuição.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Maneiras de alocar mais memória

O tamanho da DWU e a classe de recurso de usuário em conjunto determinam a quantidade de memória disponível para uma consulta de usuário. Para aumentar a concessão de memória para uma consulta de carga, você pode aumentar o número de DWUs ou aumentar a classe de recurso.

- Para aumentar as DWUs, consulte [Como faço para escalar o desempenho?](../sql-data-warehouse/quickstart-scale-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- Para alterar a classe de recurso de uma consulta, consulte [Alterar um exemplo de classe de recurso de usuário](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#change-a-users-resource-class).

## <a name="next-steps"></a>Próximas etapas

Para encontrar mais maneiras de melhorar o desempenho no Synapse SQL, consulte a [visão geral de desempenho](../overview-terminology.md).

