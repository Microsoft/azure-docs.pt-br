---
title: Encontrar rota com os Mapas do Azure | Microsoft Docs
description: Rotear para um ponto de interesse usando os Mapas do Azure
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: fd75ca1fbad358e80a2c040b5ead8c50611489e2
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478870"
---
# <a name="route-to-a-point-of-interest-using-azure-maps"></a>Rotear para um ponto de interesse usando os Mapas do Azure

Este tutorial mostra como usar sua conta dos Mapas do Azure e o SDK do Serviço de Roteiros para encontrar a rota para seu ponto de interesse. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma nova página da Web usando a API de controle de mapeamento
> * Definir coordenadas de endereço
> * Serviço de Roteiros de Consulta para obter o trajeto até o ponto de interesse

## <a name="prerequisites"></a>Pré-requisitos

Antes de prosseguir, siga as etapas no tutorial anterior para [criar sua conta dos Mapas do Azure](./tutorial-search-location.md#createaccount) e [obter a chave de assinatura da conta](./tutorial-search-location.md#getkey).

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>Criar um novo mapa

As etapas a seguir mostra como criar uma página HTML estática inserida com a API do Controle de Mapeamento.

1. Em seu computador local, crie um novo arquivo e nomeie-o como **MapRoute.html**.
2. Adicione os seguintes componentes HTML ao arquivo:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
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

    Observe que o cabeçalho HTML inclui os arquivos de recurso CSS e JavaScript hospedados pela biblioteca de Controle de mapa do Azure. Observe o evento `onload` no corpo da página que chamará a função `GetMap` quando o corpo da página for carregado. Essa função conterá o código JavaScript embutido para acessar as APIs do Azure Mapas. 

3. Adicione o código JavaScript a seguir à função `GetMap`. Substitua a cadeia de caracteres `<Your Azure Maps Key>` pela chave primária que você copiou da sua conta dos Mapas.

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

    O `atlas.Map` fornece o controle de um mapa Web visual e interativo, além de ser um componente da API de Controle de Mapeamento do Azure.

4. Salve o arquivo e abra-o em seu navegador. Agora você tem um mapa básico que você pode desenvolver ainda mais.

   ![Exibir mapa básico](media/tutorial-route-location/basic-map.png)

## <a name="define-how-the-route-will-be-rendered"></a>Definir como a rota será renderizada

Neste tutorial, uma rota simples será renderizada usando um ícone de símbolo para o início e o fim da rota e uma linha para o caminho da rota.

1. Após inicializar o mapa, adicione o código JavaScript a seguir.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: '#2272B9',
            strokeWidth: 5,
            lineJoin: 'round',
            lineCap: 'round'
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
           },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
        }));
    });
    ```
    
    No manipulador `ready` de eventos do mapa, uma fonte de dados é criada para armazenar a linha da rota e os pontos de partida e de chegada. Uma camada de linhas é criada e anexada à fonte de dados para definir como a linha de rota será renderizada. A linha da rota será renderizada em uma bela tonalidade de azul com uma largura de 5 pixels e limites e junções arredondadas. Quando você adiciona a camada ao mapa, um segundo parâmetro com o valor de `'labels'` é passado especificando que essa camada deve ser renderizada abaixo dos rótulos do mapa. Isso fará com que a linha de rota não cubra os rótulos de estrada. Uma camada de símbolo é criada e anexada à fonte de dados. Essa camada especifica como os pontos de partida e chegada serão renderizados; neste caso, expressões foram adicionadas para recuperar as informações de rótulo de texto e de imagem de ícone das propriedades em cada objeto de ponto. 
    
2. Neste tutorial, defina o ponto de partida como Microsoft e o ponto de chegada como um posto de gasolina em Seattle. No manipulador `ready` de eventos do mapa, adicione o código a seguir.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end points of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.130137, 47.644702]), {
        title: "Redmond",
        icon: "pin-blue"
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.3352, 47.61397]), {
        title: "Seattle",
        icon: "pin-round-blue"
    });

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 80
    });
    ```

    Esse código cria dois [objetos de Ponto GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) para representar os pontos de partida e chegada da rota e adiciona os pontos à fonte de dados. As propriedades `title` e `icon` são adicionadas a cada ponto. O último bloco define a exibição da câmera usando as informações de latitude e longitude dos pontos de partida e de chegada, usando a propriedade [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) do mapa.

3. Salve o arquivo **MapRoute.html** e atualize seu navegador. Agora o mapa está centralizado em Seattle e você pode ver o marcador azul indicando o ponto de partida e o marcador azul redondo indicando o ponto de chegada.

   ![Exibir o mapa com os pontos inicial e final marcados](media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Obter direções

Esta seção mostra como usar a API do serviço de roteiros do Azure Mapas para encontrar várias rotas com base em um determinado ponto de partida até um ponto de chegada. O serviço de roteiros fornece APIs para planejar as rotas *mais rápida*, *mais curta*, *econômica* ou *emocionante* entre dois locais. Também permite planejar rotas futuras usando o amplo banco de dados de histórico de tráfego do Azure e prevendo durações de rotas para cada dia e hora. Para saber mais, confira [Obter direções de rota](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Todas as funcionalidades a seguir devem ser adicionadas **dentro do eventListener do carregamento de mapa** para assegurar que elas sejam carregadas depois que os recursos do mapa estejam prontos para serem acessados.

1. Na função GetMap, adicione o código JavaScript a seguir.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   O `SubscriptionKeyCredential` cria um `SubscriptionKeyCredentialPolicy` para autenticar solicitações HTTP para Azure Mapas com a chave de assinatura. O `atlas.service.MapsURL.newPipeline()` usa a `SubscriptionKeyCredential` política e cria uma instância de [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest). O `routeURL` representa uma URL para as operações de [rota](https://docs.microsoft.com/rest/api/maps/route) do Azure Mapas.

2. Depois de configurar as credenciais e a URL, adicione o código JavaScript a seguir para construir a rota do ponto de partida ao ponto de chegada. O `routeURL` solicita ao serviço de rotas do Azure Mapas que calcule as direções da rota. Um conjunto de recursos GeoJSON da resposta é então extraído usando o método `geojson.getFeatures()` e adicionada à fonte de dados.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();
        datasource.add(data);
    });
    ```

3. Salve o arquivo **MapRoute.html** e atualize seu navegador da Web. Para uma conexão bem-sucedida com APIs dos Mapas, você deverá ver um mapa semelhante ao seguinte.

    ![Controle de Mapeamento e Serviço de Roteiros do Azure](./media/tutorial-route-location/map-route.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma nova página da Web usando a API de controle de mapeamento
> * Definir coordenadas de endereço
> * Consultar o serviço de roteiros para obter o trajeto até o ponto de interesse

> [!div class="nextstepaction"]
> [Exibir código-fonte completo](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html)

> [!div class="nextstepaction"]
> [Exibir amostra dinâmica](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination)

O tutorial a seguir demonstra como criar uma consulta de rota com restrições como modo de viagem ou tipo de carga e, em seguida, exiba várias rotas no mesmo mapa.

> [!div class="nextstepaction"]
> [Localizar rotas para diferentes modos de viagem](./tutorial-prioritized-routes.md)
