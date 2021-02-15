---
title: Adicionar controles a um mapa | Mapas do Microsoft Azure
description: Como adicionar controle de zoom, controle de densidade, controle de rotação e um seletor de estilo a um mapa em mapas de Microsoft Azure.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: 2a225d4ea19cccea114e47cc61a8055d28e8cd99
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895283"
---
# <a name="add-controls-to-a-map"></a>Adicionar controles a um mapa

Este artigo mostra como adicionar controles a um mapa. Você também aprenderá a criar um mapa com todos os controles e um [seletor de estilo](./choose-map-style.md).

## <a name="add-zoom-control"></a>Adicionar o controle de zoom

Um controle de zoom Adiciona botões para ampliar e reduzir o mapa. O exemplo de código a seguir cria uma instância da classe [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) e a adiciona ao canto inferior direito do mapa.

```javascript
//Construct a zoom control and add it to the map.
map.controls.add(new atlas.control.ZoomControl(), {
    position: 'bottom-right'
});
```

Veja abaixo o exemplo de código completo em execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Adicionando um controle de zoom' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>Adicionando um controle de zoom</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-pitch-control"></a>Adicionar controle de densidade

Um controle de densidade Adiciona botões para inclinar a inclinação para mapear em relação ao horizonte. O exemplo de código a seguir cria uma instância da classe [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) . Ele adiciona o PitchControl ao canto superior direito do mapa.

```javascript
//Construct a pitch control and add it to the map.
map.controls.add(new atlas.control.PitchControl(), {
    position: 'top-right'
});
```

Veja abaixo o exemplo de código completo em execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Adicionando um controle de densidade' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>Adicionando um controle de pitch</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-compass-control"></a>Adicionar controle de bússola

Um controle Compass adiciona um botão para girar o mapa. O exemplo de código a seguir cria uma instância da classe de [controle Compass](/javascript/api/azure-maps-control/atlas.control.compasscontrol) e a adiciona ao canto inferior esquerdo do mapa.

```javascript
//Construct a compass control and add it to the map.
map.controls.add(new atlas.control.Compass(), {
    position: 'bottom-left'
});
```

Veja abaixo o exemplo de código completo em execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Adicionando um controle de rotação' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>Adicionando um controle de rotação</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="a-map-with-all-controls"></a>Um mapa com todos os controles

Vários controles podem ser colocados em uma matriz e adicionados ao mapa de uma só vez e posicionados na mesma área do mapa para simplificar o desenvolvimento. O seguinte adiciona os controles de navegação padrão ao mapa usando essa abordagem.

```javascript
map.controls.add([
    new atlas.control.ZoomControl(),
    new atlas.control.CompassControl(),
    new atlas.control.PitchControl(),
    new atlas.control.StyleControl()
], {
    position: "top-right"
});
```

O exemplo de código a seguir adiciona os controles de seletor de zoom, bússola, pitch e estilo ao canto superior direito do mapa. Observe como eles são automaticamente empilhados. A ordem dos objetos de controle no script determina a ordem em que aparecem no mapa. Para alterar a ordem dos controles no mapa, você pode alterar sua ordem na matriz.

<br/>

<iframe height='500' scrolling='no' title='Um mapa com todos os controles' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>Um mapa com todos os controles</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O controle do seletor de estilo é definido pela classe [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) . Para obter mais informações sobre como usar o controle de seletor de estilo, consulte [escolher um estilo de mapa](choose-map-style.md).

## <a name="customize-controls"></a>Personalizar controles

Aqui está uma ferramenta para testar as várias opções para personalizar os controles.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opções de controle de navegação" src="//codepen.io/azuremaps/embed/LwBZMx/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte as <a href='https://codepen.io/azuremaps/pen/LwBZMx/'>Opções de controle de navegação</a> por caneta pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

Se você quiser criar controles de navegação personalizados, crie uma classe que se estenda da `atlas.Control` classe ou crie um elemento HTML e posicione-o acima do div do mapa. Fazer com que este controle de interface do usuário chame a `setCamera` função Maps para mover o mapa. 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Controle de bússola](/javascript/api/azure-maps-control/atlas.control.compasscontrol)

> [!div class="nextstepaction"]
> [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) 

> [!div class="nextstepaction"]
> [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) 

> [!div class="nextstepaction"]
> [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) 

Consulte os seguintes artigos para o código completo:

> [!div class="nextstepaction"]
> [Adicionar um PIN](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar um pop-up](./map-add-popup.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linha](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](map-add-shape.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolhas](map-add-bubble-layer.md)