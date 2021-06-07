---
title: 'Tutorial: Migrar um aplicativo Web dos Bing Mapas | Microsoft Azure Mapas'
description: Tutorial sobre como migrar um aplicativo Web do Bing Mapas para o Microsoft Azure Mapas.
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: devx-track-js
ms.openlocfilehash: 2b072107275fba1ff83ab3ddac63ed8bf7766356
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389103"
---
# <a name="tutorial-migrate-a-web-app-from-bing-maps"></a>Tutorial: Migrar um aplicativo Web dos Bing Mapas

Os aplicativos Web que usam os Bing Mapas geralmente usam o SDK do JavaScript V8 dos Bing Mapas. O SDK da Web do Azure Mapas é o SDK baseado no Azure adequado para o qual migrar. O SDK da Web do Azure Mapas lhe permite personalizar mapas interativos com seu próprio conteúdo e imagens para exibição em seus aplicativos móveis ou Web. Esse controle utiliza WebGL, permitindo que você processe grandes conjuntos de dados com alto desempenho. Desenvolva com esse SDK usando JavaScript ou TypeScript. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Carregar um mapa
> * Localizar um mapa
> * Adicionar marcadores, polilinhas e polígonos.
> * Exibir informações em um pop-up ou uma caixa de informações
> * Carregar e exibir dados KML e GeoJSON
> * Agrupar marcadores
> * Sobrepor uma camada de peça
> * Mostrar dados de tráfego
> * Adicionar uma sobreposição de piso

Se estiver migrando um aplicativo Web existente, verifique se ele está usando uma biblioteca de controle de mapeamento de software livre, como Cesium, Leaflet e OpenLayers. Se ele estiver e você preferir continuar usando essa biblioteca, poderá conectá-la aos serviços de bloco dos Azure Mapas ([blocos de estrada](/rest/api/maps/render/getmaptile) \| [blocos de satélite](/rest/api/maps/render/getmapimagerytile)). Os links abaixo fornecem os detalhes de como usar os Azure Mapas em algumas bibliotecas de controle de mapeamento de software livre comumente usadas.

* [Cesium](https://cesiumjs.org/) – Um controle de mapeamento 3D para a Web. [Exemplos de código](https://azuremapscodesamples.azurewebsites.net/?search=Cesium) \| [Repositório de plug-ins]()
* [Leaflet](https://leafletjs.com/) – Controle de mapeamento 2D leve para a Web. [Exemplos de código](https://azuremapscodesamples.azurewebsites.net/?search=leaflet) \| [Repositório de plug-ins]()
* [OpenLayers](https://openlayers.org/) – Um controle de mapeamento 2D para a Web compatível com projeções. [Exemplos de código](https://azuremapscodesamples.azurewebsites.net/?search=openlayers) \| [Repositório de plug-ins]()

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

A tabela a seguir lista os principais recursos da API no SDK do JavaScript V8 dos Bing Mapas e o suporte de uma API semelhante no SDK da Web dos Azure Mapas.

| Recurso nos Bing Mapas        | Suporte do SDK da Web do Azure Mapas                                                             |
|--------------------------|:--------------------------------------------------------------------------------------:|
| Pinos                 | ✓                                                                                      |
| Clustering de pinos       | ✓                                                                                      |
| Polilinhas e polígonos     | ✓                                                                                      |
| Sobreposições de piso          | ✓                                                                                      |
| Mapas de calor                | ✓                                                                                      |
| Camadas de bloco              | ✓                                                                                      |
| Camada KML                | ✓                                                                                      |
| Camada de contorno            | [Amostras](https://azuremapscodesamples.azurewebsites.net/?search=contour)              |
| Camada de agrupamento de dados       | Incluído no [módulo de fonte de dados em grade](https://github.com/Azure-Samples/azure-maps-gridded-data-source) de software livre do Azure Mapas       |
| Camada de bloco animado      | Incluído no [módulo de animação](https://github.com/Azure-Samples/azure-maps-animations) open-source dos Azure Mapas |
| Ferramentas de desenho            | ✓                                                                                      |
| Serviço geocodificador         | ✓                                                                                      |
| Serviço de instruções       | ✓                                                                                      |
| Serviço da matriz de distância  | ✓                                                                                      |
| Serviço de dados espaciais     | N/D                                                                                    |
| Imagens aéreas/satélite | ✓                                                                                      |
| Imagens panorâmicas         | N/D                                                                                |
| Imagens de rua       | N/D                                                                                |
| Suporte a GeoJSON          | ✓                                                                                      |
| Suporte a GeoXML           | ✓ [Módulo de E/S espacial](how-to-use-spatial-io-module.md)                                                                                     |
| Suporte a Texto Bem Conhecido  | ✓                                                                                      |
| Estilos de mapas personalizados        | Parcial                                                                                |

Os Azure Mapas também têm muitos [módulos open-source adicionais para o SDK da Web](open-source-projects.md#open-web-sdk-modules) que ampliam seus recursos.

## <a name="notable-differences-in-the-web-sdks"></a>Diferenças notáveis nos SDKs da Web

Estas são algumas das principais diferenças entre os SDKs da Web dos Bing Mapas e dos Azure Mapas que você deve conhecer:

* Além de fornecer um ponto de extremidade hospedado para acessar o SDK da Web do Azure Mapas, também há um pacote NPM disponível para inserir o SDK da Web em aplicativos, se você preferir. Para obter mais informações, confira esta [documentação](./how-to-use-map-control.md). Esse pacote também inclui definições de TypeScript.
* Os Bing Mapas fornecem dois branches hospedados do SDK: Lançamento e Experimental. O branch Experimental pode receber várias atualizações por dia durante a execução de um novo desenvolvimento. Os Azure Mapas hospedam apenas um branch de lançamento; no entanto, os recursos experimentais são criados como módulos personalizados no projeto de exemplos de código open-source dos Azure Mapas. Os Bing Mapas também tinham um branch congelado que era atualizado com menos frequência, reduzindo assim o risco de alterações que causam falha decorrentes de um lançamento. Nos Azure Mapas, você pode usar o módulo NPM e apontar para qualquer versão secundária anterior.

> [!TIP]
> Os Azure Mapas publicam as versões minimizadas e não minimizadas do SDK. Basta remover `.min` dos nomes de arquivos. A versão não minimizada é útil durante a depuração de problemas, mas use a versão minimizada em produção para aproveitar o tamanho de arquivo menor.

* Depois de criar uma instância da classe Map no Azure Mapas, seu código deve aguardar até que o evento `ready` ou `load` dos mapas sejam disparados antes de interagir com o mapa. Esses eventos garantem que todos os recursos de mapa sejam carregados e fiquem prontos para serem acessados.
* Ambas as plataformas usam um sistema de divisão semelhante para os mapas base, no entanto, os blocos nos Bing Mapas têm uma dimensão de 256 pixels, enquanto os blocos nos Azure Mapas têm uma dimensão de 512 pixels. Assim, para obter nos Azure Mapas a mesma exibição de mapa dos Bing Mapas, um nível de zoom usado nos Bing Mapas precisa ser subtraído por um nos Azure Mapas.
* As coordenadas nos Bing Mapas são chamadas de `latitude, longitude`, enquanto os Azure Mapas usam `longitude, latitude`. Esse formato se alinha com o `[x, y]` padrão que é seguido pela maioria das plataformas GIS.

* As formas no SDK da Web do Azure Mapas são baseadas no esquema GeoJSON. As classes auxiliares são expostas por meio do namespace [atlas.data](/javascript/api/azure-maps-control/atlas.data). Também há a classe [atlas.Shape](/javascript/api/azure-maps-control/atlas.shape), que pode ser usada para encapsular objetos GeoJSON e facilitar a atualização e manutenção desses objetos de uma maneira que possibilite a associação de dados.
* As coordenadas nos Azure Mapas são definidas como objetos Position, que podem ser especificados como uma matriz de número simples no formato `[longitude, latitude]` ou `new atlas.data.Position(longitude, latitude)`.

> [!TIP]
> A classe Position tem uma função auxiliar estática para importar coordenadas que estão no formato `latitude, longitude`. A função [atlas.data.Position.fromLatLng](/javascript/api/azure-maps-control/atlas.data.position) pode, muitas vezes, substituir a função `new Microsoft.Maps.Location` no código dos Bing Mapas.

* Em vez de especificar informações de estilo em cada forma adicionada ao mapa, o Azure Mapas separa os estilos dos dados. Os dados são armazenados em fontes de dados e conectados a camadas de renderização que o código dos Azure Mapas usam para renderizar os dados. Essa abordagem oferece o benefício de desempenho aprimorado. Além disso, muitas camadas são compatíveis com o estilo controlado por dados, em que a lógica de negócios pode ser adicionada às opções de estilo de camada que mudarão a maneira como as formas individuais são renderizadas dentro de uma camada com base nas propriedades definidas na forma.
* Os Azure Mapas fornecem várias funções úteis de matemática espacial no namespace de `atlas.math`; no entanto, elas são diferentes daquelas no módulo de matemática espacial dos Bing Mapas. A principal diferença é que os Azure Mapas não fornecem funções internas para operações binárias, como união e interseção, no entanto, como os Azure Mapas são baseados em GeoJSON, que é um padrão aberto, há muitas bibliotecas open-source disponíveis. Uma opção popular que funciona bem com os Azure Mapas e fornece uma grande variedade de funcionalidades de matemática espacial é [turf js](http://turfjs.org/).

Confira também o [Glossário dos Azure Mapas](./glossary.md) para obter uma lista detalhada da terminologia associada.

## <a name="web-sdk-side-by-side-examples"></a>Exemplos lado a lado do SDK da Web

Confira a seguir uma coleção de exemplos de código para cada plataforma que abrange casos de uso comuns que ajudarão você a migrar seu aplicativo Web do SDK do JavaScript V8 dos Bing Mapas para o SDK da Web dos Azure Mapas. Exemplos de código relacionados a aplicativos Web são fornecidos em JavaScript. No entanto, o Azure Mapas também fornece definições de TypeScript como uma opção adicional por meio de um [módulo NPM](./how-to-use-map-control.md).

**Tópicos**

* [Carregar um mapa](#load-a-map)
* [Como localizar o mapa](#localizing-the-map)
* [Como configurar a exibição do mapa](#setting-the-map-view)
* [Como adicionar um pino](#adding-a-pushpin)
* [Como adicionar um pino personalizado](#adding-a-custom-pushpin)
* [Como adicionar uma polilinha](#adding-a-polyline)
* [Como adicionar um polígono](#adding-a-polygon)
* [Exibir uma caixa de informações](#display-an-infobox)
* [Clustering de pinos](#pushpin-clustering)
* [Adicionar um mapa de calor](#add-a-heat-map)
* [Sobrepor uma camada de peça](#overlay-a-tile-layer)
* [Mostrar dados de tráfego](#show-traffic-data)
* [Adicionar uma sobreposição de piso](#add-a-ground-overlay)
* [Adicionar dados KML ao mapa](#add-kml-data-to-the-map)
* [Adicionar ferramentas de desenho](#add-drawing-tools)

### <a name="load-a-map"></a>Carregar um mapa

Carregar um mapa em ambos os SDKs segue o mesmo conjunto de etapas;

* Adicione uma referência ao SDK do Mapa.
* Adicione uma marca `div` ao corpo da página, que funcionará como um espaço reservado para o mapa.
* Crie uma função JavaScript chamada quando a página é carregada.
* Crie uma instância da respectiva classe de mapa.

**Algumas das principais diferenças**

* Os Bing Mapas exigem que uma chave de conta seja especificada na referência de script da API ou como uma opção do mapa. As credenciais de autenticação para o Azure Mapas são especificadas como opções da classe do mapa. Pode ser uma chave de assinatura ou informações do Azure Active Directory.
* Os Bing Mapas usam uma função de retorno de chamada na referência de script da API, que é usada para chamar uma função de inicialização para carregar o mapa. Com o Azure Mapas, o evento OnLoad da página deve ser usado.
* Ao usarem uma ID para fazer referência ao elemento `div` no qual o mapa será renderizado, os Bing Mapas usam um seletor HTML (ou seja, `#myMap`), enquanto os Azure Mapas usam apenas o valor de ID (ou seja, `myMap`).
* As coordenadas nos Azure Mapas são definidas como objetos Position, que podem ser especificados como uma matriz de número simples no formato `[longitude, latitude]`.
* O nível de zoom nos Azure Mapas é um nível inferior ao exemplo dos Bing Mapas devido à diferença nos tamanhos de sistema de divisão entre as plataformas.
* Por padrão, os Azure Mapas não adicionam nenhum controle de navegação à tela do mapa, como botões de zoom e botões de estilo de mapa. No entanto, há controles para adicionar um seletor de estilo de mapa, botões de zoom, controle de bússola ou rotação e um controle de densidade.
* Um manipulador de eventos é adicionado ao Azure Mapas para monitorar o evento `ready` da instância do mapa. Isso será disparado quando o mapa terminar de carregar o contexto do WebGL e todos os recursos necessários. Qualquer código pós-carregamento pode ser adicionado a esse manipulador de eventos.

Os exemplos a seguir mostram como carregar um mapa básico centralizado nas coordenadas de Nova York (longitude:-73,985, latitude: 40,747) e que está no nível de zoom 12 nos Bing Mapas.

**Antes: Bing Mapas**

O código a seguir é um exemplo de como exibir um Bing Mapas centralizado e ampliado em uma localização.

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
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Executar esse código em um navegador exibirá um mapa semelhante à imagem a seguir:

![Mapa dos Bing Mapas](media/migrate-bing-maps-web-app/bing-maps-load-map.jpg)

**Depois: Azure Mapas**

O código a seguir mostra como carregar um mapa com o mesmo modo de exibição no Azure Mapas, juntamente com um controle de estilo de mapa e botões de zoom.

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

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom and map style controls to top right of map.
                map.controls.add([
                        new atlas.control.StyleControl(),
                        new atlas.control.ZoomControl()
                    ], {
                        position: 'top-right'
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

![Mapa dos Azure Mapas](media/migrate-bing-maps-web-app/azure-maps-load-map.jpg)

A documentação detalhada de como configurar e usar o controle de mapeamento do Azure Mapas em um aplicativo Web pode ser encontrada [aqui](how-to-use-map-control.md).

> [!TIP]
> Os Azure Mapas publicam as versões minimizadas e não minimizadas do SDK. Remova `.min` dos nomes de arquivos. A versão não minimizada é útil durante a depuração de problemas, mas use a versão minimizada em produção para aproveitar o tamanho de arquivo menor.

**Recursos adicionais**

* O Azure Mapas também fornece controles de navegação para girar e pinçar a exibição de mapa conforme documentado [aqui](map-add-controls.md).

### <a name="localizing-the-map"></a>Como localizar o mapa

Se o público-alvo estiver espalhado em vários países ou falar idiomas diferentes, a localização será importante.

**Antes: Bing Mapas**

Para localizar os Bing Mapas, o idioma e a região são especificados usando os parâmetros `setLang` e `UR` adicionados à referência de marca `<script>` na API. Determinados recursos nos Bing Mapas só estão disponíveis em alguns mercados, sendo assim, o mercado do usuário é especificado usando o parâmetro `setMkt`.

```html
<script type="text/javascript" src="https://www.bing.com/api/maps/mapcontrol?callback=initMap&setLang=[language_code]&setMkt=[market]&UR=[region_code]" async defer></script>
```

Aqui está um exemplo dos Bing Mapas com o idioma definido como "fr-FR".

![Mapa dos Bing Mapas localizado](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)

**Depois: Azure Mapas**

Os Azure Mapas fornecem apenas opções para definir o idioma e a exibição regional do mapa. Um parâmetro de mercado não é usado para limitar os recursos. Existem duas maneiras diferentes de definir o idioma e a exibição regional do mapa. A primeira opção é adicionar essas informações ao namespace global `atlas`, o que fará com que todas as instâncias de controle de mapeamento do aplicativo usem essas configurações como padrão. O seguinte define o idioma como francês ("fr-FR") e a exibição regional como `"Auto"`:

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

A segunda opção é passar essas informações para as opções de mapa ao carregar o mapa, como:

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
> Com o Azure Mapas, é possível carregar várias instâncias de mapa na mesma página com diferentes configurações de idioma e região. Além disso, também é possível atualizar essas configurações no mapa após ele ter sido carregado. Uma lista detalhada de idiomas compatíveis no Azure Mapas pode ser encontrada [aqui](./supported-languages.md).

Aqui está um exemplo do Azure Mapas com o idioma definido como "fr" e a região do usuário definida como "fr-FR".

![Mapa dos Azure Mapas localizado](media/migrate-bing-maps-web-app/bing-maps-localized-map.jpg)

### <a name="setting-the-map-view"></a>Como configurar a exibição do mapa

É possível mover programaticamente mapas dinâmicos nos Azure Mapas e nos Bing Mapas para novas localizações geográficas chamando as funções adequadas em JavaScript. Os exemplos a seguir mostram como fazer com que o mapa exiba imagem aérea de satélite, centralize o mapa em uma localização com coordenadas (longitude: -111,0225, latitude: 35,0272) e altere o nível de zoom para 15 nos Bing Mapas.

> [!NOTE]
> Os Bing Mapas usam peças com dimensão de 256 pixels, enquanto os Azure Mapas usam um tamanho de peça maior, de 512 pixels. Isso reduz o número de solicitações de rede necessárias para que os Azure Mapas carreguem a mesma área do mapa que os Bing Mapas. No entanto, devido à maneira como as pirâmides de peças funcionam nos controles de mapeamento, as peças maiores nos Azure Mapas significam que, ao usar os Azure Mapas, você precisa subtrair o nível de zoom usado nos Bing Mapas por 1 para alcançar as mesmas áreas visíveis de mapa dos Bing Mapas.

**Antes: Bing Mapas**

O controle de mapeamento dos Bing Mapas pode ser movido de maneira programática usando a função `setView`, que permite especificar o centro do mapa e um nível de zoom.

```javascript
map.setView({
    mapTypeId: Microsoft.Maps.MapTypeId.aerial,
    center: new Microsoft.Maps.Location(35.0272, -111.0225),
    zoom: 15
});
```

![Configuração da exibição de mapa dos Bing Mapas](media/migrate-bing-maps-web-app/bing-maps-set-map-view.jpg)

**Depois: Azure Mapas**

Nos Azure Mapas, a posição do mapa pode ser alterada programaticamente usando a função `setCamera` do mapa, e o estilo do mapa pode ser alterado usando a função `setStyle`. Observe que as coordenadas nos Azure Mapas estão no formato "longitude, latitude", e o valor do nível de zoom é subtraído por um.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite_with_roads'
});
```

![Configuração da exibição de mapa dos Azure Mapas](media/migrate-bing-maps-web-app/azure-maps-set-map-view.jpg)

**Recursos adicionais**

-   [Escolher um estilo de mapa](./choose-map-style.md)
-   [Estilos de mapa compatíveis](./supported-map-styles.md)

### <a name="adding-a-pushpin"></a>Como adicionar um pino

Nos Azure Mapas, há várias maneiras de renderizar os dados de pontos no mapa;

* Marcadores HTML – renderiza pontos usando elementos DOM tradicionais. Os Marcadores de HTML dão suporte para arrastar.
* Camada de Símbolos – renderiza pontos com um ícone e/ou texto dentro do contexto WebGL.
* Camada de Bolhas – renderiza pontos como círculos no mapa. O raio dos círculos pode ser dimensionado com base nas propriedades dos dados.

As camadas de Símbolos e Bolhas são renderizadas no contexto WebGL e podem renderizar conjuntos muito grandes de pontos no mapa. Essas camadas exigem que os dados sejam armazenados em uma fonte de dados. As fontes de dados e as camadas de renderização devem ser adicionadas ao mapa depois que o evento `ready` é disparado. Os marcadores HTML são renderizados como elementos DOM dentro da página e não usam uma fonte de dados. Quanto mais elementos DOM uma página tiver, mais lenta ela será. Ao renderizar mais de algumas centenas de pontos em um mapa, é recomendável usar uma das camadas de renderização.

Os exemplos abaixo adicionam um marcador ao mapa em (longitude: -0,2, latitude: 51,5) com o número 10 sobreposto como um rótulo.

**Antes: Bing Mapas**

Com os Bing Mapas, os marcadores são adicionados ao mapa usando a classe `Microsoft.Maps.Pushpin`*. Os pinos são adicionados ao mapa usando uma de duas funções.

A primeira função é criar uma camada, inserir o pino nela e adicioná-la à propriedade `layers` do mapa.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

A segunda é adicioná-lo usando a propriedade `entities` do mapa. Essa função é marcada como preterida na documentação dos Bing Mapas V8, mas permanece parcialmente funcional em cenários básicos.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    text: '10'
});

map.entities.add(pushpin);
```

![Adição de pino nos Bing Mapas](media/migrate-bing-maps-web-app/bing-maps-add-pushpin.jpg)

**Depois: Azure Mapas usando Marcadores HTML**

Nos Azure Mapas, os marcadores HTML podem ser usados para exibir com facilidade um ponto no mapa e são recomendados para aplicativos simples que só precisam exibir um pequeno número de pontos no mapa. Para usar um marcador HTML, crie uma instância da classe `atlas.HtmlMarker`, defina as opções de texto e posição e adicione o marcador ao mapa usando a função `map.markers.add`.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

![Adição de marcador nos Azure Mapas](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)

**Depois: Azure Mapas usando uma Camada de Símbolos**

Ao usar uma camada de símbolos, os dados devem ser adicionados a uma fonte de dados e a fonte de dados deve ser anexada à camada. Além disso, a fonte de dados e a camada devem ser adicionadas ao mapa depois de o evento de `ready` ser disparado. Para renderizar um valor de texto exclusivo acima de um símbolo, as informações de texto precisam ser armazenadas como uma propriedade do ponto de dados e essa propriedade deve ser referenciada na `textField` opção da camada. Isso é um pouco mais trabalhoso do que usar marcadores HTML, mas oferece muitas vantagens de desempenho.

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

![Adição de camada de símbolos nos Azure Mapas](media/migrate-bing-maps-web-app/azure-maps-add-pushpin.jpg)

**Recursos adicionais**

-   [Criar uma fonte de dados](./create-data-source-web-sdk.md)
-   [Adicionar uma camada de Símbolo](./map-add-pin.md)
-   [Adicionar uma camada de Bolhas](./map-add-bubble-layer.md)
-   [Dados de ponto de cluster](./clustering-point-data-web-sdk.md)
-   [Adicionar Marcadores HTML](./map-add-custom-html.md)
-   [Usar expressões de estilo controladas por dados](./data-driven-style-expressions-web-sdk.md)
-   [Opções de ícone da camada de Símbolos](/javascript/api/azure-maps-control/atlas.iconoptions)
-   [Opção de texto da camada de Símbolos](/javascript/api/azure-maps-control/atlas.textoptions)
-   [Classe de marcador HTML](/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [Opções de marcador HTML](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-custom-pushpin"></a>Como adicionar um pino personalizado

Imagens personalizadas podem ser usadas para representar pontos em um mapa. A imagem a seguir é usada nos exemplos abaixo e usa uma imagem personalizada para exibir um ponto no mapa em (latitude: 51,5, longitude: -0,2) e desloca a posição do marcador para que o ponto do ícone de pino alinhe-se com a posição correta no mapa.

| ![Adição de pino nos Azure Mapas](media/migrate-bing-maps-web-app/yellow-pushpin.png)|
|:-----------------------------------------------------------------------:|
| yellow-pushpin.png                                                        |

**Antes: Bing Mapas**

Nos Bing Mapas, um marcador personalizado é criado passando uma URL para uma imagem nas opções de `icon` de um pino. A opção `anchor` é usada para alinhar o ponto da imagem do pino com a coordenada no mapa. O valor de âncora nos Bing Mapas em relação ao canto superior esquerdo da imagem.

```javascript
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(51.5, -0.2), {
    icon: 'ylw-pushpin.png',
    anchor: new Microsoft.Maps.Point(5, 30)
});

var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);
```

![Adição de pino personalizado nos Bing Mapas](media/migrate-bing-maps-web-app/bing-maps-add-custom-pushpin.jpg)

**Depois: Azure Mapas usando Marcadores HTML**

Para personalizar um marcador HTML no Azure Mapas, um `string` HTML ou `HTMLElement` pode ser passado para a opção `htmlContent` do marcador. No Azure Mapas, uma opção `anchor` é usada para especificar a posição relativa do marcador em relação à coordenada de posição usando um dos nove pontos de referência definidos: "centro", "superior", "inferior", "esquerda", "direita", "superior esquerdo", "superior direito", "inferior esquerdo", "inferior direito". O conteúdo é ancorado e definido como "inferior" por padrão, ou seja, a parte inferior central do conteúdo html. Para facilitar a migração de código dos Bing Mapas, defina a âncora como "superior esquerdo" e use a opção `offset` com o mesmo deslocamento usado nos Bing Mapas. Os deslocamentos nos Azure Mapas são movidos na direção oposta dos Bing Mapas, portanto, multiplique-os por menos um.

> [!TIP]
> Adicione `pointer-events:none` como um estilo no conteúdo HTML para desabilitar o comportamento de arrastar padrão no MS Edge, que exibirá um ícone indesejado.

```html
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

![Adição de marcador personalizado nos Azure Mapas](media/migrate-bing-maps-web-app/azure-maps-add-custom-marker.jpg)

**Depois: Azure Mapas usando uma Camada de Símbolos**

As camadas de símbolo no Azure Mapas também dão suporte a imagens personalizadas, mas é preciso primeiro que a imagem seja carregada nos recursos de mapa e que uma ID exclusiva seja atribuída a ela. Em seguida, a camada de símbolo pode fazer referência a essa ID. O símbolo pode ser deslocado para alinhar-se ao ponto correto na imagem pelo uso da opção `offset` de ícone. No Azure Mapas, uma opção `anchor` é usada para especificar a posição relativa do símbolo em relação à coordenada de posição usando um dos nove pontos de referência definidos: "centro", "superior", "inferior", "esquerda", "direita", "superior esquerdo", "superior direito", "inferior esquerdo", "inferior direito". O conteúdo é ancorado e definido como "inferior" por padrão, ou seja, a parte inferior central do conteúdo HTML. Para facilitar a migração de código dos Bing Mapas, defina a âncora como "superior esquerdo" e use a opção `offset` com o mesmo deslocamento usado nos Bing Mapas. Os deslocamentos nos Azure Mapas são movidos na direção oposta dos Bing Mapas, portanto, multiplique-os por menos um.

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
                map.imageSprite.add('my-yellow-pin', 'ylw-pushpin.png').then(function () {

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

![Adição de camada de símbolo personalizada nos Bing Mapas](media/migrate-bing-maps-web-app/azure-maps-add-custom-symbol-layer.jpg)

> [!TIP]
> Para criar uma renderização personalizada avançada de pontos, use várias camadas de renderização juntas. Por exemplo, se você quiser ter vários alfinetes com o mesmo ícone em diferentes círculos coloridos, em vez de criar várias imagens para cada sobreposição de cor de uma camada de símbolos na parte superior de uma camada de bolhas e fazer com que referenciem a mesma fonte de dados. Isso será muito mais eficiente do que criar e fazer com que o mapa mantenha várias imagens diferentes.

**Recursos adicionais**

-   [Criar uma fonte de dados](./create-data-source-web-sdk.md)
-   [Adicionar uma camada de Símbolo](./map-add-pin.md)
-   [Adicionar Marcadores HTML](./map-add-custom-html.md)
-   [Usar expressões de estilo controladas por dados](./data-driven-style-expressions-web-sdk.md)
-   [Opções de ícone da camada de Símbolos](/javascript/api/azure-maps-control/atlas.iconoptions)
-   [Opção de texto da camada de Símbolos](/javascript/api/azure-maps-control/atlas.textoptions)
-   [Classe de marcador HTML](/javascript/api/azure-maps-control/atlas.htmlmarker)
-   [Opções de marcador HTML](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

### <a name="adding-a-polyline"></a>Como adicionar uma polilinha

As polilinhas são usadas para representar uma linha ou um caminho no mapa. Os exemplos abaixo mostram como criar uma polilinha tracejada no mapa.

**Antes: Bing Mapas**

Nos Bing Mapas, a classe Polyline usa uma matriz de locais e um conjunto de opções.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polyline.
var polyline = new Microsoft.Maps.Polyline([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1)
    ], {
        strokeColor: 'red',
        strokeThickness: 4,
        strokeDashArray: [3, 3]
    });

//Add the polyline to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polyline);
map.layers.insert(layer);
```

![Polilinha nos Bing Mapas](media/migrate-bing-maps-web-app/bing-maps-line.jpg)

**Depois: Azure Mapas**

Nos Azure Mapas, as polilinhas são chamadas para os objetos `LineString` ou `MultiLineString` de termos geoespaciais mais comuns. Esses objetos podem ser adicionados a uma fonte de dados e renderizados usando uma camada de linha. As opções de matriz de tracejado, cor e largura do traço são praticamente idênticas entre as plataformas.

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

![Linha nos Azure Mapas](media/migrate-bing-maps-web-app/azure-maps-line.jpg)

**Recursos adicionais**

-   [Adicionar linhas ao mapa](./map-add-line-layer.md)
-   [Opções da camada de Linhas](/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [Usar expressões de estilo controladas por dados](./data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Como adicionar um polígono

Os polígonos são usados para representar uma área no mapa. Os Azure Mapas e os Bing Mapas dão um suporte muito semelhante aos polígonos. Os exemplos abaixo mostram como criar um polígono que forma um triângulo com base na coordenada central do mapa.

**Antes: Bing Mapas**

Nos Bing Mapas, a classe Polygon usa uma matriz de coordenadas ou anéis de coordenadas e um conjunto de opções.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create the polygon.
var polygon = new Microsoft.Maps.Polygon([
        center,
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude - 1),
        new Microsoft.Maps.Location(center.latitude - 0.5, center.longitude + 1),
        center
    ], {
        fillColor: 'rgba(0, 255, 0, 0.5)',
        strokeColor: 'red',
        strokeThickness: 2
    });

//Add the polygon to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(polygon);
map.layers.insert(layer);
```

![Polígono nos Bing Mapas](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)

**Depois: Azure Mapas**

Nos Azure Mapas, os objetos Polygon e MultiPolygon podem ser adicionados a uma fonte de dados e renderizados no mapa usando camadas. A área de um polígono pode ser renderizada em uma camada do polígono. O contorno de um polígono pode ser renderizado usando uma camada de linhas.

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

![Polígono nos Azure Mapas](media/migrate-bing-maps-web-app/azure-maps-polygon.jpg)

**Recursos adicionais**

-   [Adicionar um polígono a um mapa](./map-add-shape.md#use-a-polygon-layer)
-   [Adicionar um círculo ao mapa](./map-add-shape.md#add-a-circle-to-the-map)
-   [Opções da camada de polígono](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)
-   [Opções da camada de Linhas](/javascript/api/azure-maps-control/atlas.linelayeroptions)
-   [Usar expressões de estilo controladas por dados](./data-driven-style-expressions-web-sdk.md)

### <a name="display-an-infobox"></a>Exibir uma caixa de informações

Informações adicionais para uma entidade podem ser exibidas no mapa como uma classe `Microsoft.Maps.Infobox` nos Bing Mapas. Nos Azure Mapas, isso pode ser feito usando a classe `atlas.Popup`. Os exemplos abaixo adicionam ao mapa um pino/marcador que, quando clicado, exibe uma caixa de informações/pop-up.

**Antes: Bing Mapas**

Com os Bing Mapas, uma caixa de informações é criada usando o construtor `Microsoft.Maps.Infobox`.

```javascript
//Add a pushpin where we want to display an infobox.
var pushpin = new Microsoft.Maps.Pushpin(new Microsoft.Maps.Location(47.6, -122.33));

//Add the pushpin to the map using a layer.
var layer = new Microsoft.Maps.Layer();
layer.add(pushpin);
map.layers.insert(layer);

//Create an infobox and bind it to the map.
var infobox = new Microsoft.Maps.Infobox(new Microsoft.Maps.Location(47.6, -122.33), {
    description: '<div style="padding:5px"><b>Hello World!</b></div>',
    visible: false
});
infobox.setMap(map);

//Add a click event to the pushpin to open the infobox.
Microsoft.Maps.Events.addHandler(pushpin, 'click', function () {
    infobox.setOptions({ visible: true });
});
```

![Caixa de informações nos Bing Mapas](media/migrate-bing-maps-web-app/bing-maps-infobox.jpg)

**Depois: Azure Mapas**

Nos Azure Mapas, um pop-up pode ser usado para exibir informações adicionais para uma localização. Um objeto `string` ou `HTMLElement` HTML pode ser passado para a opção `content` do pop-up. Os pop-ups podem ser exibidos independentemente de qualquer forma, se desejado, portanto, exigem que um valor de `position` seja especificado. Para exibir um pop-up, chame a função `open` e passe o mapa no qual o pop-up deve ser exibido.

```javascript
//Add a marker to the map that to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:10px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

![Pop-up Azure Mapas](media/migrate-bing-maps-web-app/azure-maps-popup.jpg)

> [!NOTE]
> Para fazer o mesmo com uma camada de símbolos, bolhas, linhas ou polígonos, passe a camada para o código do evento de mapas, em vez de um marcador.

**Recursos adicionais**

-   [Adicionar um pop-up](./map-add-popup.md)
-   [Pop-up com o Conteúdo de Mídia](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
-   [Pop-ups em Formas](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
-   [Reutilizar pop-up com vários marcadores](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
-   [Classe de pop-up](/javascript/api/azure-maps-control/atlas.popup)
-   [Opções de pop-up](/javascript/api/azure-maps-control/atlas.popupoptions)

### <a name="pushpin-clustering"></a>Clustering de pinos

Ao visualizar muitos pontos de dados no mapa, os pontos se sobrepõem, o mapa fica confuso e torna-se difícil ver e usar. O clustering de dados de ponto pode ser usado para aprimorar essa experiência do usuário e também o desempenho. O clustering de dados de ponto são o processo de combinar dados de ponto que estão próximos uns dos outros e os representar no mapa como um ponto de dados clusterizado individual. À medida que o usuário aplica zoom ao mapa, os clusters se dividem em seus pontos de dados individuais.

Os exemplos abaixo carregam um feed GeoJSON de dados de terremoto da última semana e os adiciona ao mapa. Os clusters são renderizados como círculos em escala e coloridos, dependendo do número de pontos que eles contêm.

> [!NOTE]
> São usados vários algoritmos diferentes no clustering de pinos. Os Bing Mapas usam uma função simples baseada em grade, enquanto os Azure Mapas usam métodos de clustering baseados em pontos mais avançados e visualmente atrativos.

**Antes: Bing Mapas**

Nos Bing Mapas, os dados GeoJSON podem ser carregados usando o módulo GeoJSON. Os pinos podem ser clusterizados carregando no módulo de clustering e usando a camada de clustering que ele contém.

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
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2
            });

            //Load the GeoJSON and Clustering modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.Clustering'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (pins) {

                    //Create a ClusterLayer with options and add it to the map.
                    clusterLayer = new Microsoft.Maps.ClusterLayer(pins, {
                        clusteredPinCallback: createCustomClusteredPin,
                        gridSize: 100
                    });

                    map.layers.insert(clusterLayer);
                });
            });
        }

        //A function that defines how clustered pins are rendered.
        function createCustomClusteredPin(cluster) {
            //Get the number of pushpins in the cluster
            var clusterSize = cluster.containedPushpins.length;

            var radius = 20;    //Default radius to 20 pixels.
            var fillColor = 'lime';     //Default to lime green.

            if (clusterSize >= 750) {
                radius = 40;   //If point_count >= 750, radius is 40 pixels.
                fillColor = 'red';    //If the point_count >= 750, color is red.
            } else if (clusterSize >= 100) {
                radius = 30;    //If point_count >= 100, radius is 30 pixels.
                fillColor = 'yellow';    //If the point_count >= 100, color is yellow.
            }

            //Create an SVG string of a circle with the specified radius and color.
            var svg = ['<svg xmlns="http://www.w3.org/2000/svg" width="', (radius * 2), '" height="', (radius * 2), '">',
                '<circle cx="', radius, '" cy="', radius, '" r="', radius, '" fill="', fillColor, '"/>',
                '<text x="50%" y="50%" dominant-baseline="middle" text-anchor="middle" style="font-size:12px;font-family:arial;fill:black;" >{text}</text>',
                '</svg>'];

            //Customize the clustered pushpin using the generated SVG and anchor on its center.
            cluster.setOptions({
                icon: svg.join(''),
                anchor: new Microsoft.Maps.Point(radius, radius),
                textOffset: new Microsoft.Maps.Point(0, radius - 8) //Subtract 8 to compensate for height of text.
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Clustering nos Bing Mapas](media/migrate-bing-maps-web-app/bing-maps-clustering.jpg)

**Depois: Azure Mapas**

No Azure Mapas, os dados são adicionados e gerenciados por uma fonte de dados. As camadas conectam-se a fontes de dados e renderizam os dados nelas. A classe `DataSource` no Azure Mapas fornece várias opções de clustering.

* `cluster` – informa a fonte de dados para os dados do ponto de cluster. 
* `clusterRadius` – o raio em pixels para os pontos de cluster juntos.
* `clusterMaxZoom` – o nível de zoom máximo em que ocorre o clustering. Se você ampliar mais do que isso, todos os pontos serão renderizados como símbolos.
* `clusterProperties` – define as propriedades personalizadas que são calculadas usando expressões em todos os pontos de cada cluster e adicionadas às propriedades de cada ponto de cluster.

Quando o clustering estiver habilitado, a fonte de dados enviará pontos de dados clusterizados e não clusterizados para camadas para renderização. A fonte de dados é capaz de clusterizar centenas de milhares de pontos de dados. Um ponto de dados clusterizado tem as seguintes propriedades:

| Nome da propriedade               | Type    | Descrição                                    |
|-----------------------------|---------|------------------------------------------------|
| `cluster`                   | booleano | Indica se o recurso representa um cluster.     |
| `cluster_id`                | string  | Uma ID exclusiva para o cluster que pode ser usada com as funções `getClusterExpansionZoom`, `getClusterChildren` e `getClusterLeaves` das classes `DataSource`. |
| `point_count`               | número  | O número de pontos que o cluster contém.     |
| `point_count_abbreviated`   | string  | Uma cadeia de caracteres que abrevia o valor de `point_count` se for longo. (por exemplo, 4.000 torna-se 4K) |

A classe `DataSource` tem a seguinte função auxiliar para acessar informações adicionais sobre um cluster usando o `cluster_id`.

| Função       | Tipo de retorno        | Descrição     |
|----------------|--------------------|-----------------|
| `getClusterChildren(clusterId: number)`                              | `Promise<Feature<Geometry, any> | Shape>` | Recupera os filhos do cluster fornecido no próximo nível de zoom. Esses filhos podem ser uma combinação de formas e subclusters. Os subclusters serão recursos com propriedades correspondentes às propriedades do cluster. |
| `getClusterExpansionZoom(clusterId: number)`                         | `Promise<number>`                            | Calcula um nível de zoom em que o cluster começará a se expandir ou se separar.    |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | `Promise<Feature<Geometry, any> | Shape>` | Recupera todos os pontos em um cluster. Defina o `limit` para retornar um subconjunto dos pontos e use `offset` para paginar os pontos.    |

Ao renderizar dados clusterizados no mapa, geralmente, é mais fácil usar duas ou mais camadas. O exemplo abaixo usa três camadas, uma camada de bolhas para desenhar círculos coloridos em escala com base no tamanho dos clusters, uma camada de símbolos para renderizar o tamanho do cluster como texto e uma segunda camada de símbolos para renderizar os pontos não clusterizados. Há muitas outras maneiras de renderizar dados clusterizados no Azure Mapas realçadas na documentação [Dados do ponto de cluster](./clustering-point-data-web-sdk.md).

Os dados GeoJSON podem ser importados diretamente no Azure Mapas usando a função `importDataFromUrl` na classe `DataSource`.

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

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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
                        filter: ['has', 'point_count'] //Only rendered data points that have a point_count property, which clusters do.
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

![Clustering do Azure Mapas](media/migrate-bing-maps-web-app/azure-maps-clustering.jpg)

**Recursos adicionais**

-   [Adicionar uma camada de Símbolo](./map-add-pin.md)
-   [Adicionar uma camada de Bolhas](./map-add-bubble-layer.md)
-   [Dados de ponto de cluster](./clustering-point-data-web-sdk.md)
-   [Usar expressões de estilo controladas por dados](./data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Adicionar um mapa de calor

Mapas de calor, também conhecidos como ponto de mapas de densidade, são um tipo de visualização de dados usado para representar a densidade de dados usando um intervalo de cores. Geralmente são usados para mostrar os "pontos de acesso" dos dados em um mapa e são uma ótima maneira de renderizar grandes conjuntos de dados de ponto.

Os exemplos abaixo carregam um feed GeoJSON do USGS de todos os terremotos ocorridos no último mês e os renderiza como um mapa de calor.

**Antes: Bing Mapas**

Nos Bing Mapas, para criar um mapa de calor, carregue o módulo de mapa de calor. Da mesma forma, o módulo GeoJSON é carregado para adicionar suporte aos dados GeoJSON.

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
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(20, -160),
                zoom: 2,
                mapTypeId: Microsoft.Maps.MapTypeId.aerial
            });

            //Load the GeoJSON and HeatMap modules.
            Microsoft.Maps.loadModule(['Microsoft.Maps.GeoJson', 'Microsoft.Maps.HeatMap'], function () {

                //Load the GeoJSON data from a URL.
                Microsoft.Maps.GeoJson.readFromUrl(earthquakeFeed, function (shapes) {

                    //Create a heat map and add it to the map.
                    var heatMap = new Microsoft.Maps.HeatMapLayer(shapes, {
                        opacity: 0.65,
                        radius: 10
                    });
                    map.layers.insert(heatMap);
                });
            });
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![Mapa de calor nos Bing Mapas](media/migrate-bing-maps-web-app/bing-maps-heatmap.jpg)

**Depois: Azure Mapas**

No Azure Mapas, carregue os dados GeoJSON em uma fonte de dados e conecte a fonte de dados a uma camada do mapa de calor. Os dados GeoJSON podem ser importados diretamente no Azure Mapas usando a função `importDataFromUrl` na classe `DataSource`.

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
                style: 'satellite_with_roads',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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

![Mapa de calor nos Azure Mapas](media/migrate-bing-maps-web-app/azure-maps-heatmap.jpg)

**Recursos adicionais**

-   [Adicionar uma camada do mapa de calor](./map-add-heat-map-layer.md)
-   [Classe da camada do mapa de calor](/javascript/api/azure-maps-control/atlas.layer.heatmaplayer)
-   [Opções da camada do mapa de calor](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)
-   [Usar expressões de estilo controladas por dados](./data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Sobrepor uma camada de peça

As camadas de peças permitem que você sobreponha imagens grandes que foram divididas em imagens com peças menores que se alinham com o sistema de agrupamento lado a lado dos mapas. Essa é uma maneira comum de sobrepor imagens grandes ou conjuntos de dados muito grandes.

Os exemplos abaixo sobrepõem uma camada de peça de radar meteorológico da Iowa Environmental Mesonet of Iowa State University que usa um esquema de nomenclatura de peças X, Y e Zoom.

**Antes: Bing Mapas**

Nos Bing Mapas, as camadas de peças podem ser criadas usando a classe `Microsoft.Maps.TileLayer`.

```javascript
var weatherTileLayer = new Microsoft.Maps.TileLayer({
    mercator: new Microsoft.Maps.TileSource({
        uriConstructor: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{zoom}/{x}/{y}.png'
    })
});
map.layers.insert(weatherTileLayer);
```

![Mapa de calor ponderado do Bing Mapas](media/migrate-bing-maps-web-app/bing-maps-weighted-heatmap.jpg)

**Depois: Azure Mapas**

No Azure Mapas, uma camada de peças pode ser adicionada ao mapa praticamente da mesma forma que qualquer outra camada. Uma URL formatada que tem os espaços reservados em x, y e zoom (`{x}`, `{y}` e `{z}`, respectivamente) é usada para informar à camada na qual acessar as peças. As camadas de bloco no Azure Mapas também são compatíveis com os espaços reservados `{quadkey}`, `{bbox-epsg-3857}` e `{subdomain}`.

> [!TIP]
> No Azure Mapas, as camadas podem ser facilmente renderizadas sob outras camadas, incluindo as camadas do mapa base. Geralmente, é desejável renderizar camadas de peça abaixo dos rótulos de mapa para que elas sejam fáceis de ler. A função `map.layers.add` usa um segundo parâmetro que é a ID de uma segunda camada para inserir a nova camada abaixo. Para inserir uma camada de peça abaixo dos rótulos de mapa, o seguinte código pode ser usado:
>
> `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

![Mapa de calor ponderado do Azure Mapas](media/migrate-bing-maps-web-app/azure-maps-weighted-heatmap.jpg)

> [!TIP]
> As solicitações de bloco podem ser capturadas usando a opção `transformRequest` do mapa. Isso permitirá que você modifique ou adicione cabeçalhos à solicitação, se desejar.

**Recursos adicionais**

-   [Adicionar camadas de bloco](./map-add-tile-layer.md)
-   [Classe da camada do bloco](/javascript/api/azure-maps-control/atlas.layer.tilelayer)
-   [Opções de camada de bloco](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

### <a name="show-traffic-data"></a>Mostrar dados de tráfego

Os dados de tráfego podem ser sobrepostos nos Azure Mapas e nos Bing Mapas.

**Antes: Bing Mapas**

Nos Bing Mapas, os dados de tráfego podem ser sobrepostos ao mapa usando o módulo de tráfego.

```javascript
Microsoft.Maps.loadModule('Microsoft.Maps.Traffic', function () {
    var manager = new Microsoft.Maps.Traffic.TrafficManager(map);
    manager.show();
});
```

![Tráfego dos Bing Mapas](media/migrate-bing-maps-web-app/bing-maps-traffic.jpg)

**Depois: Azure Mapas**

O Azure Mapas fornece várias opções diferentes para exibir o tráfego. Incidentes de tráfego, como bloqueios e acidentes na estrada, podem ser exibidos como ícones no mapa. O fluxo de tráfego, como estradas codificadas por cor, pode ser sobreposto ao mapa e as cores podem ser modificadas para se basearem na relação entre o limite de velocidade postado e o atraso, seja ele o normal esperado ou o atraso absoluto. Os dados de incidentes nos Azure Mapas são atualizados a cada minuto, e os dados de fluxo são atualizados a cada dois minutos.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

![Tráfego do Azure Mapas](media/migrate-bing-maps-web-app/azure-maps-traffic.jpg)

Se você clicar em um dos ícones de tráfego no Azure Mapas, informações adicionais serão exibidas em um pop-up.

![Pop-up de tráfego dos Azure Mapas](media/migrate-bing-maps-web-app/azure-maps-traffic-popup.jpg)

**Recursos adicionais**

-   [Mostrar tráfego no mapa](./map-show-traffic.md)
-   [Opções de sobreposição de tráfego](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)
-   [Controle de tráfego](https://azuremapscodesamples.azurewebsites.net/?sample=Traffic%20controls)

### <a name="add-a-ground-overlay"></a>Adicionar uma sobreposição de piso

Os mapas do Azure e do Bing são compatíveis com a sobreposição de imagens com referência geográfica no mapa para que sejam movidas e dimensionadas conforme você faz panorâmica e aplica zoom no mapa. Nos Bing Mapas, isso é conhecido como sobreposições de piso, enquanto nos Azure Mapas é chamado de camadas de imagem. Esse recurso é ótimo para criar plantas baixas, sobrepor mapas antigos ou imagens de um drone.

**Antes: Bing Mapas**

Ao criar uma sobreposição de piso nos Bing Mapas, você precisará especificar a URL para a imagem a ser sobreposta e uma caixa delimitadora à qual a imagem será associada no mapa. Este exemplo sobrepõe uma imagem de mapa de [Newark, Nova Jersey, de 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) no mapa.

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
            map = new Microsoft.Maps.Map(document.getElementById('myMap'), {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.740, -74.18),
                zoom: 12
            });

            var overlay = new Microsoft.Maps.GroundOverlay({
                //Create a LocationRect from the edges of the bounding box; north, west, south, east.
                bounds: Microsoft.Maps.LocationRect.fromEdges(40.773941, -74.22655, 40.712216, -74.12544),
                imageUrl: 'newark_nj_1922.jpg'
            });
            map.layers.insert(overlay);
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Executar esse código em um navegador exibirá um mapa semelhante à imagem a seguir:

![Sobreposição de piso dos Bing Mapas](media/migrate-bing-maps-web-app/bing-maps-ground-overlay.jpg)

**Depois: Azure Mapas**

No Azure Mapas, as imagens com referência geográfica podem ser sobrepostas usando a classe `atlas.layer.ImageLayer`. Essa classe requer uma URL para uma imagem e um conjunto de coordenadas para os quatro cantos da imagem. A imagem deve ser hospedada no mesmo domínio ou ter CORs habilitados.

> [!TIP]
> Se você tiver apenas informações de norte, sul, leste, oeste e de rotação, e não coordenadas para cada canto da imagem, poderá usar a função [atlas.layer.ImageLayer.getCoordinatesFromEdges](/javascript/api/azure-maps-control/atlas.layer.imagelayer#getcoordinatesfromedges-number--number--number--number--number-) estática.

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

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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

![Sobreposição de piso dos Azure Mapas](media/migrate-bing-maps-web-app/azure-maps-ground-overlay.jpg)

**Recursos adicionais**

-   [Sobrepor uma imagem](./map-add-image-layer.md)
-   [Classe da camada de imagem](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

### <a name="add-kml-data-to-the-map"></a>Adicionar dados KML ao mapa

Os Azure e Bing Mapas podem importar e renderizar dados KML, KMZ, GeoRSS, GeoJSON e WKT (Texto Bem Conhecido) no mapa. Os Azure Mapas também dão suporte a arquivos GPX, GML, CSV espacial, WMS (Serviços de Mapeamento da Web), WMTS (Serviços de Bloco de Mapeamento da Web) e WFS (Serviços de Recurso da Web).

**Antes: Bing Mapas**

Executar esse código em um navegador exibirá um mapa semelhante à imagem a seguir:

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
            map = new Microsoft.Maps.Map('#myMap', {
                credentials: '<Your Bing Maps Key>',
                center: new Microsoft.Maps.Location(40.747, -73.985),
                zoom: 12
            });
                
            Microsoft.Maps.loadModule('Microsoft.Maps.GeoXml', function () {
                var callback = function (dataset) {
                    if (dataset.shapes) {
                        var l = new Microsoft.Maps.Layer();
                        l.add(dataset.shapes);
                        map.layers.insert(l);
                    }
                    if (dataset.layers) {
                        for (var i = 0, len = dataset.layers.length; i < len; i++) {
                            map.layers.insert(dataset.layers[i]);
                        }
                    }
                };
                Microsoft.Maps.GeoXml.readFromUrl('myKMLFile.kml', { error: function (msg) { alert(msg); } }, callback);
            });                
        }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

![KML do Bing Mapas](media/migrate-bing-maps-web-app/bing-maps-kml.jpg)

**Depois: Azure Mapas**

Nos Azure Mapas, GeoJSON é o principal formato de dados usado no SK da Web; outros formatos de dados espaciais podem ser facilmente integrados usando o [módulo de E/S espacial](/javascript/api/azure-maps-spatial-io/). Esse módulo tem funções para ler e gravar dados espaciais e também inclui uma camada de dados simples que pode renderizar dados facilmente de qualquer um desses formatos de dados espaciais. Para ler os dados em um arquivo de dados espaciais, envie uma URL ou dados brutos como uma cadeia de caracteres ou um blob para a função `atlas.io.read`. Isso retornará todos os dados analisados do arquivo que podem ser adicionados ao mapa. O KML é um pouco mais complexo do que a maioria dos formatos de dados espaciais porque ele inclui muito mais informações de estilo. A classe `SpatialDataLayer` dá suporte à renderização da maioria desses estilos; no entanto, as imagens de ícone precisam ser carregadas no mapa antes de carregar os dados do recurso e as sobreposições de piso precisam ser adicionadas como camadas ao mapa separadamente. Ao carregar dados por meio de uma URL, eles devem ser hospedados em um ponto de extremidade habilitado para CORs ou um serviço de proxy deve ser passado como uma opção para a função de leitura.

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

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.
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
                atlas.io.read('myKMLFile.kml').then(async r => {
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

![KML dos Azure Mapas](media/migrate-bing-maps-web-app/azure-maps-kml.jpg)

**Recursos adicionais**

-   [função atlas.io.read](/javascript/api/azure-maps-spatial-io/atlas.io#read-string---arraybuffer---blob--spatialdatareadoptions-)
-   [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)
-   [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

### <a name="add-drawing-tools"></a>Adicionar ferramentas de desenho

Os Bing e Azure Mapas fornecem um módulo que adiciona a capacidade para o usuário desenhar e editar formas no mapa usando o mouse ou outro dispositivo de entrada. Ambos dão suporte ao desenho de pinos, linhas e polígonos. Os Azure Mapas também fornecem opções para desenhar círculos e retângulos.

**Antes: Bing Mapas**

Nos Bing Mapas, o módulo `DrawingTools` é carregado usando a função `Microsoft.Maps.loadModule`. Uma vez carregado, uma instância da classe DrawingTools pode ser criada, e a função `showDrawingManager` é chamada para adicionar uma barra de ferramentas ao mapa.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
    var map, drawingManager;

    function initMap() {
        map = new Microsoft.Maps.Map('#myMap', {
            credentials: '<Your Bing Maps Key>'
        });

        //Load the DrawingTools module
        Microsoft.Maps.loadModule('Microsoft.Maps.DrawingTools', function () {
            //Create an instance of the DrawingTools class and bind it to the map.
            var tools = new Microsoft.Maps.DrawingTools(map);

            //Show the drawing toolbar and enable editting on the map.
            tools.showDrawingManager(function (manager) {
                //Store a reference to the drawing manager as it will be useful later.
                drawingManager = manager;
            });
        });
    }
    </script>

    <!-- Bing Maps Script Reference -->
    <script src="https://www.bing.com/api/maps/mapcontrol?callback=initMap" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>

```

![Ferramentas de desenho dos Bing Mapas](media/migrate-bing-maps-web-app/bing-maps-drawing-tools.jpg)

**Depois: Azure Mapas**

No Azure Mapas, é necessário carregar o módulo ferramentas de desenho carregando os arquivos JavaScript e CSS que precisam ser referenciados no aplicativo. Depois que o mapa for carregado, uma instância da classe `DrawingManager` poderá ser criada e uma instância `DrawingToolbar` anexada.

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

    <!-- Add references to the Azure Maps Map Drawing Tools JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
    
    <script type='text/javascript'>
        var map, drawingManager;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps key to the map SDK. Get an Azure Maps key at https://azure.com/maps. NOTE: The primary key should be used as the key.                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an instance of the drawing manager and display the drawing toolbar.
                drawingManager = new atlas.drawing.DrawingManager(map, {
                    toolbar: new atlas.control.DrawingToolbar({ position: 'top-left' })
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

![Ferramentas de desenho dos Azure Mapas](media/migrate-bing-maps-web-app/azure-maps-drawing-tools.jpg)

> [!TIP]
> Nas camadas dos Azure Mapas, as ferramentas de desenho fornecem várias maneiras nas quais os usuários podem desenhar formas. Por exemplo, ao desenhar um polígono, o usuário pode clicar para adicionar cada ponto ou manter o botão esquerdo do mouse pressionado e arrastá-lo para desenhar um caminho. Isso pode ser modificado usando a opção `interactionType` do `DrawingManager`.

**Recursos adicionais**

-   [Documentação](./set-drawing-options.md)
-   [Exemplos de código](https://azuremapscodesamples.azurewebsites.net/#Drawing-Tools-Module)

## <a name="additional-resources"></a>Recursos adicionais

Dê uma olhada nos [módulos open-source do SDK da Web do Azure Mapas](open-source-projects.md#open-web-sdk-modules). Esses módulos fornecem várias funcionalidades adicionais e são totalmente personalizáveis.

Examine os exemplos de código relacionados à migração de outros recursos dos Bing Mapas:

**Visualizações de dados**

> [!div class="nextstepaction"]
> [Camada de contorno](https://azuremapscodesamples.azurewebsites.net/?search=contour)

> [!div class="nextstepaction"]
> [Agrupamento de dados](https://azuremapscodesamples.azurewebsites.net/?search=data%20binning)

**Serviços**

> [!div class="nextstepaction"]
> [Como usar o módulo de serviços do Azure Mapas](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Pesquisar pontos de interesse](./map-search-location.md)

> [!div class="nextstepaction"]
> [Obter informações de uma coordenada (inverter geocódigo)](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Mostrar trajetos de A para B](./map-route.md)

> [!div class="nextstepaction"]
> [Pesquisar a sugestão automática com a interface do usuário do JQuery](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

Saiba mais sobre o SDK da Web do Azure Mapas.

> [!div class="nextstepaction"]
> [Como usar o Controle de Mapeamento](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Como usar o módulo de serviços](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Como usar o módulo de ferramentas de desenho](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Exemplos de código](/samples/browse/?products=azure-maps)

> [!div class="nextstepaction"]
> [Documentação de referência da API do Serviço SDK da Web do Azure Mapas](/javascript/api/azure-maps-control/)

## <a name="clean-up-resources"></a>Limpar recursos

Não há recursos a serem limpos.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a migração do Bing Mapas para o Azure Mapas.

> [!div class="nextstepaction"]
> [Migrar um serviço Web](migrate-from-bing-maps-web-services.md)