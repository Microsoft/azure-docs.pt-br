---
title: ST_ISVALID na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre a função do sistema SQL ST_ISVALID no Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1e7c124da91a947a0ac8426ce8c92347396236c4
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "96004425"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retorna um valor booliano que indica se a expressão de ponto geojson especificada, Polygon, MultiPolygon ou LineString é válida.  
  
## <a name="syntax"></a>Sintaxe
  
```sql
ST_ISVALID(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*spatial_expr*  
   É um ponto geojson, polígono ou expressão de LineString.  
  
## <a name="return-types"></a>Tipos de retorno
  
  Retorna uma expressão booliana.  
  
## <a name="examples"></a>Exemplos
  
  O exemplo a seguir mostra como verificar se um ponto é válido usando ST_VALID.  
  
  Por exemplo, esse ponto tem um valor de latitude que não está no intervalo de valores válido [-90, 90] e, portanto, a consulta retornará false.  
  
  No caso dos polígonos, a especificação GeoJSON exige que o último par de coordenadas fornecido seja igual ao primeiro para criar uma forma fechada. Os pontos em um polígono devem ser especificados no sentido anti-horário. Um polígono especificado no sentido horário representa o inverso da região dentro dele.  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Este é o conjunto de resultados.  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>Próximas etapas

- [Funções espaciais Azure Cosmos DB](sql-query-spatial-functions.md)
- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
