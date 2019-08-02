---
title: Várias rotas com os Mapas do Azure | Microsoft Docs
description: Encontre rotas para diferentes modos de viagem usando os Mapas do Azure
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: ad67b17d76e811d5977955c40f444c4b7c0a01e3
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478844"
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>Encontre rotas para diferentes modos de viagem usando os Mapas do Azure

Este tutorial mostra como usar sua conta dos Mapas do Azure e o serviço de roteiro para encontrar a rota para seu ponto de interesse, priorizado por seu modo de viagem. Você exibe duas rotas diferentes em seu mapa, uma para carros e outra para caminhões que podem ter restrições de rota devido a altura, peso ou carga perigosa. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma nova página da Web usando a API de controle de mapeamento
> * Visualizar o fluxo de tráfego em seu mapa
> * Criar consultas de rota que declarem o modo de viagem
> * Exibir várias rotas em seu mapa

## <a name="prerequisites"></a>Pré-requisitos

Antes de prosseguir, siga as etapas no primeiro tutorial para [criar sua conta dos Mapas do Azure](./tutorial-search-location.md#createaccount) e [obter a chave de assinatura da conta](./tutorial-search-location.md#getkey).

## <a name="create-a-new-map"></a>Criar um novo mapa

As etapas a seguir mostra como criar uma página HTML estática inserida com a API do Controle de Mapeamento.

1. Em seu computador local, crie um novo arquivo e nomeie-o como **MapTruckRoute.html**.
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

    A classe `atlas.Map` fornece o controle de um mapa Web visual e interativo, além de ser um componente da API de Controle de Mapeamento do Azure.

4. Salve o arquivo e abra-o em seu navegador. Agora você tem um mapa básico que você pode desenvolver ainda mais.

   ![Exibir mapa básico](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Visualizar o fluxo de tráfego

1. Adicione a exibição do fluxo de tráfego ao mapa. O evento `ready` de mapas aguarda até que os recursos de mapas sejam carregados e prontos para interagir com segurança com ele.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    No manipulador `ready` de eventos de carregamento, a configuração do fluxo de tráfego no mapa é definida como `relative`, que é a velocidade da estrada em relação ao fluxo livre. Você também pode defini-lo na velocidade `absolute` do caminho, ou `relative-delay`, o qual exibe a velocidade relativa em que difere do fluxo contínuo.

2. Salve o arquivo **MapTruckRoute.html** e atualize a página em seu navegador. Se interagir com o mapa e ampliá-lo em Los Angeles, você deverá ver as ruas com os dados de tráfego atuais.

   ![Exibir o mapa de trânsito](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Definir como a rota será renderizada

Neste tutorial, duas rotas serão calculadas e renderizadas no mapa. Uma rota usando estradas acessíveis a carros e outra usando as acessíveis a caminhões. Quando renderizadas, exibiremos um ícone de símbolo para o início e o fim da rota e linhas de cores diferentes para cada caminho de rota.

1. Após inicializar o mapa, adicione o código JavaScript a seguir no evento de carregamento `ready` de mapa.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('ready', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
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
    
    No manipulador `ready` de eventos do mapa, uma fonte de dados é criada para armazenar a linha da rota e os pontos de partida e de chegada. Uma camada de linhas é criada e anexada à fonte de dados para definir como a linha de rota será renderizada. Expressões são usadas para recuperar a largura da linha e a cor das propriedades no recurso de linha de rota. Quando você adiciona a camada ao mapa, um segundo parâmetro com o valor de `'labels'` é passado especificando que essa camada deve ser renderizada abaixo dos rótulos do mapa. Isso fará com que a linha de rota não cubra os rótulos de estrada. Uma camada de símbolo é criada e anexada à fonte de dados. Essa camada especifica como os pontos de partida e chegada serão renderizados; neste caso, expressões foram adicionadas para recuperar as informações de rótulo de texto e de imagem de ícone das propriedades em cada objeto de ponto. 
    
2. Neste tutorial, defina o ponto inicial como uma empresa fictícia em Seattle chamada Fabrikam e o ponto de destino como um escritório da Microsoft. No manipulador `ready` de eventos do mapa, adicione o código a seguir.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-round-blue'
    });
    ```

    Esse código cria dois [objetos GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) para representar os pontos de partida e chegada da rota. As propriedades `title` e `icon` são adicionadas a cada ponto.

3. Em seguida, adicione o código JavaScript abaixo para adicionar os pinos dos pontos de partida e chegada ao mapa:

    ```JavaScript
    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });
    ```

    Os pontos de partida e chegada são adicionados à fonte de dados. A caixa delimitadora dos pontos de partida e chegada é calculada com a função `atlas.data.BoundingBox.fromData`. Essa caixa delimitadora é usada para definir o modo de exibição das câmeras por toda a rota usando a função `map.setCamera`. Um preenchimento é adicionado para compensar as dimensões de pixel dos ícones de símbolo.

4. Salve o arquivo e atualize seu navegador para ver os pinos exibidos em seu mapa. Agora o mapa está centralizado em Seattle e você pode ver o pin azul redondo marcando o ponto de início e o pin azul marcando o ponto final.

   ![Exibir o mapa com os pontos inicial e final](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Renderizar rotas priorizadas por modo de viagem

Esta seção mostra como usar a API do serviço de roteiros dos Mapas para encontrar várias rotas com base em um determinado ponto de partida até o ponto de destino com base no seu modo de transporte. O serviço de roteiros fornece APIs para planejar a rota *mais rápida*, *mais curta*, *econômica* ou *emocionante* entre dois locais, considerando as condições de trânsito em tempo real. Também permite planejar rotas futuras usando o amplo banco de dados de histórico de tráfego do Azure e prevendo durações de rotas para cada dia e hora. Para saber mais, confira [Trajeto GetRoute](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Todos os blocos de código a seguir devem ser adicionados **dentro do eventListener de carregamento do mapa** para garantir que eles sejam carregados após o carregamento total do mapa.

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

2. Depois de configurar as credenciais e a URL, adicione o seguinte código JavaScript para construir uma rota do ponto inicial ao final para um caminhão transportando carga com classificação USHazmatClass2 e exibir os resultados.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request for a truck vehicle type
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates,{
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```

    O snippet de código acima consulta o serviço de roteamento do Azure Mapas por meio do método [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest). A linha de rota, em seguida, é extraída da coleção de recursos GeoJSON, mais precisamente da resposta que é extraída usando o método `geojson.getFeatures()`. A linha da rota é então adicionada à fonte de dados. Ela também adiciona um índice de 0 para garantir que ela seja renderizada antes de quaisquer outras linhas na fonte de dados. Isso é feito porque o cálculo da rota de caminhão geralmente será mais lento do que o cálculo de rota de carro e, se a linha da rota de caminhão for adicionada à fonte de dados após a rota de carro, ela será renderizada por cima. Duas propriedades são adicionadas à linha de rota de caminhão: uma cor de traço que é um belo tom de azul e uma largura de traço de 9 pixels.

3. Adicione o código JavaScript a seguir para construir uma rota para um carro e exibir os resultados.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.  
        datasource.add(routeLine);
    });
    ```

    O snippet de código acima consulta o serviço de roteamento do Azure Mapas por meio do método [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest). A linha de rota, em seguida, é extraída da coleção de recursos GeoJSON, mais precisamente da resposta que é extraída usando o método `geojson.getFeatures()`. A linha da rota é então adicionada à fonte de dados. Duas propriedades são adicionadas à linha de rota de carro: uma cor de traço que é um tom de roxo e uma largura de traço de 5 pixels.  

4. Salve o arquivo **MapTruckRoute.html** e atualize seu navegador para observar o resultado. Para uma conexão bem-sucedida com APIs dos Mapas, você deverá ver um mapa semelhante ao seguinte.

    ![Rotas priorizadas com o Serviço de Roteiros do Azure](./media/tutorial-prioritized-routes/prioritized-routes.png)

    A rota de caminhão é azul e mais grossa, enquanto a rota de carro é roxa e mais fina. A rota de carro vai passa pelo Lago Washington pela estrada I-90, que passa por túneis em áreas residenciais, restringindo a passagem de carga de lixo tóxico. A rota de caminhão, que especifica um tipo de carga USHazmatClass2, é instruída corretamente a usar uma estrada diferente.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar uma nova página da Web usando a API de controle de mapeamento
> * Visualizar o fluxo de tráfego em seu mapa
> * Criar consultas de rota que declarem o modo de viagem
> * Exibir várias rotas em seu mapa

> [!div class="nextstepaction"]
> [Exibir código-fonte completo](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

> [!div class="nextstepaction"]
> [Exibir amostra dinâmica](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

O próximo tutorial demonstra o processo de criação de um localizador de lojas simples usando o Azure Mapas.

> [!div class="nextstepaction"]
> [Criar um localizador de lojas usando o Azure Mapas](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)