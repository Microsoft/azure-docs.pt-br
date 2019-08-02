---
title: Diretrizes de design para tabelas distribuídas – SQL Data Warehouse do Azure | Microsoft Docs
description: Recomendações para a criação de tabelas distribuídas por hash e round-robin no SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 4b322415592a7202387cb6776d2c040cda765b27
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479346"
---
# <a name="guidance-for-designing-distributed-tables-in-azure-sql-data-warehouse"></a>Diretrizes de design para tabelas distribuídas no SQL Data Warehouse do Azure
Recomendações para a criação de tabelas distribuídas por hash e round-robin no SQL Data Warehouse do Azure.

Este artigo pressupõe que você esteja familiarizado com os conceitos de movimentação e distribuição de dados no SQL Data Warehouse.  Para obter mais informações, consulte [SQL Data Warehouse do Azure – Arquitetura MPP (processamento paralelo massivo)](massively-parallel-processing-mpp-architecture.md). 

## <a name="what-is-a-distributed-table"></a>O que é uma tabela distribuída?
Uma tabela distribuída é exibida como uma única tabela, mas as linhas são armazenadas em 60 distribuições. As linhas são distribuídas com um algoritmo round-robin ou hash.  

**Tabelas distribuídas por hash** melhoram o desempenho de consulta em grandes tabelas de fatos e são o foco deste artigo. **Tabelas de round-robin** são úteis para melhorar a velocidade do carregamento. Essas opções de design têm um impacto significativo em melhorar o desempenho de carregamento e consulta.

Outra opção de armazenamento de tabela é replicar uma pequena tabela em todos os nós de computação. Para obter mais informações, confira [Criação de diretrizes para tabelas replicadas](design-guidance-for-replicated-tables.md). Para escolher rapidamente entre as três opções, consulte tabelas distribuídas na [visão geral de tabelas](sql-data-warehouse-tables-overview.md). 

Como parte do design de tabela, compreenda seus dados o tanto quanto possível e a maneira como eles são consultados.  Por exemplo, considere estas perguntas:

- Qual é o tamanho da tabela?   
- Com que frequência a tabela é atualizada?   
- Há tabelas de dimensões e fatos no data warehouse?   


### <a name="hash-distributed"></a>Tabelas distribuídas por hash
Uma tabela distribuída por hash distribui linhas da tabela em todos os nós de computação usando uma função de hash determinística para atribuir cada linha a uma [distribuição](massively-parallel-processing-mpp-architecture.md#distributions). 

![Tabela distribuída](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Tabela distribuída")  

Como valores idênticos sempre hash para a mesma distribuição, o data warehouse tem conhecimento interno dos locais de linha. SQL Data Warehouse usa esse conhecimento para minimizar a movimentação de dados durante as consultas, o que melhora o desempenho da consulta. 

Tabelas distribuídas por hash funcionam bem para grandes tabelas de fatos em um esquema em estrela. Podem ter um grande número de linhas e ainda obter um alto desempenho. É claro, há algumas considerações de design que ajudam você a obter o desempenho que o sistema distribuído foi desenvolvido para fornecer. Escolher uma boa coluna de distribuição é uma consideração que é descrita neste artigo. 

Considere o uso de uma tabela distribuída por hash quando:

- O tamanho da tabela no disco é maior de 2 GB.
- A tabela tiver operações frequentes de inserção, atualização e exclusão. 

### <a name="round-robin-distributed"></a>Distribuição round robin
Uma tabela round robin distribui linhas de tabela uniformemente em todas as distribuições. A atribuição de linhas para distribuições é aleatória. Ao contrário das tabelas distribuídas por hash, não há garantia de que as linhas com valores iguais sejam atribuídas à mesma distribuição. 

Como resultado, o sistema às vezes precisa chamar uma operação de movimentação de dados para organizar melhor seus dados antes de poder resolver uma consulta.  Essa etapa extra pode causar lentidão em suas consultas. Por exemplo, adicionar uma tabela de round-robin geralmente requer embaralhar linhas, que é uma queda no desempenho.

Considere usar a distribuição round robin para a sua tabela nos seguintes cenários:

- Ao começar, como um simples ponto de partida já que é padrão
- Se não houver uma chave de junção óbvia
- Se não houver colunas candidatas boas para distribuir a tabela de hash
- Se a tabela não compartilhar uma chave de junção comum com outras tabelas
- Se a junção for menos significativa do que outras junções na consulta
- Quando a tabela é uma tabela temporária de preparo

O tutorial [Carregar dados do New York taxicab para o SQL Data Warehouse do Azure](load-data-from-azure-blob-storage-using-polybase.md#load-the-data-into-your-data-warehouse) fornece um exemplo de carregamento de dados em uma tabela de preparo de round-robin.


## <a name="choosing-a-distribution-column"></a>Escolher uma coluna de distribuição
Uma tabela distribuída por hash tem uma coluna de distribuição que é a chave de hash. Por exemplo, o código a seguir cria uma tabela distribuída por hash com ProductKey como a coluna de distribuição.

```SQL
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
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
,  DISTRIBUTION = HASH([ProductKey])
)
;
``` 

Escolher uma coluna de distribuição é uma decisão de design importante como os valores nesta coluna determinam como as linhas são distribuídas. A melhor escolha depende de vários fatores e geralmente envolve as vantagens e desvantagens. No entanto, se você não escolher a melhor coluna na primeira vez, você pode usar [Criar tabela como selecionar (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) para recriar a tabela com uma coluna de distribuição diferente. 

### <a name="choose-a-distribution-column-that-does-not-require-updates"></a>Escolha uma coluna de distribuição que não necessita de atualizações
Você não pode atualizar uma coluna de distribuição, a menos que você exclua a linha e insira uma nova linha com os valores atualizados. Portanto, selecione uma coluna com valores estáticos. 

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Escolha uma coluna de distribuição com dados que distribui uniformemente

Para melhor desempenho, todas as distribuições devem ter aproximadamente o mesmo número de linhas. Quando uma ou mais distribuições tem um número desproporcional de linhas, algumas distribuições concluem sua parte de uma consulta paralela antes de outros. Uma vez que a consulta não pode concluir até que todas as distribuições concluam o processamento, cada consulta é somente tão rápida quanto a distribuição mais lenta.

- Distorção de dados significa que os dados não são distribuídos uniformemente entre as distribuições
- Processar distorção significa que algumas distribuições demoram mais do que outras ao executar consultas em paralelo. Isso pode acontecer quando os dados estão distorcidos.
  
Para equilibrar o processamento paralelo, selecione uma coluna de distribuição que:

- **Tem muitos valores exclusivos.** A coluna pode ter alguns valores duplicados. No entanto, todas as linhas com o mesmo valor são atribuídas para a mesma distribuição. Como há 60 distribuições, a coluna deve ter pelo menos 60 valores exclusivos.  Normalmente, o número de valores exclusivos é muito maior.
- **Não tem valores nulos ou tem apenas alguns valores nulos.** Para obter um exemplo extremo, se todos os valores na coluna forem NULL, todas as linhas são atribuídas para a mesma distribuição. Como resultado, o processamento de consulta é afetado por uma distribuição e não se beneficia com processamento paralelo. 
- **Não é uma coluna de dados**. Todos os dados para a mesma data chegam na mesma distribuição. Se vários usuários são filtrados na mesma data, apenas 1 das 60 distribuições faz todo o trabalho de processamento. 

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Escolha uma coluna de distribuição que minimiza a movimentação de dados

Para obter a consulta correta os resultados de consultas podem mover dados de um nó de computação para outro. Movimentação de dados geralmente acontece quando as consultas em tabelas distribuídas contêm junções e agregações. Escolher uma coluna de distribuição que ajuda a minimizar movimentação de dados é uma das estratégias mais importantes para otimizar o desempenho do SQL Data Warehouse.

Para minimizar a movimentação de dados selecione a coluna de distribuição que:

- É usada nas cláusulas `JOIN`, `GROUP BY`, `DISTINCT`, `OVER`, e `HAVING`. Quando duas grandes tabelas de fatos têm junções frequentes, o desempenho da consulta melhora quando você distribui ambas as tabelas em uma das colunas de junção.  Quando uma tabela não é usada em junções, considere distribuir a tabela em uma coluna que é frequentemente na cláusula `GROUP BY`.
- *Não* é usada em cláusulas `WHERE`. Isso pode restringir a consulta para não executar em todas as distribuições. 
- *Não* é uma coluna de dados. Geralmente, cláusulas WHERE filtram por data.  Quando isso acontece, todo o processamento pode ser executado em apenas algumas distribuições.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>O que fazer quando nenhuma das colunas são em uma boa coluna de distribuição

Se nenhuma das suas colunas tiver valores distintos suficientes para uma coluna de distribuição, será possível criar uma nova coluna como uma composição de um ou mais valores. Para evitar a movimentação de dados durante a execução da consulta, use a coluna de distribuição de composição como uma coluna de junção em consultas.

Quando você cria uma tabela distribuída por hash, a próxima etapa é carregar dados na tabela.  Para carregar as diretrizes, consulte [Visão geral de carregamento](sql-data-warehouse-overview-load.md). 

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Como saber se a coluna de distribuição é uma boa opção
Depois que os dados são carregados em uma tabela distribuída por hash, verifique para ver como as linhas são distribuídas uniformemente entre as distribuições de 60. As linhas por distribuição podem variar até 10% sem um impacto significativo no desempenho. 

### <a name="determine-if-the-table-has-data-skew"></a>Determinar se a tabela tem distorção de dados
Uma maneira rápida de verificar a distorção de dados é usar [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql). O código SQL a seguir retorna o número de linhas da tabela que são armazenados em cada uma das 60 distribuições. Para um desempenho mais equilibrado, as linhas na tabela distribuída devem ser divididas uniformemente entre todas as distribuições.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Para identificar quais tabelas têm distorção de dados maior de 10%:

1. Criar o modo de exibição dbo.vTableSizes que é mostrado no artigo [visão geral de tabelas](sql-data-warehouse-tables-overview.md#table-size-queries).  
2. Execute a consulta a seguir:

```sql
select *
from dbo.vTableSizes
where two_part_name in
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having (max(row_count * 1.000) - min(row_count * 1.000))/max(row_count * 1.000) >= .10
    )
order by two_part_name, row_count
;
```

### <a name="check-query-plans-for-data-movement"></a>Planos de consulta de seleção para a movimentação de dados
Uma boa coluna de distribuição permite junções e agregações para que o movimento de dados seja mínimo. Isso afeta a maneira que as junções devem ser gravadas. Para obter o movimento de dados mínimo de uma junção em duas tabelas distribuídas por hash, uma das colunas de junção deve ser a coluna de distribuição.  Quando duas tabelas distribuídas por hash são integradas em uma coluna de distribuição do mesmo tipo de dados, a junção não exige a movimentação de dados. Junções podem usar colunas adicionais sem incorrer em movimento de dados.

Para evitar a movimentação de dados durante uma junção:

- As tabelas envolvidas na junção devem ser distribuídas por hash em **uma** das colunas que participam da junção.
- Os tipos de dados das colunas de junção devem ser correspondentes entre as duas tabelas.
- As colunas devem ser associadas com um operador equals.
- O tipo de associação pode não ser uma `CROSS JOIN`.

Para ver se consultas estão com a movimentação de dados, você pode examinar o plano de consulta.  


## <a name="resolve-a-distribution-column-problem"></a>Resolver um problema de coluna de distribuição
Não é necessário resolver todos os casos de distorção de dados. A distribuição de dados é uma questão de encontrar o equilíbrio certo entre minimizar a distorção de dados e minimizar a movimentação de dados. Nem sempre é possível minimizar a distorção de dados e a movimentação de dados. Às vezes, o benefício de ter o mínimo de movimentação de dados pode superar o impacto de ter a distorção de dados.

Para decidir se deve resolver a distorção de dados em uma tabela, você deve compreender o máximo possível sobre os volumes de dados e consultas na carga de trabalho. Você pode usar as etapas no artigo [Monitoramento de consulta](sql-data-warehouse-manage-monitor.md) para monitorar o impacto de distorção no desempenho da consulta. Especificamente, procure quanto tempo grandes consultas demoram para ser concluída em distribuições individuais.

Como você não pode alterar a coluna de distribuição em uma tabela existente, uma forma comum de resolver distorção de dados é recriar a tabela com uma coluna de distribuição diferente.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>Criar novamente a tabela com uma nova coluna de distribuição
Este exemplo usa [CREATE TABLE AS SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=aps-pdw-2016-au7) para recriar uma tabela com uma coluna de distribuição de hash diferente.

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

## <a name="next-steps"></a>Próximas etapas

Para criar uma tabela replicada, use uma dessas instruções:

- [CRIAR TABELA (SQL Data Warehouse do Azure)](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [CREATE TABLE AS SELECT (SQL Data Warehouse do Azure](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)


