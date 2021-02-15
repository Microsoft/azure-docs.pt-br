---
title: Atalhos de teclado e tipos de interação de ferramentas de desenho no mapa | Mapas do Microsoft Azure
description: Como desenhar e editar formas usando um mouse, tela sensível ao toque ou teclado no SDK da Web do Microsoft Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b609a21348222150faf5fb828f2cc38ca6658f5d
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895810"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>Tipos de interação e atalhos de teclado no módulo ferramentas de desenho

Este artigo descreve todas as diferentes maneiras de desenhar e editar formas usando um mouse, tela sensível ao toque ou atalhos de teclado.

O Gerenciador de desenho dá suporte a três maneiras diferentes de interagir com o mapa, para desenhar formas.

* `click` -As coordenadas são adicionadas quando o mouse ou toque é clicado.
* `freehand ` -As coordenadas são adicionadas quando o mouse ou toque é arrastado no mapa.
* `hybrid` -As coordenadas são adicionadas quando o mouse ou toque é clicado ou arrastado.

## <a name="how-to-draw-shapes"></a>Como desenhar formas

 Antes que qualquer forma possa ser desenhada, defina a `drawingMode` opção do Gerenciador de desenho com uma configuração de desenho com suporte. Essa configuração pode ser programada ou invocada pressionando um dos botões de desenho na barra de ferramentas. O modo de desenho permanece habilitado, mesmo depois que uma forma é desenhada, facilitando o desenho de formas adicionais do mesmo tipo. Defina programaticamente o modo de desenho para um estado ocioso. Ou, alterne para um estado ocioso clicando no botão modos de desenho atuais na barra de ferramentas.

As próximas seções descrevem todas as diferentes maneiras pelas quais as formas podem ser desenhadas no mapa.

### <a name="how-to-draw-a-point"></a>Como desenhar um ponto

Quando o Gerenciador de desenho está no `draw-point` modo de desenho, as ações a seguir podem ser feitas para desenhar pontos no mapa. Esses métodos funcionam com todos os modos de interação.

**Iniciar desenho**
 - Clique no botão esquerdo do mouse ou toque no mapa para adicionar um ponto ao mapa. 
 - Se o mouse estiver sobre o mapa, pressione a `F` tecla e um ponto será adicionado na coordenada do ponteiro do mouse. Esse método fornece maior precisão para adicionar um ponto ao mapa. Haverá menos movimento no mouse devido ao pressionamento do movimento do botão esquerdo do mouse.
 - Continue clicando, tocando ou pressionando `F` para adicionar mais pontos ao mapa.
 
**Concluir desenho**
 - Clique em qualquer botão na barra de ferramentas de desenho. 
 - Defina programaticamente o modo de desenho. 
 - Pressione a `C` tecla.

**Cancelar desenho**
 - Pressione a `Escape` tecla.

### <a name="how-to-draw-a-line"></a>Como desenhar uma linha

Quando o Gerenciador de desenho está no `draw-line` modo, as ações a seguir podem ser feitas para desenhar pontos no mapa, dependendo do modo de interação.

**Iniciar desenho**
 - Modo de clique
   * Clique no botão esquerdo do mouse ou toque no mapa para adicionar cada ponto de uma linha no mapa. Uma coordenada é adicionada à linha para cada clique ou toque. 
   * Se o mouse estiver sobre o mapa, pressione a `F` tecla e um ponto será adicionado na coordenada do ponteiro do mouse. Esse método fornece maior precisão para adicionar um ponto ao mapa. Haverá menos movimento no mouse devido ao pressionamento do movimento do botão esquerdo do mouse.
   * Continue clicando até que todos os pontos desejados tenham sido adicionados à linha.
 - Modo de FreeHand
   * Pressione o botão esquerdo do mouse ou toque no mapa e arraste o mouse ou o ponto de toque ao lado. As coordenadas são adicionadas à linha à medida que o mouse ou o ponto de toque se move em torno do mapa. Assim que o evento de mouse ou de toque é disparado, o desenho é concluído. A frequência na qual as coordenadas são adicionadas é definida pela opção gerenciadores de desenho `freehandInterval` .
 - Modo híbrido
   * Alternar entre os métodos Click e FreeHand, conforme desejado, ao desenhar uma única linha. Por exemplo, clique em alguns pontos, mantenha pressionado e arraste o mouse para adicionar vários pontos e clique em mais alguns. 

**Concluir desenho**
 - Modo híbrido/clique
   * Clique duas vezes no mapa no último ponto. 
   * Clique em qualquer botão na barra de ferramentas de desenho. 
   * Defina programaticamente o modo de desenho. 
 - Modo de FreeHand
   * Solte o botão do mouse ou o ponto de toque.
 - Pressione a `C` tecla.

**Cancelar desenho**
 - Pressione a `Escape` tecla.

### <a name="how-to-draw-a-polygon"></a>Como desenhar um polígono

Quando o Gerenciador de desenho está no `draw-polygon` modo, as ações a seguir podem ser feitas para desenhar pontos no mapa, dependendo do modo de interação.

**Iniciar desenho**
 - Modo de clique
   * Clique no botão esquerdo do mouse ou toque no mapa para adicionar cada ponto de um polígono no mapa. Uma coordenada é adicionada ao polígono para cada clique ou toque. 
   * Se o mouse estiver sobre o mapa, pressione a `F` tecla e um ponto será adicionado na coordenada do ponteiro do mouse. Esse método fornece maior precisão para adicionar um ponto ao mapa. Haverá menos movimento no mouse devido ao pressionamento do movimento do botão esquerdo do mouse.
   * Continue clicando até que todos os pontos desejados tenham sido adicionados ao polígono.
 - Modo de FreeHand
   * Pressione o botão esquerdo do mouse ou toque no mapa e arraste o mouse ou o ponto de toque ao lado. As coordenadas são adicionadas ao polígono à medida que o mouse ou o ponto de toque se move em volta do mapa. Assim que o evento de mouse ou de toque é disparado, o desenho é concluído. A frequência na qual as coordenadas são adicionadas é definida pela opção gerenciadores de desenho `freehandInterval` .
 - Modo híbrido
   * Alternar entre os métodos Click e FreeHand, conforme desejado, ao desenhar um único polígono. Por exemplo, clique em alguns pontos, mantenha pressionado e arraste o mouse para adicionar vários pontos e clique em mais alguns. 

**Concluir desenho**
 - Modo híbrido/clique
   * Clique duas vezes no mapa no último ponto. 
   * Clique no primeiro ponto no polígono.
   * Clique em qualquer botão na barra de ferramentas de desenho. 
   * Defina programaticamente o modo de desenho. 
 - Modo de FreeHand
   * Solte o botão do mouse ou o ponto de toque.
 - Pressione a `C` tecla.

**Cancelar desenho**
 - Pressione a `Escape` tecla.

### <a name="how-to-draw-a-rectangle"></a>Como desenhar um retângulo

Quando o Gerenciador de desenho está no `draw-rectangle` modo, as ações a seguir podem ser feitas para desenhar pontos no mapa, dependendo do modo de interação. A forma gerada seguirá a [especificação geojson estendida para retângulos](extend-geojson.md#rectangle).

**Iniciar desenho**
 - Pressione o botão esquerdo do mouse ou toque no mapa para adicionar o primeiro canto do retângulo e arraste para criar o retângulo. 

**Concluir desenho**
 - Solte o botão do mouse ou o ponto de toque.
 - Defina programaticamente o modo de desenho. 
 - Pressione a `C` tecla.

**Cancelar desenho**
 - Pressione a `Escape` tecla.

### <a name="how-to-draw-a-circle"></a>Como desenhar um círculo

Quando o Gerenciador de desenho está no `draw-circle` modo, as ações a seguir podem ser feitas para desenhar pontos no mapa, dependendo do modo de interação. A forma gerada seguirá a [especificação geojson estendida para círculos](extend-geojson.md#circle).

**Iniciar desenho**
 - Pressione o botão esquerdo do mouse ou toque no mapa para adicionar o centro do círculo e arraste dar um raio aos círculos. 

**Concluir desenho**
 - Solte o botão do mouse ou o ponto de toque.
 - Defina programaticamente o modo de desenho. 
 - Pressione a `C` tecla.

**Cancelar desenho**
 - Pressione a `Escape` tecla.

## <a name="keyboard-shortcuts"></a>Atalhos do teclado

As ferramentas de desenho dão suporte a atalhos de teclado. Esses atalhos de teclado são funcionais quando o mapa está em foco.

| Chave      | Ação                            |
|----------|-----------------------------------|
| `C` | Conclui qualquer desenho em andamento e define o modo de desenho como ocioso. O foco será movido para o elemento de mapa de nível superior.  |
| `Escape` | Cancela qualquer desenho em andamento e define o modo de desenho como ocioso. O foco será movido para o elemento de mapa de nível superior.  |
| `F` | Adiciona uma coordenada a um ponto, linha ou polígono se o mouse estiver sobre o mapa. Ação equivalente de clicar no mapa quando estiver no modo de clique ou híbrido. Esse atalho permite desenhos mais precisos e mais rápidos. Você pode usar uma mão para posicionar o mouse e outro para pressionar o botão sem mover o mouse do gesto de imprensa. |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes no módulo ferramentas de desenho:

> [!div class="nextstepaction"]
> [Gerenciador de desenho](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [Barra de ferramentas desenho](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)