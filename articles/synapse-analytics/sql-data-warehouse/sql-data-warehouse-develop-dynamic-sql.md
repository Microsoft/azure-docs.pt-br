---
title: Usando SQL dinâmico
description: Dicas para soluções de desenvolvimento usando SQL dinâmico para pools SQL dedicados no Azure Synapse Analytics.
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
ms.openlocfilehash: 18dcdbf5a4840fda3c2689cc3c8c003470bf1c7f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679594"
---
# <a name="dynamic-sql-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>SQL dinâmico para pools SQL dedicados no Azure Synapse Analytics

Estão incluídos neste artigo Dicas para soluções de desenvolvimento que usam SQL dinâmico em pools SQL dedicados.

## <a name="dynamic-sql-example"></a>Exemplo de SQL dinâmico

Ao desenvolver o código do aplicativo para pools SQL dedicados, talvez seja necessário usar o SQL dinâmico para ajudar a fornecer soluções flexíveis, genéricas e modulares. Os pools dedicados do SQL não dão suporte a tipos de dados de blob no momento.

O não suporte a tipos de dados blob pode limitar o tamanho de suas cadeias de caracteres desde que os tipos de dados blobs incluam tipos varchar (máx) e nvarchar (máx).

Se você tiver usado esses tipos no código do aplicativo para criar cadeias de caracteres grandes, será necessário dividir o código em partes e usar a instrução EXEC em vez disso.

Um exemplo simples:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Se a cadeia de caracteres for curta, você poderá usar [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) normalmente.

> [!NOTE]
> As instruções executadas como SQL dinâmico ainda estarão sujeitas a todas as regras de validação do T-SQL.

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).
