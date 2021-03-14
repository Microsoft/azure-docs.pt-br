---
title: Adicionar uma camada do mapa de calor aos mapas do Android | Mapas do Microsoft Azure
description: Saiba como criar um mapa de calor. Consulte como usar o SDK do MapsAndroid do Azure para adicionar uma camada do mapa de calor a um mapa. Descubra como personalizar as camadas do mapa de calor.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: fce2c2d007f92c43e763826f9345f773324e885e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100178"
---
# <a name="add-a-heat-map-layer-android-sdk"></a>Adicionar uma camada do mapa de calor (SDK do Android)

Mapas de calor, também conhecidos como mapas de densidade de ponto, são um tipo de visualização de dados. Eles são usados para representar a densidade de dados usando um intervalo de cores e mostrar os dados "pontos de acesso" em um mapa. Mapas de calor são uma ótima maneira de renderizar conjuntos de valores com um grande número de pontos. 

A renderização de dezenas de milhares de pontos como símbolos pode abranger a maior parte da área do mapa. Esse caso provavelmente resultará em muitos símbolos sobrepostos. Dificultando uma compreensão melhor dos dados. No entanto, a visualização desse mesmo conjunto de dados como um mapa de calor facilita a visualização da densidade e da densidade relativa de cada ponto de dados.

Você pode usar mapas de calor em vários cenários diferentes, incluindo:

- **Dados de temperatura**: fornece aproximações para o que é a temperatura entre dois pontos de dados.
- **Dados para sensores de ruído**: mostra não apenas a intensidade do ruído em que o sensor está, mas também pode fornecer informações sobre a dissipação ao longo de uma distância. O nível de ruído em um site pode não estar alto. Se a área de cobertura de ruído de vários sensores se sobrepuser, é possível que essa área sobreposta possa ter níveis de ruído maiores. Dessa forma, a área sobreposta estaria visível no mapa de calor.
- **Rastreamento de GPS**: inclui a velocidade como um mapa de altura ponderado, em que a intensidade de cada ponto de dados é baseada na velocidade. Por exemplo, essa funcionalidade fornece uma maneira de ver onde um veículo estava acelerando.

> [!TIP]
> Por padrão, as camadas do mapa de calor renderizam as coordenadas de todas as geometrias em uma fonte de dados. Para limitar a camada para que ela só processe recursos de geometria de ponto, defina a `filter` opção da camada como `eq(geometryType(), "Point")` . Se você também quiser incluir recursos do MultiPoint, defina a `filter` opção da camada como `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Heat-Maps-and-Image-Overlays-in-Azure-Maps/player?format=ny]

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de concluir as etapas no documento [início rápido: criar um aplicativo Android](quick-android-map.md) . Os blocos de código neste artigo podem ser inseridos no `onReady` manipulador de eventos Maps.

## <a name="add-a-heat-map-layer"></a>Adicionar uma camada do mapa de calor

Para renderizar uma fonte de dados de pontos como um mapa de calor, passe sua fonte de dados para uma instância da `HeatMapLayer` classe e adicione-a ao mapa.

O exemplo de código a seguir carrega um feed geojson de terremotos da última semana e os renderiza como um mapa de calor. Cada ponto de dados é renderizado com um raio de 10 pixels em todos os níveis de zoom. Para garantir uma melhor experiência do usuário, o mapa de calor está abaixo da camada de rótulo para que os rótulos fiquem claramente visíveis. Os dados neste exemplo são do programa de [riscos de terremoto USGS](https://earthquake.usgs.gov/). Este exemplo carrega dados geojson da Web usando o bloco de código do utilitário de importação de dados fornecido no documento [criar uma fonte de dados](create-data-source-android-sdk.md) .

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a heat map layer.
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(10f),
  heatmapOpacity(0.8f)
);

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels");

//Import the geojson data and add it to the data source.
Utils.importData("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        source.add(fc);

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

//Create a heat map layer.
val layer = HeatMapLayer(
    source,
    heatmapRadius(10f),
    heatmapOpacity(0.8f)
)

//Add the layer to the map, below the labels.
map.layers.add(layer, "labels")

//Import the geojson data and add it to the data source.
Utils.importData("https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson",
    this
) { result: String? ->
    //Parse the data as a GeoJSON Feature Collection.
    val fc = FeatureCollection.fromJson(result!!)

    //Add the feature collection to the data source.
    source.add(fc)

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

A captura de tela a seguir mostra um mapa que carrega um mapa de calor usando o código acima.

![Mapear com a camada do mapa de calor de terremotos recentes](media/map-add-heat-map-layer-android/android-heat-map-layer.png)

## <a name="customize-the-heat-map-layer"></a>Personalizar a camada do mapa de calor

O exemplo anterior personalizou o mapa de calor, definindo as opções de raio e opacidade. A camada do mapa de calor fornece várias opções de personalização, incluindo:

- `heatmapRadius`: Define um raio de pixel no qual processar cada ponto de dados. Você pode definir o raio como um número fixo ou como uma expressão. Usando uma expressão, você pode dimensionar o raio com base no nível de zoom e representar uma área espacial consistente no mapa (por exemplo, um raio de 5 km).
- `heatmapColor`: Especifica como o mapa de calor é colorido. Um gradiente de cor é um recurso comum dos mapas de calor. Você pode obter o efeito com uma `interpolate` expressão. Você também pode usar uma `step` expressão para colorir o mapa de calor, dividindo a densidade visualmente em intervalos que se assemelham a uma contorno ou mapa de estilo de radar. Essas paletas de cores definem as cores do valor de densidade mínimo ao máximo.

  Você especifica valores de cor para mapas de calor como uma expressão no `heatmapDensity` valor. A cor da área em que não há dados definidos no índice 0 da expressão de "interpolação" ou a cor padrão de uma expressão "de nível". Você pode usar esse valor para definir uma cor de plano de fundo. Geralmente, esse valor é definido como transparente ou um preto semitransparente. 

  Aqui estão exemplos de expressões de cor:

  | Expressão de cor de interpolação | Expressão de cor de nível |
  |--------------------------------|--------------------------|
  | interpolar<br/>&nbsp;&nbsp;&nbsp;&nbsp;linear (), <br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Stop (0, Color (Color. Transparent)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;parar (0,01, cor (cor. MAGENTA)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Stop (0,5, Color (parseColor ("#fb00fb"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Stop (1, Color (parseColor ("#00c3ff")))<br/>)` | etapa<br/>&nbsp;&nbsp;&nbsp;&nbsp;heatmapDensity(),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Color (Color. Transparent),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Stop (0,01, Color (parseColor ("#000080"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;Stop (0,25, Color (parseColor ("#000080"))),<br/>&nbsp;&nbsp;&nbsp;&nbsp;parar (0,5, cor (cor. verde)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;parar (0,5, cor (cor. amarelo)),<br/>&nbsp;&nbsp;&nbsp;&nbsp;parar (1, cor (cor. vermelha))<br/>) |

- `heatmapOpacity`: Especifica o quão opaco ou transparente a camada do mapa de calor é.
- `heatmapIntensity`: Aplica um multiplicador ao peso de cada ponto de dados para aumentar a intensidade geral do calor. Isso causa uma diferença no peso dos pontos de dados, facilitando a visualização.
- `heatmapWeight`: Por padrão, todos os pontos de dados têm um peso de 1 e são ponderados igualmente. A opção Weight atua como um multiplicador e você pode defini-la como um número ou uma expressão. Se um número for definido como o peso, será a equivalência de colocar cada ponto de dados no mapa duas vezes. Por exemplo, se o peso for `2` , a densidade dobrará. Definir a opção de peso para um número renderiza o mapa de calor de maneira semelhante a usar a opção de intensidade.

  No entanto, se você usar uma expressão, o peso de cada ponto de dados poderá ser baseado nas propriedades de cada ponto de dados. Por exemplo, suponha que cada ponto de dados representa um terremoto. O valor de magnitude tem sido uma métrica importante para cada ponto de dados de terremoto. Os terremotos acontecem o tempo todo, mas têm uma magnitude menor e não são percebidos. Use o valor de magnitude em uma expressão para atribuir o peso a cada ponto de dados. Usando o valor de magnitude para atribuir o peso, você obtém uma representação melhor da significância dos terremotos dentro do mapa de calor.
- `minZoom` e `maxZoom` : o intervalo de nível de zoom em que a camada deve ser exibida.
- `filter`: Uma expressão de filtro usada para limitar o recuperado da origem e renderizado na camada.
- `sourceLayer`: Se a fonte de dados conectada à camada for uma fonte de bloco vetorial, uma camada de origem dentro dos blocos de vetor deverá ser especificada.
- `visible`: Oculta ou mostra a camada.

Este é um exemplo de um mapa de calor em que uma expressão de interpolação de uma capa é usada para criar um gradiente de cor suave. A `mag` propriedade definida nos dados é usada com uma interpolação exponencial para definir o peso ou a relevância de cada ponto de dados.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
    heatmapRadius(10f),

    //A linear interpolation is used to create a smooth color gradient based on the heat map density.
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.BLACK)),
            stop(0.25, color(Color.MAGENTA)),
            stop(0.5, color(Color.RED)),
            stop(0.75, color(Color.YELLOW)),
            stop(1, color(Color.WHITE))
        )
    ),

    //Using an exponential interpolation since earthquake magnitudes are on an exponential scale.
    heatmapWeight(
       interpolate(
            exponential(2),
            get("mag"),
            stop(0,0),

            //Any earthquake above a magnitude of 6 will have a weight of 1
            stop(6, 1)
       )
    )
)
```

::: zone-end

A captura de tela a seguir mostra a camada de mapa de calor personalizada acima usando os mesmos dados do exemplo de mapa de calor anterior.

![Mapear com a camada personalizada do mapa de calor de terremotos recentes](media/map-add-heat-map-layer-android/android-custom-heat-map-layer.png)

## <a name="consistent-zoomable-heat-map"></a>Mapa de calor com zoom consistente

Por padrão, o raios de pontos de dados renderizados na camada do mapa de calor têm um raio de pixel fixo para todos os níveis de zoom. À medida que você aplica zoom no mapa, os dados são agregados e a camada do mapa de calor é diferente. O vídeo a seguir mostra o comportamento padrão do mapa de calor onde ele mantém um raio de pixel ao aplicar zoom no mapa.

![Animação mostrando um zoom de mapa com uma camada de mapa de calor mostrando um tamanho de pixel consistente](media/map-add-heat-map-layer-android/android-heat-map-layer-zoom.gif)

Use uma `zoom` expressão para dimensionar o raio para cada nível de zoom, de modo que cada ponto de dados cubra a mesma área física do mapa. Essa expressão faz com que a camada do mapa de calor pareça mais estática e consistente. Cada nível de zoom do mapa tem duas vezes mais pixels vertical e horizontalmente como o nível de zoom anterior.

Dimensionar o raio para que ele fique duplo com cada nível de zoom cria um mapa de calor que se parece consistente em todos os níveis de zoom. Para aplicar esse dimensionamento, use `zoom` com uma expressão base 2 `exponential interpolation` , com o conjunto de raio de pixel definido para o nível de zoom mínimo e um raio dimensionado para o nível máximo de zoom calculado como `2 * Math.pow(2, minZoom - maxZoom)` mostrado no exemplo a seguir. Aplique zoom no mapa para ver como o mapa de calor é dimensionado com o nível de zoom.

::: zone pivot="programming-language-java-android"

```java
HeatMapLayer layer = new HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2, 19 - 1) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = HeatMapLayer(source,
  heatmapRadius(
    interpolate(
      exponential(2),
      zoom(),

      //For zoom level 1 set the radius to 2 pixels.
      stop(1, 2f),

      //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
      stop(19, Math.pow(2.0, 19 - 1.0) * 2f)
    )
  ),
  heatmapOpacity(0.75f)
)
```

::: zone-end

O vídeo a seguir mostra um mapa que executa o código acima, que dimensiona o raio enquanto o mapa está sendo ampliado para criar uma renderização de mapa de calor consistente nos níveis de zoom.

![Animação mostrando um zoom de mapa com uma camada de mapa de calor mostrando um tamanho geoespacial consistente](media/map-add-heat-map-layer-android/android-consistent-zoomable-heat-map-layer.gif)

## <a name="next-steps"></a>Próximas etapas

Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos:

> [!div class="nextstepaction"]
> [Criar uma fonte de dados](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-android-sdk.md)
