---
title: Adicionar barra de ferramentas de ferramentas de desenho para mapear | Mapas do Microsoft Azure
description: Como adicionar uma barra de ferramentas de desenho a um mapa usando o SDK da Web do Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: b00628ec5a9f41b027bf90b93421f3aa1404e97a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92896388"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Adicionar uma barra de ferramentas de ferramentas de desenho a um mapa

Este artigo mostra como usar o módulo ferramentas de desenho e exibir a barra de ferramentas desenho no mapa. O controle [DrawingToolbar](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar) adiciona a barra de ferramentas de desenho no mapa. Você aprenderá a criar mapas com apenas uma e todas as ferramentas de desenho e como personalizar a renderização das formas de desenho no Gerenciador de desenho.

## <a name="add-drawing-toolbar"></a>Adicionar barra de ferramentas de desenho

O código a seguir cria uma instância do Gerenciador de desenho e exibe a barra de ferramentas no mapa.

```javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Abaixo está o exemplo de código completo em execução da funcionalidade acima:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Adicionar barra de ferramentas de desenho" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte a <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>barra de ferramentas Adicionar desenho</a> da caneta pelo mapas do Azure ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Opções de barra de ferramentas de limite exibidas

O código a seguir cria uma instância do Gerenciador de desenho e exibe a barra de ferramentas com apenas uma ferramenta de desenho de polígono no mapa. 

```javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

Abaixo está o exemplo de código completo em execução da funcionalidade acima:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Adicionar uma ferramenta de desenho de polígono" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>Adicionar uma ferramenta de desenho de polígono</a> pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Alterar o estilo de renderização do desenho

O estilo das formas que são desenhadas pode ser personalizado pela recuperação das camadas subjacentes do Gerenciador de desenho usando a `drawingManager.getLayers()` função e, em seguida, definindo as opções nas camadas individuais. As alças de arrastar que aparecem para coordenadas ao editar uma forma são marcadores HTML. O estilo das alças de arrastar pode ser personalizado passando opções de marcador HTML para `dragHandleStyle` as `secondaryDragHandleStyle` Opções e do Gerenciador de desenho.  

O código a seguir obtém as camadas de renderização do Gerenciador de desenho e modifica suas opções para alterar o estilo de renderização para desenho. Nesse caso, os pontos serão renderizados com um ícone de marcador azul. As linhas serão vermelhas e quatro pixels de largura. Polígonos terão uma cor de preenchimento verde e uma estrutura de tópicos laranja. Em seguida, ele altera os estilos das alças de arrastar para ícones quadrados. 

```javascript
//Get rendering layers of drawing manager.
var layers = drawingManager.getLayers();

//Change the icon rendered for points.
layers.pointLayer.setOptions({
    iconOptions: {
        image: 'marker-blue'
    }
});

//Change the color and width of lines.
layers.lineLayer.setOptions({
    strokeColor: 'red',
    strokeWidth: 4
});

//Change fill color of polygons.
layers.polygonLayer.setOptions({
    fillColor: 'green'
});

//Change the color of polygon outlines.
layers.polygonOutlineLayer.setOptions({
    strokeColor: 'orange'
});

//Update the style of the drag handles that appear when editting.
drawingManager.setOptions({
    //Primary drag handle that represents coordinates in the shape.
    dragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="15" height="15" viewBox="0 0 15 15" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="15" height="15" style="stroke:black;fill:white;stroke-width:4px;"/></svg>',
        draggable: true
    },

    //Secondary drag hanle that represents mid-point coordinates that users can grab to add new cooridnates in the middle of segments.
    secondaryDragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="10" height="10" viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="10" height="10" style="stroke:white;fill:black;stroke-width:4px;"/></svg>',
        draggable: true
    }
});  
```

Abaixo está o exemplo de código completo em execução da funcionalidade acima:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Alterar o estilo de renderização do desenho" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>estilo de renderização de desenho de alteração</a> de caneta pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Próximas etapas

Saiba como usar recursos adicionais do módulo de ferramentas de desenho:

> [!div class="nextstepaction"]
> [Obter dados da forma](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagir a eventos de desenho](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Tipos de interação e atalhos de teclado](drawing-tools-interactions-keyboard-shortcuts.md)

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Barra de ferramentas desenho](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)

> [!div class="nextstepaction"]
> [Gerenciador de desenho](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)