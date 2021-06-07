---
title: Consultando dados geoespaciais com Azure Cosmos DB
description: Consultando dados espaciais com Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: bb9a0351b6f1de47f3687995c65060a23bdb2874
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93336105"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>Consultando dados geoespaciais com Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo explicará como consultar dados geoespaciais em Azure Cosmos DB usando o SQL e o LINQ. Atualmente, o armazenamento e o acesso a dados geoespaciais têm suporte apenas Azure Cosmos DB contas da API do SQL. O Azure Cosmos DB dá suporte às funções internas do OGC (Open Geospatial Consortium) a seguir em consultas geoespaciais. Para obter mais informações sobre o conjunto completo de funções internas na linguagem SQL, consulte [funções do sistema de consulta no Azure Cosmos DB](sql-query-system-functions.md).

## <a name="spatial-sql-built-in-functions"></a>Funções internas espaciais do SQL

Aqui está uma lista de funções geoespaciais do sistema úteis para consulta no Azure Cosmos DB:

|**Usage**|**Descrição**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | Retorna a distância entre as duas expressões de ponto GeoJSON, Polígono ou LineString.|
|ST_WITHIN (spatial_expr, spatial_expr) | Retorna uma expressão booliana que indica se o primeiro objeto GeoJSON (Ponto, Polígono ou LineString) está em um segundo objeto GeoJSON (Ponto, Polígono ou LineString).|
|ST_INTERSECTS (spatial_expr, spatial_expr)| Retorna uma expressão booliana que indica se os dois objetos GeoJSON especificados (Ponto, Polígono ou LineString) se cruzam.|
|ST_ISVALID| Retorna um valor Booliano que indica se a expressão especificada de Ponto, Polígono ou LineString GeoJSON é válida.|
| ST_ISVALIDDETAILED| Retorna um valor JSON que contém um valor booliano se a expressão de ponto geojson, polígono ou LineString especificada é válida. Se for inválido, ele retornará o motivo como um valor de cadeia de caracteres.|

As funções espaciais podem ser usadas para executar consultas de proximidade em consultas espaciais. Por exemplo, aqui está uma consulta que retorna todos os documentos de família que estão dentro de 30 km do local especificado usando a `ST_DISTANCE` função interna.

**Consulta**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {"type": "Point", "coordinates":[31.9, -4.8]}) < 30000
```

**Resultados**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Se você incluir a indexação espacial em sua política de indexação, as "consultas de distância" serão servidas com eficiência por meio do índice. Para obter mais informações sobre a indexação espacial, consulte [indexação geoespacial](sql-query-geospatial-index.md). Se você não tiver um índice espacial para os caminhos especificados, a consulta fará uma verificação do contêiner.

`ST_WITHIN` pode ser usado para verificar se um ponto está dentro de um polígono. Normalmente, os Polígonos são usados para representar limites como códigos postais, fronteiras de estado ou formações naturais. Novamente, se você incluir a indexação espacial em sua política de indexação, as consultas "internas" serão servidas com eficiência por meio do índice.

Os argumentos Polygon no `ST_WITHIN` podem conter apenas um único anel, ou seja, os polígonos não devem conter buracos neles.

**Consulta**

```sql
    SELECT *
    FROM Families f
    WHERE ST_WITHIN(f.location, {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })
```

**Resultados**

```json
    [{
      "id": "WakefieldFamily",
    }]
```

> [!NOTE]
> Da mesma forma como os tipos sem correspondência funcionam na consulta do Azure Cosmos DB, se o valor de localização especificado em um dos argumentos for malformado ou inválido, ele será avaliado como **indefinido** e o documento avaliado será ignorado nos resultados da consulta. Se sua consulta não retornar nenhum resultado, execute `ST_ISVALIDDETAILED` para depurar por que o tipo espacial é inválido.
>
>

O Azure Cosmos DB também dá suporte à execução de consultas inversas, ou seja, você pode indexar polígonos ou linhas no Azure Cosmos DB e, depois, consultar as áreas que contêm um ponto especificado. Esse padrão é normalmente usado em logística para identificar, por exemplo, quando um caminhão entra ou sai de uma determinada área.

**Consulta**

```sql
    SELECT *
    FROM Areas a
    WHERE ST_WITHIN({"type": "Point", "coordinates":[31.9, -4.8]}, a.location)
```

**Resultados**

```json
    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]
```

`ST_ISVALID` e `ST_ISVALIDDETAILED` pode ser usado para verificar se um objeto espacial é válido. Por exemplo, a consulta a seguir verifica a validade de um ponto com um valor de latitude fora do intervalo (-132,8). `ST_ISVALID` retorna apenas um valor booliano e `ST_ISVALIDDETAILED` retorna o booliano e uma cadeia de caracteres que contém o motivo pelo qual ele é considerado inválido.

**Consulta**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**Resultados**

```json
    [{
      "$1": false
    }]
```

Essas funções também podem ser usadas para validar Polígonos. Por exemplo, aqui, usamos `ST_ISVALIDDETAILED` para validar um polígono que não está fechado.

**Consulta**

```sql
    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})
```

**Resultados**

```json
    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]
```

## <a name="linq-querying-in-the-net-sdk"></a>Consultas LINQ no SDK do .NET

O SDK do .NET do SQL também fornece métodos stub `Distance()` e `Within()` para uso em expressões LINQ. O provedor LINQ do SQL traduz as chamadas desse método nas chamadas de função internas do SQL equivalentes (ST_DISTANCE e ST_WITHIN, respectivamente).

Aqui está um exemplo de uma consulta LINQ que localiza todos os documentos no contêiner Cosmos do Azure cujo `location` valor está dentro de um raio de 30 km do ponto especificado usando LINQ.

**Consulta LINQ para distância**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

Da mesma forma, aqui está uma consulta para localizar todos os documentos cujo `location` está dentro da caixa/polígono especificada.

**Consulta LINQ para dentro**

```csharp
    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
         .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar o suporte geoespacial no Azure Cosmos DB, em seguida, poderá:

* Saiba mais sobre a [Consulta do Azure Cosmos DB](sql-query-getting-started.md)
* Saiba mais sobre [os dados geoespaciais e de localização geojson no Azure Cosmos DB](sql-query-geospatial-intro.md)
* Saiba mais sobre [dados espaciais de índice com Azure Cosmos DB](sql-query-geospatial-index.md)
