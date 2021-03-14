---
title: Dados geoespaciais e de localização geojson em Azure Cosmos DB
description: Entenda como criar objetos espaciais com Azure Cosmos DB e a API do SQL.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: b20c72ae3ed8a8fffa02fc3a2c86f9f73ba2663b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692123"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Dados geoespaciais e de localização geojson em Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo é uma introdução à funcionalidade geoespacial no Azure Cosmos DB. Depois de ler nossa documentação sobre indexação geoespacial, você poderá responder às seguintes perguntas:

* Como fazer para armazenar dados espaciais no Azure Cosmos DB?
* Como posso consultar dados espaciais em Azure Cosmos DB em SQL e LINQ?
* Como fazer para habilitar ou desabilitar a indexação espacial no Azure Cosmos DB?

## <a name="spatial-data-use-cases"></a>Casos de uso de dados espaciais

Os dados geoespaciais geralmente envolvem consultas de proximidade, por exemplo, "encontrar todas as cafeterias próximas ao local atual". Os casos de uso comuns são:

* Análise de geolocalização, conduzindo iniciativas específicas de marketing localizadas.
* Personalização baseada na localização, para vários setores, como varejo e saúde.
* Aprimoramento de logística para otimização de transporte.
* Análise de riscos, especialmente para empresas de seguros e finanças.
* Reconhecimento de situação, para alertas e notificações.

## <a name="introduction-to-spatial-data"></a>Introdução aos dados espaciais

Os dados espaciais descrevem a posição e a forma dos objetos no espaço. Na maioria dos aplicativos, eles correspondem aos objetos na Terra e dados geoespaciais. Os dados espaciais podem ser usados para representar a localização de uma pessoa, um lugar de interesse ou a divisa de uma cidade ou de um lago.

A API do SQL do Azure Cosmos DB dá suporte a dois tipos de dados espaciais: o tipo de dados **Geometry** e o tipo de dados **geography** .

- O tipo **Geometry** representa dados em um sistema de coordenadas euclidiana (simples)
- O tipo **geografia** representa dados em um sistema de coordenadas esféricas.

## <a name="supported-data-types"></a>Tipos de dados com suporte

O Azure Cosmos DB dá suporte à indexação e à consulta de dados de ponto geoespaciais representados usando a [especificação GeoJSON](https://tools.ietf.org/html/rfc7946). As estruturas de dados GeoJSON são sempre objetos JSON válidos e, portanto, podem ser armazenadas e consultadas usando o Azure Cosmos DB sem nenhuma ferramenta ou biblioteca especializada.

O Azure Cosmos DB dá suporte aos seguintes tipos de dados espaciais:

- Point
- LineString
- Polygon
- MultiPolygon

### <a name="points"></a>Pontos

Um **Ponto** denota uma única posição no espaço. Em dados geoespaciais, um Ponto representa o local exato, que poderia ser um endereço de um supermercado, de um quiosque, de um automóvel ou de uma cidade.  Um ponto é representado no GeoJSON (e no Azure Cosmos DB) usando seu par de coordenadas ou a longitude e a latitude.

Aqui está um exemplo de JSON para um ponto:

**Pontos no Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

Os tipos de dados espaciais podem ser inseridos em um documento Azure Cosmos DB, conforme mostrado neste exemplo de um perfil de usuário que contém dados de localização:

**Usar o perfil com a localização armazenada no Azure Cosmos DB**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

### <a name="points-in-a-geometry-coordinate-system"></a>Pontos em um sistema de coordenadas de geometria

Para o tipo de dados **Geometry** , a especificação geojson especifica o eixo horizontal primeiro e o eixo vertical segundo.

### <a name="points-in-a-geography-coordinate-system"></a>Pontos em um sistema de coordenadas geography

Para o tipo de dados **geography** , a especificação geojson especifica a longitude primeiro e a latitude segundo. Assim como acontece em outros aplicativos de mapeamento, a longitude e a latitude são ângulos e são representados em graus. Os valores de longitude são medidos a partir do Meridiano Principal e estão entre -180,0 graus e 180,0 graus e os valores de latitude são medidos a partir do Equador e estão entre -90,0 graus e 90,0 graus.

O Azure Cosmos DB interpreta coordenadas como representadas de acordo com o sistema de referência WGS-84. Consulte abaixo para obter mais detalhes sobre os sistemas de coordenadas de referência.

### <a name="linestrings"></a>LineStrings

**LineStrings** representam uma série de dois ou mais pontos no espaço e os segmentos de linha que os conectam. Em dados geoespaciais, as LineStrings são comumente usadas para representar vias expressas ou rios.

**LineStrings em geojson**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>Polígonos

Um **Polígono** é um limite de pontos conectados que formam uma LineString fechada. Os polígonos são comumente usados para representar formações naturais, como lagos ou jurisdições políticas, como cidades e estados. Aqui está um exemplo de um polígono no Azure Cosmos DB:

**Polígonos no GeoJSON**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 32, -5 ],
        [ 32, -4.7 ],
        [ 31.8, -4.7 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> A especificação GeoJSON exige que, para Polígonos válidos, o último par de coordenadas fornecido seja igual ao primeiro para criar uma forma fechada.
>
> Os pontos em um Polígono devem ser especificados no sentido anti-horário. Um Polígono especificado no sentido horário representa o inverso da região dentro dele.
>
>

### <a name="multipolygons"></a>Multipolígonos

Um **MultiPolygon** é uma matriz de zero ou mais polígonos. Os **Multigonals** não podem se sobrepor a lados ou ter qualquer área comum. Eles podem tocar em um ou mais pontos.

**Multigonals em geojson**

```json
{
    "type":"MultiPolygon",
    "coordinates":[[[
        [52.0, 12.0],
        [53.0, 12.0],
        [53.0, 13.0],
        [52.0, 13.0],
        [52.0, 12.0]
        ]],
        [[
        [50.0, 0.0],
        [51.0, 0.0],
        [51.0, 5.0],
        [50.0, 5.0],
        [50.0, 0.0]
        ]]]
}
```

## <a name="coordinate-reference-systems"></a>Sistemas de referência de coordenadas

Como a forma da terra é irregular, as coordenadas dos dados geoespaciais geográficos são representadas em muitos CRS (sistemas de referência de coordenadas), cada um com seus próprios quadros de referência e unidades de medida. Por exemplo, o "National Grid of Britain" é um sistema de referência preciso para o Reino Unido, mas não para fora dele.

O CRS mais popular em uso hoje é o Sistema Geodésico Mundial [WGS-84](https://earth-info.nga.mil/GandG/update/index.php). Os dispositivos GPS e vários serviços de mapeamento, incluindo as APIs do Google Maps e do Bing Mapas usam WGS-84. Azure Cosmos DB dá suporte à indexação e à consulta de dados geoespaciais geography usando apenas o CRS WGS-84.

## <a name="creating-documents-with-spatial-data"></a>Criando documentos com dados espaciais
Quando você cria documentos que contêm valores GeoJSON, eles são indexados automaticamente com um índice espacial de acordo com a política de indexação do contêiner. Se você estiver trabalhando com um SDK do Azure Cosmos DB em uma linguagem dinamicamente tipada, como Python ou Node.js, deverá criar um GeoJSON válido.

**Criar documentos com dados geoespaciais no Node.js**

```javascript
var userProfileDocument = {
    "id":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

Se você estiver trabalhando com as APIs do SQL, poderá usar as `Point` `LineString` classes,, e `Polygon` `MultiPolygon` dentro do `Microsoft.Azure.Cosmos.Spatial` namespace para inserir informações de localização em seus objetos de aplicativo. Essas classes ajudam a simplificar a serialização e a desserialização de dados espaciais no GeoJSON.

**Criar documentos com dados geoespaciais no .NET**

```csharp
using Microsoft.Azure.Cosmos.Spatial;

public class UserProfile
{
    [JsonProperty("id")]
    public string id { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await container.CreateItemAsync( new UserProfile
    {
        id = "cosmosdb",
        Location = new Point (-122.12, 47.66)
    });
```

Se você não tiver as informações de latitude e longitude, mas tiver os endereços físicos ou o nome do local como cidade ou país/região, poderá pesquisar as coordenadas reais usando um serviço de geocodificação, como os serviços REST do Bing Maps. Saiba mais sobre a geocodificação do Bing Mapas [aqui](/bingmaps/rest-services/).

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar o suporte geoespacial no Azure Cosmos DB, em seguida, poderá:

* Saiba mais sobre a [Consulta do Azure Cosmos DB](sql-query-getting-started.md)
* Saiba mais sobre como [consultar dados espaciais com Azure Cosmos DB](sql-query-geospatial-query.md)
* Saiba mais sobre [dados espaciais de índice com Azure Cosmos DB](sql-query-geospatial-index.md)