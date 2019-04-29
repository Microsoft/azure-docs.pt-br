---
title: Adicionar um marcador HTML ao Azure Mapas | Microsoft Docs
description: Como adicionar um marcador HTML ao mapa em Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 1c812a77429e13ea39b2f4946043c13e10aaf097
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769661"
---
# <a name="add-html-markers-to-the-map"></a>Adicionar marcadores HTML ao mapa

Este artigo mostra como adicionar um HTML personalizado, como um arquivo de imagem, ao mapa como um marcador HTML.

> [!NOTE]
> Os marcadores HTML não conectam fontes de dados. Em vez disso, as informações de posição são adicionadas diretamente ao marcador e o marcador é adicionado à propriedade `markers` do mapa que é um [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest).

> [!IMPORTANT]
> Ao contrário da maioria das camadas no controle de Web do Azure Mapas que usam WebGL para renderização, os marcadores HTML usam elementos DOM tradicionais para renderização. Assim, quanto mais marcadores HTML forem adicionados a uma página, mais elementos DOM existirão. O desempenho poderá ser prejudicado após adicionar algumas centenas de marcadores HTML. Para conjuntos de dados maiores, considere o clustering de dados ou utilizar uma camada de Símbolos ou Bolhas.

## <a name="add-an-html-marker"></a>Adicionar um marcador HTML

A classe HtmlMarker tem um estilo padrão. É possível personalizar o marcador, definindo as opções de cor e texto do marcador. O estilo padrão da classe HtmlMarker é um modelo de SVG que possui um espaço reservado para cores e texto. Defina as propriedades de cor e texto nas opções do HtmlMarker para uma personalização rápida. 

<br/>

<iframe height='500' scrolling='no' title='Adicionar um marcador HTML a um mapa' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Adicionar um marcador HTML a um mapa</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco do código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

O segundo bloco de código adiciona um [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest) ao mapa usando a propriedade [markers](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#markers) da classe [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). O HtmlMarker é adicionado ao mapa de dentro da função de [ouvinte de eventos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) para garantir que ele seja exibido depois do carregamento total do mapa.

## <a name="create-svg-templated-html-marker"></a>Criar um marcador HTML com modelo de SVG

O padrão `htmlContent` de um marcador HTML é um modelo de SVG contendo as pastas de local `{color}` e `{text}`. É possível criar cadeia de caracteres de SVG personalizadas e adicionar esses mesmos espaços reservados ao SVG, de forma que a configuração das opções `color` e `text` do marcador atualize esses espaços reservados no SVG.

<br/>

<iframe height='500' scrolling='no' title='Marcador HTML com modelo de SVG personalizado' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>Marcador HTML com modelo de SVG personalizado</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-css-styled-html-marker"></a>Adicionar um marcador HTML com estilo CSS

Um dos benefícios dos marcadores HTML é que há muitas ótimas personalizações que podem ser obtidas usando CSS. Nesse exemplo, o conteúdo do HtmlMarker consiste em HTML e CSS que criam um marcador animado que cai no local e pulsa.

<br/>

<iframe height='500' scrolling='no' title='DataSource HTML' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>DataSource HTML</a> pelos Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Marcadores HTML arrastáveis

Esse exemplo mostra como tornar um marcador HTML arrastável. Marcadores HTML dão suporte a eventos `drag`, `dragstart` e `dragend`.

<br/>

<iframe height='500' scrolling='no' title='Marcador HTML arrastável' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>Marcador HTML arrastável</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Adicionar eventos do mouse a marcadores HTML

Estes exemplos mostram como adicionar eventos do mouse e de arrastar a um marcador HTML.

<br/>

<iframe height='500' scrolling='no' title='Adicionando eventos do mouse a marcadores HTML' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>Adicionando eventos do mouse a marcadores HTML</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkermanager?view=azure-iot-typescript-latest)

Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolos](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolhas](./map-add-bubble-layer.md)