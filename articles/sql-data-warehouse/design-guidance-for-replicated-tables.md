---
title: Diretrizes de design para tabelas replicadas – SQL Data Warehouse do Azure | Microsoft Docs
description: Recomendações para criar tabelas replicadas em seu esquema do SQL Data Warehouse do Azure. 
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/19/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: acea42f7f4ab986e9828000ab7cfc9e302ed92a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082893"
---
# <a name="design-guidance-for-using-replicated-tables-in-azure-sql-data-warehouse"></a>Diretrizes de design para usar tabelas replicadas no SQL Data Warehouse do Azure
Este artigo fornece recomendações para criar tabelas replicadas no esquema do SQL Data Warehouse. Use essas recomendações para melhorar o desempenho da consulta ao reduzir a movimentação de dados e a complexidade da consulta.

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você esteja familiarizado com os conceitos de movimentação e distribuição de dados no SQL Data Warehouse.  Para saber mais, consulte o artigo sobre [arquitetura](massively-parallel-processing-mpp-architecture.md). 

Como parte do design de tabela, compreenda seus dados o tanto quanto possível e a maneira como eles são consultados.  Por exemplo, considere estas perguntas:

- Qual é o tamanho da tabela?   
- Com que frequência a tabela é atualizada?   
- Há tabelas de dimensões e fatos no data warehouse?   

## <a name="what-is-a-replicated-table"></a>O que é uma tabela replicada?
Uma tabela replicada tem uma cópia completa da tabela acessível em cada nó de computação. Replicar uma tabela elimina a necessidade de transferir dados entre nós de Computação antes de uma junção ou agregação. Como a tabela tem várias cópias, as tabelas replicadas funcionam melhor quando o tamanho da tabela é menor que 2 GB compactados.  2 GB não é um limite rígido.  Se os dados são estáticos e não é alterado, você pode replicar tabelas maiores.

O diagrama a seguir mostra uma tabela replicada que é acessível em cada nó de computação. No SQL Data Warehouse, a tabela replicada é totalmente copiada para um banco de dados de distribuição em cada nó de computação. 

![Tabela replicada](media/guidance-for-using-replicated-tables/replicated-table.png "Tabela replicada")  

As tabelas replicadas funcionam bem para tabelas de dimensão em um esquema em estrela. Tabelas de dimensões geralmente são unidas a tabelas de fatos que são distribuídas de forma diferente do que a tabela de dimensões.  As dimensões são geralmente de um tamanho que torna possível armazenar e manter várias cópias. As dimensões armazenam dados descritivos que são alterados lentamente, como nome e endereço do cliente e detalhes do produto. A natureza dos dados de alteração lenta resulta em menos manutenção da tabela replicada. 

Considere usar uma tabela replicada quando:

- O tamanho da tabela no disco for menor que 2 GB, independentemente do número de linhas. Para localizar o tamanho de uma tabela, você pode usar o comando [DBCC PDW_SHOWSPACEUSED](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql): `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`. 
- A tabela é usada em junções que normalmente exigiriam a movimentação de dados. Ao unir tabelas que não são distribuídas na mesma coluna, como uma tabela distribuída em hash a uma tabela round robin, a movimentação de dados é necessária para concluir a consulta.  Se uma das tabelas é pequena, considere uma tabela replicada. É recomendável usar tabelas replicadas em vez de tabelas round robin na maioria dos casos. Para exibir as operações de movimentação de dados em planos de consulta, use [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql).  O BroadcastMoveOperation é a operação de movimentação de dados típica que pode ser eliminada por meio de uma tabela replicada.  
 
As tabelas replicadas poderão não render o melhor desempenho de consulta quando:

- A tabela tiver operações frequentes de inserção, atualização e exclusão. Essas operações de DML (linguagem de manipulação de dados) exigem uma recompilação da tabela replicada. Recompilar com frequência pode causar um desempenho mais lento.
- O data warehouse for dimensionado com frequência. Dimensionar um data warehouse altera o número de nós de computação, que acarreta a recriação de tabela replicada.
- A tabela tem um grande número de colunas, mas as operações de dados normalmente acessam somente um pequeno número de colunas. Neste cenário, em vez de replicar toda a tabela, pode ser mais eficaz fazer a distribuição da tabela e, em seguida, criar um índice nas colunas acessadas com frequência. Quando uma consulta exigir a movimentação de dados, o SQL Data Warehouse só moverá dados para as colunas solicitadas. 

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Usar tabelas replicadas com predicados de consulta simples
Antes de optar por distribuir ou replicar uma tabela, considere os tipos de consultas que você planeja executar em relação à tabela. Sempre que possível,

- Use tabelas replicadas para consultas com predicados de consulta simples, como igualdade ou desigualdade.
- Use tabelas distribuídas para consultas com predicados de consulta complexos, como CURTIR ou NÃO CURTIR.

As consultas de uso intensivo da CPU apresentam melhor desempenho quando o trabalho é distribuído entre todos os nós de computação. Por exemplo, as consultas que executam cálculos em cada linha de uma tabela apresentam um desempenho melhor nas tabelas distribuídas do que nas tabelas replicadas. Como uma tabela replicada é armazenada na íntegra em cada nó de computação, uma consulta de uso intensivo da CPU em uma tabela replicada é executada em toda a tabela em cada nó de computação. A computação extra pode diminuir o desempenho da consulta.

Por exemplo, esta consulta tem um predicado complexo.  Ele é executado mais rapidamente quando os dados estão em uma tabela distribuída em vez de uma tabela replicada. Neste exemplo, os dados podem ser distribuída round-robin.

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Converter tabelas round robin existentes em tabelas replicadas
Se você já tiver tabelas round robin, é recomendável convertê-las em tabelas replicadas, se eles atendem aos critérios descritos neste artigo. As tabelas replicadas melhoram o desempenho em tabelas round robin porque elas eliminam a necessidade da movimentação de dados.  Uma tabela round robin sempre requer a movimentação de dados para as junções. 

Este exemplo usa [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) para alterar a tabela DimSalesTerritory para uma tabela replicada. Este exemplo funciona independentemente de DimSalesTerritory ser distribuído por hash ou por round robin.

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]   
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE') 

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```  

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Exemplo de desempenho de consulta de round-robin versus replicado 

Uma tabela replicada não requer nenhuma movimentação de dados para as junções porque a tabela completa já está presente em cada nó de computação. Se as tabelas de dimensões forem distribuídas por round robin, uma união copiará a tabela de dimensões na íntegra para cada nó de computação. Para mover os dados, o plano de consulta contém uma operação chamada BroadcastMoveOperation. Esse tipo de operação de movimentação de dados reduz o desempenho da consulta e é eliminada usando tabelas replicadas. Para exibir as etapas do plano de consulta, use a exibição de catálogo do sistema [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql). 

Por exemplo, na consulta a seguir em relação ao esquema AdventureWorks, a tabela `FactInternetSales` é distribuída em hash. As tabelas `DimDate` e `DimSalesTerritory` são tabelas de dimensões menores. Esta consulta retorna o total de vendas na América do Norte, do ano fiscal de 2004:

```sql
SELECT [TotalSalesAmount] = SUM(SalesAmount)
FROM dbo.FactInternetSales s
INNER JOIN dbo.DimDate d
  ON d.DateKey = s.OrderDateKey
INNER JOIN dbo.DimSalesTerritory t
  ON t.SalesTerritoryKey = s.SalesTerritoryKey
WHERE d.FiscalYear = 2004
  AND t.SalesTerritoryGroup = 'North America'
```
Recriamos `DimDate` e `DimSalesTerritory` como tabelas round robin. Como resultado, a consulta mostrou o seguinte plano de consulta, que tem várias operações de movimentação difundidas: 
 
![Plano de consulta round robin](media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

Recriamos `DimDate` e `DimSalesTerritory` como tabelas replicadas e executamos a consulta novamente. O plano de consulta resultante é muito menor e não tem nenhuma movimentação difundida.

![Plano de consulta replicado](media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>Considerações sobre o desempenho para modificar as tabelas replicadas
O SQL Data Warehouse implementa uma tabela replicada, mantendo uma versão mestre da tabela. Ele copia a versão mestre para um banco de dados de distribuição em cada nó de computação. Quando há uma alteração, o SQL Data Warehouse primeiro atualiza a tabela mestre. Em seguida, ele recompila as tabelas em cada nó de computação. Uma recompilação de uma tabela replicada inclui copiar a tabela para cada nó de computação e, em seguida, compilar os índices.  Por exemplo, uma tabela replicada em um DW400 tem 5 cópias dos dados.  Uma cópia mestre e uma cópia completa em cada nó de Computação.  Todos os dados são armazenados em bancos de dados de distribuição. O SQL Data Warehouse usa esse modelo para dar suporte a instruções de modificação de dados mais rápidas e operações de dimensionamento flexíveis. 

As recompilações são necessárias depois que:
- Os dados são carregados ou modificados
- O Data Warehouse é dimensionado para um nível diferente
- A definição da tabela é atualizada

As recompilações não são necessárias após:
- Pausar a operação
- Retomar a operação

A recompilação não acontece imediatamente depois que os dados são modificados. Em vez disso, a recompilação é acionada na primeira vez em que uma consulta faz uma seleção pela tabela.  A consulta que disparou a recompilação imediatamente lê da versão mestre da tabela, enquanto os dados são copiados de forma assíncrona para cada nó de Computação. Até que a cópia de dados esteja concluída, as consultas subsequentes continuarão a usar a versão mestre da tabela.  Se ocorrer qualquer atividade na tabela replicada que força outra recompilação, a cópia dos dados é invalidada e a próxima instrução SELECT disparará dados a serem copiados novamente. 

### <a name="use-indexes-conservatively"></a>Use os índices de forma prudente
As práticas de indexação padrão se aplicam às tabelas replicadas. O SQL Data Warehouse recompila cada índice de tabela replicada como parte da recompilação. Use somente os índices quando o ganho de desempenho superar o custo de recompilar os índices.  
 
### <a name="batch-data-loads"></a>Cargas de dados em lotes
Ao carregar dados em tabelas replicadas, tente minimizar as recompilações ao enviar as cargas de envio em lote juntas. Execute todas as cargas em lote antes de executar as instruções SELECT.

Por exemplo, esse padrão de carga carrega dados de quatro fontes e invoca quatro recompilações. 

- Carregar da fonte de dados 1.
- A instrução SELECT aciona a recompilação 1.
- Carregar da fonte de dados 2.
- A instrução SELECT aciona a recompilação 2.
- Carregar da fonte de dados 3.
- A instrução SELECT aciona a recompilação 3.
- Carregar da fonte de dados 4.
- A instrução SELECT aciona a recompilação 4.

Por exemplo, esse padrão de carga carrega dados de quatro fontes, mas invoca apenas uma recompilação.

- Carregar da fonte de dados 1.
- Carregar da fonte de dados 2.
- Carregar da fonte de dados 3.
- Carregar da fonte de dados 4.
- A instrução SELECT aciona a recompilação.


### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Recompilar uma tabela replicada após um carregamento em lote
Para garantir tempos de execução de consulta consistentes, considere forçar a compilação das tabelas replicadas após um carregamento em lote. Caso contrário, a primeira consulta ainda usará a movimentação de dados para concluir a consulta. 

Essa consulta usa o DMV [sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql) para listar as tabelas replicadas que foram modificadas, mas não foram recompiladas.

```sql 
SELECT [ReplicatedTable] = t.[name]
  FROM sys.tables t  
  JOIN sys.pdw_replicated_table_cache_state c  
    ON c.object_id = t.object_id 
  JOIN sys.pdw_table_distribution_properties p 
    ON p.object_id = t.object_id 
  WHERE c.[state] = 'NotReady'
    AND p.[distribution_policy_desc] = 'REPLICATE'
```
 
Para disparar uma recompilação, execute a instrução a seguir em cada tabela na saída anterior. 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
``` 
 
## <a name="next-steps"></a>Próximas etapas 
Para criar uma tabela replicada, use uma dessas instruções:

- [CRIAR TABELA (SQL Data Warehouse do Azure)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [CREATE TABLE AS SELECT (SQL Data Warehouse do Azure)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)

Para obter uma visão geral das tabelas distribuídas, consulte [Tabelas distribuídas](sql-data-warehouse-tables-distribute.md).



