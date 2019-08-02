---
title: Criar um localizador de lojas usando o Azure Mapas | Microsoft Docs
description: Crie um localizador de lojas usando o Azure Mapas.
author: walsehgal
ms.author: v-musehg
ms.date: 11/15/2018
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 4cc21a4dbab7d5114eed8414c6530eab5f42bb00
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478852"
---
# <a name="create-a-store-locator-by-using-azure-maps"></a>Criar um localizador de lojas usando o Azure Mapas

Este tutorial orienta você pelo processo de criação de um localizador de lojas simples usando o Azure Mapas. Localizadores de lojas são comuns. Muitos dos conceitos usados nesse tipo de aplicativo também são usados por muitos outros tipos de aplicativos. Oferecer um localizador de lojas para os clientes é uma necessidade para a maioria das empresas que fazem vendas diretamente aos consumidores. Neste tutorial, você aprenderá como:
    
> [!div class="checklist"]
> * Criar uma nova página da Web usando a API de Controle de Mapeamento do Azure.
> * Carregar dados personalizados de um arquivo e exibi-los em um mapa.
> * Usar o serviço de pesquisa do Azure Mapas para encontrar um endereço ou digitar uma consulta.
> * Obter a localização do usuário do navegador e mostrá-la no mapa.
> * Combinar várias camadas para criar símbolos personalizados no mapa.  
> * Agrupar pontos de dados em cluster.  
> * Adicionar controles de zoom ao mapa.

<a id="Intro"></a>

Vá diretamente para o [exemplo de localizador de lojas ativo](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator) ou para o [código-fonte](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator). 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste tutorial, primeiro [crie sua conta do Azure Mapas](./tutorial-search-location.md#createaccount) e [obtenha a chave de assinatura da conta](./tutorial-search-location.md#getkey).

## <a name="design"></a>Design

Antes de mergulhar no código, é uma boa ideia começar com um design. O localizador de lojas pode ser simples ou complexo; você é quem escolhe. Neste tutorial, criaremos um localizador de lojas simples. Incluímos algumas dicas ao longo do caminho para ajudá-lo a estender algumas funcionalidades se você optar por isso. Podemos criar um localizador de lojas para uma empresa fictícia chamada Contoso Coffee. A figura abaixo mostra um layout geral delineado do localizador de lojas que criamos neste tutorial:

<br/>
<center>

![Localizador de lojas delineado para lojas da Contoso Coffee](./media/tutorial-create-store-locator/SimpleStoreLocatorWireframe.png)</center>

Para maximizar a utilidade do localizador de lojas, incluímos um layout dinâmico que se ajusta à largura da tela do usuário quando ela tem menos de 700 pixels de largura. Um layout dinâmico facilita o uso do localizador de lojas em uma tela pequena, como em um dispositivo móvel. Aqui está um layout delineado para tela pequena:  

<br/>
<center>

![Localizador de lojas delineado para a Contoso Coffee em um dispositivo móvel](./media/tutorial-create-store-locator/SimpleStoreLocatorMobileWireframe.png)</center>

O localizador delineado mostra um aplicativo bastante simples. O aplicativo tem uma caixa de pesquisa, uma lista de lojas próximas, um mapa com alguns marcadores (símbolos) e uma janela pop-up que exibe informações adicionais quando o usuário seleciona um marcador. Mais detalhadamente, aqui estão os recursos que criamos para esse localizador de lojas neste tutorial:

* Todos os lugares do arquivo de dados delimitado por tabulação importado são carregados no mapa.
* O usuário pode ampliar ou reduzir o mapa, fazer uma pesquisa e selecionar o botão de GPS Minha Localização.
* O layout da página se ajusta com base na largura da tela do dispositivo.  
* Um cabeçalho mostra o logotipo da loja.  
* O usuário pode usar uma caixa de pesquisa e um botão de pesquisa para procurar um lugar, como um endereço, um CEP ou uma cidade. 
* Um evento `keypress` adicionado à caixa de pesquisa dispara uma pesquisa quando o usuário pressiona Enter. Essa funcionalidade muitas vezes é negligenciada, mas ela cria uma experiência do usuário melhor.
* Quando o mapa é movido, a distância de cada lugar contando do centro do mapa é calculada. A lista de resultados é atualizada para exibir os lugares mais próximos na parte superior do mapa.  
* Quando você seleciona um resultado na lista de resultados, o mapa é centralizado no local selecionado e as informações sobre o local aparecem em uma janela pop-up.  
* A escolha de um lugar específico no mapa também dispara uma janela pop-up.
* Quando o usuário reduz o mapa, os locais são agrupados em clusters. Clusters são representados por um círculo com um número dentro do círculo. Os clusters são formados e separados dependendo do nível de zoom escolhido pelo usuário.
* A escolha de um cluster amplia o mapa em dois níveis e centraliza sobre o lugar do cluster.

<a id="create a data-set"></a>

## <a name="create-the-store-location-dataset"></a>Criar o conjunto de dados do lugar da loja

Antes de podermos desenvolver um aplicativo localizador de lojas, precisaremos criar um conjunto de dados das lojas que queremos exibir no mapa. Neste tutorial, usamos um conjunto de dados de uma cafeteria fictícia chamada Contoso Coffee. O conjunto de dados para esse localizador de lojas simples é gerenciado em uma pasta de trabalho do Excel. O conjunto de dados contém 10.213 lojas da Contoso Coffee espalhadas em nove países: Alemanha, Canadá, Dinamarca, Espanha, Estados Unidos, França, Itália, Países Baixos e Reino Unido. Aqui está uma captura de tela mostrando como devem ser os dados:

<br/>
<center>

![Captura de tela dos dados do localizador de lojas em uma pasta de trabalho do Excel](./media/tutorial-create-store-locator/StoreLocatorDataSpreadsheet.png)</center>

Você pode [baixar a pasta de trabalho do Excel](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). 

Observando a captura de tela dos dados, podemos fazer as seguintes observações:
    
* As informações de lugar são armazenadas usando as colunas **AddressLine**, **City**, **Municipality** (região), **AdminDivision** (estado/província), **PostCode** (CEP) e **Country**.  
* As colunas **Latitude** e **Longitude** contêm as coordenadas de cada loja da Contoso Coffee. Se você não tiver informações sobre as coordenadas, poderá usar os serviços de pesquisa do Azure Mapas para determinar as coordenadas de lugar.
* Algumas colunas adicionais contêm metadados relacionados às cafeterias: um número de telefone, colunas de Boolianos de pontos de acesso Wi-Fi e acessibilidade de cadeira de rodas e horários de abertura e fechamento em formato de 24 horas. Você pode criar suas próprias colunas com metadados que sejam mais relevantes para seus dados de localização.

> [!Note]
> O Azure Mapas processa dados na projeção Mercator esférica "EPSG:3857", mas lê os dados em "EPSG:4325" que usam a datum WGS84. 

Há muitas maneiras de expor o conjunto de dados ao aplicativo. Uma das abordagens é carregar os dados em um banco de dados e expor um serviço Web que consulta os dados e envia os resultados para o navegador do usuário. Essa opção é ideal para grandes conjuntos de dados ou para conjuntos de dados que são atualizados com frequência. No entanto, essa opção exige significativamente mais trabalho de desenvolvimento e tem um custo mais alto. 

Outra abordagem é converter esse conjunto de dados em um arquivo de texto simples que o navegador possa analisar com facilidade. O arquivo em si pode ser hospedado com o resto do aplicativo. Essa opção mantém as coisas simples, mas só é uma boa opção para conjuntos de dados menores, pois o usuário baixa todos os dados. Usamos o arquivo de texto simples para este conjunto de dados porque o arquivo de dados tem menos de 1 MB.  

Para converter a pasta de trabalho em um arquivo de texto simples, salve a pasta de trabalho como um arquivo delimitado por tabulação. Cada coluna é delimitada por um caractere de tabulação, o que facilita a análise das colunas em nosso código. Você pode usar o formato CSV (valores delimitados por vírgula), mas essa opção requer mais lógica de análise. Qualquer campo abrangido por vírgula seria encapsulado com aspas. Para exportar esses dados como um arquivo delimitado por tabulação no Excel, selecione **Salvar como**. Na lista suspensa **Salvar como tipo**, selecione **Texto (delimitado por tabulação)(*.txt)** . Nomeie o arquivo *ContosoCoffee.txt*. 

<br/>
<center>

![Captura de tela da caixa de diálogo Salvar como tipo](./media/tutorial-create-store-locator/SaveStoreDataAsTab.png)</center>

Se você abrir o arquivo de texto no Bloco de Notas, ele será mais ou menos assim:

<br/>
<center>

![Captura de tela de um arquivo do Bloco de Notas que mostra um conjunto de dados delimitados por tabulação](./media/tutorial-create-store-locator/StoreDataTabFile.png)</center>


## <a name="set-up-the-project"></a>Configurar o projeto

Para criar o projeto, você pode usar o [Visual Studio](https://visualstudio.microsoft.com) ou o editor de código de sua preferência. Na pasta do projeto, crie três arquivos: *index.html*, *CSS* e *index.js*. Esses arquivos definem o layout, o estilo e a lógica do aplicativo. Crie uma pasta chamada *data* e adicione *ContosoCoffee.txt* à pasta. Crie outra pasta chamada *images*. Usamos dez imagens neste aplicativo para os marcadores, botões e ícones do mapa. Você pode [baixar estas imagens](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). A pasta do projeto agora deve ficar mais ou menos assim:

<br/>
<center>

![Captura de tela da pasta do projeto do Localizador de Lojas Simples](./media/tutorial-create-store-locator/StoreLocatorVSProject.png)</center>

## <a name="create-the-user-interface"></a>Criar a interface do usuário

Para criar a interface do usuário, adicione código ao *index.html*:

1. Adicione as seguintes marcas `meta` ao `head` de *index.html*. As marcas definem o conjunto de caracteres (UTF-8), mandam o Internet Explorer e o Microsoft Edge usarem as versões mais recentes do navegador e especificam um visor que funciona bem com layouts dinâmicos.

    ```HTML
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="IE=Edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

1. Adicione referências para os arquivos CSS e JavaScript do controle Web do Azure Mapas:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

1. Adicione uma referência para o módulo dos serviços do Azure Mapas. O módulo é uma biblioteca JavaScript que encapsula os serviços REST do Azure Mapas e os torna fáceis de usar em JavaScript. O módulo é útil para viabilizar a funcionalidade de pesquisa.

    ```HTML
    <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
    ```

1. Adicione referências para *index.js* e *index.css*:

    ```HTML
    <link rel="stylesheet" href="index.css" type="text/css">
    <script src="index.js"></script>
    ```

1. No corpo do documento, adicione uma marca `header`. Na marca `header`, adicione o nome do logotipo e da empresa.

    ```HTML
    <header>
        <img src="images/Logo.png" />
        <span>Contoso Coffee</span>
    </header>
    ```

1. Adicione uma marca `main` e crie um painel de pesquisa que tenha um botão de pesquisa e uma caixa de texto. Além disso, adicione referências `div` para o mapa, o painel de lista e o botão de GPS Minha Localização.

    ```HTML
    <main>
        <div class="searchPanel">
            <div>
                <input id="searchTbx" type="search" placeholder="Find a store" />
                <button id="searchBtn" title="Search"></button>
            </div>
        </div>
        <div id="listPanel"></div>
        <div id="myMap"></div>
        <button id="myLocationBtn" title="My Location"></button>
    </main>
    ```

Quando tiver terminado, *index.html* deverá se parecer com [este arquivo index.html de exemplo](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/index.html).

A próxima etapa é definir os estilos da CSS. Os estilos da CSS definem como os componentes do aplicativo são dispostos e a aparência do aplicativo. Abra *index.css* e adicione código a seguir. O estilo `@media` define opções de estilo alternativas para uso quando a largura da tela é menor que 700 pixels.  

   ```CSS
    html, body {
        padding: 0;
        margin: 0;
        font-family: Gotham, Helvetica, sans-serif;
        overflow-x: hidden;
    }

    header {
        width: calc(100vw - 10px);
        height: 30px;
        padding: 15px 0 20px 20px;
        font-size: 25px;
        font-style: italic;
        font-family: "Comic Sans MS", cursive, sans-serif;
        line-height: 30px;
        font-weight: bold;
        color: white;
        background-color: #007faa;
    }

    header span {
        vertical-align: middle;
    }

    header img {
        height: 30px;
        vertical-align: middle;
    }

    .searchPanel {
        position: relative;
        width: 350px;
    }

    .searchPanel div {
        padding: 20px;
    }

    .searchPanel input {
        width: calc(100% - 50px);
        font-size: 16px;
        border: 0;
        border-bottom: 1px solid #ccc;
    }

    #listPanel {
        position: absolute;
        top: 135px;
        left: 0px;
        width: 350px;
        height: calc(100vh - 135px);
        overflow-y: auto;
    }

    #myMap { 
        position: absolute;
        top: 65px;
        left: 350px;
        width: calc(100vw - 350px);
        height: calc(100vh - 65px);
    }

    .statusMessage {
        margin: 10px;
    }

    #myLocationBtn, #searchBtn {
        margin: 0;
        padding: 0;
        border: none;
        border-collapse: collapse;
        width: 32px;
        height: 32px; 
        text-align: center;
        cursor: pointer;
        line-height: 32px;
        background-repeat: no-repeat;
        background-size: 20px;
        background-position: center center;
        z-index: 200;
    }

    #myLocationBtn {
        position: absolute;
        top: 150px;
        right: 10px;
        box-shadow: 0px 0px 4px rgba(0,0,0,0.16);
        background-color: white;
        background-image: url("images/GpsIcon.png");
    }

    #myLocationBtn:hover {
        background-image: url("images/GpsIcon-hover.png");
    }

    #searchBtn {
        background-color: transparent;
        background-image: url("images/SearchIcon.png");
    }

    #searchBtn:hover {
        background-image: url("images/SearchIcon-hover.png");
    }

    .listItem {
        height: 50px;
        padding: 20px;
        font-size: 14px;
    }

    .listItem:hover {
        cursor: pointer;
        background-color: #f1f1f1;
    }

    .listItem-title {
        color: #007faa;
        font-weight: bold;
    }

    .storePopup {
        min-width: 150px;
    }

    .storePopup .popupTitle {
        border-top-left-radius: 4px;
        border-top-right-radius: 4px;
        padding: 8px;
        height: 30px;
        background-color: #007faa;
        color: white;
        font-weight: bold;
    }

    .storePopup .popupSubTitle {
        font-size: 10px;
        line-height: 12px;
    }

    .storePopup .popupContent {
        font-size: 11px;
        line-height: 18px;
        padding: 8px;
    }

    .storePopup img {
        vertical-align:middle;
        height: 12px;
        margin-right: 5px;
    }

    /* Adjust the layout of the page when the screen width is less than 700 pixels. */
    @media screen and (max-width: 700px) {
        .searchPanel {
            width: 100vw;
        }

        #listPanel {
            top: 385px;
            width: 100%;
            height: calc(100vh - 385px);
        }

        #myMap {
            width: 100vw;
            height: 250px;
            top: 135px;
            left: 0px;
        }

        #myLocationBtn {
            top: 220px;
        }
    }

    .mapCenterIcon {
        display: block;
        width: 10px;
        height: 10px;
        border-radius: 50%;
        background: orange;
        border: 2px solid white;
        cursor: pointer;
        box-shadow: 0 0 0 rgba(0, 204, 255, 0.4);
        animation: pulse 3s infinite;
    }

    @keyframes pulse {
        0% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0.4);
        }

        70% {
            box-shadow: 0 0 0 50px rgba(0, 204, 255, 0);
        }

        100% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0);
        }
    }
   ```

Se você executar o aplicativo agora, verá o cabeçalho, a caixa de pesquisa e o botão de pesquisa, mas o mapa não estará visível porque ainda não foi carregado. Se você tentar fazer uma pesquisa, nada acontecerá. Precisamos configurar a lógica de JavaScript descrita na próxima seção para acessar toda a funcionalidade do localizador de lojas.

## <a name="wire-the-application-with-javascript"></a>Conectar o aplicativo com JavaScript

Até aqui, tudo está configurado na interface do usuário. Agora, precisamos adicionar o JavaScript para carregar e analisar os dados e, em seguida, renderizar os dados no mapa. Para começar, abra *index.js* e adicione código a ele conforme descrito nas etapas a seguir.

1. Adicione opções globais para facilitar a atualização das configurações. Além disso, defina as variáveis para o mapa, uma janela pop-up, uma fonte de dados, uma camada de ícone, um marcador de HTML que exibe o centro de uma área de pesquisa e uma instância do cliente do serviço de pesquisa do Azure Mapas.

    ```JavaScript
    //The maximum zoom level to cluster data point data on the map.
    var maxClusterZoomLevel = 11;

    //The URL to the store location data.
    var storeLocationDataUrl = 'data/ContosoCoffee.txt';

    //The URL to the icon image.
    var iconImageUrl = 'images/CoffeeIcon.png';
    var map, popup, datasource, iconLayer, centerMarker, searchURL;
    ```

1. Adicione código ao *index.js*. O código a seguir inicializa o mapa, adiciona um [ouvinte de evento](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) que aguarda até que a página termine de carregar, conecta eventos para monitorar o carregamento do mapa e habilita o botão de pesquisa e o botão Minha Localização.

   Quando o usuário seleciona o botão de pesquisa, ou quando ele pressiona Enter depois de inserir um lugar na caixa de pesquisa, uma pesquisa difusa é iniciada em relação à consulta do usuário. Transmita uma matriz de valores de país ISO 2 para a opção `countrySet` a fim de limitar os resultados da pesquisa a esses países/regiões. Limitar os países/regiões a serem pesquisados ajuda a aumentar a precisão dos resultados retornados. 
  
   Quando a pesquisa for concluída, use o primeiro resultado e defina a câmera do mapa sobre essa área. Quando o usuário seleciona o botão Minha Localização, use a API de Localização Geográfica HTML5 que está incorporada no navegador para recuperar a localização do usuário e centralizar o mapa sobre ela.  

   > [!Tip]
   > Quando você usa janelas pop-up, é melhor criar uma instância de `Popup` única e reutilizá-la atualizando seu conteúdo e posição. Para cada instância de `Popup` que você adicionar ao seu código, vários elementos de DOM serão adicionados à página. Quanto mais elementos de DOM houver em uma página, mais itens o navegador terá que controlar. Se houver muitos itens, o navegador poderá ficar lento.

    ```JavaScript
    function initialize() {
        //Initialize a map instance.
        map = new atlas.Map('myMap', {
            center: [-90, 40],
            zoom: 2,

            //Add your Azure Maps subscription key to the map SDK.
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });

        //Create a pop-up window, but leave it closed so we can update it and display it later.
        popup = new atlas.Popup();

        //Use SubscriptionKeyCredential with a subscription key
        const subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

        //Use subscriptionKeyCredential to create a pipeline
        const pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
            retryOptions: { maxTries: 4 } // Retry options
        });

        //Create an instance of the SearchURL client.
        searchURL = new atlas.service.SearchURL(pipeline);

        //If the user selects the search button, geocode the value the user passed in.
        document.getElementById('searchBtn').onclick = performSearch;

        //If the user presses Enter in the search box, perform a search.
        document.getElementById('searchTbx').onkeyup = function(e) {
            if (e.keyCode === 13) {
                performSearch();
            }
        };

        //If the user selects the My Location button, use the Geolocation API to get the user's location. Center and zoom the map on that location.
        document.getElementById('myLocationBtn').onclick = setMapToUserLocation;

        //Wait until the map resources are ready.
        map.events.add('ready', function() {

            //Add your post-map load functionality.

        });
    }

    //Create an array of country ISO 2 values to limit searches to. 
    var countrySet = ['US', 'CA', 'GB', 'FR','DE','IT','ES','NL','DK'];

    function performSearch() {
        var query = document.getElementById('searchTbx').value;

        //Perform a fuzzy search on the users query.
        searchURL.searchFuzzy(atlas.service.Aborter.timeout(3000), query, {
            //Pass in the array of country ISO2 for which we want to limit the search to.
            countrySet: countrySet
        }).then(results => {
            //Parse the response into GeoJSON so that the map can understand.
            var data = results.geojson.getFeatures();

            if (data.features.length > 0) {
                //Set the camera to the bounds of the results.
                map.setCamera({
                    bounds: data.features[0].bbox,
                    padding: 40
                });
            } else {
                document.getElementById('listPanel').innerHTML = '<div class="statusMessage">Unable to find the location you searched for.</div>';
            }
        });
    }

    function setMapToUserLocation() {
        //Request the user's location.
        navigator.geolocation.getCurrentPosition(function(position) {
            //Convert the Geolocation API position to a longitude and latitude position value that the map can interpret and center the map over it.
            map.setCamera({
                center: [position.coords.longitude, position.coords.latitude],
                zoom: maxClusterZoomLevel + 1
            });
        }, function(error) {
            //If an error occurs when the API tries to access the user's position information, display an error message.
            switch (error.code) {
                case error.PERMISSION_DENIED:
                    alert('User denied the request for geolocation.');
                    break;
                case error.POSITION_UNAVAILABLE:
                    alert('Position information is unavailable.');
                    break;
                case error.TIMEOUT:
                    alert('The request to get user position timed out.');
                    break;
                case error.UNKNOWN_ERROR:
                    alert('An unknown error occurred.');
                    break;
            }
        });
    }

    //Initialize the application when the page is loaded.
    window.onload = initialize;
    ```

1. No ouvinte de evento `ready` do mapa, adicione um controle de zoom e um marcador de HTML para exibir o centro de uma área de pesquisa.

    ```JavaScript
    //Add a zoom control to the map.
    map.controls.add(new atlas.control.ZoomControl(), {
        position: 'top-right'
    });

    //Add an HTML marker to the map to indicate the center to use for searching.
    centerMarker = new atlas.HtmlMarker({
        htmlContent: '<div class="mapCenterIcon"></div>',
        position: map.getCamera().center
    });

    map.markers.add(centerMarker);
    ```

1. No ouvinte de evento `ready` do mapa, adicione uma fonte de dados. Em seguida, faça uma chamada de carregamento e analise o conjunto de dados. Habilite o clustering na fonte de dados. Clustering dos pontos sobrepostos dos grupos da fonte de dados em um cluster. Os clusters se dividem em pontos individuais quando o usuário amplia o mapa. Isso torna a experiência do usuário mais fluida e melhora o desempenho.

    ```JavaScript
    //Create a data source, add it to the map, and then enable clustering.
    datasource = new atlas.source.DataSource(null, {
        cluster: true,
        clusterMaxZoom: maxClusterZoomLevel - 1
    });

    map.sources.add(datasource);

    //Load all the store data now that the data source is defined.  
    loadStoreData();
    ```

1. Depois de carregar o conjunto de dados no ouvinte de evento `ready` do mapa, defina um conjunto de camadas para renderizar os dados. Uma camada de bolha é usada para renderizar os pontos de dados em cluster. Uma camada de símbolo é usada para renderizar o número de pontos em cada cluster acima da camada de bolha. Uma segunda camada de símbolo renderiza um ícone personalizado de lugares individuais no mapa.

   Adicione os eventos `mouseover` e `mouseout` às camadas de bolha e de ícone para alterar o cursor do mouse quando o usuário passa o mouse sobre um ícone ou cluster no mapa. Adicione um evento `click` à camada de bolha do cluster. Esse evento `click` amplia o mapa em dois níveis e centraliza o mapa em um cluster quando o usuário seleciona um cluster. Adicione um evento `click` à camada de ícone. Esse evento `click` exibe uma janela pop-up que mostra os detalhes de uma cafeteria quando um usuário seleciona um ícone de localização individual. Adicione um evento ao mapa para monitorar quando o mapa para de se movimentar. Quando esse evento é acionado, atualize os itens no painel de listas.  

    ```JavaScript
    //Create a bubble layer to render clustered data points.
    var clusterBubbleLayer = new atlas.layer.BubbleLayer(datasource, null, {
        radius: 12,
        color: '#007faa',
        strokeColor: 'white',
        strokeWidth: 2,
        filter: ['has', 'point_count'] //Only render data points that have a point_count property; clusters have this property.
    });

    //Create a symbol layer to render the count of locations in a cluster.
    var clusterLabelLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none' //Hide the icon image.
        },

        textOptions: {
            textField: ['get', 'point_count_abbreviated'],
            size: 12,
            font: ['StandardFont-Bold'],
            offset: [0, 0.4],
            color: 'white'
        }
    });

    map.layers.add([clusterBubbleLayer, clusterLabelLayer]);

    //Load a custom image icon into the map resources.
    map.imageSprite.add('myCustomIcon', iconImageUrl).then(function() {

    //Create a layer to render a coffee cup symbol above each bubble for an individual location.
    iconLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            //Pass in the ID of the custom icon that was loaded into the map resources.
            image: 'myCustomIcon',

            //Optionally, scale the size of the icon.
            font: ['SegoeUi-Bold'],

            //Anchor the center of the icon image to the coordinate.
            anchor: 'center',

            //Allow the icons to overlap.
            allowOverlap: true
        },

        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
    });

    map.layers.add(iconLayer);

    //When the mouse is over the cluster and icon layers, change the cursor to a pointer.
    map.events.add('mouseover', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'pointer';
    });

    //When the mouse leaves the item on the cluster and icon layers, change the cursor back to the default (grab).
    map.events.add('mouseout', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'grab';
    });

    //Add a click event to the cluster layer. When the user selects a cluster, zoom into it by two levels.  
    map.events.add('click', clusterBubbleLayer, function(e) {
        map.setCamera({
            center: e.position,
            zoom: map.getCamera().zoom + 2
        });
    });

    //Add a click event to the icon layer and show the shape that was selected.
    map.events.add('click', iconLayer, function(e) {
        showPopup(e.shapes[0]);
    });

    //Add an event to monitor when the map is finished rendering the map after it has moved.
    map.events.add('render', function() {
        //Update the data in the list.
        updateListItems();
    });
    ```

1. Quando o conjunto de dados da cafeteria é carregado, ele deve ser baixado primeiro. Em seguida, o arquivo de texto deve ser dividido em linhas. A primeira linha contém as informações de cabeçalho. Para facilitar o entendimento do código a seguir, vamos analisar o cabeçalho em um objeto, que podemos usar para pesquisar o índice de célula de cada propriedade. Após a primeira linha, percorra as linhas restantes e crie um recurso de ponto. Adicione o recurso de ponto à fonte de dados. Por fim, atualize o painel de lista.

    ```JavaScript
    function loadStoreData() {

    //Download the store location data.
    fetch(storeLocationDataUrl)
        .then(response => response.text())
        .then(function(text) {

            //Parse the tab-delimited file data into GeoJSON features.
            var features = [];

            //Split the lines of the file.
            var lines = text.split('\n');

            //Grab the header row.
            var row = lines[0].split('\t');

            //Parse the header row and index each column to make the code for parsing each row easier to follow.
            var header = {};
            var numColumns = row.length;
            for (var i = 0; i < row.length; i++) {
                header[row[i]] = i;
            }

            //Skip the header row and then parse each row into a GeoJSON feature.
            for (var i = 1; i < lines.length; i++) {
                row = lines[i].split('\t');

                //Ensure that the row has the correct number of columns.
                if (row.length >= numColumns) {

                    features.push(new atlas.data.Feature(new atlas.data.Point([parseFloat(row[header['Longitude']]), parseFloat(row[header['Latitude']])]), {
                        AddressLine: row[header['AddressLine']],
                        City: row[header['City']],
                        Municipality: row[header['Municipality']],
                        AdminDivision: row[header['AdminDivision']],
                        Country: row[header['Country']],
                        PostCode: row[header['PostCode']],
                        Phone: row[header['Phone']],
                        StoreType: row[header['StoreType']],
                        IsWiFiHotSpot: (row[header['IsWiFiHotSpot']].toLowerCase() === 'true') ? true : false,
                        IsWheelchairAccessible: (row[header['IsWheelchairAccessible']].toLowerCase() === 'true') ? true : false,
                        Opens: parseInt(row[header['Opens']]),
                        Closes: parseInt(row[header['Closes']])
                    }));
                }
            }

            //Add the features to the data source.
            datasource.add(new atlas.data.FeatureCollection(features));

            //Initially, update the list items.
            updateListItems();
        });
    }
    ```

1. Quando o painel de lista é atualizado, a distância do centro do mapa para todos os recursos de ponto na exibição do mapa atual é calculada. Os recursos, em seguida, são classificados pela distância. HTML é gerado para exibir cada local no painel de lista.

    ```JavaScript
    var listItemTemplate = '<div class="listItem" onclick="itemSelected(\'{id}\')"><div class="listItem-title">{title}</div>{city}<br />Open until {closes}<br />{distance} miles away</div>';

    function updateListItems() {
        //Hide the center marker.
        centerMarker.setOptions({
            visible: false
        });

        //Get the current camera and view information for the map.
        var camera = map.getCamera();
        var listPanel = document.getElementById('listPanel');

        //Check to see whether the user is zoomed out a substantial distance. If they are, tell the user to zoom in and to perform a search or select the My Location button.
        if (camera.zoom < maxClusterZoomLevel) {
            //Close the pop-up window; clusters might be displayed on the map.  
            popup.close(); 
            listPanel.innerHTML = '<div class="statusMessage">Search for a location, zoom the map, or select the My Location button to see individual locations.</div>';
        } else {
            //Update the location of the centerMarker property.
            centerMarker.setOptions({
                position: camera.center,
                visible: true
            });

            //List the ten closest locations in the side panel.
            var html = [], properties;

            /*
            Generating HTML for each item that looks like this:
            <div class="listItem" onclick="itemSelected('id')">
                <div class="listItem-title">1 Microsoft Way</div>
                Redmond, WA 98052<br />
                Open until 9:00 PM<br />
                0.7 miles away
            </div>
            */

            //Get all the shapes that have been rendered in the bubble layer. 
            var data = map.layers.getRenderedShapes(map.getCamera().bounds, [iconLayer]);

            //Create an index of the distances of each shape.
            var distances = {};

            data.forEach(function (shape) {
                if (shape instanceof atlas.Shape) {

                    //Calculate the distance from the center of the map to each shape and store in the index. Round to 2 decimals.
                    distances[shape.getId()] = Math.round(atlas.math.getDistanceTo(camera.center, shape.getCoordinates(), 'miles') * 100) / 100;
                }
            });

            //Sort the data by distance.
            data.sort(function (x, y) {
                return distances[x.getId()] - distances[y.getId()];
            });

            data.forEach(function(shape) {
                properties = shape.getProperties();
                html.push('<div class="listItem" onclick="itemSelected(\'', shape.getId(), '\')"><div class="listItem-title">',
                properties['AddressLine'],
                '</div>',
                //Get a formatted addressLine2 value that consists of City, Municipality, AdminDivision, and PostCode.
                getAddressLine2(properties),
                '<br />',

                //Convert the closing time to a format that is easier to read.
                getOpenTillTime(properties),
                '<br />',

                //Get the distance of the shape.
                distances[shape.getId()],
                ' miles away</div>');
            });

            listPanel.innerHTML = html.join('');

            //Scroll to the top of the list panel in case the user has scrolled down.
            listPanel.scrollTop = 0;
        }
    }

    //This converts a time that's in a 24-hour format to an AM/PM time or noon/midnight string.
    function getOpenTillTime(properties) {
        var time = properties['Closes'];
        var t = time / 100;
        var sTime;

        if (time === 1200) {
            sTime = 'noon';
        } else if (time === 0 || time === 2400) {
            sTime = 'midnight';
        } else {
            sTime = Math.round(t) + ':';

            //Get the minutes.
            t = (t - Math.round(t)) * 100;

            if (t === 0) {
                sTime += '00';
            } else if (t < 10) {
                sTime += '0' + t;
            } else {
                sTime += Math.round(t);
            }

            if (time < 1200) {
                sTime += ' AM';
            } else {
                sTime += ' PM';
            }
        }

        return 'Open until ' + sTime;
    }

    //Create an addressLine2 string that contains City, Municipality, AdminDivision, and PostCode.
    function getAddressLine2(properties) {
        var html = [properties['City']];

        if (properties['Municipality']) {
            html.push(', ', properties['Municipality']);
        }

        if (properties['AdminDivision']) {
            html.push(', ', properties['AdminDivision']);
        }

        if (properties['PostCode']) {
            html.push(' ', properties['PostCode']);
        }

        return html.join('');
    }
    ```

1. Quando o usuário seleciona um item no painel de lista, a forma à qual o item está relacionado é recuperada da fonte de dados. Uma janela pop-up é gerada com base nas informações de propriedade armazenadas na forma. O mapa é centralizado sobre a forma. Se o mapa tiver menos de 700 pixels de largura, a exibição do mapa será deslocada para que a janela pop-up fique visível.

    ```JavaScript
    //When a user selects a result in the side panel, look up the shape by its ID value and display the pop-up window.
    function itemSelected(id) {
        //Get the shape from the data source by using its ID.  
        var shape = datasource.getShapeById(id);
        showPopup(shape);

        //Center the map over the shape on the map.
        var center = shape.getCoordinates();
        var offset;

        //If the map is less than 700 pixels wide, then the layout is set for small screens.
        if (map.getCanvas().width < 700) {
            //When the map is small, offset the center of the map relative to the shape so that there is room for the popup to appear.
            offset = [0, -80];
        }

        map.setCamera({
            center: center,
            centerOffset: offset
        });
    }

    function showPopup(shape) {
        var properties = shape.getProperties();

        /* Generating HTML for the pop-up window that looks like this:

            <div class="storePopup">
                <div class="popupTitle">
                    3159 Tongass Avenue
                    <div class="popupSubTitle">Ketchikan, AK 99901</div>
                </div>
                <div class="popupContent">
                    Open until 22:00 PM<br/>
                    <img title="Phone Icon" src="images/PhoneIcon.png">
                    <a href="tel:1-800-XXX-XXXX">1-800-XXX-XXXX</a>
                    <br>Amenities:
                    <img title="Wi-Fi Hotspot" src="images/WiFiIcon.png">
                    <img title="Wheelchair Accessible" src="images/WheelChair-small.png">
                </div>
            </div>
        */

         //Calculate the distance from the center of the map to the shape in miles, round to 2 decimals.
        var distance = Math.round(atlas.math.getDistanceTo(map.getCamera().center, shape.getCoordinates(), 'miles') * 100)/100;

        var html = ['<div class="storePopup">'];
        html.push('<div class="popupTitle">',
            properties['AddressLine'],
            '<div class="popupSubTitle">',
            getAddressLine2(properties),
            '</div></div><div class="popupContent">',

            //Convert the closing time to a format that's easier to read.
            getOpenTillTime(properties),

            //Add the distance information.  
            '<br/>', distance,
            ' miles away',
            '<br /><img src="images/PhoneIcon.png" title="Phone Icon"/><a href="tel:',
            properties['Phone'],
            '">',  
            properties['Phone'],
            '</a>'
        );

        if (properties['IsWiFiHotSpot'] || properties['IsWheelchairAccessible']) {
            html.push('<br/>Amenities: ');

            if (properties['IsWiFiHotSpot']) {
                html.push('<img src="images/WiFiIcon.png" title="Wi-Fi Hotspot"/>');
            }

            if (properties['IsWheelchairAccessible']) {
                html.push('<img src="images/WheelChair-small.png" title="Wheelchair Accessible"/>');
            }
        }

        html.push('</div></div>');

        //Update the content and position of the pop-up window for the specified shape information.
        popup.setOptions({

            //Create a table from the properties in the feature.
            content:  html.join(''),
            position: shape.getCoordinates()
        });

        //Open the pop-up window.
        popup.open(map);
    }
    ```

Agora você tem um localizador de lojas totalmente funcional. Em um navegador da Web, abra o arquivo *index.html* para o localizador de lojas. Quando os clusters são exibidos no mapa, você pode procurar um lugar usando a caixa de pesquisa, selecionando o botão Minha Localização, selecionando um cluster ou ampliando o mapa para ver lugares individualmente.

Na primeira vez em que um usuário seleciona o botão Minha Localização, o navegador exibe um aviso de segurança que solicita permissão para acessar a localização do usuário. Se o usuário concordar em compartilhar sua localização, o mapa ampliará a localização do usuário e as cafeterias próximas serão exibidas. 

<br/>
<center>

![Captura de tela da solicitação do navegador para acessar o local do usuário](./media/tutorial-create-store-locator/GeolocationApiWarning.png)</center>

Quando você ampliar o suficiente em uma área com cafeterias, os clusters se dividem em lugares individuais. Selecione um dos ícones no mapa ou selecione um item no painel lateral para ver uma janela pop-up que mostra informações sobre esse lugar.

<br/>
<center>

![Captura de tela do localizador de lojas concluído](./media/tutorial-create-store-locator/FinishedSimpleStoreLocator.png)</center>

Se você redimensionar a janela do navegador para menos de 700 pixels de largura ou abrir o aplicativo em um dispositivo móvel, o layout muda para se adequar melhor a telas menores. 

<br/>
<center>

![Captura de tela da versão do localizador de lojas para telas pequenas](./media/tutorial-create-store-locator/FinishedSimpleStoreLocatorSmallScreen.png)</center>

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como criar um localizador de lojas básico usando o Azure Mapas. O localizador de lojas que você criou neste tutorial pode ter todas as funcionalidades desejadas. Você pode adicionar recursos ao seu localizador de lojas ou usar recursos mais avançados para uma experiência do usuário mais personalizada: 

> [!div class="checklist"]
> * Habilite [sugestões enquanto você digita](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20Autosuggest%20and%20JQuery%20UI) na caixa de pesquisa.  
> * Adicione [suporte a vários idiomas](https://azuremapscodesamples.azurewebsites.net/?sample=Map%20Localization). 
> * Permita que o usuário [filtre por lugares ao longo de uma rota](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Data%20Along%20Route). 
> * Adicione a capacidade de [definir filtros](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Symbols%20by%20Property). 
> * Adicione suporte para especificar um valor inicial de pesquisa usando uma cadeia de caracteres de consulta. Quando você incluir essa opção no localizador de lojas, os usuários poderão salvar e compartilhar pesquisas. Ela também fornece um método fácil para que você possa passar pesquisas de outra página para essa.  
> * Implante o localizador de lojas como um [aplicativo Web do Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html). 
> * Armazene seus dados em um banco de dados e pesquise lugares mais próximos. Para obter mais informações, consulte a [Visão geral de tipos de dados espaciais do SQL Server](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-types-overview?view=sql-server-2017) e [Consultar dados espaciais sobre o vizinho mais próximo](https://docs.microsoft.com/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?view=sql-server-2017).

> [!div class="nextstepaction"]
> [Exibir código-fonte completo](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)

> [!div class="nextstepaction"]
> [Exibir amostra dinâmica](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Simple%20Store%20Locator)

Para saber mais sobre a cobertura e as funcionalidades do Azure Mapas:

> [!div class="nextstepaction"]
> [Níveis de zoom e grade lado a lado](zoom-levels-and-tile-grid.md)

Para ver mais exemplos de código e uma experiência interativa de codificação:

> [!div class="nextstepaction"]
> [Como usar o Controle de Mapeamento](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)