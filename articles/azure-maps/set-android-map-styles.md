---
title: Definir um estilo de mapa no Android Maps | Mapas do Microsoft Azure
description: Aprenda duas maneiras de definir o estilo de um mapa. Consulte como usar o SDK do Android do Azure Maps no arquivo de layout ou na classe de atividade para ajustar o estilo.
author: rbrundritt
ms.author: richbrun
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 1cce355c8ffbcd4704bd32b0e4d1739c77c2b623
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97678480"
---
# <a name="set-map-style-android-sdk"></a>Definir o estilo do mapa (SDK do Android)

Este artigo mostra duas maneiras de definir estilos de mapa usando o SDK do Android do Azure Maps. O mapas do Azure tem seis estilos de mapas diferentes para escolher. Para obter mais informações sobre os estilos de mapa com suporte, consulte [estilos de mapa com suporte no mapas do Azure](supported-map-styles.md).

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de concluir as etapas no documento [início rápido: criar um aplicativo Android](quick-android-map.md) .

## <a name="set-map-style-in-the-layout"></a>Definir o estilo de mapa no layout

Você pode definir um estilo de mapa no arquivo de layout para sua classe de atividade ao adicionar o controle de mapa. O código a seguir define o local central, o nível de zoom e o estilo de mapa.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/mapcontrol"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_centerLat="47.602806"
    app:mapcontrol_centerLng="-122.329330"
    app:mapcontrol_zoom="12"
    app:mapcontrol_style="grayscale_dark"
    />
```

A captura de tela a seguir mostra o código acima exibindo um mapa de estrada com o estilo de tons de cinza escuro.

![Mapa com estilo de mapa de estrada em escala de cinza](media/set-android-map-styles/android-grayscale-dark.png)

## <a name="set-map-style-in-code"></a>Definir o estilo do mapa no código

O estilo de mapa pode ser definido em programaticamente no código usando o `setStyle` método do mapa. O código a seguir define o local central e o nível de zoom usando o `setCamera` método Maps e o estilo de mapa como `SATELLITE_ROAD_LABELS` .

```java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-122.33, 47.64)), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE_ROAD_LABELS));
});
```

A captura de tela a seguir mostra o código acima exibindo um mapa com o estilo dos rótulos de estrada satélite.

![Mapear com estilo de rótulos de estrada satélite](media/set-android-map-styles/android-satellite-road-labels.png)

## <a name="setting-the-map-camera"></a>Configurando a câmera do mapa

A câmera do mapa controla o que parte do mapa é exibido no mapa. A câmera pode estar no layout de nossos programaticamente no código. Ao defini-lo no código, há dois métodos principais para definir a posição do mapa; usando Center e zoom, ou passando uma caixa delimitadora. O código a seguir mostra como definir todas as opções de câmera opcionais ao usar o `center` e o `zoom` .

```java
//Set the camera of the map using center and zoom.
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.64)), 

    //The zoom level. Typically a value between 0 and 22.
    zoom(14),

    //The amount of tilt in degrees the map where 0 is looking straight down.
    pitch(45),

    //Direction the top of the map is pointing in degrees. 0 = North, 90 = East, 180 = South, 270 = West
    bearing(90),

    //The minimum zoom level the map will zoom-out to when animating from one location to another on the map.
    minZoom(10),
    
    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

Geralmente, é desejável concentrar o mapa em um conjunto de dados. Uma caixa delimitadora pode ser calculada a partir de recursos que usam o `MapMath.fromData` método e pode ser passada para a `bounds` opção da câmera do mapa. Ao definir uma exibição de mapa com base em uma caixa delimitadora, geralmente é útil especificar um `padding` valor para levar em conta o tamanho de pixel dos pontos que estão sendo processados como bolhas ou símbolos. O código a seguir mostra como definir todas as opções de câmera opcionais ao usar uma caixa delimitadora para definir a posição da câmera.

```java
//Set the camera of the map using a bounding box.
map.setCamera(
    //The area to focus the map on.
    bounds(BoundingBox.fromLngLats(
        //West
        -122.4594,

        //South
        47.4333,
        
        //East
        -122.21866,
        
        //North
        47.75758
    )),

    //Amount of pixel buffer around the bounding box to provide extra space around the bounding box.
    padding(20),

    //The maximium zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

Observe que a taxa de proporção de uma caixa delimitadora pode não ser igual à taxa de proporção do mapa, pois o mapa geralmente mostrará a área de caixa delimitadora completa, mas, muitas vezes, será apertado vertical ou horizontalmente.

## <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolhas](map-add-bubble-layer-android.md)
