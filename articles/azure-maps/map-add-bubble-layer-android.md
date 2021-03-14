---
title: Adicionar uma camada de bolha aos mapas do Android | Mapas do Microsoft Azure
description: Saiba como renderizar pontos em mapas como círculos com tamanhos fixos. Consulte como usar o SDK do Android do Azure Maps para adicionar e personalizar camadas de bolha para essa finalidade.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: f3c175f30c5c0e6206f4fee274e0f3f000e55a74
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100161"
---
# <a name="add-a-bubble-layer-to-a-map-android-sdk"></a>Adicionar uma camada de bolha a um mapa (SDK do Android)

Este artigo mostra como renderizar dados de ponto de uma fonte de dados como uma camada de bolha em um mapa. Camadas de bolha renderizam pontos como círculos no mapa com um raio de pixel fixo.

> [!TIP]
> As camadas de bolha por padrão processarão as coordenadas de todas as geometrias em uma fonte de dados. Para limitar a camada para que ela só processe recursos de geometria de ponto, defina a `filter` opção da camada como `eq(geometryType(), "Point")` . Se você também quiser incluir recursos do MultiPoint, defina a `filter` opção da camada como `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de concluir as etapas no documento [início rápido: criar um aplicativo Android](quick-android-map.md) . Os blocos de código neste artigo podem ser inseridos no `onReady` manipulador de eventos Maps.

## <a name="add-a-bubble-layer"></a>Adicionar uma camada de bolha

O código a seguir carrega uma matriz de pontos em uma fonte de dados. Em seguida, ele conecta os pontos de dados a uma camada de bolha. A camada de bolha renderiza o raio de cada bolha com cinco pixels e uma cor de preenchimento de branco. E uma cor de traço de azul e uma largura de traço de seis pixels.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create point locations.
Point[] points = new Point[] {
    Point.fromLngLat(-73.985708, 40.75773),
    Point.fromLngLat(-73.985600, 40.76542),
    Point.fromLngLat(-73.985550, 40.77900),
    Point.fromLngLat(-73.975550, 40.74859),
    Point.fromLngLat(-73.968900, 40.78859)
};

//Add multiple points to the data source.
source.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
BubbleLayer layer = new BubbleLayer(source, 
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
);

map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create point locations.
val points: Array<Point> = arrayOf<Point>(
    Point.fromLngLat(-73.985708, 40.75773),
    Point.fromLngLat(-73.985600, 40.76542),
    Point.fromLngLat(-73.985550, 40.77900),
    Point.fromLngLat(-73.975550, 40.74859),
    Point.fromLngLat(-73.968900, 40.78859)
)

//Add multiple points to the data source.
source.add(points)

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
val layer = BubbleLayer(
    source,
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
)

map.layers.add(layer)
```

::: zone-end

A captura de tela a seguir mostra que o código acima renderiza pontos em uma camada de bolha.

![Mapear com pontos renderizados usando a camada de bolha](media/map-add-bubble-layer-android/android-bubble-layer.png)

## <a name="show-labels-with-a-bubble-layer"></a>Mostrar rótulos de uma camada de bolha

Este código mostra como usar uma camada de bolha para renderizar um ponto no mapa. E, como usar uma camada de símbolo para renderizar um rótulo. Para ocultar o ícone da camada de símbolo, defina a `iconImage` opção como `"none"` .

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add a data point to the map.
source.add(Point.fromLngLat(-122.336641,47.627631));

//Add a bubble layer.
map.layers.add(new BubbleLayer(source,
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
));

//Add a symbol layer to display text, hide the icon image.
map.layers.add(new SymbolLayer(source,
    //Hide the icon image.
    iconImage("none"),
    textField("Museum of History & Industry (MOHAI)"),
    textColor("#005995"),
    textOffset(new Float[]{0f, -2.2f})
));
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Add a data point to the map.
source.add(Point.fromLngLat(-122.336641, 47.627631))

//Add a bubble layer.
map.layers.add(
    BubbleLayer(
        source,
        bubbleRadius(5f),
        bubbleColor("white"),
        bubbleStrokeColor("#4288f7"),
        bubbleStrokeWidth(6f)
    )
)

//Add a symbol layer to display text, hide the icon image.
map.layers.add(
    SymbolLayer(
        source,  //Hide the icon image.
        iconImage("none"),
        textField("Museum of History & Industry (MOHAI)"),
        textColor("#005995"),
        textOffset(arrayOf(0f, -2.2f))
    )
)
```

::: zone-end

A captura de tela a seguir mostra o código acima renderização um ponto em uma camada de bolha e um rótulo de texto para o ponto com uma camada de símbolo.

![Mapear com ponto renderizado usando uma camada de bolha e um rótulo de texto com camada de símbolo](media/map-add-bubble-layer-android/android-bubble-symbol-layer.png)

## <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Criar uma fonte de dados](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Exibir informações do recurso](display-feature-information-android.md)
