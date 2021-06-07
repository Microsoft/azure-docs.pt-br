---
title: Tutorial – Migrar um aplicativo Web do Google Maps para o Microsoft Azure Mapas
description: Tutorial sobre como migrar um aplicativo Web do Google Maps para o Microsoft Azure Mapas
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: devx-track-js
ms.openlocfilehash: fcb8090427530271600a6699fafa5c488c426784
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680875"
---
# <a name="tutorial-migrate-a-web-app-from-google-maps"></a>Tutorial: Migrar um aplicativo Web do Google Maps

A maioria dos aplicativos Web que usa o Google Maps usa o SDK do Google Maps V3 para JavaScript. O SDK da Web do Azure Mapas é o SDK baseado no Azure adequado para o qual migrar. O SDK da Web dos Azure Mapas permite que você personalize mapas interativos com as imagens e o conteúdo próprios. Você pode executar seu aplicativo em aplicativos Web ou móveis. Esse controle utiliza WebGL, permitindo que você processe grandes conjuntos de dados com alto desempenho. Desenvolva com esse SDK usando JavaScript ou TypeScript. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Carregar um mapa
> * Localizar um mapa
> * Adicionar marcadores, polilinhas e polígonos.
> * Exibir informações em um pop-up ou uma janela de informações
> * Carregar e exibir dados KML e GeoJSON
> * Marcadores de clusters
> * Sobrepor uma camada de peça
> * Mostrar dados de tráfego
> * Adicionar uma sobreposição de piso

Você também aprenderá:

> [!div class="checklist"]
> * Como realizar tarefas comuns de mapeamento usando o SDK Web do Azure Mapas.
> * Melhores práticas para aprimorar o desempenho e a experiência do usuário.
> * Dicas sobre como criar seu aplicativo usando recursos mais avançados e disponíveis no Azure Mapas.

Se estiver migrando um aplicativo Web existente, verifique se ele está usando uma biblioteca de controle de mapeamento de software livre. Exemplos de biblioteca de controle de mapeamento de software livre são: Cesium, Leaflet e OpenLayers. Você ainda poderá migrar o seu aplicativo, mesmo que ele use uma biblioteca de controle de mapeamento de software livre e você não queira usar o SDK da Web dos Azure Mapas. Nesse caso, conecte o aplicativo aos serviços de peças dos Azure Mapas ([peças de estrada](/rest/api/maps/render/getmaptile) \| [peças de satélite](/rest/api/maps/render/getmapimagerytile)). Os pontos a seguir fornecem detalhes de como usar os Azure Mapas em algumas bibliotecas de controle de mapeamento de software livre comumente usadas.

* Cesium – um controle de mapeamento 3D para a Web. [Exemplo de código](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [Documentação](https://cesiumjs.org/)
* Leaflet – controle de mapeamento 2D leve para a Web. [Exemplo de código](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [Documentação](https://leafletjs.com/)
* OpenLayers – controle de mapeamento 2D para a Web compatível com projeções. [Exemplo de código](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [Documentação](https://openlayers.org/)

Se você estiver desenvolvendo com uma estrutura JavaScript, um dos seguintes projetos open-source pode ser útil:

* [ng-azure-maps](https://github.com/arnaudleclerc/ng-azure-maps) – um wrapper Angular 10 alternativo nos mapas do Azure.
* [AzureMapsControl.Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) – um componente Blazor dos Azure Mapas.
* [Componente React do Azure Mapas](https://github.com/WiredSolutions/react-azure-maps) – um wrapper React para o controle dos Azure Mapas.
* [Vue Azure Mapas](https://github.com/rickyruiz/vue-azure-maps) – um componente dos Azure Mapas para o aplicativo Vue.

## <a name="prerequisites"></a>Pré-requisitos

1. Entre no [portal do Azure](https://portal.azure.com). Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
2. [Faça uma conta do Azure Mapas](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Obtenha uma chave de assinatura primária](quick-demo-map-app.md#get-the-primary-key-for-your-account), também conhecida como a chave primária ou a chave de assinatura. Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](how-to-manage-authentication.md).

## <a name="key-features-support"></a>Suporte aos principais recursos

A tabela lista os principais recursos da API no SDK do Google Maps V3 para JavaScript e o recurso de API compatível com o SDK Web do Azure Mapas.

| Recurso do Google Maps     | Suporte do SDK da Web do Azure Mapas |
|-------------------------|:--------------------------:|
| Marcadores                 | ✓                          |
| Clustering de marcadores       | ✓                          |
| Polilinhas e polígonos    | ✓                          |
| Camadas de dados             | ✓                          |
| Sobreposições de piso         | ✓                          |
| Mapas de calor               | ✓                          |
| Camadas de bloco             | ✓                          |
| Camada KML               | ✓                          |
| Ferramentas de desenho           | ✓                          |
| Serviço geocodificador        | ✓                          |
| Serviço de instruções      | ✓                          |
| Serviço da matriz de distância | ✓                          |
| Serviço de elevação       | ✓                          |

## <a name="notable-differences-in-the-web-sdks"></a>Diferenças notáveis nos SDKs da Web

Veja algumas das principais diferenças entre os SDKs Web do Google Maps e do Azure Mapas que você deve conhecer:

- Além de fornecer um ponto de extremidade hospedado para acessar o SDK Web do Azure Mapas, há um pacote NPM disponível. Insira o pacote do SDK Web em aplicativos. Para obter mais informações, confira esta [documentação](how-to-use-map-control.md). Esse pacote também inclui definições de TypeScript.
- Primeiro, você precisa criar uma instância da classe Map no Azure Mapas. Aguarde até que o evento `ready` ou `load` dos mapas sejam acionados antes de interagir de forma programática com o mapa. Essa ordem garantirá que todos os recursos de mapa tenham sido carregados e estejam prontos para serem acessados.
- Ambas as plataformas usam um sistema de organização semelhante para os mapas base. As peças no Google Maps têm dimensão de 256 pixels; no entanto, as peças no Azure Mapas têm dimensão de 512 pixels. Para obter no Azure Mapas a mesma exibição de mapa que se obtém no Google Maps, subtraia o nível de zoom do Google Maps pelo número um no Azure Mapas.
- As coordenadas no Google Maps são chamadas de `latitude,longitude`, enquanto o Azure Mapas usa `longitude,latitude`. O formato do Azure Mapas está alinhado com o padrão `[x, y]`, seguido pela maioria das plataformas GIS.
- As formas no SDK da Web do Azure Mapas são baseadas no esquema GeoJSON. As classes auxiliares são expostas por meio do [namespace *atlas.data*](/javascript/api/azure-maps-control/atlas.data). Há também a classe [*atlas.Shape*](/javascript/api/azure-maps-control/atlas.shape). Use essa classe para encapsular objetos GeoJSON a fim de facilitar a atualização e manter os dados de forma associável.
- As coordenadas nos Azure Mapas são definidas como objetos Position. Uma coordenada é especificada como uma matriz numérica no formato `[longitude,latitude]`. Ou, ela é especificada usando a nova atlas.data.Position(longitude, latitude).
    > [!TIP]
    > A classe Position tem um método auxiliar estático para importar coordenadas que estão no formato "latitude, longitude". O método [atlas.data.Position.fromLatLng](/javascript/api/azure-maps-control/atlas.data.position) pode, muitas vezes, ser substituído pelo método `new google.maps.LatLng` no código do Google Maps.
- Em vez de especificar informações de estilo em cada forma adicionada ao mapa, o Azure Mapas separa os estilos dos dados. Os dados são armazenados em uma fonte de dados e estão conectados às camadas de renderização. O código dos Azure Mapas usa fontes de dados para renderizar os dados. Essa abordagem oferece o benefício de desempenho aprimorado. Além disso, muitas camadas dão suporte ao estilo controlado por dados, em que a lógica de negócios pode ser adicionada às opções de estilo de camada. Esse suporte altera como as formas individuais são renderizadas em uma camada com base nas propriedades definidas na forma.

## <a name="web-sdk-side-by-side-examples"></a>Exemplos lado a lado do SDK da Web

Esta coleção traz exemplos de código para cada plataforma e cada um deles abrange um caso de uso comum. Ela destina-se a ajudar você a migrar o seu aplicativo Web do SDK do Google Maps V3 para JavaScript para o SDK da Web dos Azure Mapas. Exemplos de código relacionados a aplicativos Web são fornecidos em JavaScript. No entanto, os Azure Mapas também fornecem definições de TypeScript como uma opção adicional por meio de um [módulo npm](how-to-use-map-control.md).

**Tópicos**

* [Carregar um mapa](#load-a-map)
* [Como localizar o mapa](#localizing-the-map)
* [Como configurar a exibição do mapa](#setting-the-map-view)
* [Adicionar um marcador](#adding-a-marker)
* [Adicionar um marcador personalizado](#adding-a-custom-marker)
* [Como adicionar uma polilinha](#adding-a-polyline)
* [Como adicionar um polígono](#adding-a-polygon)
* [Exibir a janela Informações](#display-an-info-window)
* [Importar um arquivo GeoJSON](#import-a-geojson-file)* 
* [Clustering de marcadores](#marker-clustering)
* [Adicionar um mapa de calor](#add-a-heat-map)
* [Sobrepor uma camada de peça](#overlay-a-tile-layer)
* [Mostrar dados de tráfego](#show-traffic-data)
* [Adicionar uma sobreposição de piso](#add-a-ground-overlay)
* [Adicionar dados KML ao mapa](#add-kml-data-to-the-map)

### <a name="load-a-map"></a>Carregar um mapa

Ambos os SDKs têm as mesmas etapas para carregar um mapa:

* Adicione uma referência ao SDK do Mapa.
* Adicione uma marca `div` ao corpo da página, que funcionará como um espaço reservado para o mapa.
* Crie uma função JavaScript chamada quando a página é carregada.
* Crie uma instância da respectiva classe de mapa.

**Algumas das principais diferenças**

* O Google Maps exige que uma chave de conta seja especificada na referência de script da API. As credenciais de autenticação para o Azure Mapas são especificadas como opções da classe do mapa. Essas credenciais podem ser uma chave de assinatura ou informações do Azure Active Directory.
* O Google Maps aceita uma função de retorno de chamada na referência de script da API, que é usada para chamar uma função de inicialização para carregar o mapa. Com o Azure Mapas, o evento OnLoad da página deve ser usado.
* Ao fazer referência ao elemento `div` no qual o mapa será renderizado, a classe `Map` no Azure Mapas requer apenas o valor `id`, enquanto o Google Maps requer um objeto `HTMLElement`.
* As coordenadas nos Azure Mapas são definidas como objetos Position, que podem ser especificados como uma matriz de número simples no formato `[longitude, latitude]`.
* O nível de zoom nos Azure Mapas é um nível inferior ao nível de zoom no Google Maps. Essa discrepância é devida à diferença nos tamanhos do sistema de organização das duas plataformas.
* Os Azure Mapas não adicionam nenhum controle de navegação à tela do mapa. Portanto, por padrão, um mapa não tem botões de zoom nem botões de estilo de mapa. No entanto, há opções de controle para adição de seletor de estilo de mapa, botões de zoom, controle de bússola ou rotação e controle de densidade.
* Um manipulador de eventos é adicionado ao Azure Mapas para monitorar o evento `ready` da instância do mapa. Esse evento será disparado quando o mapa concluir o carregamento do contexto do WebGL e todos os recursos necessários. Adicione a esse manipulador de eventos qualquer código que desejar executar depois que o mapa concluir o carregamento.

Os exemplos básicos abaixo usam o Google Maps para carregar um mapa centralizado em Nova York em determinadas coordenadas. A longitude: -73,985, a latitude: 40,747 e o mapa está no nível de zoom igual a 12.

#### <a name="before-google-maps"></a>Antes: Google Maps

Exibir um mapa do Google centralizado e ampliado em um local.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Executar esse código em um navegador exibirá um mapa semelhante à imagem a seguir:

![Google Maps simples](media/migrate-google-maps-web-app/simple-google-map.png)

#### <a name="after-azure-maps"></a>Depois: Azure Mapas

Carregar um mapa com a mesma exibição no Azure Mapas, juntamente com um controle de estilo de mapa e botões de zoom.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Specify authentication information when loading the map.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom controls to bottom right of map.
                map.controls.add(new atlas.control.ZoomControl(), {
                    position: 'bottom-right'
                });

                //Add map style control in top left corner of map.
                map.controls.add(new atlas.control.StyleControl(), {
                    position: 'top-left'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Executar esse código em um navegador exibirá um mapa semelhante à imagem a seguir:

![Tutorial simples para Azure Mapas](media/migrate-google-maps-web-app/simple-azure-maps.png)

Encontre documentação detalhada de como configurar e usar o controle de mapa do Azure Mapas em um aplicativo Web clicando [aqui](how-to-use-map-control.md).

> [!NOTE]
> Ao contrário do Google Maps, o Azure Mapas não exige um centro inicial e um nível de zoom para carregar o mapa. Se essas informações não forem fornecidas ao carregar o mapa, o Azure Mapas tentará determinar a cidade do usuário. Ele centralizará e aplicará zoom no mapa nesse local.

**Recursos adicionais:**

* O Azure Mapas também fornece controles de navegação para girar e pinçar a exibição do mapa conforme documentado [aqui](map-add-controls.md).

### <a name="localizing-the-map"></a>Como localizar o mapa

Quando o público-alvo está espalhado em vários países/regiões ou fala idiomas diferentes, a localização é importante.

#### <a name="before-google-maps"></a>Antes: Google Maps

Para localizar o Google Maps, adicione parâmetros de idioma e região.

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Aqui está um exemplo do Google Maps com o idioma definido como "fr-FR".

![Localização do Google Maps](media/migrate-google-maps-web-app/google-maps-localization.png)

#### <a name="after-azure-maps"></a>Depois: Azure Mapas

O Azure Mapas fornece duas maneiras diferentes de definir o idioma e a exibição regional do mapa. A primeira opção é adicionar essas informações ao namespace *atlas* global. Isso fará com que todas as instâncias do controle de mapeamento no aplicativo usem essas configurações como padrão. O seguinte define o idioma como francês ("fr-FR") e a exibição regional como "Automática":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

A segunda opção é passar essas informações para as opções de mapa ao carregar o mapa. Dessa forma:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> Com os Azure Mapas, é possível carregar várias instâncias de mapa na mesma página com diferentes configurações de idioma e região. Também é possível atualizar essas configurações no mapa após ele ter sido carregado.

Encontre uma lista detalhada de [idiomas compatíveis](supported-languages.md) com o Azure Mapas.

Aqui está um exemplo do Azure Mapas com o idioma definido como "fr" e a região do usuário definida como "fr-FR".

![Localização do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-localization.png)

### <a name="setting-the-map-view"></a>Como configurar a exibição do mapa

Os mapas dinâmicos nos Azure Mapas e no Google Maps podem ser movidos de maneira programática para novas localizações geográficas. Para fazer isso, chame as funções apropriadas em JavaScript. Os exemplos mostram como fazer com que o mapa exiba a imagem aérea do satélite, centralize o mapa sobre uma localização e altere o nível de zoom para 15 no Google Maps. As seguintes coordenadas de localização são usadas: longitude: -111,0225 e latitude: 35,0272.

> [!NOTE]
> O Google Maps usa peças de dimensão de 256 pixels, enquanto o Azure Mapas usa um tamanho de peça maior, de 512 pixels. Portanto, o Azure Mapas requer menos solicitações de rede para carregar a mesma área de mapa que o Google Maps. Devido à maneira como as pirâmides de peças funcionam nos controles de mapa, você precisa subtrair o nível de zoom usado no Google Maps pelo número um ao usar Azure Mapas. Essa operação aritmética garante que as peças maiores no Azure Mapas renderizem a mesma área de mapa que no Google Maps,

#### <a name="before-google-maps"></a>Antes: Google Maps

Mova o controle de mapa do Google Maps usando o método `setOptions`. Esse método permite que você especifique o centro do mapa e um nível de zoom.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

![Exibição de conjunto do Google Maps](media/migrate-google-maps-web-app/google-maps-set-view.png)

#### <a name="after-azure-maps"></a>Depois: Azure Mapas

No Azure Mapas, altere a posição do mapa usando o método `setCamera` e altere o estilo do mapa usando o método `setStyle`. As coordenadas nos Azure Mapas estão no formato "longitude, latitude", e o valor do nível de zoom é subtraído em um.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

![Definição de exibição do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)

**Recursos adicionais:**

* [Escolher um estilo de mapa](choose-map-style.md)
* [Estilos de mapa compatíveis](supported-map-styles.md)

### <a name="adding-a-marker"></a>Adicionar um marcador

Nos Azure Mapas, há várias maneiras de renderizar os dados de pontos no mapa:

* **Marcadores HTML** – renderiza pontos usando elementos DOM tradicionais. Os Marcadores de HTML dão suporte para arrastar.
* **Camada de Símbolos** – renderiza pontos com um ícone ou texto dentro do contexto WebGL.
* **Camada de Bolhas** – renderiza pontos como círculos no mapa. O raio dos círculos pode ser dimensionado com base nas propriedades dos dados.

Renderize camadas de Símbolo e de Bolha no contexto do WebGL. Ambas as camadas podem renderizar grandes conjuntos de pontos no mapa. Essas camadas exigem que os dados sejam armazenados em uma fonte de dados. As fontes de dados e as camadas de renderização devem ser adicionadas ao mapa depois que o evento `ready` é disparado. Os marcadores HTML são renderizados como elementos DOM na página e eles não usam uma fonte de dados. Quanto mais elementos DOM uma página tiver, mais lenta ela será. Ao renderizar mais de algumas centenas de pontos em um mapa, é recomendável usar uma das camadas de renderização.

Vamos adicionar um marcador ao mapa com o número 10 sobreposto como um rótulo. Usar longitude: -0,2 e latitude: 51,5.

#### <a name="before-google-maps"></a>Antes: Google Maps

Com o Google Maps, adicione marcadores ao mapa usando a classe `google.maps.Marker` e especifique o mapa como uma das opções.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

![Marcador do Google Maps](media/migrate-google-maps-web-app/google-maps-marker.png)

**Depois: Azure Mapas usando Marcadores HTML**

Nos Azure Mapas, use marcadores HTML para exibir um ponto no mapa. Os marcadores HTML são recomendados para aplicativos que só precisam exibir um pequeno número de pontos no mapa. Para usar um marcador HTML, crie uma instância da classe `atlas.HtmlMarker`. Defina as opções de texto e de posição e adicione o marcador ao mapa usando o método `map.markers.add`.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

![Marcador HTML do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-html-marker.png)

**Depois: Azure Mapas usando uma Camada de Símbolos**

Para uma camada de Símbolos, adicione os dados a uma fonte de dados. Anexe a fonte de dados à camada. Além disso, a fonte de dados e a camada devem ser adicionadas ao mapa depois de o evento de `ready` ser disparado. Para renderizar um valor de texto exclusivo acima de um símbolo, as informações de texto precisam ser armazenadas como uma propriedade do ponto de dados. A propriedade deve ser referenciada na opção `textField` da camada. Essa abordagem é um pouco mais complexa do que usar marcadores HTML, mas tem melhor desempenho.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Camada de símbolos do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)

**Recursos adicionais:**

- [Criar uma fonte de dados](create-data-source-web-sdk.md)
- [Adicionar uma camada de Símbolo](map-add-pin.md)
- [Adicionar uma camada de Bolhas](map-add-bubble-layer.md)
- [Dados de ponto de cluster](clustering-point-data-web-sdk.md)
- [Adicionar Marcadores HTML](map-add-custom-html.md)
- [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)
- [Opções de ícone da camada de Símbolos](/javascript/api/azure-maps-control/atlas.iconoptions)
- [Opção de texto da camada de Símbolos](/javascript/api/azure-maps-control/atlas.textoptions)
- [Classe de marcador HTML](/javascript/api/azure-maps-control/atlas.htmlmarker)
- [Opções de marcador HTML](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-custom-marker"></a>Adicionar um marcador personalizado

Você pode usar imagens personalizadas para representar pontos em um mapa. O mapa abaixo usa uma imagem personalizada para exibir um ponto no mapa. O ponto é exibido na latitude: 51,5 e na longitude: -0,2. A âncora desloca a posição do marcador, de modo que o ponto do ícone de pino seja alinhado com a posição correta no mapa.

<center>

![imagem do pino amarelo](media/migrate-google-maps-web-app/yellow-pushpin.png)<br/>
yellow-pushpin.png</center>

#### <a name="before-google-maps"></a>Antes: Google Maps

Crie um marcador personalizado especificando um objeto `Icon` que contenha o `url` para a imagem. Especifique um ponto `anchor` para alinhar o ponto da imagem do pino com a coordenada no mapa. O valor de âncora no Google Maps é relativo ao canto superior esquerdo da imagem.

```javascript
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    icon: {
        url: 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png',
        anchor: new google.maps.Point(5, 30)
    },
    map: map
});
```

![Marcador personalizado do Google Maps](media/migrate-google-maps-web-app/google-maps-custom-marker.png)

**Depois: Azure Mapas usando Marcadores HTML**

Para personalizar um marcador HTML, passe um `string` ou `HTMLElement` HTML para a opção `htmlContent` do marcador. Use a opção `anchor` para especificar a posição relativa do marcador, em relação à coordenada da posição. Atribua um dos nove pontos de referência definidos à opção `anchor`. Esses pontos definidos são: "centro", "superior", "inferior", "esquerda", "direita", "superior esquerdo", "superior direito", "inferior esquerdo" e "inferior direito". O conteúdo é ancorado no centro inferior do conteúdo HTML por padrão. Para facilitar a migração de código do Google Maps, defina o `anchor` como "superior esquerdo" e, em seguida, use a opção `pixelOffset` com o mesmo deslocamento usado no Google Maps. Os deslocamentos no Azure Mapas são movidos na direção oposta dos deslocamentos no Google Maps. Portanto, multiplique os deslocamentos por menos um.

> [!TIP]
> Adicione `pointer-events:none` como um estilo no conteúdo HTML para desabilitar o comportamento de arrastar padrão no Microsoft Edge, que exibirá um ícone indesejado.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

![Marcador HTML personalizado do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)

**Depois: Azure Mapas usando uma Camada de Símbolos**

As camadas de Símbolos no Azure Mapas também são compatíveis com imagens personalizadas. Primeiro, carregue a imagem para os recursos de mapa e atribua uma ID exclusiva para ela. Referencie a imagem na camada de símbolos. Use a opção `offset` para alinhar a imagem com o ponto correto no mapa. Use a opção `anchor` para especificar a posição relativa do símbolo, em relação à coordenada da posição. Use um dos nove pontos de referência definidos. Esses pontos são: "centro", "superior", "inferior", "esquerda", "direita", "superior esquerdo", "superior direito", "inferior esquerdo" e "inferior direito". O conteúdo é ancorado no centro inferior do conteúdo HTML por padrão. Para facilitar a migração de código do Google Maps, defina o `anchor` como "superior esquerdo" e, em seguida, use a opção `offset` com o mesmo deslocamento usado no Google Maps. Os deslocamentos no Azure Mapas são movidos na direção oposta dos deslocamentos no Google Maps. Portanto, multiplique os deslocamentos por menos um.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Camada de símbolos de ícone personalizado do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</

> [!TIP]
> Para renderizar pontos personalizados avançados, use várias camadas de renderização juntas. Por exemplo, digamos que você deseje ter vários pinos que tenham o mesmo ícone em diferentes círculos coloridos. Em vez de criar várias imagens para cada sobreposição de cor, adicione uma camada de símbolos na parte superior de uma camada de bolha. Faça com que a referência de pinos tenha a mesma fonte de dados. Essa abordagem será mais eficiente do que criar e manter várias imagens diferentes.

**Recursos adicionais:**

- [Criar uma fonte de dados](create-data-source-web-sdk.md)
- [Adicionar uma camada de Símbolo](map-add-pin.md)
- [Adicionar Marcadores HTML](map-add-custom-html.md)
- [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)
- [Opções de ícone da camada de Símbolos](/javascript/api/azure-maps-control/atlas.iconoptions)
- [Opção de texto da camada de Símbolos](/javascript/api/azure-maps-control/atlas.textoptions)
- [Classe de marcador HTML](/javascript/api/azure-maps-control/atlas.htmlmarker)
- [Opções de marcador HTML](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-polyline"></a>Como adicionar uma polilinha

Use polilinhas para representar uma linha ou um caminho no mapa. Vamos criar uma polilinha tracejada no mapa.

#### <a name="before-google-maps"></a>Antes: Google Maps

A classe Polyline aceita um conjunto de opções. Passe uma matriz de coordenadas na opção `path` da polilinha.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Define a symbol using SVG path notation, with an opacity of 1.
var lineSymbol = {
    path: 'M 0,-1 0,1',
    strokeOpacity: 1,
    scale: 4
};

//Create the polyline.
var line = new google.maps.Polyline({
    path: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1)
    ],
    strokeColor: 'red',
    strokeOpacity: 0,
    strokeWeight: 4,
    icons: [{
        icon: lineSymbol,
        offset: '0',
        repeat: '20px'
    }]
});

//Add the polyline to the map.
line.setMap(map);
```

![Polilinha do Google Maps](media/migrate-google-maps-web-app/google-maps-polyline.png)

#### <a name="after-azure-maps"></a>Depois: Azure Mapas

As polilinhas são chamadas de objetos `LineString` ou `MultiLineString`. Esses objetos podem ser adicionados a uma fonte de dados e renderizados usando uma camada de linha. Adicione `LineString` a uma fonte de dados e, em seguida, adicione a fonte de dados a uma `LineLayer` para renderizá-la.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

![Polilinha do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-polyline.png)

**Recursos adicionais:**

- [Adicionar linhas ao mapa](map-add-line-layer.md)
- [Opções da camada de Linhas](/javascript/api/azure-maps-control/atlas.linelayeroptions)
- [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Como adicionar um polígono

Os Azure Mapas e o Google Maps fornecem suporte semelhante para polígonos. Os polígonos são usados para representar uma área no mapa. Os exemplos a seguir mostram como criar um polígono que forma um triângulo com base na coordenada central do mapa.

#### <a name="before-google-maps"></a>Antes: Google Maps

A classe Polygon aceita um conjunto de opções. Passe uma matriz de coordenadas para a opção `paths` do polígono.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create a polygon.
var polygon = new google.maps.Polygon({
    paths: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1),
        center
    ],
    strokeColor: 'red',
    strokeWeight: 2,
    fillColor: 'rgba(0, 255, 0, 0.5)'
});

//Add the polygon to the map
polygon.setMap(map);
```

![Polígono do Google Maps](media/migrate-google-maps-web-app/google-maps-polygon.png)

#### <a name="after-azure-maps"></a>Depois: Azure Mapas

Adicione um objeto `Polygon` ou `MultiPolygon` a uma fonte de dados. Renderize o objeto no mapa usando camadas. Renderize a área de um polígono usando uma camada de polígonos. Por fim, renderize o contorno de um polígono usando uma camada de linhas.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

![Polígono do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-polygon.png)

**Recursos adicionais:**

- [Adicionar um polígono a um mapa](map-add-shape.md)
- [Adicionar um círculo ao mapa](map-add-shape.md#add-a-circle-to-the-map)
- [Opções da camada de polígono](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)
- [Opções da camada de Linhas](/javascript/api/azure-maps-control/atlas.linelayeroptions)
- [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Exibir a janela Informações

Informações adicionais de uma entidade podem ser exibidas no mapa como uma classe `google.maps.InfoWindow` no Google Maps. Nos Azure Mapas, essa funcionalidade pode ser obtida com a classe `atlas.Popup`. Os exemplos a seguir adicionam um marcador ao mapa. Quando o marcador é clicado, uma janela de informações ou um pop-up é exibido.

#### <a name="before-google-maps"></a>Antes: Google Maps

Crie uma instância de uma janela de informações usando o construtor `google.maps.InfoWindow`.

```javascript
//Add a marker in which to display an infowindow for.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(47.6, -122.33),
    map: map
});

//Create an infowindow.
var infowindow = new google.maps.InfoWindow({
    content: '<div style="padding:5px"><b>Hello World!</b></div>'
});

//Add a click event to the marker to open the infowindow.
marker.addListener('click', function () {
    infowindow.open(map, marker);
});
```

![Pop-up do Google Maps](media/migrate-google-maps-web-app/google-maps-popup.png)

#### <a name="after-azure-maps"></a>Depois: Azure Mapas

Vamos usar um pop-up para exibir informações adicionais sobre o local. Passe um objeto `string` ou `HTMLElement` HTML para a opção `content` do pop-up. Se quiser, os pop-ups poderão ser exibidos de maneira independente de qualquer forma. Portanto, os pop-ups exigem a especificação de um valor `position`. Especifique o valor de `position`. Para exibir um pop-up, chame o método `open` e passe o `map` no qual o pop-up deve ser exibido.

```javascript
//Add a marker to the map in which to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:5px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

![Pop-up Azure Mapas](media/migrate-google-maps-web-app/azure-maps-popup.png)

> [!NOTE]
> Você pode fazer a mesma coisa com uma camada de símbolos, bolhas, linhas ou polígonos, passando a camada escolhida para o código do evento de mapas, em vez de um marcador.

**Recursos adicionais:**

- [Adicionar um pop-up](map-add-popup.md)
- [Pop-up com o Conteúdo de Mídia](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Pop-ups em Formas](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Reutilizar pop-up com vários marcadores](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Classe de pop-up](/javascript/api/azure-maps-control/atlas.popup)
- [Opções de pop-up](/javascript/api/azure-maps-control/atlas.popupoptions)

### <a name="import-a-geojson-file"></a>Importar um arquivo GeoJSON

O Google Maps é compatível com carregar e definir dinamicamente o estilo de dados GeoJSON usando a classe `google.maps.Data`. A funcionalidade dessa classe está muito mais alinhada com o estilo controlado por dados do Azure Mapas. Mas há uma diferença importante. Com o Google Maps, você especifica uma função de retorno de chamada. A lógica de negócios para definir o estilo de cada recurso que ele processou individualmente no thread da IU. No entanto, no Azure Mapas, as camadas são compatíveis com a especificação de expressões controladas por dados como opções de estilo. Essas expressões são processadas no momento da renderização em um thread separado. A abordagem do Azure Mapas melhora o desempenho da renderização. Essa vantagem é percebida quando conjuntos de dados maiores precisam ser renderizados rapidamente.

Os exemplos a seguir carregam um feed GeoJSON do USGS de todos os terremotos ocorridos nos últimos sete dias. Os dados de terremotos são renderizados como círculos dimensionados no mapa. A cor e a escala de cada círculo baseiam-se na magnitude de cada terremoto, que é armazenado na propriedade `"mag"` de cada recurso no conjunto de dados. Se a magnitude for superior ou igual a cinco, o círculo será vermelho. Se ela for superior ou igual a três, mas inferior a cinco, o círculo será laranja. Se ela for inferior a três, o círculo será verde. O raio de cada círculo será o exponencial da magnitude multiplicado por 0,1.

#### <a name="before-google-maps"></a>Antes: Google Maps

Especifique uma única função de retorno de chamada no método `map.data.setStyle`. Dentro da função de retorno de chamada, aplique a lógica de negócios a cada recurso. Carregue o feed GeoJSON com o método `map.data.loadGeoJson`.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Define a callback to style each feature.
            map.data.setStyle(function (feature) {

                //Extract the 'mag' property from the feature.
                var mag = parseFloat(feature.getProperty('mag'));

                //Set the color value to 'green' by default.
                var color = 'green';

                //If the mag value is greater than 5, set the color to 'red'.
                if (mag >= 5) {
                    color = 'red';
                }
                //If the mag value is greater than 3, set the color to 'orange'.
                else if (mag >= 3) {
                    color = 'orange';
                }

                return /** @type {google.maps.Data.StyleOptions} */({
                    icon: {
                        path: google.maps.SymbolPath.CIRCLE,

                        //Scale the radius based on an exponential of the 'mag' value.
                        scale: Math.exp(mag) * 0.1,
                        fillColor: color,
                        fillOpacity: 0.75,
                        strokeWeight: 2,
                        strokeColor: 'white'
                    }
                });
            });

            //Load the data feed.
            map.data.loadGeoJson(earthquakeFeed);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![GeoJSON do Google Maps](media/migrate-google-maps-web-app/google-maps-geojson.png)

#### <a name="after-azure-maps"></a>Depois: Azure Mapas

O GeoJSON é o tipo de dados base no Azure Mapas. Importe-o para uma fonte de dados usando o método `datasource.importFromUrl`. Use uma camada de bolhas. A camada de bolhas fornece uma funcionalidade para renderizar círculos dimensionados, com base nas propriedades dos recursos em uma fonte de dados. Em vez de ter uma função de retorno de chamada, a lógica de negócios é convertida em uma expressão e passada para as opções de estilo. As expressões definem o funcionamento da lógica de negócios. As expressões podem ser transmitidas para outro thread e avaliadas em relação aos dados do recurso. Várias fontes de dados e camadas podem ser adicionadas aos Azure Mapas, cada uma com uma lógica de negócios diferente. Esse recurso permite que vários conjuntos de dados sejam renderizados no mapa de maneiras diferentes.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as scaled circles.
                map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
                    //Make the circles semi-transparent.
                    opacity: 0.75,

                    color: [
                        'case',

                        //If the mag value is greater than 5, return 'red'.
                        ['>=', ['get', 'mag'], 5],
                        'red',

                        //If the mag value is greater than 3, return 'orange'.
                        ['>=', ['get', 'mag'], 3],
                        'orange',

                        //Return 'green' as a fallback.
                        'green'
                    ],

                    //Scale the radius based on an exponential of the 'mag' value.
                    radius: ['*', ['^', ['e'], ['get', 'mag']], 0.1]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![GeoJSON do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-geojson.png)

**Recursos adicionais:**

* [Adicionar uma camada de Símbolo](map-add-pin.md)
* [Adicionar uma camada de Bolhas](map-add-bubble-layer.md)
* [Dados de ponto de cluster](clustering-point-data-web-sdk.md)
* [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Clustering de marcadores

Ao visualizar muitos pontos de dados no mapa, eles podem se sobrepor. A sobreposição faz com que o mapa pareça confuso e fique difícil de ler e usar. O clustering de dados de ponto é o processo de combinar pontos de dados que estão próximos uns dos outros e os representar no mapa como um ponto de dados clusterizado individual. À medida que o usuário aplica zoom ao mapa, os clusters se dividem em seus pontos de dados individuais. Clusterize pontos de dados para melhorar a experiência do usuário e o desempenho do mapa.

Nos exemplos a seguir, o código carrega um feed GeoJSON dos dados de terremoto da última semana e os adiciona ao mapa. Os clusters são renderizados como círculos dimensionados e coloridos. A escala e a cor dos círculos dependem do número de pontos que eles contêm.

> [!NOTE]
> O Google Maps e Azure Mapas usam algoritmos de clustering ligeiramente diferentes. Dessa forma, às vezes a distribuição de pontos nos clusters pode variar.

#### <a name="before-google-maps"></a>Antes: Google Maps

Use a biblioteca MarkerCluster para clusterizar marcadores. Os ícones de cluster são limitados a imagens, que têm os números de um a cinco como o nome. Eles são hospedados no mesmo diretório.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Download the GeoJSON data.
            fetch(earthquakeFeed)
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    //Loop through the GeoJSON data and create a marker for each data point.
                    var markers = [];

                    for (var i = 0; i < data.features.length; i++) {

                        markers.push(new google.maps.Marker({
                            position: new google.maps.LatLng(data.features[i].geometry.coordinates[1], data.features[i].geometry.coordinates[0])
                        }));
                    }

                    //Create a marker clusterer instance and tell it where to find the cluster icons.
                    var markerCluster = new MarkerClusterer(map, markers,
                        { imagePath: 'https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m' });
                });
        }
    </script>

    <!-- Load the marker cluster library. -->
    <script src="https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/markerclusterer.js"></script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Clustering do Google Maps](media/migrate-google-maps-web-app/google-maps-clustering.png)

#### <a name="after-azure-maps"></a>Depois: Azure Mapas

Adicione e gerencie dados em uma fonte de dados. Conecte fontes de dados e camadas e, em seguida, renderize os dados. A classe `DataSource` no Azure Mapas fornece várias opções de clustering.

* `cluster` – informa a fonte de dados para os dados do ponto de cluster.
* `clusterRadius` – o raio em pixels para os pontos de cluster juntos.
* `clusterMaxZoom` – o nível de zoom máximo no qual o clustering ocorre. Se você ampliar mais do que esse nível, todos os pontos serão renderizados como símbolos.
* `clusterProperties` – define as propriedades personalizadas que são calculadas usando expressões em todos os pontos de cada cluster e adicionadas às propriedades de cada ponto de cluster.

Quando o clustering estiver habilitado, a fonte de dados enviará pontos de dados clusterizados e não clusterizados para camadas para renderização. A fonte de dados é capaz de clusterizar centenas de milhares de pontos de dados. Um ponto de dados clusterizado tem as seguintes propriedades:

| Nome da propriedade             | Type    | Descrição   |
|---------------------------|---------|---------------|
| `cluster`                 | booleano | Indica se o recurso representa um cluster. |
| `cluster_id`              | string  | Uma ID exclusiva para o cluster que pode ser usada com os métodos DataSource `getClusterExpansionZoom`, `getClusterChildren` e `getClusterLeaves`. |
| `point_count`             | número  | O número de pontos que o cluster contém.  |
| `point_count_abbreviated` | string  | Uma cadeia de caracteres que abrevia o valor de `point_count` se for longo. (por exemplo, 4.000 torna-se 4K)  |

A classe `DataSource` tem a seguinte função auxiliar para acessar informações adicionais sobre um cluster usando o `cluster_id`.

| Método | Tipo de retorno | Descrição |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Promessa&lt;Matriz&lt;Recurso&lt;Geometria, qualquer&gt; \| Forma&gt;&gt; | Recupera os filhos do cluster fornecido no próximo nível de zoom. Esses filhos podem ser uma combinação de formas e subclusters. Os subclusters serão recursos com propriedades correspondentes a ClusteredProperties. |
| `getClusterExpansionZoom(clusterId: number)` | Promise&lt;number&gt; | Calcula um nível de zoom no qual o cluster começará a se expandir ou separar. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Promessa&lt;Matriz&lt;Recurso&lt;Geometria, qualquer&gt; \| Forma&gt;&gt; | Recupera todos os pontos em um cluster. Defina o `limit` para retornar um subconjunto dos pontos e use `offset` para paginar os pontos. |

Ao renderizar dados clusterizados no mapa, geralmente é melhor usar duas ou mais camadas. O exemplo a seguir usa três camadas. Uma camada de bolha para desenhar círculos coloridos dimensionados com base no tamanho dos clusters. Uma camada de símbolo para renderizar o tamanho do cluster como texto. Além disso, ela usa uma segunda camada de símbolo para renderizar os pontos não clusterizados. Há muitas outras maneiras de renderizar dados clusterizados. Para obter mais informações, confira a documentação [Dados do ponto de cluster](clustering-point-data-web-sdk.md).

Importe dados GeoJSON diretamente usando a função `importDataFromUrl` na classe `DataSource`, dentro do mapa do Azure Mapas.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points which have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Clustering do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-clustering.png)

**Recursos adicionais:**

* [Adicionar uma camada de Símbolo](map-add-pin.md)
* [Adicionar uma camada de Bolhas](map-add-bubble-layer.md)
* [Dados de ponto de cluster](clustering-point-data-web-sdk.md)
* [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Adicionar um mapa de calor

Mapas de calor, também conhecidos como mapas de densidade de ponto, são um tipo de visualização de dados. Eles são usados para representar a densidade de dados usando um intervalo de cores. Muitas vezes, são usados para mostrar os "pontos quentes" dos dados em um mapa. Os mapas de calor são uma ótima maneira de renderizar conjuntos de dados grandes de pontos.

Os exemplos a seguir carregam um feed GeoJSON do USGS de todos os terremotos ocorridos no último mês e os renderiza como um mapa de calor ponderado. A propriedade `"mag"` é usada como o peso.

#### <a name="before-google-maps"></a>Antes: Google Maps

Para criar um mapa de calor, carregue a biblioteca de "visualização" adicionando `&libraries=visualization` à URL do script da API. A camada do mapa de calor no Google Maps não dá suporte a dados GeoJSON diretamente. Primeiro, baixe os dados e converta-os em uma matriz de pontos de dados ponderados:

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {

            var map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2,
                mapTypeId: 'satellite'
            });

            //Download the GeoJSON data.
            fetch(url).then(function (response) {
                return response.json();
            }).then(function (res) {
                var points = getDataPoints(res);

                var heatmap = new google.maps.visualization.HeatmapLayer({
                    data: points
                });
                heatmap.setMap(map);
            });
        }

        function getDataPoints(geojson, weightProp) {
            var points = [];

            for (var i = 0; i < geojson.features.length; i++) {
                var f = geojson.features[i];

                if (f.geometry.type === 'Point') {
                    points.push({
                        location: new google.maps.LatLng(f.geometry.coordinates[1], f.geometry.coordinates[0]),
                        weight: f.properties[weightProp]
                    });
                }
            }

            return points;
        } 
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]&libraries=visualization" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Mapa de calor do Google Maps](media/migrate-google-maps-web-app/google-maps-heatmap.png)

#### <a name="after-azure-maps"></a>Depois: Azure Mapas

Carregue os dados GeoJSON em uma fonte de dados e conecte a fonte de dados a uma camada do mapa de calor. A propriedade que será usada para o peso pode ser passada para a opção `weight` usando uma expressão. Importe dados GeoJSON diretamente no Azure Mapas usando a função `importDataFromUrl` na classe `DataSource`.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    weight: ['get', 'mag'],
                    intensity: 0.005,
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Mapa de calor do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-heatmap.png)

**Recursos adicionais:**

- [Adicionar uma camada do mapa de calor](map-add-heat-map-layer.md)
- [Classe da camada do mapa de calor](/javascript/api/azure-maps-control/atlas.layer.heatmaplayer)
- [Opções da camada do mapa de calor](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)
- [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Sobrepor uma camada de peça

As camadas de peças no Azure Mapas são conhecidas como sobreposições de imagens no Google Maps. As camadas de peças permitem a sobreposição de imagens grandes que foram divididas em imagens com peças menores, que se alinham com o sistema de organização dos mapas. Essa abordagem é normalmente usada para a sobreposição de imagens grandes ou de conjuntos de dados grandes.

Os exemplos a seguir sobrepõem uma camada de peça de radar meteorológico da Iowa Environmental Mesonet of Iowa State University.

#### <a name="before-google-maps"></a>Antes: Google Maps

No Google Maps, camadas de bloco podem ser criadas usando a classe `google.maps.ImageMapType`.

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```

![Camada de peças do Google Maps](media/migrate-google-maps-web-app/google-maps-tile-layer.png)

#### <a name="after-azure-maps"></a>Depois: Azure Mapas

Adicione uma camada de peças ao mapa da mesma forma como qualquer outra camada. Use uma URL formatada que tenha os espaços reservados em x, y e zoom, `{x}`, `{y}` e `{z}`, para informar à camada onde acessar as peças. As camadas de peças dos Azure Mapas também dão suporte aos espaços reservados `{quadkey}`, `{bbox-epsg-3857}` e `{subdomain}`.

> [!TIP]
> No Azure Mapas, as camadas podem ser facilmente renderizadas sob outras camadas, incluindo as camadas do mapa base. Geralmente, é desejável renderizar camadas de peça abaixo dos rótulos de mapa para que elas sejam fáceis de ler. O método `map.layers.add` usa um segundo parâmetro que é a ID da camada na qual inserir a nova camada abaixo. Para inserir uma camada de peças abaixo dos rótulos de mapa, use este código: `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

![Camada de peça do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)

> [!TIP]
> As solicitações de bloco podem ser capturadas usando a opção `transformRequest` do mapa. Isso permitirá que você modifique ou adicione cabeçalhos à solicitação, se desejar.

**Recursos adicionais:**

- [Adicionar camadas de bloco](map-add-tile-layer.md)
- [Classe da camada do bloco](/javascript/api/azure-maps-control/atlas.layer.tilelayer)
- [Opções de camada de bloco](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

### <a name="show-traffic-data"></a>Mostrar dados de tráfego

Os dados de tráfego podem ser sobrepostos ao Azure Mapas e ao Google Maps.

#### <a name="before-google-maps"></a>Antes: Google Maps

Sobreponha os dados de tráfego no mapa usando a camada de tráfego.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

![Tráfego do Google Maps](media/migrate-google-maps-web-app/google-maps-traffic.png)

#### <a name="after-azure-maps"></a>Depois: Azure Mapas

O Azure Mapas fornece várias opções diferentes para exibir o tráfego. Exiba incidentes de tráfego, como bloqueios e acidentes na estrada, como ícones no mapa. Sobreponha o fluxo de tráfego e as estradas codificadas por cores no mapa. As cores podem ser modificadas com base no limite de velocidade publicado, relativo ao atraso normal esperado ou de acordo com o atraso absoluto. Os dados de incidentes nos Azure Mapas são atualizados a cada minuto; os dados de fluxo são atualizados a cada dois minutos.

Atribua os valores desejados para as opções `setTraffic`.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

![Tráfego do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-traffic.png)

Se você clicar em um dos ícones de tráfego no Azure Mapas, informações adicionais serão exibidas em um pop-up.

![Incidente de trânsito do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)

**Recursos adicionais:**

* [Mostrar tráfego no mapa](map-show-traffic.md)
* [Opções de sobreposição de tráfego](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Adicionar uma sobreposição de piso

Os mapas do Azure e do Google são compatíveis com a sobreposição de imagens georreferenciadas no mapa. As imagens georreferenciadas são movidas e dimensionadas conforme você faz a panorâmica e aplica zoom no mapa. No Google Maps, as imagens georreferenciadas são conhecidas como sobreposições de piso, enquanto nos Azure Mapas, são chamadas de camadas de imagem. Elas são ótimas para criar plantas baixas, sobrepor mapas antigos ou imagens de um drone.

#### <a name="before-google-maps"></a>Antes: Google Maps

Especifique a URL para a imagem que você deseja sobrepor e uma caixa delimitadora para associar a imagem no mapa. Este exemplo sobrepõe uma imagem de mapa de [Newark, Nova Jersey, de 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) no mapa.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.740, -74.18),
                zoom: 12
            });

            var imageBounds = {
                north: 40.773941,
                south: 40.712216,
                east: -74.12544,
                west: -74.22655
            };

            historicalOverlay = new google.maps.GroundOverlay(
                'https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg',
                imageBounds);
            historicalOverlay.setMap(map);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

Executar esse código em um navegador exibirá um mapa semelhante à imagem a seguir:

![Sobreposição de imagem do Google Maps](media/migrate-google-maps-web-app/google-maps-image-overlay.png)

#### <a name="after-azure-maps"></a>Depois: Azure Mapas

Use a classe `atlas.layer.ImageLayer` para sobrepor imagens georreferenciadas. Essa classe requer uma URL para uma imagem e um conjunto de coordenadas para os quatro cantos da imagem. A imagem deve ser hospedada no mesmo domínio ou ter CORs habilitados.

> [!TIP]
> Se você tiver apenas informações de norte, sul, leste, oeste e de rotação, e não tiver coordenadas para cada canto da imagem, poderá usar o método [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](/javascript/api/azure-maps-control/atlas.layer.imagelayer#getcoordinatesfromedges-number--number--number--number--number-) estático.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Sobreposição de imagem do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)

**Recursos adicionais:**

- [Sobrepor uma imagem](map-add-image-layer.md)
- [Classe da camada de imagem](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

### <a name="add-kml-data-to-the-map"></a>Adicionar dados KML ao mapa

Os mapas do Azure e do Google podem importar e renderizar dados do KML, KMZ e GeoRSS no mapa. O Azure Mapas também é compatível com GPX, GML, arquivos CSV espaciais, GeoJSON, WKT (Well Known Text), WMS (Serviço de Mapeamento da Web), WMTS (Serviço de Bloco de Mapeamento da Web) e WFS (Serviço de Recurso da Web). Os Azure Mapas leem os arquivos localmente na memória e, na maioria dos casos, podem tratar arquivos KML muito maiores. 

#### <a name="before-google-maps"></a>Antes: Google Maps

```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(0, 0),
                zoom: 1
            });

             var layer = new google.maps.KmlLayer({
              url: 'https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml',
              map: map
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

Executar esse código em um navegador exibirá um mapa semelhante à imagem a seguir:

![KML do Google Maps](media/migrate-google-maps-web-app/google-maps-kml.png)

#### <a name="after-azure-maps"></a>Depois: Azure Mapas

Nos Azure Mapas, GeoJSON é o principal formato de dados usado no SK da Web; outros formatos de dados espaciais podem ser facilmente integrados usando o [módulo de E/S espacial](/javascript/api/azure-maps-spatial-io/). Esse módulo tem funções para ler e gravar dados espaciais e também inclui uma camada de dados simples que pode renderizar dados facilmente de qualquer um desses formatos de dados espaciais. Para ler os dados em um arquivo de dados espaciais, envie uma URL ou dados brutos como uma cadeia de caracteres ou um blob para a função `atlas.io.read`. Isso retornará todos os dados analisados do arquivo que podem ser adicionados ao mapa. O KML é um pouco mais complexo do que a maioria dos formatos de dados espaciais porque ele inclui muito mais informações de estilo. A classe `SpatialDataLayer` dá suporte à renderização da maioria desses estilos; no entanto, as imagens de ícone precisam ser carregadas no mapa antes de carregar os dados do recurso e as sobreposições de piso precisam ser adicionadas como camadas ao mapa separadamente. Ao carregar dados por meio de uma URL, eles devem ser hospedados em um ponto de extremidade habilitado para CORs ou um serviço de proxy deve ser passado como uma opção para a função de leitura. 

```javascript
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
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
            map.events.add('ready', function () {
            
                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Add a simple data layer for rendering the data.
                layer = new atlas.layer.SimpleDataLayer(datasource);
                map.layers.add(layer);

                //Read a KML file from a URL or pass in a raw KML string.
                atlas.io.read('https://googlearchive.github.io/js-v2-samples/ggeoxml/cta.kml').then(async r => {
                    if (r) {

                        //Check to see if there are any icons in the data set that need to be loaded into the map resources.
                        if (r.icons) {
                            //For each icon image, create a promise to add it to the map, then run the promises in parrallel.
                            var imagePromises = [];

                            //The keys are the names of each icon image.
                            var keys = Object.keys(r.icons);

                            if (keys.length !== 0) {
                                keys.forEach(function (key) {
                                    imagePromises.push(map.imageSprite.add(key, r.icons[key]));
                                });

                                await Promise.all(imagePromises);
                            }
                        }

                        //Load all features.
                        if (r.features && r.features.length > 0) {
                            datasource.add(r.features);
                        }

                        //Load all ground overlays.
                        if (r.groundOverlays && r.groundOverlays.length > 0) {
                            map.layers.add(r.groundOverlays);
                        }

                        //If bounding box information is known for data, set the map view to it.
                        if (r.bbox) {
                            map.setCamera({ bounds: r.bbox, padding: 50 });
                        }
                    }
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![KML do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-kml.png)</center>

**Recursos adicionais:**

- [função atlas.io.read](/javascript/api/azure-maps-spatial-io/atlas.io#read-string---arraybuffer---blob--spatialdatareadoptions-)
- [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
- [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

## <a name="additional-code-samples"></a>Exemplos de código adicionais

A seguir estão alguns exemplos de código adicionais relacionados à migração do Google Maps:

* [Ferramentas de desenho](map-add-drawing-toolbar.md)
* [Limitar o Mapa a Panorâmica de Dois Dedos](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
* [Limitar zoom da roda de rolagem](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
* [Criar um Controle de Tela Inteira](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**Serviços:**

* [Como usar o módulo de serviços do Azure Mapas](how-to-use-services-module.md)
* [Pesquisar pontos de interesse](map-search-location.md)
* [Obter informações de uma coordenada (inverter geocódigo)](map-get-information-from-coordinate.md)
* [Mostrar trajetos de A para B](map-route.md)
* [Pesquisar a sugestão automática com a interface do usuário do JQuery](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Mapeamento de classe do SDK da Web do Google Maps V3 para Azure Mapas

O apêndice a seguir fornece uma referência cruzada sobre as classes comumente usadas no Google Maps V3 e no SDK Web dos Azure Mapas equivalente.

### <a name="core-classes"></a>Classes de núcleo

| Google Maps   | Mapas do Azure  |
|---------------|-------------|
| `google.maps.Map` | [atlas.Map](/javascript/api/azure-maps-control/atlas.map)  |
| `google.maps.InfoWindow` | [atlas.Popup](/javascript/api/azure-maps-control/atlas.popup)  |
| `google.maps.InfoWindowOptions` | [atlas.PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [atlas.data.Position](/javascript/api/azure-maps-control/atlas.data.position)  |
| `google.maps.LatLngBounds` | [atlas.data.BoundingBox](/javascript/api/azure-maps-control/atlas.data.boundingbox) |
| `google.maps.MapOptions`  | [atlas.CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)<br/>[atlas.CameraBoundsOptions](/javascript/api/azure-maps-control/atlas.cameraboundsoptions)<br/>[atlas.ServiceOptions](/javascript/api/azure-maps-control/atlas.serviceoptions)<br/>[atlas.StyleOptions](/javascript/api/azure-maps-control/atlas.styleoptions)<br/>[atlas.UserInteractionOptions](/javascript/api/azure-maps-control/atlas.userinteractionoptions) |
| `google.maps.Point`  | [atlas.Pixel](/javascript/api/azure-maps-control/atlas.pixel)   |

## <a name="overlay-classes"></a>Classes de Sobreposição

| Google Maps  | Mapas do Azure  |
|--------------|-------------|
| `google.maps.Marker` | [atlas.HtmlMarker](/javascript/api/azure-maps-control/atlas.htmlmarker)<br/>[atlas.data.Point](/javascript/api/azure-maps-control/atlas.data.point)  |
| `google.maps.MarkerOptions`  | [atlas.HtmlMarkerOptions](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)<br/>[atlas.layer.SymbolLayer](/javascript/api/azure-maps-control/atlas.layer.symbollayer)<br/>[atlas.SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)<br/>[atlas.IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions)<br/>[atlas.TextOptions](/javascript/api/azure-maps-control/atlas.textoptions)<br/>[atlas.layer.BubbleLayer](/javascript/api/azure-maps-control/atlas.layer.bubblelayer)<br/>[atlas.BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions) |
| `google.maps.Polygon`  | [atlas.data.Polygon](/javascript/api/azure-maps-control/atlas.data.polygon)               |
| `google.maps.PolygonOptions` |[atlas.layer.PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer)<br/> [atlas.PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)<br/> [atlas.layer.LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer)<br/> [atlas.LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)|
| `google.maps.Polyline` | [atlas.data.LineString](/javascript/api/azure-maps-control/atlas.data.linestring)         |
| `google.maps.PolylineOptions` | [atlas.layer.LineLayer](/javascript/api/azure-maps-control/atlas.layer.linelayer)<br/>[atlas.LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions) |
| `google.maps.Circle`  | Confira [Adicionar um círculo ao mapa](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [atlas.TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)         |
| `google.maps.ImageMapTypeOptions` | [atlas.TileLayerOptions](/javascript/api/azure-maps-control/atlas.tilelayeroptions) |
| `google.maps.GroundOverlay`  | [atlas.layer.ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)<br/>[atlas.ImageLayerOptions](/javascript/api/azure-maps-control/atlas.imagelayeroptions) |

## <a name="service-classes"></a>Classes de Serviço

O SDK Web do Azure Mapas inclui um módulo de serviço que pode ser carregado separadamente. Esse módulo encapsula os serviços REST dos Azure Mapas com uma API Web e pode ser usado em aplicativos JavaScript, TypeScript e Node.js.

| Google Maps | Mapas do Azure  |
|-------------|-------------|
| `google.maps.Geocoder` | [atlas.service.SearchUrl](/javascript/api/azure-maps-rest/atlas.service.searchurl)  |
| `google.maps.GeocoderRequest`  | [atlas.SearchAddressOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions)<br/>[atlas.SearchAddressRevrseOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions)<br/>[atlas.SearchAddressReverseCrossStreetOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions)<br/>[atlas.SearchAddressStructuredOptions](/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions)<br/>[atlas.SearchAlongRouteOptions](/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions)<br/>[atlas.SearchFuzzyOptions](/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions)<br/>[atlas.SearchInsideGeometryOptions](/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions)<br/>[atlas.SearchNearbyOptions](/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions)<br/>[atlas.SearchPOIOptions](/javascript/api/azure-maps-rest/atlas.service.searchpoioptions)<br/>[atlas.SearchPOICategoryOptions](/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions) |
| `google.maps.DirectionsService`  | [atlas.service.RouteUrl](/javascript/api/azure-maps-rest/atlas.service.routeurl)  |
| `google.maps.DirectionsRequest`  | [atlas.CalculateRouteDirectionsOptions](/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions) |
| `google.maps.places.PlacesService` | [atlas.service.SearchUrl](/javascript/api/azure-maps-rest/atlas.service.searchurl)  |

## <a name="libraries"></a>Bibliotecas

As bibliotecas adicionam funcionalidade extra ao mapa. Muitas dessas bibliotecas estão no SDK principal do Azure Mapas. Aqui estão algumas classes equivalentes a serem usadas no lugar dessas bibliotecas do Google Maps

| Google Maps           | Mapas do Azure   |
|-----------------------|--------------|
| Biblioteca de desenhos       | [Módulo de ferramentas de desenho](set-drawing-options.md) |
| Biblioteca de geometrias      | [atlas.math](/javascript/api/azure-maps-control/atlas.math)   |
| Biblioteca de visualizações | [Camada do mapa de calor](map-add-heat-map-layer.md) |

## <a name="clean-up-resources"></a>Limpar recursos

Não há recursos a serem limpos.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a migração para o Azure Mapas:

> [!div class="nextstepaction"]
> [Migrar um serviço Web](migrate-from-google-maps-web-services.md)
