---
title: Adicionar uma camada de símbolo aos mapas do Android | Mapas do Microsoft Azure
description: Saiba como adicionar um marcador a um mapa. Veja um exemplo que usa o SDK do Android do Azure Maps para adicionar uma camada de símbolo que contém dados baseados em pontos de uma fonte de dados.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: edb758469a06dcb7914025ea449b9d952e939533
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097203"
---
# <a name="add-a-symbol-layer-android-sdk"></a>Adicionar uma camada de símbolo (SDK do Android)

Este artigo mostra como renderizar dados de ponto de uma fonte de dados como uma camada de símbolo em um mapa usando o SDK do Android do Azure Maps. As camadas de símbolo processam pontos como uma imagem e texto no mapa.

> [!TIP]
> Por padrão, as camadas de Símbolo renderizarão as coordenadas de todas as geometrias em uma fonte de dados. Para limitar a camada para que ela só processe recursos de geometria de ponto, defina a `filter` opção da camada como `eq(geometryType(), "Point")` . Se você também quiser incluir recursos do MultiPoint, defina a `filter` opção da camada como `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de concluir as etapas no documento [início rápido: criar um aplicativo Android](quick-android-map.md) . Os blocos de código neste artigo podem ser inseridos no `onReady` manipulador de eventos Maps.

## <a name="add-a-symbol-layer"></a>Adicionar uma camada de símbolo

Para poder adicionar uma camada de símbolo ao mapa, você precisa executar algumas etapas. Primeiro, crie uma fonte de dados e adicione-a ao mapa. Crie uma camada de símbolo. Em seguida, passe a fonte de dados para a camada de símbolo para recuperar os dados da fonte de dados. Por fim, adicione dados à fonte de dados para que haja algo a ser renderizado.

O código a seguir demonstra o que deve ser adicionado ao mapa depois que ele é carregado. Este exemplo renderiza um único ponto no mapa usando uma camada de símbolo.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source);

//Add the layer to the map.
map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0))

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(source)

//Add the layer to the map.
map.layers.add(layer)
```

::: zone-end

Há três tipos diferentes de dados de ponto que podem ser adicionados ao mapa:

- Geometria de ponto geojson-esse objeto contém apenas uma coordenada de um ponto e nada mais. O `Point.fromLngLat` método estático pode ser usado para criar facilmente esses objetos.
- Geojson MultiPoint Geometry-este objeto contém as coordenadas de vários pontos e nada mais. Passe uma matriz de pontos para a `MultiPoint` classe para criar esses objetos.
- Recurso geojson – esse objeto consiste em qualquer geometria geojson e um conjunto de propriedades que contêm metadados associados à geometria.

Para obter mais informações, consulte o documento [criar uma fonte de dados](create-data-source-android-sdk.md) ao criar e adicionar dados ao mapa.

O exemplo de código a seguir cria uma geometria de ponto geojson e a transmite para o recurso geojson e tem um `title` valor adicionado às suas propriedades. A `title` propriedade é exibida como texto acima do ícone de símbolo no mapa.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(0, 0));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source, 
    //Get the title property of the feature and display it on the map.
    textField(get("title"))
);

//Add the layer to the map.
map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(0, 0))

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!")

//Add the feature to the data source.
source.add(feature)

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(
    source,  //Get the title property of the feature and display it on the map.
    textField(get("title"))
)

//Add the layer to the map.
map.layers.add(layer)
```

::: zone-end

A captura de tela a seguir mostra o código acima renderização um recurso de ponto usando um ícone e um rótulo de texto com uma camada de símbolo.

![Mapear com o ponto renderizado usando uma camada de símbolo que exibe um ícone e um rótulo de texto para um recurso de ponto](media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> Por padrão, as camadas de símbolo otimizam a renderização de símbolos, ocultando os símbolos que se sobrepõem. À medida que você amplia, os símbolos ocultos se tornam visíveis. Para desabilitar esse recurso e renderizar todos os símbolos em todos os momentos, defina as `iconAllowOverlap` `textAllowOverlap` Opções e como `true` .

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Adicionar um ícone personalizado a uma camada de símbolo

As camadas de símbolo são renderizadas usando o WebGL. Assim, todos os recursos, como imagens de ícone, precisam ser carregados no contexto do WebGL. Este exemplo mostra como adicionar um ícone personalizado aos recursos de mapa. Esse ícone é usado para renderizar dados de ponto com um símbolo personalizado no mapa. A propriedade `textField` da camada de símbolo exige a especificação de uma expressão. Nesse caso, queremos renderizar a propriedade de temperatura. Como a temperatura é um número, ela precisa ser convertida em uma cadeia de caracteres. Além disso, queremos acrescentar "° f" a ele. Uma expressão pode ser usada para fazer essa concatenação; `concat(Expression.toString(get("temperature")), literal("°F"))`.

::: zone pivot="programming-language-java-android"

```java
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773));

//Add a property to the feature.
feature.addNumberProperty("temperature", 64);

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),

    //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(new Float[]{0f, -1.5f})
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers)

//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773))

//Add a property to the feature.
feature.addNumberProperty("temperature", 64)

//Add the feature to the data source.
source.add(feature)

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(
    source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),  //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(arrayOf(0f, -1.5f))
)
```

::: zone-end

Para este exemplo, a imagem a seguir foi carregada na pasta desenhável do aplicativo.

| ![Imagem do ícone de clima da chuva hadrônicos](media/how-to-add-symbol-to-android-map/showers.png)|
|:-----------------------------------------------------------------------:|
| showers.png                                                  |

A captura de tela a seguir mostra o código acima renderização um recurso de ponto usando um ícone personalizado e um rótulo de texto formatado com uma camada de símbolo.

![Mapear com o ponto renderizado usando uma camada de símbolo que exibe um ícone personalizado e um rótulo de texto formatado para um recurso de ponto](media/how-to-add-symbol-to-android-map/android-custom-symbol-layer.png)

> [!TIP]
> Quando você quiser renderizar apenas o texto com uma camada de símbolo, poderá ocultar o ícone definindo a `iconImage` propriedade das opções de ícone como `"none"` .

## <a name="modify-symbol-colors"></a>Modificar cores de símbolo

O SDK do Android do Azure Maps vem com um conjunto de variações de cores predefinidas do ícone de marcador padrão. Por exemplo, `marker-red` pode ser passado para a `iconImage` opção de uma camada de símbolo para renderizar uma versão vermelha do ícone de marcador nessa camada.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    iconImage("marker-red")
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    iconImage("marker-red")
)
```

::: zone-end

A tabela a seguir lista todos os nomes de imagem de ícone internos disponíveis. Todos esses marcadores recebem suas cores de recursos de cores que você pode substituir. Além de substituir a cor de preenchimento principal deste marcador. No entanto, observe que a substituição da cor de um desses marcadores seria aplicada a todas as camadas que usam essa imagem de ícone.

| Nome da imagem do ícone | Nome do recurso de cor |
|-----------------|---------------------|
| `marker-default` | `mapcontrol_marker_default` |
| `marker-black` | `mapcontrol_marker_black` |
| `marker-blue` | `mapcontrol_marker_blue` |
| `marker-darkblue` | `mapcontrol_marker_darkblue` |
| `marker-red` | `mapcontrol_marker_red` |
| `marker-yellow` | `mapcontrol_marker_yellow` |

Você também pode substituir a cor da borda de todos os marcadores usando o `mapcontrol_marker_border` nome do recurso de cor. As cores desses marcadores podem ser substituídas adicionando-se uma cor com o mesmo nome no `colors.xml` arquivo do aplicativo. Por exemplo, o arquivo a seguir `colors.xml` tornaria a cor de marcador padrão verde brilhante.

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="mapcontrol_marker_default">#00FF00</color>
</resources>
```

A seguir está uma versão modificada do XML de vetor de marcador padrão que você pode modificar para criar versões personalizadas adicionais do marcador padrão. A versão modificada pode ser adicionada à `drawable` pasta do seu aplicativo e adicionada à imagem de mapas Sprite usando `map.images.add` e, em seguida, usada com uma camada de símbolo.

```xml
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24.5dp"
    android:height="36.5dp"
    android:viewportWidth="24.5"
    android:viewportHeight="36.5">
    <path
        android:pathData="M12.25,0.25a12.2543,12.2543 0,0 0,-12 12.4937c0,6.4436 6.4879,12.1093 11.059,22.5641 0.5493,1.2563 1.3327,1.2563 1.882,0C17.7621,24.8529 24.25,19.1857 24.25,12.7437A12.2543,12.2543 0,0 0,12.25 0.25Z"
        android:strokeWidth="0.5"
        android:fillColor="@color/mapcontrol_marker_default"
        android:strokeColor="@color/mapcontrol_marker_border"/>
</vector>
```

## <a name="next-steps"></a>Próximas etapas

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Criar uma fonte de dados](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolhas](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Exibir informações do recurso](display-feature-information-android.md)
