---
title: Adicionar uma camada de linha aos mapas do Android | Mapas do Microsoft Azure
description: Saiba como adicionar linhas a mapas. Veja exemplos que usam o SDK do Android do Azure Maps para adicionar camadas de linha a mapas e personalizar linhas com símbolos e gradientes de cor.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 3e68be79a4405af103512a9009187857a0d9af39
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681417"
---
# <a name="add-a-line-layer-to-the-map-android-sdk"></a>Adicionar uma camada de linha ao mapa (SDK do Android)

Uma camada de linhas pode ser usada para renderizar os recursos `LineString` e `MultiLineString` como caminhos ou rotas no mapa. Uma camada de linhas também pode ser usada para renderizar a estrutura de tópicos dos recursos `Polygon` e `MultiPolygon`. Uma fonte de dados é conectada a uma camada de linhas para fornecer a ela dados a serem renderizados.

> [!TIP]
> Por padrão, as camadas de linhas renderizarão as coordenadas dos polígonos e das linhas em uma fonte de dados. Para limitar a camada para que ela só processe recursos de geometria de LineString, defina a `filter` opção da camada como `eq(geometryType(), "LineString")` . Se você quiser incluir recursos MultiLineString também, defina a `filter` opção da camada como `any(eq(geometryType(), "LineString"), eq(geometryType(), "MultiLineString"))` .

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de concluir as etapas no documento [início rápido: criar um aplicativo Android](quick-android-map.md) . Os blocos de código neste artigo podem ser inseridos no `onReady` manipulador de eventos Maps.

## <a name="add-a-line-layer"></a>Adicionar uma camada de linha

O código a seguir mostra como criar uma linha. Adicione a linha a uma fonte de dados e, em seguida, processe-a com uma camada de linha usando a `LineLayer` classe.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a list of points.
List<Point> points = Arrays.asList(
    Point.fromLngLat(-73.972340, 40.743270),
    Point.fromLngLat(-74.004420, 40.756800));

//Create a LineString geometry and add it to the data source.
source.add(LineString.fromLngLats(points));

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor("blue"),
    strokeWidth(5f)
);

map.layers.add(layer);
```

A captura de tela a seguir mostra o código acima Renderizando uma linha em uma camada de linha.

![Mapear com uma linha renderizada usando a camada de linha](media/android-map-add-line-layer/android-line-layer.png)

## <a name="data-drive-line-style"></a>Estilo de linha de unidade de dados

O código a seguir cria dois recursos de linha e adiciona um valor de limite de velocidade como uma propriedade para cada linha. Uma camada de linha usa uma expressão de estilo de unidade de dados cor das linhas com base no valor do limite de velocidade. Como as sobreposições de dados de linha ao longo de estradas, o código a seguir adiciona a camada de linha abaixo da camada de rótulo para que os rótulos de estrada ainda possam ser lidos claramente.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a line feature.
Feature feature = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.131821, 47.704033),
        Point.fromLngLat(-122.099919, 47.703678))));

//Add a property to the feature.
feature.addNumberProperty("speedLimitMph", 35);

//Add the feature to the data source.
source.add(feature);

//Create a second line feature.
Feature feature2 = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.126662, 47.708265),
        Point.fromLngLat(-122.126877, 47.703980))));

//Add a property to the second feature.
feature2.addNumberProperty("speedLimitMph", 15);

//Add the second feature to the data source.
source.add(feature2);

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor(
        interpolate(
            linear(),
            get("speedLimitMph"),
            stop(0, color(Color.GREEN)),
            stop(30, color(Color.YELLOW)),
            stop(60, color(Color.RED))
        )
    ),
    strokeWidth(5f)
);

map.layers.add(layer, "labels");
```

A captura de tela a seguir mostra o código acima renderizando duas linhas em uma camada de linha com sua cor sendo recuperada de uma expressão de estilo controlada por dados com base em uma propriedade nos recursos de linha.

![Mapear com linhas com estilo de unidade de dados renderizadas em uma camada de linha](media/android-map-add-line-layer/android-line-layer-data-drive-style.png)

## <a name="add-symbols-along-a-line"></a>Adicionar símbolos ao longo de uma linha

Este exemplo demonstra como adicionar ícones de seta ao longo de uma linha no mapa. Ao usar uma camada de símbolo, defina a `symbolPlacement` opção como `SymbolPlacement.LINE` . Essa opção renderizará os símbolos ao longo da linha e vai girar os ícones (0 grau = direita).

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Load a image of an arrow into the map image sprite and call it "arrow-icon".
map.images.add("arrow-icon", R.drawable.purple-arrow-right);

//Create and add a line to the data source.
source.add(LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.18822, 47.63208),
        Point.fromLngLat(-122.18204, 47.63196),
        Point.fromLngLat(-122.17243, 47.62976),
        Point.fromLngLat(-122.16419, 47.63023),
        Point.fromLngLat(-122.15852, 47.62942),
        Point.fromLngLat(-122.15183, 47.62988),
        Point.fromLngLat(-122.14256, 47.63451),
        Point.fromLngLat(-122.13483, 47.64041),
        Point.fromLngLat(-122.13466, 47.64422),
        Point.fromLngLat(-122.13844, 47.65440),
        Point.fromLngLat(-122.13277, 47.66515),
        Point.fromLngLat(-122.12779, 47.66712),
        Point.fromLngLat(-122.11595, 47.66712),
        Point.fromLngLat(-122.11063, 47.66735),
        Point.fromLngLat(-122.10668, 47.67035),
        Point.fromLngLat(-122.10565, 47.67498))));

//Create a line layer and add it to the map.
map.layers.add(new LineLayer(source,
    strokeColor("DarkOrchid"),
    strokeWidth(5f)
));

//Create a symbol layer and add it to the map.
map.layers.add(new SymbolLayer(source,
    //Space symbols out along line.
    symbolPlacement(SymbolPlacement.LINE),

    //Spread the symbols out 100 pixels apart.
    symbolSpacing(100f),
    
    //Use the arrow icon as the symbol.
    iconImage("arrow-icon"),

    //Allow icons to overlap so that they aren't hidden if they collide with other map elements.
    iconAllowOverlap(true),

    //Center the symbol icon.
    iconAnchor(AnchorType.CENTER),

    //Scale the icon size.
    iconSize(0.8f)
));
```

Para este exemplo, a imagem a seguir foi carregada na pasta desenhável do aplicativo.

| ![Imagem do ícone de seta roxa](media/android-map-add-line-layer/purple-arrow-right.png)|
|:-----------------------------------------------------------------------:|
|                                                  |

A captura de tela abaixo mostra o código acima Renderizando uma linha com ícones de seta exibidos ao longo dele.

![Mapear com linhas com estilo de unidade de dados com setas renderizadas em uma camada de linha](media/android-map-add-line-layer/android-symbols-along-line-path.png)

## <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Criar uma fonte de dados](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](how-to-add-shapes-to-android-map.md)
