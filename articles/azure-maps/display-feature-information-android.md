---
title: Exibir informações de recurso em mapas do Android | Mapas do Microsoft Azure
description: Saiba como exibir informações quando os usuários interagem com recursos de mapa. Use o SDK do Android do Azure Maps para exibir mensagens do sistema e outros tipos de mensagens.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: ea9ed2c74651a7719533340a24c4741d4e25b805
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605715"
---
# <a name="display-feature-information"></a>Exibir informações do recurso

Os dados espaciais geralmente são representados usando pontos, linhas e polígonos. Esses dados geralmente têm informações de metadados associadas a ele. Por exemplo, um ponto pode representar o local de um restaurante e os metadados sobre esse restaurante podem ser seu nome, endereço e tipo de comida que ele atende. Esses metadados podem ser adicionados como propriedades de um geojson `Feature` . O código a seguir cria um recurso de ponto simples com uma `title` propriedade que tem um valor de "Olá, mundo!"

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64))

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!")

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature)
```

::: zone-end

Consulte a documentação [criar uma fonte de dados](create-data-source-android-sdk.md) para obter maneiras de criar e adicionar dados ao mapa.

Quando um usuário interage com um recurso no mapa, os eventos podem ser usados para reagir a essas ações. Um cenário comum é exibir uma mensagem composta das propriedades de metadados de um recurso com o qual o usuário interagiu. O `OnFeatureClick` evento é o principal evento usado para detectar quando o usuário tocou em um recurso no mapa. Também há um `OnLongFeatureClick` evento. Ao adicionar o `OnFeatureClick` evento ao mapa, ele pode ser limitado a uma única camada passando a ID de uma camada para limitar a ela. Se nenhuma ID de camada for passada, tocar em qualquer recurso no mapa, independentemente da camada em que ele está, acionaria esse evento. O código a seguir cria uma camada de símbolo para renderizar dados de ponto no mapa e, em seguida, adiciona um `OnFeatureClick` evento e o limita a essa camada de símbolo.

::: zone pivot="programming-language-java-android"

```java
//Create a symbol and add it to the map.
SymbolLayer layer = new SymbolLayer(source);
map.layers.add(layer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Do something with the message.

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false;
}, layer.getId());    //Limit this event to the symbol layer.
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a symbol and add it to the map.
val layer = SymbolLayer(source)
map.layers.add(layer)

//Add a feature click event to the map.
map.events.add(OnFeatureClick { features: List<Feature> ->
    //Retrieve the title property of the feature as a string.
    val msg = features[0].getStringProperty("title")

    //Do something with the message.

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false
}, layer.getId()) //Limit this event to the symbol layer.
```

::: zone-end

## <a name="display-a-toast-message"></a>Exibir uma mensagem do sistema

Uma mensagem do sistema é uma das maneiras mais fáceis de exibir informações para o usuário e está disponível em todas as versões do Android. Ele não dá suporte a nenhum tipo de entrada de usuário e só é exibido por um curto período de tempo. Se você quiser permitir que o usuário saiba rapidamente algo sobre o que ele tocou, uma mensagem do sistema poderá ser uma boa opção. O código a seguir mostra como uma mensagem do sistema pode ser usada com o `OnFeatureClick` evento.

::: zone pivot="programming-language-java-android"

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false;
}, layer.getId());    //Limit this event to the symbol layer.
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Add a feature click event to the map.
map.events.add(OnFeatureClick { features: List<Feature> ->
    //Retrieve the title property of the feature as a string.
    val msg = features[0].getStringProperty("title")

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show()

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false
}, layer.getId()) //Limit this event to the symbol layer.
```

::: zone-end

![Animação de um recurso que está sendo tocado e uma mensagem do sistema sendo exibida](media/display-feature-information-android/symbol-layer-click-toast-message.gif)

Além das mensagens do sistema, há muitas outras maneiras de apresentar as propriedades de metadados de um recurso, como:

- [Widget snackbar](https://developer.android.com/training/snackbar/showing.html)  -  `Snackbars` forneça comentários leves sobre uma operação. Eles mostram uma breve mensagem na parte inferior da tela no celular e na parte inferior esquerda em dispositivos maiores. `Snackbars` aparecem acima de todos os outros elementos na tela e apenas um pode ser exibido de cada vez.
- [Caixas de diálogo](https://developer.android.com/guide/topics/ui/dialogs) -uma caixa de diálogo é uma pequena janela que solicita que o usuário tome uma decisão ou insira informações adicionais. Uma caixa de diálogo não preenche a tela e é normalmente usada para eventos modais que exigem que os usuários executem uma ação antes que possam continuar.
- Adicione um [fragmento](https://developer.android.com/guide/components/fragments) à atividade atual.
- Navegue até outra atividade ou exibição.

## <a name="display-a-popup"></a>Exibir um pop-up

O Azure Maps SDK do Android fornece uma `Popup` classe que facilita a criação de elementos de anotação de interface do usuário que são ancorados a uma posição no mapa. Para pop-ups, você precisa passar uma exibição com um layout relativo para a `content` opção do pop-up. Aqui está um exemplo de layout simples que exibe texto escuro sobre um plano de fundo While.

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:orientation="vertical"
    android:background="#ffffff"
    android:layout_margin="8dp"
    android:padding="10dp"

    android:layout_height="match_parent">

    <TextView
        android:id="@+id/message"
        android:layout_width="wrap_content"
        android:text=""
        android:textSize="18dp"
        android:textColor="#222"
        android:layout_height="wrap_content"
        android:width="200dp"/>

</RelativeLayout>
```

Supondo que o layout acima seja armazenado em um arquivo chamado `popup_text.xml` na `res -> layout` pasta de um aplicativo, o código a seguir cria um pop-up, adiciona-o ao mapa. Quando um recurso é clicado, a `title` propriedade é exibida usando o `popup_text.xml` layout, com o centro inferior do layout ancorado à posição especificada no mapa.

::: zone pivot="programming-language-java-android"

```java
//Create a popup and add it to the map.
Popup popup = new Popup();
map.popups.add(popup);

map.events.add((OnFeatureClick)(feature) -> {
    //Get the first feature and it's properties.
    Feature f = feature.get(0);
    JsonObject props = f.properties();

    //Retrieve the custom layout for the popup.
    View customView = LayoutInflater.from(this).inflate(R.layout.popup_text, null);

    //Access the text view within the custom view and set the text to the title property of the feature.
    TextView tv = customView.findViewById(R.id.message);
    tv.setText(props.get("title").getAsString());

    //Get the coordinates from the clicked feature and create a position object.
    List<Double> c = ((Point)(f.geometry())).coordinates();
    Position pos = new Position(c.get(0), c.get(1));

    //Set the options on the popup.
    popup.setOptions(
        //Set the popups position.
        position(pos),

        //Set the anchor point of the popup content.
        anchor(AnchorType.BOTTOM),

        //Set the content of the popup.
        content(customView)

        //Optionally, hide the close button of the popup.
        //, closeButton(false)
    );

    //Open the popup.
    popup.open();

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false;
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a popup and add it to the map.
val popup = Popup()
map.popups.add(popup)

map.events.add(OnFeatureClick { feature: List<Feature> ->
    //Get the first feature and it's properties.
    val f = feature[0]
    val props = f.properties()

    //Retrieve the custom layout for the popup.
    val customView: View = LayoutInflater.from(this).inflate(R.layout.popup_text, null)

    //Access the text view within the custom view and set the text to the title property of the feature.
    val tv: TextView = customView.findViewById(R.id.message)
    tv.text = props!!["title"].asString

    //Get the coordinates from the clicked feature and create a position object.
    val c: List<Double> = (f.geometry() as Point?).coordinates()
    val pos = Position(c[0], c[1])

    //Set the options on the popup.
    popup.setOptions( 
        //Set the popups position.
        position(pos),  

        //Set the anchor point of the popup content.
        anchor(AnchorType.BOTTOM),  

        //Set the content of the popup.
        content(customView) 

        //Optionally, hide the close button of the popup.
        //, closeButton(false)
    )

    //Open the popup.
    popup.open()

    //Return a boolean indicating if event should be consumed or continue bubble up.
    return false
})
```

::: zone-end

A captura de tela a seguir mostra pop-ups que aparecem quando os recursos são clicados e ficam ancorados em seu local especificado no mapa conforme ele se move.

![Animação de um pop-up sendo exibido e o mapa movido com o Popup ancorado para uma posição no mapa](./media/display-feature-information-android/android-popup.gif)

## <a name="next-steps"></a>Próximas etapas

Para adicionar mais dados ao seu mapa:

> [!div class="nextstepaction"]
> [Reagir a eventos de mapeamento](android-map-events.md)

> [!div class="nextstepaction"]
> [Criar uma fonte de dados](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolhas](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linha](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](how-to-add-shapes-to-android-map.md)
