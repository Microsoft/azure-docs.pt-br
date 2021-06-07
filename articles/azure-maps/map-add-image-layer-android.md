---
title: Adicionar uma camada de imagem a um mapa do Android | Mapas do Microsoft Azure
description: Saiba como adicionar imagens a um mapa. Veja como usar o SDK do Android do Azure Maps para personalizar as camadas de imagem e sobreposição de imagens em conjuntos fixos de coordenadas.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: e1d99297c0357039606149bdf7e5a526258fc7c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054428"
---
# <a name="add-an-image-layer-to-a-map-android-sdk"></a>Adicionar uma camada de imagem a um mapa (SDK do Android)

Este artigo mostra como sobrepor uma imagem a um conjunto fixo de coordenadas. Aqui estão alguns exemplos de diferentes tipos de imagens que podem ser sobrepostas em mapas:

* Imagens capturadas de drones
* Criando floorplans
* Histórico ou outras imagens de mapa especializadas
* Plantas de sites de trabalho

> [!TIP]
> Uma camada de imagem é uma maneira fácil de sobrepor uma imagem em um mapa. Observe que imagens grandes podem consumir muita memória e potencialmente causar problemas de desempenho. Nesse caso, considere dividir a imagem em blocos e carregá-los no mapa como uma camada de peça.

## <a name="add-an-image-layer"></a>Adicionar uma camada de imagem

O código a seguir sobrepõe uma imagem de um [mapa de Newark, Nova Jersey, de 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) no mapa. Essa imagem é adicionada à `drawable` pasta do projeto. Uma camada de imagem é criada definindo-se a imagem e as coordenadas para os quatro cantos no formato `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` . A adição de camadas de imagem abaixo da `label` camada geralmente é desejável.

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setImage(R.drawable.newark_nj_1922)
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216)   //Bottom Left Corner
        )
    ),
    setImage(R.drawable.newark_nj_1922)
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

Como alternativa, uma URL para uma imagem hospedada no online pode ser especificada. No entanto, se o seu cenário permitir, adicione a imagem à `drawable` pasta projetos, que será carregada mais rapidamente, uma vez que a imagem estará disponível localmente e não precisará ser baixada.

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216) //Bottom Left Corner
        )
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

A captura de tela a seguir mostra um mapa de Newark, Nova Jersey, de 1922 sobreposto usando uma camada de imagem.

![Mapa de Newark, Nova Jersey, de 1922 sobreposto usando uma camada de imagem](media/map-add-image-layer-android/android-image-layer.gif)

## <a name="import-a-kml-file-as-ground-overlay"></a>Importar um arquivo KML como uma sobreposição de terra

Este exemplo demonstra como adicionar informações de sobreposição de terra KML como uma camada de imagem no mapa. As sobreposições de aterramento KML fornecem coordenadas norte, Sul, leste e oeste e uma rotação no sentido anti-horário. Porém, a camada de imagem espera coordenadas para cada canto da imagem. A sobreposição de aterramento KML neste exemplo é para o Chartres Cathedral e é originada de [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kml xmlns="http://www.opengis.net/kml/2.2" xmlns:gx="http://www.google.com/kml/ext/2.2" xmlns:kml="http://www.opengis.net/kml/2.2" xmlns:atom="http://www.w3.org/2005/Atom">
<GroundOverlay>
    <name>Map of Chartres cathedral</name>
    <Icon>
        <href>https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png</href>
        <viewBoundScale>0.75</viewBoundScale>
    </Icon>
    <LatLonBox>
        <north>48.44820923628113</north>
        <south>48.44737203258976</south>
        <east>1.488833825534365</east>
        <west>1.486788581643038</west>
        <rotation>46.44067597839695</rotation>
    </LatLonBox>
</GroundOverlay>
</kml>
```

O código usa o `getCoordinatesFromEdges` método estático da `ImageLayer` classe. Esse método calcula os quatro cantos da imagem usando as informações do Norte, Sul, leste, oeste e de rotação da sobreposição de aterramento KML.

::: zone pivot="programming-language-java-android"

```java
//Calculate the corner coordinates of the ground overlay.
Position[] corners = ImageLayer.getCoordinatesFromEdges(
    //North, south, east, west
    48.44820923628113, 48.44737203258976, 1.488833825534365, 1.486788581643038,

    //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
    360 - 46.44067597839695
);

//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Calculate the corner coordinates of the ground overlay.
val corners: Array<Position> =
    ImageLayer.getCoordinatesFromEdges( //North, south, east, west
        48.44820923628113,
        48.44737203258976,
        1.488833825534365,
        1.486788581643038,  //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
        360 - 46.44067597839695
    )

//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

A captura de tela a seguir mostra um mapa com uma sobreposição de aterramento KML sobreposta usando uma camada de imagem.

![Mapear com uma sobreposição de aterramento KML sobreposta usando uma camada de imagem](media/map-add-image-layer-android/android-ground-overlay.jpg)

> [!TIP]
> Use os `getPixels` `getPositions` métodos e da classe da camada de imagem para converter entre coordenadas geográficas da camada de imagem posicionada e as coordenadas de pixel de imagem local.

## <a name="next-steps"></a>Próximas etapas

Consulte o artigo a seguir para saber mais sobre maneiras de sobrepor imagens em um mapa.

> [!div class="nextstepaction"]
> [Adicionar uma camada de bloco](how-to-add-tile-layer-android-map.md)