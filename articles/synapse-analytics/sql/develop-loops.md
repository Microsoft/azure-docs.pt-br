---
title: Usar loops T-SQL
description: Dicas para usar loops T-SQL, substituir cursores e desenvolver soluções relacionadas com o Synapse SQL no Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 0d83e1305a851bf6bafb6c4c79f5caf73f8e44b6
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120879"
---
# <a name="use-t-sql-loops-with-synapse-sql-in-azure-synapse-analytics"></a>Usar loops T-SQL com Synapse SQL no Azure Synapse Analytics

Este artigo fornece dicas essenciais para o uso de loops T-SQL, a substituição de cursores e o desenvolvimento de soluções relacionadas com o SQL Synapse.

## <a name="purpose-of-while-loops"></a>Finalidade de loops WHILE

Synapse SQL dá suporte ao loop [while](/sql/t-sql/language-elements/while-transact-sql?preserve-view=true&view=sql-server-ver15) para executar repetidamente blocos de instrução. Esse loop WHILE continuará desde que as condições especificadas sejam verdadeiras ou até que o código especificamente encerre o loop usando a palavra-chave BREAK. 

Os loops no SQL Synapse são úteis para substituir cursores definidos no código SQL. Felizmente, quase todos os cursores que são escritos em código SQL são do tipo somente leitura de avanço rápido. Portanto, os loops WHILE são uma ótima alternativa para substituir cursores.

## <a name="replace-cursors-in-synapse-sql"></a>Substituir cursores em Synapse SQL

Antes de mergulharmos no, a seguinte pergunta deve ser considerada: "o cursor poderia ser reescrito para usar operações baseadas em conjunto?" Em muitos casos, a resposta é sim e geralmente é a melhor abordagem. Uma operação baseada em conjunto geralmente é executada mais rapidamente do que uma abordagem iterativa, linha por linha.

Os cursores somente leitura de avanço rápido são facilmente substituídos por uma construção de looping. O código a seguir é um exemplo simples. Este exemplo de código atualiza as estatísticas para cada tabela no banco de dados. Iterando sobre as tabelas no loop, cada comando é executado em sequência.

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

Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](develop-overview.md).