---
title: Usando exibições do T-SQL no Azure SQL Data Warehouse | Microsoft Docs
description: Dicas para usar exibições T-SQL no Azure SQL Data Warehouse para desenvolvimento de soluções.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 8a770e66120e69271744942899186ece39b2a3c3
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479515"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Exibições no Azure SQL Data Warehouse
Dicas para usar exibições T-SQL no Azure SQL Data Warehouse para desenvolvimento de soluções. 

## <a name="why-use-views"></a>Por que usar exibições?
As exibições podem ser usadas em diversas maneiras diferentes de melhorar a qualidade de sua solução.  Este artigo destaca alguns exemplos de como aprimorar sua solução com exibições, bem como as limitações que precisam ser consideradas.


> [!IMPORTANT]
> Consulte a nova sintaxe de exibição materializada em [criar exibição materializada como SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).  Para obter mais informações, consulte as [notas de versão](/azure/sql-data-warehouse/release-notes-10-0-10106-0).
>


> [!NOTE]
> A sintaxe para CREATE VIEW não é discutida neste artigo. Para saber mais, consulte a documentação [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql).
> 

## <a name="architectural-abstraction"></a>Abstração de arquitetura

Um padrão de aplicativo comum é recriar tabelas usando CREATE TABLE AS SELECT (CTAS) seguido por um objeto de renomeação do padrão durante o carregamento dos dados.

O exemplo a seguir adiciona novos registros de data para uma dimensão de data. Observe como uma nova tabela, DimDate_New, é criada pela primeira vez e renomeada para substituir a versão original da tabela.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

No entanto, essa abordagem pode resultar em tabelas que aparecem e desaparecem da exibição do usuário, bem como nas mensagens de erro "a tabela não existe". As exibições podem ser usadas para fornecer aos usuários uma camada de apresentação consistente enquanto os objetos subjacentes são renomeados. Fornecendo acesso a dados por meio de exibições, os usuários não precisam de visibilidade para as tabelas subjacentes. Essa camada fornece uma experiência de usuário consistente enquanto garante que os designers do data warehouse podem desenvolver o modelo de dados. Capacidade para desenvolver as tabelas subjacentes significa que os designers podem usar CTAS para maximizar o desempenho durante o processo de carregamento de dados.   

## <a name="performance-optimization"></a>Otimização do desempenho
As exibições também podem ser utilizadas para impor junções de desempenho otimizadas entre as tabelas. Por exemplo, uma exibição pode incorporar uma chave de distribuição redundante como parte dos critérios de junção para minimizar a movimentação dos dados. Outro benefício de uma exibição pode ser forçar uma dica de consulta ou junção específica. Usar exibições dessa maneira garante que as junções sempre sejam executadas de forma ideal, evitando a necessidade dos usuários lembrarem a construção correta de suas junções.

## <a name="limitations"></a>Limitações
Os modos de exibição no SQL Data Warehouse são armazenados somente como metadados. Consequentemente, as opções a seguir não estão disponíveis:

* Não há opção de associação de esquema
* Tabelas base não podem ser atualizadas por meio da exibição
* Exibições não podem ser criadas em tabelas temporárias
* Não há suporte para as dicas EXPAND / NOEXPAND
* não há exibições indexadas no SQL Data Warehouse

## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento do SQL Data Warehouse](sql-data-warehouse-overview-develop.md).


