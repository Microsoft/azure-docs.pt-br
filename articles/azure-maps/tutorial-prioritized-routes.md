---
title: 'Tutorial: Localizar várias rotas por modo de viagem | Microsoft Azure Mapas'
description: Tutorial sobre como usar os Azure Mapas para localizar rotas para modos de viagem específicos para pontos de interesse. Veja como exibir várias rotas nos mapas.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 5e7f38e4bbc95e04bf54f8f7a2dd80e21891e8df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102215486"
---
# <a name="tutorial-find-and-display-routes-for-different-modes-of-travel-using-azure-maps"></a>Tutorial: localizar e exibir rotas para diferentes modos de viagem usando o Azure Mapas

Este tutorial mostra como usar o [serviço Roteiros](/rest/api/maps/route) e o [Controle de Mapeamento](./how-to-use-map-control.md) dos Azure Mapas a fim de exibir trajetos de rota para veículos particulares e comerciais (caminhões) com o tipo de carga `USHazmatClass2`. Além disso, mostraremos como visualizar os dados de tráfego em tempo real em um mapa. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar e exibir o Controle de Mapeamento em uma página da Web
> * Renderizar dados de tráfego em tempo real em um mapa
> * Solicitar e exibir rotas para veículos particulares e comerciais em um mapa

## <a name="prerequisites"></a>Pré-requisitos

1. Entre no [portal do Azure](https://portal.azure.com).

2. [Crie uma conta do Azure Mapas](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Obtenha uma chave de assinatura primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), também conhecida como a chave primária ou a chave de assinatura. Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](how-to-manage-authentication.md).

Você pode obter o código-fonte completo para o exemplo [aqui](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html). É possível encontrar um exemplo ao vivo [aqui](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel).

## <a name="create-a-new-web-page-using-the-map-control-api"></a>Criar uma nova página da Web usando a API de controle de mapeamento

As etapas a seguir mostram como criar e exibir o Controle de Mapeamento em uma página da Web.

1. Em seu computador local, crie um novo arquivo e nomeie-o como **MapTruckRoute.html**.
2. Copie/cole a marcação HTML a seguir no arquivo.

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

     O cabeçalho HTML inclui os arquivos de recurso CSS e JavaScript hospedados pela biblioteca de Controle de Mapeamento do Azure. O evento `onload` do corpo chama a função `GetMap`. Na próxima etapa, adicionaremos o código de inicialização do Controle de Mapeamento.

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

4. Salve o arquivo e abra-o em seu navegador. Um exemplo é exibido.

    :::image type="content" source="./media/tutorial-prioritized-routes/basic-map.png" alt-text="Renderização de mapa básica do Controle de Mapeamento":::

## <a name="render-real-time-traffic-data-on-a-map"></a>Renderizar dados de tráfego em tempo real em um mapa

1. Acrescente o código JavaScript a seguir à função `GetMap`. Esse código implementa o manipulador de eventos `ready` do Controle de Mapeamento. O restante do código neste tutorial será colocado no manipulador de eventos `ready`.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    No manipulador `ready` de eventos do mapa, a configuração do fluxo de tráfego no mapa é definida como `relative`, que é a velocidade da estrada em relação ao fluxo livre. Confira mais opções de tráfego na [Interface TrafficOptions](/javascript/api/azure-maps-control/atlas.trafficoptions).

2. Salve o arquivo **MapTruckRoute.html** e atualize a página em seu navegador. Se você ampliar o mapa de qualquer cidade, como Los Angeles, verá que as ruas aparecem com o fluxo de tráfego atual.

    :::image type="content" source="./media/tutorial-prioritized-routes/traffic-map.png" alt-text="Exibir o tráfego em um mapa":::

<a id="queryroutes"></a>

## <a name="define-route-display-rendering"></a>Definir renderização de exibição de rota

Neste tutorial, duas rotas serão calculadas e renderizadas no mapa. A primeira rota será calculada para um veículo particular (carro). A segunda rota será calculada para um veículo comercial (caminhão) a fim de mostrar a diferença entre os resultados. Após a renderização, o mapa exibirá um ícone de símbolo para os pontos de partida e chegada da rota e as geometrias das linhas de rota com cores diferentes para cada caminho. Para obter mais informações sobre como adicionar camadas de linha, confira [Adicionar uma camada de linha a um mapa](map-add-line-layer.md). Para saber mais sobre camadas de símbolo, confira [Adicionar uma camada de símbolo a um mapa](map-add-pin.md).

1. No manipulador de eventos `ready` do Controle de Mapeamento, acrescente o código a seguir.

    ```JavaScript

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

    ```


    No manipulador de eventos `ready` do Controle de Mapeamento, uma fonte de dados é criada para armazenar a rota do ponto inicial ao final. [Expressões](data-driven-style-expressions-web-sdk.md) são usadas para recuperar a largura da linha e a cor das propriedades no recurso de linhas de rota. Para garantir que a linha de rota não cubra os rótulos de estrada, passamos um segundo parâmetro com o valor de `'labels'`.

    Em seguida, uma camada de símbolo é criada e anexada à fonte de dados. Essa camada especifica como os pontos inicial e final são renderizados. As expressões foram adicionadas para recuperar as informações de rótulo de texto e de imagem de ícone das propriedades em cada objeto de ponto. Confira mais sobre expressões em [Expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md).

2. Defina o ponto de partida como uma empresa fictícia em Seattle chamada Fabrikam e o ponto de chegada como um escritório da Microsoft.  No manipulador de eventos `ready` do Controle de Mapeamento, acrescente o código a seguir.


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

    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });

    ```

    Esse código cria dois [objetos de Ponto GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) para representar os pontos inicial e final, que são então adicionados à fonte de dados.

    O último bloco de código define a exibição da câmera usando a latitude e a longitude dos pontos inicial e final. Os pontos de partida e chegada são adicionados à fonte de dados. A caixa delimitadora dos pontos de partida e chegada é calculada com a função `atlas.data.BoundingBox.fromData`. Essa caixa delimitadora é usada para definir o modo de exibição das câmeras por toda a rota usando a função `map.setCamera`. Um preenchimento é adicionado para compensar as dimensões de pixel dos ícones de símbolo. Para obter mais informações sobre a propriedade setCamera do Controle de Mapeamento, confira a propriedade [setCamera(CameraOptions | CameraBoundsOptions & AnimationOptions)](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-).

3. Salve **TruckRoute.html** e atualize o navegador. Agora o mapa está centralizado em Seattle. O alfinete azul em formato de lágrima marca o ponto inicial. O alfinete azul redondo marca o ponto final.

   :::image type="content" source="./media/tutorial-prioritized-routes/pins-map.png" alt-text="Exibir o mapa com os pontos inicial e final":::

<a id="multipleroutes"></a>

## <a name="request-and-display-private-and-commercial-vehicle-routes-on-a-map"></a>Solicitar e exibir rotas para veículos particulares e comerciais em um mapa

Esta seção mostra como usar o serviço Roteiros do Azure Mapas para obter o trajeto de um ponto a outro com base no seu meio de transporte. Usaremos dois meios de transporte: caminhão e carro.

>[!TIP]
>O serviço Roteiros fornece APIs para planejar rotas *mais rápidas*, *mais curtas*, *econômicas* ou *emocionantes* com base na distância, nas condições de trânsito e no meio de transporte usado. O serviço também permite que os usuários planejem rotas futuras com base nas condições históricas de tráfego. Os usuários podem ver a previsão de durações de rota para qualquer determinado horário. Para obter mais informações, confira [API de Obtenção de trajeto de rota](/rest/api/maps/route/getroutedirections).

1. Na função `GetMap`, dentro do manipulador de eventos `ready` do controle, adicione o seguinte ao código JavaScript.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   O `SubscriptionKeyCredential` cria um `SubscriptionKeyCredentialPolicy` para autenticar solicitações HTTP para Azure Mapas com a chave de assinatura. O `atlas.service.MapsURL.newPipeline()` usa a `SubscriptionKeyCredential` política e cria uma instância de [Pipeline](/javascript/api/azure-maps-rest/atlas.service.pipeline). O `routeURL` representa uma URL para as operações de [rota](/rest/api/maps/route) do Azure Mapas.

2. Depois de configurar as credenciais e a URL, adicione o código JavaScript a seguir para construir uma rota de caminhão do ponto inicial ao final. Essa rota é criada e exibida para um caminhão transportando carga com classificação `USHazmatClass2`.

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

    O código acima consulta o serviço Roteiros dos Azure Mapas por meio da [API de Trajetos de Rota dos Azure Mapas](/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-). A linha de rota, em seguida, é extraída da coleção de recursos GeoJSON, mais precisamente da resposta que é extraída usando o método `geojson.getFeatures()`. Por fim, a linha da rota é adicionada à fonte de dados. Estamos adicionando-a ao índice 0, para garantir que a rota de caminhão seja renderizada antes de qualquer outra linha na fonte de dados, porque o cálculo da rota de caminhão, em geral, será mais lento do que um cálculo de rota de carro. Se a linha de rota de caminhão for adicionada à fonte de dados após a rota de carro, ela será renderizada acima dela. Duas propriedades são adicionadas à linha de rota de caminhão: uma cor de traço azul e uma largura de traço de nove pixels.

    >[!TIP]
    > Para ver todas as opções e valores possíveis para a API de Trajetos de Rota do Azure Mapas, confira [Parâmetros de URI para Trajetos de Rota POST](/rest/api/maps/route/postroutedirections#uri-parameters).

3. Agora, adicione o código JavaScript a seguir para construir uma rota de carro.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. This will add the car route after the truck route.  
        datasource.add(routeLine);
    });
    ```

    O código acima consulta o serviço de rotas do Azure Mapas por meio do método [API de Trajetos de Rota do Azure Mapas](/javascript/api/azure-maps-rest/atlas.service.routeurl#calculateroutedirections-aborter--geojson-position----calculateroutedirectionsoptions-). A linha de rota, em seguida, é extraída da coleção de recursos GeoJSON, mais precisamente da resposta que é extraída usando o método `geojson.getFeatures()`. Por fim, a linha da rota é adicionada à fonte de dados. Duas propriedades são adicionadas à linha de rota de caminhão: uma cor de traço roxa e uma largura de traço de cinco pixels.

4. Salve o arquivo **TruckRoute.html** e atualize seu navegador da Web. Agora, o mapa exibirá as rotas de caminhão e de carro.

    :::image type="content" source="./media/tutorial-prioritized-routes/prioritized-routes.png" alt-text="Rotas para veículos particulares e comerciais em um mapa com o serviço Roteiros do Azure":::

    A rota de caminhão é exibida usando uma linha azul espessa. A rota de carro é exibida usando uma linha roxa fina. A rota de carro passa pelo Lago Washington pela estrada I-90, que atravessa túneis em áreas residenciais. Como os túneis estão próximos às áreas residenciais, a carga de resíduos perigosos é restrita. A rota de caminhão, que especifica um tipo de carga `USHazmatClass2`, é instruída a usar outra rodovia.

Você pode obter o código-fonte completo para o exemplo [aqui](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html). É possível encontrar um exemplo ao vivo [aqui](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel).

Você também pode [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

## <a name="clean-up-resources"></a>Limpar os recursos

Não há recursos que exijam limpeza.

## <a name="next-steps"></a>Próximas etapas

O próximo tutorial demonstra o processo de criação de um localizador de lojas simples usando o Azure Mapas.

> [!div class="nextstepaction"]
> [Criar um localizador de lojas usando o Azure Mapas](./tutorial-create-store-locator.md)