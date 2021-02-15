---
title: Adicionar uma camada de imagem a um mapa | Mapas do Microsoft Azure
description: Saiba como adicionar imagens a um mapa. Veja como usar o SDK da Web do Azure Maps para personalizar as camadas de imagem e sobreposição de imagens em conjuntos fixos de coordenadas.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 43de832095e2c2dd674a156da914ed26f1e472d0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892937"
---
# <a name="add-an-image-layer-to-a-map"></a>Adicionar uma camada de imagem ao um mapa

Este artigo mostra como sobrepor uma imagem a um conjunto fixo de coordenadas. Aqui estão alguns exemplos de diferentes tipos de imagens que podem ser sobrepostas em mapas:

* Imagens capturadas de drones
* Criando floorplans
* Histórico ou outras imagens de mapa especializadas
* Plantas de sites de trabalho
* Imagens de radar de clima

> [!TIP]
> Um [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer) é uma maneira fácil de sobrepor uma imagem em um mapa. Observe que os navegadores podem ter dificuldades para carregar uma imagem grande. Nesse caso, considere dividir a imagem em blocos e carregá-los no mapa como um [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer).

A camada de imagem dá suporte aos seguintes formatos de imagem:

- JPEG
- PNG
- BMP
- GIF (sem animações)

## <a name="add-an-image-layer"></a>Adicionar uma camada de imagem

O código a seguir sobrepõe uma imagem de um [mapa de Newark, Nova Jersey, de 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) no mapa. Um [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer) é criado passando uma URL para uma imagem e coordena para os quatro cantos no formato `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` .

```javascript
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
```

Aqui está o exemplo de código completo em execução do código anterior.

<br/>

<iframe height='500' scrolling='no' title='Adicionar uma Camada de Imagem' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/eQodRo/'>Camada de Imagem Simples</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="import-a-kml-file-as-ground-overlay"></a>Importar um arquivo KML como uma sobreposição de terra

Este exemplo demonstra como adicionar informações de sobreposição de terra KML como uma camada de imagem no mapa. As sobreposições de aterramento KML fornecem coordenadas norte, Sul, leste e oeste e uma rotação no sentido anti-horário. Porém, a camada de imagem espera coordenadas para cada canto da imagem. A sobreposição de aterramento KML neste exemplo é para o Chartres Cathedral e é originada de [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

O código usa a `getCoordinatesFromEdges` função estática da classe [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer) . Ele calcula os quatro cantos da imagem usando as informações do Norte, Sul, leste, oeste e de rotação da sobreposição de aterramento KML.

<br/>

<iframe height='500' scrolling='no' title='Sobreposição de terra KML como camada de imagem' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>Sobreposição de Terra KML como Camada de Imagem</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-an-image-layer"></a>Customizar uma camada de imagem

A camada de imagem tem muitas opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='Opções de Camada de Imagem' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>Opções de Camada de Imagem</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [ImageLayer](/javascript/api/azure-maps-control/atlas.layer.imagelayer)

> [!div class="nextstepaction"]
> [ImageLayerOptions](/javascript/api/azure-maps-control/atlas.imagelayeroptions)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar uma camada de bloco](./map-add-tile-layer.md)