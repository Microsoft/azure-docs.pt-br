---
title: Criar uma fonte de dados para um mapa no Microsoft Azure Maps
description: 'Descubra como criar uma fonte de dados para um mapa. Saiba mais sobre as fontes de dados que o SDK da Web do Azure Maps usa: fontes geojson e blocos de vetor.'
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 9964c99ddfb59811fc67df634b41cede5847ede0
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97678852"
---
# <a name="create-a-data-source"></a>Criar uma fonte de dados

O SDK da Web do Azure Maps armazena dados em fontes de dados. O uso de fontes de dados otimiza as operações de dados para consulta e renderização. Atualmente, há dois tipos de fontes de dados:

* **Origem geojson**: gerencia dados de local brutos no formato geojson localmente. Bom para conjuntos de dados pequenos a médios (em cima de centenas de milhares de formas).
* **Origem do bloco de vetor**: carrega dados formatados como blocos de vetor para a exibição de mapa atual, com base no sistema de mapas de blocos. Ideal para conjuntos de dados grandes a grandes (milhões ou bilhões de formas).

## <a name="geojson-data-source"></a>Fonte de dados geojson

Uma fonte de dados baseada em geojson carrega e armazena dados localmente usando a `DataSource` classe. Os dados geojson podem ser criados ou criados manualmente usando as classes auxiliares no namespace do [Atlas. Data](/javascript/api/azure-maps-control/atlas.data) . A `DataSource` classe fornece funções para importar arquivos geojson locais ou remotos. Arquivos geojson remotos devem ser hospedados em um ponto de extremidade habilitado para CORs. A `DataSource` classe fornece funcionalidade para dados de ponto de cluster. E, os dados podem ser facilmente adicionados, removidos e atualizados com a `DataSource` classe. O código a seguir mostra como os dados geojson podem ser criados no Azure Maps.

```javascript
//Create raw GeoJSON object.
var rawGeoJson = {
     "type": "Feature",
     "geometry": {
         "type": "Point",
         "coordinates": [-100, 45]
     },
     "properties": {
         "custom-property": "value"
     }
};

//Create GeoJSON using helper classes (less error prone and less typing).
var geoJsonClass = new atlas.data.Feature(new atlas.data.Point([-100, 45]), {
    "custom-property": "value"
}); 
```

Depois de criadas, as fontes de dados podem ser adicionadas ao mapa por meio da `map.sources` propriedade, que é um [sourcemanager](/javascript/api/azure-maps-control/atlas.sourcemanager). O código a seguir mostra como criar um `DataSource` e adicioná-lo ao mapa.

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);
```

O código a seguir mostra as diferentes maneiras em que os dados geojson podem ser adicionados a um `DataSource` .

```javascript
//GeoJsonData in the following code can be a single or array of GeoJSON features or geometries, a GeoJSON feature colleciton, or a single or array of atlas.Shape objects.

//Add geoJSON object to data source. 
source.add(geoJsonData);

//Load geoJSON data from URL. URL should be on a CORs enabled endpoint.
source.importDataFromUrl(geoJsonUrl);

//Overwrite all data in data source.
source.setShapes(geoJsonData);
```

> [!TIP]
> Digamos que você deseja substituir todos os dados em um `DataSource` . Se você fizer chamadas para as `clear` `add` funções then, o mapa poderá renderizar novamente duas vezes, o que pode causar um pouco de atraso. Em vez disso `setShapes` , use a função, que removerá e substituirá todos os dados na fonte de dados e disparará apenas uma única rerenderização do mapa.

## <a name="vector-tile-source"></a>Origem do bloco de vetor

Uma fonte de bloco de vetor descreve como acessar uma camada de bloco de vetor. Use a classe [VectorTileSource](/javascript/api/azure-maps-control/atlas.source.vectortilesource) para criar uma instância de uma fonte de bloco de vetor. As camadas de bloco de vetor são semelhantes às camadas de bloco, mas não são as mesmas. Uma camada de peça é uma imagem rasterizada. As camadas de bloco de vetor são um arquivo compactado, no formato **PBF** . Esse arquivo compactado contém dados de mapa de vetor e uma ou mais camadas. O arquivo pode ser renderizado e estilizado no cliente, com base no estilo de cada camada. Os dados em um bloco de vetor contêm recursos geográficos na forma de pontos, linhas e polígonos. Há várias vantagens de usar camadas de bloco vetorial em vez de camadas de bloco rasterizadas:

* Um tamanho de arquivo de um bloco de vetor normalmente é muito menor do que um bloco rasterizado equivalente. Dessa forma, menos largura de banda é usada. Isso significa menor latência, um mapa mais rápido e uma melhor experiência do usuário.
* Como os blocos de vetor são renderizados no cliente, eles se adaptam à resolução do dispositivo em que estão sendo exibidos. Como resultado, os mapas renderizados aparecem mais bem definidos, com rótulos de cristal claro.
* Alterar o estilo dos dados nos mapas de vetor não exige o download dos dados novamente, já que o novo estilo pode ser aplicado ao cliente. Por outro lado, alterar o estilo de uma camada de bloco de varredura normalmente requer o carregamento de blocos do servidor, aplicando o novo estilo.
* Como os dados são entregues na forma de vetor, há menos processamento no lado do servidor necessário para preparar os dados. Como resultado, os dados mais recentes podem ser disponibilizados mais rapidamente.

O mapas do Azure segue a [especificação de bloco de vetor Mapbox](https://github.com/mapbox/vector-tile-spec), um padrão aberto. O mapas do Azure fornece os seguintes serviços de blocos de vetor como parte da plataforma:

- Detalhes do [](/rest/api/maps/renderv2/getmaptilepreview)  |  [formato de dados](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile) da documentação dos blocos de estrada
- Detalhes do [](/rest/api/maps/traffic/gettrafficincidenttile)  |  [formato de dados](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles) de documentação de incidentes de tráfego
- Detalhes do [](/rest/api/maps/traffic/gettrafficflowtile)  |  [formato de dados](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles) de documentação do fluxo de tráfego
- O Azure Maps Creator (versão prévia) também permite que blocos de vetor personalizados sejam criados e acessados por meio da [renderização de bloco Get v2](/rest/api/maps/renderv2/getmaptilepreview)

> [!TIP]
> Ao usar blocos de imagem vetorial ou rasterizada do serviço de renderização do Azure Maps com o SDK da Web, você pode substituir `atlas.microsoft.com` pelo espaço reservado `{azMapsDomain}` . Esse espaço reservado será substituído pelo mesmo domínio usado pelo mapa e também acrescentará automaticamente os mesmos detalhes de autenticação. Isso simplifica muito a autenticação com o serviço de renderização ao usar a autenticação Azure Active Directory.

Para exibir dados de uma fonte de bloco de vetor no mapa, conecte a origem a uma das camadas de renderização de dados. Todas as camadas que usam uma fonte de vetor devem especificar um `sourceLayer` valor nas opções. O código a seguir carrega o serviço de bloco de vetor do fluxo de tráfego do Azure Maps como uma fonte de bloco de vetor e, em seguida, o exibe em um mapa usando uma camada de linha. Essa fonte de bloco vetorial tem um único conjunto de dados na camada de origem chamada "fluxo de tráfego". Os dados de linha nesse conjunto de dados têm uma propriedade chamada `traffic_level` que é usada neste código para selecionar a cor e dimensionar o tamanho das linhas.

```javascript
//Create a vector tile source and add it to the map.
var source = new atlas.source.VectorTileSource(null, {
    tiles: ['https://{azMapsDomain}/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}'],
    maxZoom: 22
});
map.sources.add(source);

//Create a layer for traffic flow lines.
var flowLayer = new atlas.layer.LineLayer(source, null, {
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer: 'Traffic flow',

    //Color the roads based on the traffic_level property. 
    strokeColor: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 'red',
        0.33, 'orange',
        0.66, 'green'
    ],

    //Scale the width of roads based on the traffic_level property. 
    strokeWidth: [
        'interpolate',
        ['linear'],
        ['get', 'traffic_level'],
        0, 6,
        1, 1
    ]
});

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(flowLayer, 'labels');
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de linha de bloco de vetor" src="https://codepen.io/azuremaps/embed/wvMXJYJ?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a <a href='https://codepen.io/azuremaps/pen/wvMXJYJ'>camada de linha de bloco de vetor</a> de caneta pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="connecting-a-data-source-to-a-layer"></a>Conectando uma fonte de dados a uma camada

Os dados são renderizados no mapa usando camadas de renderização. Uma única fonte de dados pode ser referenciada por uma ou mais camadas de renderização. As seguintes camadas de renderização exigem uma fonte de dados:

* [Camada de bolha](map-add-bubble-layer.md) – renderiza dados de ponto como círculos em escala no mapa.
* [Camada de símbolo](map-add-pin.md) – renderiza dados de ponto como ícones ou texto.
* [Camada do mapa de calor](map-add-heat-map-layer.md) – renderiza dados de ponto como um mapa de calor de densidade.
* [Camada de linha](map-add-shape.md) – renderizar uma linha e ou renderizar a estrutura de polígonos. 
* [Camada de polígono](map-add-shape.md) – preenche a área de um polígono com uma cor sólida ou padrão de imagem.

O código a seguir mostra como criar uma fonte de dados, adicioná-la ao mapa e conectá-la a uma camada de bolha. Em seguida, importe dados do ponto geojson de um local remoto para a fonte de dados. 

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(source));

//Load the earthquake data.
source.importDataFromUrl('https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.geojson');
```

Há camadas de renderização adicionais que não se conectam a essas fontes de dados, mas carregam diretamente os dados para renderização. 

* [Camada de imagem](map-add-image-layer.md) – sobrepõe uma única imagem na parte superior do mapa e associa seus cantos a um conjunto de coordenadas especificadas.
* [Camada de bloco](map-add-tile-layer.md) – sobrepõe uma camada de bloco de rasterização na parte superior do mapa.

## <a name="one-data-source-with-multiple-layers"></a>Uma fonte de dados com várias camadas

Várias camadas podem ser conectadas a uma única fonte de dados. Há vários cenários diferentes nos quais essa opção é útil. Por exemplo, considere o cenário no qual um usuário desenha um polígono. Devemos renderizar e preencher a área do polígono, pois o usuário adiciona pontos ao mapa. A adição de uma linha com estilo para descrever o polígono facilita a visualização das bordas do polígono, conforme o usuário desenha. Para editar convenientemente uma posição individual no polígono, podemos adicionar um identificador, como um PIN ou um marcador, acima de cada posição.

![Mapa mostrando várias camadas Renderizando dados de uma única fonte de dados](media/create-data-source-web-sdk/multiple-layers-one-datasource.png)

Na maioria das plataformas de mapeamento, você precisaria de um objeto Polygon, de um objeto line e de um PIN para cada posição no polígono. À medida que o polígono é modificado, você precisa atualizar manualmente a linha e os Pins, o que pode se tornar rapidamente complexo.

Com o Azure Maps, tudo o que você precisa é de um único polígono em uma fonte de dados, conforme mostrado no código abaixo.

```javascript
//Create a data source and add it to the map.
var source = new atlas.source.DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(new atlas.data.Polygon([[[/* Coordinates for polygon */]]]));

//Create a polygon layer to render the filled in area of the polygon.
var polygonLayer = new atlas.layer.PolygonLayer(source, 'myPolygonLayer', {
     fillColor: 'rgba(255,165,0,0.2)'
});

//Create a line layer for greater control of rendering the outline of the polygon.
var lineLayer = new atlas.layer.LineLayer(source, 'myLineLayer', {
     color: 'orange',
     width: 2
});

//Create a bubble layer to render the vertices of the polygon as scaled circles.
var bubbleLayer = new atlas.layer.BubbleLayer(source, 'myBubbleLayer', {
     color: 'orange',
     radius: 5,
     strokeColor: 'white',
     strokeWidth: 2
});

//Add all layers to the map.
map.layers.add([polygonLayer, lineLayer, bubbleLayer]);
```

> [!TIP]
> Ao adicionar camadas ao mapa usando a `map.layers.add` função, a ID ou a instância de uma camada existente pode ser passada como um segundo parâmetro. Isso dirá ao MAP para inserir a nova camada que está sendo adicionada abaixo da camada existente. Além de passar uma ID de camada, esse método também dá suporte aos valores a seguir.
>
> * `"labels"` -Insere a nova camada abaixo das camadas do rótulo do mapa.
> * `"transit"` -Insere a nova camada abaixo da estrada do mapa e das camadas de trânsito.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [DataSource](/javascript/api/azure-maps-control/atlas.source.datasource)

> [!div class="nextstepaction"]
> [DataSourceOptions](/javascript/api/azure-maps-control/atlas.datasourceoptions)

> [!div class="nextstepaction"]
> [VectorTileSource](/javascript/api/azure-maps-control/atlas.source.vectortilesource)

> [!div class="nextstepaction"]
> [VectorTileSourceOptions](/javascript/api/azure-maps-control/atlas.vectortilesourceoptions)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar um pop-up](map-add-popup.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolhas](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linha](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](map-add-shape.md)

> [!div class="nextstepaction"]
> [Adicionar um mapa de calor](map-add-heat-map-layer.md)

> [!div class="nextstepaction"]
> [Exemplos de código](/samples/browse/?products=azure-maps)