---
title: Atribuir variáveis no SQL Data Warehouse do Azure | Microsoft Docs
description: Dicas para atribuir variáveis de T-SQL no SQL Data Warehouse do Azure para desenvolver soluções.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 6c943478f3904aac17a572f012f2b2b69ffa2223
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479554"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Atribuindo variáveis no SQL Data Warehouse do Azure

Dicas para atribuir variáveis de T-SQL no SQL Data Warehouse do Azure para desenvolver soluções.

## <a name="setting-variables-with-declare"></a>Definição de variáveis com DECLARE

As variáveis no SQL Data Warehouse são definidas usando a instrução `DECLARE` ou a instrução `SET`. A inicialização de variáveis com DECLARE é uma das maneiras mais flexíveis de definir um valor de variável no SQL Data Warehouse.

```sql
DECLARE @v  int = 0
;
```

Você também pode usar DECLARE para definir mais de uma variável por vez. Não é possível usar SELECT nem UPDATE para fazer o seguinte:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Não é possível inicializar nem usar uma variável na mesma instrução DECLARE. Para ilustrar esse ponto, o exemplo a seguir **não** é permitido, já que @p1 foi inicializado e usado na mesma instrução DECLARE. O exemplo a seguir apresenta um erro.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Definição de valores com SET

SET é um método comum para definir uma única variável.

As instruções a seguir sempre são maneiras válidas de definir uma variável com SET:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Você só pode definir uma variável por vez com SET. No entanto, os operadores compostos são permitidos.

## <a name="limitations"></a>Limitações

Você não pode usar UPDATE para atribuição de variável.

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).
