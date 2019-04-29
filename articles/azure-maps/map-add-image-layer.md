---
title: Adicionar uma Camada de Imagem para o Azure Mapas | Microsoft Docs
description: Como adicionar uma Camada de Imagem ao mapa em Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 5396fefca3a60dea7a503f8b4e84cc575753ea30
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769559"
---
# <a name="add-an-image-layer-to-a-map"></a>Adicionar uma camada de imagem ao um mapa

Este artigo mostra como você pode sobrepor uma imagem para um conjunto fixo de coordenadas do mapa. Há muitos cenários em que a sobreposição de uma imagem do mapa é feita. Aqui estão alguns exemplos dos tipos de imagens geralmente sobrepostos em mapas;

* Imagens capturadas de drones.
* Plantas baixas de construção.
* Histórico ou outras imagens de mapa especializado.
* Blueprints de sites de trabalho.
* Imagens de radar de clima.

> [!TIP]
> Uma [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) é uma maneira fácil de sobrepor uma imagem em um mapa. No entanto, se a imagem for grande, o navegador pode apresentar dificuldades para carregá-la. Nesse caso, considere dividir sua imagem em blocos e carregá-las no mapa como uma [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest).

## <a name="add-an-image-layer"></a>Adicionar uma camada de imagem

Este exemplo mostra como sobrepor uma imagem de um [mapa de Newark Nova Jersey de 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) no mapa.

<br/>

<iframe height='500' scrolling='no' title='Adicionar uma Camada de Imagem' src='//codepen.io/azuremaps/embed/eQodRo/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/eQodRo/'>Camada de Imagem Simples</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco do código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, uma [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) é criada, passando uma URL para uma imagem e coordenadas para os quatro cantos no formato `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]`.

## <a name="import-a-kml-ground-overlay"></a>Importar uma sobreposição de terra KML

Este exemplo mostra como sobrepor informações de Sobreposição de Terra como uma camada de imagem no mapa. Sobreposições de terra KML fornecem norte, sul, Leste e as coordenadas de oeste e uma rotação no sentido anti-horário, a imagem da camada espera as coordenadas para cada canto da imagem. A sobreposição de terra KML neste exemplo é da Catedral de Chartres e originada de [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

<br/>

<iframe height='500' scrolling='no' title='Sobreposição de terra KML como camada de imagem' src='//codepen.io/azuremaps/embed/EOJgpj/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/EOJgpj/'>Sobreposição de Terra KML como Camada de Imagem</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O código acima usa `getCoordinatesFromEdges` função estática da classe [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest) para calcular os quatro cantos da imagem do norte, sul, leste, oeste e informações de rotação de sobreposição de terra KML.


## <a name="customize-an-image-layer"></a>Customizar uma camada de imagem

A camada de imagem tem muitas opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='Opções de Camada de Imagem' src='//codepen.io/azuremaps/embed/RqOGzx/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/RqOGzx/'>Opções de Camada de Imagem</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar uma camada de bloco](./map-add-tile-layer.md)