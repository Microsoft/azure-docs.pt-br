---
title: Tutorial – Migrar um aplicativo do Android | Microsoft Azure Mapas
description: Tutorial sobre como migrar um aplicativo Android do Google Maps para o Microsoft Azure Mapas
author: rbrundritt
ms.author: richbrun
ms.date: 08/19/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: f4b0642ce54b862b4d4c7b9663cf10e74b206281
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680478"
---
# <a name="tutorial-migrate-an-android-app-from-google-maps"></a>Tutorial: Migrar um aplicativo Android do Google Maps

O SDK do Android dos Azure Mapas tem uma interface de API semelhante à do SDK da Web. Se você desenvolveu um desses SDKs, muitos dos mesmos conceitos, melhores práticas e arquiteturas se aplicam. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Carregar um mapa
> * Localizar um mapa
> * Adicionar marcadores, polilinhas e polígonos.
> * Sobrepor uma camada de peça
> * Mostrar dados de tráfego

O SDK do Android do Azure Mapas dá suporte a uma versão mínima do Android de API 21: Android 5.0.0 (Lollipop).

Todos os exemplos são fornecidos em Java; no entanto, você pode usar o Kotlin com o SDK do Android dos Azure Mapas.

Para obter mais informações sobre o desenvolvimento com o SDK do Android pelos Azure Mapas, confira os [Guia de instruções do SDK do Android dos Azure Mapas](how-to-use-android-map-control-library.md).

## <a name="prerequisites"></a>Pré-requisitos

1. Crie uma conta do Azure Mapas entrando no [portal do Azure](https://portal.azure.com). Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
2. [Faça uma conta do Azure Mapas](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Obtenha uma chave de assinatura primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), também conhecida como a chave primária ou a chave de assinatura. Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](how-to-manage-authentication.md).

## <a name="load-a-map"></a>Carregar um mapa

O carregamento de um mapa em um aplicativo Android usando o Google Maps ou os Azure Mapas consiste em etapas semelhantes. Ao usar um dos SDKs, você precisará:

* Obter uma API ou chave de assinatura para acessar uma das duas plataformas.
* Adicionar um XML a uma atividade para especificar em que local o mapa deve ser renderizado e como ele deve ser disposto.
* Substituir todos os métodos de ciclo de vida da atividade que contém a exibição de mapa pelos métodos correspondentes na classe de mapa. Em particular, você precisará substituir os seguintes métodos:
  * `onCreate(Bundle)`
  * `onStart()`
  * `onResume()`
  * `onPause()`
  * `onStop()`
  * `onDestroy()`
  * `onSaveInstanceState(Bundle)`
  * `onLowMemory()`
* Aguarde até que o mapa esteja pronto antes de tentar acessá-lo e programá-lo.

### <a name="before-google-maps"></a>Antes: Google Maps

Para exibir um mapa usando o SDK do Google Maps para Android, as etapas a seguir serão executadas:

1. Verifique se os serviços do Google Play estão instalados.
2. Adicione uma dependência para o serviço do Google Maps ao arquivo **gradle.build** do módulo:

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

3. Adicione uma chave de API do Google Maps dentro da seção do aplicativo do arquivo **google\_maps\_api.xml**:

    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

4. Adicione um fragmento de mapa à atividade principal:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

5. No arquivo **MainActivity.java**, você precisará importar o SDK do Google Maps. Encaminhar todos os métodos de ciclo de vida da atividade que contém a exibição do mapa para aqueles correspondentes na classe do mapa. Recupere uma instância de `MapView` do fragmento de mapa usando o método `getMapAsync(OnMapReadyCallback)`. O `MapView` inicializa automaticamente o sistema de mapas e a exibição. Edite o arquivo **MainActivity.java** da seguinte maneira:

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
 
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

Quando você executa um aplicativo, o controle de mapa é carregado conforme mostrado na imagem a seguir.

![Google Maps simples](media/migrate-google-maps-android-app/simple-google-maps.png)

### <a name="after-azure-maps"></a>Depois: Azure Mapas

Para exibir um mapa usando o SDK do Azure Mapas para Android, as etapas a seguir precisam ser executadas:

1. Abra o arquivo de nível superior **build.gradle** e adicione o seguinte código à seção do bloco **todos os projetos**:

    ```java
    maven {
        url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Atualize o **app/build.gradle** e adicione a ele o seguinte código:

    1. Verifique se o **minSdkVersion** do seu projeto está na API 21 ou superior.

    2. Adicione o código a seguir à seção do Android:

        ```java
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```

    3. Atualize o bloco de dependências. Adicione uma nova linha de dependência de implementação ao SDK mais recente do Android dos Azure Mapas:

        ```Java
        implementation "com.microsoft.azure.maps:mapcontrol:0.6"
        ```

        > [!Note]
        > Você pode definir o número de versão para "0+" para que o código sempre aponte para a última versão.

    4. Vá para **Arquivo** na barra de ferramentas e clique em **Sincronizar Projeto com Arquivos Gradle**.

3. Adicione um fragmento de mapa à atividade principal (resources pwd\> layout \> activity\_main.xml):

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            />
    </FrameLayout>
    ```

4. No arquivo **MainActivity.java**, você precisará:

    * Importa o SDK dos Azure Mapas
    * Definir suas informações de autenticação dos Azure Mapas
    * Obter a instância de controle de mapa no método **onCreate**

     Definir as informações de autenticação na classe `AzureMaps` usando os métodos `setSubscriptionKey` ou `setAadProperties`. Para essa atualização global, lembre-se de adicionar suas informações de autenticação a cada exibição.

    O controle de mapa contém os próprios métodos de ciclo de vida para gerenciar o ciclo de vida do OpenGL do Android. Esses métodos precisam ser chamados diretamente na atividade independente. Para chamar corretamente os métodos de ciclo de vida do controle de mapa, é necessário substituir os métodos de ciclo de vida a seguir na atividade que contém o controle de mapa. Chame o respectivo método de controle de mapa.

    * `onCreate(Bundle)`
    * `onStart()`
    * `onResume()`
    * `onPause()`
    * `onStop()`
    * `onDestroy()`
    * `onSaveInstanceState(Bundle)`
    * `onLowMemory()`

    Edite o arquivo **MainActivity.java** da seguinte maneira:

    ```Java
    package com.example.myapplication;
    
    //For older versions use: import android.support.v7.app.AppCompatActivity; 
    import androidx.appcompat.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    
    public class MainActivity extends AppCompatActivity {
    
    static {
        AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");

        //Alternatively use Azure Active Directory authenticate.
        //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
    }

    MapControl mapControl;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mapControl = findViewById(R.id.mapcontrol);

        mapControl.onCreate(savedInstanceState);

        //Wait until the map resources are ready.
        mapControl.onReady(map -> {
            //Add your post map load code here.

        });
    }

    @Override
    public void onResume() {
        super.onResume();
        mapControl.onResume();
    }

    @Override
    protected void onStart(){
        super.onStart();
        mapControl.onStart();
    }

    @Override
    public void onPause() {
        super.onPause();
        mapControl.onPause();
    }

    @Override
    public void onStop() {
        super.onStop();
        mapControl.onStop();
    }

    @Override
    public void onLowMemory() {
        super.onLowMemory();
        mapControl.onLowMemory();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        mapControl.onDestroy();
    }

    @Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        mapControl.onSaveInstanceState(outState);
    }}
    ```

Se você executar seu aplicativo, o controle de mapa será carregado conforme mostrado na imagem a seguir.

![Tutorial simples para Azure Mapas](media/migrate-google-maps-android-app/simple-azure-maps.png)

Observe que o controle do Azure Mapas dá suporte a uma maior redução de zoom e fornece uma melhor exibição de todo o mundo.

> [!TIP]
> Se você estiver usando um emulador do Android em um computador Windows, o mapa poderá não ser renderizado devido a conflitos com o OpenGL e a renderização de gráficos acelerada por software. O procedimento a seguir funcionou para algumas pessoas resolverem esse problema. Abra o Gerenciador de AVD e selecione o dispositivo virtual a ser editado. Role a página para baixo no painel **Verificar Configuração**. Na seção **Desempenho Emulado**, defina a opção **Gráficos** para **Hardware**.

## <a name="localizing-the-map"></a>Como localizar o mapa

A localização é importante quando o público-alvo está espalhado em vários países/regiões ou fala idiomas diferentes.

### <a name="before-google-maps"></a>Antes: Google Maps

Adicione o código a seguir ao método `onCreate` para definir o idioma do mapa. O código precisa ser adicionado antes da configuração da exibição de contexto do mapa. O código de idioma "fr" limita o idioma ao francês.

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

Aqui está um exemplo do Google Maps com o idioma definido como "fr".

![Localização do Google Maps](media/migrate-google-maps-android-app/google-maps-localization.png)

### <a name="after-azure-maps"></a>Depois: Azure Mapas

Os Azure Mapas fornecem três maneiras diferentes de definir o idioma e a exibição regional do mapa. A primeira opção é transmitir as informações do idioma e da exibição regional para a classe `AzureMaps`. Essa opção usa os métodos estáticos `setLanguage` e `setView` globalmente. Isso significa que o idioma padrão e a exibição regional são definidos em todos os controles dos Azure Mapas carregados em seu aplicativo. Este exemplo define o francês usando o código de idioma "fr-FR".

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("Auto");
}
```

A segunda opção é transmitir as informações de idioma e de exibição para o código XML do controle de mapa.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="Auto"
    />
```

A terceira opção é programar o idioma e a exibição de mapa regional usando o método `setStyle` dos mapas. Essa opção atualiza o idioma e a exibição regional toda vez que o código é executado.

```java
mapControl.onReady(map -> {
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    );
});
```

Aqui está um exemplo do Azure Mapas com o idioma definido como "fr-FR".

![Localização do Azure Mapas](media/migrate-google-maps-android-app/azure-maps-localization.png)

Examine a lista completa de [Idiomas com suporte](supported-languages.md).

## <a name="setting-the-map-view"></a>Como configurar a exibição do mapa

Os mapas dinâmicos nos Azure Mapas e no Google Maps podem ser movidos de maneira programática para novas localizações geográficas chamando os métodos apropriados. Vamos fazer com que o mapa exiba uma imagem aérea de satélite, centralize o mapa sobre uma localização com as coordenadas e altere o nível de zoom. Para este exemplo, usaremos a latitude: 35,0272, a longitude: -111,0225 e o nível de zoom 15.

### <a name="before-google-maps"></a>Antes: Google Maps

A câmera do controle de mapa do Google Maps pode ser movida de maneira programática com o método `moveCamera`. O método `moveCamera` permite que você especifique o centro do mapa e um nível de zoom. O método `setMapType` altera o tipo de mapa a ser exibido.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

![Exibição de conjunto do Google Maps](media/migrate-google-maps-android-app/google-maps-set-view.png)

> [!NOTE]
> O Google Maps usa blocos de 256 pixels, enquanto o Azure Mapas usa um tamanho de peça maior, de 512 pixels. Isso reduz o número de solicitações de rede necessárias para que o Azure Mapas carregue a mesma área do mapa que o Google Maps. Para obter essa mesma área visível como um mapa no Google Maps, você precisará subtrair o nível de zoom usado no Google Maps em um ao usar os Azure Mapas.

### <a name="after-azure-maps"></a>Depois: Azure Mapas

Conforme observado anteriormente, para obter a mesma área visível nos Azure Mapas, subtraia o nível de zoom usado no Google Maps em um. Nesse caso, use um nível de zoom igual a 14.

A exibição de mapa inicial pode ser definida em atributos XML no controle de mapeamento.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

A exibição de mapa pode ser programada com os métodos `setCamera` e `setStyle` dos mapas.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-111.0225, 35.0272)), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

![Definição de exibição do Azure Mapas](media/migrate-google-maps-android-app/azure-maps-set-view.png)

**Recursos adicionais:**

* [Estilos de mapa compatíveis](supported-map-styles.md)

## <a name="adding-a-marker"></a>Adicionar um marcador

Os dados de ponto geralmente são renderizados com uma imagem no mapa. Essas imagens são chamadas de marcadores, pinos ou símbolos. Os seguintes exemplos renderizam os dados de ponto como marcadores no mapa na latitude: 51,5, longitude: -0,2.

### <a name="before-google-maps"></a>Antes: Google Maps

Com o Google Maps, os marcadores são adicionados usando o método `addMarker` dos mapas.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

![Marcador do Google Maps](media/migrate-google-maps-android-app/google-maps-marker.png)

### <a name="after-azure-maps"></a>Depois: Azure Mapas

Nos Azure Mapas, renderize os dados de ponto no mapa adicionando primeiro os dados a uma fonte de dados. Em seguida, conectando essa fonte de dados a uma camada de símbolo. A fonte de dados otimiza o gerenciamento de dados espaciais no controle de mapeamento. A camada de símbolo especifica como renderizar os dados de ponto usando-os como uma imagem ou um texto.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

![Marcador do Azure Mapas](media/migrate-google-maps-android-app/azure-maps-marker.png)

## <a name="adding-a-custom-marker"></a>Adicionar um marcador personalizado

Imagens personalizadas podem ser usadas para representar pontos em um mapa. O mapa nos exemplos abaixo usa uma imagem personalizada para exibir um ponto no mapa. O ponto está na latitude: 51,5 e na longitude: -0,2. A âncora desloca a posição do marcador, de modo que o ponto do ícone de pino seja alinhado com a posição correta no mapa.

![imagem do pino amarelo](media/migrate-google-maps-web-app/yellow-pushpin.png)<br/>
yellow-pushpin.png

Em ambos os exemplos, a imagem acima é adicionada à pasta desenhável dos recursos de aplicativos.

### <a name="before-google-maps"></a>Antes: Google Maps

Com o Google Maps, imagens personalizadas podem ser usadas para marcadores. Carregue imagens personalizadas usando a opção `icon` do marcador. Para alinhar o ponto da imagem com a coordenada, use a opção `anchor`. A âncora é relativa às dimensões da imagem. Nesse caso, a âncora tem 0,2 unidade de largura e 1 unidade de altura.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.yellow-pushpin))
    .anchor(0.2f, 1f));
}
```

![Marcador personalizado do Google Maps](media/migrate-google-maps-android-app/google-maps-custom-marker.png)

### <a name="after-azure-maps"></a>Depois: Azure Mapas

As camadas de símbolo nos Azure Mapas dão suporte a imagens personalizadas, mas primeiro a imagem precisa ser carregada nos recursos de mapa e receber uma ID exclusiva. Em seguida, a camada de símbolo precisa referenciar essa ID. Desloque o símbolo para alinhá-lo com o ponto correto na imagem usando a opção `iconOffset`. O deslocamento do ícone é mostrado em pixels. Por padrão, o deslocamento é relativo ao centro-inferior da imagem, mas esse valor de deslocamento pode ser ajustado usando a opção `iconAnchor`. Este exemplo define a opção `iconAnchor` como `"center"`. Ele usa um deslocamento de ícone para mover a imagem em cinco pixels para a direita e 15 pixels para cima, a fim de alinhá-la com o ponto da imagem do pino.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.yellow_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

![Marcador personalizado do Azure Mapas](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)

## <a name="adding-a-polyline"></a>Como adicionar uma polilinha

As polilinhas são usadas para representar uma linha ou um caminho no mapa. Os exemplos a seguir mostram como criar uma polilinha tracejada no mapa.

### <a name="before-google-maps"></a>Antes: Google Maps

Com o Google Maps, renderize uma polilinha usando a classe `PolylineOptions`. Adicione a polilinha ao mapa usando o método `addPolyline`. Defina a cor do traço usando a opção `color`. Defina a largura do traço usando a opção `width`. Adicione uma matriz de traço usando a opção `pattern`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

![Polilinha do Google Maps](media/migrate-google-maps-android-app/google-maps-polyline.png)

### <a name="after-azure-maps"></a>Depois: Azure Mapas

Nos Azure Mapas, as polilinhas são chamadas de objetos `LineString` ou `MultiLineString`. Adicione esses objetos a uma fonte de dados e renderize-os usando uma camada de linhas. Defina a largura do traço usando a opção `strokeWidth`. Adicione uma matriz de traço usando a opção `strokeDashArray`.

As unidades de "pixel" da matriz de traço e de largura do traço no SDK da Web dos Azure Mapas são as mesmas usadas no serviço Google Maps. Ambos aceitam os mesmos valores para produzir os mesmos resultados.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

![Polilinha do Azure Mapas](media/migrate-google-maps-android-app/azure-maps-polyline.png)

## <a name="adding-a-polygon"></a>Como adicionar um polígono

Os polígonos são usados para representar uma área no mapa. Os exemplos a seguir mostram como criar um polígono. Esse polígono forma um triângulo com base na coordenada central do mapa.

### <a name="before-google-maps"></a>Antes: Google Maps

Com o Google Maps, renderize um polígono usando a classe `PolygonOptions`. Adicione o polígono ao mapa usando o método `addPolygon`. Defina as cores de preenchimento e de traço usando as opções `fillColor` e `strokeColor`, respectivamente. Defina a largura do traço usando a opção `strokeWidth`.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

![Polígono do Google Maps](media/migrate-google-maps-android-app/google-maps-polygon.png)

### <a name="after-azure-maps"></a>Depois: Azure Mapas

Nos Azure Mapas, adicione objetos `Polygon` e `MultiPolygon` a uma fonte de dados e renderize-os no mapa usando camadas. Renderize a área de um polígono em uma camada de polígonos. Renderize o contorno de um polígono usando uma camada de linhas. Defina a cor e a largura do traço usando as opções `strokeColor` e `strokeWidth`.

As unidades de "pixel" da matriz de traço e da largura do traço no SDK da Web dos Azure Mapas se alinham com as respectivas unidades do Google Maps. Ambos aceitam os mesmos valores e produzem os mesmos resultados.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

![Polígono do Azure Mapas](media/migrate-google-maps-android-app/azure-maps-polygon.png)

## <a name="overlay-a-tile-layer"></a>Sobrepor uma camada de peça

 Use camadas de peças para sobrepor as imagens de camada que foram divididas em imagens com peças menores, que se alinham com o sistema de agrupamento lado a lado dos mapas. Essa abordagem é uma maneira comum de sobrepor imagens de camada ou conjuntos de dados grandes. As camadas de peças são conhecidas como sobreposições de imagens no Google Maps.

Os exemplos a seguir sobrepõem uma camada de peça de radar meteorológico da Iowa Environmental Mesonet of Iowa State University. As peças têm tamanho de 256 pixels.

### <a name="before-google-maps"></a>Antes: Google Maps

Com o Google Maps, uma camada de peça pode ser sobreposta no mapa. Use a classe `TileOverlayOptions`. Adicione a camada de peça ao mapa usando o método `addTileLauer`. Para tornar as peças semitransparentes, a opção `transparency` é definida como 0,2 ou 20% transparente.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

![Camada de peças do Google Maps](media/migrate-google-maps-android-app/google-maps-tile-layer.png)

### <a name="after-azure-maps"></a>Depois: Azure Mapas

Uma camada de peça pode ser adicionada ao mapa de maneira semelhante a qualquer outra camada. Uma URL formatada que tem os espaços reservados x, y e de zoom (`{x}`, `{y}` e `{z}`, respectivamente) é usada para informar à camada onde acessar as peças. Além disso, as camadas de peças nos Azure Mapas dão suporte aos espaços reservados `{quadkey}`, `{bbox-epsg-3857}` e `{subdomain}`. Para tornar a camada de peça semitransparente, é usado um valor de opacidade de 0,8. A opacidade e a transparência, embora semelhantes, usam valores invertidos. Para fazer a conversão entre as duas opções, subtraia o valor do número um.

> [!TIP]
> Nos Azure Mapas, é conveniente renderizar camadas abaixo de outras camadas, incluindo camadas do mapa base. Além disso, geralmente, é desejável renderizar camadas de peça abaixo dos rótulos de mapa para que elas sejam fáceis de serem lidas. O método `map.layers.add` usa um segundo parâmetro que é a ID da camada na qual a nova camada abaixo será inserida. Para inserir uma camada de peça abaixo dos rótulos de mapa, o seguinte código pode ser usado: `map.layers.add(myTileLayer, "labels");`

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

![Camada de peça do Azure Mapas](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)

## <a name="show-traffic"></a>Mostrar o tráfego

Os Azure Mapas e o Google Maps têm opções para sobrepor os dados de tráfego.

### <a name="before-google-maps"></a>Antes: Google Maps

Com o Google Maps, os dados de fluxo de tráfego podem ser sobrepostos no mapa, com a transmissão de verdadeiro para o método `setTrafficEnabled` do mapa.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

![Tráfego do Google Maps](media/migrate-google-maps-android-app/google-maps-traffic.png)

### <a name="after-azure-maps"></a>Depois: Azure Mapas

O Azure Mapas fornece várias opções diferentes para exibir o tráfego. Incidentes de tráfego, como bloqueios e acidentes na estrada, podem ser exibidos como ícones no mapa. O fluxo de tráfego e as estradas codificadas por cores podem ser sobrepostas no mapa. As cores podem ser modificadas para que apareçam em relação ao limite de velocidade postado, ao atraso normal esperado ou ao atraso absoluto. Os dados de incidentes nos Azure Mapas são atualizados a cada minuto; os dados de fluxo são atualizados a cada dois minutos.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

![Tráfego do Azure Mapas](media/migrate-google-maps-android-app/azure-maps-traffic.png)

## <a name="clean-up-resources"></a>Limpar recursos

Não há recursos a serem limpos.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a migração para o Azure Mapas:

> [!div class="nextstepaction"]
> [Migrar um aplicativo Android](migrate-from-google-maps-android-app.md)
