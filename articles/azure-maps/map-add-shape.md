---
title: Adicionar uma camada de polígono a um mapa | Microsoft Azure Mapas
description: Saiba como adicionar polígonos ou círculos a mapas. Veja como usar o SDK da Web do Azure Maps para personalizar formas geométricas e torná-las fáceis de atualizar e manter.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 96553e75d4b982cfe67d03961d4356a3844f253c
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890990"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Adicionar uma camada de polígono ao mapa

Este artigo mostra como renderizar as áreas de geometrias de recurso `Polygon` e `MultiPolygon` no mapa usando uma camada de polígono. O SDK da Web do Azure Mapas também dá suporte à criação de geometrias de círculo conforme definido no [esquema GeoJSON estendido](extend-geojson.md#circle). Esses círculos são transformados em polígonos quando renderizados no mapa. Todas as geometrias de recursos podem ser facilmente atualizadas quando encapsuladas com a classe [atlas.Shape](/javascript/api/azure-maps-control/atlas.shape).

## <a name="use-a-polygon-layer"></a>Usar uma camada de polígono 

Quando uma camada de polígono é conectada a uma fonte de dados e carregada no mapa, ela renderiza a área com os recursos `Polygon` e `MultiPolygon`. Para criar um polígono, adicione-o a uma fonte de dados e renderize-o com uma camada de polígono usando a classe [PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer).

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a rectangular polygon.
dataSource.add(new atlas.data.Feature(
    new atlas.data.Polygon([[
        [-73.98235, 40.76799],
        [-73.95785, 40.80044],
        [-73.94928, 40.7968],
        [-73.97317, 40.76437],
        [-73.98235, 40.76799]
    ]])
));

//Create and add a polygon layer to render the polygon to the map, below the label layer.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    fillOpacity: 0.7
}), 'labels');
```

Veja abaixo a amostra completa e em execução do código acima.

<br/>

<iframe height='500' scrolling='no' title='Adicionar um polígono a um mapa ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Adicionar um polígono a um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Usar um polígono e uma camada de linha juntos

Uma camada de linha é usada para renderizar o contorno de polígonos. O exemplo de código a seguir renderiza um polígono como o exemplo anterior, mas agora adiciona uma camada de linha. Essa camada de linha é uma segunda camada conectada à fonte de dados.  

<br/>

<iframe height='500' scrolling='no' title='Camada de polígonos e linhas para adicionar o polígono' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja o Pen <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Polígono e a camada de linha para adicionar o polígono</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Preencher um polígono com um padrão

Além de preencher um polígono com uma cor, você pode usar um padrão de imagem para preencher o polígono. Carregue um padrão de imagem para os recursos de sprite de imagem de mapas e então faça referência a essa imagem com a propriedade `fillPattern` da camada de polígono.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Padrão de preenchimento do polígono" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Confira o <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>Padrão de preenchimento do polígono</a> da Canela pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> O SDK da Web do Azure Mapas fornece vários modelos de imagem personalizáveis que você pode usar como padrões de preenchimento. Para obter mais informações, confira o documento [Como usar modelos de imagem](how-to-use-image-templates-web-sdk.md).

## <a name="customize-a-polygon-layer"></a>Personalizar uma camada de polígono

A camada de polígono tem apenas algumas opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Adicionar um círculo ao mapa

O Azure Mapas usa uma versão estendida do esquema GeoJSON que fornece uma definição para círculos, conforme observado [aqui](extend-geojson.md#circle). Um círculo é renderizado no mapa criando um recurso `Point`. Esse `Point` tem uma propriedade `subType` com um valor de `"Circle"` e uma propriedade `radius` com um número que representa o raio em metros. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

O SDK da Web do Azure Mapas converte esses recursos `Point` em recursos do `Polygon`. Em seguida, esses recursos são renderizados no mapa usando as camadas de polígono e de linha, conforme mostrado no exemplo de código a seguir.

<br/>

<iframe height='500' scrolling='no' title='Adicionar um círculo a um mapa' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Adicionar um círculo a um mapa</a> pelo Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Facilitar a atualização de uma geometria

Uma classe `Shape` envolve uma [Geometria](/javascript/api/azure-maps-control/atlas.data.geometry) ou um [Recurso](/javascript/api/azure-maps-control/atlas.data.feature) e facilita a atualização e manutenção desses recursos. Para criar uma instância de uma variável de forma, passe uma geometria ou um conjunto de propriedades para o construtor de forma.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

O exemplo de código a seguir mostra como encapsular um objeto GeoJSON de círculo com uma classe de forma. Conforme o valor do raio muda na forma, o círculo é renderizado automaticamente no mapa.

<br/>

<iframe height='500' scrolling='no' title='Atualizar propriedades de forma' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Pena <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>Atualizar propriedades de forma</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Polygon](/javascript/api/azure-maps-control/atlas.data.polygon)

> [!div class="nextstepaction"]
> [PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos:

> [!div class="nextstepaction"]
> [Criar uma fonte de dados](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar um pop-up](map-add-popup.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Como usar modelos de imagem](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linha](map-add-line-layer.md)

Recursos adicionais:

> [!div class="nextstepaction"]
> [Extensão de especificação GeoJSON do Azure Mapas](extend-geojson.md#circle)