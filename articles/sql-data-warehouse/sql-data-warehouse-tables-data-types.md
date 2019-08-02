---
title: Definindo tipos de dados - SQL Data Warehouse do Azure | Microsoft Docs
description: Recomendações para definir tipos de dados de tabela no SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 61bb977271186699b0a72389e1538573f978c56b
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479361"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Tipos de dados de tabela no SQL Data Warehouse do Azure
Recomendações para definir tipos de dados de tabela no SQL Data Warehouse do Azure. 

## <a name="what-are-the-data-types"></a>Quais são os tipos de dados?

O SQL Data Warehouse oferece suporte aos tipos comuns de dados usados. Para obter uma lista dos tipos de dados com suporte, consulte [tipos de dados](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) na instrução CREATE TABLE. 

## <a name="minimize-row-length"></a>Minimizar o tamanho da linha
Minimizar o tamanho dos tipos de dados reduz o tamanho da linha, o que leva a um desempenho de consulta melhor. Use o menor tipo de dados que funcione para seus dados. 

- Evite definir as colunas de caractere como um tamanho padrão grande. Por exemplo, se o maior valor for 25 caracteres, defina a coluna como VARCHAR(25). 
- Evite usar [NVARCHAR][NVARCHAR] quando precisar somente de VARCHAR.
- Quando possível, use NVARCHAR(4000) ou VARCHAR(8000) em vez de NVARCHAR(MAX) ou VARCHAR(MAX).

Se estiver usando tabelas externas do PolyBase para carregar as tabelas, o tamanho definido da linha da tabela não pode exceder 1 MB. Quando uma linha com dados de tamanho variável exceder 1 MB, você poderá carregar a linha com BCP, mas não com PolyBase.

## <a name="identify-unsupported-data-types"></a>Identificar tipos de dados sem suporte
Se você estiver migrando o banco de dados de outro banco de dados SQL, poderá encontrar alguns tipos de dados que não têm suporte no SQL Data Warehouse. Use esta consulta para descobrir os tipos de dados sem suporte em seu esquema SQL existente.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Soluções alternativas para os tipos de dados sem suporte

A lista a seguir mostra os tipos de dados para os quais o SQL Data Warehouse não dá suporte e fornece alternativas que você pode usar no lugar dos tipos de dados sem suporte.

| Tipos de dados sem suporte | Solução alternativa |
| --- | --- |
| [geometria](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [geografia](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Divida a coluna em várias colunas fortemente tipadas. |
| [tabela](/sql/t-sql/data-types/table-transact-sql) |Converta em tabelas temporárias. |
| [timestamp](/sql/t-sql/data-types/date-and-time-types) |Refazer o código para usar [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) e a função [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql). Somente as constantes são suportados como padrões, portanto, current_timestamp não pode ser definida como uma restrição padrão. Se precisar migrar os valores de versão de linha de uma coluna tipada com o carimbo de data/hora, use [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) ou [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) para os valores de versão de linha NOT NULL ou NULL. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [Tipos definidos pelo usuário](/sql/relational-databases/native-client/features/using-user-defined-types) |Converta para o tipo de dados nativo quando possível. |
| valores padrão | Os valores padrão dão suporte somente a literais e constantes. |


## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o desenvolvimento de tabelas, consulte [Visão geral da tabela](sql-data-warehouse-tables-overview.md).
