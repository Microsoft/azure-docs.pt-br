---
title: Usando loops T-SQL
description: Dicas para o desenvolvimento de soluções usando loops T-SQL e substituindo cursores por pools SQL dedicados no Azure Synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: a6c4eb98d77ece6e6ae130fd57cc263ee7e5ca64
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683215"
---
# <a name="using-t-sql-loops-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Usando loops T-SQL para pools de SQL dedicados no Azure Synapse Analytics

Estão incluídos neste artigo Dicas para o desenvolvimento de solução de pools SQL dedicado usando loops T-SQL e substituindo cursores.

## <a name="purpose-of-while-loops"></a>Finalidade de loops WHILE

Os pools dedicados do SQL no Azure Synapse dão suporte ao loop [while](/sql/t-sql/language-elements/while-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para execução repetida de blocos de instrução. Esse loop WHILE continuará desde que as condições especificadas sejam verdadeiras ou até que o código especificamente encerre o loop usando a palavra-chave BREAK.

A execução de loops é útil para a substituição de cursores definidos no código SQL. Felizmente, quase todos os cursores que são escritos em código SQL são do tipo somente leitura de avanço rápido. Portanto, os loops WHILE são uma ótima alternativa para substituir cursores.

## <a name="replacing-cursors-in-dedicated-sql-pool"></a>Substituindo cursores no pool SQL dedicado

No entanto, antes de mergulhar no início, você deve fazer a seguinte pergunta: "este cursor pode ser reescrito para usar operações baseadas em conjunto?"

Em muitos casos, a resposta é sim e geralmente é a melhor abordagem. Uma operação baseada em conjunto geralmente terá um desempenho mais rápido do que uma abordagem iterativa de linha por linha.

Os cursores somente leitura de avanço rápido podem ser facilmente substituídos por um constructo de looping. O exemplo a seguir é simples. Este exemplo de código atualiza as estatísticas para cada tabela no banco de dados. Iterando sobre as tabelas no loop, cada comando é executado em sequência.

Primeiro, crie uma tabela temporária que contém um número de linha exclusivo usado para identificar as instruções individuais:

```sql
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Em segundo lugar, inicialize as variáveis necessárias para executar o loop:

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Agora, execute um loop sobre as instruções para executar uma por vez:

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Finalmente, descarte a tabela temporária criada na primeira etapa

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).
