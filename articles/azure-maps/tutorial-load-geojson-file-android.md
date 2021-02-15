---
title: 'Tutorial: Carregar dados GeoJSON no SDK do Android do Azure Mapas | Microsoft Azure Mapas'
description: Tutorial sobre como carregar o arquivo de dados GeoJSON no SDK do mapa do Android do Azure Mapas.
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b527cd7b3f841b6cb3dcf2dce6930f3bd9bcc184
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681228"
---
# <a name="tutorial-load-geojson-data-into-azure-maps-android-sdk"></a>Tutorial: Carregar dados GeoJSON no SDK do Android do Azure Mapas

Este tutorial orienta você pelo processo de importação de um arquivo GeoJSON de dados de localização para o SDK do Android do Azure Mapas. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar o Azure Mapas a um aplicativo Android.
> * Criar uma fonte de dados e carregá-la em um arquivo GeoJSON de um arquivo local ou da Web.
> * Exibir os dados do mapa.

## <a name="prerequisites"></a>Pré-requisitos

1. Conclua o [Início Rápido: Criar um aplicativo Android](quick-android-map.md). Este tutorial estenderá o código usado naquele guia de início rápido.
2. Baixe o arquivo GeoJSON [Exemplos de Pontos de Interesse](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json).

### <a name="import-geojson-data-from-web-or-assets-folder"></a>Importar dados GeoJSON da pasta de ativos ou da Web

A maioria dos arquivos GeoJSON encapsula todos os dados em uma `FeatureCollection`. Com isso em mente, se os arquivos GeoJSON forem carregados no aplicativo como uma cadeia de caracteres, eles poderão ser transmitidos para o método `fromJson` estático da coleção de recursos, que desserializará a cadeia de caracteres em um objeto GeoJSON `FeatureCollection` que poderá ser adicionado ao mapa.

As etapas a seguir mostram como importar um arquivo GeoJSON para o aplicativo e desserializá-lo como um objeto `FeatureCollection` GeoJSON.

1. Conclua o [Início Rápido: Criar um aplicativo Android](quick-android-map.md), pois as etapas a seguir se baseiam nesse aplicativo.
2. No painel do projeto do Android Studio, clique com o botão direito do mouse na pasta **app** e acesse `New > Folder > Assets Folder`.
3. Arraste e solte o arquivo GeoJSON [Exemplo de Pontos de Interesse](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) na pasta ativos.
4. Crie um arquivo chamado **Utils.java** e adicione o código a seguir a esse arquivo. Esse código fornece um método estático chamado `importData` que importa de maneira assíncrona um arquivo da pasta `assets` do aplicativo ou da Web usando uma URL como uma cadeia de caracteres e a retorna para o thread da IU usando um método de retorno de chamada simples.

    ```java
    //Modify the package name as needed to align with your application.
    package com.example.myapplication;
    
    import android.content.Context;
    import android.os.Handler;
    import android.os.Looper;
    import android.webkit.URLUtil;
    
    import java.io.BufferedReader;
    import java.io.IOException;
    import java.io.InputStream;
    import java.io.InputStreamReader;
    import java.net.URL;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    
    import javax.net.ssl.HttpsURLConnection;
    
    public class Utils {
    
        interface SimpleCallback {
            void notify(String result);
        }
    
        /**
         * Imports data from a web url or asset file name and returns it to a callback.
         * @param urlOrFileName A web url or asset file name that points to data to load.
         * @param context The context of the app.
         * @param callback The callback function to return the data to.
         */
        public static void importData(String urlOrFileName, Context context, SimpleCallback callback){
            importData(urlOrFileName, context, callback, null);
        }
        
        /**
         * Imports data from a web url or asset file name and returns it to a callback.
         * @param urlOrFileName A web url or asset file name that points to data to load.
         * @param context The context of the app.
         * @param callback The callback function to return the data to.
         * @param error A callback function to return errors to.
         */
        public static void importData(String urlOrFileName, Context context, SimpleCallback callback, SimpleCallback error){
            if(urlOrFileName != null && callback != null) {
                ExecutorService executor = Executors.newSingleThreadExecutor();
                Handler handler = new Handler(Looper.getMainLooper());
    
                executor.execute(() -> {
                    String data = null;
    
                    try {
    
                        if(URLUtil.isNetworkUrl(urlOrFileName)){
                            data = importFromWeb(urlOrFileName);
                        } else {
                            //Assume file is in assets folder.
                            data = importFromAssets(context, urlOrFileName);
                        }
    
                        final String result = data;
    
                        handler.post(() -> {
                            //Ensure the resulting data string is not null or empty.
                            if (result != null && !result.isEmpty()) {
                                callback.notify(result);
                            } else {
                                error.notify("No data imported.");
                            }
                        });
                    } catch(Exception e) {
                        if(error != null){
                            error.notify(e.getMessage());
                        }
                    }
                });
            }
        }
    
        /**
         * Imports data from an assets file as a string.
         * @param context The context of the app.
         * @param fileName The asset file name.
         * @return
         * @throws IOException
         */
        private static String importFromAssets(Context context, String fileName) throws IOException {
            InputStream stream = null;
    
            try {
                stream = context.getAssets().open(fileName);
    
                if(stream != null) {
                    return readStreamAsString(stream);
                }
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                // Close Stream and disconnect HTTPS connection.
                if (stream != null) {
                    stream.close();
                }
            }
    
            return null;
        }
    
        /**
         * Imports data from the web as a string.
         * @param url URL to the data.
         * @return
         * @throws IOException
         */
        private static String importFromWeb(String url) throws IOException {
            InputStream stream = null;
            HttpsURLConnection connection = null;
            String result = null;
    
            try {
                connection = (HttpsURLConnection) new URL(url).openConnection();
    
                //For this use case, set HTTP method to GET.
                connection.setRequestMethod("GET");
    
                //Open communications link (network traffic occurs here).
                connection.connect();
    
                int responseCode = connection.getResponseCode();
                if (responseCode != HttpsURLConnection.HTTP_OK) {
                    throw new IOException("HTTP error code: " + responseCode);
                }
    
                //Retrieve the response body as an InputStream.
                stream = connection.getInputStream();
    
                if (stream != null) {
                    return readStreamAsString(stream);
                }
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                // Close Stream and disconnect HTTPS connection.
                if (stream != null) {
                    stream.close();
                }
                if (connection != null) {
                    connection.disconnect();
                }
            }
    
            return result;
        }
    
        /**
         * Reads an input stream as a string.
         * @param stream Stream to convert.
         * @return
         * @throws IOException
         */
        private static String readStreamAsString(InputStream stream) throws IOException {
            //Convert the contents of an InputStream to a String.
            BufferedReader in = new BufferedReader(new InputStreamReader(stream, "UTF-8"));
    
            String inputLine;
            StringBuffer response = new StringBuffer();
    
            while ((inputLine = in.readLine()) != null) {
                response.append(inputLine);
            }
    
            in.close();
    
            return response.toString();
        }
    }
    ```

5. Acesse o arquivo **MainActivity.java** e adicione o código a seguir dentro do retorno de chamada para o evento `mapControl.onReady`, localizado dentro do método `onCreate`. Esse código usa o utilitário de importação para ler o arquivo **SamplePoiDataSet.json** como uma cadeia de caracteres e desserializa-o como uma coleção de recursos usando o método `fromJson` estático da classe `FeatureCollection`. Esse código também calcula a área da caixa delimitadora para todos os dados da coleção de recursos e usa isso para definir a câmera do mapa com o foco nos dados.

    ```java
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);
    
    //Import the GeoJSON data and add it to the data source.
    Utils.importData("SamplePoiDataSet.json",
        this,
        (String result) -> {
            //Parse the data as a GeoJSON Feature Collection.
            FeatureCollection fc = FeatureCollection.fromJson(result);
    
            //Add the feature collection to the data source.
            source.add(fc);
    
            //Optionally, update the maps camera to focus in on the data.
    
            //Calculate the bounding box of all the data in the Feature Collection.
            BoundingBox bbox = MapMath.fromData(fc);
    
            //Update the maps camera so it is focused on the data.
            map.setCamera(
                bounds(bbox),

                //Padding added to account for pixel size of rendered points.
                padding(20)
            );
        });
    ```

6. Agora que há um código para carregar os dados GeoJSON no mapa usando uma fonte de dados, precisamos especificar como esses dados devem ser exibidos no mapa. Há várias camadas de renderização diferentes para dados de ponto: [Camada de bolha](map-add-bubble-layer-android.md), [Camada de símbolo](how-to-add-symbol-to-android-map.md) e [Camada do mapa de calor](map-add-heat-map-layer-android.md) são as usadas com mais frequência. Adicione o código a seguir para renderizar os dados em uma camada de bolha no retorno de chamada para o evento `mapControl.onReady` depois do código para importar os dados.

    ```java
    //Create a layer and add it to the map.
    BubbleLayer layer = new BubbleLayer(source);
    map.layers.add(layer);
    ```

7. Execute o aplicativo. Um mapa será exibido com foco nos EUA, com círculos sobrepostos para cada localização no arquivo GeoJSON.

    ![Mapa dos EUA com os dados de um arquivo GeoJSON exibidos](media/tutorial-load-geojson-file-android/android-import-geojson.png)


## <a name="clean-up-resources"></a>Limpar os recursos

Siga as seguintes etapas para limpar os recursos deste tutorial:

1. Feche o Android Studio e exclua o aplicativo que você criou.
2. Se você testou o aplicativo em um dispositivo externo, desinstale-o desse dispositivo.

## <a name="next-steps"></a>Próximas etapas

Para ver mais exemplos de código e uma experiência interativa de codificação:

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Exibir informações do recurso](display-feature-information-android.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linha](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](how-to-add-shapes-to-android-map.md)
