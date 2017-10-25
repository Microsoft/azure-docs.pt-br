---
title: "Usando exibições do T-SQL no Azure SQL Data Warehouse | Microsoft Docs"
description: "Dicas para usar exibições Transact-SQL no Azure SQL Data Warehouse para desenvolvimento de soluções."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: b5208f32-8f4a-4056-8788-2adbb253d9fd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: d2a03be810bd7f792876607ec735eb578b65a3b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="views-in-sql-data-warehouse"></a>Modos de exibição no SQL Data Warehouse
Os modos de exibição são particularmente úteis no SQL Data Warehouse. Eles podem ser usados em diversas maneiras diferentes de melhorar a qualidade de sua solução.  Este artigo destaca alguns exemplos de como aprimorar sua solução com exibições, bem como as limitações que precisam ser consideradas.

> [!NOTE]
> A sintaxe para `CREATE VIEW` não é discutida neste artigo. Consulte o artigo [CREATE VIEW][CREATE VIEW] no MSDN para obter essas informações de referência.
> 
> 

## <a name="architectural-abstraction"></a>Abstração de arquitetura
Um padrão de aplicativo muito comum é recriar tabelas usando CREATE TABLE AS SELECT (CTAS) seguido por um objeto de renomeação do padrão durante o carregamento dos dados.

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

No entanto, essa abordagem pode resultar em tabelas que aparecem e desaparecem da exibição do usuário, bem como nas mensagens de erro "a tabela não existe". As exibições podem ser usadas para fornecer aos usuários uma camada de apresentação consistente enquanto os objetos subjacentes são renomeados. Fornecer aos usuários o acesso aos dados através de uma exibição significa que os usuários não precisam ter visibilidade das tabelas subjacentes. Isso fornece uma experiência de usuário consistente enquanto garante que os designers do data warehouse podem aprimorar o modelo de dados e maximizar o desempenho usando o CTAS durante o processo de carregamento de dados.    

## <a name="performance-optimization"></a>Otimização do desempenho
As exibições também podem ser utilizadas para impor junções de desempenho otimizadas entre as tabelas. Por exemplo, uma exibição pode incorporar uma chave de distribuição redundante como parte dos critérios de junção para minimizar a movimentação dos dados.  Outro benefício de uma exibição pode ser forçar uma dica de consulta ou junção específica. Usar exibições dessa maneira garante que as junções sempre sejam executadas de forma ideal, evitando a necessidade dos usuários lembrarem a construção correta de suas junções.

## <a name="limitations"></a>Limitações
Os modos de exibição no SQL Data Warehouse são somente metadados.  Consequentemente, as opções a seguir não estão disponíveis:

* Não há opção de associação de esquema
* Tabelas base não podem ser atualizadas por meio da exibição
* Exibições não podem ser criadas em tabelas temporárias
* Não há suporte para as dicas EXPAND / NOEXPAND
* não há exibições indexadas no SQL Data Warehouse

## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento do SQL Data Warehouse][SQL Data Warehouse development overview].
Para ver a sintaxe `CREATE VIEW` , consulte [CREATE VIEW][CREATE VIEW].

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CREATE VIEW]: https://msdn.microsoft.com/en-us/library/ms187956.aspx

<!--Other Web references-->
