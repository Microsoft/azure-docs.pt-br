---
title: Introdução ao controle de mapa de Android do mapas do Azure | Microsoft Docs
description: O controle de mapa Android em mapas do Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 9df5eb9fa4493f82c6efd4a8e30eee324e4eac2a
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273840"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Introdução ao SDK do Android do Azure mapas

O SDK de Android do mapas do Azure é uma biblioteca de mapa de vetor para Android. Este artigo o orienta os processos de instalar o SDK de Android do mapas do Azure e carregar um mapa.

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

1. Abra o nível superior **Build. gradle** arquivo e adicione o seguinte código para o **todos os projetos**, **repositórios** bloquear seção:

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Atualização de seu **gradle** e adicione o seguinte código nele:
    
    1. Certifique-se de que seu projeto **minSdkVersion** é a API 21 ou mais.

    2. Adicione o seguinte código à seção Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. Atualize seu bloco de dependências e adicionar uma nova linha de dependência de implementação para o Android SDK mais recente do Azure mapas:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

    > [!Note]
    > O Azure mapas do SDK do Android está sendo regularmente atualizados e aprimorados. Você pode ver os [Introdução ao controle de mapa Android](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) documentação para obter o número de versão de implementação de mapas do Azure mais recente. Além disso, você pode definir o número de versão de "0,2" para "0 +" para que ele seja sempre apontar para a versão mais recente.

3. Edite **res** > **layout** > **activity_main** e substituí-lo com o seguinte:
    
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

4. No **mainactivity. Java** arquivo, você precisará:
    
    * Adicione as importações do SDK de mapas do Azure
    * definir as informações de autenticação de mapas do Azure
    * obter a instância de controle de mapa **onCreate** método

    Definindo as informações de autenticação na classe AzureMaps globalmente usando os métodos setSubscriptionKey ou setAadProperties torna assim você não terá que adicionar suas informações de autenticação em cada exibição. O controle de mapa contém seus próprios métodos de ciclo de vida de gerenciamento OpenGL ciclo de vida do Android, que deve ser chamado diretamente do que a atividade contentora. Para que o aplicativo corretamente, chamar métodos de ciclo de vida do controle de mapa, você deve substituir os seguintes métodos de ciclo de vida na atividade que contém o controle de mapa e chame o método de controle de mapa respectivo. 

    Editar o **mainactivity. Java** arquivos da seguinte maneira:
    
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
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
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

<center>

![Mapa do Android](./media/how-to-use-android-map-control-library/android-map.png)</center>

## <a name="next-steps"></a>Próximas etapas

Saiba como adicionar dados de sobreposição no mapa:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo para um mapa do Android](https://review.docs.microsoft.com/azure/azure-maps/how-to-add-symbol-to-android-map)

> [!div class="nextstepaction"]
> [Adicionar formas a um mapa de Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Estilos de mapa de alteração em mapas do Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)
