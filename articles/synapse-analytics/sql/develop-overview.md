---
title: Recursos para desenvolvimento de recursos do SQL Synapse
description: Conceitos de desenvolvimento, decisões de design, recomendações e técnicas de codificação para Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: d47b4847a12b63532e44a8a1a47101dd065f811b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96446587"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Decisões de design e técnicas de codificação para recursos do Synapse SQL no Azure Synapse Analytics
Neste artigo, você encontrará uma lista de recursos para o pool de SQL dedicado e funções de pool de SQL sem servidor do Synapse SQL. Os artigos recomendados são divididos em duas seções: principais decisões de design e técnicas de desenvolvimento e codificação.

O objetivo desses artigos é ajudá-lo a desenvolver a abordagem técnica ideal para os componentes do SQL Synapse na análise de Synapse do Azure.

## <a name="key-design-decisions"></a>Principais decisões de design
Os artigos abaixo destacam os conceitos e as decisões de design para o desenvolvimento do Synapse SQL:

| Artigo | pool de SQL dedicado | Pool de SQL sem servidor |
| ------- | -------- | ------------- |
| [Conexões](connect-overview.md)                    | Sim | Sim |
| [Simultaneidade e classes de recursos](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Sim    | Não |
| [Transações](develop-transactions.md)              | Sim | Não |
| [Esquemas definidos pelo usuário](develop-user-defined-schemas.md) | Sim | Sim |
| [Distribuição da tabela](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Sim | Não |
| [Índices da tabela](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Sim | Não |
| [Partições de tabela](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Sim | Não |
| [Estatísticas](develop-tables-statistics.md)            | Sim | Sim |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Sim | Não |
| [Tabelas externas](develop-tables-external-tables.md) | Sim | Sim |
| [CETAS](develop-tables-cetas.md)                     | Sim | Sim |


## <a name="recommendations"></a>Recomendações

Abaixo você encontrará artigos essenciais que enfatizam técnicas de codificação específicas, dicas e recomendações para desenvolvimento:

| Artigo | pool de SQL dedicado | Pool de SQL sem servidor |
| ------- | -------- | ------------- |
| [Procedimentos armazenados](develop-stored-procedures.md)  | Sim                | Não                      |
| [Rótulos](develop-label.md)                           | Sim                | Não                      |
| [Exibições](develop-views.md)                             | Sim                | Sim                     |
| [Tabelas temporárias](develop-tables-temporary.md)       | Sim                | Sim                     |
| [SQL dinâmico](develop-dynamic-sql.md)                 | Sim                | Sim                     |
| [Loop](develop-loops.md)                         | Sim                | Sim                     |
| [Agrupar por opções](develop-group-by-options.md)       | Sim                | Não                      |
| [Atribuição de variável](develop-variable-assignment.md) | Sim                | Sim                     |

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações de referência, consulte [instruções T-SQL do pool do SQL](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

