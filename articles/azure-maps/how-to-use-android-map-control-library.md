---
title: Como usar o controle de mapa Android em mapas do Azure | Microsoft Docs
description: O controle de mapa Android em mapas do Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 15706addbe6b7f6310223978130158c792a47c89
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770293"
---
# <a name="how-to-use-the-azure-maps-android-sdk"></a>Como usar o SDK de Android do mapas do Azure

O SDK de Android do mapas do Azure é uma biblioteca de mapa de vetor para Android. Este artigo o orienta os processos de instalar o SDK de Android do mapas do Azure, carregando um mapa e colocando um pin no mapa.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="create-an-azure-maps-account"></a>Criar uma conta dos Mapas do Azure

Para concluir os procedimentos neste artigo, primeiro você precisa [criar uma conta do Azure mapas](how-to-manage-account-keys.md) no tipo de preço S1.

### <a name="download-android-studio"></a>Baixar Android Studio

Você precisará baixar Android Studio e criar um projeto com uma atividade vazia antes de instalar o SDK de Android do mapas do Azure. Você pode [baixar Android Studio](https://developer.android.com/studio/) gratuitamente do Google. 

## <a name="create-a-project-in-android-studio"></a>Criar um projeto no Android Studio

Primeiro, você precisa criar um novo projeto com uma atividade vazia. Conclua estas etapas para criar um projeto do Android Studio:

1. Sob **escolha seu projeto**, selecione **telefone e Tablet**. O aplicativo será executado nesse fator forma.
2. Sobre o **telefone e Tablet** guia, selecione **atividade vazia**e, em seguida, selecione **próxima**.
3. Em **Configurar seu projeto**, selecione `API 21: Android 5.0.0 (Lollipop)` como o SDK mínimo. Isso é a versão mais antiga com suporte pelo SDK do Android mapas do Azure.
4. Aceite o padrão `Activity Name` e `Layout Name` e selecione **concluir**.

Consulte a [documentação do Android Studio](https://developer.android.com/studio/intro/) para obter mais ajuda com a instalação do Android Studio e criando um novo projeto.

![Criar um projeto](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Configurar um dispositivo virtual

O Android Studio permite configurar um dispositivo Android virtual no computador. Isso pode ajudá-lo a testar seu aplicativo durante o desenvolvimento. Para configurar um dispositivo virtual, selecione o ícone do Gerenciador de dispositivo Virtual Android (AVD) no canto superior direito da tela do projeto e, em seguida, selecione **criar dispositivo Virtual**. Você também pode obter o Gerenciador de AVD selecionando **ferramentas** > **Android** > **Gerenciador de AVD** da barra de ferramentas. No **telefones** categoria, selecione **Nexus 5x**e, em seguida, selecione **próxima**.

Você pode aprender mais sobre como configurar um AVD na [documentação do Android Studio](https://developer.android.com/studio/run/managing-avds).

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-the-azure-maps-android-sdk"></a>Instalar o SDK do Android de mapas do Azure

A próxima etapa na criação de seu aplicativo é instalar o SDK de Android do mapas do Azure. Conclua estas etapas para instalar o SDK:

1. Adicione o seguinte código para o **todos os projetos**, **repositórios** bloquear sua **Build. gradle** arquivo.

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Atualização de seu **gradle** e adicione o seguinte código nele:

    1. Adicione o seguinte código ao bloco Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. Atualize seu bloco de dependências e adicione o seguinte código nele:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. Definir permissões, adicionando o seguinte XML para seu **androidmanifest. XML** arquivo:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. Edite **res** > **layout** > **activity_main** para que ele se parece com este XML:
    
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
            app:mapcontrol_cameraTargetLat="47.64"
            app:mapcontrol_cameraTargetLng="-122.33"
            app:mapcontrol_cameraZoom="12"
            />

    </FrameLayout>
    ```

5. Edite **MainActivity.java** para criar uma classe de atividade de exibição de mapa. Depois de editar, deve se parecer com esta classe:

    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);

        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
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
        }

    }

    ```

## <a name="import-classes"></a>Importar classes

Depois de concluir as etapas anteriores, você provavelmente receberá avisos do Android Studio sobre alguns dos códigos. Para resolver esses avisos, importe as classes referenciadas em `MainActivity.java`.

Você pode importar automaticamente essas classes selecionando Alt + Enter (opção + Return em um Mac).

Selecione o botão de execução, conforme mostrado na seguinte gráfico (ou pressione controle + R em um Mac), para compilar seu aplicativo.

![Clique em Executar](./media/how-to-use-android-map-control-library/run-app.png)

Android Studio levará alguns segundos para compilar o aplicativo. Depois que a compilação estiver concluída, você pode testar seu aplicativo no dispositivo Android emulado. Você deverá ver um mapa como este:

![Mapa do Android](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>Adicionar um marcador ao mapa

Para adicionar um marcador para seu mapa, adicione a `mapView.getMapAsync()` função `MainActivity.java`. O último `MainActivity.java` código deve ter esta aparência:

```java
package com.example.myapplication;

import android.app.Activity;
import android.os.Bundle;
import com.mapbox.geojson.Feature;
import com.mapbox.geojson.Point;
import com.microsoft.azure.maps.mapcontrol.AzureMaps;
import com.microsoft.azure.maps.mapcontrol.MapControl;
import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
import com.microsoft.azure.maps.mapcontrol.source.DataSource;
import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
public class MainActivity extends AppCompatActivity {
    
    static{
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

    MapControl mapControl;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mapControl = findViewById(R.id.mapcontrol);

        mapControl.onCreate(savedInstanceState);

        mapControl.getMapAsync(map -> {
            DataSource dataSource = new DataSource();
            dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

            SymbolLayer symbolLayer = new SymbolLayer(dataSource);
            symbolLayer.setOptions(iconImage("my-icon"));

            map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            map.sources.add(dataSource);
            map.layers.add(symbolLayer);
        });
    }

    @Override
    public void onStart() {
        super.onStart();
        mapControl.onStart();
    }

    @Override
    public void onResume() {
        super.onResume();
        mapControl.onResume();
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
    }
}
```

Execute o aplicativo novamente. Você deverá ver um marcador no mapa, conforme mostrado aqui:

![Marcador de mapa do Android](./media/how-to-use-android-map-control-library/android-map-pin.png)