---
title: ST_DISTANCE na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL ST_DISTANCE no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 736cf488efbdc095823a1fa603f191442d907de3
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "96004459"
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

Essa função de sistema se beneficiará de um [índice geoespacial](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Próximas etapas

- [Funções espaciais Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
