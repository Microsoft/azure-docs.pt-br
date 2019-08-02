---
title: Trabalhando com dados geoespaciais na conta da API do Azure Cosmos DB SQL |
description: Entenda como criar, indexar e consultar objetos espaciais com o Azure Cosmos DB e a API do SQL.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: sngun
ms.openlocfilehash: 1654ccabab751896fcbbab09d112080ccc2f67b6
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467723"
---
# <a name="use-geospatial-and-geojson-location-data-with-azure-cosmos-db-sql-api-account"></a>Use dados de localização Geoespacial e GeoJSON com a conta da API do Azure Cosmos DB SQL

Este artigo é uma introdução à funcionalidade geoespacial no Azure Cosmos DB. Atualmente, armazenar e acessar dados geoespaciais suporte apenas para contas de API de SQL do Cosmos DB. Depois de ler este artigo, você poderá responder as seguintes perguntas:

* Como fazer para armazenar dados espaciais no Azure Cosmos DB?
* Como fazer para consultar dados geoespaciais no Azure Cosmos DB, no SQL e no LINQ?
* Como fazer para habilitar ou desabilitar a indexação espacial no Azure Cosmos DB?

Este artigo mostra como trabalhar com os dados espaciais com a API do SQL. Consulte este [projeto do GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) para obter exemplos de código.

## <a name="introduction-to-spatial-data"></a>Introdução aos dados espaciais
Os dados espaciais descrevem a posição e a forma dos objetos no espaço. Na maioria dos aplicativos, eles correspondem aos objetos na Terra e dados geoespaciais. Os dados espaciais podem ser usados para representar a localização de uma pessoa, um lugar de interesse ou a divisa de uma cidade ou de um lago. Com frequência, os casos de uso comum envolvem consultas de proximidade, por exemplo, “encontre todas as cafeterias próximas ao local atual”. 

### <a name="geojson"></a>GeoJSON
O Azure Cosmos DB dá suporte à indexação e à consulta de dados de ponto geoespaciais representados usando a [especificação GeoJSON](https://tools.ietf.org/html/rfc7946). As estruturas de dados GeoJSON são sempre objetos JSON válidos e, portanto, podem ser armazenadas e consultadas usando o Azure Cosmos DB sem nenhuma ferramenta ou biblioteca especializada. Os SDKs do Azure Cosmos DB fornecem classes auxiliares e métodos que facilitam o trabalho com os dados espaciais. 

### <a name="points-linestrings-and-polygons"></a>Pontos, LineStrings e Polígonos
Um **Ponto** denota uma única posição no espaço. Em dados geoespaciais, um Ponto representa o local exato, que poderia ser um endereço de um supermercado, de um quiosque, de um automóvel ou de uma cidade.  Um ponto é representado no GeoJSON (e no Azure Cosmos DB) usando seu par de coordenadas ou a longitude e a latitude. Veja um exemplo JSON para um ponto.

**Pontos no Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

> [!NOTE]
> A especificação de GeoJSON mostra a longitude primeiro e a latitude depois. Assim como acontece em outros aplicativos de mapeamento, a longitude e a latitude são ângulos e são representados em graus. Os valores de longitude são medidos a partir do Meridiano Principal e estão entre -180,0 graus e 180,0 graus e os valores de latitude são medidos a partir do Equador e estão entre -90,0 graus e 90,0 graus. 
> 
> O Azure Cosmos DB interpreta coordenadas como representadas de acordo com o sistema de referência WGS-84. Consulte abaixo para obter mais detalhes sobre os sistemas de coordenadas de referência.
> 
> 

Isso pode ser inserido em um documento do Azure Cosmos DB, como mostrado neste exemplo de um perfil do usuário que contém dados de localização:

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

Além dos pontos, o GeoJSON também dá suporte a LineStrings e a polígonos. **LineStrings** representam uma série de dois ou mais pontos no espaço e os segmentos de linha que os conectam. Em dados geoespaciais, as LineStrings são comumente usadas para representar vias expressas ou rios. Um **Polígono** é um limite de pontos conectados que formam uma LineString fechada. Os polígonos são comumente usados para representar formações naturais, como lagos ou jurisdições políticas, como cidades e estados. Veja a seguir um exemplo de Polígono no Azure Cosmos DB. 

**Polígonos no GeoJSON**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ],
        [ 32, -4.7 ],
        [ 32, -5 ],
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

Além de Ponto, LineString e Polígono, o GeoJSON também especifica a representação de como agrupar vários locais geoespaciais, além de como associar propriedades arbitrárias a geolocalização como um **Recurso**. Como esses objetos são um JSON válido, todos eles podem ser armazenados e processados no Azure Cosmos DB. No entanto, o Azure Cosmos DB dá suporte apenas à indexação automática de pontos.

### <a name="coordinate-reference-systems"></a>Sistemas de referência de coordenadas
Como a forma da Terra é irregular, as coordenadas de dados geoespaciais são representadas em muitos sistemas de coordenadas de referência (CRS), cada um com seus próprios quadros de referência e unidades de medida. Por exemplo, o "National Grid of Britain" é um sistema de referência preciso para o Reino Unido, mas não para fora dele. 

O CRS mais popular em uso hoje é o Sistema Geodésico Mundial [WGS-84](http://earth-info.nga.mil/GandG/wgs84/). Os dispositivos GPS e vários serviços de mapeamento, incluindo as APIs do Google Maps e do Bing Mapas usam WGS-84. O Azure Cosmos DB dá suporte à indexação e à consulta de dados geoespaciais usando apenas o CRS WGS-84. 

## <a name="creating-documents-with-spatial-data"></a>Criando documentos com dados espaciais
Quando você cria documentos que contêm valores GeoJSON, eles são indexados automaticamente com um índice espacial de acordo com a política de indexação do contêiner. Se você estiver trabalhando com um SDK do Azure Cosmos DB em uma linguagem dinamicamente tipada, como Python ou Node.js, deverá criar um GeoJSON válido.

**Criar documentos com dados geoespaciais no Node.js**

```json
var userProfileDocument = {
    "name":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

Se você estiver trabalhando com as APIs do SQL, use as classes `Point` e `Polygon` no namespace `Microsoft.Azure.Documents.Spatial` para inserir informações sobre localização nos objetos do aplicativo. Essas classes ajudam a simplificar a serialização e a desserialização de dados espaciais no GeoJSON.

**Criar documentos com dados geoespaciais no .NET**

```json
using Microsoft.Azure.Documents.Spatial;

public class UserProfile
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "profiles"), 
    new UserProfile 
    { 
        Name = "cosmosdb", 
        Location = new Point (-122.12, 47.66) 
    });
```

Se você não tiver as informações de latitude e longitude, mas tiver os endereços físicos ou o nome do local como cidade ou país/região, poderá pesquisar as coordenadas reais usando um serviço de geocodificação, como os serviços REST do Bing Maps. Saiba mais sobre a geocodificação do Bing Mapas [aqui](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>Consultando tipos espaciais
Agora que já vimos como inserir dados geoespaciais, vamos dar uma olhada em como consultar esses dados usando o Azure Cosmos DB com o SQL e o LINQ.

### <a name="spatial-sql-built-in-functions"></a>Funções internas espaciais do SQL
O Azure Cosmos DB dá suporte às funções internas do OGC (Open Geospatial Consortium) a seguir em consultas geoespaciais. Para obter mais informações sobre o conjunto completo de funções internas na linguagem SQL, consulte [Query Azure Cosmos DB](how-to-sql-query.md).

|**Uso**|**Descrição**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | Retorna a distância entre as duas expressões de ponto GeoJSON, Polígono ou LineString.|
|ST_WITHIN (spatial_expr, spatial_expr) | Retorna uma expressão booliana que indica se o primeiro objeto GeoJSON (Ponto, Polígono ou LineString) está em um segundo objeto GeoJSON (Ponto, Polígono ou LineString).|
|ST_INTERSECTS (spatial_expr, spatial_expr)| Retorna uma expressão booliana que indica se os dois objetos GeoJSON especificados (Ponto, Polígono ou LineString) se cruzam.|
|ST_ISVALID| Retorna um valor Booliano que indica se a expressão especificada de Ponto, Polígono ou LineString GeoJSON é válida.|
| ST_ISVALIDDETAILED| Retorna um valor JSON que contém um valor Booliano caso a expressão especificada de Ponto, Polígono ou LineString GeoJSON é válida e, se for inválida, adicionalmente o motivo como um valor de cadeia de caracteres.|

As funções espaciais podem ser usadas para executar consultas de proximidade em consultas espaciais. Por exemplo, veja uma consulta que retorna todos os documentos de família que estejam em um raio de 30 km do local especificado usando a função interna ST_DISTANCE. 

**Consulta**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]

Se você incluir a indexação espacial em sua política de indexação, as "consultas de distância" serão servidas com eficiência por meio do índice. Para mais informações sobre indexação espacial, veja a seção abaixo. Se você não tiver um índice espacial para os caminhos especificados, ainda poderá executar consultas espaciais especificando o cabeçalho da solicitação `x-ms-documentdb-query-enable-scan` com o valor definido como "true". No .NET, isso pode ser feito passando o argumento **FeedOptions** opcional para consultas com [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) definido como true. 

ST_WITHIN pode ser usado para verificar se um ponto está dentro de um Polígono. Normalmente, os Polígonos são usados para representar limites como códigos postais, fronteiras de estado ou formações naturais. Novamente, se você incluir a indexação espacial em sua política de indexação, as consultas "internas" serão servidas com eficiência por meio do índice. 

Os argumentos do polígono no ST_WITHIN podem conter apenas um único toque, ou seja, os Polígonos não devem conter orifícios neles. 

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Resultados**

    [{
      "id": "WakefieldFamily",
    }]

> [!NOTE]
> Da mesma forma como os tipos sem correspondência funcionam na consulta do Azure Cosmos DB, se o valor de localização especificado em um dos argumentos for malformado ou inválido, ele será avaliado como **indefinido** e o documento avaliado será ignorado nos resultados da consulta. Se sua consulta não retornar resultados, execute ST_ISVALIDDETAILED para depurar o motivo pelo qual o tipo espacial é inválido.     
> 
> 

O Azure Cosmos DB também dá suporte à execução de consultas inversas, ou seja, você pode indexar polígonos ou linhas no Azure Cosmos DB e, depois, consultar as áreas que contêm um ponto especificado. Esse padrão é normalmente usado em logística para identificar, por exemplo, quando um caminhão entra ou sai de uma determinada área. 

**Consulta**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)


**Resultados**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

ST_ISVALID e ST_ISVALIDDETAILED podem ser usados para verificar se um objeto espacial é válido. Por exemplo, a consulta a seguir verifica a validade de um ponto com um valor de latitude fora do intervalo (-132,8). ST_ISVALID retorna um valor Booliano e ST_ISVALIDDETAILED retorna o Booliano e uma cadeia de caracteres com o motivo pelo qual ele é considerado inválido.

**Consulta**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Resultados**

    [{
      "$1": false
    }]

Essas funções também podem ser usadas para validar Polígonos. Por exemplo, ST_ISVALIDDETAILED é usado aqui para validar um Polígono que não está fechado. 

**Consulta**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Resultados**

    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]

### <a name="linq-querying-in-the-net-sdk"></a>Consultas LINQ no SDK do .NET
O SDK do .NET do SQL também fornece métodos stub `Distance()` e `Within()` para uso em expressões LINQ. O provedor LINQ do SQL traduz as chamadas desse método nas chamadas de função internas do SQL equivalentes (ST_DISTANCE e ST_WITHIN, respectivamente). 

Veja um exemplo de uma consulta LINQ que localiza todos os documentos da coleção do Azure Cosmos DB cujo valor de “localização” está em um raio de 30 km do ponto especificado usando o LINQ.

**Consulta LINQ para distância**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

Da mesma forma, veja uma consulta para localizar todos os documentos cuja "localização" seja o interior da caixa/Polígono especificada. 

**Consulta LINQ para dentro**

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

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


Agora que já vimos como consultar documentos usando o LINQ e o SQL, vamos dar uma olhada em como configurar o Azure Cosmos DB para indexação espacial.

## <a name="indexing"></a>Indexação
Como descrevemos no documento [Indexação independente de esquema com o Azure Cosmos DB](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), projetamos o mecanismo de banco de dados do Azure Cosmos DB para ser verdadeiramente independente de esquema e fornecer suporte de primeira classe ao JSON. O mecanismo de banco de dados otimizado para gravação do Azure Cosmos DB também entende nativamente os dados espaciais (pontos, Polígonos e linhas) representados no padrão GeoJSON.

Em resumo, a geometria é projetada a partir de coordenadas geodésicas em um plano 2D e então dividida progressivamente em células usando um **quadtree**. Essas células são mapeadas para 1D com base na localização da célula em uma **curva de preenchimento de espaço de Hilbert**, que preserva a localidade de pontos. Além disso, quando os dados de localização são indexados, eles passam por um processo conhecido como **mosaico**, ou seja, todas as células que interseccionam uma localização são identificadas e armazenadas como chaves no índice do Azure Cosmos DB. No momento da consulta, argumentos como pontos e Polígonos também são incluídos no mosaico para extrair os intervalos de IDs de célula relevantes e usados para recuperar dados do índice.

Se você especificar uma política de indexação que inclua o índice espacial para /* (todos os caminhos), todos os pontos encontrados na coleção serão indexados para consultas espaciais eficientes (ST_WITHIN e ST_DISTANCE). Os índices espaciais não têm um valor de precisão e sempre usam um valor de precisão padrão.

> [!NOTE]
> O Azure Cosmos DB dá suporte à indexação automática de Points, Polygons e LineStrings
> 
> 

O snippet JSON a seguir mostra uma política de indexação com indexação espacial habilitada, ou seja, qualquer ponto GeoJSON encontrado em documentos para consultas espaciais do índice. Se você estiver modificando a política de indexação usando o portal do Azure, poderá especificar o JSON a seguir para a política de indexação para habilitar a indexação espacial em sua coleção.

**JSON da política de indexação da coleção com espacial habilitado para pontos e Polígonos**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Range",
                   "dataType":"String",
                   "precision":-1
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                },
                {
                   "kind":"Spatial",
                   "dataType":"Polygon"
                }                
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

Veja um snippet de código no .NET que mostra como criar uma coleção com indexação espacial ativado para todos os caminhos que contenham pontos. 

**Criar uma coleção com indexação espacial**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

E veja como você pode modificar uma coleção existente para aproveitar a indexação espacial em quaisquer pontos armazenados em documentos.

**Modificar uma coleção existente com indexação espacial**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point));
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [!NOTE]
> Se o valor GeoJSON de localização no documento estiver malformado ou inválido, então ele não será indexado para consultas espaciais. Você pode validar valores de localização usando ST_ISVALID e ST_ISVALIDDETAILED.
> 
> Caso sua definição de coleção inclua uma chave de partição, o andamento da transformação de indexação não será relatado. 
> 
> 

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu a usar o suporte geoespacial no Azure Cosmos DB, em seguida, poderá:

* Começar a codificar com os [exemplos de código geoespacial .NET no GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
* Experimente as consultas geoespaciais no [Espaço de Consulta do Azure Cosmos DB](https://www.documentdb.com/sql/demo#geospatial)
* Saiba mais sobre a [Consulta do Azure Cosmos DB](how-to-sql-query.md)
* Saiba mais sobre as [Políticas de indexação do Azure Cosmos DB](index-policy.md)

