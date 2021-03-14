---
title: Adicionar controles a um mapa do Android | Mapas do Microsoft Azure
description: Como adicionar controle de zoom, controle de densidade, controle de rotação e um seletor de estilo a um mapa em Microsoft Azure mapas SDK do Android.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 90d037fc02bdc1c4d6fe682386790561c890c1e6
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100212"
---
# <a name="add-controls-to-a-map-android-sdk"></a>Adicionar controles a um mapa (SDK do Android)

Este artigo mostra como adicionar controles de interface do usuário ao mapa.

## <a name="add-zoom-control"></a>Adicionar o controle de zoom

Um controle de zoom Adiciona botões para ampliar e reduzir o mapa. O exemplo de código a seguir cria uma instância da `ZoomControl` classe e a adiciona a um mapa.

::: zone pivot="programming-language-java-android"

```java
//Construct a zoom control and add it to the map.
map.controls.add(new ZoomControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a zoom control and add it to the map.
map.controls.add(ZoomControl())
```

::: zone-end

A captura de tela abaixo é de um controle de zoom carregado em um mapa.

![Controle de zoom adicionado ao mapa](media/map-add-controls-android/android-zoom-control.jpg)

## <a name="add-pitch-control"></a>Adicionar controle de densidade

Um controle de densidade Adiciona botões para inclinar a inclinação para mapear em relação ao horizonte. O exemplo de código a seguir cria uma instância da `PitchControl` classe e a adiciona a um mapa.

::: zone pivot="programming-language-java-android"

```java
//Construct a pitch control and add it to the map.
map.controls.add(new PitchControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a pitch control and add it to the map.
map.controls.add(PitchControl())
```

::: zone-end

A captura de tela abaixo é de um controle de timbre carregado em um mapa.

![Controle de densidade adicionado ao mapa](media/map-add-controls-android/android-pitch-control.jpg)

## <a name="add-compass-control"></a>Adicionar controle de bússola

Um controle Compass adiciona um botão para girar o mapa. O exemplo de código a seguir cria uma instância da `CompassControl` classe e a adiciona a um mapa.

::: zone pivot="programming-language-java-android"

```java
//Construct a compass control and add it to the map.
map.controls.add(new CompassControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a compass control and add it to the map.
map.controls.add(CompassControl())
```

::: zone-end

A captura de tela abaixo é de um controle de bússola carregado em um mapa.

![Controle de bússola adicionado ao mapa](media/map-add-controls-android/android-compass-control.jpg)

## <a name="add-traffic-control"></a>Adicionar controle de tráfego

Um controle de tráfego adiciona um botão para alternar a visibilidade dos dados de tráfego no mapa. O exemplo de código a seguir cria uma instância da `TrafficControl` classe e a adiciona a um mapa.

::: zone pivot="programming-language-java-android"

```java
//Construct a traffic control and add it to the map.
map.controls.add(new TrafficControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a traffic control and add it to the map.
map.controls.add(TrafficControl())
```

::: zone-end

A captura de tela abaixo é de um controle de tráfego carregado em um mapa.

![Controle de tráfego adicionado ao mapa](media/map-add-controls-android/android-traffic-control.jpg)

## <a name="a-map-with-all-controls"></a>Um mapa com todos os controles

Vários controles podem ser colocados em uma matriz e adicionados ao mapa de uma só vez e posicionados na mesma área do mapa para simplificar o desenvolvimento. O seguinte adiciona os controles de navegação padrão ao mapa usando essa abordagem.

::: zone pivot="programming-language-java-android"

```java
map.controls.add(
    new Control[]{
        new ZoomControl(),
        new CompassControl(),
        new PitchControl(),
        new TrafficControl()
    }
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.controls.add(
    arrayOf<Control>(
        ZoomControl(),
        CompassControl(),
        PitchControl(),
        TrafficControl()
    )
)
```

::: zone-end

A captura de tela abaixo mostra todos os controles carregados em um mapa. Observe que a ordem em que eles são adicionados ao mapa é a ordem em que eles serão exibidos.

![Todos os controles adicionados ao mapa](media/map-add-controls-android/android-all-controls.jpg)

## <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolhas](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linha](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](how-to-add-shapes-to-android-map.md)
