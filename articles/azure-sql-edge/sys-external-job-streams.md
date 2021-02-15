---
title: sys.external_job_streams (Transact-SQL)-Azure SQL Edge
description: Saiba mais sobre como usar sys.external_job_streams no Azure SQL Edge
keywords: sys.external_job_streams, SQL no Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 35010d3aba7f6d5ee3185291c917ff7726ba8bd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90900353"
---
# <a name="sysexternal_job_streams-transact-sql"></a>sys.external_job_streams (Transact-SQL)

Retorna uma linha para o objeto de fluxo externo de entrada ou saída mapeado para um trabalho de fluxo externo.

|Nome da coluna|Tipo de dados|Descrição|  
|-----------------|---------------|-----------------|
|**job_id**|**int**| Número de identificação do objeto para o objeto de trabalho de fluxo. Essa coluna é mapeada para a coluna object_id de sys.external_streaming_jobs.|
|**stream_id**|**int**| Número de identificação do objeto para o objeto de fluxo. Essa coluna é mapeada para a coluna object_id de sys.external_streams. |
|**is_input**|**bit**| 1 se o objeto de fluxo é usado para uma entrada do trabalho de fluxo, caso contrário é 0.|
|**is_output**|**bit**| 1 se o objeto de fluxo é usado para uma saída do trabalho de fluxo, caso contrário é 0.|

## <a name="example"></a>Exemplo

Essa exibição do catálogo é usada junto com as exibições `sys.external_streams` e `sys.external_streaming_jobs`. Uma consulta de exemplo é mostrada abaixo

```sql
Select
    sj.Name as Job_Name,
    sj.Create_date as Job_Create_date,
    sj.modify_date as Job_Modify_date,
    sj.statement as Stream_Job_Query,
    Input_Stream_Name =
        Case js.is_input
            when 1 then s.Name
            else null
        END,
    output_Stream_Name =
        case js.is_output
            when 1 then s.Name
            else null
        END,
    s.location as Stream_Location
from sys.external_job_streams js
inner join sys.external_streams s on s.object_id = js.stream_id
inner join sys.external_streaming_jobs sj on sj.object_id = js.job_id
```

## <a name="permissions"></a>Permissões

A visibilidade dos metadados em exibições do catálogo está limitada aos protegíveis que pertencem a um usuário ou para os quais o usuário recebeu permissão. Para obter mais informações, consulte [Metadata Visibility Configuration](/sql/relational-databases/security/metadata-visibility-configuration/).

## <a name="see-also"></a>Confira também

- [Exibições do Catálogo (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [Exibições do Sistema (Transact-SQL)](/sql/t-sql/language-reference/)
