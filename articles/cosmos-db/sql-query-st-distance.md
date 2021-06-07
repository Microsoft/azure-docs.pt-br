---
title: ST_DISTANCE na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL ST_DISTANCE no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f01f5faf68821fe9f85657c74111efdbb02bd204
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100559930"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retorna a distância entre as duas expressões de ponto geojson, polígono, MultiPolygon ou LineString. Para saber mais, consulte o artigo [dados geoespaciais e locais geojson](sql-query-geospatial-intro.md) .
  
## <a name="syntax"></a>Sintaxe
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*spatial_expr*  
   É qualquer expressão de objeto Ponto GeoJSON, Polígono ou LineString válida.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão numérica que contém a distância. Isso é expresso em metros no sistema de referência padrão.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como retornar todos os documentos da família que estão dentro de 30 km do local especificado usando a `ST_DISTANCE` função interna. .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Este é o conjunto de resultados.  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>Comentários

Essa função do sistema se beneficiará de um [índice geoespacial](index-policy.md#spatial-indexes) , exceto em consultas com agregações.

## <a name="next-steps"></a>Próximas etapas

- [Funções espaciais Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
