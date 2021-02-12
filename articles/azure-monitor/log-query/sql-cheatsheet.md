---
title: Folha de referências de SQL para a consulta de log do Azure Monitor | Microsoft Docs
description: Ajuda para usuários que estão familiarizados com o SQL para escrever consultas de log no Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: e9f937323f4e14b5c8c3f30c94bf9d2daef0baea
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606684"
---
# <a name="sql-to-azure-monitor-log-query-cheat-sheet"></a>Folha de referências de SQL para a consulta de log do Azure Monitor 

A tabela abaixo ajuda os usuários que estão familiarizados com o SQL a aprender a linguagem de consulta do Kusto para gravar consultas de log no Azure Monitor. Observe o comando T-SQL para a solução de cenários comuns e o equivalente em uma consulta Azure Monitor log.

## <a name="sql-to-azure-monitor"></a>SQL para Azure Monitor

Descrição |Consulta SQL | Consulta de log do Azure Monitor
----------------------------------------|---------------------------------------------------------------------------------------------------|----------------------------------------
Selecionar todos os dados de uma tabela |`SELECT * FROM dependencies` |<code>dependencies</code>
Selecionar colunas específicas em uma tabela |`SELECT name, resultCode FROM dependencies` |<code>dependencies <br>&#124; project name, resultCode</code>
Selecionar 100 registros em uma tabela |`SELECT TOP 100 * FROM dependencies` |<code>dependencies <br>&#124; take 100</code>
Avaliação de nulo |`SELECT * FROM dependencies WHERE resultCode IS NOT NULL` |<code>dependencies <br>&#124; where isnotnull(resultCode)</code>
Comparação de cadeias de caracteres: igualdade |`SELECT * FROM dependencies WHERE name = "abcde"` |<code>dependencies <br>&#124; where name == "abcde"</code>
Comparação de cadeias de caracteres: substring |`SELECT * FROM dependencies WHERE name like "%bcd%"` |<code>dependencies <br>&#124; where name contains "bcd"</code>
Comparação de cadeias de caracteres: curinga |`SELECT * FROM dependencies WHERE name like "abc%"` |<code>dependencies <br>&#124; where name startswith "abc"</code>
Comparação de datas: último 1 dia |`SELECT * FROM dependencies WHERE timestamp > getdate()-1` |<code>dependencies <br>&#124; where timestamp > ago(1d)</code>
Comparação de datas: intervalo de datas |`SELECT * FROM dependencies WHERE timestamp BETWEEN '2016-10-01' AND '2016-11-01'` |<code>dependencies <br>&#124; where timestamp between (datetime(2016-10-01) .. datetime(2016-10-01))</code>
Comparação de boolianos |`SELECT * FROM dependencies WHERE !(success)` |<code>dependencies <br>&#124; where success == "False" </code>
Classificar |`SELECT name, timestamp FROM dependencies ORDER BY timestamp asc` |<code>dependencies <br>&#124; order by timestamp asc </code>
Distinct |`SELECT DISTINCT name, type  FROM dependencies` |<code>dependencies <br>&#124; summarize by name, type </code>
Agrupamento, Agregação |`SELECT name, AVG(duration) FROM dependencies GROUP BY name` |<code>dependencies <br>&#124; summarize avg(duration) by name </code>
Aliases de coluna, Estender |`SELECT operation_Name as Name, AVG(duration) as AvgD FROM dependencies GROUP BY name` |<code>dependencies <br>&#124; summarize AvgD=avg(duration) by operation_Name <br>&#124; project Name=operation_Name, AvgD</code>
N principais registros por medida |`SELECT TOP 100 name, COUNT(*) as Count FROM dependencies GROUP BY name ORDER BY Count asc` |<code>dependencies <br>&#124; summarize Count=count() by name <br>&#124; top 100 by Count asc</code>
Union |`SELECT * FROM dependencies UNION SELECT * FROM exceptions` |<code>union dependencies, exceptions</code>
União: com condições |`SELECT * FROM dependencies WHERE value > 4 UNION SELECT * FROM exceptions WHERE value < 5` |<code>dependencies <br>&#124; where value > 4 <br>&#124; union (exceptions <br>&#124; where value < 5)</code>
Join |`SELECT * FROM dependencies JOIN exceptions ON dependencies.operation_Id = exceptions.operation_Id`|<code>dependencies <br>&#124; join (exceptions) on operation_Id == operation_Id</code>


## <a name="next-steps"></a>Próximas etapas

- Percorra as lições sobre como [escrever consultas de log em Azure monitor](get-started-queries.md).
