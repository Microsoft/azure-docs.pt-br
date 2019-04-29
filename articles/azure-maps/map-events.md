---
title: Manipular eventos de mouse com o Azure Maps | Microsoft Docs
description: Como criar um mapa interativo do Javascript com eventos de mapa
author: jingjing-z
ms.author: jinzh
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 4fce8eae25942d098bb3f3277938bfaa3dafa00b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770441"
---
# <a name="interact-with-the-map---mouse-events"></a>Interagir com o mapa - eventos de mouse

Este artigo mostra como usar a propriedade [map class](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) [events](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) para realçar os eventos no mapa e em diferentes camadas do mapa. Ele também mostra como usar a propriedade map class events para realçar eventos ao interagir com um marcador HTML.

## <a name="interact-with-the-map"></a>Interagir com o mapa

<iframe height='600' scrolling='no' title='Interagir com o mapa – eventos do mouse' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>Interagir com os eventos do mapa do mouse</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'> CodePen</a>.
</iframe>

Percorra o mapa acima e visualize os eventos de mouse correspondentes realçados à direita. Você pode clicar na **guia JS** para visualizar e editar o código JavaScript. Você também pode clicar no botão **Edit on CodePen** e editar o código no CodePen.

## <a name="interact-with-map-layers"></a>Interagir com as camadas do mapa

<iframe height='600' scrolling='no' title='Interação com o mapa – eventos de camada' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>Interação com o mapa – eventos de camada</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O código acima destaca o nome dos eventos que são acionados conforme você interage com a camada de símbolo. A camada de polígono, bolha, linha e símbolo dão suporte ao mesmo conjunto de eventos. A camada de peça não dá suporte a nenhum desses eventos.

## <a name="interact-with-html-marker"></a>Interagir com o marcador HTML

<iframe height='500' scrolling='no' title='Interação com o mapa – eventos de marcador HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>Interação com o mapa – eventos de marcador HTML</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O código acima adiciona eventos de mapa de Javascript a um marcador HTML. Ele também destaca o nome dos eventos que são acionados conforme você interage com o marcador HTML.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Usando o módulo de serviços de mapas do Azure](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Página de exemplo de código](https://aka.ms/AzureMapsSamples)
