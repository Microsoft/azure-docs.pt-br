---
title: 'Tutorial: Localizar a rota para uma localização | Microsoft Azure Mapas'
description: Tutorial sobre como localizar uma rota para um ponto de interesse. Veja como definir coordenadas de endereço e consultar o serviço Roteiros dos Azure Mapas para obter o trajeto até o ponto.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 1f1647766eeae917f7a2266ebb8f00fdfab3452e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210570"
---
# <a name="tutorial-how-to-display-route-directions-using-azure-maps-route-service-and-map-control"></a>Tutorial: Como exibir o trajeto de rota usando o serviço Roteiros do Azure Mapas e o Controle de Mapeamento

Esse tutorial mostra como usar a [API do serviço Roteiros](/rest/api/maps/route) do Azure Mapas e o [Controle de Mapeamento](./how-to-use-map-control.md) para exibir o trajeto de rota do ponto inicial ao final. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar e exibir o Controle de Mapeamento em uma página da Web. 
> * Defina a renderização de exibição da rota definindo [Camadas de símbolo](map-add-pin.md) e [Camadas de linha](map-add-line-layer.md).
> * Crie e adicione objetos GeoJSON ao mapa para representar os pontos inicial e final.
> * Obtenha o trajeto de rota dos pontos inicial e final usando a [API de Obtenção de trajeto de Rota](/rest/api/maps/route/getroutedirections).

Você pode obter o código-fonte completo para o exemplo [aqui](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html). É possível encontrar um exemplo ao vivo [aqui](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination).

## <a name="prerequisites"></a>Pré-requisitos

1. [Fazer uma conta do Azure Mapas](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Obtenha uma chave de assinatura primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), também conhecida como a chave primária ou a chave de assinatura.

<a id="getcoordinates"></a>

## <a name="create-and-display-the-map-control"></a>Criar e exibir o Controle de Mapeamento

As etapas a seguir mostram como criar e exibir o Controle de Mapeamento em uma página da Web.

1. Em seu computador local, crie um novo arquivo e nomeie-o como **MapRoute.html**.
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

     :::image type="content" source="./media/tutorial-route-location/basic-map.png" alt-text="Renderização de mapa básica do Controle de Mapeamento":::

## <a name="define-route-display-rendering"></a>Definir renderização de exibição de rota

Neste tutorial, vamos renderizar a rota usando uma camada de linhas. Os pontos inicial e final serão renderizados usando uma camada de símbolo. Para obter mais informações sobre como adicionar camadas de linha, confira [Adicionar uma camada de linha a um mapa](map-add-line-layer.md). Para saber mais sobre camadas de símbolo, confira [Adicionar uma camada de símbolo a um mapa](map-add-pin.md).

1. Acrescente o código JavaScript a seguir à função `GetMap`. Esse código implementa o manipulador de eventos `ready` do Controle de Mapeamento. O restante do código neste tutorial será colocado no manipulador de eventos `ready`.

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

    No manipulador de eventos `ready` do Controle de Mapeamento, uma fonte de dados é criada para armazenar a rota do ponto inicial ao final. Para definir como a linha de rota será renderizada, uma camada de linhas será criada e anexada à fonte de dados.  Para garantir que a linha de rota não cubra os rótulos de estrada, passamos um segundo parâmetro com o valor de `'labels'`.

    Em seguida, uma camada de símbolo é criada e anexada à fonte de dados. Essa camada especifica como os pontos inicial e final são renderizados. As expressões foram adicionadas para recuperar as informações de rótulo de texto e de imagem de ícone das propriedades em cada objeto de ponto. Para saber mais sobre as expressões, confira [Expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md).

2. Defina o ponto inicial como Microsoft e o ponto final como um posto de gasolina em Seattle.  No manipulador de eventos `ready` do Controle de Mapeamento, acrescente o código a seguir.

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

    Esse código cria dois [objetos de Ponto GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) para representar os pontos inicial e final, que são então adicionados à fonte de dados. 

    O último bloco de código define a exibição da câmera usando a latitude e a longitude dos pontos inicial e final. Os pontos de partida e chegada são adicionados à fonte de dados. A caixa delimitadora dos pontos de partida e chegada é calculada com a função `atlas.data.BoundingBox.fromData`. Essa caixa delimitadora é usada para definir o modo de exibição das câmeras por toda a rota usando a função `map.setCamera`. Um preenchimento é adicionado para compensar as dimensões de pixel dos ícones de símbolo. Para obter mais informações sobre a propriedade setCamera do Controle de Mapeamento, confira a propriedade [setCamera(CameraOptions | CameraBoundsOptions & AnimationOptions)](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-).

3. Salve **MapRoute.html** e atualize o navegador. Agora o mapa está centralizado em Seattle. O alfinete azul em formato de lágrima marca o ponto inicial. O alfinete azul redondo marca o ponto final.

    :::image type="content" source="./media/tutorial-route-location/map-pins.png" alt-text="Exibir os pontos de início e fim das rotas no mapa":::

<a id="getroute"></a>

## <a name="get-route-directions"></a>Obter trajeto de rota

Esta seção mostra como usar a API de Direções de Rota do Azure Mapas para obter direções de rota e o tempo estimado de chegada de um ponto para outro.

>[!TIP]
>Os serviços Roteiros do Azure Mapas fornecem APIs para planejar rotas com base em diferentes tipos de rota, como *mais rápidas*, *mais curtas*, *econômicas* ou *emocionantes* com base na distância, nas condições de tráfego e no meio de transporte usado. O serviço também permite que os usuários planejem rotas futuras com base nas condições históricas de tráfego. Os usuários podem ver a previsão de durações de rota para qualquer determinado horário. Para obter mais informações, confira [API de Obtenção de trajeto de rota](/rest/api/maps/route/getroutedirections).

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

2. Depois de configurar as credenciais e a URL, acrescente o código a seguir no manipulador de eventos `ready` do controle. Esse código constrói a rota do ponto inicial ao ponto final. O `routeURL` solicita à API do serviço Roteiros do Azure Mapas que calcule os trajetos de rota. Um conjunto de recursos GeoJSON da resposta é então extraído usando o método `geojson.getFeatures()` e adicionada à fonte de dados.

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

3. Salve o arquivo **MapRoute.html** e atualize seu navegador da Web. O mapa agora deve exibir a rota do ponto inicial ao ponto final.

     :::image type="content" source="./media/tutorial-route-location/map-route.png" alt-text="Serviço de Roteiros e Controle de Mapeamento do Azure":::

Você pode obter o código-fonte completo para o exemplo [aqui](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/route.html). É possível encontrar um exemplo ao vivo [aqui](https://azuremapscodesamples.azurewebsites.net/?sample=Route%20to%20a%20destination).

## <a name="clean-up-resources"></a>Limpar os recursos

Não há recursos que exijam limpeza.

## <a name="next-steps"></a>Próximas etapas

O tutorial a seguir demonstra como criar uma consulta de rota com restrições como modo de viagem ou tipo de carga. Em seguida, é possível exibir várias rotas no mesmo mapa.

> [!div class="nextstepaction"]
> [Localizar rotas para diferentes modos de viagem](./tutorial-prioritized-routes.md)