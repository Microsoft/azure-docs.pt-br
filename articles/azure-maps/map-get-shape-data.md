---
title: Obter dados de formas em um mapa | Mapas do Microsoft Azure
description: Neste artigo, saiba como obter dados de forma desenhados em um mapa usando o SDK da Web do Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: ddb8009e544ede82d1c56d112950ff247a87380c
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890693"
---
# <a name="get-shape-data"></a>Obter dados da forma

Este artigo mostra como obter dados de formas que são desenhadas no mapa. Usamos a função **drawingprovider. GetSource ()** dentro do [Gerenciador de desenho](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#getsource--). Há vários cenários em que você deseja extrair dados geojson de uma forma desenhada e usá-los em outro lugar.  


## <a name="get-data-from-drawn-shape"></a>Obter dados da forma desenhada

A função a seguir obtém os dados de origem da forma desenhada e os gera na tela. 

```javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Abaixo está o exemplo de código completo em execução, onde você pode desenhar uma forma para testar a funcionalidade:

<br/>

<iframe height="686" title="Obter dados da forma" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>obter dados de forma</a> por mapas do Azure ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Próximas etapas

Saiba como usar recursos adicionais do módulo de ferramentas de desenho:

> [!div class="nextstepaction"]
> [Reagir a eventos de desenho](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Tipos de interação e atalhos de teclado](drawing-tools-interactions-keyboard-shortcuts.md)

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Gerenciador de desenho](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [Barra de ferramentas desenho](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)