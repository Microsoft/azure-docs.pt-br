---
title: Módulo de ferramentas de desenho | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a definir dados de opções de desenho usando o SDK da Web do Microsoft Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 95a04d763fa5982181cc1c797bce969d9857ae4b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92890625"
---
# <a name="use-the-drawing-tools-module"></a>Usar o módulo de ferramentas de desenho

O SDK da Web do Azure Maps fornece um *módulo de ferramentas de desenho*. Esse módulo facilita desenhar e editar formas no mapa usando um dispositivo de entrada, como um mouse ou tela sensível ao toque. A classe principal desse módulo é o [Gerenciador de desenho](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#setoptions-drawingmanageroptions-). O Gerenciador de desenho fornece todos os recursos necessários para desenhar e editar formas no mapa. Ele pode ser usado diretamente e está integrado a uma interface do usuário personalizada da barra de ferramentas. Você também pode usar a classe de [barra de ferramentas de desenho](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar) interna. 

## <a name="loading-the-drawing-tools-module-in-a-webpage"></a>Carregando o módulo ferramentas de desenho em uma página da Web

1. Crie um novo arquivo HTML e [implemente o mapa como de costume](./how-to-use-map-control.md).
2. Carregue o módulo ferramentas de desenho do Azure Maps. Você pode carregá-lo de uma das duas maneiras:
    - Use a versão de rede de distribuição de conteúdo do Azure hospedada globalmente do módulo serviços do Azure Maps. Adicione referência à folha de estilo JavaScript e CSS no `<head>` elemento do arquivo:

        ```html
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/drawing/0/atlas-drawing.min.js"></script>
        ```

    - Ou então, você pode carregar o módulo ferramentas de desenho para o código-fonte do SDK Web do Azure Maps localmente usando o pacote [Azure-Maps-desenho-Tools](https://www.npmjs.com/package/azure-maps-drawing-tools) NPM e, em seguida, hospedá-lo com seu aplicativo. Esse pacote também inclui definições de TypeScript. Use este comando:
    
        > **NPM instalar Azure-Maps-desenho-ferramentas**
    
        Em seguida, adicione uma referência à folha de estilo JavaScript e CSS no `<head>` elemento do arquivo:

         ```html
        <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.css" type="text/css" />
        <script src="node_modules/azure-maps-drawing-tools/dist/atlas-drawing.min.js"></script>
         ```

## <a name="use-the-drawing-manager-directly"></a>Usar o Gerenciador de desenho diretamente

Depois que o módulo ferramentas de desenho for carregado em seu aplicativo, você poderá habilitar os recursos de desenho e edição usando o [Gerenciador de desenho](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager#setoptions-drawingmanageroptions-). Você pode especificar opções para o Gerenciador de desenho ao criar uma instância dele ou usar a `drawingManager.setOptions()` função como alternativa.

### <a name="set-the-drawing-mode"></a>Definir o modo de desenho

O código a seguir cria uma instância do Gerenciador de desenho e define a opção de **modo** de desenho. 

```javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon"
});
```

O código a seguir é um exemplo de execução completa de como definir um modo de desenho do Gerenciador de desenho. Clique no mapa para começar a desenhar um polígono.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Desenhar um polígono" src="//codepen.io/azuremaps/embed/YzKVKRa/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/YzKVKRa/'>desenhar um polígono</a> pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="set-the-interaction-type"></a>Definir o tipo de interação

O Gerenciador de desenho dá suporte a três maneiras diferentes de interagir com o mapa para desenhar formas.

* `click` -As coordenadas são adicionadas quando o mouse ou toque é clicado.
* `freehand ` -As coordenadas são adicionadas quando o mouse ou toque é arrastado no mapa. 
* `hybrid` -As coordenadas são adicionadas quando o mouse ou toque é clicado ou arrastado.

O código a seguir habilita o modo de desenho de polígono e define o tipo de interação de desenho que o Gerenciador de desenho deve aderir `freehand` . 

```javascript
//Create an instance of the drawing manager and set drawing mode.
drawingManager = new atlas.drawing.DrawingManager(map,{
    mode: "draw-polygon",
    interactionType: "freehand"
});
```

 Este exemplo de código implementa a funcionalidade de desenhar um polígono no mapa. Basta manter o botão esquerdo do mouse e arrastá-lo livremente.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Desenho livre" src="//codepen.io/azuremaps/embed/ZEzKoaj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o desenho de caneta <a href='https://codepen.io/azuremaps/pen/ZEzKoaj/'>livre</a> pelo mapas do Azure ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>


### <a name="customizing-drawing-options"></a>Personalizando opções de desenho

Os exemplos anteriores demonstraram como personalizar opções de desenho ao instanciar o Gerenciador de desenho. Você também pode definir as opções do Gerenciador de desenho usando a `drawingManager.setOptions()` função. Abaixo está uma ferramenta para testar a personalização de todas as opções do Gerenciador de desenho usando a função SetOptions.

<br/>

<iframe height="685" title="Personalizar o Gerenciador de desenho" src="//codepen.io/azuremaps/embed/LYPyrxR/?height=600&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/LYPyrxR/'>obter dados de forma</a> por mapas do Azure ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Próximas etapas

Saiba como usar recursos adicionais do módulo de ferramentas de desenho:

> [!div class="nextstepaction"]
> [Adicionar uma barra de ferramentas de desenho](map-add-drawing-toolbar.md)

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
> [Gerenciador de desenho](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [Barra de ferramentas desenho](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)