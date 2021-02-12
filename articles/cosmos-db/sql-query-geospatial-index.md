---
title: Indexar dados geoespaciais com Azure Cosmos DB
description: Indexar dados espaciais com Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: tisande
ms.openlocfilehash: 47eedf1ddbb155180d364c42ec179b3e01279e44
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336207"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Indexar dados geoespaciais com Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Projetamos Azure Cosmos DB mecanismo de banco de dados para ser verdadeiramente independente de esquema e fornecer suporte de primeira classe para JSON. O mecanismo de banco de dados otimizado para gravação de Azure Cosmos DB entende nativamente os dados espaciais representados no padrão geojson.

Em resumo, a geometria é projetada a partir de coordenadas geodésicas em um plano 2D e então dividida progressivamente em células usando um **quadtree**. Essas células são mapeadas para 1D com base na localização da célula em uma **curva de preenchimento de espaço de Hilbert** , que preserva a localidade de pontos. Além disso, quando os dados de localização são indexados, eles passam por um processo conhecido como **mosaico** , ou seja, todas as células que interseccionam uma localização são identificadas e armazenadas como chaves no índice do Azure Cosmos DB. No momento da consulta, argumentos como pontos e Polígonos também são incluídos no mosaico para extrair os intervalos de IDs de célula relevantes e usados para recuperar dados do índice.

Se você especificar uma política de indexação que inclua um índice espacial para `/*` (todos os caminhos), todos os dados encontrados no contêiner serão indexados para consultas espaciais eficientes.

> [!NOTE]
> Azure Cosmos DB dá suporte à indexação de pontos, LineStrings, polígonos e multigonals. Se você indexar qualquer um desses tipos, iremos indexar automaticamente todos os outros tipos. Em outras palavras, se você indexar polígonos, também indexaremos pontos, LineStrings e multigonals. A indexação de um novo tipo espacial não afeta o encargo de Write RU ou o tamanho do índice, a menos que você tenha dados geojson válidos desse tipo.

## <a name="modifying-geospatial-configuration"></a>Modificando configuração geoespacial

Em seu contêiner, a **configuração geoespacial** especifica como os dados espaciais serão indexados. Especifique uma **configuração geoespacial** por contêiner: geography ou Geometry.

Você pode alternar entre o tipo espacial **geography** e **Geometry** no portal do Azure. É importante que você crie uma [política de indexação de geometria espacial válida com uma caixa delimitadora](#geometry-data-indexing-examples) antes de alternar para o tipo espacial de geometry.

Veja como definir a **configuração geoespacial** no **Data Explorer** no portal do Azure:

:::image type="content" source="./media/sql-query-geospatial-index/geospatial-configuration.png" alt-text="Definindo configuração geoespacial":::

Você também pode modificar o `geospatialConfig` no SDK do .net para ajustar a **configuração geoespacial** :

Se não for especificado, o `geospatialConfig` padrão será o tipo de dados geography. Quando você modifica o `geospatialConfig` , todos os dados geoespaciais existentes no contêiner serão reindexados.

Aqui está um exemplo para modificar o tipo de dados geoespaciais para `geometry` definindo a `geospatialConfig` propriedade e adicionando um **boundingBox** :

```csharp
    //Retrieve the container's details
    ContainerResponse containerResponse = await client.GetContainer("db", "spatial").ReadContainerAsync();
    //Set GeospatialConfig to Geometry
    GeospatialConfig geospatialConfig = new GeospatialConfig(GeospatialType.Geometry);
    containerResponse.Resource.GeospatialConfig = geospatialConfig;
    // Add a spatial index including the required boundingBox
    SpatialPath spatialPath = new SpatialPath
            {  
                Path = "/locations/*",
                BoundingBox = new BoundingBoxProperties(){
                    Xmin = 0,
                    Ymin = 0,
                    Xmax = 10,
                    Ymax = 10
                }
            };
    spatialPath.SpatialTypes.Add(SpatialType.Point);
    spatialPath.SpatialTypes.Add(SpatialType.LineString);
    spatialPath.SpatialTypes.Add(SpatialType.Polygon);
    spatialPath.SpatialTypes.Add(SpatialType.MultiPolygon);

    containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);

    // Update container with changes
    await client.GetContainer("db", "spatial").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="geography-data-indexing-examples"></a>Exemplos de indexação de dados de Geografia

O trecho JSON a seguir mostra uma política de indexação com indexação espacial habilitada para o tipo de dados **geography** . Ele é válido para dados espaciais com o tipo de dados geography e indexará qualquer ponto geojson, polígono, MultiPolygon ou LineString encontrado em documentos para consultas espaciais. Se você estiver modificando a política de indexação usando o portal do Azure, poderá especificar o JSON a seguir para a política de indexação para habilitar a indexação espacial em seu contêiner:

**Política de indexação de contêiner JSON com indexação espacial de Geografia**

```json
{
    "automatic": true,
    "indexingMode": "Consistent",
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
    "excludedPaths": []
}
```

> [!NOTE]
> Se o valor GeoJSON de localização no documento estiver malformado ou inválido, então ele não será indexado para consultas espaciais. Você pode validar valores de localização usando ST_ISVALID e ST_ISVALIDDETAILED.

Você também pode [Modificar a política de indexação](how-to-manage-indexing-policy.md) usando o CLI do Azure, o PowerShell ou qualquer SDK.

## <a name="geometry-data-indexing-examples"></a>Exemplos de indexação de dados geometry

Com o tipo de dados **Geometry** , semelhante ao tipo de dados geography, você deve especificar caminhos e tipos relevantes para indexar. Além disso, você também deve especificar um `boundingBox` dentro da política de indexação para indicar a área desejada a ser indexada para esse caminho específico. Cada caminho geoespacial exige seu próprio `boundingBox` .

A caixa delimitadora consiste nas seguintes propriedades:

- **xmin** : a coordenada x indexada mínima
- **ymin** : a coordenada y indexada mínima
- **xmax** : a coordenada de x indexada máxima
- **ymax** : a coordenada y indexada máxima

Uma caixa delimitadora é necessária porque os dados geométricos ocupam um plano que pode ser infinito. No entanto, os índices espaciais exigem um espaço finito. Para o tipo de dados **geography** , a terra é o limite e você não precisa definir uma caixa delimitadora.

Crie uma caixa delimitadora que contenha todos (ou mais) dos seus dados. Somente as operações computadas nos objetos que estão inteiramente dentro da caixa delimitadora poderão utilizar o índice espacial. Tornar a caixa delimitadora maior do que o necessário afetará negativamente o desempenho da consulta.

Aqui está um exemplo de política de indexação que indexa dados de **geometria** com **geospatialConfig** definido como `geometry` :

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/locations/*",
            "types": [
                "Point",
                "LineString",
                "Polygon",
                "MultiPolygon"
            ],
            "boundingBox": {
                "xmin": -10,
                "ymin": -20,
                "xmax": 10,
                "ymax": 20
            }
        }
    ]
}
```

A política de indexação acima tem um **boundingBox** de (-10, 10) para coordenadas x e (-20, 20) para coordenadas y. O contêiner com a política de indexação acima indexará todos os pontos, polígonos, subpolígonos e LineStrings que estão inteiramente dentro dessa região.

> [!NOTE]
> Se você tentar adicionar uma política de indexação com um **boundingBox** a um contêiner com `geography` tipo de dados, haverá falha. Você deve modificar o **geospatialConfig** do contêiner para que ele seja `geometry` antes de adicionar um **boundingBox**. Você pode adicionar dados e modificar o restante da política de indexação (como os caminhos e tipos) antes ou depois de selecionar o tipo de dados geoespaciais para o contêiner.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar o suporte geoespacial no Azure Cosmos DB, em seguida, poderá:

* Saiba mais sobre a [Consulta do Azure Cosmos DB](sql-query-getting-started.md)
* Saiba mais sobre como [consultar dados espaciais com Azure Cosmos DB](sql-query-geospatial-query.md)
* Saiba mais sobre [os dados geoespaciais e de localização geojson no Azure Cosmos DB](sql-query-geospatial-intro.md)
