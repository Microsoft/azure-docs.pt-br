---
title: "Orientação sobre tipos de dados - SQL Data Warehouse do Azure | Microsoft Docs"
description: "Recomendações para definir os tipos de dados que são compatíveis com o SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: barbkess
editor: 
ms.assetid: d4a1f0a3-ba9f-44b9-95f6-16a4f30746d6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 06/02/2017
ms.author: shigu;barbkess
ms.openlocfilehash: 5c24c71af16bd9851d9caf15fecfa4bb76f5f77e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="guidance-for-defining-data-types-for-tables-in-sql-data-warehouse"></a>Orientação para definição de tipos de dados para as tabelas no SQL Data Warehouse
Use estas recomendações para definir os tipos de dados de tabela que são compatíveis com o SQL Data Warehouse. Além da compatibilidade, minimizar o tamanho dos tipos de dados melhora o desempenho da consulta.

O SQL Data Warehouse oferece suporte aos tipos comuns de dados usados. Para obter uma lista dos tipos de dados com suporte, consulte [tipos de dados](/sql/docs/t-sql/statements/create-table-azure-sql-data-warehouse.md#datatypes) na instrução CREATE TABLE. 


## <a name="minimize-row-length"></a>Minimizar o tamanho da linha
Minimizar o tamanho dos tipos de dados reduz o tamanho da linha, o que leva a um desempenho de consulta melhor. Use o menor tipo de dados que funcione para seus dados. 

- Evite definir as colunas de caractere como um tamanho padrão grande. Por exemplo, se o maior valor for 25 caracteres, defina a coluna como VARCHAR(25). 
- Evite usar [NVARCHAR][NVARCHAR] quando precisar somente de VARCHAR.
- Quando possível, use NVARCHAR(4000) ou VARCHAR(8000) em vez de NVARCHAR(MAX) ou VARCHAR(MAX).

Se estiver usando o Polybase para carregar as tabelas, o tamanho definido da linha da tabela não pode exceder 1 MB. Quando uma linha com dados de tamanho variável exceder 1 MB, você poderá carregar a linha com BCP, mas não com PolyBase.

## <a name="identify-unsupported-data-types"></a>Identificar tipos de dados sem suporte
Se você estiver migrando o banco de dados de outro banco de dados SQL, poderá encontrar alguns tipos de dados que não têm suporte no SQL Data Warehouse. Use esta consulta para descobrir os tipos de dados sem suporte em seu esquema SQL existente.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','timestamp','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Usar soluções alternativas para os tipos de dados sem suporte

A lista a seguir mostra os tipos de dados para os quais o SQL Data Warehouse não dá suporte e fornece alternativas que você pode usar no lugar dos tipos de dados sem suporte.

| Tipos de dados sem suporte | Solução alternativa |
| --- | --- |
| [geometry][geometry] |[varbinary][varbinary] |
| [geography][geography] |[varbinary][varbinary] |
| [hierarchyid][hierarchyid] |[nvarchar][nvarchar](4000) |
| [image][ntext,text,image] |[varbinary][varbinary] |
| [text][ntext,text,image] |[varchar][varchar] |
| [ntext][ntext,text,image] |[nvarchar][nvarchar] |
| [sql_variant][sql_variant] |Divida a coluna em várias colunas fortemente tipadas. |
| [table][table] |Converta em tabelas temporárias. |
| [timestamp][timestamp] |Retrabalhe o código para usar [datetime2][datetime2] e a função `CURRENT_TIMESTAMP`.  Somente as constantes são suportados como padrões, portanto, current_timestamp não pode ser definida como uma restrição padrão. Se precisar migrar os valores de versão de linha de uma coluna tipada com o carimbo de data/hora, use [BINARY][BINARY](8) ou [VARBINARY][BINARY](8) para os valores de versão de linha NOT NULL ou NULL. |
| [xml][xml] |[varchar][varchar] |
| [Tipos definidos pelo usuário][user defined types] |Converta para o tipo de dados nativo quando possível. |
| valores padrão | Os valores padrão dão suporte somente a literais e constantes.  Não há suporte para expressões ou funções não determinísticas como `GETDATE()` ou `CURRENT_TIMESTAMP`. |


## <a name="next-steps"></a>Próximas etapas
Para obter mais informações, consulte:

- [Práticas recomendadas do SQL Data Warehouse][SQL Data Warehouse Best Practices]
- [Visão geral da tabela][Overview]
- [Distribuição de uma tabela][Distribute]
- [Indexação de uma tabela][Index]
- [Particionamento de uma tabela][Partition]
- [Manter estatísticas de tabela][Statistics]
- [Tabelas temporárias][Temporary]

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->

<!--Other Web references-->
[create table]: https://msdn.microsoft.com/library/mt203953.aspx
[bigint]: https://msdn.microsoft.com/library/ms187745.aspx
[binary]: https://msdn.microsoft.com/library/ms188362.aspx
[bit]: https://msdn.microsoft.com/library/ms177603.aspx
[char]: https://msdn.microsoft.com/library/ms176089.aspx
[date]: https://msdn.microsoft.com/library/bb630352.aspx
[datetime]: https://msdn.microsoft.com/library/ms187819.aspx
[datetime2]: https://msdn.microsoft.com/library/bb677335.aspx
[datetimeoffset]: https://msdn.microsoft.com/library/bb630289.aspx
[decimal]: https://msdn.microsoft.com/library/ms187746.aspx
[float]: https://msdn.microsoft.com/library/ms173773.aspx
[geometry]: https://msdn.microsoft.com/library/cc280487.aspx
[geography]: https://msdn.microsoft.com/library/cc280766.aspx
[hierarchyid]: https://msdn.microsoft.com/library/bb677290.aspx
[int]: https://msdn.microsoft.com/library/ms187745.aspx
[money]: https://msdn.microsoft.com/library/ms179882.aspx
[nchar]: https://msdn.microsoft.com/library/ms186939.aspx
[nvarchar]: https://msdn.microsoft.com/library/ms186939.aspx
[ntext,text,image]: https://msdn.microsoft.com/library/ms187993.aspx
[real]: https://msdn.microsoft.com/library/ms173773.aspx
[smalldatetime]: https://msdn.microsoft.com/library/ms182418.aspx
[smallint]: https://msdn.microsoft.com/library/ms187745.aspx
[smallmoney]: https://msdn.microsoft.com/library/ms179882.aspx
[sql_variant]: https://msdn.microsoft.com/library/ms173829.aspx
[sysname]: https://msdn.microsoft.com/library/ms186939.aspx
[table]: https://msdn.microsoft.com/library/ms175010.aspx
[time]: https://msdn.microsoft.com/library/bb677243.aspx
[timestamp]: https://msdn.microsoft.com/library/ms182776.aspx
[tinyint]: https://msdn.microsoft.com/library/ms187745.aspx
[uniqueidentifier]: https://msdn.microsoft.com/library/ms187942.aspx
[varbinary]: https://msdn.microsoft.com/library/ms188362.aspx
[varchar]: https://msdn.microsoft.com/library/ms186939.aspx
[xml]: https://msdn.microsoft.com/library/ms187339.aspx
[user defined types]: https://msdn.microsoft.com/library/ms131694.aspx
