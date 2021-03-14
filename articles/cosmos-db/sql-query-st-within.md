---
title: ST_WITHIN na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL ST_WITHIN no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9372da349d0ea9169bb59570b7e6dd0e597d1cdf
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558233"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retorna uma expressão booliana que indica se o objeto geojson (ponto, polígono, MultiPolygon ou LineString) especificado no primeiro argumento está dentro do geojson (ponto, polígono, MultiPolygon ou LineString) no segundo argumento.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*spatial_expr*  
   É uma expressão de objeto de ponto geojson, polígono ou LineString.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna um valor booliano.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como localizar todos os documentos da família em um polígono usando `ST_WITHIN` .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{ "id": "WakefieldFamily" }]  
```  

## <a name="remarks"></a>Comentários

Essa função do sistema se beneficiará de um [índice geoespacial](index-policy.md#spatial-indexes) , exceto em consultas com agregações.

## <a name="next-steps"></a>Próximas etapas

- [Funções espaciais Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
