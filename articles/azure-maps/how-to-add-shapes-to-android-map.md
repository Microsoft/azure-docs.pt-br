---
title: Adicionar uma camada de polígono aos mapas do Android | Mapas do Microsoft Azure
description: Saiba como adicionar polígonos ou círculos a mapas. Veja como usar o SDK do Android do Azure Maps para personalizar formas geométricas e torná-las fáceis de atualizar e manter.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 68d68424e71bcf60bf504ae174b84b9c361b8637
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097254"
---
# <a name="add-a-polygon-layer-to-the-map-android-sdk"></a>Adicionar uma camada de polígono ao mapa (SDK do Android)

Este artigo mostra como renderizar as áreas de geometrias de recurso `Polygon` e `MultiPolygon` no mapa usando uma camada de polígono.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de concluir as etapas no documento [início rápido: criar um aplicativo Android](quick-android-map.md) . Os blocos de código neste artigo podem ser inseridos no `onReady` manipulador de eventos Maps.

## <a name="use-a-polygon-layer"></a>Usar uma camada de polígono

Quando uma camada de polígono é conectada a uma fonte de dados e carregada no mapa, ela renderiza a área com os recursos `Polygon` e `MultiPolygon`. Para criar um polígono, adicione-o a uma fonte de dados e renderize-o com uma camada de polígono usando a `PolygonLayer` classe.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source, 
    fillColor("red"),
    fillOpacity(0.7f)
), "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a rectangular polygon.
source.add(
    Polygon.fromLngLats(
        Arrays.asList(
            Arrays.asList(
                Point.fromLngLat(-73.98235, 40.76799),
                Point.fromLngLat(-73.95785, 40.80044),
                Point.fromLngLat(-73.94928, 40.79680),
                Point.fromLngLat(-73.97317, 40.76437),
                Point.fromLngLat(-73.98235, 40.76799)
            )
        )
    )
)

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(
    PolygonLayer(
        source,
        fillColor("red"),
        fillOpacity(0.7f)
    ), "labels"
)
```

::: zone-end

A captura de tela a seguir mostra o código acima renderizando a área de um polígono usando uma camada de polígono.

![Polígono com sua área de preenchimento renderizada](media/how-to-add-shapes-to-android-map/android-polygon-layer.png)

## <a name="use-a-polygon-and-line-layer-together"></a>Usar um polígono e uma camada de linha juntos

Uma camada de linha é usada para renderizar o contorno de polígonos. O exemplo de código a seguir renderiza um polígono como o exemplo anterior, mas agora adiciona uma camada de linha. Essa camada de linha é uma segunda camada conectada à fonte de dados.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source,
    fillColor("rgba(0, 200, 200, 0.5)")
), "labels");

//Create and add a line layer to render the outline of the polygon.
map.layers.add(new LineLayer(source,
    strokeColor("red"),
    strokeWidth(2f)
));
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a rectangular polygon.
source.add(
    Polygon.fromLngLats(
        Arrays.asList(
            Arrays.asList(
                Point.fromLngLat(-73.98235, 40.76799),
                Point.fromLngLat(-73.95785, 40.80044),
                Point.fromLngLat(-73.94928, 40.79680),
                Point.fromLngLat(-73.97317, 40.76437),
                Point.fromLngLat(-73.98235, 40.76799)
            )
        )
    )
)

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(
    PolygonLayer(
        source,
        fillColor("rgba(0, 200, 200, 0.5)")
    ), "labels"
)

//Create and add a line layer to render the outline of the polygon.
map.layers.add(
    LineLayer(
        source,
        strokeColor("red"),
        strokeWidth(2f)
    )
)
```

::: zone-end

A captura de tela a seguir mostra o código acima renderizando um polígono com seu contorno renderizado usando uma camada de linha.

![Polígono com sua área de preenchimento e estrutura de tópicos renderizados](media/how-to-add-shapes-to-android-map/android-polygon-and-line-layer.png)

> [!TIP]
> Ao estruturar um polígono com uma camada de linha, não se esqueça de fechar todos os anéis em polígonos de modo que cada matriz de pontos tenha o mesmo ponto inicial e final. Se isso não for feito, a camada de linha poderá não conectar o último ponto do polígono ao primeiro ponto.

## <a name="fill-a-polygon-with-a-pattern"></a>Preencher um polígono com um padrão

Além de preencher um polígono com uma cor, você pode usar um padrão de imagem para preencher o polígono. Carregue um padrão de imagem na imagem de mapas recursos de Sprite e, em seguida, faça referência a essa imagem com a `fillPattern` opção da camada de polígono.

::: zone pivot="programming-language-java-android"

```java
//Load an image pattern into the map image sprite.
map.images.add("fill-checker-red", R.drawable.fill_checker_red);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-50, -20),
            Point.fromLngLat(0, 40),
            Point.fromLngLat(50, -20),
            Point.fromLngLat(-50, -20)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source,
        fillPattern("fill-checker-red"),
        fillOpacity(0.5f)
), "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Load an image pattern into the map image sprite.
map.images.add("fill-checker-red", R.drawable.fill_checker_red)

//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a polygon.
source.add(
    Polygon.fromLngLats(
        Arrays.asList(
            Arrays.asList(
                Point.fromLngLat(-50, -20),
                Point.fromLngLat(0, 40),
                Point.fromLngLat(50, -20),
                Point.fromLngLat(-50, -20)
            )
        )
    )
)

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(
    PolygonLayer(
        source,
        fillPattern("fill-checker-red"),
        fillOpacity(0.5f)
    ), "labels"
)
```

::: zone-end

Para este exemplo, a imagem a seguir foi carregada na pasta desenhável do aplicativo.

| ![Imagem do ícone de seta roxa](media/how-to-add-shapes-to-android-map/fill-checker-red.png)|
|:-----------------------------------------------------------------------:|
| fill_checker_red.png                                                    |

Veja a seguir uma captura de tela do código acima renderizando um polígono com um padrão de preenchimento no mapa.

![Polígono com um padrão de preenchimento renderizado no mapa](media/how-to-add-shapes-to-android-map/android-polygon-pattern.jpg)

## <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Criar uma fonte de dados](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linha](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de extrusão de polígono](map-extruded-polygon-android.md)
