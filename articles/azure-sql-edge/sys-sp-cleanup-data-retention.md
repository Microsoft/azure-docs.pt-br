---
title: sys.sp_cleanup_data_retention (Transact-SQL)-Azure SQL Edge
description: Saiba mais sobre como usar o sys.sp_cleanup_data_retention (Transact-SQL) no Azure SQL Edge
keywords: sys.sp_cleanup_data_retention (Transact-SQL), SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 9c0a6700a476d4f7f875af5373e3c99bc4e25af2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90933110"
---
# <a name="syssp_cleanup_data_retention-transact-sql"></a>sys.sp_cleanup_data_retention (Transact-SQL)

**Aplica-se a:** Borda do SQL do Azure

Executa a limpeza de registros obsoletos de tabelas que têm políticas de retenção de dados habilitadas. Para obter mais informações, consulte [retenção de dados](data-retention-overview.md). 

## <a name="syntax"></a>Sintaxe 

```sql
sys.sp_cleanup_data_retention   
    { [@schema_name = ] 'schema_name' },  
    { [@table_name = ] 'table_name' },   
    [ [@rowcount =] rowcount OUTPUT ]    

```

## <a name="arguments"></a>Argumentos  
`[ @schema_name = ] schema_name`    
 É o nome do esquema proprietário da tabela na qual a limpeza precisa ser executada. *schema_name* é um parâmetro obrigatório do tipo **sysname**.
  
`[ @table_name = ] 'table_name'`    
 É o nome da tabela na qual a operação de limpeza precisa ser executada. *table_name* é um parâmetro obrigatório do tipo **sysname**.

## <a name="output-parameter"></a>Parâmetro de saída  

`[ @rowcount = ] rowcount OUTPUT`   
 RowCount é um parâmetro de saída opcional que representa o número de registros de limpeza da tabela. *RowCount* é int.
  
## <a name="permissions"></a>Permissões    
 Requer db_owner permissões.

## <a name="next-steps"></a>Próximas etapas
- [Retenção de dados e limpeza automática de dados](data-retention-overview.md)
- [Gerenciar dados históricos com a política de retenção](data-retention-cleanup.md) 
- [Habilitar e desabilitar a retenção de dados](data-retention-enable-disable.md)
