---
title: Melhorar o desempenho do índice columnstore – SQL Data Warehouse do Azure | Microsoft Docs
description: Reduza os requisitos de memória ou aumente a memória disponível para maximizar o número de linhas que um índice columnstore compacta em cada rowgroup.
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
origin.date: 03/22/2019
ms.date: 04/01/2019
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: e7ab09522184f5c2d1c5168b24b2948f58e5189e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60748761"
---
# <a name="maximizing-rowgroup-quality-for-columnstore"></a>Maximizando a qualidade do grupo de linhas para o columnstore

A qualidade do grupo de linhas é determinada pelo número de linhas em um grupo de linhas. Aumentar a memória disponível pode maximizar o número de linhas de que um índice columnstore compacta em cada rowgroup.  Use estes métodos para melhorar as taxas de compactação e o desempenho da consulta em índices columnstore.

## <a name="why-the-rowgroup-size-matters"></a>Por que o tamanho do rowgroup é importante
Como um índice columnstore examina uma tabela com o exame de segmentos de coluna de rowgroups individuais, maximizar o número de linhas em cada rowgroup melhora o desempenho da consulta. Quando os rowgroups têm um número elevado de linhas, a compactação de dados melhora, o que significa que há menos dados para serem lidos do disco.

Para obter mais informações sobre rowgroups, consulte [Guia de índices Columnstore](https://msdn.microsoft.com/library/gg492088.aspx).

## <a name="target-size-for-rowgroups"></a>Tamanho de destino para rowgroups
Para o melhor desempenho de consulta, o objetivo é maximizar o número de linhas por rowgroup em um índice columnstore. Um rowgroup pode ter, no máximo, 1.048.576 linhas. Não é um problema ter o número máximo de linhas por rowgroup. Os índices Columnstore obtêm um bom desempenho quando os rowgroups têm, pelo menos, 100.000 linhas.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Os rowgroups podem ser cortados durante a compactação

Durante um carregamento em massa ou uma recompilação de índices columnstore, às vezes, não há memória suficiente disponível para compactar todas as linhas designadas para cada rowgroup. Quando há pressão de memória, os índices columnstore cortam o tamanho do rowgroup para que a compactação no columnstore possa ser bem-sucedida. 

Quando não há memória suficiente para compactar, pelo menos, 10.000 linhas em cada rowgroup, o SQL Data Warehouse gera um erro.

Para obter mais informações sobre o carregamento em massa, consulte [Carregamento em massa em um índice columnstore clusterizado](https://msdn.microsoft.com/library/dn935008.aspx#Bulk ).

## <a name="how-to-monitor-rowgroup-quality"></a>Como monitorar a qualidade do grupo de linhas

O DM pdw_nodes_db_column_store_row_group_physical_stats DMV ([DM db_column_store_row_group_physical_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql) contém a definição da exibição correspondente do banco de dados SQL ao SQL Data Warehouse) que expõe informações úteis como o número de linhas em rowgroups e o motivo para a fragmentação se houver fragmentação. Você pode criar a exibição a seguir como uma maneira útil consultar essa DMV para obter informações sobre a fragmentação do grupo de linhas.

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
- CARREGAMENTO EM MASSA: Esse motivo de corte é usado quando o lote de entrada de linhas para a carga tinha menos de um milhão de linhas. O mecanismo criará grupos de linhas compactado se houver mais que 100.000 linhas sendo inseridas (em vez de inserir no repositório delta), mas define o motivo do corte para CARREGAMENTO EM MASSA. Nesse cenário, considere aumentar a carga de lote para incluir mais linhas. Além disso, reavalie o esquema de particionamento para garantir que não está muito granular, já que os grupos de linhas não podem abranger os limites de partição.
- MEMORY_LIMITATION: Para criar grupos de linhas com um milhão de linhas, uma determinada quantidade de memória de trabalho é necessária para o mecanismo. Quando a memória disponível da sessão de carregamento é menor do que a memória necessária do trabalho, grupos de linhas são cortados prematuramente. As seções a seguir explicam como estimar a memória necessária e alocar mais memória.
- DICTIONARY_SIZE: Esse motivo do corte indica que a fragmentação do grupo de linhas ocorreu devido a pelo menos uma coluna de cadeia de caracteres com cadeias de caracteres amplas e/ou de alta cardinalidade. O tamanho do dicionário está limitado a 16 MB de memória e quando esse limite é atingido o grupo de linhas é compactado. Se você se deparar com essa situação, considere isolar a coluna problemática em uma tabela separada.

## <a name="how-to-estimate-memory-requirements"></a>Como estimar os requisitos de memória

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

O máximo de memória necessário para compactar um rowgroup é aproximadamente

- 72 MB +
- \#linhas \* \#colunas \* 8 bytes +
- \#linhas \* \#colunas de cadeia de caracteres curta \* 32 bytes +
- \#colunas de cadeia de caracteres longa \* 16 MB para o dicionário de compactação

em que as colunas de cadeia de caracteres curta usam tipos de dados de cadeia de caracteres de <= 32 bytes e as colunas de cadeia de caracteres longa usam tipos de dados de cadeia de caracteres de > 32 bytes.

As cadeias de caracteres longas são compactadas com um método de compactação projetado para a compactação de texto. Esse método de compactação usa um *dicionário* para armazenar os padrões de texto. O tamanho máximo de um dicionário é de 16 MB. Há apenas um dicionário para cada coluna de cadeia de caracteres longa no rowgroup.

Para obter uma discussão detalhada sobre os requisitos de memória de columnstore, assista ao vídeo [Escala do SQL Data Warehouse do Azure: configuração e diretrizes](https://channel9.msdn.com/Events/Ignite/2016/BRK3291).

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

Os índices Columnstore criam um ou mais rowgroups por partição. No SQL Data Warehouse, o número de partições aumenta rapidamente porque os dados são distribuídos e cada distribuição é particionada. Se a tabela tiver um número excessivo de partições, talvez não haja linhas suficientes para preencher os rowgroups. A falta de linhas não cria a pressão de memória durante a compactação, mas leva a rowgroups que não obtêm o melhor desempenho de consulta de columnstore.

Outro motivo para evitar o excesso de particionamento é que há uma sobrecarga de memória no carregamento de linhas em um índice columnstore em uma tabela particionada. Durante o carregamento, várias partições poderão receber as linhas de entrada, que são mantidas na memória até que cada partição tenha linhas suficientes para ser compactada. Ter um número excessivo de partições cria pressão de memória adicional.

### <a name="simplify-the-load-query"></a>Simplificar a consulta de carga

O banco de dados compartilha a concessão de memória para uma consulta entre todos os operadores na consulta. Quando uma consulta de carga tem classificações e junções complexas, a memória disponível para compactação é reduzida.

Crie a consulta de carga para que ela se concentre apenas no carregamento da consulta. Se você precisar executar transformações nos dados, execute-as separadamente da consulta de carga. Por exemplo, prepare os dados em uma tabela de heap, execute as transformações e carregue a tabela de preparo no índice columnstore. Você também pode carregar os dados primeiro e usar o sistema MPP para transformar os dados.

### <a name="adjust-maxdop"></a>Ajustar o MAXDOP

Cada distribuição compacta rowgroups no columnstore em paralelo quando há mais de um núcleo de CPU disponível por distribuição. O paralelismo exige recursos de memória adicionais, o que pode levar à pressão de memória e ao corte de rowgroup.

Para reduzir a pressão de memória, use a dica de consulta MAXDOP para forçar a operação de carregamento a ser executada em modo serial em cada distribuição.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Maneiras de alocar mais memória

O tamanho da DWU e a classe de recurso de usuário em conjunto determinam a quantidade de memória disponível para uma consulta de usuário. Para aumentar a concessão de memória para uma consulta de carga, você pode aumentar o número de DWUs ou aumentar a classe de recurso.

- Para aumentar as DWUs, consulte [Como faço para escalar o desempenho?](quickstart-scale-compute-portal.md)
- Para alterar a classe de recurso de uma consulta, consulte [Alterar um exemplo de classe de recurso de usuário](resource-classes-for-workload-management.md#change-a-users-resource-class).

## <a name="next-steps"></a>Próximas etapas

Para encontrar mais maneiras de melhorar o desempenho no SQL Data Warehouse, consulte a [Visão geral do desempenho](sql-data-warehouse-overview-manage-user-queries.md).

