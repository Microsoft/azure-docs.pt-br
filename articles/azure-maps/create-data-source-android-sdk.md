---
title: Criar uma fonte de dados para mapas do Android | Mapas do Microsoft Azure
description: 'Descubra como criar uma fonte de dados para um mapa. Saiba mais sobre as fontes de dados que o Azure Maps SDK do Android usa: fontes geojson e blocos de vetor.'
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: e870134e2ecd431aa3e5c02638120027f0d47df2
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101453"
---
# <a name="create-a-data-source-android-sdk"></a>Criar uma fonte de dados (SDK do Android)

O Azure Maps SDK do Android armazena dados em fontes de dados. O uso de fontes de dados otimiza as operações de dados para consulta e renderização. Atualmente, há dois tipos de fontes de dados:

- **Origem geojson**: gerencia dados de local brutos no formato geojson localmente. Bom para conjuntos de dados pequenos a médios (em cima de centenas de milhares de formas).
- **Origem do bloco de vetor**: carrega dados formatados como blocos de vetor para a exibição de mapa atual, com base no sistema de mapas de blocos. Ideal para conjuntos de dados grandes a grandes (milhões ou bilhões de formas).

## <a name="geojson-data-source"></a>Fonte de dados geojson

O Azure Maps usa geojson como um de seus modelos de dados primários. Geojson é uma forma padrão geoespacial aberta para representar dados geoespaciais no formato JSON. As classes geojson disponíveis no Azure Maps SDK do Android para facilitar a criação e serializar dados geojson. Carregar e armazenar dados geojson na `DataSource` classe e renderizá-los usando camadas. O código a seguir mostra como os objetos geojson podem ser criados no Azure Maps.

::: zone pivot="programming-language-java-android"

```java
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add a property to the feature.
feature.addStringProperty("custom-property", "value");

//Add the feature to the data source.
source.add(feature);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45))

//Add a property to the feature.
feature.addStringProperty("custom-property", "value")

//Add the feature to the data source.
source.add(feature)
```

::: zone-end

Como alternativa, as propriedades podem ser carregadas em um Jsonobject primeiro, em seguida, passadas para o recurso ao criá-lo, conforme mostrado abaixo.

::: zone pivot="programming-language-java-android"

```java
//Create a JsonObject to store properties for the feature.
JsonObject properties = new JsonObject();
properties.addProperty("custom-property", "value");

Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a JsonObject to store properties for the feature.
val properties = JsonObject()
properties.addProperty("custom-property", "value")

val feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties)
```

::: zone-end

Depois que você tiver um recurso geojson criado, uma fonte de dados poderá ser adicionada ao mapa por meio da `sources` Propriedade do mapa. O código a seguir mostra como criar um `DataSource` , adicioná-lo ao mapa e adicionar um recurso à fonte de dados.

```javascript
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add GeoJSON feature to the data source.
source.add(feature);
```

O código a seguir mostra várias maneiras de criar um recurso geojson, refeaturecollection e geometrias.

::: zone pivot="programming-language-java-android"

```java
//GeoJSON Point Geometry
Point point = Point.fromLngLat(LONGITUDE, LATITUDE);

//GeoJSON Point Geometry
LineString linestring = LineString.fromLngLats(PointList);

//GeoJSON Polygon Geometry
Polygon polygon = Polygon.fromLngLats(listOfPointList);
 
Polygon polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject,innerLineStringObject);

//GeoJSON MultiPoint Geometry
MultiPoint multiPoint = MultiPoint.fromLngLats(PointList);

//GeoJSON MultiLineString Geometry
MultiLineString multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList);

MultiLineString multiLineString = MultiLineString.fromLineString(singleLineString);

//GeoJSON MultiPolygon Geometry
MultiPolygon multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList);

MultiPolygon multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon);

MultiPolygon multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList);

//GeoJSON Feature
Feature pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE));
 
//GeoJSON FeatureCollection 
FeatureCollection featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature);
 
FeatureCollection featureCollection = FeatureCollection.fromFeatures(listOfFeatures);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//GeoJSON Point Geometry
val point = Point.fromLngLat(LONGITUDE, LATITUDE)

//GeoJSON Point Geometry
val linestring = LineString.fromLngLats(PointList)

//GeoJSON Polygon Geometry
val polygon = Polygon.fromLngLats(listOfPointList)

val polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject, innerLineStringObject)

//GeoJSON MultiPoint Geometry
val multiPoint = MultiPoint.fromLngLats(PointList)

//GeoJSON MultiLineString Geometry
val multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList)

val multiLineString = MultiLineString.fromLineString(singleLineString)

//GeoJSON MultiPolygon Geometry
val multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList)

val multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon)

val multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList)

//GeoJSON Feature
val pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE))

//GeoJSON FeatureCollection 
val featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature)

val featureCollection = FeatureCollection.fromFeatures(listOfFeatures)
```

::: zone-end

### <a name="serialize-and-deserialize-geojson"></a>Serializar e desserializar geojson

Todas as classes de coleção de recursos, recurso e geometria têm os `fromJson()` `toJson()` métodos estáticos, que ajudam com a serialização. A cadeia de caracteres JSON formatada válida, passada pelo `fromJson()` método, criará o objeto Geometry. Esse `fromJson()` método também significa que você pode usar Gson ou outras estratégias de serialização/desserialização. O código a seguir mostra como pegar um recurso geojson em cadeias e desserializá-lo na classe Feature e, em seguida, serializá-lo novamente em uma cadeia de caracteres geojson.

::: zone pivot="programming-language-java-android"

```java
//Take a stringified GeoJSON object.
String GeoJSON_STRING = "{"
    + "      \"type\": \"Feature\","            
    + "      \"geometry\": {"
    + "            \"type\": \"Point\""
    + "            \"coordinates\": [-100, 45]"
    + "      },"
    + "      \"properties\": {"
    + "            \"custom-property\": \"value\""
    + "      },"
    + "}";

//Deserialize the JSON string into a feature.
Feature feature = Feature.fromJson(GeoJSON_STRING);
 
//Serialize a feature collection to a string.
String featureString = feature.toJson();
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Take a stringified GeoJSON object.
val GeoJSON_STRING = ("{"
        + "      \"type\": \"Feature\","
        + "      \"geometry\": {"
        + "            \"type\": \"Point\""
        + "            \"coordinates\": [-100, 45]"
        + "      },"
        + "      \"properties\": {"
        + "            \"custom-property\": \"value\""
        + "      },"
        + "}")

//Deserialize the JSON string into a feature.
val feature = Feature.fromJson(GeoJSON_STRING)

//Serialize a feature collection to a string.
val featureString = feature.toJson()
```

::: zone-end

### <a name="import-geojson-data-from-web-or-assets-folder"></a>Importar dados GeoJSON da pasta de ativos ou da Web

A maioria dos arquivos geojson contém uma Featurecollection. Ler arquivos geojson como cadeias de caracteres e usar o `FeatureCollection.fromJson` método para desserializá-lo.

O código a seguir é uma classe reutilizável para importar dados da pasta de ativos Web ou locais como uma cadeia de caracteres e retorná-los ao thread da interface do usuário por meio de uma função de retorno de chamada.

::: zone pivot="programming-language-java-android"

```java
import android.content.Context;
import android.os.Handler;
import android.os.Looper;
import android.webkit.URLUtil;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.URL;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

import javax.net.ssl.HttpsURLConnection;

public class Utils {

    interface SimpleCallback {
        void notify(String result);
    }

    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback){
        importData(urlOrFileName, context, callback, null);
    }
    
    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     * @param error A callback function to return errors to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback, SimpleCallback error){
        if(urlOrFileName != null && callback != null) {
            ExecutorService executor = Executors.newSingleThreadExecutor();
            Handler handler = new Handler(Looper.getMainLooper());

            executor.execute(() -> {
                String data = null;

                try {

                    if(URLUtil.isNetworkUrl(urlOrFileName)){
                        data = importFromWeb(urlOrFileName);
                    } else {
                        //Assume file is in assets folder.
                        data = importFromAssets(context, urlOrFileName);
                    }

                    final String result = data;

                    handler.post(() -> {
                        //Ensure the resulting data string is not null or empty.
                        if (result != null && !result.isEmpty()) {
                            callback.notify(result);
                        } else {
                            error.notify("No data imported.");
                        }
                    });
                } catch(Exception e) {
                    if(error != null){
                        error.notify(e.getMessage());
                    }
                }
            });
        }
    }

    /**
     * Imports data from an assets file as a string.
     * @param context The context of the app.
     * @param fileName The asset file name.
     * @return
     * @throws IOException
     */
    private static String importFromAssets(Context context, String fileName) throws IOException {
        InputStream stream = null;

        try {
            stream = context.getAssets().open(fileName);

            if(stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
        }

        return null;
    }

    /**
     * Imports data from the web as a string.
     * @param url URL to the data.
     * @return
     * @throws IOException
     */
    private static String importFromWeb(String url) throws IOException {
        InputStream stream = null;
        HttpsURLConnection connection = null;
        String result = null;

        try {
            connection = (HttpsURLConnection) new URL(url).openConnection();

            //For this use case, set HTTP method to GET.
            connection.setRequestMethod("GET");

            //Open communications link (network traffic occurs here).
            connection.connect();

            int responseCode = connection.getResponseCode();
            if (responseCode != HttpsURLConnection.HTTP_OK) {
                throw new IOException("HTTP error code: " + responseCode);
            }

            //Retrieve the response body as an InputStream.
            stream = connection.getInputStream();

            if (stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
            if (connection != null) {
                connection.disconnect();
            }
        }

        return result;
    }

    /**
     * Reads an input stream as a string.
     * @param stream Stream to convert.
     * @return
     * @throws IOException
     */
    private static String readStreamAsString(InputStream stream) throws IOException {
        //Convert the contents of an InputStream to a String.
        BufferedReader in = new BufferedReader(new InputStreamReader(stream, "UTF-8"));

        String inputLine;
        StringBuffer response = new StringBuffer();

        while ((inputLine = in.readLine()) != null) {
            response.append(inputLine);
        }

        in.close();

        return response.toString();
    }
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
import android.content.Context
import android.os.Handler
import android.os.Looper
import android.webkit.URLUtil
import java.net.URL
import java.util.concurrent.ExecutorService
import java.util.concurrent.Executors

class Utils {
    companion object {

        /**
            * Imports data from a web url or asset file name and returns it to a callback.
            * @param urlOrFileName A web url or asset file name that points to data to load.
            * @param context The context of the app.
            * @param callback The callback function to return the data to.
            */
        fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit) {
            importData(urlOrFileName, context, callback, null)
        }

        /**
            * Imports data from a web url or asset file name and returns it to a callback.
            * @param urlOrFileName A web url or asset file name that points to data to load.
            * @param context The context of the app.
            * @param callback The callback function to return the data to.
            * @param error A callback function to return errors to.
            */
        public fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit, error: ((String?) -> Unit)?) {
            if (urlOrFileName != null && callback != null) {
                val executor: ExecutorService = Executors.newSingleThreadExecutor()
                val handler = Handler(Looper.getMainLooper())
                executor.execute {
                    var data: String? = null
                    
                    try {
                        data = if (URLUtil.isNetworkUrl(urlOrFileName)) {
                            URL(urlOrFileName).readText()
                        } else { //Assume file is in assets folder.
                            context.assets.open(urlOrFileName).bufferedReader().use{
                                it.readText()
                            }
                        }

                        handler.post {
                            //Ensure the resulting data string is not null or empty.
                            if (data != null && !data.isEmpty()) {
                                callback(data)
                            } else {
                                error!!("No data imported.")
                            }
                        }
                    } catch (e: Exception) {
                        error!!(e.message)
                    }
                }
            }
        }
    }
}
```

::: zone-end

O código a seguir mostra como usar esse utilitário para importar dados geojson como uma cadeia de caracteres e retorná-los ao thread da interface do usuário por meio de um retorno de chamada. No retorno de chamada, os dados de cadeia de caracteres podem ser serializados em uma coleção de recursos geojson e adicionados à fonte de dados. Opcionalmente, atualize a câmera mapas para se concentrar nos dados.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Import the GeoJSON data and add it to the data source.
Utils.importData("SamplePoiDataSet.json", this) { 
    result: String? ->
        //Parse the data as a GeoJSON Feature Collection.
            val fc = FeatureCollection.fromJson(result!!)

        //Add the feature collection to the data source.
        source.add(fc)

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        val bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),

            //Padding added to account for pixel size of rendered points.
            padding(20)
        )
    }
```

::: zone-end

## <a name="vector-tile-source"></a>Origem do bloco de vetor

Uma fonte de bloco de vetor descreve como acessar uma camada de bloco de vetor. Use a `VectorTileSource` classe para criar uma instância de uma fonte de bloco de vetor. As camadas de bloco de vetor são semelhantes às camadas de bloco, mas não são as mesmas. Uma camada de peça é uma imagem rasterizada. As camadas de bloco de vetor são um arquivo compactado, no formato **PBF** . Esse arquivo compactado contém dados de mapa de vetor e uma ou mais camadas. O arquivo pode ser renderizado e estilizado no cliente, com base no estilo de cada camada. Os dados em um bloco de vetor contêm recursos geográficos na forma de pontos, linhas e polígonos. Há várias vantagens de usar camadas de bloco vetorial em vez de camadas de bloco rasterizadas:

- Um tamanho de arquivo de um bloco de vetor normalmente é muito menor do que um bloco rasterizado equivalente. Dessa forma, menos largura de banda é usada. Isso significa menor latência, um mapa mais rápido e uma melhor experiência do usuário.
- Como os blocos de vetor são renderizados no cliente, eles se adaptam à resolução do dispositivo em que estão sendo exibidos. Como resultado, os mapas renderizados aparecem mais bem definidos, com rótulos de cristal claro.
- Alterar o estilo dos dados nos mapas de vetor não exige o download dos dados novamente, já que o novo estilo pode ser aplicado ao cliente. Por outro lado, alterar o estilo de uma camada de bloco de varredura normalmente requer o carregamento de blocos do servidor, aplicando o novo estilo.
- Como os dados são entregues na forma de vetor, há menos processamento no lado do servidor necessário para preparar os dados. Como resultado, os dados mais recentes podem ser disponibilizados mais rapidamente.

O mapas do Azure segue a [especificação de bloco de vetor Mapbox](https://github.com/mapbox/vector-tile-spec), um padrão aberto. O mapas do Azure fornece os seguintes serviços de blocos de vetor como parte da plataforma:

- Detalhes do [](/rest/api/maps/renderv2/getmaptilepreview)  |  [formato de dados](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile) da documentação dos blocos de estrada
- Detalhes do [](/rest/api/maps/traffic/gettrafficincidenttile)  |  [formato de dados](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles) de documentação de incidentes de tráfego
- Detalhes do [](/rest/api/maps/traffic/gettrafficflowtile)  |  [formato de dados](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles) de documentação do fluxo de tráfego
- O criador do mapas do Azure também permite que blocos de vetor personalizados sejam criados e acessados por meio da [renderização de bloco Get v2](/rest/api/maps/renderv2/getmaptilepreview)

> [!TIP]
> Ao usar blocos de imagem vetorial ou rasterizada do serviço de renderização do Azure Maps com o SDK da Web, você pode substituir `atlas.microsoft.com` pelo espaço reservado `azmapsdomain.invalid` . Esse espaço reservado será substituído pelo mesmo domínio usado pelo mapa e também acrescentará automaticamente os mesmos detalhes de autenticação. Isso simplifica muito a autenticação com o serviço de renderização ao usar a autenticação Azure Active Directory.

Para exibir dados de uma fonte de bloco de vetor no mapa, conecte a origem a uma das camadas de renderização de dados. Todas as camadas que usam uma fonte de vetor devem especificar um `sourceLayer` valor nas opções. O código a seguir carrega o serviço de bloco de vetor do fluxo de tráfego do Azure Maps como uma fonte de bloco de vetor e, em seguida, o exibe em um mapa usando uma camada de linha. Essa fonte de bloco vetorial tem um único conjunto de dados na camada de origem chamada "fluxo de tráfego". Os dados de linha nesse conjunto de dados têm uma propriedade chamada `traffic_level` que é usada neste código para selecionar a cor e dimensionar o tamanho das linhas.

::: zone pivot="programming-language-java-android"

```java
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
String trafficFlowUrl = "https://azmapsdomain.invalid/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}";

//Create a vector tile source and add it to the map.
VectorTileSource source = new VectorTileSource(
    tiles(new String[] { trafficFlowUrl }),
    maxSourceZoom(22)
);
map.sources.add(source);

//Create a layer for traffic flow lines.
LineLayer layer = new LineLayer(source,
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),

    //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),

    //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1,1)
        )
    )
);

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
val trafficFlowUrl = "https://azmapsdomain.invalid/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}"

//Create a vector tile source and add it to the map.
val source = VectorTileSource(
    tiles(arrayOf(trafficFlowUrl)),
    maxSourceZoom(22)
)
map.sources.add(source)

//Create a layer for traffic flow lines.
val layer = LineLayer(
    source,  //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),  //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),  //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1, 1)
        )
    )
)

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels")
```

::: zone-end

![Mapa com linhas de estrada codificadas por cor mostrando os níveis de fluxo de tráfego](media/create-data-source-android-sdk/android-vector-tile-source-line-layer.png)

## <a name="connecting-a-data-source-to-a-layer"></a>Conectando uma fonte de dados a uma camada

Os dados são renderizados no mapa usando camadas de renderização. Uma única fonte de dados pode ser referenciada por uma ou mais camadas de renderização. As seguintes camadas de renderização exigem uma fonte de dados:

- [Camada de bolha](map-add-bubble-layer-android.md) – renderiza dados de ponto como círculos em escala no mapa.
- [Camada de símbolo](how-to-add-symbol-to-android-map.md) – renderiza dados de ponto como ícones ou texto.
- [Camada do mapa de calor](map-add-heat-map-layer-android.md) – renderiza dados de ponto como um mapa de calor de densidade.
- [Camada de linha](android-map-add-line-layer.md) – renderizar uma linha e ou renderizar a estrutura de polígonos.
- [Camada de polígono](how-to-add-shapes-to-android-map.md) – preenche a área de um polígono com uma cor sólida ou padrão de imagem.

O código a seguir mostra como criar uma fonte de dados, adicioná-la ao mapa e conectá-la a uma camada de bolha. Em seguida, importe dados do ponto geojson de um local remoto para a fonte de dados.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a layer that defines how to render points in the data source and add it to the map.
val layer = BubbleLayer(source)
map.layers.add(layer)

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data", this) { 
    result: String? ->
        //Parse the data as a GeoJSON Feature Collection.
        val fc = FeatureCollection.fromJson(result!!)
    
        //Add the feature collection to the data source.
        dataSource.add(fc)
    
        //Optionally, update the maps camera to focus in on the data.
        //Calculate the bounding box of all the data in the Feature Collection.
        val bbox = MapMath.fromData(fc)
    
        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20)
        )
    }
```

::: zone-end

Há camadas de renderização adicionais que não se conectam a essas fontes de dados, mas carregam diretamente os dados para renderização.

- [Camada de bloco](how-to-add-tile-layer-android-map.md) – sobrepõe uma camada de bloco de rasterização na parte superior do mapa.

## <a name="one-data-source-with-multiple-layers"></a>Uma fonte de dados com várias camadas

Várias camadas podem ser conectadas a uma única fonte de dados. Há vários cenários diferentes nos quais essa opção é útil. Por exemplo, considere o cenário no qual um usuário desenha um polígono. Devemos renderizar e preencher a área do polígono, pois o usuário adiciona pontos ao mapa. A adição de uma linha com estilo para descrever o polígono facilita a visualização das bordas do polígono, conforme o usuário desenha. Para editar convenientemente uma posição individual no polígono, podemos adicionar um identificador, como um PIN ou um marcador, acima de cada posição.

![Mapa mostrando várias camadas Renderizando dados de uma única fonte de dados](media/create-data-source-android-sdk/multiple-layers-one-datasource.png)

Na maioria das plataformas de mapeamento, você precisaria de um objeto Polygon, de um objeto line e de um PIN para cada posição no polígono. À medida que o polígono é modificado, você precisa atualizar manualmente a linha e os Pins, o que pode se tornar rapidamente complexo.

Com o Azure Maps, tudo o que você precisa é de um único polígono em uma fonte de dados, conforme mostrado no código abaixo.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats(/* List of points */));

//Create a polygon layer to render the filled in area of the polygon.
PolygonLayer polygonLayer = new PolygonLayer(source,
    fillColor("rgba(255,165,0,0.2)")
);

//Create a line layer for greater control of rendering the outline of the polygon.
LineLayer lineLayer = new LineLayer(source,
    strokeColor("orange"),
    strokeWidth(2f)
);

//Create a bubble layer to render the vertices of the polygon as scaled circles.
BubbleLayer bubbleLayer = new BubbleLayer(source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
);

//Add all layers to the map.
map.layers.add(new Layer[] { polygonLayer, lineLayer, bubbleLayer });
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats())

//Create a polygon layer to render the filled in area of the polygon.
val polygonLayer = PolygonLayer(
    source,
    fillColor("rgba(255,165,0,0.2)")
)

//Create a line layer for greater control of rendering the outline of the polygon.
val lineLayer = LineLayer(
    source,
    strokeColor("orange"),
    strokeWidth(2f)
)

//Create a bubble layer to render the vertices of the polygon as scaled circles.
val bubbleLayer = BubbleLayer(
    source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
)

//Add all layers to the map.
map.layers.add(arrayOf<Layer>(polygonLayer, lineLayer, bubbleLayer))
```

::: zone-end

> [!TIP]
> Ao adicionar camadas ao mapa usando o `map.layers.add` método, a ID ou a instância de uma camada existente pode ser passada como um segundo parâmetro. Isso dirá ao MAP para inserir a nova camada que está sendo adicionada abaixo da camada existente. Além de passar uma ID de camada, esse método também dá suporte aos valores a seguir.
>
> - `"labels"` -Insere a nova camada abaixo das camadas do rótulo do mapa.
> - `"transit"` -Insere a nova camada abaixo da estrada do mapa e das camadas de trânsito.

## <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolhas](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linha](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar um mapa de calor](map-add-heat-map-layer-android.md)

> [!div class="nextstepaction"]
> [Exemplos de código do SDK Web](/samples/browse/?products=azure-maps)