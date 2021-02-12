---
title: Como usar o módulo e/s espacial do mapas do Azure | Mapas do Microsoft Azure
description: Saiba como usar o módulo de e/s espacial fornecido pelo SDK da Web do Azure Maps. Este módulo fornece recursos robustos para facilitar para os desenvolvedores a integração de dados espaciais com o SDK da Web do Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/28/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: cd64c80acceb1542c080fc45efbce59f287d448a
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680689"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>Como usar o módulo e/s espacial do mapas do Azure

O SDK da Web do Azure Maps fornece o **módulo de e/s espacial**, que integra dados espaciais com o SDK da Web do Azure Maps usando JavaScript ou TypeScript. Os recursos robustos deste módulo permitem aos desenvolvedores:

- [Ler e gravar arquivos de dados espaciais comuns](spatial-io-read-write-spatial-data.md). Os formatos de arquivo com suporte incluem os arquivos: KML, KMZ, GPX, GeoRSS, GML, geojson e CSV contendo colunas com informações espaciais. Também dá suporte a texto de Well-Known (WKT).
- [Conecte-se aos serviços do Open Geospatial Consortium (OGC) e integre-o ao SDK da Web do Azure Maps. Sobreponha os serviços de mapa da Web (WMS) e os serviços de bloco do mapa da Web (WMTS) como camadas no mapa](spatial-io-add-ogc-map-layer.md).
- [Consultar dados em um serviço de recurso da Web (WFS)](spatial-io-connect-wfs-service.md).
- [Sobreponha conjuntos de dados complexos que contêm informações de estilo e faça com que eles sejam renderizados automaticamente usando o código mínimo](spatial-io-add-simple-data-layer.md).
- [Aproveite o XML de alta velocidade e o leitor de arquivo delimitado e classes de gravador](spatial-io-core-operations.md).

Neste guia, aprenderemos como integrar e usar o módulo de e/s espacial em um aplicativo Web.

Este vídeo fornece uma visão geral do módulo de e/s espacial no SDK da Web do Azure Maps.

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Easily-integrate-spatial-data-into-the-Azure-Maps/player?format=ny]

> [!WARNING]
> Use apenas dados e serviços que sejam de uma fonte confiável, especialmente se fizer referência a ele de outro domínio. O módulo de e/s espacial executa etapas para minimizar o risco. no entanto, a abordagem mais segura não é permitir que todos os dados do danagerous em seu aplicativo comecem. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder usar o módulo de e/s espacial, você precisará [fazer uma conta do Azure Maps](./quick-demo-map-app.md#create-an-azure-maps-account) e [obter a chave de assinatura primária para sua conta](./quick-demo-map-app.md#get-the-primary-key-for-your-account).

## <a name="installing-the-spatial-io-module"></a>Instalando o módulo de e/s espacial

Você pode carregar o módulo e/s espacial do Azure Maps usando uma das duas opções:

* A CDN do Azure hospedada globalmente para o módulo de e/s espacial do Azure Maps. Para essa opção, você adiciona uma referência ao JavaScript no `<head>` elemento do arquivo HTML.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* O código-fonte para [Azure-Maps-espacial-Io](https://www.npmjs.com/package/azure-maps-spatial-io) pode ser carregado localmente e, em seguida, hospedado com seu aplicativo. Esse pacote também inclui definições de TypeScript. Para essa opção, use o seguinte comando para instalar o pacote:

    ```sh
    npm install azure-maps-spatial-io
    ```

    Em seguida, adicione uma referência ao JavaScript no `<head>` elemento do documento HTML:

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>Usando o módulo de e/s espacial

1. Criar um novo arquivo HTML.

2. Carregue o SDK da Web do Azure Maps e inicialize o controle de mapa. Consulte o guia de [controle do mapa do Azure Maps](./how-to-use-map-control.md) para obter os detalhes. Depois de concluir esta etapa, o arquivo HTML deverá ter esta aparência:

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <script type='text/javascript'>

            var map;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    // Write your code here to make sure it runs once the map resources are ready

                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

2. Carregue o módulo de e/s espacial do Azure Maps. Para este exercício, use a CDN para o módulo de e/s espacial do mapas do Azure. Adicione a referência abaixo ao `<head>` elemento do seu arquivo HTML:

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. Inicialize um `datasource` e adicione a fonte de dados ao mapa. Inicialize um `layer` e adicione a fonte de dados à camada do mapa. Em seguida, processe a fonte de dados e a camada. Antes de rolar para baixo para ver o código completo na próxima etapa, pense nos melhores lugares para colocar a fonte de dados e os trechos de código de camada. Lembre-se de que, antes de manipular programaticamente o mapa, devemos aguardar até que o recurso de mapa esteja pronto.

    ```javascript
    var datasource, layer;
    ```

    e

    ```javascript
    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);
    
    //Add a simple data layer for rendering the data.
    layer = new atlas.layer.SimpleDataLayer(datasource);
    map.layers.add(layer);
    ```

4. Juntando tudo isso, o código HTML deve ser semelhante ao código a seguir. Este exemplo demonstra como ler um arquivo XML de uma URL. Em seguida, carregue e exiba os dados de recurso do arquivo no mapa. 

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title>Spatial IO Module Example</title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <!-- Add reference to the Azure Maps Spatial IO module. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

        <script type='text/javascript'>
            var map, datasource, layer;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Add a simple data layer for rendering the data.
                    layer = new atlas.layer.SimpleDataLayer(datasource);
                    map.layers.add(layer);

                    //Read an XML file from a URL or pass in a raw XML string.
                    atlas.io.read('superCoolKmlFile.xml').then(r => {
                        if (r) {
                            //Add the feature data to the data source.
                            datasource.add(r);

                            //If bounding box information is known for data, set the map view to it.
                            if (r.bbox) {
                                map.setCamera({
                                    bounds: r.bbox,
                                    padding: 50
                                });
                            }
                        }
                    });
                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

5. Lembre-se de substituir `<Your Azure Maps Key>` pela sua chave primária. Abra o arquivo HTML e você verá resultados semelhantes à imagem a seguir:

    <center>

    ![Exemplo de dados espaciais](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>Próximas etapas

O recurso que demonstramos aqui é apenas um dos muitos recursos disponíveis no módulo de e/s espacial. Leia os guias abaixo para saber como usar outras funcionalidades no módulo de e/s espacial:

> [!div class="nextstepaction"]
> [Adicionar uma camada de dados simples](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [Ler e gravar dados espaciais](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada do mapa OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Conectar-se a um serviço WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Aproveitar as principais operações](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Detalhes do formato de dados com suporte](spatial-io-supported-data-format-details.md)

Consulte a documentação de e/s espacial do Azure Maps:

> [!div class="nextstepaction"]
> [Pacote de e/s espacial do Azure Maps](/javascript/api/azure-maps-spatial-io/)