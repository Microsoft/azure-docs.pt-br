---
title: Adicionar um marcador HTML ao mapa | Microsoft Azure Mapas
description: Saiba como adicionar marcadores HTML a mapas. Consulte como usar o SDK da Web do Azure Maps para personalizar marcadores e adicionar pop-ups e eventos de mouse a um marcador.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 1c4367e2a649f4e239e2dab374afc4fb867e517b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92891186"
---
# <a name="add-html-markers-to-the-map"></a>Adicionar marcadores HTML ao mapa

Este artigo mostra como adicionar um HTML personalizado, como um arquivo de imagem, ao mapa como um marcador HTML.

> [!NOTE]
> Os marcadores HTML não conectam fontes de dados. Em vez disso, as informações de posição são adicionadas diretamente ao marcador e o marcador é adicionado à propriedade `markers` do mapa que é um [HtmlMarkerManager](/javascript/api/azure-maps-control/atlas.htmlmarkermanager).

> [!IMPORTANT]
> Ao contrário da maioria das camadas no controle de Web do Azure Mapas que usam WebGL para renderização, os marcadores HTML usam elementos DOM tradicionais para renderização. Assim, quanto mais marcadores HTML forem adicionados a uma página, mais elementos DOM existirão. O desempenho poderá ser prejudicado após adicionar algumas centenas de marcadores HTML. Para conjuntos de dados maiores, considere o clustering de dados ou utilizar uma camada de Símbolos ou Bolhas.

## <a name="add-an-html-marker"></a>Adicionar um marcador HTML

A classe [HtmlMarker](/javascript/api/azure-maps-control/atlas.htmlmarker) tem um estilo padrão. É possível personalizar o marcador, definindo as opções de cor e texto do marcador. O estilo padrão da classe de marcador HTML é um modelo de SVG que tem um espaço reservado para `{color}` e `{text}`. Defina as propriedades de cor e texto nas opções do marcador HTML para uma personalização rápida. 

O código a seguir cria um marcador HTML e define a propriedade color como "DodgerBlue" e a propriedade text como "10". Um popup é anexado ao marcador e o evento `click` é usado para alternar a visibilidade do popup.

```javascript
//Create an HTML marker and add it to the map.
var marker = new atlas.HtmlMarker({
    color: 'DodgerBlue',
    text: '10',
    position: [0, 0],
    popup: new atlas.Popup({
        content: '<div style="padding:10px">Hello World</div>',
        pixelOffset: [0, -30]
    })
});

map.markers.add(marker);

//Add a click event to toggle the popup.
map.events.add('click',marker, () => {
    marker.togglePopup();
});
```

Veja abaixo o exemplo de código completo em execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Adicionar um marcador HTML a um mapa' src='//codepen.io/azuremaps/embed/MVoeVw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Adicionar um marcador HTML a um mapa</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-svg-templated-html-marker"></a>Criar um marcador HTML com modelo de SVG

O padrão `htmlContent` de um marcador HTML é um modelo de SVG contendo as pastas de local `{color}` e `{text}`. É possível criar cadeia de caracteres de SVG personalizadas e adicionar esses mesmos espaços reservados ao SVG, de forma que a configuração das opções `color` e `text` do marcador atualize esses espaços reservados no SVG.

<br/>

<iframe height='500' scrolling='no' title='Marcador HTML com modelo de SVG personalizado' src='//codepen.io/azuremaps/embed/LXqMWx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/LXqMWx/'>Marcador HTML com modelo de SVG personalizado</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> O SDK Web do Azure Mapas fornece vários modelos de imagem de SVG que podem ser usados com marcadores HTML. Para obter mais informações, confira o documento [Como usar modelos de imagem](how-to-use-image-templates-web-sdk.md).

## <a name="add-a-css-styled-html-marker"></a>Adicionar um marcador HTML com estilo CSS

Um dos benefícios dos marcadores HTML é que há muitas ótimas personalizações que podem ser obtidas usando CSS. Nesse exemplo, o conteúdo do HtmlMarker consiste em HTML e CSS que criam um marcador animado que cai no local e pulsa.

<br/>

<iframe height='500' scrolling='no' title='DataSource HTML' src='//codepen.io/azuremaps/embed/qJVgMx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/qJVgMx/'>DataSource HTML</a> pelos Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="draggable-html-markers"></a>Marcadores HTML arrastáveis

Esse exemplo mostra como tornar um marcador HTML arrastável. Marcadores HTML dão suporte a eventos `drag`, `dragstart` e `dragend`.

<br/>

<iframe height='500' scrolling='no' title='Marcador HTML arrastável' src='//codepen.io/azuremaps/embed/wQZoEV/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/wQZoEV/'>Marcador HTML arrastável</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-mouse-events-to-html-markers"></a>Adicionar eventos do mouse a marcadores HTML

Estes exemplos mostram como adicionar eventos do mouse e de arrastar a um marcador HTML.

<br/>

<iframe height='500' scrolling='no' title='Adicionando eventos do mouse a marcadores HTML' src='//codepen.io/azuremaps/embed/RqOKRz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/RqOKRz/'>Adicionando eventos do mouse a marcadores HTML</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [HtmlMarker](/javascript/api/azure-maps-control/atlas.htmlmarker)

> [!div class="nextstepaction"]
> [HtmlMarkerOptions](/javascript/api/azure-maps-control/atlas.htmlmarkeroptions)

> [!div class="nextstepaction"]
> [HtmlMarkerManager](/javascript/api/azure-maps-control/atlas.htmlmarkermanager)

Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos:

> [!div class="nextstepaction"]
> [Como usar modelos de imagem](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolhas](./map-add-bubble-layer.md)