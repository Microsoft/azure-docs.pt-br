---
title: 'Tutorial: Pesquisar localizações próximas em um mapa | Microsoft Azure Mapas'
description: Tutorial sobre como pesquisar pontos de interesse em um mapa. Veja como usar o SDK da Web dos Azure Mapas para adicionar funcionalidades de pesquisa e caixas de pop-up interativas a um mapa.
author: anastasia-ms
ms.author: v-stharr
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 31dd1c06b0f17b469454593131ccdc93b45b2446
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624960"
---
# <a name="tutorial-search-nearby-points-of-interest-using-azure-maps"></a>Tutorial: Pesquisar pontos de interesse próximos usando os Mapas do Azure

Este tutorial mostra como configurar uma conta dos Mapas do Azure e depois usar as APIs de Mapas para procurar um ponto de interesse. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma conta dos Mapas do Azure
> * Obter a chave primária de sua conta dos Mapas
> * Criar uma nova página da Web usando a API de controle de mapeamento
> * Usar o serviço de pesquisa dos Mapas para localizar um ponto de interesse próximo

## <a name="prerequisites"></a>Pré-requisitos

<a id="createaccount"></a>
<a id="getkey"></a>

1. Entre no [portal do Azure](https://portal.azure.com). Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
2. [Faça uma conta do Azure Mapas](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Obtenha uma chave de assinatura primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), também conhecida como a chave primária ou a chave de assinatura. Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](how-to-manage-authentication.md).

<a id="createmap"></a>

## <a name="create-a-new-map"></a>Criar um novo mapa

A API de Controle de Mapeamento é uma biblioteca de clientes conveniente. Essa API permite que você integre com facilidade os Mapas ao seu aplicativo Web. Ela oculta a complexidade das chamadas básicas de serviço REST e aumenta a produtividade com os componentes personalizáveis. As etapas a seguir mostra como criar uma página HTML estática inserida com a API do Controle de Mapeamento.

1. Em seu computador local, crie um novo arquivo e nomeie-o como **MapSearch.html**.
2. Adicione os seguintes componentes HTML ao arquivo:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Search</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
        function GetMap(){
            //Add Map Control JavaScript code here.
        }
        </script>

        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

   Observe que o cabeçalho HTML inclui os arquivos de recurso CSS e JavaScript hospedados pela biblioteca de Controle de mapa do Azure. Observe o evento `onload` no corpo da página que chamará a função `GetMap` quando o corpo da página for carregado. Essa `GetMap` função conterá o código JavaScript embutido para acessar as APIs do Azure Mapas.

3. Adicione o seguinte código JavaScript à função `GetMap` do arquivo HTML. Substitua a cadeia de caracteres `<Your Azure Maps Key>` pela chave primária que você copiou da sua conta dos Mapas.

    ```JavaScript
    //Instantiate a map object
    var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
            authType: 'subscriptionKey',
            subscriptionKey: '<Your Azure Maps Key>'
        }
    });
    ```

   Este segmento inicializa a API de Controle de Mapeamento da sua chave de conta do Azure Mapas. `atlas` é o namespace que contém a API e os componentes visuais relacionados. `atlas.Map` fornece o controle para um mapa visual e interativo na Web.

4. Salve suas alterações no arquivo e abra a página HTML em um navegador. O mapa mostrado é o mais básico que você pode fazer chamando `atlas.Map` e usando a sua chave de conta.

   ![Exibir o mapa](./media/tutorial-search-location/basic-map.png)

5. Na função `GetMap`, depois de inicializar o mapa, adicione o código JavaScript a seguir.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering point data.
        var resultLayer = new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: 'pin-round-darkblue',
                anchor: 'center',
                allowOverlap: true
            },
            textOptions: {
                anchor: "top"
            }
        });

        map.layers.add(resultLayer);
    });
    ```

   Neste segmento de código, um evento `ready` é adicionado ao mapa, que será disparado quando os recursos de mapa forem carregados e o mapa estiver pronto para ser acessado. No manipulador de eventos `ready` do mapa, uma fonte de dados é criada para armazenar os dados de resultado. Uma camada de símbolo é criada e anexada à fonte de dados. Essa camada especifica como os dados de resultados da fonte de dados devem ser renderizados. Nesse caso, o resultado é renderizado com um ícone de marcador redondo azul-escuro, centralizado sobre a coordenada de resultados e permite que outros ícones sejam sobrepostos. A camada de resultado é adicionada às camadas do mapa.

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Adicionar recursos de pesquisa

Esta seção mostra como usar a [API de Pesquisa](/rest/api/maps/search) dos Mapas do Azure para encontrar um ponto de interesse em seu mapa. Trata-se de uma API RESTful projetada para desenvolvedores para pesquisa de endereços, pontos de interesse e outras informações geográficas. O serviço de Pesquisa atribui informações de latitude e longitude a um endereço especificado. O **Módulo de Serviço** explicado a seguir pode ser usado para procurar um local usando a API de Pesquisa de Mapas.

### <a name="service-module"></a>Módulo de serviço

1. No manipulador de eventos `ready` do mapa, construa a URL do serviço de pesquisa adicionando o código JavaScript a seguir.

    ```JavaScript
   // Use SubscriptionKeyCredential with a subscription key
   var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

   // Use subscriptionKeyCredential to create a pipeline
   var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

   // Construct the SearchURL object
   var searchURL = new atlas.service.SearchURL(pipeline); 
   ```

   O `SubscriptionKeyCredential` cria um `SubscriptionKeyCredentialPolicy` para autenticar solicitações HTTP para Azure Mapas com a chave de assinatura. O `atlas.service.MapsURL.newPipeline()` usa a `SubscriptionKeyCredential` política e cria uma instância de [Pipeline](/javascript/api/azure-maps-rest/atlas.service.pipeline). O `searchURL` representa uma URL para as operações de [pesquisa](/rest/api/maps/search) do Azure Mapas.

2. Adicione o bloco de script a seguir para criar a consulta de pesquisa. Ele usa o Serviço de Pesquisa Difusa, que é uma API de pesquisa básica do Serviço de Pesquisa. O Serviço de Pesquisa Difusa trata a maioria das entradas difusas, como endereços, locais e POI (pontos de interesse). Esse código pesquisa postos de gasolina próximos em um raio especificado das coordenadas de latitude e longitude fornecidas. A coleção do recurso GeoJSON da resposta é então extraída usando o método `geojson.getFeatures()` e adicionado à fonte de dados, que automaticamente faz com que os dados sejam renderizados no mapa através da camada do símbolo. A última parte do script define a exibição de câmera dos mapas usando a caixa delimitadora dos resultados com a propriedade [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) do mapa.

    ```JavaScript
    var query =  'gasoline-station';
    var radius = 9000;
    var lat = 47.64452336193245;
    var lon = -122.13687658309935;

    searchURL.searchPOI(atlas.service.Aborter.timeout(10000), query, {
        limit: 10,
        lat: lat,
        lon: lon,
        radius: radius
    }).then((results) => {

        // Extract GeoJSON feature collection from the response and add it to the datasource
        var data = results.geojson.getFeatures();
        datasource.add(data);

        // set camera to bounds to show the results
        map.setCamera({
            bounds: data.bbox,
            zoom: 10
        });
    });
    ```

3. Salve o arquivo **MapSearch.html** e atualize seu navegador. Você deverá ver o mapa centralizado em Seattle e marcadores redondos azuis nas localizações de postos de gasolina da região.

   ![Exibir o mapa com os resultados da pesquisa](./media/tutorial-search-location/pins-map.png)

4. Você pode ver os dados brutos que o mapa estiver renderizando ao digitar o seguinte HTTPRequest em seu navegador. Substitua \<Your Azure Maps Key\> pela chave primária.

   ```http
   https://atlas.microsoft.com/search/poi/json?api-version=1.0&query=gasoline%20station&subscription-key=<subscription-key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

Neste momento, a página MapSearch pode exibir os locais de pontos de interesse que são retornados de uma consulta de pesquisa difusa. Vamos adicionar alguns recursos interativos e mais informações sobre os locais.

## <a name="add-interactive-data"></a>Adicionar dados interativos

O mapa que fizemos até agora apenas analisa os dados de longitude/latitude para os resultados da pesquisa. No entanto, o JSON bruto retornado pelo serviço Pesquisa dos Mapas contém informações adicionais sobre cada posto de gasolina. Incluindo o nome e o endereço. Você pode incorporar esses dados ao mapa com caixas pop-up interativas.

1. Adicione as linhas de código a seguir no manipulador de `ready` eventos do mapa após o código para consultar o serviço de pesquisa difusa. Esse código criará uma instância de um pop-up e adicionará um evento de passar o mouse à camada de símbolos.

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```

    A API `*atlas.Popup` fornece uma janela de informações ancorada na posição requerida no mapa. 

2. Adicione o código a seguir dentro da função `GetMap` para mostrar as informações do resultado no pop-up depois de passar o mouse.

    ```JavaScript
    function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occurred on.

        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = `
          <div style="padding:5px">
            <div><b>${p.poi.name}</b></div>
            <div>${p.address.freeformAddress}</div>
            <div>${position[1]}, ${position[0]}</div>
          </div>`;

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html,
            position: position
        });

        //Open the popup.
        popup.open(map);
    }
    ```

3. Salve o arquivo e atualize seu navegador. Agora, o mapa no navegador mostra pop-ups de informações quando você passa o mouse sobre qualquer marcação de pesquisa.

    ![Controle de mapa do Azure e Serviço de Pesquisa](./media/tutorial-search-location/popup-map.png)

Para ver o código completo deste tutorial, clique [aqui](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html). Para ver a amostra ao vivo, clique [aqui](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)

## <a name="clean-up-resources"></a>Limpar os recursos

Não há recursos que exijam limpeza.

## <a name="next-steps"></a>Próximas etapas

O seguinte tutorial demonstra como exibir uma rota entre dois locais.

> [!div class="nextstepaction"]
> [Rotear para um destino](./tutorial-route-location.md)