---
title: Adicionar uma camada de bolha a um mapa | Mapas do Microsoft Azure
description: Saiba como renderizar pontos em mapas como círculos com tamanhos fixos. Consulte como usar o SDK da Web do Azure Maps para adicionar e personalizar camadas de bolha para essa finalidade.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: cae29dcc0d334a2296199da0d8e3bc4562e275d3
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895321"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Adicionar uma camada de bolha a um mapa

Este artigo mostra como renderizar dados de ponto de uma fonte de dados como uma camada de bolha em um mapa. Camadas de bolha renderizam pontos como círculos no mapa com um raio de pixel fixo. 

> [!TIP]
> As camadas de bolha por padrão processarão as coordenadas de todas as geometrias em uma fonte de dados. Para limitar a camada de modo que ela só processe os recursos de geometria de ponto, defina a `filter` propriedade da camada como `['==', ['geometry-type'], 'Point']` ou `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` se você quiser incluir recursos do MultiPoint também.

## <a name="add-a-bubble-layer"></a>Adicionar uma camada de bolha

O código a seguir carrega uma matriz de pontos em uma fonte de dados. Em seguida, ele conecta os pontos de dados a uma [camada de bolha](/javascript/api/azure-maps-control/atlas.layer.bubblelayer). A camada de bolha renderiza o raio de cada bolha com cinco pixels e uma cor de preenchimento de branco. E uma cor de traço de azul e uma largura de traço de seis pixels. 

```javascript
//Add point locations.
var points = [
    new atlas.data.Point([-73.985708, 40.75773]),
    new atlas.data.Point([-73.985600, 40.76542]),
    new atlas.data.Point([-73.985550, 40.77900]),
    new atlas.data.Point([-73.975550, 40.74859]),
    new atlas.data.Point([-73.968900, 40.78859])
];

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Add multiple points to the data source.
dataSource.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(dataSource, null, {
    radius: 5,
    strokeColor: "#4288f7",
    strokeWidth: 6, 
    color: "white" 
}));
```

Veja abaixo o exemplo de código completo em execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='DataSource BubbleLayer' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>DataSource BubbleLayer</a> pelos Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>Mostrar rótulos de uma camada de bolha

Este código mostra como usar uma camada de bolha para renderizar um ponto no mapa. E, como usar uma camada de símbolo para renderizar um rótulo. Para ocultar o ícone da camada de símbolo, defina a `image` propriedade das opções de ícone como `'none'` .

<br/>

<iframe height='500' scrolling='no' title='DataSource MultiLayer' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>DataSource MultiLayer</a> pelos Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-bubble-layer"></a>Personalizar uma camada de bolha

A camada de bolha tem apenas algumas opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de bolha' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>Opções de Camada de Bolhas</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [BubbleLayer](/javascript/api/azure-maps-control/atlas.layer.bubblelayer)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Criar uma fonte de dados](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Exemplos de código](/samples/browse/?products=azure-maps)