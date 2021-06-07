---
title: Funções de verificação de tipo no Azure Cosmos DB linguagem de consulta
description: Saiba mais sobre a verificação de tipo das funções do sistema SQL no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2becc9216d847dfe26d8fd3a433993112fff7980
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96546338"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Funções de verificação de tipo (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

As funções de verificação de tipo permitem verificar o tipo de uma expressão em uma consulta SQL. Você pode usar funções de verificação de tipo para determinar os tipos de propriedades em itens imediatamente, quando elas são variáveis ou desconhecidas. 

## <a name="functions"></a>Funções

Aqui está uma tabela de funções internas de verificação de tipo com suporte:

As funções a seguir dão suporte a verificação de tipo em relação aos valores de entrada e retornam, cada um, um valor booliano.  

* [IS_ARRAY](sql-query-is-array.md)
* [IS_BOOL](sql-query-is-bool.md)
* [IS_DEFINED](sql-query-is-defined.md)
* [IS_NULL](sql-query-is-null.md)
* [IS_NUMBER](sql-query-is-number.md)
* [IS_OBJECT](sql-query-is-object.md)
* [IS_PRIMITIVE](sql-query-is-primitive.md)
* [IS_STRING](sql-query-is-string.md)

## <a name="next-steps"></a>Próximas etapas

- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções definidas pelo usuário](sql-query-udfs.md)
- [Agregações](sql-query-aggregate-functions.md)
